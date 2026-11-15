# Media Domain

## Purpose

Provide useful YouTube access without claiming capabilities that the active backend did not perform.

## Capability ladder

| Capability | Base implementation claim |
|---|---|
| Video discovery | Exa can return candidate YouTube URLs. |
| Metadata | The extension may retrieve title, channel, duration, and publication information. |
| Captions | An optional local `yt-dlp` path may retrieve manual or auto-generated captions when YouTube exposes them. |
| Audio transcription | A future local transcription backend may generate text from permitted audio. |
| Visual understanding | Not provided by captions or metadata. Requires a vision-capable backend that actually processes frames or video. |
| Full video understanding | Not provided unless an explicit multimodal backend is configured and invoked. |

## Honest YouTube fallback

When captions are available, return the caption language, caption type, timestamps, and provenance. When captions are unavailable, report metadata only and say that the video was not analyzed. Do not present metadata as a transcript or summary.

The base path should avoid downloading video media when subtitle retrieval is sufficient. Any later audio or video download must be explicit, bounded, cancellable, and subject to applicable permissions and source restrictions.

## Dependencies

`yt-dlp` is an optional local dependency for caption retrieval. Its absence is a capability limitation, not a reason to claim that the video was understood. Audio transcription and frame extraction are separate optional capabilities and should not be silently bundled into caption retrieval.

## Open questions

- Should caption retrieval be exposed as a dedicated tool or remain a `fetch_content` URL route?
- Which language preference and fallback order should be used?
- Should local transcription be included in a later release, and which local engine is acceptable?
