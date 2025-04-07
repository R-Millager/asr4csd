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

## **2. Install pandas package.

If you haven't installed the pandas package yet, now is the time to do so.

> `pandas` is a powerful Python library for data analysis and manipulation. It allows you to work with data in structured formats like tables, making it easy to filter, sort, calculate, and export data—similar to how you might work in Excel or R.

Copy the following and run in Anaconda prompt:

```sh
pip install pandas
```

## **3. Install and Launch Jupyter Notebook**

If you haven't installed Jupyter Notebook yet, now is the time to do so.

> `Jupyter Notebook` is an interactive tool for writing and running Python code in your web browser. You can see immediate results while experimenting with code, and there is space to add notes.

Copy the following and run in Anaconda prompt:

```sh
pip install notebook
```

To launch Jupyter Notebook from Anaconda prompt:

```sh
jupyter notebook
```

This will open a browser window where you can create and run Python notebooks.

## **4. Practice in Jupyter Notebook**

Create a new notebook and try the following examples.
Optional viewing: Here is [an Anaconda tutorial on Jupyter Notebook](https://freelearning.anaconda.cloud/get-started-with-anaconda/18571).

### **A. Open a new Jupyter Notebook**

The newly opened Jupyter browser window will resemble a directory.
Click New > Python 3 to create a blank notebook. By default, this will be named `Untitled.ipynb`.
Open the new (Untitled) notebook.

### **B. Create and display a simple table**

Within the new notebook, rename it "test1" (File > Rename). The .ipynb extension will be preserved).

In the blank command prompt of the notebook, copy the following:

```python
import pandas as pd

data = {'Speaker': ['Speaker 1', 'Speaker 2'], 'Text': ['Good day to you, sir!', 'Fine day to you, ma'am.']}
df = pd.DataFrame(data)
print(df)
```

Then click the "run" (▶️) button to see a sample transcript given in a table format.

### **C. Work with lists and dictionaries**

Further experiment with key Python concepts by copying the following code and running (▶️) in Jupyter.

```python
# A dictionary of lists
data = {
    "names": ["Kirk", "Spock", "Bones"],
    "ages": [32, 103, 39]
}

# Access values
print(data["names"])        # Prints the list of names
print(data["ages"][2])      # Prints the third item (count from 0) in the list of ages

# Loop over list
for name in data["names"]:
    print(f"Greetings, {name}!")
```

Lists are sometimes referred to as vectors in R.

---

## **5. Assigning Working Directories**

It is important to understand navigating file directories so that Python knows where to find files on your device. You can check or change your working directory using the `os` module:

```python
import os

# Show current working directory
print(os.getcwd())

# Change directory (adjust the path to your actual folder)
os.chdir("C:/Users/YourName/Documents/asr") #<- here is where you put your test folder!
print("New working directory:", os.getcwd())
```

> ⚠️ Before running this code, be sure you have selected the folder you want. On your machine, you should have a working folder with codes and test audio files for this project.
> ⚠️ Also be sure to *always* use forward slashes (/) rather than backslashes (\) in your directory assignment.

---

## **6. BONUS: Run a Python Script from the Command Line**

Open a text editor and paste the following code into a new file. Save it as `hello_script.py`.

```python
print("This practice Python script says, 'Live long and prosper.'")
```

Now, in Anaconda Prompt, navigate to the folder where the file is saved and run:

```sh
python hello_script.py
```

You should see: `This practice Python script says, 'Live long and prosper.'`

---

Spend some time experimenting with these basics. Once you're comfortable, you'll be well-prepared for processing audio and transcripts using Whisper and Pyannote!
