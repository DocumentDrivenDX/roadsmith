---
ddx:
  id: feature-registry
  authoring:
    home: repo
  depends_on:
    - prd
---

# Feature Registry

**Last Updated**: 2026-11-10

## Active Features

Owner is the project operator for every feature until owners are assigned.
Each feature maps to one PRD subsystem.

| ID | Name | Description | Priority | Owner | Source | Updated |
|----|------|-------------|----------|-------|--------|---------|
| FEAT-001 | Backlog Intake | Import items from CSV or pasted text; manage items and goals manually (FR-1–FR-3) | P0 | Operator | PRD § Backlog Intake; US-001, US-002 | 2026-11-10 |
| FEAT-002 | Prioritization | RICE scoring and deterministic ranking (P0); evidence flags and rank sensitivity (P1) (FR-4–FR-6) | P0 | Operator | PRD § Prioritization; US-003, US-004 | 2026-11-10 |
| FEAT-003 | Sequencing and Capacity | Periods with capacity budgets, dependencies, auto-sequencing, and plan checks (P0); period close (P1) (FR-7–FR-11) | P0 | Operator | PRD § Sequencing and Capacity; US-005–US-008 | 2026-11-10 |
| FEAT-004 | AI Assistance | AI-drafted RICE inputs, plan critique with validated diffs, and degraded mode (P0); notes to items (P1) (FR-12–FR-17) | P0 | Operator | PRD § AI Assistance; US-009–US-012 | 2026-11-10 |
| FEAT-005 | Accounts and Workspaces | Sign-up, sign-in, and workspace-private roadmaps (P0); teammate invitations and password reset (P1) (FR-18–FR-20) | P0 | Operator | PRD § Accounts and Workspaces; US-013–US-015 | 2026-11-10 |

## Product Lifecycle

No releases yet.

## Dependencies

| Feature | Depends On | Type | Notes |
|---------|------------|------|-------|
| FEAT-002 | FEAT-001 | Required | Scores attach to backlog items |
| FEAT-003 | FEAT-001 | Required | Dependencies and placements reference backlog items |
| FEAT-003 | FEAT-002 | Required | Auto-sequencing uses rank order; capacity uses RICE Effort |
| FEAT-004 | FEAT-002 | Required | Drafted RICE inputs and unsupported-score findings use the scoring model |
| FEAT-004 | FEAT-003 | Required | Critique findings and diff validation use plan checks and the dependency graph |
| FEAT-001 | FEAT-005 | Required | Items and roadmaps belong to a workspace |

## Relevant References

- [PRD](prd.md), [Product Vision](../00-discover/product-vision.md),
  [Concerns](concerns.md), [Principles](principles.md)

## Feature Categories

### Planning core (deterministic)
- FEAT-001: Backlog Intake
- FEAT-002: Prioritization
- FEAT-003: Sequencing and Capacity

### AI layer
- FEAT-004: AI Assistance

### Platform
- FEAT-005: Accounts and Workspaces

## ID Rules

1. Sequential numbering: FEAT-XXX (zero-padded 3 digits)
2. Never reuse IDs, even for cancelled features
3. Do not encode category or priority into the ID
4. Keep full behavior in Feature Specifications, not in this registry

## Deprecated/Cancelled

None.

| ID | Name | Status | Reason | Date |
|----|------|--------|--------|------|
| — | — | — | — | — |
