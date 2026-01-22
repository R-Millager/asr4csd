# **Part 3: Installing and Running Whisper**

> **A note on Whisper models:** This tutorial uses `stable-ts`, a wrapper around OpenAI's Whisper model that improves timestamp handling. You will interact with Whisper through the `stable_whisper` Python interface, but the underlying transcription model is still Whisper.

## **1. Install Whisper**

1. Open **Anaconda Prompt**.
2. Activate your environment:
   ```sh
   conda activate whisper_py
   ```
3. Install Whisper and dependencies:
   ```sh
   pip install stable-ts==2.18.1 whisper-timestamped==1.15.8
   ```
   >These versions are "pinned" to ensure consistent behavior and avoid breaking changes in future updates.
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
  cd "C:\Users\YourName\Documents\asr"
   ```

Replace the example path above with the actual location of your audio files.

## **3. Run a Test Transcription**

1. Place an audio file (e.g., `test_audio.wav`) in the working directory.
2. Run Whisper to generate a transcription:
   ```sh
   python -c "import stable_whisper; model = stable_whisper.load_model('base.en'); result = model.transcribe('test_audio.wav', word_timestamps=True); print(result.text)"
   ```
3. If this command runs without errors and prints text to the terminal, Whisper is installed correctly. If you see an error here, stop and resolve it before continuing—later steps assume Whisper is functioning.

---

## **4. Save Whisper Output in Jupyter Notebook**

To use Whisper's transcription in later tutorials, you’ll want to save the output as a `.json` file.

This is easiest to do using **Jupyter Notebook**, which we introduced in the previous tutorial.

---

### **Steps:**

1. With your "whisper_py" environment still active, launch Jupyter Notebook:
   ```sh
   jupyter notebook
   ```

2. You should see a new browser window open with Jupyter notebook. In the browser window, create a new notebook. Ensure the notebook is using the `whisper_py` kernel; otherwise, Whisper may not be available.

3. Paste and run the following code in a cell:

#### **A. Save output as `.json`**

```python
import stable_whisper
import json

# Load the model (you can change 'base.en' to another model size if desired)
model = stable_whisper.load_model("base.en")

# Transcribe your audio file
result = model.transcribe("test_audio.wav", word_timestamps=True)

# Save the full result (includes segments with start/end times)
with open("whisper_output.json", "w") as f:
    json.dump(result.to_dict(), f, indent=2)

print("✅ Whisper output saved as 'whisper_output.json'")
```

> The `.json` file preserves all metadata produced by Whisper (including timing and segmentation) and serves as the most complete intermediate format.

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
#### **C. (Optional) Save word-level timestamps as `.csv`**

```python
# Extract word-level timestamps
segments = result.to_dict()["segments"]

# Collect words across all segments
words = []
for segment in segments:
    if "words" in segment:
        words.extend(segment["words"])

# Save word-level data to CSV
import csv
with open("whisper_words.csv", "w", newline="", encoding="utf-8") as f:
    writer = csv.DictWriter(f, fieldnames=["start", "end", "word"])
    writer.writeheader()
    for word in words:
        writer.writerow({
            "start": word["start"],
            "end": word["end"],
            "word": word["text"].strip()
        })

print("✅ Word-level timestamps saved as 'whisper_words.csv'")
```

> 📁 **Reminder:** Some of these saved files will be used in Step 5 to align the transcript with speaker diarization from Pyannote.

## **Notes about Whisper performance**

By default, this tutorial uses the `base.en` model, which offers a balance of speed and accuracy for CPU-based systems.

To do this, change the model loading line in your code:

```python
model = stable_whisper.load_model("base.en")
```

to use a smaller model, such as:

```python
model = stable_whisper.load_model("tiny.en")
```

Here are a few common model options (from smallest to largest):

- `tiny.en` – very fast, lower accuracy
- `base.en` – balanced speed and accuracy (used in this tutorial)
- `small.en` – slower but more accurate
- `medium.en`, `large` – higher accuracy but much slower

> 📝 The `.en` suffix loads English-only versions of the models, which are slightly faster and more memory-efficient than multilingual versions.

## Congratulations!

After this step, you are able to use Whisper as an open-source tool for generating transcripts from audio. In [Step 4](python_for_asr_tutorial_day4.md) we will add the *diarization* step to assign utterances to individual speakers.