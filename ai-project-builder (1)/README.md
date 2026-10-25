# AI Project Builder

A full-stack starter for generating project code from specifications, downloading generated files as ZIP, and publishing to GitHub with OAuth.

## Stack
- React + Vite
- FastAPI
- OpenAI-compatible chat completions API (configurable by `AI_BASE_URL`)
- GitHub OAuth + GitHub REST API
- SQLite for lightweight project/session persistence

## Requirements
- Python 3.11+
- Node.js 20+
- GitHub OAuth App credentials for GitHub publishing
- AI provider API key for real AI generation

## Quick start

### 1. Backend
```bash
cd backend
python -m venv .venv
# Windows PowerShell: .venv\Scripts\Activate.ps1
# macOS/Linux: source .venv/bin/activate
pip install -r requirements.txt
copy .env.example .env  # Windows; use cp on macOS/Linux
uvicorn app.main:app --reload --port 8000
```
Edit `backend/.env` and configure `AI_API_KEY`, `GITHUB_CLIENT_ID`, `GITHUB_CLIENT_SECRET`, and `SESSION_SECRET`.

### 2. Frontend
```bash
cd frontend
npm install
npm run dev
```
Open the Vite URL, normally http://localhost:5173. The frontend proxies `/api` to `http://localhost:8000`.

### 3. GitHub OAuth callback
Set the GitHub OAuth App callback URL to:
`http://localhost:8000/api/github/callback`
For production, use HTTPS and your production callback URL. Configure `FRONTEND_URL` and `BACKEND_URL` accordingly.

## Features
- Analyze and generate code from a topic/specification
- Upload PDF, DOCX, TXT, and image files (image text extraction requires Tesseract; PDF/DOCX parsing uses Python libraries)
- File tree and source preview
- Download ZIP while excluding secrets and generated folders
- GitHub OAuth login, repository listing, new repository creation, and commit/publish
- Server-side credentials, session cookies, OAuth state, input/file validation, safe archive paths, publishing confirmation

## API
- `POST /api/projects/analyze`
- `POST /api/projects/generate`
- `GET /api/projects/{project_id}/files`
- `GET /api/projects/{project_id}/download`
- `GET /api/github/login`
- `GET /api/github/callback`
- `GET /api/github/repositories`
- `POST /api/github/publish`

## Notes and limitations
- AI generation requires a compatible provider endpoint and key. Defaults target OpenAI-compatible `/chat/completions`; set `AI_BASE_URL` to your provider's base URL and `AI_MODEL`.
- Image OCR requires the system Tesseract executable and `pytesseract`; without it, image uploads return a clear error.
- GitHub publishing requires a configured OAuth app. OAuth tokens are held in a signed, HTTP-only session cookie in this demo. For production, use a server-side session store, HTTPS, secure cookie settings, CSRF protection for all state-changing routes, rate limiting, and a managed secret store.
- This starter creates/updates repository contents via the GitHub Contents API. GitHub may impose API limits; errors are surfaced to the client.
- Generated code is untrusted output. Review it before running. The app does not execute generated code.
