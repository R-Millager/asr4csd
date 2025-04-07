# **Day 3: Learning Basic Python for Whisper/Pyannote**

> **Why this is important:** Understanding basic Python operations will help you troubleshoot errors, modify transcription settings, and process data more effectively. Today will also introduce use of Jupyter Notebook as a workspace for coding.

These basic Python skills will be useful for working with Whisper and Pyannote:

- Running Python scripts: `python script.py`
- Using `pandas` for data handling
- Working with lists and dictionaries
- Navigating file paths and working directories in Python
- Using Jupyter Notebook for interactive coding

## **1. Activate Your Environment**

As always when starting work in Python, open **Anaconda Prompt** and activate the `whisper_py` environment:

```sh
conda activate whisper_py
```

## **2. Install and Launch Jupyter Notebook**

If you haven't installed Jupyter Notebook yet, do so with:

```sh
pip install notebook
```

To launch Jupyter Notebook:

```sh
jupyter notebook
```

This will open a browser window where you can create and run Python notebooks.

## **3. Practice in Jupyter Notebook**

Create a new notebook and try the following examples.

### **A. Create and display a simple table**

```python
import pandas as pd

data = {'Speaker': ['Speaker 1', 'Speaker 2'], 'Text': ['Hello!', 'How are you?']}
df = pd.DataFrame(data)
print(df)
```

### **B. Work with lists and dictionaries**

```python
# A dictionary of lists
data = {
    "names": ["Alice", "Bob", "Charlie"],
    "ages": [30, 25, 35]
}

# Access values
print(data["names"])        # Prints the list of names
print(data["ages"][1])      # Prints 25

# Loop over list
for name in data["names"]:
    print(f"Hello, {name}!")
```

---

## **4. Navigating File Paths in Python**

You can check or change your working directory using the `os` module:

```python
import os

# Show current working directory
print(os.getcwd())

# Change directory (adjust the path to your actual folder)
os.chdir("C:/Users/YourName/Documents/asr")
print("New working directory:", os.getcwd())
```

> ⚠️ Be careful when changing directories. Ensure the folder path exists on your machine.

---

## **5. BONUS: Run a Python Script from the Command Line**

Open a text editor and paste the following code into a new file. Save it as `hello_script.py`.

```python
print("Hello from a Python script!")
```

Now, in Anaconda Prompt, navigate to the folder where the file is saved and run:

```sh
python hello_script.py
```

You should see: `Hello from a Python script!`

---

Spend some time experimenting with these basics. Once you're comfortable, you'll be well-prepared for processing audio and transcripts using Whisper and Pyannote!
