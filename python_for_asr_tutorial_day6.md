# **Part 6: Running an Audio File - Full Workflow**

**Why this is important**: Now that you have completed the installations and learned the basics, this step serves as a quick-reference workflow for running Whisper and Pyannote on an interaction audio file. We will add in some additional pipeline details to improve functionality. **Additional authorship credit to Isabel Arvelo, M.S., for writing early drafts of portions of this tutorial page and pipeline.**

*NOTE: If you have already been through this tutorial once and want to directly open the pipeline in Jupyter Notebook, you can go straight there by downloading [this file](NEEDTOADD) and opening it in your working directory. You could also [skip to the middle of this tutorial](#4-run-whisper-and-pyannote) to go right to the pipeline code.

# **STILL DO DO FOR THIS TUTORIAL PAGE:**
1. Upload a finished .ipbyn file in the Git to allow users who want to just jump right in to upload.
3. Edit/correct step 5 and below.
4. Add future notes - lags, model sizes, batch processing, etc.
5. On to BatchAlign and the rest of the tutorial!
6. Send to Suma and Hannah to test drive? Send to Isa?

# **Getting Started: Set Up Your Virtual Environment**

As with the previous step, we will begin by setting up your workspace and Jupyter Notebook to run all subsequent code. We will also take care of installing a few extra packages to smooth out our pipeline.

> 🧠 **Before you begin**, activate your environment, install packages, confirm your working directory, and launch Jupyter:
```sh
conda activate whisper_py

cd C:\Users\YourName\Documents\asr
pip install praatio --upgrade
jupyter notebook
```
Then **open a new notebook** and proceed with the steps below.

## **1. Pre-preparation**

1. First you will create a file to upload (`config.json`) with the HuggingFace token you saved in *Step 4*. Open a plain text editor (like *Notepad*) and copy this exact text, replacing `YOUR_TOKEN_HERE` with your Token:
```json
{
  "huggingface": {
    "token": "YOUR_TOKEN_HERE"
  }
}
```
Save the file as `config.json` in your working directory.

2. Next you will import libraries (packages) to use. Copy and run the following code at the top of your new notebook:
```python
import stable_whisper
import pandas as pd # aliases are often used to avoid having to refer to the library by its full name 
import torch
import os
import json
import gc
```

3. Run the following code to better keep track of progress when running the remainder of the pipeline:
```python
from pyannote.audio.pipelines.utils.hook import ProgressHook
from pyannote.core import Timeline, Segment
from pyannote.database.util import load_rttm
from pyannote.audio import Pipeline
from collections import defaultdict
```

4. Select the model you will use by running the following:
```python
model = stable_whisper.load_model('base.en')
```
This code will process everything in English (`.en`) and will use the base model of Whisper.

> 🧠 **Tip: Choosing and Adjusting the Model**
> 
> Whisper models come in different sizes, which affect their speed, memory requirements, and accuracy.  
> When using `stable_whisper`, you can select from:
> 
> - `'tiny'`, `'tiny.en'`
> - `'base'`, `'base.en'`
> - `'small'`, `'small.en'`
> - `'medium'`, `'medium.en'`
> - `'large-v1'`, `'large-v2'`, `'large-v3'`, `'large'`
> 
> Models ending in `.en` are **trained only on English data** and are typically **faster and slightly more accurate** for English speech. Models without `.en` are **multilingual** and can transcribe multiple languages.
> 
> 🔍 For general English transcription with maximum accuracy, `large-v3` is the most powerful model — but it is multilingual by default.
> 
> Choose your model based on your needs for speed, size, and language support. See below for rough details:

| Size   | Parameters | English-only model | Multilingual model | Required VRAM | Relative Speed |
|:-------|:----------:|:------------------:|:------------------:|:-------------:|:--------------:|
| tiny   | 39 M        | tiny.en             | tiny               | ~1 GB         | ~32x           |
| base   | 74 M        | base.en             | base               | ~1 GB         | ~16x           |
| small  | 244 M       | small.en            | small              | ~2 GB         | ~6x            |
| medium | 769 M       | medium.en           | medium             | ~5 GB         | ~2x            |
| large  | 1550 M      | N/A                 | large              | ~10 GB        | 1x             |

## **2. Select inputs and presets**

The following code will ensure that necessary folders are ready for your transcription. You may also note that we have the option to select output by word or segment, and .csv or .txt.

> - `AUDIO_FOLDER`: The folder where your `.wav` files will be located.
> - `OUTPUT_FOLDER`: The folder where your transcription and diarization outputs will be saved.

Copy the following and run in Jupyter:
```python
# --- Import Required Libraries ---
import os
import json
import torch
from pyannote.audio import Pipeline

# --- Define Folder Paths ---
AUDIO_FOLDER = "raw_audio_folder/"
OUTPUT_FOLDER = "transcription_output/"

# --- Ensure Input and Output Folders Exist ---
os.makedirs(AUDIO_FOLDER, exist_ok=True)
os.makedirs(OUTPUT_FOLDER, exist_ok=True)

# --- Set Processing Options ---
DIARIZATION = True   # Options: True, False
LEVEL = "WORD"       # Options: "WORD", "SEGMENT"
EXPORT_AS = "CSV"    # Options: "CSV", "TXT"

# --- Set Up Diarization Pipeline if Requested ---
if DIARIZATION == True:
    # Load HuggingFace token from config.json
    if not os.path.exists('config.json'):
        raise FileNotFoundError("config.json file not found. Please create it before running this script.")

    with open('config.json', 'r') as config_file:
        config = json.load(config_file)
    HUB_TOKEN = config['huggingface']['token']

    # Load pretrained diarization pipeline
    pipeline = Pipeline.from_pretrained(
        "pyannote/speaker-diarization-3.1",
        use_auth_token=HUB_TOKEN
    )

    # Dynamically select device (CPU or CUDA if available)
    device = torch.device("cuda" if torch.cuda.is_available() else "cpu")
    pipeline.to(device)
```

## **3. Prepare audio file(s)**

Put one or more `.wav` files in `raw_audio_folder/` so they are ready to be processed.

## **4. RUN WHISPER AND PYANNOTE**

This is the main event, in which you should be able to run all of the following code to batch transcribe your audio file(s):

```python
def is_overlap(start1, end1, start2, end2):
    """
    Check if two time intervals overlap.
    
    Args:
        start1 (float): Start time of first interval.
        end1 (float): End time of first interval.
        start2 (float): Start time of second interval.
        end2 (float): End time of second interval.
        
    Returns:
        bool: True if intervals overlap, False otherwise.
    """
    return max(start1, start2) < min(end1, end2)
```

```python
def diarize(audio_file_path):
    """
    Perform diarization on an audio file and return a DataFrame of speaker segments.

    Args:
        audio_file_path (str): Path to the audio file.
        
    Returns:
        pd.DataFrame: DataFrame with 'start', 'end', and 'speaker' columns.
    """
    if not os.path.exists(audio_file_path):
        raise FileNotFoundError(f"The audio file {audio_file_path} does not exist.")
    
    with ProgressHook() as hook:
        diarization = pipeline(audio_file_path, hook=hook)
    
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
    """
    Align diarization results with ASR transcription segments to assign a predominant speaker label.

    Args:
        speaker_segs_df (pd.DataFrame): Diarization output with 'start', 'end', 'speaker' columns.
        df_segments (pd.DataFrame): Transcription segments with 'start', 'end' times.

    Returns:
        pd.DataFrame: Updated transcription DataFrame with a 'predominant_speaker' column.
    """
    if speaker_segs_df.empty:
        raise ValueError("The speaker segments DataFrame is empty.")
    if df_segments.empty:
        raise ValueError("The transcription segments DataFrame is empty.")

    labels = []
    predominant_labels = []
    max_overlaps = []
    durations = []
    all_overlaps = []
    
    # Note: For very large datasets (e.g., >5,000 segments),
    # iterating with .iterrows() and .apply() may become slow.
    # Consider using optimized overlap structures (e.g., interval trees) if needed.

    for i, row in df_segments.iterrows():
        
        overlaps = speaker_segs_df.apply(
            lambda x: (x['speaker'], min(row['end'], x['end']) - max(row['start'], x['start'])) 
            if is_overlap(row['start'], row['end'], x['start'], x['end']) else (None, 0), 
            axis=1
        )
        
        overlaps = overlaps[overlaps.apply(lambda x: x[0] is not None)]
        
        overlapping_labels = overlaps.apply(lambda x: x[0])
        overlapping_labels.reset_index(drop=True, inplace=True)
        labels.append(" ".join(overlapping_labels))
        
        collapsed_dict = defaultdict(float)
        
        for item in overlaps:
            key, value = item
            collapsed_dict[key] += value
        
        collapsed_list = list(collapsed_dict.items())
        non_empty_label_overlap = [item for item in collapsed_list if item[0] is not None]
        collapsed_series = pd.Series(collapsed_list)
        
        overlap_times = collapsed_series.apply(lambda x: x[1])
        overlap_labels = collapsed_series.apply(lambda x: x[0])
        
        overlap_times.reset_index(drop=True, inplace=True)
        overlap_labels.reset_index(drop=True, inplace=True)
        
        if not overlap_times.empty:
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
```python
# --- MAIN PROCESSING LOOP ---
for file_name in os.listdir(AUDIO_FOLDER):
    if file_name.endswith(".wav"):
        audio_path = os.path.join(AUDIO_FOLDER, file_name)
        print(f"Processing {audio_path}...")

        # 1. Transcribe with Whisper
        result = model.transcribe(audio_path, regroup=False, verbose=True)

        # 2. Convert segments to DataFrame
        df_segments = pd.DataFrame(result['segments'])

        # 3. (Optional) Perform diarization and align
        if DIARIZATION:
            speaker_segs_df = diarize(audio_path)
            df_segments = align_diarization_and_transcription(speaker_segs_df, df_segments)

        # 4. Select columns for export based on LEVEL
        if LEVEL == "WORD":
            export_data = pd.DataFrame(result['words'])
            if DIARIZATION:
                export_data = align_diarization_and_transcription(speaker_segs_df, export_data)
        else:  # "SEGMENT" level
            export_data = df_segments

        # 5. Export to selected format
        base_filename = os.path.splitext(file_name)[0]
        output_path = os.path.join(OUTPUT_FOLDER, f"{base_filename}_transcript.{EXPORT_AS.lower()}")

        if EXPORT_AS == "CSV":
            export_data.to_csv(output_path, index=False)
        elif EXPORT_AS == "TXT":
            with open(output_path, "w", encoding="utf-8") as f:
                for _, row in export_data.iterrows():
                    start = row.get('start', 0)
                    end = row.get('end', 0)
                    text = row.get('text', '')
                    speaker = row.get('predominant_speaker', '')
                    f.write(f"{start:.2f}-{end:.2f} [{speaker}] {text}\n")

        print(f"Saved transcription to {output_path}")
```

---

## **CONGRATULATIONS**

You should, at this point, have a finished batch of transcripts.  Each .wav file will have its transcription saved to your selected `OUTPUT_FOLDER` in the format you specified (`CSV` or `TXT`).