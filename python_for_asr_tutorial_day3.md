# **Day 3: Learning Basic Python for Whisper/Pyannote**

> **Why this is important:** Understanding basic Python operations will help you troubleshoot errors, modify transcription settings, and process data more effectively.

## **1. Activate Your Environment**

Before beginning this lesson, open **Anaconda Prompt** and activate the `whisper_py` environment:

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

> 💡 **Tip:** Save your work frequently in Jupyter Notebook, especially when experimenting with code.

## **3. Key Python Skills to Learn**

These basic Python skills will be useful for working with Whisper and Pyannote:

- Running Python scripts: `python script.py`
- Using `pandas` for data handling (`pip install pandas`)
- Working with lists and dictionaries
- Navigating file paths and working directories in Python

## **4. Practice Using Python for Data Handling**

Create a new notebook in Jupyter and paste in the following code to try it out:

```python
import pandas as pd

data = {'Speaker': ['Speaker 1', 'Speaker 2'], 'Text': ['Hello!', 'How are you?']}
df = pd.DataFrame(data)
print(df)
```

This will display a simple table (like a CSV) in your notebook output.

> 🔹 **Recommended Practice:** Play around with the data — try changing the speaker names, adding new rows, or saving it to a CSV with `df.to_csv("my_table.csv", index=False)`.

---

Feel free to spend more time exploring how Jupyter works. You can also search online for beginner tutorials on Python and pandas if you want extra practice before moving on.
