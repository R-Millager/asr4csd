# Step-by-Step Guide: Running Whisper and Pyannote on Windows for Speech-Language Transcription

## **Overview**

This guide will help you set up and run **Whisper** (for transcription) and **Pyannote** (for speaker diarization) on your Windows 11 device. Since these tools rely on Python and machine learning models, the main focus of this tutorial is to establish the necessary environment configured. Opportunities for additional learning and data science literacy are noted. We will use Anaconda as a user-friendly platform for running Python; although Anaconda is not strictly required for running Python on a PC, we recommend it for beginners. This tutorial was conceived as a 7-day learning process, with manageable goals for each day's topic, however it is a self-guided tutorial that can be taken at the reader's preferred pace.

### **Prerequisites**

- Windows 11 PC (**NOTE TO SELF: ARE THERE MEMORY/PERFORMANCE REQUIREMENTS?)
- Basic familiarity with R and RStudio (Python knowledge **not required**) **NOTE TO SELF: IS THIS REALLY A REQUIREMENT? AT A GLANCE I'M NOT SURE WE ACTUALLY USE R OR RSTUDIO, NEED TO CONFIRM**
- Administrative permissions (and willingness) to install additional tools like Python and Jupyter Notebook
- (Optional) University server access for heavier computations

### **Other Resources**

