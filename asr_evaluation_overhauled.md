
# 📚 Offline Reference: Evaluating ASR Accuracy and Efficiency Using CLAN and CSV Workflows

This guide is designed to walk you through **how to evaluate automatic speech recognition (ASR)** output for both **accuracy** (what was said, and who said it) and **efficiency** (how much time/effort ASR saves you).

You will learn how to:
- Prepare and compare your **CLAN (.cha)** or **CSV** transcripts
- Calculate **Word Error Rate (WER)** and **Diarization Error Rate (DER)** by hand and with Python
- Choose appropriate tools and file formats for different types of evaluations
- Design research comparing ASR pipelines to manual transcription
- Explore relevant, focused academic references

---

## 1. 📁 Organizing Transcripts for Evaluation

You can use either of the following transcript formats:

### A. CLAN (`.cha` files)

- Includes speaker IDs (`*CHI:` or `*PAR:`), tiers, and often timestamps.
- Use CLAN tools (`freq`, `kwal`, `diff`, `timedur`) to preprocess or compare transcripts.
- Best for CHAT-formatted corpora and fine-grained annotation.

**Tip**: Use CLAN’s `diff` command to compare a hand-coded `.cha` file to an ASR-generated one.

### B. CSV (for automated or scriptable workflows)

| filename     | speaker | start_time | end_time | text                |
|--------------|---------|------------|----------|---------------------|
| pilotSimon   | CHI     | 0.00       | 1.24     | I want the red one. |
| pilotSimon   | PAR     | 1.25       | 2.60     | You want the red one? |

- Timestamps in seconds or milliseconds
- Speaker ID as string
- Text should be cleaned (lowercased, no punctuation) for WER

---

## 2. 📏 Word Error Rate (WER)

### A. By Hand

WER compares a hypothesis to a reference transcript:

**WER = (Substitutions + Deletions + Insertions) / Reference Word Count**

#### Example:
Reference: `I want the red one`  
Hypothesis: `I want red`

| Word # | REF | HYP | Error Type     |
|--------|-----|-----|----------------|
| 1      | I   | I   | correct        |
| 2      | want| want| correct        |
| 3      | the | –   | deletion (D)   |
| 4      | red | red | correct        |
| 5      | one | –   | deletion (D)   |

- Substitutions: 0
- Deletions: 2
- Insertions: 0
- WER = 2 / 5 = **0.40**

Use this on 3–5 utterances to manually evaluate ASR quality.

---

### B. In Python (using `jiwer`)

```python
from jiwer import compute_measures

ref = "I want the red one"
hyp = "I want red"

results = compute_measures(ref, hyp)
print(f"WER: {results['wer']:.2f}")
```

📦 `pip install jiwer`

Use `jiwer.Compose()` for preprocessing (lowercase, strip punctuation, etc.).

---

## 3. 🗣️ Diarization Error Rate (DER)

DER assesses **who spoke when** and whether the system got it right.

**DER = (Speaker Confusion + Missed Speech + False Alarm) / Total Speech Time**

### A. By Hand (small sample)

1. On paper, draw timelines for each speaker from 0s to N seconds.
2. Mark actual speech vs. ASR-predicted speech.
3. Identify:
   - **Missed** = speech present in reference but missing in hypothesis
   - **False alarm** = ASR labels speech where none exists
   - **Confusion** = wrong speaker assigned

Time mismatch of ±250ms is typically allowed.

---

### B. In Python (using `pyannote.metrics`)

1. Convert your diarized output to `.rttm` format (one line per speech segment).
2. Use this script:

```python
from pyannote.metrics.diarization import DiarizationErrorRate
from pyannote.core import Annotation, Segment

def load_rttm(path):
    annotation = Annotation()
    with open(path) as f:
        for line in f:
            parts = line.strip().split()
            start = float(parts[3])
            duration = float(parts[4])
            speaker = parts[7]
            annotation[Segment(start, start + duration)] = speaker
    return annotation

ref = load_rttm("reference/sample.rttm")
hyp = load_rttm("asr/sample.rttm")

metric = DiarizationErrorRate()
print("DER:", metric(ref, hyp))
```

📦 `pip install pyannote.metrics`

---

## 4. 🧪 Research Design Ideas

### A. Efficiency and Accuracy Comparison

- **Sample**: ~10 audio files, each with both ASR and hand transcripts
- **Metrics**: WER, DER, time taken to transcribe
- **Measure**: Total time to create transcript (ASR-assisted vs. hand-coded)

### B. Post-editing Study

- Provide ASR transcripts to participants
- Track number of edits and time to finalize
- Compare to hand-coding from scratch

### C. Inter-rater Reliability vs. ASR

- Compare ASR to multiple human coders
- Use agreement metrics like Cohen’s Kappa or Krippendorff’s Alpha

### D. Tier-Specific Accuracy

- Evaluate accuracy separately for adult and child speech
- Measure diarization errors (wrong speaker) per tier

---

## 5. 📚 Recommended References

1. **Lamel, L. & Gauvain, J.-L. (2002).**  
   *Speech Recognition Evaluation in the NIST Framework*  
   [Link](https://www.nist.gov/system/files/documents/2021/06/14/LREC2002-RT04.pdf)

2. **Watanabe, S. et al. (2020).**  
   *CHiME-6 Challenge: Tackling multispeaker speech recognition for unsegmented recordings*  
   https://arxiv.org/abs/2004.09249

3. **Goldwater, S. et al. (2010).**  
   *What to evaluate and how: Evaluation metrics for learning from child-directed speech*  
   [CogSci Proceedings](https://escholarship.org/uc/item/4v37c8xg)

4. **Gomez, R. et al. (2023).**  
   *Evaluating Speech Recognition Tools for Child-Adult Interaction Research*  
   (A more recent applied comparison of Whisper, Google, Rev, etc.)

---

## ✅ Final Notes

- WER is easy to calculate, but doesn't consider speaker accuracy — always pair it with diarization or utterance-level precision.
- You can script all comparisons in Python, but also use CLAN's `diff` or `kwal` for hands-on review.
- Don’t forget to document preprocessing choices (e.g., punctuation stripping, speaker tag handling).

> 🧭 Use this guide as a reference while writing, editing, or planning your evaluations offline!
