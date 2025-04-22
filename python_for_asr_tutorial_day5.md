# **Day 5: Analyzing Pyannote and Whisper Results**

> **Why this is important:** Understanding Pyannote’s output will help you align diarization results with transcripts and ensure accuracy. This tutorial will bring together skills you have learned from previous tutorials, comparing output from Whisper (Part 3), Pyannote (Part 4), and using Jupyter Notebook (Part 2) for visualization.

## **Getting Started: Use Jupyter Notebook for Day 5**

Day 5 brings together all the skills you've learned so far — importing model outputs, comparing timestamps, assigning speaker labels, and exporting aligned results.

The best way to complete these steps is with **Jupyter Notebook**, which lets you:
- Inspect Whisper and Pyannote outputs side-by-side
- Write and debug alignment code in steps
- Export `.csv` or `.json` files for use in your final analysis

> 🧠 **Before you begin**, activate your environment and launch Jupyter:
```sh
conda activate whisper_py
jupyter notebook
```
Then **open a new notebook** and proceed with the steps below.

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
- Save the aligned data in a CSV for easier inspection or analysis.

---

### **How to Create a CSV with Aligned Results**

Here’s a basic method for aligning and exporting data:

1. **Make sure you have output from both models**:
   - Whisper’s transcript should contain **start and end times** for each utterance (e.g., in `whisper_output.json`).
   - Pyannote’s diarization should include **start/end timestamps and speaker labels** (e.g., in `pyannote_output.json`).

2. **Use a Jupyter Notebook and run the following code** to align the two datasets by matching each Whisper segment to the Pyannote speaker active during that time:

```python
import json
import csv

# Load Whisper and Pyannote outputs
with open("whisper_output.json", "r") as f:
    whisper_data = json.load(f)["segments"]

with open("pyannote_output.json", "r") as f:
    pyannote_data = json.load(f)

aligned = []

# Match each Whisper segment to the Pyannote speaker active at its start time
for segment in whisper_data:
    start = segment["start"]
    end = segment["end"]
    text = segment["text"]

    # Find speaker label from Pyannote
    speaker = "UNKNOWN"
    for entry in pyannote_data:
        if entry["start"] <= start < entry["end"]:
            speaker = entry["speaker"]
            break

    aligned.append({
        "start_time": start,
        "end_time": end,
        "speaker": speaker,
        "text": text.strip()
    })

# Save to CSV
with open("aligned_output.csv", "w", newline="", encoding="utf-8") as f:
    writer = csv.DictWriter(f, fieldnames=["start_time", "end_time", "speaker", "text"])
    writer.writeheader()
    writer.writerows(aligned)

print("✅ Aligned CSV saved as 'aligned_output.csv'")
```

3. **Check your output**: The CSV file will contain each utterance, its time range, assigned speaker, and the spoken text.

| start_time | end_time | speaker     | text                          |
|------------|----------|-------------|-------------------------------|
| 0.5        | 2.2      | SPEAKER_00  | What happened to that data, Bones? |
| 2.3        | 4.1      | SPEAKER_01  | Jim, I'm a doctor, not a data scientist! |

> 📁 This aligned CSV will be useful for reviewing your data or preparing it for further analysis in ELAN, CLAN, or other tools for speech-language analysis.

To align Whisper’s transcript with Pyannote’s diarization results:

- Compare **timestamps** between both outputs.
- Assign speaker labels (`SPEAKER_00`, `SPEAKER_01`, etc.) to each transcript segment.
- Optionally, save aligned data in a CSV (covered in later sections).

## **3. Common Issues & Debugging**

- If speaker labels seem **inaccurate**, try a **smaller audio segment**.
- If timestamps don’t match, check if **both Whisper and Pyannote used the same audio file**.
- If diarization runs too slowly, consider a **university server with GPU access**.

---

## **4. (Optional) Inspect Aligned Output in a Table**

Once your aligned CSV is saved, you can quickly view it using `pandas`:

```python
import pandas as pd

df = pd.read_csv("aligned_output.csv")
df.head()
```

This is a helpful way to verify that speaker labels, timestamps, and transcript segments all aligned correctly.