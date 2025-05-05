# **Part 6: Running an Audio File - Full Workflow**

**Why this is important**: Now that you have completed the installations and learned the basics, this step serves as a quick-reference workflow for running Whisper and Pyannote on an interaction audio file. We will add in some additional pipeline details to improve functionality, and allow for multiple consecutive audio files to be processed as a batch. **Additional authorship credit to Isabel Arvelo, M.S., for writing early drafts of portions of this pipeline.**

*NOTE: If you have already been through this tutorial once and want to directly open the pipeline in Jupyter Notebook, you can go straight there by downloading [this file](pipeline-v1.0.ipynb) and opening it in your working directory. You could also [skip to the middle of this tutorial](#3-run-whisper-and-pyannote) to go right to the pipeline code.*

> This pipeline supports **batch-processing** — you can place multiple `.wav` files into the raw audio folder, and all will be processed automatically. Note also that this code will **always expect two speakers**, anticipating evaluation of speaker dyads. Finally, **if you will be using GPU processing** you will need [additional steps here](#4-additional-step-for-gpu-use).

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
import stable_whisper
import pandas as pd # aliases are often used to avoid having to refer to the library by its full name 
import torch
import os
import json
import gc
```

```python
from pyannote.audio.pipelines.utils.hook import ProgressHook
from pyannote.core import Timeline, Segment
from pyannote.database.util import load_rttm
from pyannote.audio import Pipeline
```

```python
from collections import defaultdict
```

2. Load the Whisper model:

```python
model = stable_whisper.load_model(WHISPER_MODEL)
```

---

## **2. Set Up Folders and Pipelines**

```python
# --- Set Working Directory ---
os.chdir(WORKING_DIR)
os.makedirs(AUDIO_FOLDER, exist_ok=True)
os.makedirs(OUTPUT_FOLDER, exist_ok=True)

# --- Set up Pyannote Pipeline if Diarization is Enabled ---
if (DIARIZATION == True):
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
def diarize(audio_file_path):
    
    with ProgressHook() as hook:
          diarization = pipeline(audio_file_path, hook=hook, min_speakers=2, max_speakers=2) #<--this indicates anticipated # of speakers
    
    diarization_list = [{
                'start': segment.start,
                'end': segment.end,
                'speaker': list(track.values())[0]
            } for segment, track in diarization._tracks.items()]

    diarization_df = pd.DataFrame(diarization_list)

    return diarization_df 
```

```python
def align_diarization_and_transcription(speaker_segs_df, df_segments):
    
    labels = []
    predominant_labels = []
    max_overlaps = []
    durations = []
    all_overlaps = []
    
      
    for i, row in df_segments.iterrows():
        
        overlaps = speaker_segs_df.apply(
            lambda x: (x['speaker'], min(row['end'], x['end']) - max(row['start'], x['start'])) 
            if is_overlap(row['start'], row['end'], x['start'], x['end']) else (None, 0), 
            axis=1
        )
        
        # Filter out non-overlapping entries
        overlaps = overlaps[overlaps.apply(lambda x: x[0] is not None)]
        
        # Extract labels and their corresponding overlap times
        overlapping_labels = overlaps.apply(lambda x: x[0])
        
        overlapping_labels.reset_index(drop=True, inplace=True)
        
        labels.append(" ".join(overlapping_labels))
        
        # Initialize a defaultdict to store the summed values
        collapsed_dict = defaultdict(float)
        
        # Iterate over the series and sum values for each key
        for item in overlaps:
            key, value = item
            collapsed_dict[key] += value
        
        # Convert the defaultdict back to a list of tuples 
        collapsed_list = list(collapsed_dict.items())
        non_empty_label_overlap = [item for item in collapsed_list if item[0] is not None]
        collapsed_series = pd.Series(collapsed_list)
        
        overlap_times = collapsed_series.apply(lambda x: x[1])
        overlap_labels = collapsed_series.apply(lambda x: x[0])
        
        
        overlap_times.reset_index(drop=True, inplace=True)
        overlap_labels.reset_index(drop=True, inplace=True)
        
        # Determine the predominant label (the one with the greatest overlap time)
        if not overlap_times.empty:
           # print(overlap_times)
            predominant_label = overlap_labels.iloc[overlap_times.idxmax()]
            predominant_labels.append(predominant_label)
            max_overlap = overlap_times.iloc[overlap_times.idxmax()]
            max_overlaps.append(max_overlap)
            all_overlaps.append(non_empty_label_overlap)
        else:
            predominant_labels.append("")
            max_overlaps.append("")
            all_overlaps.append("")
        
    df_segments["predominant_speaker"] = predominant_labels

    return df_segments
```

### Main Processing Loop

```python
def process_audio_file(model, audio_file_path, level, diarization = False):
    result = model.transcribe(audio_file_path)

    data = result.ori_dict

    if diarization == True:
        diarization_df = diarize(audio_file_path)


    if level == "WORD":
        words_data = []
        for segment in data['segments']:
            for word in segment['words']:
                words_data.append({
                    'word': word['word'],
                    'start': word['start'],
                    'end': word['end'],
                    'probability': word['probability']
                })
        
        timestamp_df = pd.DataFrame(words_data)

    elif level == "SEGMENT":
        utterances_data = []
        for segment in data['segments']:
            utterances_data.append({
                'text': segment['text'],
                'start': segment['start'],
                'end': segment['end'],
                'id': segment['id']
            })
        
        timestamp_df = pd.DataFrame(utterances_data)

    if diarization == True:
        diarization_and_timestamp_df = align_diarization_and_transcription(diarization_df, timestamp_df)
        return diarization_and_timestamp_df 
    else:
        return timestamp_df
```

```python
def df_to_textgrid(df):
    # Create a new TextGrid object
    tg = textgrid.Textgrid()
    
    min_time = df['start'].min()
    max_time = df['end'].max()
    
    # Check if 'predominant_speaker' column exists
    has_speaker_info = 'predominant_speaker' in df.columns
    
    if has_speaker_info:
        # Create an IntervalTier for each unique speaker
        speakers = df['predominant_speaker'].unique()
        for speaker in speakers:
            # Create a tier for the speaker
            speaker_tier =  IntervalTier(str(speaker), [], minT=min_time, maxT=max_time)
            
            # Filter the DataFrame for this speaker
            speaker_df = df[df['predominant_speaker'] == speaker]
            
            # Add intervals to the tier
            for _, row in speaker_df.iterrows():
                interval = Interval(row['start'], row['end'], row['word'])
                speaker_tier.insertEntry(interval, collisionMode='merge')
            
            # Add tier to TextGrid
            tg.addTier(speaker_tier)
    
    # Create a transcription tier (this will be created regardless of speaker info)
    trans_tier = IntervalTier('transcription', [], minT=min_time, maxT=max_time)

    
    for _, row in df.iterrows():
        interval = Interval(row['start'], row['end'], row['text'])
        trans_tier.insertEntry(interval, collisionMode='merge')
    
    tg.addTier(trans_tier)
    
    return tg
```

```python
audio_folder = AUDIO_FOLDER
output_folder = OUTPUT_FOLDER

os.makedirs(output_folder, exist_ok=True)

for filename in os.listdir(audio_folder):
    if filename.lower().endswith(('.wav', '.mp3', '.flac')):  # Add or remove audio formats as needed
        audio_file_path = os.path.join(audio_folder, filename)
        
        print(f"Processing: {filename}")
        
        # Process the audio file
        df_transcript = process_audio_file(model, audio_file_path, level = LEVEL, diarization = DIARIZATION)
        
        # Generate output filename (without extension)
        output_filename = os.path.splitext(filename)[0]
        
        if EXPORT_AS == "CSV":
            output_path = os.path.join(output_folder, f"{output_filename}.csv")
            df_transcript.to_csv(output_path, index=False)
        elif EXPORT_AS == "TXT":
            output_path = os.path.join(output_folder, f"{output_filename}.txt")
            with open(output_path, 'w') as f:
                df_string = df_transcript.to_string(header=False, index=False)
                f.write(df_string)
        else:
            print(f"Unsupported export format: {export_format}")
            continue
        
        print(f"Saved transcription to: {os.path.basename(output_path)}")

    gc.collect()
    torch.cuda.empty_cache()
```

---

## **4. ADDITIONAL STEP FOR GPU USE**

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