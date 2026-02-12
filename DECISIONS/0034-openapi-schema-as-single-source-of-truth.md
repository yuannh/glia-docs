# adr 0034: openapi schema as single source of truth for api contract

Status: Accepted  
Date: 2026-02-05

## Context
API shape and response types were documented in multiple places, leading to
drift and inconsistent client code generation. iOS and other clients need a
machine-readable, authoritative contract.

## Decision
Treat **OpenAPI schema** as the single source of truth for the API contract.

- API version is defined in one place (e.g. app/core/version.py) and used everywhere.
- All endpoints declare response_model (or equivalent) so the generated openapi.json
  contains complete response schemas.
- Documentation (api-reference, api-guide, sse-chat) is generated or aligned with
  the schema; human-maintained docs do not contradict it.
- Clients may generate strong-typed code from openapi.json.

## Consequences

### Positive
- One authoritative contract; reduced drift between code and docs.
- Clients can generate and maintain SDKs from schema.
- Version and schema stay in sync when release process uses the single version source.

### Trade-offs
- All new endpoints must expose proper response models.
- Breaking schema changes require explicit versioning and communication.

## Alternatives Considered
- Keep API docs only in markdown  
  Rejected: no machine-readable contract for clients.
- Multiple version sources  
  Rejected: causes inconsistency across docs and builds.

## Validation / Acceptance Criteria
- openapi.json is generated from the running application and includes all public endpoints.
- Response schemas are non-empty for documented endpoints.
- Version in openapi.json matches the single version source.

## Links
- Related Architecture:
  - `architecture/foundation-v0.10.0.md`
