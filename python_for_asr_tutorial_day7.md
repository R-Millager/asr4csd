# **Day 7: Optimizing Workflows**

> **Why this is important:** Making your transcription and diarization process more efficient will save time when working with multiple files.

## **1. Batch Processing: Running Multiple Audio Files**

To transcribe multiple audio files in a directory:

```python
import stable_whisper
import os

model = stable_whisper.load_model("base.en")

audio_files = [f for f in os.listdir("audio_directory") if f.endswith(".wav")]

for file in audio_files:
    result = model.transcribe(os.path.join("audio_directory", file))
    print(f"Transcript for {file}:\n{result.text}\n")
```

## **2. Saving and Exporting Results to CSV**

```python
import pandas as pd

# Example transcription result
transcriptions = [
    {"file": "audio1.wav", "text": "Hello, world!"},
    {"file": "audio2.wav", "text": "How are you today?"}
]

df = pd.DataFrame(transcriptions)
df.to_csv("transcriptions.csv", index=False)
```

---


Use Whisper to generate a transcription:
```sh
python -c "import stable_whisper; model = stable_whisper.load_model('base.en'); result = model.transcribe('my_audio.wav'); print(result.text)"
```
For larger or more accurate models, replace `'base.en'` with `'medium.en'` or `'large.en'`.


Ensure you have a Hugging Face token set up before running:
```sh
python -c "from pyannote.audio import Pipeline; pipeline = Pipeline.from_pretrained('pyannote/speaker-diarization', use_auth_token='YOUR_TOKEN_HERE'); print(pipeline('my_audio.wav'))"
```


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

---

