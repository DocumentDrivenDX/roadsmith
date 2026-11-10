---
ddx:
  id: FEAT-001
  authoring:
    home: repo
  depends_on:
    - prd
---

# Feature Specification: FEAT-001 — Backlog Intake

**Feature ID**: FEAT-001
**Priority**: P0
**Owner**: Roadsmith product team (assumption: the PRD names no owner)
**Covered PRD Subsystem(s)**: Backlog Intake
**Covered PRD Requirements**: FR-1, FR-2, FR-3
**Cross-Subsystem Rationale**: None — single subsystem.

## Overview

Backlog Intake implements PRD P0-1: import backlog items from a CSV
(comma-separated values) file or pasted tabular text (FR-1), manage items by
hand (FR-2), and define roadmap goals that items link to (FR-3). Every later
subsystem works on the items and goals this feature creates, so intake sets
the floor for the PRD goal of a first sequenced roadmap in one sitting.

## Ideal Future State

Maya exports 60–120 items from a spreadsheet or Linear, drops the file into a
roadmap, and sees a preview with title, description, and estimate columns
already matched. Rows that cannot be imported are listed by row number and
reason, and the rest import in one step. Maya then tidies the backlog by hand,
writes three goals for the quarter, and links items to them. Going from a raw
export to a clean, goal-linked backlog takes minutes, needs no AI, and nothing
enters the roadmap until Maya confirms it.

## Problem Statement

- **Current situation**: A PM's backlog lives in a spreadsheet or a tracker
  export (PRD persona Maya: 60–120 items in a spreadsheet and a Linear
  export). Suites that model more require heavy setup [PRD F5, F14].
- **Pain points**: Re-keying items costs hours. An import that stops on the
  first bad row, or that silently drops rows, leaves the PM unsure what entered
  the plan. Items without goals cannot be checked for goal coverage (FR-14).
- **Desired outcome**: A 50–100 row export becomes roadmap items in under 5
  minutes of PM time (assumption, derived from the PRD's 60-minute
  time-to-first-clean-roadmap hypothesis). Every source row is either imported
  or reported with its row number and reason.

## Functional Areas

| Area | User question or job | Feature responsibility |
|------|----------------------|------------------------|
| Import Source | Can I bring my export in as a file or by pasting it? | Accept a CSV file or pasted tabular text, read its rows and columns, and reject unreadable sources before mapping. |
| Column Mapping | Does Roadsmith understand which column is which? | Suggest and let the PM confirm which source column feeds each item field. |
| Preview and Validation | What will happen if I import this? | Show, before anything is created, which rows import, which are rejected, and which carry warnings. |
| Confirmation and Summary | What did the import actually do? | Create items only on confirmation, all at once, and report every rejected row and warning. |
| Item Management | Can I fix and extend the backlog by hand? | Create, edit, and delete individual backlog items. |
| Goals and Linking | Which items serve which goal this quarter? | Create, rename, and delete roadmap goals, and link each item to zero or more goals. |

### Terms

- **Backlog item**: one unit of planned work in one roadmap. It has a title,
  an optional description, optional RICE inputs (Reach, Impact, Confidence,
  Effort; owned by the Prioritization subsystem, FR-4), and zero or more goal
  links.
- **Goal**: a named outcome label in one roadmap. It is not an item and carries
  no score, effort, or key results.
- **Source**: the CSV file or pasted text for one import. Roadsmith does not
  keep the source after the import is confirmed or cancelled.
- **Source row**: one record in the source. Its **row number** follows
  spreadsheet numbering: the header row, when present, is row 1, and a record
  that spans several lines because of a quoted line break is one row.
- **Rejected row**: a source row that creates no item. **Warning**: a note on a
  source row that still creates an item.

## Task Model

| Task | Goal | Users | Needs | Rules | Realized by | Key |
|------|------|-------|-------|-------|-------------|-----|
| Import a backlog export into a roadmap | Every source row is an item or a reported rejection | Sole or lead PM, team of 5–50 | "Get my backlog in without a day of setup" | — | US-001 | import-backlog |
| Curate items and link them to goals | The backlog holds the right items, each linked to the goals it serves | Sole or lead PM, team of 5–50 | "Get my backlog in without a day of setup" | — | US-002 | curate-backlog-goals |

## Requirements

### Functional Requirements by Area

#### Import Source

