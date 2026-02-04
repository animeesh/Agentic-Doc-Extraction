# PaddleOCR with Agent Integration – Lab 2

## Overview
This lab demonstrates how **PaddleOCR** can be used for text detection and recognition, how its output can be integrated with an **LLM-based agent**, and how **layout detection** helps address real‑world document challenges. The lesson builds from basic OCR on receipts to complex documents such as tables, handwritten forms, reports, academic articles, and bank statements.

---

## 1. Setup and Imports
The notebook begins by importing the required libraries:

- **PIL** – image loading and manipulation
- **OpenCV** and **NumPy** – image processing
- **Matplotlib** – visualization
- **PaddleOCR** – OCR engine

An API key is also loaded to enable agent-based interaction, similar to the previous lab.

---

## 2. PaddleOCR Pipeline
A `PaddleOCR` object is initialized with **English** as the language.

Internally, the pipeline uses two deep learning models:

- **Text Detection (_DET)** – identifies text regions and bounding boxes
- **Text Recognition (_REC)** – converts detected text into characters

These models are not invoked sequentially by the user. PaddleOCR manages preprocessing and model execution as a **single unified pipeline**.

---

## 3. Running OCR on a Receipt
A receipt image (from Lab 1) is processed using PaddleOCR.

- The OCR output is a **list**, where each element corresponds to a processed page.
- For a single-page image, the list contains **one dictionary**.

From the first result (`result[0]`), the following are extracted and printed:

- Recognized text
- Confidence scores
- Bounding box coordinates

Scrolling through the output reveals all text detected across the receipt.

---

## 4. Preprocessing and Visualization
Before displaying results, PaddleOCR automatically performs preprocessing such as:

- Rotation correction
- Deskewing
- Image unwarping

The displayed image is the **processed image**, with:

- Bounding boxes drawn around detected text
- Recognized text overlaid on the image

Compared to the original receipt:

- Some background is removed
- The image is slightly rotated for better horizontal alignment

---

## 5. Why Bounding Boxes Matter
Bounding boxes provide **localization information**, showing exactly where each text field appears in the document.

This improves accuracy and reliability. For example, a value that was previously misread (`795`) is now correctly detected.

---

## 6. Exposing PaddleOCR as an Agent Tool
PaddleOCR is wrapped inside a function and registered as a **tool** for an agent.

- The function returns OCR results
- It prints text, bounding boxes, and confidence scores
- The agent can call this tool as needed

The rest of the agent setup is the same as Lesson 1, using **gpt-5-mini** as the LLM.

---

## 7. Receipt Validation with an Agent
The agent is tasked with verifying the receipt total.

- **Turquoise output** represents PaddleOCR results
- **Green output** represents the LLM’s reasoning and response

Because OCR extraction is accurate, the LLM correctly performs the arithmetic and validates the total. This demonstrates how **OCR + LLM reasoning** can solve real‑world receipt validation tasks.

---

## 8. Reusable OCR Helper Function
A helper function called `run_ocr` is introduced.

This function:

- Executes PaddleOCR
- Prints recognized text, confidence scores, and bounding boxes
- Uses the processed image
- Draws annotations on top of the image

This function is reused throughout the rest of the lesson.

---

## 9. Table Example and OCR Limitations
OCR is applied to a table image.

The output includes:

- Recognized text
- Confidence scores
- Bounding boxes
- Overlaid visual annotations

An error is observed with **scientific notation**:

- For example, `10²⁰` is incorrectly recognized as `1020`

---

## 10. Agent Reasoning to Fix Scientific Notation
The agent is tasked with extracting FLOPs from the **EN-DE** column of the table.

Results:

- PaddleOCR output remains unchanged
- The LLM uses reasoning to correct scientific notation

Example:

- `1020` is interpreted as **10²⁰**, which makes sense for FLOPs

The agent also correctly identifies missing values (e.g., **ByteNet**, **Deep-Att**) as not found.

---

## 11. Handwritten Grammar Worksheet
OCR is applied to a handwritten grammar worksheet.

Observations:

- The student name is misrecognized
- Some answers contain underscores or character-level errors
- Handwriting recognition is still an improvement over Tesseract

---

## 12. Extracting Student Answers as JSON
The agent is instructed to:

- Extract student responses as **JSON**
- **Not correct grammatical errors**

Results:

- JSON structure is correct
- Original (incorrect) grammar is preserved
- This is critical for grammar evaluation tasks

---

## 13. Summary of Repeated Exercises
Three exercises from Lesson 1 are repeated:

1. Receipt
2. Table
3. Handwritten worksheet

Key improvements:

- Better detection and recognition
- Fewer character-level errors
- Bounding boxes improve document understanding

---

## 14. Exploring OCR Weaknesses

### Report with Table and Chart
A document (`report.png`) contains:

- A table at the top
- Paragraph text in the center
- A line chart at the bottom

Issues observed:

- Charts are not detected as a single unit
- Axis labels are extracted without context
- Chart semantics are ignored

### Multi‑Column Academic Article
An academic article image contains:

- Two-column abstract
- Three-column body text
- Embedded tables

Problem:

- OCR reads text left‑to‑right across columns
- Natural reading order is broken
- Output text becomes garbled

Conclusion: PaddleOCR struggles with **multi‑column layouts**.

---

## 15. Introducing Layout Detection
Layout-aware detection is required for real‑world documents.

PaddleOCR provides a **Layout Detection** model that:

- Identifies document regions
- Assigns semantic labels

A function called `process_document` is defined, returning:

- **Label** (e.g., text, table, chart)
- **Confidence score**
- **Bounding box**

---

## 16. Layout Detection Results

### Economic Report
Detected regions include:

- Text
- Paragraph titles
- Tables
- Charts
- Numbers
- Footers

The chart is now correctly identified as a single region.

### Academic Article
Detected labels include:

- doc_title
- abstract
- text
- paragraph_title
- table
- footnote
- footer

Although one table is split into two detections, reading order is preserved and text grouping is improved.

---

## 17. Bank Statement Example
A bank statement is processed using layout detection.

Observed issues:

- Entire content detected as one large table
- Table headers are not separated
- Small footer text is ignored

This highlights remaining layout and semantic limitations.

---

## Final Takeaways

- PaddleOCR is a strong **deep-learning-based OCR engine**
- It outperforms traditional OCR on many real‑world images
- It primarily operates at the **line level**
- Layout detection adds regional structure but lacks full semantic understanding
- Human-like document comprehension requires stronger vision-based models

---

## What’s Next
The next lesson focuses on:

- Layout detection
- Reading order
- More advanced document understanding techniques

