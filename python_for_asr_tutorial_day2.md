# **Part 2: Installing and Running Whisper**

> **Why this is important:** Whisper is the core transcription tool you'll be using. Installing it properly and verifying that it runs correctly is essential before moving forward. At this stage, we will only aim to get Whisper running on your machine. Later, we will configure Whisper align with data that is timestamped and assigned to speakers. We'll also explore options for different model sizes and output formats.

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

## **2. Confirm Your Working Directory**
Before running Whisper, it's important to confirm your current working directory (where Python will look for your audio file).

In Anaconda Prompt, type the following command to display your current working directory:

   ```sh
  cd
   ```

This will display the folder you're currently working in (usually something like `C:\Users\YourName`).

If you need to move to a different directory (for instance, a folder that contains your audio files), type:

   ```sh
  cd "C:\Users\YourName\Documents\asr_example"
   ```

Replace the example path above with the actual location of your audio files.

## **3. Run a Test Transcription**

1. Place an audio file (e.g., `test_audio.wav`) in the working directory.
2. Run Whisper to generate a transcription:
   ```sh
   python -c "import stable_whisper; model = stable_whisper.load_model('base.en'); result = model.transcribe('test_audio.wav'); print(result.text)"
   ```
3. The transcript should be displayed in the terminal.

---

## **4. Save Whisper Output in Jupyter Notebook**

To use Whisper's transcription in later tutorials, you’ll want to save the output as a `.json` file.

This is easiest to do using **Jupyter Notebook**, which allows you to view, test, and save results interactively.

---

### **Steps:**

1. Make sure your environment is active:
   ```sh
   conda activate whisper_py
   ```

2. Launch Jupyter Notebook:
   ```sh
   jupyter notebook
   ```

3. In the browser window, create a new notebook.

4. Paste and run the following code in a cell:

#### **A. Save output as `.json`**

```python
import stable_whisper
import json

# Load the model (you can change 'base.en' to another model size if desired)
model = stable_whisper.load_model("base.en")

# Transcribe your audio file
result = model.transcribe("test_audio.wav")

# Save the full result (includes segments with start/end times)
with open("whisper_output.json", "w") as f:
    json.dump(result.to_dict(), f, indent=2)

print("✅ Whisper output saved as 'whisper_output.json'")
```

---

#### **B. (Optional) Also save segment data as `.csv`**

```python
import csv

# Extract just the segments (start, end, text)
segments = result.to_dict()["segments"]

# Save segments to CSV
with open("whisper_output.csv", "w", newline="", encoding="utf-8") as f:
    writer = csv.DictWriter(f, fieldnames=["start", "end", "text"])
    writer.writeheader()
    for segment in segments:
        writer.writerow({
            "start": segment["start"],
            "end": segment["end"],
            "text": segment["text"].strip()
        })

print("✅ Whisper segment data saved as 'whisper_output.csv'")
```

> 📁 **Reminder:** These saved files will be used in Day 5 to align the transcript with speaker diarization from Pyannote.

## **Troubleshooting Whisper**

### **Whisper is running slowly**
- Try using a **smaller model** such as `tiny`, `base`, or `small` instead of `large`.
- Check if your CPU/GPU is being used efficiently. On Windows, use Task Manager; for GPU users, try `nvidia-smi`.

### **No transcription output / only object reference appears**
- Ensure you're accessing the `.text` attribute correctly:
  ```python
  print(result.text)
  ```