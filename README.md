# AI Resume & Career Advisor 🎯
> **Retrieval-Augmented Generation (RAG) & Multi-Agent Career Coaching Platform**

[![Python 3.10+](https://img.shields.io/badge/python-3.10+-blue.svg)](https://www.python.org/)
[![Streamlit](https://img.shields.io/badge/UI-Streamlit-FF4B4B.svg)](https://streamlit.io/)
[![Google Gemini](https://img.shields.io/badge/LLM-Gemini%202.5%20Flash-4285F4.svg)](https://ai.google.dev/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

---

## 📌 Project Overview
Students and early-career software engineers frequently struggle to tailor their resumes for specific job descriptions, resulting in automated rejection by Applicant Tracking Systems (ATS).

The **AI Resume & Career Advisor** is an end-to-end intelligent platform that bridges this gap using:
1. **RAG Pipeline**: Parses unstructured PDF resumes and Job Descriptions, chunks content using a sliding window, computes TF-IDF vector embeddings, and retrieves the most critical job requirements via Cosine Similarity.
2. **Multi-Agent Orchestration**:
   - **Agent 1: Resume Reviewer**: Evaluates ATS compatibility across 5 distinct axes (0–100 score) and provides line-by-line STAR-format bullet rewrites.
   - **Agent 2: Career Advisor**: Categorizes missing skills into an actionable matrix, prepares a customized interview roadmap with technical and behavioral questions, and generates a **12-week (3-month) personalized learning plan** (Stretch Goal).
3. **Dual Execution Engine**:
   - **Zero-Setup Offline Deterministic Engine**: Run the complete app and all agent evaluations instantly without requiring an API key.
   - **Live Cloud LLMs**: Seamlessly connect Google Gemini 2.5 Flash / 1.5 Flash, Groq, or OpenAI via the UI sidebar.

---

## 🏗️ System Architecture

```
User (Uploads PDF Resume & JD)
      ↓
RAG Engine (PDF Parser → Sliding Chunking → Vector Store → Cosine Similarity Search)
      ↓
Multi-Agent Coordinator (Reviewer Agent + Career Advisor Agent)
      ↓
LLM Inference Engine (Gemini 2.5 Flash / OpenAI / Local Knowledge Engine)
      ↓
Structured Response (ATS Score • Missing Skills Matrix • Interview Roadmap • 3-Month Plan)
```

For detailed sequence diagrams, component breakdowns, and data flows, refer to [ARCHITECTURE_DIAGRAM.md](ARCHITECTURE_DIAGRAM.md).

---

## 🚀 Quickstart Guide

### 1. Clone & Navigate to Workspace
```bash
git clone <repo-url>
cd "LLM Training - AI Resume & Career Advisor"
```

### 2. Install Dependencies
```bash
pip install -r requirements.txt
```

### 3. Launch the Web Application
```bash
streamlit run app.py
```
Open your browser at `http://localhost:8501`.

---

## 🧪 Testing with Preloaded Scenarios
The application includes realistic sample resumes and job descriptions ready for one-click testing:
1. In the sidebar, under **Preloaded Test Scenarios**, select:
   - **Case 1: Full-Stack SWE (Alex Chen)**
   - **Case 2: AI / Data Scientist (Priya Sharma)**
2. Click **"🚀 Run Full AI Career Analysis"**.
3. Explore the interactive tabs:
   - 📊 **ATS Score & Overview**: Overall score (0-100), metric breakdowns, strengths, and shortcomings.
   - 🔍 **RAG & Skill Gap**: Matched vs. missing skills, top retrieved JD chunks.
   - ✍️ **Resume Reviewer Agent**: Original bullets vs. recruiter critique vs. optimized STAR rewrites.
   - 🎯 **Career Advisor & Roadmap**: Categorized gap matrix, domain review topics, and interview questions.
   - 📅 **3-Month Learning Plan**: Week-by-week curriculum with projects, free resources, and milestones.
   - 📄 **Export Report**: Download the full advisory report as Markdown or print to PDF.

---

## 🔬 Running Automated Unit Tests
To verify PDF extraction, RAG chunking, vector retrieval, skill gap analysis, and agent output generation:
```bash
python test_system.py
```

Expected output:
```
[1/5] Testing PDF Extraction...
 -> Successfully extracted 1915 chars from Resume and 2033 chars from JD.
[2/5] Testing RAG Ingestion & Vector Retrieval...
 -> Indexed 13 chunks. Top retrieved chunk similarity: 0.297
[3/5] Testing Skill Gap Extractor...
 -> Matched Skills: 12 skills
 -> Missing Skills: 13 skills
[4/5] Testing LLM Client & Resume Reviewer Agent...
 -> Overall ATS Score: 78/100
[5/5] Testing Career Advisor Agent (Interview Roadmap & 3-Month Plan)...
 -> Career Readiness: Intermediate - Job Ready with Minor Upskilling
 -> Learning Plan Duration: 12 Weeks (3 Months) across 3 phases.

 All End-to-End System Tests Passed Successfully!
```

---

## 📑 Project Report & Documentation
- 📘 [PROJECT_REPORT.md](PROJECT_REPORT.md): Comprehensive 3–5 page technical and academic report covering:
  - **1. Problem Statement**
  - **2. System Architecture**
  - **3. Prompt Design**
  - **4. Evaluation & Ablation Results**
  - **5. Challenges & Mitigations**
  - **6. Future Enhancements**
- 📐 [ARCHITECTURE_DIAGRAM.md](ARCHITECTURE_DIAGRAM.md): Mermaid flowcharts, sequence diagrams, and component matrices.
- 🧪 [test_system.py](test_system.py): Automated end-to-end verification script.

---

## 📂 Repository File Tree
```
├── app.py                          # Streamlit web dashboard
├── rag_engine.py                   # PDF parser, chunker, vectorizer & skill lexicon
├── llm_client.py                   # Unified Gemini / OpenAI / Offline LLM adapter
├── agents/
│   ├── __init__.py
│   ├── reviewer_agent.py           # Resume Reviewer Agent (ATS scoring, STAR rewrites)
│   └── career_advisor.py           # Career Advisor Agent (Skill gaps, interview prep, 3-month plan)
├── sample_data/
│   ├── sample_resume_swe.txt       # Candidate resume (Software Engineer)
│   ├── sample_resume_swe.pdf       # Generated PDF resume
│   ├── sample_jd_swe.txt           # Job description (CloudScale Systems)
│   ├── sample_jd_swe.pdf           # Generated PDF JD
│   ├── sample_resume_ds.txt        # Candidate resume (Data Science / AI)
│   ├── sample_resume_ds.pdf        # Generated PDF resume
│   ├── sample_jd_ds.txt            # Job description (NexusAI)
│   ├── sample_jd_ds.pdf            # Generated PDF JD
│   └── generate_sample_pdfs.py     # Script to generate sample PDFs
├── test_system.py                  # Automated test verification suite
├── requirements.txt                # Python package dependencies
├── ARCHITECTURE_DIAGRAM.md         # Full architecture diagrams & design doc
├── PROJECT_REPORT.md               # 3-5 page comprehensive project report
└── README.md                       # Documentation & quickstart guide
```
