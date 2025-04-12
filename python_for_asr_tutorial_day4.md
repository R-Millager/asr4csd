# **Day 4: Installing and Running Pyannote**

> **Why this is important:** Pyannote enables speaker diarization, allowing you to identify different speakers in an audio file with corresponding timestamps. This is critical for producing transcripts of conversations between two speakers. In later tutorial steps, we will align and merge Pyannote output with Whisper transcription to create a final transcript for analysis and coding.

## **1. Set Up Hugging Face Authentication**

> **What is Hugging Face?** Hugging Face is a platform that provides access to powerful pre-trained models for machine learning tasks like speech processing. Pyannote’s diarization models are hosted on Hugging Face, so you’ll need an account and access token to use them. In step 3 of this tutorial, you will grant permission for the use of several Hugging Face models that are used for Pyannote.

1. Create a **Hugging Face account** ([https://huggingface.co/](https://huggingface.co/)) if you don't already have one.
2. Generate an **Access Token** by visiting [https://huggingface.co/settings/tokens](https://huggingface.co/settings/tokens).

   > **What is a token?** An access token is like a secure password that allows your Python scripts to connect to Hugging Face and use their hosted models. It ensures that only authorized users can access the models, helps Hugging Face track usage, and prevents abuse of their services. You’ll paste this token into your code when running Pyannote.

   > **Recommended token settings:**
   > - **Token Role**: `Read`
   > - **Scopes/Permissions**:
   >   - ✅ `Read` access to **Models`** (required)
   >   - ✅ `Read` access to **Datasets`** *(optional — not required for Pyannote)*
   > - ❌ No write, admin, or space access needed

3. Save your token securely; you will need it to run Pyannote.

---

## **2. Install Pyannote**

1. Open **Anaconda Prompt**.
2. Activate your environment:
   ```sh
   conda activate whisper_py
   ```
3. Install Pyannote. Note that this step can take quite a while when installing for the first time:
   ```sh
   pip install pyannote.audio
   ```

---

## **3. Run Pyannote for Speaker Diarization**

> ⚠️ **One-time setup steps required:** Before you can run the diarization model, you must manually accept usage terms for two Hugging Face model repositories:
>
> 1. Visit [https://huggingface.co/pyannote/speaker-diarization](https://huggingface.co/pyannote/speaker-diarization)
> 2. Click **"Access repository"**
> 3. Accept the terms and conditions. You will need to enter some personal information (i.e., affiliation, website, data type).
> 4. Then visit [https://huggingface.co/pyannote/segmentation](https://huggingface.co/pyannote/segmentation)
> 5. Again, click **"Access repository"** and accept the terms
>
> Without these steps, the model download will fail, even if you have a valid token.

1. Ensure your Hugging Face token is stored securely.

2. Run diarization on an audio file using the following code. Be sure to confirm your working directory and to update the code with accurate *audio file name* and *hugging face token* (from the previous step):

   ```sh
   python -c "from pyannote.audio import Pipeline; pipeline = Pipeline.from_pretrained('pyannote/speaker-diarization', use_auth_token='YOUR_TOKEN_HERE'); print(pipeline('test_audio.wav'))"
   ```

3. The output will display timestamps and speaker labels.

> 💡 **Server Note:** Pyannote models are computationally intensive. If your PC struggles, consider using university servers. **NOTE TO SELF - MAY NEED TO ADD MORE DETAIL HERE.**

---
