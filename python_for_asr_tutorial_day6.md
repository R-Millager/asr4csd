# **Day 6: Running an Audio File - Full Workflow**

**Why this is important**: Now that you have completed the installations and learned the basics, this step serves as a quick-reference workflow for running Whisper and Pyannote on an audio file. Use this guide whenever you process new files.

## **1. Activate Your Environment**

Before running anything, ensure your virtual environment is activated:

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

