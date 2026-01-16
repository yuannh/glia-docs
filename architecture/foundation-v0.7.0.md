# Glia Backend Architecture — Foundation v0.7.0

**Status**: Frozen  
**Date**: 2026-01-16  
**Related code version**: `glia-core@v0.7.0`

---

## What Changed Since v0.6.0
Foundation v0.7.0 introduces **prompt contracts** and **structured links**
that make the system a navigable knowledge graph without schema changes.

Key additions:
- Prompt contracts as the only source of AI behavior.
- Structured links in cards for stable navigation.
- Explicit, explainable link reasons and topic isolation.

---

## Prompt Contracts
Prompt contracts define all AI behavior and are loaded at runtime.

- All AI calls reference a contract name and version.
- Inline prompts are forbidden.
- Contracts require deterministic fallback behavior.
- Contract metadata is logged or embedded in existing JSON fields.
- No schema changes are introduced.

---

## Contracted Generation Outputs
AI generation is constrained by contract semantics:

- Chat responses are plain text only.
- Person events follow strict structured output.
- Story summaries follow strict structured output.
- Thread hypotheses are defined but not enabled.

This establishes auditable, versioned AI boundaries.

---

## Structured Links for Navigation
Cards expose structured links for navigation.

- Story cards expose `raw_json.links.entities`.
- Timeline items expose entity links derived from structured evidence.
- Story to story links require explicit reasons.
- Links are topic-scoped and never inferred from text.

Links exist even when empty to provide a stable interface.

---

## Explicit Non-Goals
Foundation v0.7.0 does not introduce:

- Inline prompts or ad-hoc AI instructions
- Automatic semantic inference of links
- Cross-topic linking
- Schema changes for prompt contracts

---

## What This Enables Next
Foundation v0.7.0 enables:

- Auditable AI behavior with deterministic fallback
- Stable navigation across story, timeline, and entity
- Safe extension of narrative intelligence layers

---

## Related Documents
- `DECISIONS/0019-prompt-contracts-only-source.md`
- `DECISIONS/0020-structured-links-knowledge-graph-v0.md`
- `architecture/foundation-v0.6.0.md`
