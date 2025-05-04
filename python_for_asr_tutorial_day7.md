# **Day 7: Speech-Language Analysis and Accuracy Measures**

# **NOTE THAT THIS PAGE IS STILL UNDER CONSTRUCTION**

> **Why this is important:** Now that you have a pipeline that successfully estimates timestamped utterances from an audio file, there are two follow-up steps that we need to be able to use these transcripts for speech-language analysis:
- 1. [Tools to convert the ASR transcript](#1-converting-asr-transcripts-for-analysis) to formats for common speech-language analysis
- 2. [Methods to evaluate the accuracy and performance](#2-evaluating-accuracy-and-performance) of your ASR product.

---

## **1. Converting ASR transcripts for analysis**

Speech-language researchers and clinicians use a wide range of tools to code and annotate transcripts for analyis. Although this is not an exhaustive list, we provide methods to convert the ASR output from this pipeline to formats used by three powerful and widely used platforms.

*At present, all conversion code is in R. Teaching R is beyond the scope of this tutorial -- in the future, we hope to have more user-friendly applications for these conversion tools.

### **SALT**

>SALT is XXX (about SALT, link to website).

SALT is, in many ways, the most straightforward... brief words about what the SALT transcript looks like and what the final format should be. Ignores timestamps.

LINK TO CONVERSION R FILE.

### **CLAN**

>CLAN is XXX (about CLAN, link to website).

CLAN has a specific format to follow as well... describe .cha file.

LINK TO CONVERSION R FILE.

### **ELAN**

>ELAN is XXX (about ELAN, link to website).

ELAN's format is xxx, describe .eaf file.

LINK TO CONVERSION R FILE OR DESCRIBE NATIVE CONVERSION FEATURES.

## **2. Evaluating accuracy and performance**


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


FROM a previous tutorial:


### 💻 Check if your CPU or GPU is being used

Whisper can run on either your CPU or GPU, but GPU processing is significantly faster. To check which is being used:

- **On Windows:** Open **Task Manager** → go to the **Performance** tab. Look at **CPU** and **GPU** usage while Whisper is running.
- **If you have an NVIDIA GPU:** In **Anaconda Prompt**, type:
  ```sh
  nvidia-smi
  ```
  This will show active GPU processes and confirm whether Python is using the GPU.

> ⚠️ If you don't see activity, Whisper may be running on the CPU. GPU usage requires a CUDA-compatible version of PyTorch, which is not automatically installed with this tutorial.

---