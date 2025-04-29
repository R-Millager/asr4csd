# **Part 6: Running an Audio File - Full Workflow**

**Why this is important**: Now that you have completed the installations and learned the basics, this step serves as a quick-reference workflow for running Whisper and Pyannote on an interaction audio file. We will add in some additional pipeline details to improve functionality. **Additional authorship credit to Isabel Arvelo, M.S., for writing early drafts of portions of this tutorial page and pipeline.**

*NOTE: If you have already been through this tutorial once and want to directly open the pipeline in Jupyter Notebook, you can go straight there by downloading [this file](NEEDTOADD) and opening it in your working directory. You could also [skip to the middle of this tutorial](#3-run-whisper-and-pyannote) to go right to the pipeline code.

# **STILL TO DO FOR THIS TUTORIAL PAGE:**
1. Upload a finished .ipynb file in the Git to allow users who want to just jump right in to upload.
2. Add future notes - lags, model sizes, batch processing, etc.
3. On to BatchAlign and the rest of the tutorial!
4. Send to Suma and Hannah to test drive? Send to Isa?

---

# **Getting Started: Set Up Your Virtual Environment**

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

# **0. User Settings**

Update these settings once before running the pipeline. No need to edit anything later.

```python
# === USER SETTINGS ===

# 1. Your working directories
WORKING_DIR = r"C:\\Users\\YourName\\Documents\\asr"
AUDIO_FOLDER = "raw_audio_folder/"
OUTPUT_FOLDER = "transcription_output/"

# 2. Your HuggingFace token (required for Pyannote diarization)
HUGGINGFACE_TOKEN = "YOUR_TOKEN_HERE"

# 3. Whisper model choice
WHISPER_MODEL = "base.en"  # Options: "tiny.en", "base.en", "small.en", "medium.en", "large-v3", etc.

# 4. Processing options
DIARIZATION = True    # True to enable speaker diarization, False otherwise
LEVEL = "WORD"        # Options: "WORD" or "SEGMENT" - level of transcription
EXPORT_AS = "CSV"     # Options: "CSV" or "TXT" output

# 5. Set Working Directory
import os
os.chdir(WORKING_DIR)

print("User settings loaded. Working directory set.")
```

---

# **1. Pre-preparation**

1. Import libraries (packages) to use:

```python
import stable_whisper
import pandas as pd
import torch
import json
import gc
from pyannote.audio.pipelines.utils.hook import ProgressHook
from pyannote.core import Timeline, Segment
from pyannote.database.util import load_rttm
from pyannote.audio import Pipeline
from collections import defaultdict
from tqdm.auto import tqdm
```

2. Load the Whisper model:

```python
model = stable_whisper.load_model(WHISPER_MODEL)
```

> **Tip: Choosing and Adjusting the Model**
> - Models ending in `.en` are English-only (faster, slightly more accurate for English).
> - Full multilingual models are good for mixed-language recordings.

---

# **2. Set Up Folders and Pipelines**

```python
# --- Ensure Input and Output Folders Exist ---
os.makedirs(AUDIO_FOLDER, exist_ok=True)
os.makedirs(OUTPUT_FOLDER, exist_ok=True)

# --- Set Up Diarization Pipeline if Requested ---
if DIARIZATION:
    pipeline = Pipeline.from_pretrained(
        "pyannote/speaker-diarization-3.1",
        use_auth_token=HUGGINGFACE_TOKEN
    )

    # Dynamically select device (CPU or CUDA if available)
    device = torch.device("cuda" if torch.cuda.is_available() else "cpu")
    pipeline.to(device)
```

---

# **3. RUN WHISPER AND PYANNOTE**

### Helper Functions

```python
def is_overlap(start1, end1, start2, end2):
    return max(start1, start2) < min(end1, end2)
```

```python
def diarize(audio_file_path):
    if not os.path.exists(audio_file_path):
        raise FileNotFoundError(f"The audio file {audio_file_path} does not exist.")
    with ProgressHook() as hook:
        diarization = pipeline(audio_file_path, hook=hook)
    diarization_list = [{
        'start': segment.start,
        'end': segment.end,
        'speaker': list(track.values())[0]
    } for segment, track in diarization._tracks.items()]
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
# --- MAIN PROCESSING LOOP ---
for file_name in tqdm(os.listdir(AUDIO_FOLDER), desc="Processing files"):
    if file_name.endswith(".wav"):
        audio_path = os.path.join(AUDIO_FOLDER, file_name)

		# 1. Transcribe with Whisper
		result = model.transcribe(audio_path, regroup=False, verbose=False)

		# 2. Convert segments to DataFrame
		df_segments = pd.DataFrame(result.segments)

		# 3. (Optional) Perform diarization and align
			if DIARIZATION:
			speaker_segs_df = diarize(audio_path)
			df_segments = align_diarization_and_transcription(speaker_segs_df, df_segments)

		# 4. Select export level
		export_data = pd.DataFrame(result.words) if LEVEL == "WORD" else df_segments
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
                    start = row.get('start', 0)
                    end = row.get('end', 0)
                    text = row.get('text', '')
                    speaker = row.get('predominant_speaker', '')
                    f.write(f"{start:.2f}-{end:.2f} [{speaker}] {text}\n")
```

---

# **Congratulations!**

You should now have a complete transcript (and diarization labels, if selected) saved to your specified output folder for every audio file that was pre-loaded into your raw audio folder.

## **Additional Notes**
