# **Part 6: Running an Audio File - Full Workflow**

**Why this is important**: Now that you have completed the installations and learned the basics, this step serves as a quick-reference workflow for running Whisper and Pyannote on an interaction audio file. We will add in some additional pipeline details to improve functionality, and allow for multiple consecutive audio files to be processed as a batch. **Additional authorship credit to Isabel Arvelo, M.S., for writing early drafts of portions of this tutorial page and pipeline.**

*NOTE: If you have already been through this tutorial once and want to directly open the pipeline in Jupyter Notebook, you can go straight there by downloading [this file](pipeline-v1.0.ipynb) and opening it in your working directory. You could also [skip to the middle of this tutorial](#3-run-whisper-and-pyannote) to go right to the pipeline code.

> This pipeline now supports **batch-processing** — you can place multiple `.wav` files into the raw audio folder, and all will be processed automatically. Note also that this code will **always expect two speakers**, anticipating evaluation of speaker dyads. Finally, **if you will be using GPU processing** you will need [additional steps here](#-additional-step-for-gpu-use).

# **STILL TO DO FOR THIS TUTORIAL PAGE:**
1. Fix progress bar in pipeline, get output to be labname_play_whisper.csv.
3. On to BatchAlign and the rest of the tutorial!
4. Send to Suma and Hannah to test drive? Send to Isa?

---

## **Getting Started: Set Up Your Virtual Environment**

As with the previous step, we will begin by setting up your workspace and Jupyter Notebook to run all subsequent code. We will also take care of installing a few extra packages to smooth out our pipeline.

> **Before you begin**, activate your environment, install packages, confirm your working directory, and launch Jupyter:
```sh
conda activate whisper_py

cd C:\Users\YourName\Documents\asr
pip install --upgrade jupyterlab notebook ipywidgets widgetsnbextension tqdm
jupyter notebook
```
Then **open a new notebook** and proceed with the steps below.

---

## **0. User Settings**

Update these settings once before running the pipeline. No need to edit anything later.

```python
# --- 1. User Settings ---
WORKING_DIR = r"C:\Users\USERNAME\Documents\asr"
AUDIO_FOLDER = "raw_audio_folder/"
OUTPUT_FOLDER = "transcription_output/"

# --- 2. HuggingFace Token (required for Pyannote diarization) ---
HUGGINGFACE_TOKEN = "YOUR_TOKEN_HERE"

# --- 3. Whisper Model Choice ---
WHISPER_MODEL = "base.en"  # Options: "tiny.en", "base.en", "small.en", "medium.en", "large-v3", etc.

# --- 4. Processing Options ---
DIARIZATION = True   # True to enable speaker diarization
LEVEL = "WORD"    # Options: "WORD" or "SEGMENT"
EXPORT_AS = "CSV"    # Options: "CSV" or "TXT"

print("User settings loaded.")
```

> **Tip: Choosing and Adjusting the Whisper Model**
> - Models ending in `.en` are English-only (faster, slightly more accurate for English).
> - Full multilingual models are good for mixed-language recordings.

---

## **1. Library Imports and Model Setup**

1. Import libraries (packages) to use:

```python
# --- Required Libraries ---
import os
import json
import torch
import gc
import pandas as pd
from collections import defaultdict
from pyannote.core import Timeline, Segment
from pyannote.database.util import load_rttm
from pyannote.audio import Pipeline
from pyannote.audio.pipelines.utils.hook import ProgressHook

# --- Optional: Suppress Pyannote warnings about std() ---
import warnings
warnings.filterwarnings("ignore", message=".*std\\(\\).*degrees of freedom.*")
```

2. Load the Whisper model:

```python
# --- Load Whisper Model ---
import stable_whisper
model = stable_whisper.load_model(WHISPER_MODEL)
```

---

## **2. Set Up Folders and Pipelines**

```python
# --- Set Directories ---
os.chdir(WORKING_DIR)
os.makedirs(AUDIO_FOLDER, exist_ok=True)
os.makedirs(OUTPUT_FOLDER, exist_ok=True)

# --- Set up Pyannote Pipeline if Diarization is Enabled ---
if DIARIZATION:
    pipeline = Pipeline.from_pretrained(
        "pyannote/speaker-diarization-3.1",
        use_auth_token=HUGGINGFACE_TOKEN
    )
    device = torch.device("cuda" if torch.cuda.is_available() else "cpu")
    pipeline.to(device)
```

---

## **3. RUN WHISPER AND PYANNOTE**

### Helper Functions

```python
# --- Helper Function to Check Overlap ---
def is_overlap(start1, end1, start2, end2):
    return max(start1, start2) < min(end1, end2)
```

```python
# --- note that "num_speakers=2" below forces Pyannote to define two speakers ---

def diarize(audio_file_path):
    if not os.path.exists(audio_file_path):
        raise FileNotFoundError(f"The audio file {audio_file_path} does not exist.")
    with ProgressHook() as hook:
        diarization = pipeline(audio_file_path, hook=hook, num_speakers=2)

    diarization_list = []
    for segment, track in diarization._tracks.items():
        try:
            speaker = list(track.values())[0]
            diarization_list.append({
                'start': segment.start,
                'end': segment.end,
                'speaker': speaker
            })
        except Exception as e:
            print(f"⚠️ Failed to parse segment {segment}: {e}")

    if not diarization_list:
        print(f"⚠️ No speaker tracks found in {audio_file_path}")
        return pd.DataFrame(columns=['start', 'end', 'speaker'])

    return pd.DataFrame(diarization_list)
```

```python
def align_diarization_and_transcription(speaker_segs_df, df_segments):
    if speaker_segs_df.empty or df_segments.empty:
        raise ValueError("Speaker segments or transcription segments are empty.")
    labels, predominant_labels = [], []
    for _, row in df_segments.iterrows():
        overlaps = speaker_segs_df.apply(
            lambda x: (x['speaker'], min(row['end'], x['end']) - max(row['start'], x['start'])) 
            if is_overlap(row['start'], row['end'], x['start'], x['end']) else (None, 0),
            axis=1
        )
        overlaps = overlaps[overlaps.apply(lambda x: x[0] is not None)]
        collapsed = defaultdict(float)
        for speaker, duration in overlaps:
            collapsed[speaker] += duration
        if collapsed:
            predominant = max(collapsed, key=collapsed.get)
            predominant_labels.append(predominant)
        else:
            predominant_labels.append("")
    df_segments["predominant_speaker"] = predominant_labels
    return df_segments
```

### Main Processing Loop

```python

from tqdm.auto import tqdm

# --- MAIN PROCESSING LOOP ---
for file_name in tqdm(os.listdir(AUDIO_FOLDER), desc="Processing files"):
    if file_name.endswith(".wav"):
        audio_path = os.path.join(AUDIO_FOLDER, file_name)

        # 1. Transcribe with Whisper
        result = model.transcribe(
            audio_path,
            word_timestamps=True,
            regroup=False,
            verbose=False
        )

        # 2. Convert segment-level to DataFrame (used for fallback or LEVEL == "SEGMENT")
        df_segments = pd.DataFrame(result.segments)

        # 3. Perform diarization and align
        if DIARIZATION:
            speaker_segs_df = diarize(audio_path)
            if speaker_segs_df.empty:
                print(f"⚠️ Warning: No speakers found in {file_name}. Skipping diarization alignment.")
            elif LEVEL == "SEGMENT":
                df_segments = align_diarization_and_transcription(speaker_segs_df, df_segments)

        # 4. Select export level
        if LEVEL == "WORD":
            words_data = []
            for segment in result.ori_dict['segments']:
                for word in segment.get('words', []):
                    words_data.append({
                        'word': word['word'],
                        'start': word['start'],
                        'end': word['end'],
                        'probability': word.get('probability', None)
                    })
            export_data = pd.DataFrame(words_data)
        else:
            export_data = df_segments
            if DIARIZATION and LEVEL == "WORD":
    export_data = align_diarization_and_transcription(speaker_segs_df, export_data)


        # 5. Save results
        base_filename = os.path.splitext(file_name)[0]
        output_path = os.path.join(OUTPUT_FOLDER, f"{base_filename}_transcript.{EXPORT_AS.lower()}")
        if EXPORT_AS == "CSV":
            export_data.to_csv(output_path, index=False)
        else:
            with open(output_path, "w", encoding="utf-8") as f:
                for _, row in export_data.iterrows():
                    if not row.get('text'):
                        continue
                    start = row.get('start', 0)
                    end = row.get('end', 0)
                    text = row.get('text', '').strip()
                    speaker = row.get('predominant_speaker', '')
                    f.write(f"{start:.2f}-{end:.2f} [{speaker}] {text}\n")

print("✅ All files processed and saved successfully!")
```

---

## **ADDITIONAL STEP FOR GPU USE**

To use an NVIDIA GPU (*Graphics Processing Unit*) for more advanced computing power and speed, you should complete the following steps *before* running the pipeline code above.

### A. Confirm your GPU is detected

In **Anaconda Prompt**, run:
```python
nvidia-smi```

This should return a table with your GPU model and driver version. If not, you may need to install the latest driver and reboot your device.

### B. Install PyTorch with CUDA support

Again in **Anaconda Prompt** with your virtual environment active (i.e., whisper_py), enter the following for CUDA 11.8 (compatible with most systems):
```python
pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu118
```

Be sure to check CUDA compatibility and change the download link if newer downloads are needed (e.g., newer cards may use CUDA 12.1 for a link in the above code ending in `wh1/cu121`).

The following code should confirm that your environment is ready for GPU use:

```python
import torch
print(torch.cuda.is_available())      # Should return True
print(torch.cuda.get_device_name(0))  # Should print your GPU name
```

## **Congratulations!**

You should now have a complete transcript (and diarization labels, if selected) saved to your specified output folder for every audio file that was pre-loaded into your raw audio folder.

If you want to continue learning, we have included a [Step 7](python_for_asr_tutorial_day7.md) to evaluate model accuracy and performance. We also have a bank of tools to utilize for converting .csv files to common speech-language analysis platforms such as SALT and CLAN.