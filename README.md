# ordr

Minimalistic ordinal inscriptions timeline based on the most recent inscriptions.

`ordr` is a lightweight interface for watching new Bitcoin Ordinals inscriptions appear in chronological order. It is designed around a simple timeline that can surface every kind of inscribed file without forcing the experience into a single media type.

## Concept

Ordinals inscriptions are not only images. `ordr` treats the inscription stream as a broad, living archive of digital artifacts:

- Images
- Text files
- PDFs
- Videos
- 3D models
- Assets
- Programs
- Other arbitrary file types

The goal is to make recent inscriptions easy to scan, inspect, and follow as they arrive.

## Features

- Minimal chronological timeline of recent inscriptions
- Support for mixed media and arbitrary content types
- Clean metadata display for each inscription
- Fast preview-first browsing experience
- Direct access to full inscription content

## Framework

`ordr` is designed as a light web stack:

- Static HTML, CSS, and minimal JavaScript for the frontend
- Rust backend for fetching, normalizing, and serving inscription data
- Self-hosted `ord` indexer as the primary data source
- Optional SQLite cache for recent inscription metadata
- No required accounts, analytics, trackers, or third-party APIs

The frontend should stay portable and easy to mirror. The backend should stay small, auditable, and close to the underlying Ordinals data.

## Architecture

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

The Rust backend should read from a self-hosted `ord server`, expose a narrow local API for the timeline, and serve inscription content links without taking ownership away from the original source.

Initial backend responsibilities:

- Fetch recent block and inscription data from `ord`
- Normalize inscription metadata for the frontend
- Detect and expose content types
- Cache recent inscription records when useful
- Provide simple endpoints for timeline pagination and detail views

Initial frontend responsibilities:

- Render the newest inscriptions first
- Preview common media types where practical
- Show unknown or uncommon file types without hiding them
- Link directly to full inscription content
- Keep interaction lightweight and dependency-minimal

## Project Goals

- Keep the interface minimal and readable
- Prioritize the newest inscriptions
- Preserve the raw diversity of inscription content
- Avoid over-curating or hiding unusual file types
- Make exploration feel immediate
- Prefer self-hosted infrastructure
- Remain Tor-friendly and mirror-friendly
- Keep the codebase small enough to audit

## Roadmap

- Recent inscription feed
- Media-type detection and filtering
- Preview renderers for common formats
- Full inscription detail view
- Search and sorting
- Bookmarking or saved inscriptions

## Status

Early project scaffold.
