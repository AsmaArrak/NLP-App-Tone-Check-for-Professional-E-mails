# Tone Check for Professional E‑mails
code: https://colab.research.google.com/drive/1zEuFe6gPUOrAVF0cXoJnPshGYYdWUTxK?usp=sharing

## Introduction

**Tone Check** is a web application that helps users assess the emotional tone of draft emails before sending. By pasting your message into the interface, you receive:

- A color-coded risk badge (🔴 Risky, ⚪ Neutral, 🟡 Caution, 🟢 Positive)
- A list of the top three detected emotions with confidence scores

This tool ensures your communications maintain a professional tone and avoid unintended negative impressions.

## Usage

1. **Launch the demo**: Click the Gradio link (or visit the hosted URL) to open the app.
2. **Paste your email**: Enter your draft into the text box.
3. **Choose a model**: Select between the fine‑tuned **DistilBERT** or the **TF‑IDF LogReg** baseline.
4. **Analyze**: Click **Analyze ✨** to see the risk badge and top emotions.
5. **Try examples**: Use one-click examples to explore typical scenarios.

<details>
<summary>Example Input</summary>

```
Hi team,

I still haven’t received the report you promised last week. Please send it ASAP.

Thanks,
Jordan
```

**Output**:
- Risk level: 🔴 Risky
- Top emotions:
  - annoyance: 0.87
  - urgency: 0.64
  - neutral: 0.20
</details>
-------------------------
## Documentation

### Dataset

**Source & Annotations**: I used the GoEmotions dataset, which contains **58,009** Reddit comments manually annotated for **27 emotion categories** plus a **neutral** class. Each comment was labeled by three annotators, and any label agreed on by at least two annotators was retained. The final dataset splits are:

- **Training**: 42,241 examples  
- **Validation**: 5,426 examples  
- **Test**: 5,427 examples  

I converted each list of emotion tags into a **28-dimensional binary vector** (multi-hot) for model fine‑tuning.

### Baseline Model (TF‑IDF + Logistic Regression)

- **Library**: `scikit-learn`  
- **Vectorizer**: `TfidfVectorizer` with a vocabulary of the top 40,000 unigrams and bigrams by frequency.
- **Classifier**: `LogisticRegression` (one-vs-rest) trained on the *first* emotion tag for a single-label baseline.  
- **Performance**: CPU training in under 30 seconds.  

### Transformer Model (DistilBERT)

- **Library**: `transformers` from Hugging Face  
- **Backbone**: `distilbert-base-uncased` (66 million parameters) pre-trained on general English corpora.
- **Fine‑tuning**:
  - **Task**: Multi-label classification with **28 output heads** (sigmoid activations).  
  - **Hyperparameters**: 3 epochs, batch size 8, learning rate 2e-5, weight decay 0.01.  
  - **Compute**: Fine‑tuning on a single NVIDIA T4 GPU in approximately 12 minutes.  
  - **Thresholding**: The optimal probability threshold of **0.25** was selected by maximizing micro‑F1 on the validation split.



          --------------------------------


##  Results

| Model                   | micro‑F1 | macro‑F1 | subset‑Acc |
| ----------------------- | -------- | -------- | ---------- |
| TF‑IDF LogReg           | 0.504    | 0.260    | 0.504      |
| DistilBERT (fine‑tuned) | 0.100    | 0.023    | 0.020      |




## Limitations

- **Domain Shift**: GoEmotions is Reddit‑based; corporate email style may differ.
- **Multi-sentence Context**: The model processes entire input as one sequence, losing long‑context nuance.
- **Emotion Granularity**: Reducing 28 emotions to 4 buckets may oversimplify subtlety.
- **Bias**: Trained on English Reddit data; may not generalize to other cultures or languages.

---


