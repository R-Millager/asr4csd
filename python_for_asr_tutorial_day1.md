# **Part 1: Setting Up Your Environment**

> **Why this is important:** Setting up a proper environment prevents compatibility issues and ensures that all required dependencies are managed correctly. Anaconda simplifies package management and isolates the Whisper and Pyannote setup from any other Python installations that may be on your system.

## **1. Install Anaconda**

To simplify Python dependency management, we will use **Anaconda**. A standard Anaconda download will also include the required Python downloads. Other necessary downloads are detailed below.

From here on out, we will always use **Anaconda Prompt** to run scripts and packages.

1. Download and install [Anaconda](https://www.anaconda.com/products/distribution#download-section).
2. Open **Anaconda Prompt** and create a virtual environment (which we will name `whisper_py`). This virtual environment is an isolated workspace where you can install and run packages like Whisper without affecting other projects or system-wide settings on your device:
   ```sh
   conda create -n whisper_py python=3.9.25 -y
   conda activate whisper_py
   ```
3. Ensure your Python version is correct:
   ```sh
   python --version
   ```
You should see `Python 3.9.25`. If not, stop here and recreate the environment before proceeding.
>*Why Python 3.9?*
At the time of writing, this version offers the best compatibility with Whisper, Pyannote, and supporting libraries on Windows.

## **2. Install FFmpeg and PyTorch (required dependencies for Whisper/Pyannote)**

FFmpeg is a widely used tool for processing audio. PyTorch is the machine-learning library that Whisper and Pyannote rely on to run their neural network models. Even if you are using a CPU-only system (no GPU), you still need PyTorch installed to run these tools reliably. We will install a pinned CPU-only version for reproducibility. We will install them directly into our conda environment.

1. With your `whisper_py` environment activated in Anaconda Prompt (completed in the section above), install FFmpeg using the following command:
   ```sh
   conda install -c conda-forge ffmpeg
   ```
  
2. Then, install PyTorch (version 2.5.1) using the following command:
   ```sh
   pip install --index-url https://download.pytorch.org/whl/cpu torch==2.5.1 torchaudio==2.5.1
   ```

3. Ensure FFmpeg and PyTorch were properly installed by checking the version:
   ```sh
   ffmpeg -version
   python -c "import torch, torchaudio; print(torch.__version__); print(torchaudio.__version__)"
   ```
You should see `2.5.1` for both `torch` and `torchaudio`. If you see `2.6.x` or higher, reinstall using the pinned command above.
 
## **Congratulations!**

Once you have finished these steps and you are comfortable starting **Anaconda Prompt**, you are ready for [Step 2](python_for_asr_tutorial_day2.md). In the next step, we will carry on with some basic Python functions in Anaconda Prompt and set up some tools that will be needed to generate your transcripts.
