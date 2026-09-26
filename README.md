# AI Interview RAG

An AI-powered technical interview system that uses a candidate's resume to create a personalized and adaptive technical interview.

The current project combines **Resume Analysis, Retrieval-Augmented Generation (RAG), Vector Search, AI-based Answer Analysis, and Adaptive Interview Question Generation** to simulate a personalized technical interview experience.

---

## Overview

The application takes a candidate's resume as input and uses the resume throughout the interview.

The current workflow is:

```text
Resume PDF
    ↓
PDF Text Extraction
    ↓
Resume Validation
    ↓
Candidate Profile Extraction
    ↓
Resume Chunking
    ↓
OpenAI Embeddings
    ↓
Qdrant Vector Database
    ↓
Target Role + Interview Setup
    ↓
Adaptive Chat Interview
    ↓
Resume Context Retrieval
    ↓
Answer Relevance Analysis
    ↓
Adaptive Follow-up / Clarification
    ↓
Topic Progression
    ↓
Time-Aware Closing
    ↓
Final Performance Report
```

The goal is to make the interview personalized and adaptive rather than a fixed list of questions.

---

# Features

## Resume Processing

- Resume PDF upload
- PDF text extraction
- Resume/CV document validation
- Confidence-based resume validation
- AI-powered candidate profile extraction
- Unique `resume_id` generation
- Resume chunking
- OpenAI embeddings
- Qdrant vector storage
- Resume-specific semantic retrieval

## Interview

- Target-role based interview setup
- Personalized opening question
- Role-specific assessment focus
- Resume-aware interview questions
- Adaptive technical questioning
- Interview state management
- Topic tracking
- Covered-topic tracking
- Topic depth tracking
- Topic progression to avoid staying on one topic
- Evidence-driven follow-up questions
- Core fundamentals assessment
- Technical stack assessment
- Project experience assessment
- Problem-solving assessment
- Role-specific assessment
- System-design assessment

## Answer Handling

- AI-based answer relevance analysis
- Detection of answered / partially answered / not answered responses
- One clarification attempt for weak or incomplete answers
- Insufficient-evidence tracking after clarification
- Redirection when the candidate goes off-topic
- Adaptive questioning based on previous answers and resume context

## Time-Aware Interview

- Configurable interview durations:
  - 2 minutes
  - 5 minutes
  - 10 minutes
  - 20 minutes
- Backend-controlled remaining time
- Time-aware question generation
- Shorter questions when time is limited
- Closing phase when approximately 30 seconds remain
- Explicit AI closing message
- Interview completion after the closing answer

## Evaluation

- Technical correctness
- Technical depth
- Communication clarity
- Strengths
- Areas for improvement
- Overall performance report
- Questions answered

## Frontend

- Browser-based frontend
- Vanilla HTML/CSS/JavaScript
- Interview chat interface
- Timer
- Light/Dark mode
- Adaptive chat UI
- Completion screen
- Final report view

---

# Tech Stack

## Backend

- **Python**
- **FastAPI**
- **Pydantic**
- **Uvicorn**
- **PyMuPDF**
- **OpenAI API**
- **Qdrant Cloud**
- **python-dotenv**
- **python-multipart**

## Frontend

- **HTML5**
- **CSS3**
- **Vanilla JavaScript**

No React, Node.js, or frontend build system is required for the current frontend.

## AI / RAG

- OpenAI Responses API
- OpenAI Embeddings
- Qdrant Vector Database
- Retrieval-Augmented Generation (RAG)
- AI-based answer relevance analysis
- Adaptive question generation

---

# Project Structure

