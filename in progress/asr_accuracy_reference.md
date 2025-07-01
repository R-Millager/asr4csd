
# 📚 Offline Reference: ASR Transcript Accuracy, Formatting, and Evaluation

## 1. File Formatting for ASR Accuracy Evaluation

### A. Common Formats

| Format | Best For | Notes |
|--------|----------|-------|
| `.cha` (CHAT/CLAN) | Rich transcription, child language analysis | Used in CHILDES. Supports speaker IDs, tiers, and timestamps. |
| `.csv` | Easy scripting and analysis | Recommended for utterance- or word-level comparisons. One row per unit. |
| `.txt` | Simpler text comparison | Good for WER if pre-processed carefully. |
| `.rttm` | Diarization evaluation | Used with `pyannote.metrics` to assess DER. One line per speech segment. |

### B. Sample `.csv` for WER

| utterance_id | speaker | start_time | end_time | transcript          |
|--------------|---------|------------|----------|---------------------|
| utt1         | CHI     | 0.000      | 1.200    | I want the red one. |
| utt2         | PAR     | 1.300      | 2.500    | You want the red one? |

Clean up punctuation and casing before running WER.

---

## 2. Underlying Principles: WER and DER

### A. Word Error Rate (WER)

WER compares a *hypothesis* (your ASR output) to a *reference* (the ground truth).

**WER Formula**:
\[
WER = \frac{S + D + I}{N}
\]

- **S** = Substitutions (wrong word)
- **D** = Deletions (missing word)
- **I** = Insertions (extra word)
- **N** = Number of words in the reference

**Example**:
- Reference: `I want the red one`
- Hypothesis: `I want red`

Result:
- S = 0, D = 1 (`the`), I = 0
- WER = (0+1+0)/5 = **0.20**

### B. Diarization Error Rate (DER)

DER compares who was speaking when. Requires time-aligned speaker labels.

**DER Formula**:
\[
DER = \frac{FA + MISS + ERROR}{Total\_time}
\]

- **FA** = False alarm (ASR says someone spoke when no one did)
- **MISS** = Missed speech (someone spoke but ASR says no one)
- **ERROR** = Speaker confusion
- **Total_time** = Total time covered in reference

Requires `.rttm` files and tolerant matching (e.g., 250ms collar).

---

## 3. Manual Calculation (By Hand)

### A. WER by Hand

1. Align the hypothesis and reference line by line.
2. Mark:
   - `~` = Substitution
   - `-` = Deletion
   - `+` = Insertion

**Reference**: `I want the red one`  
**Hypothesis**: `I want red`  

Align:
```
I      I       ✓
want   want    ✓
the    -       D
red    red     ✓
one    -       D
```

S=0, D=2, I=0 → WER = 2/5 = 0.40

---

### B. DER by Hand (Simplified)

1. Draw timelines from 0 to end time in seconds.
2. Mark speaker turns in reference and hypothesis.
3. Count:
   - Time missed
   - Time wrongly labeled
   - Time falsely added
4. Total all errors / total duration

Use color-coded timelines to help with visual alignment.

---

## 4. Possible Research Designs

### A. Within-Recording Comparison

- Compare ASR vs. hand transcripts for **same audio**.
- Metrics: WER, DER, and time taken to create transcript.
- Unit of analysis: utterance or word.

### B. Time-Efficiency Study

- Compare time needed to produce transcripts with and without ASR prefill.
- Use timestamped editing software to track edits.
- Collect subjective ratings of usability and trust.

### C. Training/Testing Study

- Use hand-coded corpus as training/test set.
- Evaluate generalization of ASR across:
  - Speaker familiarity
  - Child vs. adult speech
  - Environmental noise

### D. Inter-Rater vs. ASR Comparison

- Compare ASR to multiple human transcribers.
- Evaluate:
  - Agreement (e.g., Krippendorff's alpha)
  - Speed
  - Post-editing burden

---

## 5. Key References

1. **Karita, S. et al. (2019).**  
   *A Comparative Study on Transformer vs RNN in Speech Applications*  
   https://arxiv.org/abs/1909.06317

2. **Watanabe, S. et al. (2017).**  
   *ESPnet: End-to-End Speech Processing Toolkit*  
   https://arxiv.org/abs/1804.00015

3. **Garofolo, J. S. et al. (1993).**  
   *DARPA TIMIT Acoustic-Phonetic Continuous Speech Corpus*

4. **Anguera, X. et al. (2012).**  
   *Speaker Diarization: A Review of Recent Research*  
   https://doi.org/10.1109/TASL.2011.2125954

---

## ✅ Notes

- Normalize your text before comparison: lowercase, strip punctuation, trim whitespace.
- Use the `--collar` option in diarization tools to ignore short overlaps or boundary fuzziness.
- Manual DER is labor-intensive — best done on short samples.

> ✨ Use this resource as a reference during offline writing or planning!
