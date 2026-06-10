# RAG, Explained — An Interactive Field Guide

Welcome to the **RAG, Explained** repository! This project is an interactive, bilingual (English & Ukrainian) field guide that walks you through building and optimizing a Retrieval-Augmented Generation (RAG) pipeline, grounded in a real-world case study.

## 📖 Project Overview

The guide and its associated lessons are based on a real Kaggle-style QA task from the **UA Agent Builder Lab** challenge:
* **Corpus**: 3,851 articles from [DOU.ua](https://dou.ua) (tech forum/news site).
* **Chunks**: 37,611 chunks (averaging 1,366 characters each) with a bilingual mix (~50% Ukrainian, ~50% Russian).
* **Validation**: Labeled set of 90 questions; test set of 364 questions.
* **Goal**: Retrieve up to 10 source URLs and extract single-token answers (`c1`, `c2`, `c3`) for sub-questions.
* **Score Climb**: The optimizations documented here pushed the system's private score from **0.232** (baseline local Gemma pipeline) to **0.520** (closing to within 0.006 of the leading rival).

The guide shows that **retrieval quality dominates RAG performance**, and that simple, data-driven decisions (like limiting the number of returned URLs to prevent a precision leak) often yield much larger gains than swapping in larger language models.

---

## 🗂️ Repository Structure

This is a clean, dependency-free, client-side frontend project. All pages run entirely in the browser and work offline.

* **[`index.html`](index.html)**: The main interactive field guide. It covers:
  * **RAG 101**: Sparse vs. Dense retrieval.
  * **RRF & Weights**: How Reciprocal Rank Fusion works and why a weight sweep is necessary.
  * **The Precision Leak**: An interactive chart showing how returning extra URLs tanks your F1 score.
  * **The Climb**: A step-by-step walk through seven submissions, explaining the single change that drove each score increase.
  * **Benchmarks & Dead Ends**: Direct comparisons of embedding models, rerankers, and failure modes.
* **[`0001-the-chunking-dilemma.html`](0001-the-chunking-dilemma.html) (Lesson 1)**: A detailed lesson exploring the trade-offs of chunk size (small vs. large) and text overlapping, and their direct impact on retrieval recall and precision.
* **[`0002-fuse-diversify-rerank.html`](0002-fuse-diversify-rerank.html) (Lesson 2)**: A deep dive into post-retrieval processing. Covers sparse/dense fusion, Reciprocal Rank Fusion (RRF), Maximal Marginal Relevance (MMR) for diversification, and LLM-based reranking.

---

## 💡 Key Engineering Lessons

### 1. Retrieval Quality is the Bottleneck
No amount of prompt engineering or model swapping will help if the correct source document isn't retrieved. Grounding your pipeline development in a measured validation set (e.g., using `recall@k` metrics) is critical.

### 2. The Precision Leak
When a dataset contains a median of 3 source URLs per question, submitting 10 URLs guarantees that 7 are incorrect. This drastically reduces precision and F1 score. Restricting your output to exactly match the target distribution (e.g., submitting exactly `k=3` URLs) can boost your score overnight.

### 3. Decoupling Context vs. Output
To maximize answer extraction accuracy, you can feed a larger pool of retrieved documents to your reader model (e.g., `recall@5` or `recall@10`) while strictly submitting only the top-3 URLs for the retrieval score. This decouples retrieval constraints from extraction constraints.

### 4. BM25 Failure Modes in Multilingual Corpora
Lexical search (BM25) fails on cross-lingual matches (e.g., matching a Ukrainian query to a Russian document) and morphological variations within the same language. Implementing learned sparse vectors (e.g., SPLADE or BGE-M3 sparse) or multi-lingual dense embeddings (e.g., `gemini-embedding-001`) helps bypass the morphology wall.

---

## 🚀 How to View and Run

Because all pages are built with Vanilla HTML, CSS, and JS:
1. **Direct View**: You can open [`index.html`](index.html) directly in any web browser.
2. **Local Server**: To serve locally with correct routing, run:
   ```bash
   python3 -m http.server 8000
   ```
   Then open `http://localhost:8000` in your browser.
3. **GitHub Pages**: When pushed to GitHub, this repository is fully compatible with GitHub Pages hosting for instant online access.