IMP-01. The system must let a PM start an import into the roadmap they have
open from a CSV file they select.

IMP-02. The system must let a PM start an import into the roadmap they have
open from tabular text they paste.

IMP-03. The system must take file and pasted sources through the same mapping,
preview, and confirmation steps.

IMP-04. The system must detect whether the source delimiter is a comma, a
semicolon, or a tab.

IMP-05. The system must let the PM change the detected delimiter before
confirming, and must refresh the preview when they do.

IMP-06. The system must read quoted fields as defined by IETF RFC 4180
("Common Format and MIME Type for CSV Files"): a quoted field keeps any
delimiter, line break, or doubled double quote inside it as field content.

IMP-07. The system must accept UTF-8 sources with or without a byte order mark
(BOM), and must not include the BOM in the first header name.

IMP-08. The system must reject a source that is not valid UTF-8 before mapping,
with a message that asks the PM to re-save the file as UTF-8.

IMP-09. The system must reject a source with no data rows before mapping, with
a message stating that no data rows were found.

IMP-10. The system must reject a source larger than 5 MB or holding more than
5,000 data rows before mapping, with a message stating the limit that was
exceeded (limits are assumptions).

IMP-11. The system must treat the first non-blank row as the header row by
default.

IMP-12. The system must let the PM declare that the source has no header row.
The first row then becomes data, and columns are labeled "Column 1",
"Column 2", and so on.

#### Column Mapping

MAP-01. The system must offer these target fields: title (required),
description, Reach, Impact, Confidence, and Effort (each optional).

MAP-02. The system must pre-select, for each target field, the source column
whose header equals the field name, ignoring case and surrounding whitespace.

MAP-03. The system must also pre-select a column headed "Name" or "Summary" for
title when no column is headed "Title".

MAP-04. The system must let the PM change or clear any pre-selected mapping.

MAP-05. The system must allow at most one source column per target field and
at most one target field per source column.

MAP-06. The system must prevent the PM from continuing while no source column
is mapped to title, and must state that title is required.

MAP-07. The system must ignore unmapped source columns and must not store
their values.

MAP-08. The system must label columns that share a header name by name and
position (for example "Notes (column 3)") so each can be mapped on its own.

#### Preview and Validation

VAL-01. The system must show a preview before creating any item. It states
how many rows will create items, how many are rejected, and how many carry
warnings.

VAL-02. The preview must show the first 20 rows as they will be imported,
using the current mapping (row count is an assumption).

VAL-03. The system must reject a row whose mapped title is empty or only
whitespace, with the reason "title missing".

VAL-04. The system must reject a row whose trimmed title exceeds 200
characters, with the reason "title too long" (limit is an assumption).

VAL-05. When a mapped RICE value is not a number, or falls outside the range
the Prioritization subsystem (FR-4) allows for that input, the system must
still create the item. It must leave that input empty and record a warning
naming the row, the field, and the value.

VAL-06. The system must accept only a period as the decimal separator in
numeric fields, so "2,5" is not a number and triggers VAL-05.

VAL-07. The system must create an item for a row whose trimmed title matches,
ignoring case, another row in the source or an existing item in the roadmap. It
must record a duplicate-title warning naming the other row or item.

VAL-08. The system must skip rows whose cells are all empty, without
reporting or counting them.

VAL-09. The system must treat missing trailing cells in a short row as empty.

VAL-10. The system must ignore cells beyond the header width in a long row and
record a warning naming the row.

VAL-11. When a quoted field is never closed, the system must import the rows
before it. It must report one error naming the row where the quote opened and
stating that the rest of the source could not be read.

VAL-12. The system must trim leading and trailing whitespace from titles.

VAL-13. The system must store and display every imported value as literal
text. A value that begins with `=`, `+`, `-`, or `@`, or contains markup, is
never evaluated.

#### Confirmation and Summary

SUM-01. The system must create no item until the PM confirms the preview.

SUM-02. The system must let the PM cancel from the mapping or preview steps,
and must create no item and discard the source when they do.

SUM-03. The system must prevent confirmation when no row would create an item.

SUM-04. On confirmation, the system must create one backlog item per accepted
row in the open roadmap, in source row order.

SUM-05. The system must create all accepted rows of one import or none of them.
If creation fails, no item from that import remains and the PM sees that the
import failed.

SUM-06. The system must create items once per confirmed preview, however many
times the PM activates confirm.

