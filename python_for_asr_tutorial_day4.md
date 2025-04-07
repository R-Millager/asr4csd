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

