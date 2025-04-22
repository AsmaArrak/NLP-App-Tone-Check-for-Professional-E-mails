# Tone Check for Professional E‑mails

![Gradio](https://img.shields.io/badge/interface-Gradio-blue) ![PyPI](https://img.shields.io/badge/python-3.8%2B-orange)

## Table of Contents

1. [Introduction](#introduction)
2. [Demo & Usage](#demo--usage)
3. [Documentation](#documentation)
   - [Dataset](#dataset)
   - [Baseline Model (TF‑IDF + Logistic Regression)](#baseline-model-tfidf--logistic-regression)
   - [Transformer Model (DistilBERT)](#transformer-model-distilbert)
   - [User Interface](#user-interface)
4. [Experiments & Results](#experiments--results)
5. [Contributions](#contributions)
6. [Limitations & Future Work](#limitations--future-work)
7. [License](#license)

---

## Introduction

**Tone Check** is a web app that helps users evaluate the emotional tone of professional emails before sending. By pasting your draft into the interface, you receive:

- A **risk badge** indicating whether your message is _Risky_ (🔴), _Neutral_ (⚪), _Caution_ (🟡), or _Positive_ (🟢)
- A list of the **top three emotions** detected, with confidence scores

This ensures clear, professional communication and avoids unintended negative impressions.

## Demo & Usage

**Clone & Install**

```bash
git clone <repo_url>
cd tone-check
pip install -r requirements.txt
python app.py
```

**Run & Interact**

1. Open `http://127.0.0.1:7860` in your browser.
2. Paste your email draft into the textbox.
3. Select `DistilBERT (fine-tuned)` or `TF-IDF baseline`.
4. Click **Analyze**.
5. View the risk badge and top emotions.

<details>
<summary>Example Input/Output</summary>

**Input:**
```
Hi John,
I still haven't received the report you promised last week. Please send it ASAP.
Thanks,
Jordan
```

**Output:**
- **Risk:** 🔴 Risky  
- **Emotions:**
  - annoyance: 0.87  
  - urgency: 0.64  
  - neutral: 0.20

</details>

## Documentation

### Dataset

**GoEmotions** (58k Reddit comments, 27 emotions + neutral)  
Source: https://huggingface.co/datasets/go_emotions  

- Train/validation/test splits  
- Multi-label annotations converted to one-hot vectors  

### Baseline Model (TF‑IDF + Logistic Regression)

- TF‑IDF vectorizer: 40k unigrams + bigrams  
- LogisticRegression (one-vs-rest)  
- Trained on the first emotion label  
- Single-label inference → risk bucket  

### Transformer Model (DistilBERT)

- Pretrained: `distilbert-base-uncased` (66M parameters)  
- Fine-tuned: 28-way sigmoid classification head  
- Training: 3 epochs, batch size 8, LR 2e-5, threshold 0.25  
- Multi-label inference → risk bucket  

### User Interface

- Built with Gradio Blocks
