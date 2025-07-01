# 📘 **Part 8 – Evaluating Model Accuracy and Performance**

In this section, we will learn how to **evaluate the accuracy** of our ASR pipeline using two key metrics:

- **Word Error Rate (WER)** – How closely the words match a human-created reference transcript
- **Diarization Error Rate (DER)** – How accurately the system assigns speech to the correct speaker

We'll implement WER using the [`jiwer`](https://pypi.org/project/jiwer/) library and DER using [`pyannote-metrics`](https://github.com/pyannote/pyannote-metrics).

---

## 📦 A. Install Required Packages

If you haven’t already, activate your environment (e.g., `whisper_py`) and install the following:

```bash
pip install jiwer
pip install pyannote.metrics
```

---

## 📁 B. File Organization

Before running this script, make sure you have the following files:

```
project_folder/
├── reference_transcripts/
│   └── sample1.txt         # Gold-standard (human) transcript
├── hypothesis_transcripts/
│   └── sample1.txt         # ASR-generated transcript
├── reference_rttm/
│   └── sample1.rttm        # Gold-standard speaker labels
├── hypothesis_rttm/
│   └── sample1.rttm        # ASR-generated speaker diarization
```

---

## 📝 C. Evaluate Word Error Rate (WER)

Create a new Python script or notebook cell:

```python
from jiwer import wer, mer, wil, compute_measures

# Load files
with open("reference_transcripts/sample1.txt", "r") as f:
    reference = f.read()

with open("hypothesis_transcripts/sample1.txt", "r") as f:
    hypothesis = f.read()

# Evaluate
measures = compute_measures(reference, hypothesis)

print("--- WER Evaluation ---")
print(f"WER: {measures['wer']:.3f}")
print(f"MER (Match Error Rate): {measures['mer']:.3f}")
print(f"WIL (Word Information Lost): {measures['wil']:.3f}")
print(f"Insertions: {measures['insertions']}")
print(f"Deletions: {measures['deletions']}")
print(f"Substitutions: {measures['substitutions']}")
```

> 💡 **Note**: This works best if punctuation and casing are consistent. Consider using `jiwer.Compose()` to normalize both texts.

---

## 🎙️ D. Evaluate Diarization Error Rate (DER)

Create a new script (or section) using `pyannote.metrics`:

```python
from pyannote.metrics.diarization import DiarizationErrorRate
from pyannote.core import Annotation, Segment

# Load RTTM files (you can also write a custom parser if needed)
def load_rttm(path):
    annotation = Annotation()
    with open(path, 'r') as file:
        for line in file:
            parts = line.strip().split()
            if len(parts) < 8:
                continue
            start = float(parts[3])
            duration = float(parts[4])
            end = start + duration
            speaker = parts[7]
            annotation[Segment(start, end)] = speaker
    return annotation

reference = load_rttm("reference_rttm/sample1.rttm")
hypothesis = load_rttm("hypothesis_rttm/sample1.rttm")

metric = DiarizationErrorRate()
score = metric(reference, hypothesis)

print("--- DER Evaluation ---")
print(f"DER: {score:.3f}")
```

---

## 🧠 E. Optional: Evaluate Speaker Confusion

If you want to isolate speaker confusion (ignoring missed/extra speech), you can use this alternate metric:

```python
from pyannote.metrics.diarization import speaker_confusion

confusion_metric = speaker_confusion.SpeakerConfusionErrorRate()
conf_score = confusion_metric(reference, hypothesis)

print("--- Speaker Confusion ---")
print(f"Confusion Error Rate: {conf_score:.3f}")
```

> 🔁 Consider looping over multiple files to evaluate full datasets!

---

## 📊 F. Summary

| Metric | Tool | Notes |
|--------|------|-------|
| **WER** | `jiwer` | Text-only comparison |
| **MER/WIL** | `jiwer` | Additional matching metrics |
| **DER** | `pyannote.metrics` | Requires RTTM-format files |
| **Speaker Confusion** | `pyannote.metrics` | Optional, isolates wrong-speaker assignments |

---

## ✅ What’s Next?
Now that you can evaluate your ASR system, you can:
- Compare different model sizes or pipelines
- Quantify performance across many recordings
- Report results for papers, reports, or improvement tracking

> 🚀 Onward to experimentation and optimization!
