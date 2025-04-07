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