```text
ai-interview-rag/
│
├── backend/
│   ├── app/
│   │   ├── analyzer/
│   │   │   ├── __init__.py
│   │   │   ├── schemas.py
│   │   │   └── service.py
│   │   │
│   │   ├── interview/
│   │   │   ├── __init__.py
│   │   │   ├── analysis/
│   │   │   │   ├── __init__.py
│   │   │   │   ├── schemas.py
│   │   │   │   └── service.py
│   │   │   │
│   │   │   ├── chat/
│   │   │   │   ├── __init__.py
│   │   │   │   ├── analysis/
│   │   │   │   │   ├── __init__.py
│   │   │   │   │   ├── schemas.py
│   │   │   │   │   └── service.py
│   │   │   │   ├── router.py
│   │   │   │   ├── schemas.py
│   │   │   │   ├── service.py
│   │   │   │   └── state.py
│   │   │   │
│   │   │   ├── interviewer.py
│   │   │   ├── planner.py
│   │   │   ├── router.py
│   │   │   ├── schemas.py
│   │   │   └── state.py
│   │   │
│   │   ├── rag/
│   │   │   ├── __init__.py
│   │   │   ├── chunker.py
│   │   │   ├── embeddings.py
│   │   │   ├── qdrant.py
│   │   │   ├── router.py
│   │   │   └── service.py
│   │   │
│   │   ├── resume/
│   │   │   ├── __init__.py
│   │   │   ├── router.py
│   │   │   ├── service.py
│   │   │   └── validator.py
│   │   │
│   │   ├── __init__.py
│   │   └── main.py
│   │
│   ├── .env.example
│   └── requirements.txt
│
├── frontend/
│   ├── index.html
│   ├── script.js
│   ├── style.css
│   │
│   ├── chat/
│   │   ├── index.html
│   │   ├── script.js
│   │   ├── style.css
│   │   ├── chat.html
│   │   ├── chat.css
│   │   ├── chat.js
│   │   ├── result.html
│   │   ├── result.css
│   │   └── result.js
│   │
│   └── interview/
│       ├── index.html
│       ├── script.js
│       └── style.css
│
├── .gitignore
└── README.md
```

> `backend/.env` is local-only and must not be committed. The repository should contain `backend/.env.example` instead.

---

# How the System Works

## 1. Resume Upload

The candidate uploads a resume in PDF format.

The backend:

1. Checks that the uploaded file is a PDF.
2. Extracts text from the PDF.
3. Validates that the document is a resume/CV.
4. Rejects documents with insufficient validation confidence.
5. Generates a structured candidate profile.
6. Creates a unique `resume_id`.
7. Ingests the resume into the RAG pipeline.

The candidate profile can contain information such as:

- Name
- Target roles
- Skills
- Experience
- Projects

---

## 2. Resume Processing for RAG

The extracted resume text is divided into smaller chunks.

Each chunk is converted into an embedding using the configured OpenAI embedding model.

The embeddings and resume-specific metadata are stored in Qdrant.

```text
Resume Text
    ↓
Chunking
    ↓
Text Chunks
    ↓
OpenAI Embeddings
    ↓
Qdrant
```

Each resume is associated with a unique `resume_id`, allowing retrieval to remain specific to the candidate.

---

## 3. Interview Setup

The candidate provides:

- Resume
- Target role
- Interview duration

The chat interview supports:

```text
2 minutes
5 minutes
10 minutes
20 minutes
```

The interviewer begins with a role-specific opening question.

For example, for an AI Engineer role, the opening asks about experience and projects relevant to AI engineering.

---

## 4. Adaptive Interview State

The chat interview maintains its own state.

The state tracks information including:

- Resume ID
- Target role
- Interview duration
- Start time
- Conversation messages
- Current topic
- Covered topics
- Current topic depth
- Clarification status
- Topics with insufficient evidence
- Closing state
- Completion status
- Final evaluation

This state allows the interviewer to make decisions based on the candidate's previous answers.

---

## 5. Adaptive Interviewing

When the candidate submits an answer, the system follows an adaptive process:

```text
Candidate Answer
       ↓
Answer Relevance Analysis
       ↓
Clarification Decision
       ↓
Resume Context Retrieval
       ↓
Topic / Evidence Update
       ↓
Time Check
       ↓
Adaptive Next Question
```

The interviewer can use:

- Target role
- Role-specific assessment focus
- Resume context
- Current topic
- Topics already covered
- Current topic depth
- Candidate's latest answer
- Recent conversation history
- Insufficient-evidence topics
- Remaining interview time

This allows the interviewer to ask follow-ups based on what the candidate actually said.

---

## 6. Assessment Areas

The adaptive interviewer can collect evidence across six main areas:

### Project Experience

Projects, responsibilities, implementation details, technical decisions, and outcomes.

### Technical Stack

Languages, frameworks, libraries, databases, APIs, tools, and technologies.

### Core Fundamentals

Relevant computer science, engineering, AI/ML, database, networking, or other foundational concepts.

### Problem Solving

Debugging, reasoning, troubleshooting, tradeoffs, and practical technical scenarios.

### Role Specific

Skills and knowledge particularly important for the selected target role.

### System Design

Architecture, scalability, reliability, component design, and technical tradeoffs.

The interviewer is designed to move between these areas instead of repeatedly staying on a single topic.

