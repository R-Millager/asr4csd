# **Part 1: Setting Up Your Environment**

> **Why this is important:** Setting up a proper environment prevents compatibility issues and ensures that all required dependencies are managed correctly. Anaconda simplifies package management and isolates the Whisper and Pyannote setup from any other Python installations that may be on your system.

## **1. Install Anaconda**

To simplify Python dependency management, we will use **Anaconda**. A standard Anaconda download will also include the required Python downloads. Other necessary downloads are detailed below.

From here on out, we will always use **Anaconda Prompt** to run scripts and packages.

1. Download and install [Anaconda](https://www.anaconda.com/products/distribution#download-section).
2. Open **Anaconda Prompt** and create a virtual environment (which we will name `whisper_py`). This virtual environment is an isolated workspace where you can install and run packages like Whisper without affecting other projects or system-wide settings on your device:
   ```sh
   conda create -n whisper_py python=3.9 -y
   conda activate whisper_py
   ```
3. Ensure your Python version is correct:
   ```sh
   python --version
   ```

## **2. Install FFmpeg (Required for Audio Processing)**

FFmpeg is a widely used tool for processing audio. We will install it directly into our conda environment.

> **Why this is important:** FFmpeg enables your Python environment to handle audio input and output, which is essential for transcription tools.

1. With your `whisper_py` environment activated in Anaconda Prompt (completed in the section above), install FFmpeg using the following command:
   ```sh
   conda install -c conda-forge ffmpeg
   ```
  
2. Ensure FFmpeg was properly installed by checking the version:
   ```sh
   ffmpeg -version
   ```
  
## **Congratulations!**

Once you have finished these steps and you are comfortable starting **Anaconda Prompt**, you are ready for [Step 2](python_for_asr_tutorial_day2.md). In the next step, we will carry on with some basic Python functions in Anaconda Prompt and set up some tools that will be needed to generate your transcripts.
