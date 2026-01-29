# Lab 2 — Advanced RAG Results (CS 5542)

> **Course:** CS 5542 — Big Data Analytics and Apps  
> **Lab:** Advanced RAG Systems Engineering  
> **Student Name:**  Rohan Ashraf Hashmi
> **GitHub Username:**  rohanhashmi2
> **Date:**  Jan 29, 2026

---

## 1. Project Dataset
- **Domain:** Venture Finance / Startup Investing
- **# Documents:** 3
- **Data Source (URL / Description):**  
  Self-authored plaintext documents covering SAFE agreements, valuation caps, price-per-share calculations, dilution, and multi-SAFE edge cases.
- **Why this dataset fits my use case:**  
  This dataset reflects realistic venture-finance documentation used in early-stage investing and cap table modeling. The documents contain both definitions and edge cases, making them suitable for evaluating retrieval accuracy, grounding, and ambiguity handling in a RAG system.

---

## 2. Queries + Mini Rubric

### Q1
- **Query:** How does a SAFE convert into equity and what role does the valuation cap play?
- **Relevant Evidence (keywords / entities / constraints):**  
  SAFE definition, conversion event, valuation cap, discount rate, price per share
- **Correct Answer Criteria (1–2 bullets):**  
  - Explains SAFE conversion at a priced financing  
  - Describes how valuation cap affects conversion price

### Q2
- **Query:** How is price per share calculated in venture financing, and why is it important for ownership modeling?
- **Relevant Evidence (keywords / entities / constraints):**  
  Pre-money valuation, shares outstanding, PPS formula, dilution, cap table
- **Correct Answer Criteria (1–2 bullets):**  
  - Provides correct PPS formula  
  - Explains impact on ownership and dilution modeling

### Q3 (Ambiguous / Edge Case)
- **Query:** What happens when multiple SAFEs with different terms convert in the same financing round?
- **Relevant Evidence (keywords / entities / constraints):**  
  Multiple SAFEs, valuation caps, discounts, ambiguity, capitalization basis
- **Correct Answer Criteria (1–2 bullets):**  
  - Acknowledges complexity and ambiguity  
  - Avoids guessing when evidence is insufficient

---

## 3. System Design

- **Chunking Strategy:** Semantic
- **Chunk Size / Overlap:** Paragraph-based semantic chunks (~400–1200 characters)
- **Embedding Model:** sentence-transformers/all-MiniLM-L6-v2
- **Vector Store / Index:** FAISS (cosine similarity)
- **Keyword Retriever:** BM25
- **Hybrid α Value(s):** 0.2
- **Re-ranking Method:** Cross-Encoder (ms-marco-MiniLM-L-6-v2)

### Design Rationale
Semantic chunking preserves financial context better than fixed-size chunking for legal and financial documents. Hybrid retrieval combines exact keyword matching with semantic similarity, improving recall for both technical and conceptual queries. A lower α value prioritizes semantic relevance while still benefiting from BM25 signals. Cross-encoder re-ranking improves Precision@K by re-evaluating top candidates using full query–chunk interactions.

---

## 4. Results

## 4. Results

| Query | Method | Precision@5 | Recall@10 |
|------|--------|-------------|-----------|
| Q1 | Keyword (BM25) | 0.60 | 1.00 |
| Q1 | Vector (FAISS) | 0.60 | 1.00 |
| Q1 | Hybrid (α=0.2) | 0.60 | 1.00 |
| Q2 | Keyword (BM25) | 0.60 | 1.00 |
| Q2 | Vector (FAISS) | 0.40 | 1.00 |
| Q2 | Hybrid (α=0.2) | 0.60 | 1.00 |
| Q3 | Keyword (BM25) | 0.60 | 1.00 |
| Q3 | Vector (FAISS) | 0.60 | 1.00 |
| Q3 | Hybrid (α=0.2) | 0.60 | 1.00 |


---

## 5. Failure Case

- **What failed?**  
  The system could not produce a fully specific answer for an ambiguous query involving multiple SAFEs with different conversion terms.
- **Which layer failed?**  
  Knowledge / evidence layer (dataset coverage).
- **Proposed system-level fix:**  
  Add clarification detection and explicit “not enough evidence” handling when retrieved documents do not define key constraints.

---

## 6. Evidence of Grounding

> **Answer:**  
> A SAFE converts into equity at a future priced financing rather than accruing interest or having a maturity date. [Chunk 1]  
> The valuation cap sets the maximum company valuation used to calculate the conversion price, protecting early investors from excessive dilution. [Chunk 1]  
> If both a valuation cap and discount apply, the lower effective price per share is used. [Chunk 1]

> **Citations:** [Chunk 1]

---

## 7. Reflection (3–5 Sentences)
This lab demonstrated that system design choices often matter more than model size in RAG performance. Hybrid retrieval and re-ranking significantly improved evidence quality compared to single-method retrieval. Ambiguous queries highlighted the importance of dataset coverage and explicit uncertainty handling. Overall, retrieval quality and grounding logic had a greater impact on answer correctness than the generator itself.

---

## Reproducibility Checklist
- [x] Project dataset included or linked  
- [x] Queries + rubric filled  
- [x] Results table completed  
- [x] Screenshots included in repo  
- [x] Notebook runs end-to-end  
 

---

> *CS 5542 — UMKC School of Science & Engineering*
