# Retrieval Domain

## Purpose

Retrieve known HTTP(S) resources and turn them into bounded, locally controlled content without depending on another paid extraction service.

## Retrieval pipeline

```text
known URL
  -> URL and SSRF validation
  -> bounded HTTP retrieval
  -> content-type detection
  -> readable or raw extraction
  -> artifact retention when needed
  -> bounded tool result
```

## Responsibilities

- Accept HTTP and HTTPS URLs only.
- Reject credentials in URLs and private, loopback, link-local, or metadata destinations.
- Revalidate redirect targets.
- Enforce timeout, response-size, redirect-hop, and concurrency limits.
- Pass through Markdown, plain text, and JSON when raw content is requested.
- Extract readable Markdown from HTML locally.
- Route PDFs and YouTube URLs to their domain-specific handlers.
- Report what was retrieved and what was not interpreted.

## Non-responsibilities

- Arbitrary shell execution exposed to the model.
- Hosted page extraction or hidden LLM summarization.
- Claiming that a fetched document was fact-checked.
- Circumventing paywalls, authentication, bot controls, or media restrictions.

## Curl question

A curl subprocess may be used as a transport implementation, but `curl` is not the public domain concept or tool. The extension must own validation, argument construction, cancellation, output limits, and redirect safety. No shell-interpolated command may cross the Pi tool boundary.

## Open questions

- Should the first implementation use Node HTTP directly, curl, or a selectable transport?
- Which HTML extraction library is acceptable for the target runtime?
- How should pages that return a JavaScript shell be reported when no browser fallback is installed?
