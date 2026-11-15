# Artifacts Domain

## Purpose

Keep source files and derived content available without flooding Pi's context window or confusing an extracted derivative with the source itself.

## Artifact classes

- **Original artifact**: bytes received from the source, such as a PDF or subtitle file.
- **Derived artifact**: locally produced content, such as extracted PDF text or normalized captions.
- **Preview**: bounded model-visible content selected from an artifact.
- **Research record**: stored search or retrieval data addressed by a response ID.

## PDF policy

A successful PDF retrieval retains the original PDF in a temporary artifact directory. Local text extraction, when available, produces a separate derived artifact. The tool result reports both paths and makes clear that extracted text is not the original PDF.

If the PDF has no usable text layer, the original remains available and the result says that extraction was unavailable or incomplete. OCR is an optional later capability, not an implied part of ordinary PDF retrieval.

## Large-result policy

Large content is not silently discarded. The tool returns a bounded preview plus a path or response ID for the complete local record.

## Safety and cleanup

- Temporary directories are created with restrictive permissions.
- Artifact names are derived from validated identifiers, not untrusted path fragments.
- Size limits apply before unbounded extraction.
- Cleanup has an explicit lifecycle and does not delete an artifact while a current tool result still points to it.

## Open questions

- Should artifacts live per session, per extension, or in an isolated configured root?
- What are the maximum PDF size, extracted text size, and retention period?
- Should the cache and temporary artifacts be separate stores?
