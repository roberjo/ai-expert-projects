# 🧭 AI Expert Roadmap – Free Tools + Gemini

This document details each project in the 12-week curriculum, with recommended **tech stack**, **free tools**, and a **development roadmap**.

---

## **Phase 1 – Foundations (Weeks 1–3)**

### **Project 1: Document Summarizer → Email Sender**
- **Goal:** Learn Gemini API basics and build a working pipeline.
- **Tech Stack:**
  - Python
  - Gemini Free Tier API
  - `smtplib` (Python standard library) for email
  - Gmail free SMTP (use app password)
- **Free Tools:** Gemini API, Gmail, Python
- **Roadmap:**
  1. Get Gemini API key from Google AI Studio.
  2. Write Python script: read `.txt` → send to Gemini → get summary.
  3. Use `smtplib` to send the summary as an email.
  4. Add CLI or minimal UI.

---

### **Project 2: Meeting Notes Summarizer**
- **Goal:** Learn to build a simple UI for LLM apps.
- **Tech Stack:**
  - Python
  - Streamlit (for UI)
  - Gemini Free Tier API
- **Free Tools:** Streamlit Cloud (free hosting)
- **Roadmap:**
  1. Build input box for transcript.
  2. Call Gemini API → structured summary + action items.
  3. Display formatted results.
  4. Deploy on Streamlit Cloud.

---

### **Project 3: Gemini vs OSS Comparison Tool**
- **Goal:** Compare Gemini output to open-source LLMs.
- **Tech Stack:**
  - Python
  - Gemini Free Tier API
  - Hugging Face `transformers`
  - Local inference (e.g., DistilGPT-2) or Hugging Face Spaces
- **Free Tools:** Hugging Face Hub (free inference), Gemini API
- **Roadmap:**
  1. Create UI to accept prompt.
  2. Call Gemini + Hugging Face model.
  3. Show side-by-side outputs.
  4. Optional: Add rating system.

---

## **Phase 2 – Agents & Multi-Step Logic (Weeks 4–6)**

### **Project 4: Customer Email Classifier → Auto-Responder**
- **Goal:** Build multi-step pipelines.
- **Tech Stack:**
  - Python
  - Gemini API
  - SQLite (lightweight DB)
- **Free Tools:** SQLite (built-in), Gemini free tier
- **Roadmap:**
  1. Input: raw email text.
  2. Gemini: classify into category.
  3. Gemini: generate draft response.
  4. Save response in SQLite.

---

### **Project 5: Job Description → Resume Matcher**
- **Goal:** Create decision tree logic with multiple Gemini calls.
- **Tech Stack:**
  - Python
  - Flask/FastAPI (simple API)
  - Gemini API
- **Free Tools:** Flask, Gemini API
- **Roadmap:**
  1. Upload job description.
  2. Gemini: extract skills.
  3. Upload resume.
  4. Gemini: calculate match percentage.
  5. Output JSON.

---

### **Project 6: Error Handling + Logging Layer**
- **Goal:** Learn production-quality practices.
- **Tech Stack:**
  - Python
  - Gemini API
  - Pydantic (schema validation)
  - Logging module
- **Free Tools:** Python libraries
- **Roadmap:**
  1. Add try/except blocks with retries.
  2. Validate Gemini output with JSON schema.
  3. Log errors to file or SQLite.

---

## **Phase 3 – Productionization (Weeks 7–9)**

### **Project 7: RAG Q&A Bot for PDFs**
- **Goal:** Add context-aware retrieval.
- **Tech Stack:**
  - Python
  - Gemini API
  - FAISS (vector database)
  - `PyPDF2` (for parsing PDFs)
- **Free Tools:** FAISS, Gemini API
- **Roadmap:**
  1. Parse PDF → extract text.
  2. Generate embeddings → store in FAISS.
  3. Query embeddings + Gemini to answer questions.

---

### **Project 8: Persistent Storage + Memory**
- **Goal:** Store usage data and chat history.
- **Tech Stack:**
  - Python
  - Gemini API
  - SQLite
- **Free Tools:** SQLite, Gemini API
- **Roadmap:**
  1. Extend RAG bot to log Q&A pairs.
  2. Store chat history per user.
  3. Add function: recall context from past chats.

---

### **Project 9: AI Tools Hub (Portfolio Dashboard)**
- **Goal:** Showcase multiple tools in one place.
- **Tech Stack:**
  - Python
  - Streamlit (UI)
  - Gemini API
- **Free Tools:** Streamlit Cloud (free hosting)
- **Roadmap:**
  1. Combine projects: summarizer, classifier, RAG bot.
  2. Build Streamlit tabs for each.
  3. Deploy portfolio site.

---

## **Phase 4 – Advanced Exploration (Weeks 10–12)**

### **Project 10: Local Open-Source LLM Comparison**
- **Goal:** Understand tradeoffs between OSS and Gemini.
- **Tech Stack:**
  - Python
  - Ollama (local LLM runner)
  - Hugging Face `transformers`
  - Gemini API
- **Free Tools:** Hugging Face Hub, Ollama, Gemini free tier
- **Roadmap:**
  1. Run lightweight OSS model locally.
  2. Compare against Gemini output.
  3. Build UI for comparison.

---

### **Project 11: Prompt Engineering + Mini Fine-Tuning**
- **Goal:** Explore structured prompting and fine-tuning basics.
- **Tech Stack:**
  - Python
  - Gemini API (prompt engineering)
  - Hugging Face `trl` for fine-tuning
- **Free Tools:** Hugging Face, Google Colab free tier (for GPU)
- **Roadmap:**
  1. Collect Q&A dataset.
  2. Prompt Gemini for consistency.
  3. Fine-tune small OSS model with `trl`.
  4. Compare tuned vs untuned.

---

### **Project 12: Capstone – Real-World AI App**
- **Goal:** Build and deploy a complete application.
- **Tech Stack:**
  - Python
  - Streamlit/FastAPI
  - Gemini API + optional OSS LLM
  - SQLite/FAISS
- **Free Tools:** Streamlit Cloud or Hugging Face Spaces (hosting)
- **Roadmap:**
  1. Pick real problem (study assistant, biz automation, doc analyzer).
  2. Build pipeline with Gemini + storage.
  3. Deploy publicly.
  4. Share as portfolio.

---

# ✅ Summary
- **Free LLM:** Gemini API Free Tier (60 RPM)
- **Free Hosting:** Streamlit Cloud, Hugging Face Spaces
- **Free Storage:** SQLite, FAISS
- **Free Development:** Python, Hugging Face, Ollama
- **Learning Flow:** Start → Agents → RAG → Production → Fine-tuning → Capstone