SUM-07. After import, the system must show the number of items created, each
rejected row with its row number and reason, and each warning with its row
number.

SUM-08. The system must keep the post-import summary visible until the PM
dismisses it.

#### Item Management

ITEM-01. The system must let a PM create a backlog item in the open roadmap
with a title of 1–200 characters after trimming and an optional description of
up to 10,000 characters (limits are assumptions).

ITEM-02. The system must let a PM edit an item's title and description.

ITEM-03. The system must show an edited title wherever the item appears,
without a page reload.

ITEM-04. The system must reject a create or edit whose trimmed title is empty
or longer than 200 characters, and must keep the PM's entered values.

ITEM-05. The system must allow two items in one roadmap to share a title, and
must show a non-blocking notice naming the existing item when a PM saves a
duplicate.

ITEM-06. The system must ask for confirmation before deleting an item. The
confirmation names the item title and states how many goal links and
dependencies will be removed with it.

ITEM-07. On confirmed deletion, the system must remove the item, its goal
links, every dependency where it is the blocker or the blocked item (FR-8), and
its period placement (FR-9).

ITEM-08. The system must leave the item unchanged when the PM cancels the
deletion confirmation.

ITEM-09. The system must keep a PM's unsaved entries and show a retry option
when saving an item fails.

#### Goals and Linking

GOAL-01. The system must let a PM create a goal in the open roadmap with a
name of 1–100 characters after trimming (limit is an assumption).

GOAL-02. The system must reject a goal name that matches another goal in the
same roadmap, ignoring case and surrounding whitespace.

GOAL-03. The system must let a PM rename a goal and must keep its links.

GOAL-04. The system must ask for confirmation before deleting a goal, stating
how many items are linked to it.

GOAL-05. On confirmed goal deletion, the system must remove the goal and its
links and must leave every linked item otherwise unchanged.

GOAL-06. The system must let a PM link an item to any number of goals in the
same roadmap, including none.

GOAL-07. The system must let a PM remove a link between an item and a goal.

GOAL-08. The system must keep at most one link between a given item and goal.

GOAL-09. The system must show, for each goal, its linked items and their count.

GOAL-10. The system must show, for each item, the goals it is linked to.

### Non-Functional Requirements

Targets below are assumptions; the PRD sets no intake performance figures.
They are measured at the 95th percentile (p95) on the supported desktop
browsers in a production-like environment.

- **Performance**: The preview for a 1,000-row, 6-column source appears within
  2 s of file selection or paste. Confirming that import creates all 1,000
  items within 5 s. A 5,000-row source previews within 8 s.
- **Performance**: A manual create, edit, delete, link, or unlink shows its
  result within 500 ms.
- **Security**: Only members of the roadmap's workspace can import into it or
  read or change its items and goals (FR-18).
- **Security**: Item titles, descriptions, goal names, and source contents
  never appear in logs or telemetry, and intake sends no content to the LLM
  (large language model) provider.
- **Security**: The system keeps no copy of the source after the import is
  confirmed or cancelled.
- **Scalability**: A roadmap with 2,000 items and 50 goals loads its backlog
  within 2 s, and every requirement above still holds at that size.
- **Reliability**: An import that fails partway leaves zero items from that
  import (SUM-05). A failed item or goal save leaves the stored version
  unchanged.
- **Usability**: Every intake task can be completed by keyboard alone and meets
  WCAG (Web Content Accessibility Guidelines, W3C) 2.2 level AA.
- **Usability**: Every rejection and warning names the row number, the field
  where one applies, and the reason in plain language.

## User Stories

- [US-001 — Import a backlog from CSV](../user-stories/US-001-import-backlog-from-csv.md)
- [US-002 — Manage backlog items and goals](../user-stories/US-002-manage-items-and-goals.md)

## Edge Cases and Error Handling

- **Spreadsheet export with semicolons**: European-locale exports use
  semicolons. Detection picks semicolon; the PM can override it (IMP-05).
- **Single-column paste**: When no delimiter is found, the source is one
  column, which the PM maps to title.
- **Excel UTF-8 with BOM**: The BOM is removed, so a header "Title" still
  matches title (IMP-07, MAP-02).
- **Windows-1252 or other non-UTF-8 file**: Rejected before mapping (IMP-08);
  no partial or garbled import.
