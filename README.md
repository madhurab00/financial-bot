# financial-bot
Financial Document Intelligence using LLM Fine-Tuning and Hybrid RAG

## Phase 1: LLM Fine-Tuning (Domain Adaptation)

### Objective
Phase 1 focuses on adapting a base language model to a specific domain using supervised fine-tuning.  
The goal is **not to add new knowledge**, but to align the model’s behavior to:

- Answer questions using only the provided context
- Follow a structured instruction format
- Refuse unsafe or out-of-scope requests
- Reduce hallucinations in a regulated domain

---

### Dataset
- Domain-specific financial question–answer data
- Mapped into an **instruction / input / output** schema
- Cleaned to remove incomplete or invalid examples
- Designed to teach *how* the model should respond rather than *what* to memorize

---

### Preprocessing
- Fixed-length tokenization with overlapping windows
- Overlap used to preserve contextual continuity across long documents
- Tokenization performed before fine-tuning to ensure consistent sequence lengths

---

### Model and Training Strategy
- Base model: a pre-trained causal language model 
- Quantization applied to reduce memory usage and enable efficient training
- LoRA used to fine-tune a small subset of parameters while keeping the base model frozen
- Training focused on instruction-following and grounded answering behavior

---

### Safety and Guardrails
- System prompt enforces domain constraints and refusal behavior
- Model trained to avoid:
  - Investment advice
  - Speculative predictions
  - Hallucinated financial figures
  - Non-public or insider information
- Red-team prompts used to validate refusal and grounding behavior

---

### Outputs
- Fine-tuned model checkpoint
- Instruction-tuning dataset in JSONL format
- Training and golden test splits for evaluation

---

### Limitations
- Retrieval-Augmented Generation (RAG) is not implemented in this phase
- Model knowledge is limited to training data and prompt context
- Handling large or frequently changing document collections is deferred to the next phase

---

## Phase 2: Hybrid Retrieval-Augmented Generation (RAG)

### Objective
Phase 2 extends the fine-tuned model by integrating a **Hybrid Retrieval-Augmented Generation (RAG)** system.

Unlike Phase 1 (behavior alignment), this phase enables the system to:

- Answer questions using large external document collections
- Ground responses in retrieved financial documents
- Reduce hallucinations through evidence-based generation
- Scale to evolving or frequently updated datasets

The goal is to combine the strengths of:
- **Fine-tuned domain behavior (Intern model)**
- **Document-grounded retrieval (Librarian system)**

---

### Architecture Overview

The Hybrid RAG system consists of:

1. **Document Ingestion Pipeline**
   - Financial reports (PDFs, structured text, filings)
   - Cleaned and converted to structured text
   - Chunked using recursive or fixed-length strategies

2. **Embedding Layer**
   - Dense vector embeddings generated for each document chunk
   - Stored in a vector database
   - Enables semantic similarity search

3. **Hybrid Retrieval Strategy**
   - Dense semantic search (vector similarity)
   - Optional keyword-based filtering (BM25 or metadata filters)
   - Top-k relevant chunks selected per query

4. **Grounded Generation**
   - Retrieved context injected into prompt
   - Fine-tuned model generates answer strictly based on retrieved content
   - Enforced abstention if information is unavailable

---

### Why Hybrid RAG?

Pure fine-tuning has limitations:
- Cannot store large financial corpora
- Cannot update knowledge without retraining
- Prone to memorization risks

Hybrid RAG solves this by:
- Keeping knowledge external
- Supporting dynamic updates
- Improving factual reliability
- Enabling traceability via retrieved source chunks

---

### Chunking Strategy

Two chunking methods are evaluated:

- **Recursive Chunking**
  - Splits by logical boundaries (headings, paragraphs)
  - Preserves semantic meaning
  - Better for structured financial documents

- **Fixed-Length Chunking**
  - Splits by token count
  - Simpler but may break semantic structure

Chunk size and overlap are tuned to balance:
- Context completeness
- Retrieval precision
- Token efficiency

---

### Prompt Structure

The generation prompt enforces strict grounding:

- Retrieved context is clearly separated
- Model instructed to:
  - Use only provided context
  - Avoid speculation
  - Abstain when information is missing


---

### Evaluation Strategy

The Hybrid RAG system is evaluated against:

- Fine-tuned model (Intern) without retrieval
- Multiple retrieval strategies (e.g., recursive vs fixed chunking)

Metrics include:
- ROUGE-L (semantic similarity to gold answers)
- Abstention compliance
- Numeric faithfulness
- Latency per query
- Retrieval relevance audit

Category-based analysis:
- Hard factual questions
- Strategic reasoning questions
- Out-of-scope prompts

---

### Safety and Compliance

Hybrid RAG enhances safety by:

- Restricting answers to retrieved financial documents
- Reducing hallucinated financial figures
- Improving explainability through source inspection
- Enforcing refusal behavior for:
  - Investment advice
  - Speculative projections
  - Insider or non-public information

---

### Outputs

- Vector database containing embedded financial documents
- RAG inference pipeline
- Evaluation reports comparing Intern vs Librarian systems
- Retrieval audit logs

---

### Limitations

- Retrieval quality depends on chunking and embedding performance
- Latency increases compared to standalone fine-tuned model
- Complex queries may require multi-hop retrieval
- System performance depends on document coverage
