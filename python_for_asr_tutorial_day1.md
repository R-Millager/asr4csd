# **Day 1: Setting Up Your Environment**

> **Why this is important:** Setting up a proper environment prevents compatibility issues and ensures that all required dependencies are managed correctly. Anaconda simplifies package management and isolates the Whisper and Pyannote setup from any other Python installations that may be on your system.

## **1. Install Anaconda**

To simplify Python dependency management, we will use **Anaconda**. A standard Anaconda download will also include the required Python downloads. Other necessary downloads are detailed below.

1. Download and install [Anaconda](https://www.anaconda.com/products/distribution#download-section).
2. Open **Anaconda Prompt** and create a virtual environment (which we will name `whisper_py`):
   ```sh
   conda create -n whisper_py python=3.9 -y
   conda activate whisper_py
   ```
3. Ensure your Python version is correct:
   ```sh
   python --version
   ```
4. Note that Anaconda has a nice tutorial to introduce you to key terms and concepts for **NOTE TO SELF: ADD MORE DETAIL HERE AND A SPECIFIC LINK TO THE VIDEOS**

## **2. Install FFmpeg (Required for Audio Processing)**

FFmpeg is a widely used Python package to enable audio processing.

> **Checking if FFmpeg is already installed:**
>
> Before proceeding with installation, check if FFmpeg is already installed by running the following command in Command Prompt: **NOTE TO SELF: DO I NEED TO EXPLAIN COMMAND PROMPT, ANACONDA PROMPT? NOTE THAT WE APPEAR TO BE USING IT ABOVE IN LINE 26, NEED TO BE CONSISTENT ACROSS THE DOCUMENT**
>
> ```sh
> ffmpeg -version
> ```
>
> If FFmpeg is installed, you will see version information. If not, follow the steps below to install it.

> **Steps in this section drawn from the following website, where more details can be found:** [PhoenixNAP: How to Install FFmpeg on Windows](https://phoenixnap.com/kb/ffmpeg-windows).

1. Download the latest FFmpeg **release build** from [FFmpeg's official website](https://ffmpeg.org/download.html).
2. Select the **Windows version** and download the **full build ZIP file**. **NOTE TO SELF: CAN PROBABLY MAP THIS TO THE ORIGINAL TUTORIAL A LITTLE BETTER**
3. Once downloaded, extract the ZIP file:
   - Right-click the downloaded ZIP file → **Extract All**.
   - Choose destination as `C:\` and extract the contents.
4. Rename the extracted folder to `ffmpeg`.
5. Inside `C:\ffmpeg`, locate the `bin` folder (which contains `ffmpeg.exe`).
6. Add `C:\ffmpeg\bin` to your **System Environment Variables**:
   - Search for "Edit the system environment variables" in Windows.
   - Click **Environment Variables** → **System Variables** → **Path** → **Edit**.
   - Click **New** and add: `C:\ffmpeg\bin`.
   - Click **OK** to save changes.
7. Verify the installation by running:
   ```sh
   ffmpeg -version
   ```
8. If the installed FFmpeg information is not displayed, you may need to restart the **Command Prompt** and re-activate Python before checking (see above).

---

