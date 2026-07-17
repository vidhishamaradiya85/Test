(planning)
I'm building a URL Shortener for a take-home assignment. Requirements:

Backend (FastAPI + Python):
- Endpoint to create a short URL from a long URL
- Redirect endpoint that resolves the short code and redirects to the original URL
- Stats endpoint returning click count for a given short URL
- API key auth for creating URLs only (redirects stay public)
- Persist to SQLite
- Handle duplicate URLs, invalid URLs, expired/missing short codes

Frontend (Next.js + React):
- Form to paste a long URL and get a short one
- Dashboard showing created URLs and click counts
- Loading and error states

I need a planning document covering:
1. My understanding of the requirements (in my own words, flagging any ambiguity)
2. The approach I'll take (data model, short-code generation strategy, auth mechanism, routing structure)
3. At least 2 alternative approaches I considered for short-code generation and why I didn't pick them
4. Tradeoffs in my chosen approach (what I'm optimizing for, what I'm giving up)
5. Open questions/edge cases worth flagging (e.g., what counts as "expired," case sensitivity of codes, redirect status code choice)

Write this as a markdown doc suitable for a /docs/ folder in a GitHub repo. Be specific and technical, not generic.

======================================================================================================================================================================
(backend)
Build the backend for a URL Shortener in /backend/ using FastAPI + SQLite (use SQLAlchemy or raw sqlite3, your call — tell me which and why).

Endpoints:
- POST /shorten — body: {url: string}, header: X-API-Key. Creates and returns a short code + short URL. If the same long URL was already shortened, return the existing short code instead of creating a duplicate. Validate the URL is well-formed; reject invalid ones with 400.
- GET /{short_code} — public, no auth. Resolves the code and issues a redirect (302) to the original URL. Increments a click counter. Return 404 for missing codes, 410 for expired ones (add an optional expiry field, default 30 days, on creation).
- GET /stats/{short_code} — public. Returns click count, original URL, created_at, expires_at.

Auth: simple API key via X-API-Key header, checked against a value from an environment variable, required only on POST /shorten.

Persist everything to SQLite in a urls.db file. Include a schema/migration script.

After building, run the server yourself and test all three endpoints (valid create, duplicate create, invalid URL, redirect, missing code, stats lookup) using curl or httpie in the terminal, and show me the output. Fix anything that fails before you finish.

Structure the code cleanly (routers/models/schemas separated, not all in one file).

======================================================================================================================================================================
(test)
Write pytest tests for the backend in /backend/tests/ (or /tests/ per repo structure — use /tests/ at the project-b level if that's cleaner, tell me which you chose and why).

Cover:
- Creating a short URL successfully with a valid API key
- Rejecting creation with a missing/invalid API key
- Rejecting an invalid/malformed URL
- Returning the same short code for a duplicate long URL
- Redirect works and increments click count
- Stats endpoint returns correct click count after multiple redirects
- 404 on a nonexistent short code
- 410 (or your chosen status) on an expired short code

Run the tests yourself and confirm they all pass. Show me the output. Use a separate test database or in-memory SQLite so tests don't pollute urls.db.

======================================================================================================================================================================
(frontend)
Build the frontend in /frontend/ using Next.js + React, consuming the backend built earlier at [BACKEND_URL, e.g. http://localhost:8000].

Two views:
1. Shorten form: paste a long URL, submit, get back a short URL (with copy-to-clipboard). Show a loading spinner while the request is in flight, and a clear error message if the backend returns 400 or the request fails (network error, backend down, etc). The API key can be hardcoded in an env var for now (NEXT_PUBLIC_API_KEY) since this is a demo.
2. Dashboard: list of URLs created in this browser session (store short codes in localStorage or similar client-side store) showing original URL, short URL, and current click count. Add a refresh button that re-fetches click counts from the stats endpoint. Loading state while fetching, error state if a lookup fails.

Use the built-in browser to actually load the app, submit a URL through the form, and check the dashboard updates. Take a screenshot and confirm it works end-to-end before you finish. Keep styling simple and clean — Tailwind is fine if it's already set up by create-next-app.

======================================================================================================================================================================
(Documentation)
I have a FastAPI backend for a URL shortener with these endpoints: [paste your actual endpoint list/OpenAPI JSON here, or summarize: POST /shorten, GET /{short_code}, GET /stats/{short_code}, auth via X-API-Key].

Write two docs:
1. API.md — endpoint reference: method, path, auth requirement, request/response examples (with curl), status codes and what they mean, including the edge cases (duplicate URL, invalid URL, expired code, missing code).
2. SETUP.md — setup guide: prerequisites, how to install backend deps, set the API key env var, run the backend, install frontend deps, set NEXT_PUBLIC_API_KEY, run the frontend, and run the tests. Assume the reader has never seen this repo.

Keep both concise and skimmable — headers, code blocks, no fluff.
