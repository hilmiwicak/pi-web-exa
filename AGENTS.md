# AGENTS.md

This repository is a small Pi extension for Exa-backed web search and locally controlled content retrieval.

- [Domain glossary](./GLOSSARY.md): canonical project language.
- [Domain documentation](./docs/): focused domain notes, kept flat and minimal.
- [Project specification](./SPEC.md): the existing `web_search` boundary regression requirements.

When a task changes domain language, update `GLOSSARY.md` in the same change. Keep domain notes short and colocated in `docs/`. Follow Pi's official extension patterns before introducing project-specific structure.

Do not claim that a capability exists unless the implementation actually performs it. YouTube is a later priority; for now, distinguish metadata, captions, transcription, and visual video understanding rather than designing those features.
