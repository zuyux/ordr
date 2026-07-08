# Roadmap

This roadmap defines the process for `ordr` v0.1.

The goal of v0.1 is a working local timeline for the most recent Ordinals inscriptions, powered by a Rust backend, a self-hosted `ord` server, and a minimal static frontend.

## v0.1 Goal

`ordr` v0.1 should prove that the project can:

- Read recent inscription data from a local `ord` server
- Normalize inscription metadata through a Rust API
- Render newest inscriptions first in a static timeline UI
- Preserve all content types as visible timeline entries
- Preview common media types without hiding unknown files
- Run without accounts, trackers, analytics, or centralized APIs

## Phase 1: Project Scaffold

Create the initial source structure.

Tasks:

- Create `backend/` Rust project
- Create `frontend/` static files
- Add basic configuration through environment variables
- Add local run documentation
- Keep the root documentation current

Expected structure:

```txt
ordr/
  backend/
  frontend/
  data/
  README.md
  ARCHITECTURE.md
  DEVELOPMENT.md
  ROADMAP.md
```

Done when:

- The Rust backend compiles
- The frontend can be opened or served locally
- Project commands are documented

## Phase 2: Rust Backend Foundation

Build the minimal HTTP service.

Tasks:

- Add HTTP server with `axum`
- Add async runtime with `tokio`
- Add structured JSON responses with `serde`
- Add request logging with `tracing`
- Add `/health` endpoint
- Add configuration for `ord` base URL

Initial endpoint:

```txt
GET /health
```

Done when:

- `GET /health` returns `{ "ok": true }`
- Backend host, port, and `ord` URL are configurable
- Backend runs locally without requiring a database

## Phase 3: ord Integration

Connect the backend to the local `ord` server.

Tasks:

- Create an `ord_client` module
- Request data from local `ord`
- Parse inscription metadata
- Handle `ord` connection errors clearly
- Add timeouts for upstream requests
- Avoid relying on centralized inscription APIs

Done when:

- Backend can talk to local `ord`
- Failed `ord` requests return useful API errors
- No third-party inscription API is required

## Phase 4: Recent Inscriptions API

Expose normalized timeline data.

Tasks:

- Add recent inscriptions endpoint
- Return compact JSON for frontend rendering
- Include inscription ID, number, content type, content length, block height, timestamp, and content URL when available
- Add `limit` query parameter
- Prepare for cursor-based pagination

Endpoint:

```txt
GET /api/inscriptions/recent
```

Response shape:

```json
{
  "items": [
    {
      "id": "INSCRIPTION_ID",
      "number": 0,
      "content_type": "text/plain",
      "content_length": 1024,
      "timestamp": "2026-01-01T00:00:00Z",
      "block_height": 0,
      "content_url": "/content/INSCRIPTION_ID"
    }
  ],
  "next_cursor": null
}
```

Done when:

- The endpoint returns newest inscriptions first
- Missing fields are handled gracefully
- Unknown content types are still returned

## Phase 5: Inscription Detail API

Add a focused endpoint for a single inscription.

Endpoint:

```txt
GET /api/inscriptions/:id
```

Tasks:

- Fetch one inscription by ID
- Normalize its metadata
- Include direct content URL
- Return `404` for unknown inscriptions
- Return stable JSON for the frontend

Done when:

- A timeline item can open a detail view
- Unknown inscription IDs return a clear error
- The frontend does not need to understand raw `ord` responses

## Phase 6: Static Timeline UI

Create the first usable interface.

Files:

```txt
frontend/index.html
frontend/style.css
frontend/app.js
```

Tasks:

- Fetch `/api/inscriptions/recent`
- Render inscription timeline items
- Show inscription ID, number, content type, size, block height, and timestamp
- Add direct content links
- Add loading and error states
- Keep styling minimal and readable

Done when:

- The page renders recent inscriptions from the backend
- Timeline entries remain readable across common desktop and mobile widths
- Failed requests show an understandable error state

## Phase 7: Preview Rules

Add lightweight previews for common media types.

Preview behavior:

```txt
image/*            -> image preview
text/*             -> short text preview when practical
application/json   -> formatted preview when practical
application/pdf    -> link or iframe preview
audio/*            -> native audio controls
video/*            -> native video controls
model/*            -> placeholder in v0.1
unknown            -> metadata-only entry
```

Tasks:

- Add content-type based renderer selection
- Avoid autoplay
- Avoid loading heavy content by default
- Keep unknown file types visible
- Add fallback state for failed previews

Done when:

- Common media types are recognizable in the timeline
- Unknown files are not hidden
- Heavy content does not dominate initial page load

## Phase 8: Optional SQLite Cache

Add caching only after the live path works.

Tasks:

- Add SQLite dependency
- Create metadata table
- Cache recent inscription records
- Read from cache when useful
- Keep cache rebuildable

Possible table fields:

```txt
id
number
content_type
content_length
block_height
timestamp
content_url
last_fetched_at
```

Done when:

- `ordr` works with or without the cache
- Cache failure does not break the live path
- `ord` remains the source of truth

## Phase 9: Privacy And Safety Pass

Review the app for cypherpunk defaults.

Tasks:

- Remove third-party scripts
- Avoid external fonts
- Confirm no analytics or trackers
- Sandbox HTML previews if added
- Avoid executing arbitrary inscription JavaScript in the app context
- Keep CORS behavior explicit
- Confirm direct content links are visible

Done when:

- The app can run fully local
- No user account or external service is required
- Inscription content is treated as untrusted

## Phase 10: v0.1 Release

Prepare the first tagged release.

Tasks:

- Test with local `ord server`
- Verify timeline loading
- Verify multiple content types
- Verify unknown content-type fallback
- Verify backend error behavior
- Update documentation
- Tag release as `v0.1.0`

Done when:

- A fresh clone can run the project with documented commands
- The app displays recent inscriptions locally
- The project meets the v0.1 goal

## v0.1 Non-Goals

The following are outside the first release:

- Wallet login
- User accounts
- Marketplace features
- Commenting or social features
- Recommendation algorithms
- Centralized hosted API dependency
- Full-text inscription search
- Complete 3D model rendering
- Advanced media transcoding
- Mobile app packaging

## v0.1 Definition Of Done

`ordr` v0.1 is complete when:

- Rust backend runs locally
- Backend reads from a self-hosted `ord server`
- Static frontend renders recent inscriptions newest-first
- Images, text, PDFs, audio, video, and unknown files are represented
- Direct content links are available
- The app works without accounts, trackers, analytics, or centralized APIs
- Setup and run instructions are documented

