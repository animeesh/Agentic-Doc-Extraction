# Intelligent Document Analysis Agent (Lab 3)

## 📌 Overview
This repository contains the implementation of **Lab 3: Intelligent Document Analysis Agent**, an end-to-end, agentic pipeline for understanding complex documents. The system combines **OCR**, **layout detection**, and **vision-language model (VLM)–powered tools** to analyze text, tables, and charts in a human-like manner.

Instead of treating documents as flat text, the pipeline first understands **structure**, then selectively applies the **right tools to the right content types**, and finally **synthesizes insights** into coherent answers.

---

## 🧠 Key Idea: Agentic Document Understanding
The pipeline mimics how a human analyst reads a report:
1. Scan the document structure
2. Identify different content types (text, tables, charts)
3. Dive deeper into specific sections when required
4. Combine findings into meaningful answers

This is achieved using a **LangChain tool-calling agent** orchestrating multiple AI components.

---

## 🏗️ Architecture Overview
The system consists of **three main stages**:

### 1️⃣ Text Extraction & Reading Order
- **PaddleOCR**: Extracts text, confidence scores, and bounding boxes
- **LayoutLMv3 (LayoutReader)**: Determines correct reading order
- Output: OCR text ordered logically (title → body → sections)

### 2️⃣ Layout Detection
- **PaddleOCR Layout Detection**
- Identifies:
  - Paragraphs
  - Titles
  - Tables
  - Charts / Figures
- Each detected region includes:
  - Region ID
  - Content type
  - Confidence score
  - Bounding box (XYXY)

### 3️⃣ Agentic Processing
- **LangChain Agent** with tool-calling capability
- Uses:
  - OCR text (for text-only questions)
  - Specialized tools (for visual content)

---

## 🧩 Core Components

### Data Structures
- **OCRRegion**: Structured representation of OCR output
- **LayoutRegion**: Structured representation of layout-detected regions

These dataclasses make the pipeline clean, readable, and maintainable.

---

## ✂️ Region Cropping Strategy
For visual reasoning (charts & tables):
- Each layout region is cropped from the original document
- Cropped images are:
  - Converted to Base64
  - Sent individually to the VLM

### Benefits
- 🎯 Focused analysis
- 🔇 Reduced noise
- 💰 Lower API costs

---

## 🛠️ Agent Tools

### 🔹 AnalyzeChart
- Uses a Vision-Language Model (VLM)
- Interprets charts and figures
- Extracts:
  - Chart type
  - Axes
  - Trends
  - Data points
- Returns structured JSON

### 🔹 AnalyzeTable
- Uses VLM for table understanding
- Extracts:
  - Headers
  - Rows
  - Structured tabular data
- Returns clean, machine-readable JSON

Both tools use **carefully designed prompts** to ensure consistent output.

---

## 🤖 The Agent Workflow

1. User asks a question about the document
2. Agent reads the **system prompt**, which includes:
   - Ordered OCR text
   - Layout region metadata
   - Tool descriptions & usage rules
3. Agent decides:
   - Can this be answered from text alone?
   - Or is a visual tool required?
4. Agent optionally calls:
   - `AnalyzeTable`
   - `AnalyzeChart`
5. Agent synthesizes all information into a final response

---

## 🔑 Model & Tools
- **LLM / VLM**: `gpt-4o-mini` (used for cost efficiency)
- **Agent Type**: LangChain Tool-Calling Agent
- **Execution**: `AgentExecutor` with `verbose=True`

---

## ⚙️ Environment Setup

### Environment Variables
Create a `.env` file containing:
```env
OPENAI_API_KEY=your_api_key_here
```

Load it at runtime before agent creation.

---

## 🧪 Testing the System

The pipeline supports multiple query types:

- **Text-only questions** → Answered directly from OCR context
- **Table extraction requests** → Calls `AnalyzeTable`
- **Chart-related questions** → Calls `AnalyzeChart`

Verbose mode allows you to inspect the agent’s reasoning and tool usage.

---

## ⚠️ Known Limitations
- VLMs may struggle with precise localization
- Complex tables and dense charts may cause hallucinations
- Layout reading order is not always perfect
- Scaling this approach requires careful tuning of each component

---

## 🚀 What’s Next
In the next lesson, this manual, multi-component pipeline is replaced by **LandingAI’s Agentic Document Extraction**, which unifies:
- OCR
- Layout analysis
- Reading order reconstruction
- Multimodal reasoning
- Schema-based extraction

…into a **single, scalable agentic workflow**.

---

## 📚 Summary
This lab demonstrates how combining:
- Traditional OCR
- Layout-aware models
- Vision-language models
- Agentic orchestration

can create a **powerful hybrid system** capable of deeply understanding complex documents such as reports, tables, figures, and multi-column layouts.

---

✨ Built as part of **Lab 3 – Intelligent Document Analysis**