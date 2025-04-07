# **Day 6: Debugging Common Errors**

> **Why this is important:** When working with machine learning models, errors are common. Learning how to debug them efficiently saves time and improves workflow.

## **1. Common Whisper & Pyannote Issues and Fixes**

### **A. Whisper Issues**

- **Issue: Whisper is running slowly**

  - **Fix:** Try using a **smaller model** (`tiny`, `base`, `small`) instead of `large`.
  - **Fix:** Ensure your CPU/GPU is being used efficiently (check Task Manager or `nvidia-smi` for GPU monitoring).

- **Issue: No transcription output / only object reference shown**

  - **Fix:** Ensure you're calling `.text` when printing results:
    ```sh
    python -c "import stable_whisper; model = stable_whisper.load_model('base.en'); result = model.transcribe('test_audio.wav'); print(result.text)"
    ```

### **B. Pyannote Issues**

- **Issue: Authentication error when running Pyannote**

  - **Fix:** Ensure your Hugging Face token is correctly stored and used.
  - **Fix:** Manually log in to Hugging Face in your terminal:
    ```sh
    huggingface-cli login
    ```

- **Issue: Speaker diarization is inaccurate**

  - **Fix:** Try **increasing the model confidence threshold** or using a **higher-quality audio file**.
  - **Fix:** Run diarization on a smaller segment before processing long files.

---

