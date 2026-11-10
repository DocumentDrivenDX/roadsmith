# Practices: datastore-postgresql (project-owned)

These practices start minimal on purpose. Design extends them as decisions
land in ADRs. Each practice names the evidence that shows it is followed.

## Requirements (Frame activity)

- Persistence requirements trace to PRD FR-1–FR-20. The data layer adds no
  product behavior of its own.
- Workspace isolation (PRD FR-18, FEAT-005) is a data-layer obligation as
  well as an API obligation.

## Design

- Record an ADR that ratifies PostgreSQL, pins the major version, and picks
  the hosting target.
- Record an ADR for the workspace-isolation mechanism: application-level
  scoping, row-level security, or both. Name the module that owns it.
- Decide how concurrent edits from two editors are resolved (PRD open
  question) before designing the write paths.
- Define the backup, restore, and recovery-point targets in the deploy
  artifacts. Do not assume what a provider guarantees.

## Implementation

- All schema changes go through versioned migrations run by one project
  command, never by hand.
- Database access lives in the data module named in the architecture boundary
  map (`modularity-and-encapsulation`). Other modules do not issue SQL.
- Connection settings come from the validated config schema
  (`typescript-bun`), not from scattered environment reads.
- Never log roadmap content. Log identifiers only.

## Testing

- Integration tests run against a real PostgreSQL instance of the pinned
  major version, not a mock or a different engine.
- Each migration is tested up and, where reversible, down.
- An isolation test suite shows that a principal in workspace X cannot list,
  read, or change workspace Y's data through any data-layer entry point.

## Deploy

- Run migrations as a gated deploy step that fails the deploy on error.
- Perform at least one restore from backup into a scratch environment before
  launch, and record the evidence.
