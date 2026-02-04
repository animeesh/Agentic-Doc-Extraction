# Document Processing Agent (OCR + Rules + LLM Agents)

This repository demonstrates how to build a **simple but powerful document processing agent** by combining **OCR**, **rule-based methods**, and **LLM-based agentic reasoning**. The goal is not to build a perfect production-grade system, but to understand how these components work together and where each of them breaks down.

---

## 🎯 Goal of the Project

- Understand how **OCR** converts documents into raw text
- See why **regex and rules** are brittle on noisy OCR outputs
- Learn how **LLM-based agents (ReAct)** introduce semantic understanding
- Observe real-world failure modes with tables, handwriting, and receipts

---

## 🧱 System Components

### 1. OCR (Eyes)
- **PIL** – load images
- **pytesseract** – extract text from images
- Produces **raw, unstructured text**

### 2. Rules (Legacy Approach)
- Regex patterns to extract fields like `tax` and `total`
- Extremely sensitive to wording, layout, and OCR noise

### 3. LLM Agent (Brain)
- Uses **LangChain ReAct framework**
- Model: `gpt-5-mini`
- Dynamically decides **when to call OCR** and **how to reason over results**

---

## 🛠️ OCR as a Tool

OCR is exposed as a **tool** using the `@tool` decorator:

- Takes an image path as input
- Runs Tesseract OCR
- Returns raw extracted text

During execution, the agent explicitly decides:

> “I need to read the document first” → calls OCR → reasons on the output

This enables **Think → Act → Observe → Think** loops.

---

## 📄 Example 1: Clean Digital Invoice

### Scenario
- High-quality digital invoice
- Clean fonts, perfect lighting, no handwriting

### Observations
- OCR performs very well
- Raw text has **no structure or meaning**

### Regex Failure
- Regex misses the **tax line**
- Incorrectly extracts **subtotal instead of total**

### Why It Failed
- `tax @` instead of `tax <space>`
- `subtotal` appears before `total`
- Regex matches patterns, not meaning

### Agent Result
- Agent calls OCR
- Understands semantic intent
- Correctly extracts **tax** and **final total**
- Returns structured **JSON**

✅ No templates, no rules, no regex

---

## 📊 Example 2: Academic Table (Attention Is All You Need)

### Task
Extract:
- Model name
- Training cost
- FLOPs for **English → German translation**

### OCR Output
- Chaotic text
- Columns misaligned
- Exponents misread
- Decimals replaced with symbols

### Agent Behavior
- Attempts best-effort interpretation
- Understands missing values (e.g., ByteNet has no cost)
- Sometimes misattributes values across columns

### Key Insight
LLMs can **reason under uncertainty**, but cannot recover information that OCR completely corrupts.

---

## ✍️ Example 3: Handwritten Fill-in-the-Blanks

### Task
Extract:
- Student name
- Answers to 10 questions

### OCR Issues
- Student name missing
- Words misread (`am` → `Aum`)
- Numbers interpreted as letters

### Agent Issues
- Hallucinates corrections
- Overcorrects grammar (e.g., `they is` → `they are`)

⚠️ Semantic reasoning ≠ faithful transcription

---

## 🧾 Example 4: Receipt Validation

### Task
Determine whether the receipt total is correct

### OCR Problems
- `$7.95` misread as `$7.99`
- Thermal print artifacts

### Agent Reasoning
- Sums OCR-extracted line items
- Compares calculated total vs printed total
- Concludes **total is incorrect**

❌ Reasoning is correct, but based on **incorrect OCR inputs**

---

## 🧠 Key Takeaways

### OCR
- Excellent at reading clean printed text
- Poor with tables, handwriting, and low-quality scans
- No understanding of structure or meaning

### Regex & Rules
- Extremely brittle
- Fail silently in production
- Cannot generalize across layouts or vendors

### LLM Agents
- Introduce **semantic understanding**
- Can reason over noisy inputs
- Still limited by OCR quality
- Can hallucinate or overcorrect

---

## 🧩 What Real-World Document Systems Need

- OCR
- Layout detection
- Vision-language models
- Agentic workflows
- Grounding & validation loops
- Human-in-the-loop for edge cases

---

## 🚀 What This Repo Demonstrates

This project shows **why modern Intelligent Document Processing (IDP)** systems are moving away from static pipelines and toward **agent-based architectures** that combine tools and reasoning.

---

## 🔜 Next Steps

Lesson 2 explores:

> **Four decades of OCR evolution**

- From classic engines like **Tesseract**
- To deep-learning-based systems like **PaddleOCR**
- And why that evolution matters for real-world workflows

---

📌 *This repository is educational by design. Expect imperfections—they are the point.*

