# **Part 6: Running an Audio File - Full Workflow**

**Why this is important**: Now that you have completed the installations and learned the basics, this step serves as a quick-reference workflow for running Whisper and Pyannote on an interaction audio file. We will add in some additional pipeline details to improve functionality. **Additional authorship credit to Isabel Arvelo, M.S., for writing early drafts of portions of this tutorial page and pipeline.**

*Use this guide whenever you want to process new files with the pipeline we have introduced.*

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
> Choose your model based on your needs for speed, size, and language support. See below for more details:

| Model        | Size (parameters) | Relative Speed | Accuracy (English) | Notes |
|:-------------|:------------------|:---------------|:-------------------|:------|
| `tiny`       | 39M               | Very Fast       | Lower              | Good for quick drafts, mobile |
| `tiny.en`    | 39M               | Very Fast       | Lower (optimized for English) | English only |
| `base`       | 74M               | Fast            | Moderate           | Good baseline model |
| `base.en`    | 74M               | Fast            | Moderate (better for English) | English only |
| `small`      | 244M              | Moderate        | High               | Balanced speed and quality |
| `small.en`   | 244M              | Moderate        | High (for English)  | English only |
| `medium`     | 769M              | Slower          | Very High          | Good for multilingual tasks |
| `medium.en`  | 769M              | Slower          | Very High (for English) | English only |
| `large-v


## **2. Select inputs and presets**

Copy the following code into Jupyter and edit everything to reflect your files and device.

```python
conda activate whisper_py
```

## **2. Set Your Working Directory**

Navigate to your working directory where your audio files are stored:

```python
cd C:\Users\USERNAME\Documents\asr
```

## **3. Run Whisper for Transcription**

Use Whisper to generate a transcription:

```python
python -c "import stable_whisper; model = stable_whisper.load_model('base.en'); result = model.transcribe('my_audio.wav'); print(result.text)"
```

*For larger or more accurate models, replace 'base.en' with 'medium.en' or 'large.en'.*

## **4. Run Pyannote for Speaker Diarization**

Ensure you have a Hugging Face token set up before running:

```python
python -c "from pyannote.audio import Pipeline; pipeline = Pipeline.from_pretrained('pyannote/speaker-diarization', use_auth_token='YOUR_TOKEN_HERE'); print(pipeline('my_audio.wav'))"
```

## **5. Save and Export Results**

To save results to a CSV file:

```python
import pandas as pd

# Load model and transcribe
test_audio = "my_audio.wav"
model = stable_whisper.load_model('base.en')
result = model.transcribe(test_audio)

# Convert to DataFrame
df = pd.DataFrame(result.segments)

# Save to CSV
df.to_csv("transcription_results.csv", index=False)

print("Transcript saved to transcription_results.csv")
```

