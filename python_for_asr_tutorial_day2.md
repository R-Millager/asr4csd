# **Day 2: Installing and Running Whisper**

> **Why this is important:** Whisper is the core transcription tool you'll be using. Installing it properly and verifying that it runs correctly is essential before moving forward. At this stage, we will only aim to get Whisper running on your machine. Later, we will configure Whisper align with data that is timestamped and assigned to speakers. We'll also explore options for different model sizes and output formats. **NOTE TO SELF: MAKE SURE THIS LAST STUFF IS CORRECT**

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
  C:\Users\YourName\Documents\asr_example
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

