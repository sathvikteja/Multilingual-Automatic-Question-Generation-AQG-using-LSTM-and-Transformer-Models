# 📚 Multilingual MCQ Generator

A pipeline-based NLP system that generates **Multiple Choice Questions (MCQs)** from a given paragraph using **transformer models, lexical resources, and translation techniques**.

---

## 🚀 Overview

This project builds an end-to-end system that:

- Accepts input text in **any language**
- Generates:
  - ✅ Question  
  - ✅ Correct Answer  
  - ✅ Distractors (wrong options)  

It combines **deep learning, NLP, and translation** into a modular pipeline.

---

## 🔄 Complete System Flow


Input Text (any language)
↓
Language Detection (langdetect)
↓
Translate → English (googletrans)
↓
Sentence Splitting (NLTK)
↓
Answer Extraction (T5 Model)
↓
Question Generation (T5 Model)
↓
Distractor Generation (WordNet + GPT-2)
↓
Translate back to original language
↓
Final MCQ Output


---

## 🧠 Core Architecture

### 🔹 Pipeline Used

- **QGPipeline (Main Pipeline)**
  - Answer Extraction
  - Question Generation
  - Distractor Generation

### 🔹 Other Pipelines (Not Used)

- `MultiTaskQAQGPipeline` → QA + QG  
- `E2EQGPipeline` → Direct QG  

---

## 🤖 Models Used

| Task | Model |
|------|------|
| Question Generation | `valhalla/t5-small-qg-hl` |
| Answer Extraction | `valhalla/t5-base-qa-qg-hl` |
| Distractor Filtering | `gpt2` |

---

## 🧠 How the System Works

### 1️⃣ Answer Extraction

- Input format:

extract answers: <hl> sentence <hl>


- Uses a transformer model to **generate important answer spans**
- Not rule-based — **generative approach**
- Based on probability:

P(word | context)


- Extracts:
  - entities
  - dates
  - concepts

---

### 2️⃣ Question Generation

- Input format:

generate question: context with <hl> answer <hl>


- Model learns mapping:

(Context + Answer) → Question


- Uses:
  - Encoder → understands context
  - Decoder → generates question token-by-token

- Learns patterns:
  - person → who
  - place → where
  - date → when
  - concept → what

- Uses **beam search (num_beams=4)** for better quality

---

### 3️⃣ Distractor Generation

#### Step 1: Target Word Extraction
- Uses last word of answer

#### Step 2: WordNet
Generates:
- synonyms
- antonyms
- hyponyms
- hypernyms

#### Step 3: GPT-2 Filtering
- Ranks candidates using:

"Which is more related to X?"


#### Step 4: Final Selection
- Top 3 distractors
- Insert correct answer randomly

---

### 4️⃣ Multilingual Handling

- Detect language using `langdetect`
- Translate input → English
- Process using models
- Translate output → original language

⚠️ Uses translation-based approach (not multilingual models)

---

## 🧠 Design Decisions

- ✅ **Answer-first approach**
  - Ensures correct and precise questions

- ✅ **Two-model architecture**
  - Separate models for answer extraction and question generation

- ✅ **Prompt engineering**
  - `"extract answers:"`
  - `"generate question:"`
  - `<hl>` tokens

- ✅ **Translation-based multilingual support**

- ✅ **Hybrid distractor generation**
  - WordNet + GPT-2

---

## 🧠 Pretraining vs Fine-tuning

### Pretraining (T5/BART)
- Learns:
  - grammar
  - semantics
- Does NOT learn:
  - question generation

### Fine-tuning (Valhalla Models)
- Learns:

context + answer → question


✔️ Your project uses **pretrained + fine-tuned models**  
❌ No training done in this project

---

## 🧠 Key Concepts

- Sequence-to-Sequence Learning
- Conditional Text Generation
- Attention Mechanism
- Prompt Engineering
- Beam Search Decoding

---

## 🛠️ Installation

```bash
pip install transformers
pip install torch
pip install nltk
pip install langdetect
pip install googletrans==3.1.0a0
📦 Setup
import nltk
nltk.download('wordnet')
nltk.download('punkt')
▶️ Usage
from pipeline import pipeline

qg = pipeline("question-generation")

text = "Delhi is the capital of India."

output = qg(text)

print(output)
📌 Sample Output
[
  {
    "question": "What is the capital of India?",
    "answer": "Delhi",
    "distractors": ["Mumbai", "Chennai", "Delhi", "Kolkata"]
  }
]
⚠️ Limitations
Translation errors may affect output
WordNet has limited vocabulary
GPT-2 ranking is heuristic
Multi-word answers not handled well
Last-word heuristic reduces accuracy
Global variable usage (ans)
🚀 Future Improvements
Use mT5 for true multilingual support
Use BERT embeddings for distractors
Improve multi-word answer handling
Replace WordNet with knowledge graphs
Use LLMs for distractor generation
Remove translation dependency
🧠 Tech Stack
Python
PyTorch
HuggingFace Transformers
NLTK (WordNet)
GPT-2
Google Translate API
📂 Project Structure
.
├── pipeline.py
├── requirements.txt
├── README.md
🎯 Key Takeaways
Uses fine-tuned transformer models
Relies on prompt engineering
Demonstrates end-to-end NLP system design
Combines DL + NLP + translation
Built with modular pipeline architecture

🙌 Acknowledgements
HuggingFace Transformers
Valhalla (T5 QG models)
NLTK WordNet
OpenAI GPT-2