- [Self-study curriculum](https://github.com/NeuralNine/python-curriculum)
- [Python for R users](https://rebeccabarter.com/blog/2023-09-11-from_r_to_python)
- [Datacamp paid course](https://www.datacamp.com/courses/python-for-r-users)

---

# **Day 1: Setting Up Your Environment**

> **Why this is important:** Setting up a proper environment prevents compatibility issues and ensures that all required dependencies are managed correctly. Anaconda simplifies package management and isolates the Whisper and Pyannote setup from any other Python installations that may be on your system.

## **1. Install Anaconda**

To simplify Python dependency management, we will use **Anaconda**. A standard Anaconda download will also include the required Python downloads. Other necessary downloads are detailed below.

1. Download and install [Anaconda](https://www.anaconda.com/products/distribution#download-section).
2. Open **Anaconda Prompt** and create a virtual environment (which we will name `whisper_py`):
   ```sh
   conda create -n whisper_py python=3.9 -y
   conda activate whisper_py
   ```
3. Ensure your Python version is correct:
   ```sh
   python --version
   ```
4. Note that Anaconda has a nice tutorial to introduce you to key terms and concepts for **NOTE TO SELF: ADD MORE DETAIL HERE AND A SPECIFIC LINK TO THE VIDEOS**

## **2. Install FFmpeg (Required for Audio Processing)**

FFmpeg is a widely used Python package to enable audio processing.

> **Checking if FFmpeg is already installed:**
>
> Before proceeding with installation, check if FFmpeg is already installed by running the following command in Command Prompt: **NOTE TO SELF: DO I NEED TO EXPLAIN COMMAND PROMPT, ANACONDA PROMPT? NOTE THAT WE APPEAR TO BE USING IT ABOVE IN LINE 26, NEED TO BE CONSISTENT ACROSS THE DOCUMENT**
>
> ```sh
> ffmpeg -version
> ```
>
> If FFmpeg is installed, you will see version information. If not, follow the steps below to install it.

> **Steps in this section drawn from the following website, where more details can be found:** [PhoenixNAP: How to Install FFmpeg on Windows](https://phoenixnap.com/kb/ffmpeg-windows).

1. Download the latest FFmpeg **release build** from [FFmpeg's official website](https://ffmpeg.org/download.html).
2. Select the **Windows version** and download the **full build ZIP file**. **NOTE TO SELF: CAN PROBABLY MAP THIS TO THE ORIGINAL TUTORIAL A LITTLE BETTER**
3. Once downloaded, extract the ZIP file:
   - Right-click the downloaded ZIP file → **Extract All**.
   - Choose destination as `C:\` and extract the contents.
4. Rename the extracted folder to `ffmpeg`.
5. Inside `C:\ffmpeg`, locate the `bin` folder (which contains `ffmpeg.exe`).
6. Add `C:\ffmpeg\bin` to your **System Environment Variables**:
   - Search for "Edit the system environment variables" in Windows.
   - Click **Environment Variables** → **System Variables** → **Path** → **Edit**.
   - Click **New** and add: `C:\ffmpeg\bin`.
   - Click **OK** to save changes.
7. Verify the installation by running:
   ```sh
   ffmpeg -version
   ```
8. If the installed FFmpeg information is not displayed, you may need to restart the **Command Prompt** and re-activate Python before checking (see above).

---

# **Day 2: Installing and Running Whisper**

> **Why this is important:** Whisper is the core transcription tool you'll be using. Installing it properly and verifying that it runs correctly is essential before moving forward. At this stage, we will only aim to get Whisper running on your machine. Later, we will configure Whisper to produce a .csv output of the transcript, and explore options for the size of the Whisper model to apply. **NOTE TO SELF: MAKE SURE THIS LAST STUFF IS CORRECT**

## **1. Install Whisper**

1. Open **Anaconda Prompt**.
2. Activate your environment:
   ```sh
   conda activate whisper_py
   ```
3. Install Whisper and dependencies:
   ```sh
   pip install stable-ts whisper-timestamped
   ```
4. Verify installation:
   ```sh
   python -c "import stable_whisper; print('Whisper installed successfully!')"
   ```

## **2. Run a Test Transcription**

1. Place an audio file (e.g., `test_audio.wav`) in the working directory.
2. Run Whisper to generate a transcription:
   ```sh
   python -c "import stable_whisper; model = stable_whisper.load_model('base.en'); result = model.transcribe('test_audio.wav'); print(result.text)"
   ```
3. The transcript should be displayed in the terminal.

---

# **Day 3: Learning Basic Python for Whisper/Pyannote**

> **Why this is important:** Understanding basic Python operations will help you troubleshoot errors, modify transcription settings, and process data more effectively.

## **1. Key Python Skills to Learn**

- Running Python scripts: `python script.py`
- Using `pandas` for data handling (`pip install pandas`)
- Working with lists and dictionaries (for storing results)
- File paths and working directories in Python

## **2. Practice Using Python for Data Handling**

Try running the following script in **Anaconda Prompt** or a **Jupyter Notebook**:
**NOTE TO SELF: IF YOU'RE EVER GOING TO USE JUPYTER NOTEBOOK, MIGHT AS WELL DO IT HERE TOO. MAKE NECESSARY CHANGES BELOW.**

```python
import pandas as pd

data = {'Speaker': ['Speaker 1', 'Speaker 2'], 'Text': ['Hello!', 'How are you?']}
df = pd.DataFrame(data)
print(df)
```

This will display a simple **table structure** like a CSV output.

> 🔹 **Recommended Practice:** Use Jupyter Notebook (`pip install notebook`) for experimenting.

---

# **Day 4: Installing and Running Pyannote**

> **Why this is important:** Pyannote enables speaker diarization, allowing you to identify different speakers in an audio file. This is useful for analyzing conversations with multiple participants.

## **1. Install Pyannote**

1. Open **Anaconda Prompt**.
2. Activate your environment:
   ```sh
   conda activate whisper_py
   ```
3. Install Pyannote:
   ```sh
   pip install pyannote.audio
   ```

## **2. Set Up Hugging Face Authentication**

1. Create a **Hugging Face account** ([https://huggingface.co/](https://huggingface.co/)) if you don't already have one.
2. Generate an **Access Token** by visiting [https://huggingface.co/settings/tokens](https://huggingface.co/settings/tokens).
3. Save your token securely; you will need it to run Pyannote.

## **3. Run Pyannote for Speaker Diarization**

1. Ensure your Hugging Face token is stored securely.

2. Run diarization on an audio file:

   ```sh
   python -c "from pyannote.audio import Pipeline; pipeline = Pipeline.from_pretrained('pyannote/speaker-diarization', use_auth_token='YOUR_TOKEN_HERE'); print(pipeline('test_audio.wav'))"
   ```

3. The output will display timestamps and speaker labels.

> 💡 **Server Note:** Pyannote models are computationally intensive. If your PC struggles, consider using university servers. **NOTE TO SELF - MAY NEED TO ADD MORE DETAIL HERE.**

---

# **Day 5: Analyzing Pyannote Results**

> **Why this is important:** Understanding Pyannote’s output will help you align diarization results with transcripts and ensure accuracy.

## **1. Interpreting Pyannote Output**

When you run Pyannote, it outputs **timestamps and speaker labels**, like this:

```json
[
  {"start": 0.0, "end": 2.5, "speaker": "SPEAKER_00"},
  {"start": 2.6, "end": 5.0, "speaker": "SPEAKER_01"}
]
```

This means **SPEAKER\_00** spoke from **0.0s to 2.5s**, and **SPEAKER\_01** spoke from **2.6s to 5.0s**.

## **2. Aligning Pyannote Results with Whisper Transcripts**

To align Whisper’s transcript with Pyannote’s diarization results:

- Compare **timestamps** between both outputs.
- Assign speaker labels (`SPEAKER_00`, `SPEAKER_01`, etc.) to each transcript segment.
- Optionally, save aligned data in a CSV (covered in later sections).

## **3. Common Issues & Debugging**

- If speaker labels seem **inaccurate**, try a **smaller audio segment**.
- If timestamps don’t match, check if **both Whisper and Pyannote used the same audio file**.
- If diarization runs too slowly, consider a **university server with GPU access**.

---

# **Day 6: Debugging Common Errors**

> **Why this is important:** When working with machine learning models, errors are common. Learning how to debug them efficiently saves time and improves workflow.

## **1. Common Whisper & Pyannote Issues and Fixes**

### **A. Whisper Issues**

- **Issue: Whisper is running slowly**

  - **Fix:** Try using a **smaller model** (`tiny`, `base`, `small`) instead of `large`.
  - **Fix:** Ensure your CPU/GPU is being used efficiently (check Task Manager or `nvidia-smi` for GPU monitoring).

- **Issue: No transcription output / only object reference shown**

  - **Fix:** Ensure you're calling `.text` when printing results:
    ```sh
    python -c "import stable_whisper; model = stable_whisper.load_model('base.en'); result = model.transcribe('test_audio.wav'); print(result.text)"
    ```

### **B. Pyannote Issues**

- **Issue: Authentication error when running Pyannote**

  - **Fix:** Ensure your Hugging Face token is correctly stored and used.
  - **Fix:** Manually log in to Hugging Face in your terminal:
    ```sh
    huggingface-cli login
    ```

- **Issue: Speaker diarization is inaccurate**

  - **Fix:** Try **increasing the model confidence threshold** or using a **higher-quality audio file**.
  - **Fix:** Run diarization on a smaller segment before processing long files.

---

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

# **Day 8: Running an Audio File - Full Workflow**

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