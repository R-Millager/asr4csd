# Step-by-Step Guide: Running Whisper and Pyannote on Windows for Speech-Language Transcription

## **Overview**

This guide will help you set up and run **Whisper** (for transcription) and **Pyannote** (for speaker diarization) on your Windows 11 device. Since these tools rely on Python and machine learning models, the main focus of this tutorial is to establish the necessary environment configured. Opportunities for additional learning and data science literacy are noted. We will use Anaconda as a user-friendly platform for running Python; although Anaconda is not strictly required for running Python on a PC, we recommend it for beginners. This tutorial was conceived as a 7-day learning process, with manageable goals for each day's topic, however it is a self-guided tutorial that can be taken at the reader's preferred pace.

### **Prerequisites**

- Windows 11 PC (**NOTE TO SELF: ARE THERE MEMORY/PERFORMANCE REQUIREMENTS?)
- Basic familiarity with R and RStudio (Python knowledge **not required**) **NOTE TO SELF: IS THIS REALLY A REQUIREMENT? AT A GLANCE I'M NOT SURE WE ACTUALLY USE R OR RSTUDIO, NEED TO CONFIRM**
- Administrative permissions (and willingness) to install additional tools like Python and Jupyter Notebook
- (Optional) University server access for heavier computations

### **Other Resources**

- [Self-study curriculum](https://github.com/NeuralNine/python-curriculum)
- [Python for R users](https://rebeccabarter.com/blog/2023-09-11-from_r_to_python)
- [Datacamp paid course](https://www.datacamp.com/courses/python-for-r-users)

## Table of Contents

| Day | Title | Key Topics |
|-----|-------|------------|
| Intro | [Overview & Setup](python_for_asr_tutorial_intro.md) | Installation goals, prerequisites, and learning path |
| Day 1 | [Setting Up Your Environment](python_for_asr_tutorial_day1.md) | Anaconda, FFmpeg installation |
| Day 2 | [Installing and Running Whisper](python_for_asr_tutorial_day2.md) | Installing Whisper, test transcription |
| Day 3 | [Learning Basic Python for Whisper/Pyannote](python_for_asr_tutorial_day3.md) | Python basics, pandas, Jupyter |
| Day 4 | [Installing and Running Pyannote](python_for_asr_tutorial_day4.md) | Pyannote setup, Hugging Face token |
| Day 5 | [Analyzing Pyannote Results](python_for_asr_tutorial_day5.md) | Diarization output, alignment |
| Day 6 | [Debugging Common Errors](python_for_asr_tutorial_day6.md) | Troubleshooting Whisper & Pyannote |
| Day 7 | [Optimizing Workflows](python_for_asr_tutorial_day7.md) | Batch transcription, CSV export |
| Day 8 | [Running an Audio File - Full Workflow](python_for_asr_tutorial_day8.md) | Step-by-step process |
| Day 9 | [Expanding to University or Third-Party Servers](python_for_asr_tutorial_day9.md) | GPU usage, SSH, job scheduling |
| Day 10 | [Summary and Next Steps](python_for_asr_tutorial_day10.md) | Recap, review, final notes |

