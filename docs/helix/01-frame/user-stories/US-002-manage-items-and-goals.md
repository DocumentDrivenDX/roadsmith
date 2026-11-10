---
ddx:
  id: US-002
  authoring:
    home: repo
  depends_on:
    - FEAT-001
---

# US-002: Manage Backlog Items and Goals

**Feature**: FEAT-001 — Backlog Intake
**Feature Requirements**: ITEM-01–ITEM-09, GOAL-01–GOAL-10
**PRD Requirements**: FR-2, FR-3
**Priority**: P0

## Story

**As a** Maya, the sole PM (product manager) at a 12-person B2B SaaS company
**I want** to add, correct, and remove backlog items by hand, write the
quarter's goals, and link each item to the goals it serves
**So that** the backlog holds exactly the items Maya plans to rank, and every
item that serves a quarterly goal is linked to it before the first AI
critique checks goal coverage.

## Context

An imported backlog is never clean. Some items are missing, some titles are
unclear, and some items are stale. Goals are lightweight labels (PRD non-goal:
OKR management), and the AI critique's goal coverage finding (FR-14) is only
useful once items are linked to them. This story bundles FR-2 and FR-3 on
purpose: linking happens on items, so one curation pass on one roadmap
exercises both, and neither is demonstrable end to end without the other's
objects.

## Walkthrough

1. Maya opens the roadmap "Q1 2027 Plan", whose backlog holds 60 imported
   items including "Billing migration", "In-app NPS survey", and "Old
   onboarding survey".
2. Maya creates the goal "Reduce churn". Roadsmith lists it with 0 linked
   items.
3. Maya creates the goals "Launch self-serve billing" and "Cut support load".
4. Maya creates an item titled "SSO for enterprise tier" with the description
   "SAML login for accounts over 50 seats". Roadsmith adds it to the backlog,
   which now holds 61 items.
5. Maya edits "In-app NPS survey" and changes its title to "In-app churn
   survey". The new title appears in the backlog without a reload.
6. Maya links "Billing migration" and "In-app churn survey" to "Reduce churn".
7. Roadsmith shows "Reduce churn" with 2 linked items, and each of those items
   shows "Reduce churn" among its goals.
8. Maya also links "Billing migration" to "Launch self-serve billing".
   Roadsmith shows both goals on that item.
9. Maya chooses to delete "Old onboarding survey". Roadsmith asks for
   confirmation, naming the item and stating that 0 goal links and 0
   dependencies will be removed.
10. Maya confirms. Roadsmith removes the item, and the backlog holds 60 items.

## Acceptance Criteria

- [ ] **US-002-AC1** — Given Maya is in the "Q1 2027 Plan" backlog, when Maya
  saves a new item titled "SSO for enterprise tier", then the item appears in
  that backlog.
- [ ] **US-002-AC2** — Given Maya is creating an item, when Maya saves with a
  title of only spaces, then Roadsmith rejects the save with a message that a
  title is required.
- [ ] **US-002-AC3** — Given a save was rejected for a blank title, when the
  message appears, then the description Maya typed is still in the form.
- [ ] **US-002-AC4** — Given Maya is creating an item, when Maya saves a
  201-character title, then Roadsmith rejects the save with a message stating
  the 200-character limit.
- [ ] **US-002-AC5** — Given item "In-app NPS survey" is linked to goal
  "Reduce churn", when Maya renames it to "In-app churn survey", then the goal's
  linked-item list shows "In-app churn survey" without a page reload.
- [ ] **US-002-AC6** — Given the backlog holds "SSO for enterprise tier", when
  Maya saves another item with the same title, then the item is saved and
  Roadsmith shows a notice naming the existing item.
- [ ] **US-002-AC7** — Given Maya chooses to delete "Billing migration", which
  has 2 goal links and 1 dependency, when the confirmation appears, then it
  names "Billing migration" and states that 2 goal links and 1 dependency will
  be removed.
- [ ] **US-002-AC8** — Given the delete confirmation for "Billing migration" is
  shown, when Maya confirms, then "Billing migration" no longer appears in the
  backlog or in any goal's linked items.
- [ ] **US-002-AC9** — Given item "Data model v2" blocks "Billing migration",
  when Maya confirms deletion of "Data model v2", then "Billing migration" has
  no blockers.
- [ ] **US-002-AC10** — Given the delete confirmation for "Billing migration"
  is shown, when Maya cancels, then the item and its goal links are unchanged.
- [ ] **US-002-AC11** — Given the roadmap has no goals, when Maya creates the
  goal "Reduce churn", then Roadsmith lists it with 0 linked items.
- [ ] **US-002-AC12** — Given the roadmap has the goal "Reduce churn", when
  Maya creates a goal named " reduce CHURN ", then Roadsmith rejects it with a
  message that the goal already exists.
- [ ] **US-002-AC13** — Given items A, B, and C and the goal "Reduce churn",
  when Maya links A and C to "Reduce churn", then the goal shows 2 linked
  items.
- [ ] **US-002-AC14** — Given Maya linked A and C to "Reduce churn" but not B,
  when Maya opens item B, then B shows no goals.
- [ ] **US-002-AC15** — Given "Billing migration" is linked to "Reduce churn",
  when Maya links it to "Launch self-serve billing", then the item shows both
  goals.
- [ ] **US-002-AC16** — Given "Billing migration" is linked to "Reduce churn",
  when Maya links it to "Reduce churn" again, then the goal still shows that
  item once.
- [ ] **US-002-AC17** — Given "Reduce churn" has 2 linked items, when Maya
  removes the link to "Billing migration", then the goal shows 1 linked item.
- [ ] **US-002-AC18** — Given "Reduce churn" has 2 linked items, when Maya
  renames it to "Reduce logo churn", then the renamed goal shows the same 2
  linked items.
