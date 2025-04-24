# **Part 8: Using Batchalign2 for CHAT Transcript Processing**

> **Why this is important:** Batchalign2 is a tool for processing audio files for CHAT/CLAN end use. The tool is a part of the TalkBank project and is available for use via [this GitHub repository](https://github.com/TalkBank/batchalign2). I summarize the key points of Batchalign here in accord with my broader project to make these pipelines more accessible for clinicians and researchers, however *all credit* for this tool is due to Houjun Liu, Brian MacWhinney, and their colleagues with TalkBank. See [Liu et al. (2023)](https://pubs.asha.org/doi/10.1044/2023_JSLHR-22-00642) for a detailed description of Batchalign and its constituent processes and goals.

---

## **1. Set Up a New Conda Environment**

Create a dedicated environment for Batchalign2 to manage dependencies effectively.

1. Open **Anaconda Prompt**.
2. Create and activate a new environment named `batchalign_py` with Python 3.11:
   ```sh
   conda create -n batchalign_py python=3.11 -y
   conda activate batchalign_py
   ```

---

## **2. Install Batchalign2**

Batchalign2 can be installed directly from PyPI.

```sh
pip install -U batchalign
```

> **Note:** If you encounter issues with `pip`, ensure it's installed:
> ```sh
> python -m ensurepip --upgrade
> ```

---

## **3. Prepare Input and Output Directories**

Batchalign2 operates on input and output directories. Let's set up these directories:

1. Create directories for input and output:
   ```sh
   mkdir C:\Users\YourName\Documents\ba_input
   mkdir C:\Users\YourName\Documents\ba_output
   ```

2. Place your sample `.wav` (CHAT transcript) and corresponding audio files (`.wav`, `.mp3`, or `.mp4`) into the `ba_input` directory.

---

## **4. Basic Usage of Batchalign2**

Batchalign2 provides three primary commands:

### **A. Transcribe**

Performs ASR on audio files.

```sh
batchalign transcribe --lang=eng C:\Users\YourName\Documents\ba_input C:\Users\YourName\Documents\ba_output
```

### **B. Align**

Aligns existing transcripts with audio.

```sh
batchalign align C:\Users\YourName\Documents\ba_input C:\Users\YourName\Documents\ba_output
```

### **C. Morphotag**

Performs morphosyntactic analysis on transcripts.

```sh
batchalign morphotag --lang=eng C:\Users\YourName\Documents\ba_input C:\Users\YourName\Documents\ba_output
```

> **Note:** Replace `eng` with the appropriate three-letter ISO language code for your data.

---

## **5. Using Batchalign2 in Jupyter Notebook**

For more interactive analysis, you can use Batchalign2 within a Jupyter Notebook.

1. Install Jupyter Notebook in your environment:
   ```sh
   pip install notebook
   ```

2. Launch Jupyter Notebook:
   ```sh
   jupyter notebook
   ```

3. In a new notebook, you can use Batchalign2 as follows:

```python
import batchalign as ba

# Create a new Document from an audio file
doc = ba.Document.new(media_path="C:/Users/YourName/Documents/ba_input/audio.wav", lang="eng")

# Initialize a pipeline for ASR and morphosyntactic analysis
pipeline = ba.BatchalignPipeline.new("asr,morphosyntax", lang="eng")

# Process the document
processed_doc = pipeline(doc)

# Access the transcript
transcript = processed_doc.transcript(include_tiers=False, strip=True)
print(transcript)
```

---

## **6. Additional Resources**

- **Batchalign2 GitHub Repository:** [https://github.com/TalkBank/batchalign2](https://github.com/TalkBank/batchalign2)
- **TalkBank Resources:** [https://talkbank.org](https://talkbank.org)