---

## 7. Answer Relevance and Clarification

Candidate answers are analyzed for relevance to the current interview question.

The system distinguishes between:

```text
answered
partially_answered
not_answered
```

If an answer is partially answered or not answered, the interviewer can make **one clarification attempt**.

```text
Question
   ↓
Weak / incomplete answer
   ↓
One clarification question
   ↓
Candidate response
   ↓
Enough evidence? ── Yes ──→ Continue
       │
       No
       ↓
Mark topic as insufficient evidence
       ↓
Move to another relevant area
```

The insufficient-evidence state does not mean that the candidate is weak. It means the interviewer was not able to collect enough evidence for that topic.

---

## 8. Topic Progression

The interviewer tracks:

- Current topic
- Topics already covered
- Number of questions asked in the current topic

This helps prevent the interview from becoming stuck on one subject.

When a topic has been sufficiently explored, the interviewer can transition to another relevant assessment area.

---

## 9. Time-Aware Interviewing

The interviewer changes its questioning strategy based on remaining time.

### More than 120 seconds

Deeper follow-ups are allowed when they provide meaningful evidence.

### 61–120 seconds

Questions become more focused and unnecessary deep exploration is avoided.

### 31–60 seconds

The interviewer prefers concise, high-value questions.

### 30 seconds or less

The interviewer moves toward the closing phase instead of beginning another deep technical discussion.

The backend remains authoritative for the interview timer.

---

## 10. Interview Closing

When very little time remains, the interviewer asks one concise closing question.

After the candidate answers the closing question, the AI sends a final message such as:

```text
Thank you for your time. That concludes the interview.
Your responses have been recorded, and the interview is now complete.
```

The interview is then marked as completed.

The frontend displays the final AI message before showing the completed-interview state.

---

## 11. Final Performance Report

After the interview is completed, the evaluation system can generate a final performance report.

The report includes:

- Overall score
- Technical correctness
- Technical depth
- Communication clarity
- Strengths
- Areas for improvement
- Questions answered

---

# API Endpoints

The current FastAPI backend exposes the following main endpoints.

## General

| Method | Endpoint | Purpose |
|---|---|---|
| GET | `/` | API status |
| GET | `/health` | Health check |

## Resume

| Method | Endpoint | Purpose |
|---|---|---|
| POST | `/resume/upload` | Upload, validate, analyze, embed, and store a resume |

## RAG

| Method | Endpoint | Purpose |
|---|---|---|
| POST | `/rag/retrieve` | Retrieve resume context for a query |

## Interview Planning / Legacy Interview Flow

| Method | Endpoint | Purpose |
|---|---|---|
| POST | `/interview/plan` | Generate an interview plan |
| POST | `/interview/start` | Start the plan-based interview |
| POST | `/interview/answer` | Submit an answer and generate the next question |
| POST | `/interview/analyze-answer` | Analyze an individual answer |

## Adaptive Chat Interview

| Method | Endpoint | Purpose |
|---|---|---|
| POST | `/interview/chat/start` | Start an adaptive chat interview |
| POST | `/interview/chat/message` | Submit a candidate answer and receive the next interviewer state |
| GET | `/interview/chat/{session_id}/status` | Get interview time/completion status |
| POST | `/interview/chat/evaluate` | Generate the final chat-interview evaluation |
| GET | `/interview/chat/{session_id}/result` | Retrieve the completed interview result |

---

# Installation and Setup

## Prerequisites

Install:

- Python 3.10+
- Git
- An OpenAI API key
- A Qdrant Cloud account

The current frontend uses plain HTML, CSS, and JavaScript, so Node.js is not required.

---

# 1. Clone the Repository

```bash
git clone <YOUR_GITHUB_REPOSITORY_URL>
cd ai-interview-rag
```

---

# 2. Create the Python Virtual Environment

The virtual environment is intentionally **not included in the repository**.

Go into the backend:

```bash
cd backend
```

Create the environment:

```bash
python -m venv venv
```

Activate it in Git Bash on Windows:

```bash
source venv/Scripts/activate
```

After activation, the terminal should show:

```text
(venv)
```

---

# 3. Install Dependencies

With the virtual environment activated:

```bash
pip install -r requirements.txt
```

---

# 4. Configure Environment Variables

The real `.env` file must remain local because it contains API credentials.

The repository should contain:

```text
backend/.env.example
```

Create your local `.env` from the example:

```bash
cd backend
cp .env.example .env
```

Then edit:

