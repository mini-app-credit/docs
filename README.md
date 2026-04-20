# Mini Credit Application

Take-home prototype: vendor builds or uploads a credit application, sends a magic link to a recipient, recipient submits, vendor approves / adjusts terms / rejects. Stack: **NestJS** API (`app/`), **React + Vite** SSR/BFF (`frontend/`), **PostgreSQL**, **Docker** (`development/`). Optional **Gemini** for PDF extraction and advisory decision summaries.

## Quick start (Docker)

From the `development/` directory:

```bash
cp -r deployment.example/ deployment/
# Edit deployment/deployment.env and deployment/sensitive.env (DB, secrets, CLIENT_URL).
# For local TLS, run: make ssl-gen  (see development/README.md)

make dev-up    # or: make up  (production-like images)
make db-push   # apply Drizzle schema if needed; SQL migrations also run on API boot
```

**URLs (default):**

| Service    | URL |
|------------|-----|
| Frontend   | http://localhost:4000 |
| API        | http://localhost:3000 |
| Mailpit UI | http://localhost:8025 |
| API docs   | http://localhost:3000/docs |

**Vendor UI:** open **http://localhost:4000/applications** (draft/list flow is not behind login). The SPA proxies `/api` to the backend.

## Environment highlights

- **`DATABASE_WRITE_URL`** — Postgres connection string (required for API).
- **`CLIENT_URL`** — Public base URL of the SPA (used in recipient/vendor email links). Set to how users reach the app, e.g. `http://YOUR_IP:4000`.
- **`GEMINI_API_KEY`** — Enables PDF parsing (`POST /applications/parse-pdf`) and AI summary (`POST /applications/:id/ai-summary`). Optional; without it those endpoints return a clear error.
- **`GEMINI_MODEL`** — Optional override (default in code: `gemini-3-flash-preview`).
- **`CORS_ORIGIN`** — Comma-separated allowed origins if the browser calls the API host directly (not via the frontend `/api` proxy). Example: `http://46.165.215.86:4000`.

A **demo vendor user** (`00000000-0000-0000-0000-000000000001`) is seeded via `app/migrations/0001_seed_demo_vendor.sql` so `applications.vendor_id` foreign keys succeed; the frontend uses the same id as `DEMO_VENDOR_ID`.

## Assumptions & decisions

- **No real vendor auth** for the credit flow: vendor routes are public; a fixed demo `vendorId` ties list/create to one logical vendor (matches PRD “no authentication required” for the vendor).
- **Email** uses SMTP (e.g. Mailpit in compose). Failures are logged; links are also logged where useful for local debugging.
- **Credit amount** is stored as an integer (USD whole dollars) in the API; UI formats as currency.
- **Revenue bands** include `under_1m` and `over_500m` in addition to the PRD buckets for a cleaner closed set in enums.
- **Recipient link** is a UUID token, 7-day expiry, single use after submit.
- **AI PDF parse** maps only to known fields; unknown keys are returned as `droppedFields` for the UI notice.

## Known gaps & shortcuts

- **Apollo.io prefill** (extra credit): not implemented.
- **Dashboard filters:** status tabs omit a dedicated “Approved (adjusted)” filter; the status exists in data and badges.
- **Entry URL:** `/` may redirect unauthenticated users to sign-in (legacy IAM shell); vendor credit flow entry is **`/applications`**.
- **Root README** and **Loom** are submission artifacts; production deploy is left to your host (compose or custom).
- **Tests** for the credit module are limited; IAM/storage pieces are broader than this case study.

## Next 10 hours (priority order)

1. **Submission polish:** dedicated vendor landing with two clear CTAs (create vs. open dashboard), and/or redirect `/` to `/applications` for anonymous users when IAM is unused.
2. **Dashboard:** add filter tab for `approved_adjusted`; optional export CSV.
3. **Observability:** structured errors for DB constraint violations (e.g. FK) instead of generic 500 in dev; request id in logs.
4. **Apollo prefill:** business name step on `/apply/:token`, server-side proxy to Apollo, map into form defaults with graceful fallback.
5. **Hardening:** rate limits on `parse-pdf` and `send`, basic request size limits already partially in place.
6. **E2E smoke:** one Playwright (or similar) path: create → send (capture link from Mailpit/log) → submit → decide.

## How AI was used

- **Cursor** (and similar) for scaffolding, refactors, and debugging (routing, Drizzle schema, Nest modules, React forms).
- **Accepted** when suggestions matched existing patterns (Feature-style folders on the frontend, thin controllers, Zod DTOs).
- **Rejected** or trimmed when proposals added extra abstractions, duplicate types, or cross-feature imports that violated the intended layout.
- **Gemini** is used at runtime only for optional PDF extraction and advisory summaries; prompts and keys stay server-side.

## Further reading

- `development/README.md` — full Makefile, SSL, hosts, troubleshooting.
- `development/deployment.example/` — env templates and notes.
