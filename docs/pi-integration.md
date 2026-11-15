# Pi Integration Domain

## Purpose

Expose a small, predictable set of Pi tools while preventing the model-facing contract from accidentally selecting providers or models.

## Current direction

The planned public tools are:

- `web_search`: discover candidate sources through Exa.
- `fetch_content`: retrieve and locally process known URLs.
- `get_search_content`: revisit bounded or stored search and retrieval records.

The third tool remains a planning decision until its storage and lifecycle behavior are implemented and tested.

## Boundary invariants

- The ordinary Pi `web_search` schema does not expose `model` or `provider` overrides.
- Stale `model` and `provider` values are ignored at argument preparation and execution boundaries.
- Query-array normalization remains supported.
- Lower-level explicit override support, if needed by another caller, is not removed by the Pi-specific boundary.
- Tool output distinguishes inline content, stored content, and local artifact paths.
- A missing optional capability produces an explicit limitation, not a fabricated success.

## Lifecycle concerns

Stored records and temporary artifacts need defined behavior for:

- a new Pi session
- reload
- fork
- compaction
- expiration and cleanup

## Open questions

- Should `get_search_content` be part of the first release or follow the two core tools?
- What Pi version and package format will this extension target?
- Which session lifecycle events are available in the target Pi version?
