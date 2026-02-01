 # adr 0031: core notes as first-class user notes with server-derived cards
 
 Status: Accepted  
 Date: 2026-02-01
 
 ## Context
 Users need a fast, reliable notes experience that supports both text and images.
 Client-only derivation of titles, excerpts, and cover images leads to inconsistent
 rendering across platforms and frequent rework. Uploading images through core
 services adds latency and cost.
 
Summary: This ADR establishes Notes as a first-class, user-scoped subsystem with
server-derived card fields and presigned uploads to reduce client complexity and
improve consistency.

 The system already enforces user isolation and durable storage for other
 domains. Notes require similar durability and isolation while remaining
 lightweight for list views and scalable for media.
 
 ## Decision
 Introduce a **first-class Notes subsystem** with durable storage, server-derived
 card fields, presigned uploads, and asset binding based on content references.
 
 - Notes are stored as their own objects with user isolation.
 - List views return derived, stable card fields; full content is accessed via detail.
 - Title, excerpt, and cover image are derived server-side from note content.
 - Media uploads use presigned PUT URLs in compatible object storage.
 - Asset records are bound/unbound to notes based on content references.
 - Request correlation identifiers are mandatory across logs for auditability.
 
 ## Consequences
 
 ### Positive
 - Stable, deterministic list rendering across clients.
 - Reduced client complexity and duplicated parsing logic.
 - Media uploads scale without burdening core services.
 - Asset binding creates an auditable foundation for future cleanup.
 - Clear, user-scoped ownership boundaries for notes and assets.
 
 ### Trade-offs
 - Server-side derivation adds complexity at write time.
 - Presigned upload flow requires environment configuration and storage policies.
 - Asset binding relies on URL shape consistency in content.
 
 ## Alternatives Considered
 - Client-derived titles/excerpts/covers  
   Rejected: inconsistent rendering and repeated parsing logic.
 - Direct multipart uploads to core  
   Rejected: higher latency and resource cost for core services.
 - Store images directly inside note content  
   Rejected: large payloads and degraded list performance.
 
 ## Validation / Acceptance Criteria
 - Notes list and detail are consistent and renderable without client parsing.
 - Derived fields are stable across saves.
 - Presigned upload flow supports image insertion and retrieval.
 - Asset binding reflects content references and unbinds removed images.
 - Request IDs correlate logs for a single request end-to-end.
 
 ## Links
 - Related Architecture:
   - `architecture/foundation-v0.14.0.md`
   - `architecture/phase-2.md`
