# Part 6: Running Audio Files — Full Workflow

**Why this is important**: Now that you have completed the installations and learned the basics, this step serves as a quick-reference workflow for running Whisper and Pyannote on an interaction audio file. We will add in some additional pipeline details to improve functionality, and allow for multiple consecutive audio files to be processed as a batch.

**Additional authorship credit to Isabel Arvelo, M.S., for writing early drafts of portions of this pipeline.**

*Quick start:* If you have already been through this tutorial once and want to directly open the pipeline in Jupyter Notebook, you can go straight there by downloading [this file](pipeline-v2.0.ipynb) and opening it in your working directory.*

> This pipeline supports **batch-processing** — you can place multiple `.wav` files into the raw audio folder, and all will be processed automatically. Note also that this code will **always expect two speakers**, anticipating evaluation of speaker dyads.

---

## **Getting Started: Set Up Your Virtual Environment**

Before running the pipeline, ensure that:
- Your whisper_py environment is activated
- Your working directory matches earlier modules
- Jupyter Notebook is already installed (from Module 2)

```sh
conda activate whisper_py

cd C:\Users\YourName\Documents\asr
jupyter notebook
```

**Open a new notebook** in this directory and proceed.

---

## **0. User Settings**

Update/edit this section once before running the pipeline.

```python
# --- User Settings ---
WORKING_DIR = r"C:\Users\USERNAME\Documents\asr"
AUDIO_FOLDER = "raw_audio_folder"
OUTPUT_FOLDER = "transcription_output"

# Hugging Face access token (required for Pyannote)
HUGGINGFACE_TOKEN = "YOUR_TOKEN_HERE"

# Whisper model choice
WHISPER_MODEL = "base.en"  # e.g., "tiny.en", "base.en", "small.en", etc.

# Processing options
DIARIZATION = True         # Enable speaker diarization
LEVEL = "WORD"             # "WORD" or "SEGMENT"
EXPORT_AS = "CSV"          # "CSV" or "TXT"

print("User settings loaded.")
```

> **Tip:** Models ending in .en are English-only and generally faster and more accurate for English recordings.

---

## **1. Library Imports and Version Checks**

```python

import os
import gc
import torch
import pandas as pd
import stable_whisper

from pyannote.audio import Pipeline
from pyannote.audio.pipelines.utils.hook import ProgressHook
```

Optional but recommended: confirm versions

```python
import importlib.metadata as im

for pkg in [
    "torch",
    "torchaudio",
    "pyannote.audio",
    "huggingface_hub",
    "stable-ts",
]:
    try:
        print(pkg, im.version(pkg))
    except im.PackageNotFoundError:
        print(pkg, "NOT INSTALLED")
```

If these do not match the pinned versions from earlier modules, stop and resolve before continuing.

## **2. Set Up Working Directories and Models**

```python
# Set working directory
os.chdir(WORKING_DIR)

audio_folder = os.path.join(WORKING_DIR, AUDIO_FOLDER)
output_folder = os.path.join(WORKING_DIR, OUTPUT_FOLDER)

os.makedirs(audio_folder, exist_ok=True)
os.makedirs(output_folder, exist_ok=True)

# Load Whisper
model = stable_whisper.load_model(WHISPER_MODEL)
```

Set up Pyannote (if enabled)

```python
if DIARIZATION:
    pipeline = Pipeline.from_pretrained(
        "pyannote/speaker-diarization-3.1",
        use_auth_token=HUGGINGFACE_TOKEN
    )
    pipeline.to(torch.device("cpu"))
```

## **3. Helper Functions**

Speaker diarization with safe ProgressHook fallback

```python
def diarize(audio_path):
    try:
        with ProgressHook() as hook:
            diarization = pipeline(
                audio_path,
                hook=hook,
                min_speakers=2,
                max_speakers=2
            )
    except Exception:
        diarization = pipeline(
            audio_path,
            min_speakers=2,
            max_speakers=2
        )

    rows = []
    for turn, _, speaker in diarization.itertracks(yield_label=True):
        rows.append({
            "start": turn.start,
            "end": turn.end,
            "speaker": speaker
        })

    return pd.DataFrame(rows)
```

Align diarization to Whisper output (overlap-based)

```python
def align_diarization_and_transcription(diar_df, whisper_df):
    speakers = []

    for _, row in whisper_df.iterrows():
        start, end = row["start"], row["end"]
        best_overlap = 0.0
        assigned_speaker = "UNKNOWN"

        for _, d in diar_df.iterrows():
            overlap = max(0.0, min(end, d["end"]) - max(start, d["start"]))
            if overlap > best_overlap:
                best_overlap = overlap
                assigned_speaker = d["speaker"]

        speakers.append(assigned_speaker)

    whisper_df["speaker"] = speakers
    return whisper_df
```

## **4. Process a Single Audio File**

```python
def process_audio_file(model, audio_path, level="WORD", diarization=False):
    result = model.transcribe(audio_path)
    data = result.ori_dict

    diar_df = diarize(audio_path) if diarization else None

    if level == "WORD":
        rows = []
        for seg in data["segments"]:
            for word in seg["words"]:
                rows.append({
                    "text": word["word"],
                    "start": word["start"],
                    "end": word["end"],
                    "probability": word["probability"],
                })
        df = pd.DataFrame(rows)

    elif level == "SEGMENT":
        rows = []
        for seg in data["segments"]:
            rows.append({
                "text": seg["text"],
                "start": seg["start"],
                "end": seg["end"],
            })
        df = pd.DataFrame(rows)

    else:
        raise ValueError("LEVEL must be 'WORD' or 'SEGMENT'")

    if diarization:
        df = align_diarization_and_transcription(diar_df, df)

    return df
```

## **5. Batch Processing Loop**
```python
for filename in os.listdir(audio_folder):
    if filename.lower().endswith((".wav", ".mp3", ".flac")):
        audio_path = os.path.join(audio_folder, filename)
        print(f"Processing: {filename}")

        df = process_audio_file(
            model,
            audio_path,
            level=LEVEL,
            diarization=DIARIZATION
        )

        stem = os.path.splitext(filename)[0]

        if EXPORT_AS == "CSV":
            out_path = os.path.join(output_folder, f"{stem}.csv")
            df.to_csv(out_path, index=False)

        elif EXPORT_AS == "TXT":
            out_path = os.path.join(output_folder, f"{stem}.txt")
            with open(out_path, "w") as f:
                f.write(df.to_string(index=False))

        else:
            raise ValueError(f"Unsupported export format: {EXPORT_AS}")

        print(f"Saved output to: {out_path}")

        gc.collect()
```

---

## **Interpreting Your Output**

- Each row represents a Whisper word or segment
- The speaker column indicates the predominant speaker based on temporal overlap
- Rows labeled UNKNOWN typically reflect silence, noise, or diarization gaps
- If many rows are UNKNOWN, confirm:
    - The same audio file was used for Whisper and Pyannote
	- Diarization spans the full recording