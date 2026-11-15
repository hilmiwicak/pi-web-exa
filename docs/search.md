# Search Domain

## Purpose

Find candidate web sources using Exa without fetching pages or invoking another model as a hidden search or summarization step.

## Responsibilities

- Normalize one query or a bounded list of queries.
- Apply supported result-count, recency, category, and domain filters.
- Call Exa through the selected credential path.
- Return compact search results with source URLs and provider evidence.
- Store full results when inline output is bounded.
- Preserve enough provenance for later retrieval and citation.

## Non-responsibilities

- Fetching arbitrary result pages automatically.
- Extracting page content.
- Selecting a second search provider.
- Producing an answer that claims more evidence than the returned sources contain.
- Choosing a model or provider based on the active Pi model.

## Invariants

- Exa is the only search provider in the planned first implementation.
- One Exa credential is the only paid provider credential required.
- `model` and `provider` are not part of the ordinary Pi-facing search contract.
- A search result is a candidate source, not proof that the source supports a claim.
- Search failures preserve useful per-query diagnostics when batch queries are used.

## Open questions

- Is an Exa API key mandatory for the first release, or should an explicitly documented keyless Exa route be supported?
- What is the maximum number of queries per call?
- Which Exa result fields are stable enough to expose as the public result shape?
