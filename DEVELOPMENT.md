# Development

This document describes the first version of `ordr`: a minimal recent Ordinals inscriptions timeline backed by a Rust API and a self-hosted `ord` server.

## First Version Goal

The first version should prove the core loop:

```txt
local ord server -> Rust API -> static timeline UI
```

It should display the most recent inscriptions, preserve all file types, and provide direct access to inscription content.

## Scope

Version 1 should be intentionally small.

Included:

- Rust backend
- Static HTML, CSS, and minimal JavaScript frontend
- Recent inscriptions endpoint
- Inscription detail endpoint
- Basic content-type detection
- Simple timeline rendering
- Basic previews for common file types
- Direct links to full inscription content
- Optional SQLite metadata cache

Not included:

- Wallet login
- User accounts
- Marketplace behavior
- Comments, likes, or social feeds
- Heavy frontend framework
- Centralized inscription API dependency
- Full media renderer coverage

## Suggested Project Structure

```txt
ordr/
  README.md
  ARCHITECTURE.md
  DEVELOPMENT.md
  backend/
    Cargo.toml
    src/
      main.rs
      config.rs
      ord_client.rs
      routes.rs
      models.rs
      cache.rs
  frontend/
    index.html
    style.css
    app.js
  data/
    ordr.sqlite
```

## Local Requirements

The first version assumes:

- Bitcoin Core is installed and synced
- `ord` is installed and indexed
- `ord server` is running locally
- Rust is installed

Example local services:

```txt
bitcoind
ord server
ordr backend
static frontend
```

## Backend

The backend should be a small Rust HTTP service.

Recommended crates:

- `axum` for HTTP routing
- `tokio` for async runtime
- `reqwest` for requests to `ord`
- `serde` and `serde_json` for JSON
- `sqlx` or `rusqlite` if SQLite is added
- `tracing` for logs

The backend should read from a local `ord server` and expose a smaller API for the frontend.

### Configuration

Configuration should be environment-variable based:

```txt
ORDR_HOST=127.0.0.1
ORDR_PORT=3000
ORDR_ORD_BASE_URL=http://127.0.0.1:80
ORDR_DATABASE_URL=sqlite:data/ordr.sqlite
```

SQLite should be optional in the first version. If no database is configured, the backend can proxy and normalize data directly from `ord`.

### Initial Endpoints

```txt
GET /health
GET /api/inscriptions/recent
GET /api/inscriptions/:id
GET /api/content-types
```

### `GET /health`

Returns a simple health response.

```json
{
  "ok": true
}
```

### `GET /api/inscriptions/recent`

Returns recent inscriptions newest-first.

Query parameters:

```txt
limit=50
cursor=optional-pagination-cursor
content_type=optional-mime-filter
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

### `GET /api/inscriptions/:id`

Returns metadata for one inscription.

```json
{
  "id": "INSCRIPTION_ID",
  "number": 0,
  "content_type": "application/pdf",
  "content_length": 2048,
  "timestamp": "2026-01-01T00:00:00Z",
  "block_height": 0,
  "content_url": "/content/INSCRIPTION_ID"
}
```

### `GET /api/content-types`

Returns content-type counts when available.

```json
{
  "items": [
    {
      "content_type": "image/png",
      "count": 12
    }
  ]
}
```

## Frontend

The frontend should be static and dependency-minimal.

Files:

```txt
frontend/index.html
frontend/style.css
frontend/app.js
```

Responsibilities:

- Fetch `/api/inscriptions/recent`
- Render newest inscriptions first
- Show core metadata
- Render lightweight previews
- Allow loading more inscriptions
- Link to the original content URL

## Timeline Item

Each timeline item should show:

- Inscription number
- Inscription ID
- Content type
- Content size
- Block height
- Timestamp when available
- Preview or file placeholder
- Link to full content

## Preview Rules

Use native browser capabilities first.

```txt
image/*            -> img preview
text/*             -> short text preview if available
application/json   -> formatted text preview if available
application/pdf    -> link or iframe preview
audio/*            -> audio controls
video/*            -> video controls
model/*            -> placeholder in v1
unknown            -> metadata-only card
```

Heavy files should not autoplay or load aggressively.

## Cache

SQLite can be added after the basic feed works.

The cache may store:

- Inscription ID
- Inscription number
- Content type
- Content length
- Block height
- Timestamp
- Content URL
- Last fetched time

The cache should be treated as rebuildable. `ord` remains the source of truth.

## Error Handling

The first version should handle:

- `ord` server unavailable
- Empty recent inscription response
- Unknown content type
- Missing content length
- Failed preview rendering
- Backend request timeout

The UI should keep failed items visible instead of removing them silently.

## Security

Inscription content should be treated as untrusted.

Version 1 should:

- Avoid executing arbitrary inscription JavaScript in the app context
- Use sandboxed iframes for HTML previews if they are added
- Avoid third-party scripts
- Avoid external fonts
- Avoid analytics
- Keep API behavior explicit and narrow

## Milestone Checklist

- Create Rust backend project
- Add health endpoint
- Add `ord` client
- Fetch recent inscription data
- Normalize inscription metadata
- Add recent inscriptions endpoint
- Add inscription detail endpoint
- Create static frontend
- Render timeline items
- Add media-type preview rules
- Add load-more pagination
- Add basic error states
- Document local run commands

## Definition Of Done

The first version is done when:

- `ordr` runs locally against a self-hosted `ord server`
- The frontend displays recent inscriptions newest-first
- Multiple file types are visible in the timeline
- Common media types have basic previews
- Unknown media types remain accessible
- The project works without accounts, trackers, or centralized APIs

