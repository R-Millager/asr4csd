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

