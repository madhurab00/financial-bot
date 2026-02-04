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
