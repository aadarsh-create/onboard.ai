# onboard.ai

Paste a GitHub repo URL → get a full developer onboarding report in seconds.

🚀 Live demo: https://ui-onboard-ai.onrender.com

🧷 API: https://api-onboard-ai.onrender.com

---

## What it does

| Section | Output |
|---|---|
| **Summary** | Plain-English overview of what the project does |
| **Architecture** | How the system is structured + a live Mermaid flowchart |
| **File Structure** | Every important file and its purpose |
| **Setup Guide** | Prerequisites, install steps, env vars, verification command |
| **Starter Tasks** | Beginner-friendly first tasks grounded in the actual code |
| **Optimizations** | Practical improvements tied to real files |

All 4 Groq calls run **in parallel** — one GitHub fetch, one wait, full report.

---

## Tech stack

| Layer | Technology |
|---|---|
| Backend | FastAPI + Python 3.11+ |
| AI | Groq API |
| GitHub fetch | `httpx` — async, no auth required for public repos |
| Frontend | React 19 + Vite (JSX, no TypeScript) |

---

## Local setup

### Prerequisites
- Python 3.11+
- Node.js 18+
- Groq API keys — [console.groq.com/keys](https://console.groq.com/keys)

---

### 1. Clone

```bash
git clone https://github.com/your-username/onboard.ai.git
cd onboard.ai
```

### 2. Backend

```bash
# Create and activate virtual environment
python -m venv .venv
.venv\Scripts\activate          # Windows
source .venv/bin/activate       # macOS / Linux

# Install dependencies
pip install -r backend/requirements.txt

# Configure environment
copy backend\.env.example backend\.env   # Windows
cp backend/.env.example backend/.env     # macOS / Linux
```

Open `backend/.env` and fill in your values:

```env
GROQ_MODEL=your-supported-model

GROQ_API_KEY1=gsk_xxxx   # analyze_repo
GROQ_API_KEY2=gsk_xxxx   # explain_architecture
GROQ_API_KEY3=gsk_xxxx   # generate_setup_guide
GROQ_API_KEY4=gsk_xxxx   # get_starter_tasks

# Optional — raises GitHub rate limit from 60 to 5000 req/hr
GITHUB_TOKEN=ghp_xxxx
```

> You can use the same key for all 4 `GROQ_API_KEY*` entries. Using 4 separate keys gives each call its own TPM bucket so all 4 run truly in parallel without hitting rate limits.

Start the backend:

```bash
uvicorn backend.main:app --reload
```

API live at **http://127.0.0.1:8000** · Swagger UI at **http://127.0.0.1:8000/docs**

---

### 3. Frontend

```bash
cd frontend
npm install
npm run dev
```

App live at **http://localhost:5173**

> Both servers must be running at the same time.

---

## API

### `GET /`

Returns the backend status and registered API endpoint paths:

```json
{
  "status": "ok",
  "endpoints": ["/analyze-all", "/download", "/health", "/docs"]
}
```

### `GET /health`

Returns `{"status": "ok"}` when the backend is running.

### `POST /analyze-all`

```json
{ "repo_url": "https://github.com/owner/repo" }
```

Returns an `OnboardingReport` with `analysis`, `architecture`, `setup_guide`, and `starter_tasks_and_optimizations`.

### `POST /download`

Takes the `/analyze-all` response (plus `repo_url`) and returns an `onboarding-report.md` file attachment.

**Error codes**

| Code | Cause |
|---|---|
| `400` | Invalid URL or no usable source files found |
| `503` | GitHub fetch failed |
| `502` | Groq returned an unexpected response |

---

## Project structure

```
onboard.ai/
├── backend/
│   ├── main.py                  FastAPI app — CORS, router registration
│   ├── groq_client.py           Async Groq wrapper — 4 capabilities + analyze_all()
│   ├── schemas.py               Pydantic request/response models
│   ├── requirements.txt
│   ├── .env.example
│   ├── routers/
│   │   ├── analyze_all.py       POST /analyze-all
│   │   └── download.py          POST /download
│   └── services/
│       └── repo_context.py      GitHub fetch + context assembly
└── frontend/
    ├── package.json
    ├── vite.config.js
    ├── .env.example
    └── src/
        ├── App.jsx              Root component — API call, state, layout
        ├── main.jsx             ReactDOM entry point
        └── components/
            ├── RepoInputForm.jsx
            ├── ReportTabs.jsx
            ├── DownloadButton.jsx
            ├── MarkdownRenderer.jsx
            ├── MermaidDiagram.jsx
            └── tabs/
                ├── SummaryTab.jsx
                ├── ArchitectureTab.jsx
                ├── FileStructureTab.jsx
                ├── SetupTab.jsx
                ├── StarterTasksTab.jsx
                └── OptimizationsTab.jsx
```
## Collaborators

<a href="https://github.com/aadarsh-create">
<img src="https://wsrv.nl/?url=github.com/aadarsh-create.png&w=120&h=120&fit=cover&mask=circle" width="60" height="60" alt="aadarsh-create" />
</a>

<a href="https://github.com/srikar6259">
<img src="https://wsrv.nl/?url=github.com/srikar6259.png&w=120&h=120&fit=cover&mask=circle" width="60" height="60" alt="aadarsh-create" />
</a>

<a href="https://github.com/Hrushi-Goud">
<img src="https://wsrv.nl/?url=github.com/Hrushi-Goud.png&w=120&h=120&fit=cover&mask=circle" width="60" height="60" alt="Hrushi-Goud" />
</a>

<a href="https://github.com/vchittam-dot">
<img src="https://wsrv.nl/?url=github.com/vchittam-dot.png&w=120&h=120&fit=cover&mask=circle" width="60" height="60" alt="Hrushi-Goud" />
</a>

<a href="https://github.com/saiyashgitam">
<img src="https://wsrv.nl/?url=github.com/saiyashgitam.png&w=120&h=120&fit=cover&mask=circle" width="60" height="60" alt="Hrushi-Goud" />
</a>
