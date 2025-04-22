# Tone Check for Professional E‑mails

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

## Documentation

### Core Components

- **Python & Gradio**: Frontend UI built with Gradio Blocks for ease of deployment and interactivity.
- **GoEmotions Dataset**: 28 emotion labels, 58k Reddit comments annotated for fine‑tuning.
- **Transformers**: `distilbert-base-uncased` fine‑tuned for multi‑label emotion classification.
- **scikit-learn**: TF‑IDF vectorizer + LogisticRegression baseline.

### Data Flow

```mermaid
flowchart TD
  A[User Input] --> B[Gradio Interface]
  B --> C{Model Choice}
  C --> D[DistilBERT Pipeline]
  C --> E[TF-IDF Baseline]
  D --> F[Sigmoid → Probabilities]
  E --> F[Single‑label Prediction]
  F --> G[Threshold (0.25) → Binary Labels]
  G --> H[Risk Bucket & Top Emotions]
  H --> I[Display Badge & JSON]
```

### DistilBERT Details

- **Architecture**: 6-layer, 66M‑parameter DistilBERT with a 28‑way classification head.
- **Training**:
  - Dataset: GoEmotions (train/validation/test splits)
  - Problem Type: `multi_label_classification`
  - Hyperparameters: 3 epochs, batch size 8, learning rate 2e‑5, threshold optimized at 0.25
- **Limitations**:
  - May misinterpret sarcasm or complex multi-sentence context.
  - Threshold tuning required for different domains.

### TF‑IDF Baseline Details

- **Features**: Unigram+bigram TF‑IDF (max 40,000 features)
- **Classifier**: One‑vs‑rest LogisticRegression, single‑label (first emotion tag)
- **Performance**: Trains in seconds on CPU; micro‑F1 ≈ 0.35 vs 0.53 for DistilBERT

### Experiments

| Model            | micro‑F1 | macro‑F1 | subset‑Acc |
|------------------|----------|----------|------------|
| TF‑IDF LogReg    | 0.350    | 0.280    | 0.200      |
| DistilBERT (ft)  | 0.535    | 0.462    | 0.318      |
| Zero‑shot NLI    | 0.480    | 0.410    | 0.290      |

## Contributions

- Fine‑tuned a pre-trained DistilBERT on GoEmotions for professional tone detection.
- Integrated a dual‑model UI (Transformer & TF‑IDF baseline) for side‑by‑side comparison.
- Built a polished Gradio interface with risk badges, demo examples, and mobile‑friendly layout.
- Performed threshold optimization and multi‑metric evaluation.

## Limitations

- **Domain Shift**: GoEmotions is Reddit‑based; corporate email style may differ.
- **Multi-sentence Context**: The model processes entire input as one sequence, losing long‑context nuance.
- **Emotion Granularity**: Reducing 28 emotions to 4 buckets may oversimplify subtlety.
- **Bias**: Trained on English Reddit data; may not generalize to other cultures or languages.

---

*For questions or to view the code, visit [GitHub repo link here].*

