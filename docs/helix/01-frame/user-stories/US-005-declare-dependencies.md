---
ddx:
  id: US-005
  authoring:
    home: repo
  depends_on:
    - FEAT-003
---

# US-005: Declare Dependencies

**Feature**: FEAT-003 — Sequencing and Capacity
**Feature Requirements**: DEP-01, DEP-02, DEP-03, DEP-04, DEP-05, DEP-06, DEP-07, DEP-08, DEP-09
**PRD Requirements**: FR-8
**Priority**: P0

## Story

**As a** Maya, the sole PM at a 12-person B2B SaaS company
**I want** to record which backlog items are blocked by which other items
**So that** no item is planned ahead of the work it needs, and a dependency
loop is caught when Maya enters it instead of in week five of the quarter.

## Context

Last quarter Maya's billing change slipped because its data-model blocker
was scheduled later, and nothing recorded that edge. Sequencing (US-006) and
plan checks (US-007) both read the "blocked by" edges this story creates, so
an unrecorded or cyclic edge makes both wrong. The story exercises the DEP
requirements of FEAT-003: declare, reject cycles with the cycle named, show
blockers and dependents, remove, and clean up on item deletion. A dependency
that conflicts with current placements is saved and reported as a finding
(DEP-09); the finding itself belongs to US-007.

## Walkthrough

1. Maya opens roadmap "Q1 2027" with items A "Billing v2", B "Usage
   metering", and C "Data model refactor", none with dependencies.
2. Maya opens item A and chooses to add a blocker.
3. Roadsmith offers the other items in the roadmap; Maya picks B.
4. Roadsmith saves "A blocked by B"; item A lists B under Blockers and item
   B lists A under Dependents.
5. Maya opens item B and adds C as a blocker; Roadsmith saves "B blocked by
   C".
6. Maya opens item C and tries to add A as a blocker.
7. Roadsmith rejects it with a message naming the cycle "A → B → C → A";
   item C still has no blockers.
8. Maya removes "A blocked by B" from item A.
9. Roadsmith removes the edge; item A shows no blockers and item B shows no
   dependents.

## Acceptance Criteria

- [ ] **US-005-AC1** — Given items A and B with no dependencies, when Maya
  declares A blocked by B, then item A lists B as a blocker.
- [ ] **US-005-AC2** — Given Maya has declared A blocked by B, when Maya
  opens item B, then item B lists A as a dependent.
- [ ] **US-005-AC3** — Given A is blocked by B and B is blocked by C, when
  Maya declares C blocked by A, then Roadsmith rejects it with a message
  naming the cycle "A → B → C → A".
- [ ] **US-005-AC4** — Given a declaration was rejected as a cycle, when
  Maya views items A, B, and C, then their blockers and dependents are the
  same as before the attempt.
- [ ] **US-005-AC5** — Given item A, when Maya declares A blocked by A, then
  Roadsmith rejects it as a cycle naming "A → A".
- [ ] **US-005-AC6** — Given A is already blocked by B, when Maya declares A
  blocked by B again, then Roadsmith says the dependency already exists.
- [ ] **US-005-AC7** — Given A is blocked by B, when Maya removes that
  dependency, then neither item A nor item B lists the other.
- [ ] **US-005-AC8** — Given A is blocked by B and B is blocked by C, when
  Maya deletes item B, then item A has no blockers and item C has no
  dependents.
- [ ] **US-005-AC9** — Given B is placed in Now and C is placed in Next,
  when Maya declares B blocked by C, then Roadsmith saves the dependency
  instead of rejecting it.
- [ ] **US-005-AC10** — Given Maya uses only the keyboard, when Maya adds
  and then removes a blocker on item A, then both changes complete with the
  same result as with a pointer.
- [ ] **US-005-AC11** — Given the AI service is unavailable, when Maya
  declares A blocked by B, then Roadsmith saves the dependency.

## Edge Cases

- **Long cycle**: A cycle through five items is named in full, each item
  once, in "blocked by" order and closing on its first item, for example
  "A → B → C → D → E → A".
- **Several cycles at once**: When the new edge would close more than one
  cycle, Roadsmith names at least one complete cycle and saves nothing.
- **Blocker from another roadmap**: Roadsmith offers only items in the same
  roadmap as blockers (FEAT-003 Out of Scope).
- **Rejected declaration and form input**: After a cycle rejection, the
  blocker picker keeps Maya's selection so Maya can read the message and
  choose another item.
- **Dependency on a shipped item**: Maya may declare an item blocked by an
  item that shipped in a closed period; checks treat it as satisfied
  (CHK-08, covered in US-008).

## Test Scenarios

| Scenario | AC ID | Input / State | Action | Expected Result |
|----------|-------|---------------|--------|-----------------|
| Declare a blocker | US-005-AC1 | Items A "Billing v2", B "Usage metering"; no edges | Declare A blocked by B | A's Blockers: B |
| Dependent shown on blocker | US-005-AC2 | A blocked by B | Open B | B's Dependents: A |
| Reject a cycle (PRD P0-3 sketch) | US-005-AC3 | A blocked by B; B blocked by C | Declare C blocked by A | Rejected; message names "A → B → C → A" |
| Nothing saved on rejection | US-005-AC4 | Same as above, after rejection | View A, B, C | A: blocker B; B: blocker C, dependent A; C: dependent B, no blockers |
| Self-dependency | US-005-AC5 | Item A | Declare A blocked by A | Rejected; message names "A → A" |
| Duplicate edge | US-005-AC6 | A blocked by B | Declare A blocked by B | Message "already exists"; A's Blockers: B (one entry) |
| Remove an edge | US-005-AC7 | A blocked by B | Remove A blocked by B | A: no blockers; B: no dependents |
| Delete a middle item | US-005-AC8 | A blocked by B; B blocked by C | Delete B | A: no blockers; C: no dependents |
| Conflicting placement accepted | US-005-AC9 | B in Now; C in Next | Declare B blocked by C | Edge saved; no rejection message |
| Keyboard only | US-005-AC10 | Items A, B; no edges | With keyboard only, add B as A's blocker, then remove it | Edge added, then removed; A shows no blockers |
| AI outage | US-005-AC11 | AI service stubbed to return errors | Declare A blocked by B | Edge saved |

## Dependencies

- **Stories**: None. Items must exist (FEAT-001 Backlog Intake, PRD FR-1, FR-2).
- **Feature Spec**: FEAT-003 — Sequencing and Capacity
- **Feature Requirements**: DEP-01 through DEP-09
- **PRD Requirements**: FR-8
- **External**: None.

## Out of Scope

- Reporting the dependency-order or same-period finding that a conflicting
  dependency causes (US-007).
- Dependency types other than "blocked by".
- Bulk import of dependencies from a CSV column.
- AI-proposed dependencies (FEAT-004 AI Assistance, PRD FR-15, FR-16).
