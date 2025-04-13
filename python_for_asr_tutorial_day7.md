# **Day 7: Options and Advanced Skills**

> **Why this is important:** As your projects grow, you'll want more flexibility and efficiency. This section introduces batch transcription, alternative model sizes, and how to scale up your workflow to university or cloud computing resources.

---

## **1. Batch Transcription and Exporting Results**

When working with multiple audio files, you can automate transcription with batch processing.

```python
import stable_whisper
import os

model = stable_whisper.load_model("base.en")

audio_files = [f for f in os.listdir("audio_directory") if f.endswith(".wav")]

for file in audio_files:
    result = model.transcribe(os.path.join("audio_directory", file))
    print(f"Transcript for {file}:
{result.text}
")
```

To export all results into a CSV file:

```python
import pandas as pd

# Example transcription results
transcriptions = [
    {"file": "audio1.wav", "text": "Hello, world!"},
    {"file": "audio2.wav", "text": "How are you today?"}
]

df = pd.DataFrame(transcriptions)
df.to_csv("transcriptions.csv", index=False)
```

You can also export segment-level data like this:

```python
test_audio = "my_audio.wav"
model = stable_whisper.load_model('base.en')
result = model.transcribe(test_audio)

df = pd.DataFrame(result.segments)
df.to_csv("transcription_results.csv", index=False)

print("✅ Transcript saved to 'transcription_results.csv'")
```
---

## **2. Running on University or Cloud Servers**

As transcription and diarization tasks grow in complexity, more computing power can help. Here's how to scale up.

### **A. GPU Acceleration**

Whisper and Pyannote run significantly faster on a GPU.

- Use your university’s GPU servers if available.
- Or consider cloud platforms like Google Colab Pro, AWS, or Azure.

### **B. SSH and Remote Access**

To connect to university servers:

```sh
ssh your_username@server_address
```

Some systems require multi-factor authentication (MFA).

### **C. Replicating Environments on a Server**

Transfer your Anaconda environment:

```sh
conda env export > environment.yml
scp environment.yml your_username@server_address:/home/your_username/
```

On the server:

```sh
conda env create -f environment.yml
conda activate whisper_py
```

### **D. Batch Jobs with SLURM**

If your university server uses SLURM, submit jobs like this:

```sh
sbatch job_script.sh
```

Example SLURM script:

```bash
#!/bin/bash
#SBATCH --job-name=whisper_transcription
#SBATCH --output=output.log
#SBATCH --time=02:00:00
#SBATCH --gres=gpu:1
conda activate whisper_py
python transcribe.py
```
---

## **3. Final Thoughts and Future Considerations**

You now have the tools to:

✅ Install and run Whisper and Pyannote  
✅ Transcribe and diarize audio files  
✅ Align transcripts with speaker labels  
✅ Save results in accessible formats  
✅ Optimize workflows through batching  
✅ Scale up to advanced computing environments  

### **Where to go next:**
- Try diarizing noisier or more complex files
- Experiment with different Whisper model sizes (`tiny`, `medium`, `large`)
- Explore Pyannote's documentation for custom thresholds or segmentation tuning
- Build your own pipelines with diarization + ASR + coding integration

---

# **End of Day 7**