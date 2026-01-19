# Glia Backend Architecture — Foundation v0.8.0

**Status**: Frozen  
**Date**: 2026-01-16  
**Related code version**: `glia-core@v0.8.0`

---

## What Changed Since v0.7.0
Foundation v0.8.0 introduces **thread hypothesis suggestions** as a controlled,
AI-generated, read-only layer that does not create or modify threads.

Key additions:
- A prompt contract for thread hypotheses with JSON-only output.
- Strict schema validation for hypotheses and evidence.
- An idempotent write path that only targets `thread_suggestions`.

---

## Thread Hypothesis Suggestions
Thread hypotheses are AI-generated suggestions derived from user evidence.

- Suggestions are JSON-only and schema-validated.
- Evidence must include at least two user messages.
- `confidence` is constrained to `{low, medium, high}`.
- Suggestions are stored with status `pending`.
- Suggestions are idempotent via `dedup_key`.

This layer is non-destructive and read-only.

---

## Write Boundaries
The hypothesis job enforces strict write boundaries:

- It reads only user messages and related evidence.
- It writes only to `thread_suggestions`.
- It never writes to `threads` or `thread_members`.

Thread creation remains a separate, explicit process.

---

## Explicit Non-Goals
Foundation v0.8.0 does not introduce:

- Automatic thread creation from hypotheses
- Changes to story, artifact, or thread data
- Cross-topic linking
- Schema changes for suggestions beyond existing fields

---

## What This Enables Next
Foundation v0.8.0 enables:

- Explainable AI hypotheses without mutation
- Safe experimentation with narrative inference
- Future promotion workflows without violating constraints

---

## Related Documents
- `DECISIONS/0021-thread-hypothesis-suggestions.md`
- `DECISIONS/0019-prompt-contracts-only-source.md`
- `architecture/foundation-v0.7.0.md`
