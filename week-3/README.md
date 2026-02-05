# Multimodal Retrieval-Augmented Generation (RAG) — CS 5542 Lab 3

This project implements and evaluates a **multimodal Retrieval-Augmented Generation (RAG)** pipeline using academic research papers. The system retrieves evidence from **text (PDFs)** and **images (figures)** and produces **grounded, extractive answers** while explicitly avoiding hallucinations.

---

## System Overview

```
flowchart TD
    Q[User Query] --> R1[Text Retriever<br/>(TF-IDF / BM25)]
    Q --> R2[Image Retriever<br/>(Caption TF-IDF)]

    R1 --> F[Fusion<br/>(alpha-weighted)]
    R2 --> F

    F --> C[Evidence Context<br/>(Text + Images)]
    C --> G[Extractive Generator]
    G --> A[Grounded Answer]
```

---

## Dataset

**Documents**
- `rag_original.pdf` — Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks  
- `multimodal_rag_survey.pdf` — A Survey of Multimodal Retrieval-Augmented Generation  

**Images**
- Figures extracted from PDFs (architecture diagrams, result tables)

**Queries**
- Conceptual and evidence-based questions with keyword rubrics  
- Includes a query with **no supporting evidence** to test faithfulness

---

## Methodology

### 1. Ingestion
- Extract page-level text chunks
- Create fixed-size overlapping chunks
- Load images with filename-based captions

### 2. Retrieval
- Text: TF-IDF and BM25
- Images: TF-IDF over captions

### 3. Fusion
- Normalize modality scores
- Fuse using alpha-weighted scoring

### 4. Generation
- Extractive, evidence-only answers
- Abstains when no evidence exists

---

## Evaluation Metrics

- Precision@5  
- Recall@10  
- Faithfulness (abstention when evidence is missing)

---

## Core Results — Chunking × Retriever Ablation

| Query | Method | Chunking | Precision@5 | Recall@10 |
|------|--------|----------|-------------|-----------|
| Q1 | TF-IDF | Page | 1.00 | 0.244 |
| Q1 | TF-IDF | Fixed | 1.00 | 0.152 |
| Q1 | BM25 | Page | 0.80 | 0.195 |
| Q1 | BM25 | Fixed | 0.60 | 0.121 |
| Q2 | TF-IDF | Page | 1.00 | 0.308 |
| Q2 | TF-IDF | Fixed | 1.00 | 0.227 |
| Q2 | BM25 | Page | 1.00 | 0.308 |
| Q2 | BM25 | Fixed | 1.00 | 0.205 |
| Q3 | All | All | 0.00 | 0.000 |

---

## Ablation — Effect of TOP_K_TEXT

| Query | TOP_K_TEXT | Precision@5 | Recall@10 |
|------|------------|-------------|-----------|
| Q1 | 2 | 1.00 | 0.244 |
| Q1 | 5 | 1.00 | 0.244 |
| Q1 | 10 | 1.00 | 0.244 |
| Q2 | 2 | 1.00 | 0.308 |
| Q2 | 5 | 1.00 | 0.308 |
| Q2 | 10 | 1.00 | 0.308 |
| Q3 | All | 0.00 | 0.000 |


---

## Discussion

### **Retrieval Evaluation**
We evaluate retrieval using Precision@5 and Recall@10. Page-based chunking consistently outperforms fixed-size chunking across both TF-IDF and BM25, suggesting that preserving document structure improves retrieval quality for academic PDFs.

### **Ablation Study**
We conduct ablations over chunking strategy (page vs fixed), retrieval method (TF-IDF vs BM25), and retrieval depth (TOP_K_TEXT ∈ {2,5,10}). Results show that page-based chunking yields higher recall, while increasing TOP_K_TEXT provides no additional benefit in this dataset.

### **Faithfulness**
For queries without supporting evidence (Q3), the system abstains from answering. This demonstrates strong faithfulness by avoiding hallucinated responses.
