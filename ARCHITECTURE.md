# Architecture

`ordr` is a minimal Ordinals inscription timeline focused on the most recent inscriptions across all file types. The project is designed to stay small, self-hostable, auditable, and independent from centralized inscription APIs.

## Principles

- Self-hosted first
- Minimal frontend dependencies
- Rust backend
- No required accounts
- No analytics or trackers
- No third-party API dependency
- Tor-friendly and mirror-friendly
- Preserve unusual file types instead of hiding them
- Prefer simple, inspectable systems over opaque infrastructure

## System Overview

```txt
Bitcoin Core node
        |
ord indexer / ord server
        |
Rust API
        |
optional SQLite cache
        |
static timeline UI
```

## Components

### Bitcoin Core

Bitcoin Core provides the canonical blockchain data used by `ord`.

`ordr` does not talk directly to Bitcoin Core in the initial architecture. It relies on `ord` to index and expose Ordinals-specific data.

### ord

`ord` is the local Ordinals indexer and server.

The `ord` server is the primary source of inscription data, including inscription IDs, metadata, content types, and content routes.

### Rust API

The Rust backend is a small service that sits between the frontend and `ord`.

Responsibilities:

- Fetch recent inscription data from `ord`
- Normalize metadata for the frontend
- Detect and expose content types
- Provide timeline pagination
- Provide inscription detail data
- Optionally cache recent records
- Avoid mutating or rehosting inscription content unless explicitly needed

The API should stay narrow. It exists to make the frontend simple, fast, and stable without hiding the underlying Ordinals data model.

### SQLite Cache

SQLite is optional and should be introduced only when useful.

Possible uses:

- Cache recent inscription metadata
- Reduce repeated requests to `ord`
- Store timeline pagination state
- Speed up filtering by content type

The cache should be disposable. `ord` remains the source of truth.

### Static Frontend

The frontend is plain HTML, CSS, and minimal JavaScript.

Responsibilities:

- Render newest inscriptions first
- Display inscription metadata
- Preview common media types where practical
- Represent unknown or uncommon files clearly
- Link to full inscription content
- Provide basic filtering and pagination

The frontend should remain easy to serve from any static host, local machine, or Tor hidden service.

## Data Flow

1. Bitcoin Core syncs blockchain data.
2. `ord` indexes inscriptions from Bitcoin Core.
3. The Rust API requests recent inscription data from `ord`.
4. The Rust API normalizes metadata and optionally writes it to SQLite.
5. The frontend requests timeline data from the Rust API.
6. The frontend renders previews, metadata, and links to full content.

## Content Types

`ordr` should treat all inscription content as valid timeline material.

Supported categories include:

- Images
- Plain text
- HTML
- JSON
- PDFs
- Audio
- Video
- 3D models
- Fonts
- Archives
- Assets
- Programs
- Unknown binary files

The interface should not assume that image inscriptions are the default or most important category.

## API Shape

Initial API endpoints may include:

```txt
GET /api/inscriptions/recent
GET /api/inscriptions/:id
GET /api/content-types
GET /health
```

The API should return compact JSON suitable for timeline rendering.

Example inscription response shape:

```json
{
  "id": "INSCRIPTION_ID",
  "number": 0,
  "content_type": "text/plain",
  "content_length": 1024,
  "timestamp": "2026-01-01T00:00:00Z",
  "block_height": 0,
  "content_url": "/content/INSCRIPTION_ID"
}
```

## Frontend Rendering Strategy

The timeline should use lightweight renderers based on content type:

- `image/*`: image preview
- `text/*`: text preview
- `application/pdf`: PDF link or embedded preview if practical
- `audio/*`: native audio control
- `video/*`: native video control
- `model/*`: 3D model placeholder or renderer
- unknown types: metadata card with direct content link

Previews should be conservative. Heavy files should not be eagerly loaded by default.

## Security And Privacy

`ordr` should assume inscription content may be untrusted.

Guidelines:

- Avoid executing arbitrary inscription scripts in the main application context
- Use sandboxed iframes for HTML previews when needed
- Avoid remote fonts, trackers, and analytics
- Avoid leaking user behavior to third-party services
- Prefer local assets and local APIs
- Keep CORS and proxy behavior explicit

## Deployment

The preferred deployment is local or self-hosted:

```txt
bitcoind + ord + ordr-api + static files
```

Possible targets:

- Local machine
- VPS
- Home server
- Tor hidden service
- Static mirror with separate API endpoint

## Non-Goals

- Marketplace features
- Wallet login
- User accounts
- Social feeds
- Centralized inscription indexing dependency
- Heavy client-side application framework
- Opinionated curation of inscription content

## Initial Milestone

The first milestone is a working recent inscriptions timeline:

- Rust API connects to local `ord`
- Recent inscriptions are listed newest-first
- Metadata is normalized into compact JSON
- Static frontend renders the timeline
- Common media types receive basic previews
- Unknown media types remain visible and accessible

