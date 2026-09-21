# Architecture & System Design: AI Resume & Career Advisor

This document details the architectural layout, component interactions, and data flow of the **AI Resume & Career Advisor** platform.

---

## 1. High-Level Flow (Core Assignment Pipeline)

The system fulfills the foundational pipeline:
$$\text{User} \longrightarrow \text{RAG Engine} \longrightarrow \text{Multi-Agent LLM Orchestrator} \longrightarrow \text{Actionable Response}$$

```
                +---------------------------------------+
                |                 USER                  |
                |   Uploads Resume & Job Description    |
                +-------------------+-------------------+
                                    |
                                    v
                +---------------------------------------+
                |              RAG ENGINE               |
                |  • PDF Extraction & Preprocessing     |
                |  • Recursive Chunking & Overlap       |
                |  • TF-IDF / Vector Embedding Index    |
                |  • Cosine Similarity Retrieval        |
                |  • Skill Lexicon Gap Extraction       |
                +-------------------+-------------------+
                                    | Top-K Chunks & Extracted Gaps
                                    v
                +---------------------------------------+
                |          LLM INFERENCE ENGINE         |
                |  • Agent 1: Resume Reviewer           |
                |  • Agent 2: Career Advisor            |
                |  • Structured JSON Output Schemas     |
                +-------------------+-------------------+
                                    |
                                    v
                +---------------------------------------+
                |               RESPONSE                |
                |  • ATS Compatibility Score (0-100)    |
                |  • Line-by-Line STAR Rewrites         |
                |  • Missing Skills Matrix              |
                |  • Interview Preparation Roadmap      |
                |  • 3-Month Personalized Learning Plan |
                +---------------------------------------+
```

---

## 2. Detailed Multi-Agent & RAG Architecture Diagram

```mermaid
flowchart TD
    subgraph UI ["Layer 1: User Interface & Ingestion (Streamlit)"]
        U[User Interface] -->|Uploads PDF / Raw Text| Ingest[Document Ingestion Module]
        Preset[Preloaded Test Presets SWE / DS] --> Ingest
        APIConfig[LLM Config: Gemini / OpenAI / Offline] --> AgentCoord
    end

    subgraph RAG ["Layer 2: RAG Pipeline & Semantic Indexer"]
        Ingest -->|Binary Stream| PDFParse[PDF Parser: pypdf]
        PDFParse -->|Cleaned Text| TextCleaner[Whitespace & Normalizer]
        TextCleaner -->|Job Description| Chunker[Recursive Sliding Chunker]
        Chunker -->|350-char Chunks| VectorStore[In-Memory Vector Store & TF-IDF]
        TextCleaner -->|Resume Text| SkillExtractor[Domain Skill Lexicon Extractor]
        TextCleaner -->|Job Description| SkillExtractor
        SkillExtractor -->|Matched & Missing Sets| SkillGap[Skill Gap Matrix & Match Ratio]
        TextCleaner -->|Resume Query| Retriever[Cosine Similarity Retriever]
        VectorStore -->|Index Matrix| Retriever
        Retriever -->|Top-K Context Chunks| AgentCoord[Agent Coordinator]
        SkillGap --> AgentCoord
    end

    subgraph Agents ["Layer 3: Multi-Agent Intelligence Layer"]
        AgentCoord -->|Resume + Top-K JD + Gaps| ReviewerAgent[Agent 1: Resume Reviewer]
        ReviewerAgent -->|ATS Score & Executive Verdict| AdvisorAgent[Agent 2: Career Advisor]
        AgentCoord -->|Retrieved Requirements| AdvisorAgent
    end

    subgraph LLM ["Layer 4: LLM Inference & Generation"]
        ReviewerAgent -.->|Structured Prompt| LLMClient[Unified LLM Client]
        AdvisorAgent -.->|Structured Prompt| LLMClient
        LLMClient --> ModelChoice{Selected Engine}
        ModelChoice -->|Cloud API| Gemini[Google Gemini 2.5 Flash]
        ModelChoice -->|Cloud API| OpenAI[OpenAI / Groq LLaMA 3.3]
        ModelChoice -->|Zero Key / Local| Offline[Deterministic Knowledge Engine]
        Gemini --> ResponseParser[JSON Schema Validator]
        OpenAI --> ResponseParser
        Offline --> ResponseParser
    end

    subgraph OutputLayer ["Layer 5: Structured Outputs & Interactive Visualizations"]
        ResponseParser --> Out1[ATS Scorecard 0-100 & 5 Sub-metrics]
        ResponseParser --> Out2[STAR Bullet Point Rewrites]
        ResponseParser --> Out3[Categorized Missing Skills Matrix]
        ResponseParser --> Out4[Targeted Interview Prep Roadmap]
        ResponseParser --> Out5[Stretch Goal: 3-Month Week-by-Week Learning Plan]
        ResponseParser --> Out6[Exportable Markdown & PDF Report]
    end
```

