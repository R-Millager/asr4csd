# **Day 4: Installing and Running Pyannote**

> **Why this is important:** Pyannote enables speaker diarization, allowing you to identify different speakers in an audio file with corresponding timestamps. This is critical for producing transcripts of conversations between two speakers. In later tutorial steps, we will align and merge Pyannote output with Whisper transcription to create a final transcript for analysis and coding.

**NOTE TO SELF: NEED TO SORT OUT PYANNOTE VERSION, INDIVIDUAL TRAINING MODELS, ETC.**

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

1. Open **Anaconda Prompt**. In order to navigate some of the privileges required to run Pyannote, you should **open Anaconda Prompt in 'Administrator Mode' by right-clicking Anaconda Prompt prior to opening.**
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

2. Run this in your Anaconda Prompt before running the diarization command:
   ```sh
   set HF_HUB_DISABLE_SYMLINKS_WARNING=1
   ```
>    This disables symlinks for Hugging Face's caching system and avoids related errors.

3. Run diarization on an audio file using the following code. Be sure to confirm your working directory and to update the code with accurate *audio file name* and *hugging face token* (from the previous step):

   ```sh
   python -c "from pyannote.audio import Pipeline; pipeline = Pipeline.from_pretrained('pyannote/speaker-diarization', use_auth_token='YOUR_TOKEN_HERE'); print(pipeline('test_audio.wav'))"
   ```

4. The output will display timestamps and speaker labels.

> 💡 **Server Note:** Pyannote models are computationally intensive. If your PC struggles, consider using university servers. **NOTE TO SELF - MAY NEED TO ADD MORE DETAIL HERE.**

---

## **4. Save Pyannote Output in Jupyter Notebook**

To use Pyannote results in later tutorials, you’ll want to **save the diarization output** as a `.json` or `.csv` file.

The best way to do this is by using **Jupyter Notebook**, which lets you write and run Python code interactively.

---

### **Steps:**

1. Make sure your conda environment is activated:
   ```sh
   conda activate whisper_py
   ```

2. Launch Jupyter Notebook:
   ```sh
   jupyter notebook
   ```

3. In the browser window that opens, create a new notebook (select the `whisper_py` environment if prompted).

4. Paste and run the following code in a cell:

#### **A. Run Pyannote and save output as `.json`**

```python
from pyannote.audio import Pipeline
import json

# Replace with your Hugging Face token and your audio file name
token = "YOUR_TOKEN_HERE"
audio_file = "test_audio.wav"

pipeline = Pipeline.from_pretrained("pyannote/speaker-diarization", use_auth_token=token)
diarization = pipeline(audio_file)

# Convert results to list of dictionaries
segments = []
for turn, _, speaker in diarization.itertracks(yield_label=True):
    segments.append({
        "start": turn.start,
        "end": turn.end,
        "speaker": speaker
    })

# Save to JSON
with open("pyannote_output.json", "w") as f:
    json.dump(segments, f, indent=2)

print("✅ Pyannote output saved as 'pyannote_output.json'")
```

#### **B. (Optional) Also save as `.csv`**

```python
import csv

with open("pyannote_output.csv", "w", newline="") as f:
    writer = csv.DictWriter(f, fieldnames=["start", "end", "speaker"])
    writer.writeheader()
    writer.writerows(segments)

print("✅ Pyannote output also saved as 'pyannote_output.csv'")
```

---

> 📁 **Reminder:** You’ll use one of these output files in Day 5 to align speaker labels with Whisper transcripts.