- [ ] **US-002-AC19** — Given Maya chooses to delete "Reduce churn", which has
  2 linked items, when the confirmation appears, then it states that 2 items
  are linked.
- [ ] **US-002-AC20** — Given "Reduce churn" has 2 linked items, when Maya
  confirms its deletion, then both items remain in the backlog with their
  titles and descriptions unchanged.
- [ ] **US-002-AC21** — Given the connection drops while Maya saves an edited
  description, when the save fails, then Roadsmith shows an error with a retry
  option and keeps the edited text.
- [ ] **US-002-AC22** — Given Maya uses only the keyboard, when Maya creates an
  item, links it to a goal, and deletes it, then each step completes without a
  pointer device.

## Edge Cases

- **Teammate deleted the item first**: Maya's save fails with a message that
  the item no longer exists, and the item is not recreated.
- **Description over 10,000 characters**: The save is rejected with a message
  stating the limit, and the typed text is kept (ITEM-01, ITEM-04).
- **Goal name over 100 characters**: Rejected with a message stating the limit
  (GOAL-01).
- **Renaming a goal to another goal's name**: Rejected as a duplicate
  (GOAL-02).
- **Deleting the last item linked to a goal**: The goal remains with 0 linked
  items.
- **AI service unavailable**: Every step in this story still works (FR-17).

## Test Scenarios

| Scenario | AC ID | Input / State | Action | Expected Result |
|----------|-------|---------------|--------|-----------------|
| Create item | US-002-AC1 | Backlog of 60 items | Save title `SSO for enterprise tier`, description `SAML login for accounts over 50 seats` | Backlog holds 61 items including the new one |
| Blank title | US-002-AC2 | New item form | Save title `"   "` | Rejected; "title is required" |
| Input kept | US-002-AC3 | Description `Cut churn from failed cards`; blank title | Save | Description field still reads `Cut churn from failed cards` |
| Title too long | US-002-AC4 | Title of 201 `x` characters | Save | Rejected; message states 200-character limit |
| Rename shows everywhere | US-002-AC5 | `In-app NPS survey` linked to `Reduce churn` | Rename to `In-app churn survey` | Goal list shows `In-app churn survey` without reload |
| Duplicate item title | US-002-AC6 | Existing `SSO for enterprise tier` | Save new item with same title | Saved; notice names existing item |
| Delete confirmation text | US-002-AC7 | `Billing migration`: 2 goal links, blocked by `Data model v2` | Choose delete | Dialog names item; "2 goal links and 1 dependency" |
| Delete confirmed | US-002-AC8 | Same as above | Confirm | Item absent from backlog, `Reduce churn`, and `Launch self-serve billing` |
| Delete a blocker | US-002-AC9 | `Data model v2` blocks `Billing migration` | Delete `Data model v2` and confirm | `Billing migration` shows no blockers |
| Delete cancelled | US-002-AC10 | Same as AC7 | Cancel | Item and both goal links present |
| Create goal | US-002-AC11 | Roadmap with 0 goals | Create `Reduce churn` | Goal listed; 0 linked items |
| Duplicate goal name | US-002-AC12 | Goal `Reduce churn` exists | Create ` reduce CHURN ` | Rejected; "goal already exists" |
| PRD FR-3 sketch, goal | US-002-AC13 | Items A, B, C; goal `Reduce churn` | Link A and C | Goal shows 2 linked items |
| PRD FR-3 sketch, item B | US-002-AC14 | Same as above | Open item B | No goals shown |
| Two goals on one item | US-002-AC15 | `Billing migration` linked to `Reduce churn` | Link to `Launch self-serve billing` | Item shows both goals |
| Repeat link | US-002-AC16 | `Billing migration` linked to `Reduce churn` | Link again | Goal lists `Billing migration` once; count 2 |
| Unlink | US-002-AC17 | `Reduce churn` with 2 items | Remove `Billing migration` link | Count 1 |
| Rename goal | US-002-AC18 | `Reduce churn` with 2 items | Rename to `Reduce logo churn` | `Reduce logo churn` shows the same 2 items |
| Goal delete confirmation | US-002-AC19 | `Reduce churn` with 2 items | Choose delete | Dialog states 2 linked items |
| Goal deleted | US-002-AC20 | Same as above | Confirm | Goal gone; backlog still holds both items unchanged |
| Save fails | US-002-AC21 | Network offline during save of description `Retry billing` | Save | Error with retry; field reads `Retry billing` |
| Keyboard only | US-002-AC22 | Backlog of 60 items; goal `Reduce churn` | Create, link, delete with Tab, Enter, Space, and Escape | Each step completes |

## Dependencies

- **Stories**: US-001 supplies the imported backlog used in the walkthrough.
  Every AC can also run on a backlog of hand-created items.
- **Feature Spec**: FEAT-001 — Backlog Intake.
- **Feature Requirements**: ITEM-01–ITEM-09, GOAL-01–GOAL-10.
- **PRD Requirements**: FR-2, FR-3. Bundled because linking is an action on
  items, so neither requirement is demonstrable end to end alone.
- **External**: A signed-in workspace member with an open roadmap (FR-18).
  US-002-AC7 and US-002-AC9 need "blocked by" dependencies from the
  Sequencing and Capacity subsystem (FR-8). Exact item and goal interfaces
  belong in a Contract written in Design.

## Out of Scope

- Editing RICE inputs or evidence notes (Prioritization subsystem, FR-4, FR-5).
- Declaring dependencies or placing items in periods (FR-8, FR-9).
- Goal descriptions, key results, or progress tracking (PRD non-goal: OKR
  management).
- Bulk edit, bulk delete, or bulk linking.
- Assignees, statuses, labels, or due dates (PRD non-goal: issue tracking).
