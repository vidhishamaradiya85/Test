# Project A & Project B

This repository contains two full-stack applications:

- **Project A** — Notes API (a CRUD notes application with search and authentication)
- **Project B** — URL Shortener (a link-shortening service with click analytics)

Both projects follow the same stack pattern: a **FastAPI + SQLite** backend paired with a **Next.js + Tailwind CSS** frontend.

---

## Table of Contents

- [Project A: Notes API](#project-a-notes-api)
  - [Tech Stack](#tech-stack-a)
  - [Features](#features-a)
  - [API Endpoints](#api-endpoints-a)
  - [Setup Instructions](#setup-instructions-a)
  - [Running Tests](#running-tests-a)
- [Project B: URL Shortener](#project-b-url-shortener)
  - [Tech Stack](#tech-stack-b)
  - [Features](#features-b)
  - [API Endpoints](#api-endpoints-b)
  - [Setup Instructions](#setup-instructions-b)
  - [Running Tests](#running-tests-b)
- [Packaging & Sharing](#packaging--sharing)

---

## Project A: Notes API

A full-stack CRUD application for creating, editing, searching, and tagging notes, secured with API key authentication.

### Tech Stack (A)

**Backend**
- FastAPI
- SQLAlchemy (SQLite)
- Pydantic v2
- pytest

**Frontend**
- Next.js (App Router)
- TypeScript
- Tailwind CSS

### Features (A)

- Full CRUD for notes (create, read, update, delete)
- Search by keyword (title/body, case-insensitive) and by tag
- Pagination on the notes list (`skip`, `limit`)
- API key authentication (`X-API-Key` header) on all endpoints
- Standardized JSON error responses across the API
- Responsive dashboard UI with tag-based note cards
- Dedicated create (`/new`) and edit (`/edit/[id]`) pages with client-side validation

### API Endpoints (A)

| Method | Endpoint              | Description                                  |
|--------|------------------------|-----------------------------------------------|
| POST   | `/notes`               | Create a new note                             |
| GET    | `/notes`                | List notes (supports `skip` and `limit`)     |
| GET    | `/notes/{note_id}`      | Retrieve a single note (404 if not found)    |
| PUT    | `/notes/{note_id}`      | Partially update a note                      |
| DELETE | `/notes/{note_id}`      | Delete a note (204 on success)               |
| GET    | `/notes/search`         | Search by `q` (keyword) and/or `tag`         |

All endpoints require an `X-API-Key` header matching the value configured in `.env`.

### Setup Instructions (A)

**Backend**

```bash
cd project-a/backend
python -m venv venv
source venv/bin/activate        # Windows: venv\Scripts\activate
pip install -r requirements.txt
cp .env.example .env            # set API_KEY and any other required values
uvicorn app.main:app --reload
```

The backend will be available at `http://localhost:8000`. Interactive API docs are available at `http://localhost:8000/docs`.

**Frontend**

```bash
cd project-a/frontend
npm install
cp .env.local.example .env.local   # set the API base URL and API key
npm run dev
```

The frontend will be available at `http://localhost:3000`.

### Running Tests (A)

```bash
cd project-a/backend
pytest
```

This runs the full suite in `tests/test_notes.py`, covering CRUD operations, validation errors, 404 handling, search, and authentication enforcement against an isolated test database.

---

## Project B: URL Shortener

A full-stack application for shortening URLs and tracking click analytics.

### Tech Stack (B)

**Backend**
- FastAPI
- SQLite
- pytest

**Frontend**
- Next.js
- Tailwind CSS

### Features (B)

- Shorten any valid URL into a short code
- Redirect from short code to original URL
- Click analytics per short code
- Duplicate URL detection and malformed URL handling
- Dark glassmorphism UI with real-time form validation
- One-click copy-to-clipboard for generated short links
- Session-based history dashboard (via `localStorage`) with live stats

### API Endpoints (B)

| Method | Endpoint                  | Description                             |
|--------|----------------------------|-------------------------------------------|
| POST   | `/shorten`                 | Create a shortened URL                   |
| GET    | `/{short_code}`             | Redirect to the original URL             |
| GET    | `/stats/{short_code}`       | Get click analytics for a short code     |

### Setup Instructions (B)

**Backend**

```bash
cd project-b/backend
python -m venv venv
source venv/bin/activate        # Windows: venv\Scripts\activate
pip install -r requirements.txt
uvicorn app.main:app --reload
```

The backend will be available at `http://localhost:8000`.

**Frontend**

```bash
cd project-b/frontend
npm install
npm run dev
```

The frontend will be available at `http://localhost:3000`.

### Running Tests (B)

```bash
# Backend
cd project-b/backend
pytest

# End-to-end (Playwright)
cd project-b/frontend
npx playwright test
```

The pytest suite validates backend reliability (shorten, redirect, stats, edge cases). The Playwright script simulates a full user flow: creating a link and confirming it appears with correct stats on the dashboard.

---

## Packaging & Sharing

Before zipping either project to share with someone else, remove auto-generated and heavy folders so the recipient can restore the project locally:

```
node_modules/
.next/
venv/
__pycache__/
*.db
```

After unzipping, the recipient can restore each project with:

```bash
# Backend
pip install -r requirements.txt

# Frontend
npm install
```

Do not commit or share `.env` / `.env.local` files — only `.env.example` should be included, documenting the required variables (e.g., `API_KEY`).
