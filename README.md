# AI Learning Management System (AI LMS)

A local full-stack course platform with student and faculty accounts, SQLite persistence, document uploads, course assignments and quizzes, progress reporting, and a course-note-aware AI tutor. The no-key demo tutor is available by default and clearly labels its non-generative responses.

## Prerequisites

- Python 3.10 or newer
- Node.js 20 or newer and npm
- Optional for legacy `.doc` / `.ppt` files: [LibreOffice](https://www.libreoffice.org/)
- Optional live tutor: a Gemini, Groq, or OpenRouter API key

## Install and configure (Windows PowerShell)

1. Open two PowerShell terminals at the project root.
2. In Terminal 1:

   ```powershell
   cd backend
   py -m venv .venv
   .\.venv\Scripts\python.exe -m pip install -r requirements.txt
   Copy-Item .env.example .env
   ```

3. Edit `backend\.env` to change `SECRET_KEY` and, optionally, set an AI provider and key.
4. Start the API in Terminal 1:

   ```powershell
   .\.venv\Scripts\python.exe -m uvicorn app.main:app --reload
   ```

5. In Terminal 2, start the React/Vite frontend:

   ```powershell
   cd frontend
   npm install
   npm run dev
   ```

6. Open <http://127.0.0.1:5173>. FastAPI serves the JSON API at <http://127.0.0.1:8000/api>; interactive API docs are at <http://127.0.0.1:8000/docs>.

SQLite tables, the upload directory, and sample course data are created automatically at backend startup. No separate database setup is needed. Use `Ctrl+C` in both terminals to stop the app.

## Demo accounts and demo mode

- Student: `student@lms.com` / `student123`
- Faculty: `faculty@lms.com` / `faculty123`
- Default `AI_PROVIDER=demo` needs no key. Demo tutoring searches extracted note text; summaries select text from the uploaded document; quiz questions and flashcards are built from its sentences. The UI identifies these as demo responses, not live model output.

New accounts can be registered as either a student or faculty member. Seed data includes an Introduction to Biology course, sample study notes, an assignment, and a practice quiz. The demo sample file itself is a text note; uploaded supported files are kept in `backend\uploads`.

## Configure an optional AI provider

Set exactly one provider/key pair in `backend\.env`; restart Uvicorn after changing environment values. `AI_PROVIDER` may be `gemini`, `groq`, or `openrouter`. If it is blank or points to a provider without a key, the app selects the first configured provider key and otherwise remains in demo mode.

```dotenv
AI_PROVIDER=gemini
GEMINI_API_KEY=your-key
GEMINI_MODEL=gemini-2.5-flash
```

For Groq, set `AI_PROVIDER=groq`, `GROQ_API_KEY`, and optionally `GROQ_MODEL` (default `llama-3.3-70b-versatile`). For OpenRouter, set `AI_PROVIDER=openrouter`, `OPENROUTER_API_KEY`, and optionally `OPENROUTER_MODEL` (default `meta-llama/llama-3.3-70b-instruct:free`). Obtain provider keys from Google AI Studio, Groq Console, or OpenRouter. Check each provider's current model and account free-tier availability before choosing a model.

Other backend settings include `SECRET_KEY`, `DATABASE_URL`, `UPLOAD_DIR`, and `MAX_UPLOAD_MB`. The default SQLite database is `backend\lms.db`. Do not use the development secret for a deployed service.

## Features

- JWT sign-in/registration, BCrypt password hashing, and student/faculty authorization.
- Faculty course CRUD, PDF/PPT/PPTX/DOC/DOCX uploads, download, extraction, assignments, multiple-choice quiz authoring, AI quiz generation, and learner progress/submission review.
- Student enrollment, materials, assignment text/file submission, quizzes with immediate answer explanations, progress history/chart, and separate persisted tutor conversations with a clear-history option.
- Gemini, Groq, and OpenRouter JSON/text requests are made through plain REST requests; readable errors are returned for provider/network failures.
- Upload file-size and extension checks; extracted source context is relevance-ranked and capped at 6,000 characters.
- `.doc` and `.ppt` extraction uses LibreOffice if installed; `.docx` and `.pptx` are processed directly.

## Verify / smoke test

1. Log in with the demo faculty account, open the seeded course, upload a PDF/DOCX/PPTX, create an assignment, then add or generate a quiz.
2. Sign out and log in as the demo student; enroll in the course if necessary, submit an assignment, and take the quiz to see the saved score/review.
3. Ask the tutor a question about a named term in the notes. Confirm the interface identifies Demo mode. Set an optional AI key and restart the backend to try live AI.