- **Quoted commas, line breaks, and quotes**: `"Billing, phase 2"` becomes one
  title. A description with a quoted line break stays one row (IMP-06).
- **Empty file or header-only file**: Rejected with "no data rows" (IMP-09).
- **Very large file**: A file over 5 MB or 5,000 data rows is rejected before
  mapping (IMP-10). The PM splits it and imports the parts.
- **Every row rejected**: The summary lists each rejection and confirm is
  unavailable (SUM-03).
- **Re-importing the same file**: Each row imports again with a
  duplicate-title warning (VAL-07). Intake never merges or updates existing
  items.
- **Teammate deletes an item while Maya edits it**: Maya's save fails with a
  message that the item no longer exists. The item is not recreated.
- **Deleting a blocker**: Removing an item that blocks others removes those
  dependencies, so dependent items are no longer blocked (ITEM-07).
- **Formula-like or markup values**: Stored and shown as literal text (VAL-13).

## Success Metrics

- Import completion rate: share of started imports that reach confirmation.
  A low rate signals mapping or validation friction.
- Effort-mapped share: share of imported items with an Effort value. It
  measures the PRD risk that PMs skip effort entry.
- Goal-linked share: share of items linked to at least one goal when the PM
  first runs a critique (FR-14).

## Constraints and Assumptions

- Intake is deterministic and works with the AI service unavailable (FR-17;
  principle "Deterministic core, AI at the edge").
- Assumption: "optional estimate fields" in FR-1 means the four RICE inputs.
  Their valid ranges are owned by the Prioritization subsystem (FR-4).
- Assumption: a RICE value that fails validation is a row warning, not a
  rejection. FR-1 rejects only rows that cannot be mapped, and a missing
  estimate is not fatal to an item.
- Assumption: a backlog belongs to one roadmap; imports, items, and goals are
  roadmap-scoped inside a workspace (FR-18).
- Assumption: duplicate titles are allowed and warned about, not blocked,
  because PMs may track similar items under one name.
- Assumption: size limits (5 MB, 5,000 rows, 200-character title,
  10,000-character description, 100-character goal name) exceed the PRD's
  50–120 item backlogs by a wide margin.
- Assumption: row numbers follow spreadsheet numbering (header is row 1) so
  the PM can find a reported row in the file. The PRD FR-1 sketch's "61-row
  CSV" is read as 61 data rows plus a header.
- Roadmap content is customer-confidential (PRD constraint), which drives the
  logging and source-retention NFRs.

## Dependencies

- **Other features**: Accounts and Workspaces subsystem (FR-18) for workspace
  and roadmap scoping. Prioritization subsystem (FR-4) for RICE input ranges.
  Sequencing and Capacity subsystem (FR-8, FR-9) for the dependency and
  placement records that item deletion removes.
- **External services**: None. Exact import, item, and goal interfaces belong
  in Contract artifacts written in Design.
- **PRD requirements**: P0-1 (FR-1, FR-2, FR-3). Supports P0-2 and P0-6 by
  supplying items and goals (FR-4, FR-12, FR-14).

## Out of Scope

- **Direct import from Linear, Jira, or GitHub Issues**: PRD P2-3. A PM
  exports CSV from those tools instead.
- **XLSX, Numbers, or Google Sheets files**: Not accepted. Copying cells from
  any spreadsheet and pasting them (IMP-02) covers the same need without a
  second file parser.
- **Automatic encoding conversion**: Non-UTF-8 files are rejected, not guessed.
- **Updating or merging existing items from a re-import**: Every import only
  creates items.
- **Importing goals, goal links, dependencies, or period placements**: Goals
  and links are managed by hand (GOAL-01–GOAL-07); dependencies and placements
  belong to Sequencing and Capacity.
- **AI extraction of items from unstructured notes**: FR-13 (P1), AI
  Assistance subsystem.
- **Editing RICE inputs or evidence on an item**: Prioritization subsystem
  (FR-4, FR-5). Intake only sets them at import.
- **Assignees, statuses, labels, due dates, or subtasks**: PRD non-goal (issue
  tracking).
- **Goal descriptions, key results, or progress tracking**: PRD non-goal (OKR
  management).
- **Saved mapping templates, bulk edit, bulk delete, or undo of a confirmed
  import**: Not in v1. The preview and confirmation step guard against bad
  imports.
- **Export of items to CSV**: PRD P2-1.
