# **Day 9: Expanding to University or Third-Party Servers**

**Why this is important**: As transcription and diarization tasks grow in complexity, you may need more computing power. This section provides an overview of what to consider when moving to a university cluster or cloud services.

## **1. Key Considerations**

### **A. GPU Acceleration**

Whisper and Pyannote run significantly faster on a GPU compared to a CPU.

If your university provides access to GPU-equipped machines, consider running your transcription there.

Alternatively, services like Google Colab Pro, AWS, and Azure offer GPU instances.

### **B. SSH & Remote Access**

If using a university server, you will likely need to connect remotely using SSH:

ssh your_username@server_address

Some servers may require additional authentication steps, such as multi-factor authentication (MFA).

### **C. Conda or Virtual Environments on Servers**

Ensure you can replicate your Anaconda environment on the university server:

```python
conda env export > environment.yml
scp environment.yml your_username@server_address:/home/your_username/
```

Then, on the server:

```python
conda env create -f environment.yml
conda activate whisper_py
```

### **D. Batch Processing & Job Scheduling**

If the university server uses SLURM for job scheduling, you may need to submit batch jobs:

sbatch job_script.sh

Example SLURM script:

```python
#!/bin/bash
#SBATCH --job-name=whisper_transcription
#SBATCH --output=output.log
#SBATCH --time=02:00:00
#SBATCH --gres=gpu:1
conda activate whisper_py
python transcribe.py
```

## **2. Next Steps**

Research available university computing resources and verify their specifications.

Test a small transcription job on the server before scaling up.

Consider cloud options if university resources are unavailable or insufficient.


---

# **Summary: What You Have Learned**

✅ Installed Anaconda, Python, FFmpeg, Whisper, and Pyannote\
✅ Ran transcription with Whisper\
✅ Ran speaker diarization with Pyannote\
✅ Understood when to use local vs. server resources\
✅ Learned just enough Python for audio processing\
✅ Gained insight into scaling up to university or cloud computing resources

---

# **End of Guide**

# **Summary: What You Have Learned**


✅ Installed Anaconda, Python, FFmpeg, Whisper, and Pyannote\
✅ Ran transcription with Whisper\
✅ Ran speaker diarization with Pyannote\
✅ Understood when to use local vs. server resources\
✅ Learned just enough Python for audio processing\
✅ Gained insight into scaling up to university or cloud computing resources

---

# **End of Guide**