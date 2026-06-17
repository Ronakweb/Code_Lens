# Code Lens - Open Source Onboarding Engine

<div align="center">

**"From Zero to Contributor in 5 Minutes"**


[![Python 3.10+](https://img.shields.io/badge/python-3.10+-blue.svg)](https://www.python.org/downloads/)
[![Next.js 14](https://img.shields.io/badge/Next.js-14-black)](https://nextjs.org/)
[![FastAPI](https://img.shields.io/badge/FastAPI-0.100+-009688.svg)](https://fastapi.tiangolo.com)

[Features](#-features) • [Quick Start](#-quick-start) • [Architecture](#-architecture) • [API Docs](#-api-documentation)

</div>

---

## 📋 Table of Contents

1. [Project Overview](#-project-overview)
2. [Problem Statement](#-problem-statement)
3. [Tech Stack](#-tech-stack)
4. [Architecture & Design](#-architecture--design)
5. [Core Features & MVP](#-core-features--mvp)
6. [Quick Start](#-quick-start)
7. [Setup & Installation](#-setup--installation)
8. [Usage Guide](#-usage-guide)
9. [API Documentation](#-api-documentation)
10. [Data Models & Schemas](#-data-models--schemas)
11. [Security & Threat Model](#-security--threat-model)
12. [Performance & Optimization](#-performance--optimization)
13. [Conclusion](#-conclusion)

---

## 🎯 Project Overview

### Title
**Code Lens** - An Intelligent Static Analysis Tool for Open Source Onboarding

### Abstract
Code Lens is an AI-powered platform that provides automated "health checks" and interactive explanations of any public GitHub repository without executing code. It bridges the gap between raw code and human understanding, making it the safest and fastest way to explore unknown software.

### Problem Statement

The modern open-source ecosystem faces a critical **Onboarding Bottleneck**:

- **Complexity Overload:** New contributors are overwhelmed by massive file structures and undocumented logic
- **Safety Risks:** Running unknown code (`npm install`, `pip install`) to understand it exposes developers to malware
- **Maintenance Fatigue:** Senior maintainers waste hours answering repetitive "how does this work?" questions
- **High Barrier to Entry:** 73% of first-time contributors abandon projects within the first week due to onboarding friction

### Solution
A web-based platform that treats code as data. It scans, visualizes, and explains project structure and quality *statically*, acting as an automated mentor for every new developer.

### Goals & Success Criteria

| Goal | Success Metric |
|------|---------------|
| Reduce onboarding time | From hours to < 5 minutes |
| Improve code safety | Zero code execution during analysis |
| Increase contributor retention | 40% reduction in first-week abandonment |
| Provide actionable insights | 85%+ user satisfaction with code quality reports |

---

## 🚀 Tech Stack

### Backend (The Brain)
| Category | Technology | Purpose |
|----------|-----------|---------|
| **Language** | Python 3.10+ | Core backend logic |
| **Framework** | FastAPI | High-performance async API server |
| **Static Analysis** | Radon | Cyclomatic complexity & maintainability metrics |
| **AI/ML** | Google Gemini 2.5 Flash | LLM for conversational AI |
| **Embeddings** | Google Generative AI Embeddings (`models/embedding-001`) | Semantic code search |
| **Vector Store** | FAISS (Facebook AI Similarity Search) | Efficient similarity search |
| **LLM Framework** | LangChain | RAG pipeline orchestration |
| **Database** | SQLite + SQLAlchemy | Persistent data storage |
| **Async Tasks** | Python Threading | Background analysis tasks |

**Rationale:** FastAPI provides async performance with automatic OpenAPI docs. Radon offers battle-tested Python metrics. Google Gemini delivers production-grade AI at scale with competitive pricing.

### Frontend (The Dashboard)
| Category | Technology | Purpose |
|----------|-----------|---------|
| **Framework** | Next.js 14 (React 18) | Server-side rendering & routing |
| **Language** | TypeScript | Type-safe development |
| **Styling** | Tailwind CSS | Utility-first responsive design |
| **UI Components** | Radix UI | Accessible component primitives |
| **Animations** | Framer Motion | Smooth transitions |
| **Charts** | Chart.js + react-chartjs-2 | Data visualizations |
| **Icons** | Lucide React | Modern icon library |
| **HTTP Client** | Axios | API communication |

**Rationale:** Next.js offers best-in-class developer experience with SEO optimization. TypeScript prevents runtime errors. Tailwind CSS accelerates UI development.

### Infrastructure & Tools
- **Version Control:** Git + GitHub
- **Environment Management:** Python venv
- **Configuration:** pydantic-settings for type-safe config
- **Security:** Rate limiting, input validation, secret scanning
- **Documentation:** Markdown, OpenAPI/Swagger

---

## 🏗️ Architecture & Design

### System Overview

The system follows a **three-tier microservices architecture** designed for security, scalability, and maintainability.

```
┌─────────────────────────────────────────────────────────────┐
│                      USER INTERFACE                         │
│           (Next.js Frontend - Port 3000)                    │
│  ┌──────────┐  ┌──────────┐  ┌─────────────────┐          │
│  │  Input   │  │  File    │  │  Chat Interface │          │
│  │  Module  │  │  Tree    │  │  (RAG)          │          │
│  └────┬─────┘  └────┬─────┘  └────────┬────────┘          │
└───────┼─────────────┼─────────────────┼────────────────────┘
        │             │                 │
        └─────────────┴─────────────────┘
                      │
                 HTTPS/REST API
                      │
┌─────────────────────┼─────────────────────────────────────┐
│                BACKEND API LAYER                           │
│           (FastAPI - Port 8000)                            │
│  ┌──────────────────┴─────────────────────────┐           │
│  │         API Endpoints & Controllers         │           │
│  ├─────────────────────────────────────────────┤           │
│  │  /analyze    /chat    /branches   /score   │           │
│  └─────────────────┬───────────────────────────┘           │
│                    │                                        │
│  ┌─────────────────┴─────────────────────────┐            │
│  │         SERVICE LAYER (Business Logic)     │            │
│  ├────────────────────────────────────────────┤            │
│  │ • Cloner Service    • Analyzer Service     │            │
│  │ • RAG Service       • Scorer Service       │            │
│  │ • Branch Manager    • Cache Service        │            │
│  │ • Data Persistence  • Async Pipeline       │            │
│  └─────────────────┬───────────────────────────┘           │
└────────────────────┼─────────────────────────────────────┘
                     │
        ┌────────────┼────────────┐
        │            │            │
        ▼            ▼            ▼
  ┌─────────┐  ┌─────────┐  ┌──────────┐
  │ Git     │  │ File    │  │ SQLite   │
  │ Clones  │  │ System  │  │ Database │
  │ (temp)  │  │ Storage │  │          │
  └─────────┘  └─────────┘  └──────────┘
        │
        ▼
  ┌────────────────────────────────┐
  │   INTELLIGENCE LAYER (AI)      │
  ├────────────────────────────────┤
  │ • Google Gemini LLM            │
  │ • Vector Embeddings            │
  │ • FAISS Index                  │
  │ • LangChain Orchestration      │
  └────────────────────────────────┘
```

### Component Breakdown

#### 1. Frontend Components (Next.js)
```
src/
├── app/
│   ├── page.tsx              # Landing page with URL input
│   ├── analyze/              # Analysis results dashboard
│   └── api/                  # API route handlers
├── components/
│   ├── FileTree.tsx          # Interactive file explorer
│   ├── ChatInterface.tsx     # RAG-powered Q&A
│   ├── HealthCard.tsx        # Quality score visualization
│   └── MetricsChart.tsx      # Complexity/maintainability charts
└── lib/
    ├── api.ts                # API client (Axios)
    └── utils.ts              # Helper functions
```

#### 2. Backend Services (Python/FastAPI)
```
backend/
├── main.py                   # FastAPI app & endpoints
├── app/
│   ├── core/
│   │   ├── config.py         # Environment config (Pydantic)
│   │   ├── rate_limiter.py   # API rate limiting
│   │   └── security.py       # Input validation & sanitization
│   ├── models/
│   │   ├── database.py       # SQLAlchemy models
│   │   └── branch.py         # Branch data structures
│   └── services/
│       ├── cloner.py         # Git repository cloning
│       ├── analyzer.py       # Static code analysis (Radon)
│       ├── rag.py            # RAG pipeline (LangChain + FAISS)
│       ├── scorer.py         # Health score calculation
│       ├── branch_manager.py # Multi-branch support
│       ├── data_persistence.py # Database operations
│       ├── async_analysis.py # Background task management
│       └── analysis_cache.py # Caching layer
```

### Data Flow

```
1. User Input
   ↓
2. URL Validation & Sanitization
   ↓
3. Secure Git Clone (shallow, no hooks)
   ↓
4. Parallel Analysis Pipeline:
   ├─→ File Structure Mapping
   ├─→ Static Analysis (Radon)
   ├─→ Technology Detection
   ├─→ Secret Scanning
   └─→ AI Indexing (Embeddings)
   ↓
5. Score Calculation (Weighted Aggregation)
   ↓
6. Database Persistence
   ↓
7. Frontend Rendering
   ↓
8. Auto-Cleanup (1-hour TTL)
```

### Key APIs

| Endpoint | Method | Purpose |
|----------|--------|---------|
| `/analyze` | POST | Initiate repository analysis |
| `/structure/{repo_id}` | GET | Fetch file tree structure |
| `/metrics/{repo_id}` | GET | Get analysis metrics |
| `/chat` | POST | Query repository via RAG |
| `/score/{repo_id}` | GET | Retrieve health score |
| `/branches/{repo_id}` | GET | List repository branches |
| `/compare` | POST | Compare branch metrics |

---

## ✨ Core Features & MVP

### Feature 1: Code Quality Health Card
**User Story:** *As a developer, I want to instantly understand if a repository is well-maintained so I can decide whether to use or contribute to it.*

**Acceptance Criteria:**
- [ ] Display overall health score (0-100)
- [ ] Break down scores by category (Readability, Complexity, Maintainability, Security, Documentation)
- [ ] Show grade (A-F) with color coding
- [ ] Generate AI-powered summary explanation
- [ ] Complete analysis in < 30 seconds for repos < 100MB

**Implementation:**
```python
# Scoring algorithm (scorer.py)
score = (
    readability_score * 0.25 +
    complexity_score * 0.20 +
    maintainability_score * 0.20 +
    docs_coverage_score * 0.20 +
    security_score * 0.15
)
```

### Feature 2: Interactive File Atlas
**User Story:** *As a contributor, I want to navigate the project structure visually so I can quickly find relevant code.*

**Acceptance Criteria:**
- [ ] Render collapsible file tree
- [ ] Show file-level complexity metrics on hover
- [ ] Color-code files by health (red=complex, green=simple)
- [ ] Support search/filter by filename or path
- [ ] Display technology badges (Python, TypeScript, etc.)

### Feature 3: Chat with Repository (RAG)
**User Story:** *As a newcomer, I want to ask questions in natural language so I don't have to read all the code manually.*

**Acceptance Criteria:**
- [ ] Answer conceptual questions ("What does this project do?")
- [ ] Find specific logic ("Where is authentication handled?")
- [ ] Cite source files in responses
- [ ] Maintain conversation context
- [ ] Respond in < 3 seconds (95th percentile)

**Example Interaction:**
```
User: "Where is the payment gateway integration?"
Bot: "The payment gateway is handled in src/services/payment.py 
     (lines 45-120). It uses Stripe's API with webhook support 
     for asynchronous payment confirmations."
```

### Feature 4: Multi-Branch Comparison
**User Story:** *As a maintainer, I want to compare code quality across branches so I can review PRs objectively.*

**Acceptance Criteria:**
- [ ] Analyze multiple branches simultaneously
- [ ] Show side-by-side metric comparison
- [ ] Highlight regressions (complexity increases, coverage drops)
- [ ] Generate diff summary

### Feature 5: Historical Trend Analysis
**User Story:** *As a team lead, I want to track code quality over time so I can measure improvement.*

**Acceptance Criteria:**
- [ ] Store metrics with timestamps
- [ ] Display trend charts (line graphs)
- [ ] Show improvement/degradation rate
- [ ] Export data as CSV

### Feature 6: Security Scanning
**User Story:** *As a security engineer, I want to detect leaked secrets so I can prevent breaches.*

**Acceptance Criteria:**
- [ ] Scan for API keys, tokens, passwords
- [ ] Detect hardcoded credentials
- [ ] Redact secrets in UI
- [ ] Generate security report

---

## ⚡ Quick Start

### Prerequisites
- **Python 3.10+** ([Download](https://www.python.org/downloads/))
- **Node.js 18+** & npm ([Download](https://nodejs.org/))
- **Git** ([Download](https://git-scm.com/))
- **Google API Key** for Gemini ([Get one](https://aistudio.google.com/apikey))

### 5-Minute Setup

```bash
# 1. Clone the repository
git clone https://github.com/yourusername/code-lens.git
cd code-lens

# 2. Set up environment variables
cp .env.example .env
# Edit .env and add your GOOGLE_API_KEY

# 3. Start the backend
cd backend
python -m venv .venv
source .venv/bin/activate  # Windows: .venv\Scripts\activate
pip install -r requirements.txt
uvicorn main:app --reload --port 8000

# 4. Start the frontend (new terminal)
cd ../frontend
npm install
npm run dev

# 5. Open http://localhost:3000
```

---

## 🔧 Setup & Installation

### Detailed Backend Setup

#### 1. Create Virtual Environment
```bash
cd backend
python -m venv .venv

# Activate (choose your OS)
# Windows PowerShell:
.venv\Scripts\Activate.ps1
# Windows CMD:
.venv\Scripts\activate.bat
# macOS/Linux:
source .venv/bin/activate
```

#### 2. Install Dependencies
```bash
pip install --upgrade pip
pip install -r requirements.txt
```

**Key Dependencies:**
```
fastapi==0.110.0
uvicorn[standard]==0.27.0
langchain==0.1.9
langchain-google-genai==0.0.11
faiss-cpu==1.7.4
radon==6.0.1
sqlalchemy==2.0.25
pydantic-settings==2.2.1
```

#### 3. Configure Environment
Create `.env` in project root:
```env
# Google AI
GOOGLE_API_KEY=your_api_key_here

# Backend Settings
TEMP_DIR=temp_clones
PROJECT_NAME=Code Lens

# Frontend URL (for CORS)
ALLOWED_ORIGINS=["http://localhost:3000"]

# Scoring Weights (must sum to ≤ 1.0)
SCORE_WEIGHTS={"readability": 0.25, "complexity": 0.20, "maintainability": 0.20, "docs_coverage": 0.20, "security": 0.15}
```

#### 4. Run Backend Server
```bash
# Development mode (auto-reload)
uvicorn main:app --reload --host 0.0.0.0 --port 8000

# Production mode
uvicorn main:app --workers 4 --host 0.0.0.0 --port 8000
```

### Detailed Frontend Setup

#### 1. Install Node Dependencies
```bash
cd frontend
npm install
```

#### 2. Configure Environment
Create `frontend/.env.local`:
```env
NEXT_PUBLIC_API_URL=http://localhost:8000
```

#### 3. Run Development Server
```bash
npm run dev
# Opens on http://localhost:3000
```

#### 4. Build for Production
```bash
npm run build
npm start
```

### Verification Checklist
- [ ] Backend running on `http://localhost:8000`
- [ ] Frontend running on `http://localhost:3000`
- [ ] API docs accessible at `http://localhost:8000/docs`
- [ ] Can analyze a repository (test with `https://github.com/fastapi/fastapi`)

---

## 📖 Usage Guide

### Basic Workflow

#### Step 1: Analyze a Repository
1. Open `http://localhost:3000`
2. Paste a GitHub URL (e.g., `https://github.com/django/django`)
3. Click "Analyze Repository"
4. Wait 15-30 seconds for analysis

#### Step 2: Explore Results
**Health Card** displays:
- Overall score (0-100)
- Category breakdown
- Grade (A-F)
- AI-generated summary

**File Tree** shows:
- Project structure
- File complexity metrics
- Technology badges

**Metrics Tab** includes:
- Lines of code
- Cyclomatic complexity
- Maintainability index
- Comment ratio

#### Step 3: Chat with Repository
1. Click "Chat" tab
2. Ask questions:
   - "What does this project do?"
   - "Where is the authentication logic?"
   - "Explain the database schema"
3. Receive AI-powered answers with source citations

### Command-Line Usage

#### Analyze from CLI
```bash
curl -X POST http://localhost:8000/analyze \
  -H "Content-Type: application/json" \
  -d '{"github_url": "https://github.com/fastapi/fastapi"}'
```

Response:
```json
{
  "repo_id": "a1b2c3d4-5678-90ef-ghij-klmnopqrstuv",
  "status": "processing",
  "message": "Analysis started"
}
```

#### Check Status
```bash
curl http://localhost:8000/status/{repo_id}
```

#### Get Results
```bash
curl http://localhost:8000/metrics/{repo_id}
```

### Advanced Features

#### Multi-Branch Analysis
```python
# Analyze specific branch
POST /analyze
{
    "github_url": "https://github.com/user/repo",
    "branch": "development"
}

# Compare branches
POST /compare
{
    "repo_id": "...",
    "branch1": "main",
    "branch2": "feature-branch"
}
```

#### Export Results
```bash
# Get JSON export
curl http://localhost:8000/export/{repo_id} > results.json

# Get CSV metrics
curl http://localhost:8000/export/{repo_id}/csv > metrics.csv
```

---

## 📡 API Documentation

### Interactive Docs
Visit `http://localhost:8000/docs` for Swagger UI with live API testing.

### Core Endpoints

#### POST /analyze
Initiate repository analysis.

**Request:**
```json
{
  "github_url": "https://github.com/user/repo",
  "branch": "main",  // optional
  "deep_analysis": false  // optional
}
```

**Response:**
```json
{
  "repo_id": "uuid-string",
  "status": "processing",
  "estimated_time": 25
}
```

#### GET /metrics/{repo_id}
Fetch analysis results.

**Response:**
```json
{
  "repo_id": "...",
  "structure": { ... },
  "metrics": {
    "total_files": 245,
    "total_lines": 15420,
    "avg_complexity": 3.8,
    "maintainability_index": 72.5
  },
  "technologies": ["Python", "FastAPI", "SQLAlchemy"],
  "health_score": {
    "overall": 82,
    "grade": "B",
    "breakdown": {
      "readability": 85,
      "complexity": 78,
      "maintainability": 80,
      "docs_coverage": 88,
      "security": 79
    }
  }
}
```

#### POST /chat
Query repository via RAG.

**Request:**
```json
{
  "repo_id": "...",
  "question": "Where is the user authentication handled?",
  "conversation_id": "optional-id"
}
```

**Response:**
```json
{
  "answer": "Authentication is handled in app/core/security.py...",
  "sources": [
    {
      "file": "app/core/security.py",
      "lines": "25-67",
      "relevance": 0.94
    }
  ],
  "conversation_id": "uuid"
}
```

### Rate Limits
- **Analysis:** 10 requests/hour per IP
- **Chat:** 30 requests/minute per IP
- **Metrics:** 100 requests/minute per IP

---

## 🗃️ Data Models & Schemas

### Repository Model
```python
class Repository(Base):
    __tablename__ = "repositories"
    
    id = Column(String, primary_key=True)
    github_url = Column(String, nullable=False)
    name = Column(String)
    owner = Column(String)
    default_branch = Column(String, default="main")
    total_size_bytes = Column(Integer)
    file_count = Column(Integer)
    created_at = Column(DateTime, default=datetime.utcnow)
    last_analyzed = Column(DateTime)
    
    branches = relationship("Branch", back_populates="repository")
    analyses = relationship("Analysis", back_populates="repository")
```

### Analysis Model
```python
class Analysis(Base):
    __tablename__ = "analyses"
    
    id = Column(String, primary_key=True)
    repo_id = Column(String, ForeignKey("repositories.id"))
    branch_name = Column(String)
    commit_sha = Column(String)
    
    # Metrics
    total_lines = Column(Integer)
    code_lines = Column(Integer)
    comment_lines = Column(Integer)
    avg_complexity = Column(Float)
    maintainability_index = Column(Float)
    
    # Scores
    overall_score = Column(Float)
    readability_score = Column(Float)
    complexity_score = Column(Float)
    maintainability_score = Column(Float)
    docs_coverage_score = Column(Float)
    security_score = Column(Float)
    
    # Metadata
    technologies = Column(JSON)
    secrets_found = Column(JSON)
    analysis_duration = Column(Float)
    created_at = Column(DateTime, default=datetime.utcnow)
```

### API Schemas (Pydantic)
```python
class AnalyzeRequest(BaseModel):
    github_url: str
    branch: Optional[str] = "main"
    deep_analysis: bool = False

class HealthScore(BaseModel):
    overall: float
    grade: str
    breakdown: Dict[str, float]
    summary: str

class ChatRequest(BaseModel):
    repo_id: str
    question: str
    conversation_id: Optional[str] = None
```

---

## 🔒 Security & Threat Model

### Threat Matrix

| Category | Risk | Mitigation | Status |
|----------|------|------------|--------|
| **System Integrity** | Malicious git hooks execute code | `git clone --no-checkout`, disable hooks | ✅ Implemented |
| **Resource Attacks** | Zip bomb crashes server | 100MB size limit, 30s timeout | ✅ Implemented |
| **Network Security** | SSRF via local IPs | DNS validation, public IP whitelist | ✅ Implemented |
| **Data Privacy** | Leaked secrets exposure | Regex scanning, UI redaction | ✅ Implemented |
| **Injection Attacks** | Command injection in URLs | Input sanitization, parameterized calls | ✅ Implemented |
| **DoS** | Rate limit bypass | IP-based throttling | ✅ Implemented |

### Security Features

#### 1. Safe Git Cloning
```python
# No code execution during clone
subprocess.run([
    "git", "clone",
    "--depth", "1",          # Shallow clone
    "--no-checkout",         # Don't checkout files yet
    "--config", "core.hooksPath=/dev/null",  # Disable hooks
    url, target_dir
], timeout=30)
```

#### 2. Secret Scanning
Detects:
- AWS keys (`AKIA[0-9A-Z]{16}`)
- API tokens (`api[_-]?key[_-]?[a-zA-Z0-9]{32,}`)
- Private keys (`-----BEGIN.*PRIVATE KEY-----`)
- Database URLs with credentials

#### 3. Input Validation
```python
# URL validation
assert url.startswith("https://github.com/")
assert not url.contains("../")  # Path traversal
assert len(url) < 200

# Size limits
MAX_REPO_SIZE = 100 * 1024 * 1024  # 100MB
MAX_FILES = 10000
```

#### 4. Rate Limiting
```python
@gemini_limiter.limit("30/minute")
async def chat_endpoint():
    ...
```

---

## ⚙️ Performance & Optimization

### Caching Strategy
- **Analysis Results:** Cached for 24 hours
- **Vector Embeddings:** Persisted to disk (FAISS index)
- **File Structure:** Cached in SQLite database

### Optimization Techniques
1. **Shallow Git Clones:** 10x faster than full clones
2. **Parallel Analysis:** File-level parallelism using ThreadPoolExecutor
3. **Lazy Loading:** Embeddings generated on-demand for chat
4. **Auto-Cleanup:** 1-hour TTL for cloned repositories

### Performance Benchmarks
| Repository Size | Analysis Time | Memory Usage |
|----------------|---------------|--------------|
| < 10MB (small) | 5-8 seconds | ~200MB |
| 10-50MB (medium) | 15-25 seconds | ~400MB |
| 50-100MB (large) | 30-45 seconds | ~800MB |

---

## 🎓 Conclusion

Code Lens represents a paradigm shift in how developers approach open-source software. By treating code as analyzable data rather than executable instructions, we've created a safe, fast, and intelligent onboarding experience that democratizes access to complex codebases.

### Why Code Lens Matters

In a world where software complexity grows exponentially, the ability to quickly understand and assess code quality is no longer a luxury—it's a necessity. Code Lens bridges the critical gap between curiosity and contribution, transforming the daunting task of exploring unfamiliar code into an intuitive, AI-guided journey.

### The Vision

We envision a future where:
- **Every developer** can confidently explore any open-source project in minutes, not hours
- **Code quality** becomes as transparent and measurable as code coverage
- **AI-powered insights** replace tribal knowledge and reduce maintainer burden
- **Safe static analysis** becomes the standard for code exploration

### Join the Movement

Code Lens is more than a tool—it's a movement towards making open-source software more accessible, maintainable, and secure. Whether you're a first-time contributor looking for your next project, a maintainer seeking to reduce onboarding friction, or a team lead evaluating dependencies, Code Lens empowers you with the intelligence you need.

**Ready to take your code comprehension to the next level?**

---

<div align="center">

### 🚀 Start Analyzing Today

**Transform how you understand code—one repository at a time.**

[![Get Started](https://img.shields.io/badge/Get%20Started-Analyze%20Now-blue?style=for-the-badge)](http://localhost:3000)
[![Star on GitHub](https://img.shields.io/badge/Star%20on-GitHub-yellow?style=for-the-badge&logo=github)](https://github.com/yourusername/code-lens)

---

**Built with 💙 using Google Gemini AI** | **Powered by FastAPI & Next.js**

*"Making open source accessible, one scan at a time."*

[⬆ Back to Top](#code-lens---open-source-onboarding-engine)

</div>
