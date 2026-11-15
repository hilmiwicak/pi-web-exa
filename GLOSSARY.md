# Project Glossary

This glossary defines the language shared by the Pi integration, search, retrieval, artifact, and media domains. It records concepts, not implementation details.

## Project shape

**Capability claim**:
A statement about what the extension actually did for a request, including the evidence source and any limits. A capability claim must not imply video understanding when only captions were retrieved.
_Avoid_: best effort, intelligent fallback

**Web access extension**:
A Pi extension that gives an agent controlled access to external web information through explicit tools.
_Avoid_: browser, internet tool

## Search domain

**Search request**:
A request to a search provider for candidate sources, expressed through queries and bounded search filters.
_Avoid_: browse request, research request

**Search result**:
A candidate source returned by a search request, normally identified by a title, URL, and provider-supplied evidence such as a snippet or highlight.
_Avoid_: answer, source document

**Search provider**:
The external service that evaluates a search request and returns search results. Exa is the selected provider for this project.
_Avoid_: search engine, backend

**Research record**:
A stored search or retrieval result that can be revisited without repeating the remote operation.
_Avoid_: conversation context, cache blob

## Retrieval domain

**Fetch request**:
A request to retrieve one or more known URLs after search or direct user selection.
_Avoid_: curl command, browse

**Readable content**:
Content extracted from a source into a form that preserves the source's useful meaning while removing presentation noise.
_Avoid_: summary, answer

**Raw content**:
The source representation returned without semantic extraction, such as plain text, JSON, or an HTTP body.
_Avoid_: original page

**Extraction**:
The local transformation of retrieved bytes into readable or raw content without asking another hosted model to interpret them.
_Avoid_: understanding, summarization

**Retrieval artifact**:
A locally retained file produced by a fetch request, such as an original PDF, subtitle file, or extracted Markdown document.
_Avoid_: attachment, download

## Document and media domain

**PDF artifact**:
The original PDF bytes retained locally after a successful PDF fetch, separate from any extracted text derivative.
_Avoid_: PDF text, PDF preview

**YouTube metadata**:
Non-content information about a YouTube video, such as its title, channel, duration, and publication details.
_Avoid_: video understanding

**YouTube caption**:
Text supplied by YouTube for a video, either manually authored or automatically generated, with its language and provenance preserved.
_Avoid_: transcript understanding

**Audio transcription**:
Text generated from spoken audio by a transcription system. It is distinct from captions because it may be produced locally from downloaded audio.
_Avoid_: captions, video summary

**Visual video understanding**:
Interpretation of visual frames or video events by a vision-capable model. Metadata or captions alone do not constitute visual video understanding.
_Avoid_: video analysis, video fetch

**Honest fallback**:
A narrower capability that is explicitly reported as narrower, such as returning captions when full video understanding is unavailable.
_Avoid_: silent fallback, graceful degradation

## Pi integration domain

**Pi tool boundary**:
The public schema and execution boundary through which Pi models invoke an extension capability.
_Avoid_: provider API, internal adapter

**Stale argument**:
An argument retained by an older tool definition or adapter that is no longer part of the current public contract.
_Avoid_: invalid request, legacy schema

**Tool result**:
The model-visible response from a Pi tool call, including bounded content, artifact references, provenance, and actionable failure information.
_Avoid_: API response
