---
title: RAG Explained
writer: Bashar Mithan
date: 2026-07-25
---

**Retrieval-Augmented Generation (RAG)** is an architectural pattern that solves the biggest limitation of Large Language Models (LLMs): their inability to access real-time data or private information

By default, an LLM only knows what it was trained on. If you ask it about a proprietary system or recent events, it will either guess (hallucinate) or say it doesn't know. RAG fixes this by treating the LLM not as a static database of memorized facts, but as a **reasoning engine.**

Instead of asking the model to answer from memory, a RAG architecture intercepts the user's query, fetches relevant factual data from an external database, and hands both the question and the facts to the LLM to process.

## How the RAG Pipeline Works

Building a RAG system requires setting up a pipeline where the order of operations is critical:

1. **Data Ingestion - Preparing the knowledge base (offline)**: Your backend parses raw documents (like system specifications, logs, or text files), breaks them into manageable "chunks", and passes them through an embedding model. This model converts the text into **embeddings** (high-dimensional numerical vectors that capture the semantic meaning of the text) and stores them in a vector database.

2. **Retrieval - Finding relevant context (Runtime)**: When a query comes in, the backend instantly converts the user's text into own vector embedding. It then queries the vector database for the "nearest neighbors", the stored documents chunks that mathematically align closest with the intent of the query.

3. **Augmentation - Constructing the payload**: The backend takes the retrieved text chunks and injects them into a predefined prompt template. For example, instead of just sending the LLM the user's question, the backend script formats a payload that essentially says: "Using ONLY the following retrieved documents, answer the user's question."

4. **Generation - The final API call**: This data-rich, augmented prompt is sent to the LLM. The model reads the injected context, applies its natural language reasoning capabilities, and generates a precise, heavily grounded answer based on your specific database.

## Embedding

Embedding is a translation from human concept (words, sentences, or images) into a geometric space that computers can calculate. Instead of treating words as isolated, meaningless ID numbers, an embedding maps them as coordinates in a high-dimensional continuous space where physical distance represents semantic similarity.

![[Pasted image 20260725015713.jpg]]Words mapped as vectors in a 3D semantic space. Source: Around the word - Hypotheses.org

### The Concept of Dimensions

In the image above, the AI has learned to score concepts along three hypothetical axes (features): `wings`, `engine`, and `sky`. In a real embedding model, there aren't just 3 dimensions, there are typically hundreds or thousands (e.g. 384, 768, or dimensions). Furthermore, the axes aren't explicitly labeled by human. They are **latent variables** discovered by the neural network during training to represent complex linguistic patterns, context, syntax, and conceptual relationships.

## Why RAG is the Industry Standard

- **Bypasses Retraining:** Training or fine-tuning an LLM on new data is computationally expensive and slow. Updating a RAG system is as simple as inserting a new row into your database.

- **Verifiable Accuracy:** Because the LLM generates its response based on the text chunks provided in the augmentation step, you can program the backend to return citations, allowing users to trace exactly which document generated a specific claim.

- **Data Privacy:** The proprietary data stays securely in your own backend database. It is only retrieved and sent in the API payload when strictly necessary for a specific query.