```text
backend/.env
```

---

# 5. Configure OpenAI

Add your OpenAI API key:

```env
OPENAI_API_KEY=your_openai_api_key
```

Configure the OpenAI model used by the application:

```env
OPENAI_MODEL=your_openai_model
```

Configure the embedding model:

```env
OPENAI_EMBEDDING_MODEL=text-embedding-3-small
```

OpenAI is used for tasks including:

- Candidate profile extraction
- Interview planning
- Interview question generation
- Answer relevance analysis
- Answer evaluation
- Resume embeddings

Never commit the real API key to GitHub.

---

# 6. Configure Qdrant

The project uses **Qdrant Cloud** for vector storage and semantic retrieval.

Create a Qdrant Cloud project and obtain:

- Qdrant URL
- Qdrant API key

Add them to:

```env
QDRANT_URL=your_qdrant_url
QDRANT_API_KEY=your_qdrant_api_key
```

The application uses resume-specific metadata so that retrieval can be restricted to the candidate's resume.

---

# 7. Start the Backend

Make sure you are inside:

```text
backend/
```

and that the virtual environment is activated.

Run:

```bash
uvicorn app.main:app --reload
```

The backend runs at:

```text
http://127.0.0.1:8000
```

Check the API:

```text
http://127.0.0.1:8000
```

Expected response:

```json
{
  "message": "AI Interview RAG API is running"
}
```

---

# 8. API Documentation

FastAPI provides interactive documentation at:

```text
http://127.0.0.1:8000/docs
```

You can use the documentation to inspect and test the backend endpoints.

---

# 9. Run the Frontend

The frontend is a static HTML/CSS/JavaScript application.

A simple option is the **Live Server** extension in VS Code.

Open the project in VS Code and serve the `frontend` directory.

The exact local port depends on your Live Server configuration. A common URL is:

```text
http://127.0.0.1:5500/
```

The adaptive chat page is located at:

```text
http://127.0.0.1:5500/chat/
```

Make sure the backend is running before using the frontend.

---

# Running the Application

## Terminal 1 — Backend

```bash
cd backend
source venv/Scripts/activate
uvicorn app.main:app --reload
```

## Browser — Frontend

Open the frontend through your local static server.

Typical workflow:

```text
1. Open the application
2. Upload a resume PDF
3. Review the extracted candidate profile
4. Select / enter the target role
5. Start the interview
6. Answer technical questions
7. Receive adaptive follow-up questions
8. Continue through different assessment areas
9. Complete the interview
10. View the final performance report
```

---

# Environment Variables

The repository should contain a safe template:

```env
QDRANT_URL=
QDRANT_API_KEY=

OPENAI_API_KEY=
OPENAI_MODEL=
OPENAI_EMBEDDING_MODEL=text-embedding-3-small
```

The real credentials belong only in:

```text
backend/.env
```

The following must not be committed:

```text
.env
.env.*
venv/
.venv/
__pycache__/
*.pyc
```

The example file is safe to commit:

```text
backend/.env.example
```

---

# Security

Never commit the following to GitHub:

- OpenAI API keys
- Qdrant API keys
- `.env` files containing credentials
- Virtual environments
- Python cache files
- Logs containing secrets
- Other private credentials

Before pushing the project, verify what Git will commit:

```bash
git status
```

Then inspect the staged files:

```bash
git diff --cached --name-only
```

Make sure `backend/.env` is not included.

---

# Current Project Scope

The current version includes:

- Resume processing
- Resume validation
- Candidate profile extraction
- Resume chunking
- OpenAI embeddings
- Qdrant vector search
- Resume-aware RAG retrieval
- Interview planning
- Adaptive chat interviewing
- Role-specific assessment focus
- Topic tracking and progression
- Answer relevance analysis
- Clarification handling
- Insufficient-evidence handling
- Time-aware questioning
- Interview closing
- Final answer evaluation
- Final performance reporting
- Browser-based frontend
- Light/Dark mode

## Not Included in the Current Version

The following are not part of the current implementation:

- User authentication
- Persistent application database
- Voice-based interviewing
- Production deployment configuration

These can be added as future extensions.

---

# Future Improvements

Possible future improvements include:

- User authentication
- Persistent interview history
- Database-backed candidate profiles
- Voice-based interviews
- Interview history and analytics
- More advanced adaptive questioning
- Additional interview types
- Production deployment
- More detailed interview analytics

---

# License

This project is currently provided for educational and development purposes.
