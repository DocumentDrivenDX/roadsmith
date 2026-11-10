# Concern: datastore-postgresql (project-owned)

Project-owned filler for the `datastore` slot, chosen by the operator on
2026-11-10. HELIX ships no PostgreSQL concern, so this file and its
`practices.md` are the governing source until a verified library concern
exists.

## Category

infrastructure

## Areas

data, api, infra

## Slot

datastore

## Boundary

Owns which database engine Roadsmith persists to and the engine-level
operating rules: connection management, migration execution, backup and
restore, and how workspace isolation is enforced at the data layer. It does
**not** own the schema shape (normalization, keys, constraints, indexes),
which belongs to `relational-data-modeling`. It does not own authorization
semantics, which belong to `auth`.

## Components

- **Engine**: PostgreSQL. The major version is pinned in a Design ADR.
- **Migrations**: versioned and ordered, checked in with the code, and run by
  one project command (`relational-data-modeling` owns their shape).
- **Workspace isolation at the data layer**: every query that touches
  roadmap data is scoped by the workspace of the authenticated principal.
  Design decides the mechanism (application scoping, row-level security, or
  both) in an ADR.
- **Backup and restore**: a restore procedure that has been exercised, owned
  by the deploy runbook.

## Constraints

- Roadmap content is customer-confidential (PRD constraint). It must not
  appear in database logs or telemetry beyond identifiers.
- No provider-specific guarantee is assumed until Design selects a hosting
  target and records evidence.

## Drift Signals (anti-patterns to reject in review)

- A query on roadmap data that is not scoped to a workspace.
- Schema changes applied by hand instead of through a migration.
- Storing derived plan-check results as the source of truth instead of
  recomputing them from items, dependencies, and periods.
- Claiming backup coverage without a recorded restore exercise.

## When to use

All Roadsmith persistence. This concern is active for every work item labelled
`area:data`, and for `area:api` items that read or write roadmap data.

## Artifact Impact

- **Architecture / ADRs**: PostgreSQL version, isolation mechanism, hosting
  target, connection pooling.
- **Data design**: schema under `relational-data-modeling`.
- **Test plan**: migration up/down tests, and isolation tests in which one
  workspace attempts to reach another's data (FEAT-005 ISO requirements).
- **Runbook**: backup, restore, and migration rollback procedures.

## ADR References

None yet. The first Design ADR should ratify PostgreSQL and pin the version.