---

## 3. Sequence Diagram (System Interaction Timeline)

```mermaid
sequenceDiagram
    autonumber
    actor User
    participant App as Streamlit Dashboard
    participant RAG as RAG Retrieval Engine
    participant Reviewer as Resume Reviewer Agent
    participant Advisor as Career Advisor Agent
    participant LLM as LLM Engine (Gemini / Offline)

    User->>App: Submits Resume & Job Description (PDF / Text)
    App->>RAG: Ingest and clean documents
    RAG->>RAG: Chunk Job Description (sliding window, overlap=60)
    RAG->>RAG: Vectorize chunks (TF-IDF / Dense embeddings)
    RAG->>RAG: Execute skill extraction against taxonomy (Tech & Soft)
    RAG->>RAG: Query vector index with resume to retrieve Top-K requirements
    RAG-->>App: Return Top-K chunks, matched skills, missing skills, match rate

    App->>Reviewer: invoke review_resume(resume, Top-K JD, skill_gap)
    Reviewer->>LLM: generate(REVIEWER_SYSTEM_PROMPT, user_prompt, json_mode=True)
    LLM-->>Reviewer: Return validated ATS Score JSON (0-100, bullet rewrites)
    Reviewer-->>App: ATS Score, sub-metrics, line-by-line critique

    App->>Advisor: invoke generate_interview_roadmap(resume, Top-K JD, skill_gap, verdict)
    Advisor->>LLM: generate(ADVISOR_SYSTEM_PROMPT, roadmap_prompt, json_mode=True)
    LLM-->>Advisor: Return Career Readiness & Interview Questions JSON
    Advisor-->>App: Prioritized gap matrix, domain topics, STAR interview Q&A

    App->>Advisor: invoke generate_learning_plan(target_role, missing_skills, verdict)
    Advisor->>LLM: generate(LEARNING_PLAN_SYSTEM_PROMPT, plan_prompt, json_mode=True)
    LLM-->>Advisor: Return 12-week curriculum JSON
    Advisor-->>App: Month-by-month roadmap, projects, resources, milestones

    App-->>User: Render interactive tabs, scorecards, visual badges, and export options
```

---

## 4. Component Responsibility Matrix

| Component | File Path | Core Technology | Primary Responsibility |
| :--- | :--- | :--- | :--- |
| **User Interface** | `app.py` | Streamlit, CSS | Multi-tab dashboard, file uploaders, metric cards, interactive preview, export button. |
| **PDF Extraction** | `rag_engine.py` | `pypdf.PdfReader` | Ingests binary PDF streams, extracts text across pages, strips artifacts and normalizes whitespace. |
| **Chunking & Indexing** | `rag_engine.py` | Python Regex, `scikit-learn` | Sentence-aware recursive chunker with 350-character window and 60-character sliding overlap. |
| **Vector Retrieval** | `rag_engine.py` | TF-IDF & Cosine Similarity | Vectorizes JD chunks, transforms resume into query vector, computes cosine similarity rankings. |
| **Skill Lexicon** | `rag_engine.py` | Regex Taxonomy Matching | Extracts 100+ technical and soft skills, computing exact set intersections and differences. |
| **Reviewer Agent** | `agents/reviewer_agent.py` | Prompt Engineering, JSON Schema | Calculates ATS score across 5 dimensions, identifies weak bullets, provides STAR rewrites. |
| **Career Advisor Agent** | `agents/career_advisor.py` | Multi-Turn Agent Prompts | Synthesizes readiness level, builds interview Q&A roadmap, and constructs 12-week learning plan. |
| **LLM Inference** | `llm_client.py` | `google-genai`, REST, Local Engine | Unified client abstracting Google Gemini, OpenAI, Groq, and a local deterministic engine. |

---

## 5. Failure Modes & Graceful Degradation

1. **Missing or Corrupted PDF**:
   - `extract_text_from_pdf` wraps parsing in a `try...except` block, returning a clean error message and allowing raw text fallback in the UI.
2. **Missing LLM API Key**:
   - System defaults seamlessly to the local deterministic knowledge engine, ensuring continuous testing and grading without crashes or external network latency.
3. **Mismatched or Empty Inputs**:
   - Validation triggers an immediate UI notification instructing the candidate to provide documents or pick a preloaded preset.
4. **LLM Output Formatting Drift**:
   - Agents enforce strict JSON parsing with markdown backtick cleanup (````json ... ````) and automatic fallback schema recovery.
