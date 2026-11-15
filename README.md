# Pi Exa Web

A small Pi extension for Exa-backed web search and local content retrieval.

## Description

This project provides a focused alternative to larger web-access extensions. It is planned around two core tools:

- `web_search` for finding sources through Exa.
- `fetch_content` for retrieving and locally processing known URLs, including PDFs.

Large results and downloaded documents can be kept in temporary local files instead of being placed entirely into Pi's context. The project prioritizes simple behavior, clear limits, local extraction, and honest capability reporting.

YouTube support is intentionally a later priority. The project will not claim video understanding when it only has metadata or captions.

See [`AGENTS.md`](./AGENTS.md), [`GLOSSARY.md`](./GLOSSARY.md), and the focused notes in [`docs/`](./docs/) for project guidance and terminology.
