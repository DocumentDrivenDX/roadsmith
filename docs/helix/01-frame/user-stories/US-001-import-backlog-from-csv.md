---
ddx:
  id: US-001
  authoring:
    home: repo
  depends_on:
    - FEAT-001
---

# US-001: Import a Backlog from CSV

**Feature**: FEAT-001 — Backlog Intake
**Feature Requirements**: IMP-01–IMP-12, MAP-01–MAP-08, VAL-01–VAL-13, SUM-01–SUM-08
**PRD Requirements**: FR-1
**Priority**: P0

## Story

**As a** Maya, the sole PM (product manager) at a 12-person B2B SaaS company
**I want** to import my backlog export as a CSV (comma-separated values) file
or as text pasted from a spreadsheet, and confirm how its columns map to item
fields
**So that** all 60 of my backlog items are in the roadmap within 5 minutes,
and every row that could not be imported is listed with its row number and
reason.

## Context

Maya keeps 60–120 backlog items in a spreadsheet and a Linear export and has
one or two days to build next quarter's roadmap. Re-keying items wastes that
time, and an import that silently drops rows leaves gaps Maya finds only in
the leadership review. This story is the first step of every Roadsmith
session: it exercises the import source, column mapping, preview, and
confirmation areas of FEAT-001. Item editing and goals are US-002.

## Walkthrough

1. Maya opens the roadmap "Q1 2027 Plan", which has an empty backlog, and
   chooses to import items.
2. Roadsmith offers two sources: select a CSV file or paste tabular text.
3. Maya selects `q1-backlog.csv`. It has a header row `Name,Notes,Points` and
   61 data rows (rows 2–62). Row 17 has an empty Name. Row 9's Notes value is
   `"Split invoices, then migrate"`.
4. Roadsmith detects a comma delimiter and shows the mapping step. Title is
   pre-mapped to "Name". Description and Effort are unmapped.
5. Maya maps "Notes" to description and "Points" to Effort.
6. Roadsmith shows the preview: 60 rows will create items, 1 row is rejected,
   and 0 rows carry warnings. The first 20 rows appear as they will be
   imported, and row 9's description reads `Split invoices, then migrate`.
7. Maya confirms the import.
8. Roadsmith creates 60 items in the "Q1 2027 Plan" backlog in source row
   order. The summary shows "60 items created" and one rejected row: row 17,
   "title missing".
9. Maya dismisses the summary and sees the 60 items in the backlog.

## Acceptance Criteria

- [ ] **US-001-AC1** — Given Maya selects a CSV file whose header row contains
  "Title", when the mapping step opens, then title is pre-mapped to "Title".
- [ ] **US-001-AC2** — Given Maya selects a CSV file whose headers are
  `Name,Notes,Points`, when the mapping step opens, then title is pre-mapped to
  "Name".
- [ ] **US-001-AC3** — Given the mapping step has no column mapped to title,
  when Maya tries to continue, then Roadsmith keeps Maya on the mapping step
  with a message that title is required.
- [ ] **US-001-AC4** — Given `q1-backlog.csv` (header plus 61 data rows, row 17
  with an empty Name) is mapped, when Maya confirms the import, then exactly 60
  items exist in the roadmap backlog.
- [ ] **US-001-AC5** — Given `q1-backlog.csv` is mapped, when Maya confirms the
  import, then the summary lists exactly one rejected row: row 17, "title
  missing".
- [ ] **US-001-AC6** — Given Maya has reached the preview, when Maya cancels the
  import, then the roadmap backlog has the same items as before the import
  began.
- [ ] **US-001-AC7** — Given Maya pastes tab-separated text copied from a
  spreadsheet, when the mapping step opens, then each tab-separated value
  appears in its own column.
- [ ] **US-001-AC8** — Given a CSV file whose header row is
  `Title;Description` and whose data rows use semicolons, when the mapping
  step opens, then Roadsmith shows two columns named "Title" and
  "Description".
- [ ] **US-001-AC9** — Given a row whose title cell is `"Billing, phase 2"`,
  when Maya confirms the import, then one item is created with the title
  `Billing, phase 2`.
- [ ] **US-001-AC10** — Given a row whose description cell contains a quoted
  line break, when Maya confirms the import, then that row creates one item
  whose description keeps the line break.
- [ ] **US-001-AC11** — Given a mapped Effort cell holds `abc`, when Maya
  confirms the import, then the item is created with no Effort value.
- [ ] **US-001-AC12** — Given a mapped Effort cell holds `abc` in row 5, when
  Maya confirms the import, then the summary shows a warning naming row 5, the
  Effort field, and the value `abc`.
- [ ] **US-001-AC13** — Given the backlog already holds an item titled
  "SSO for enterprise tier", when Maya imports a row titled
  "sso for Enterprise tier", then a new item is created and the summary shows a
  duplicate-title warning naming the existing item.
- [ ] **US-001-AC14** — Given a CSV file with a header row and no data rows,
  when Maya selects it, then Roadsmith reports that no data rows were found and
  does not open the mapping step.
- [ ] **US-001-AC15** — Given a CSV file with 5,001 data rows, when Maya
  selects it, then Roadsmith rejects it before mapping with a message stating
  the 5,000-row limit.
- [ ] **US-001-AC16** — Given a file saved in Windows-1252 encoding that
  contains the byte for `é`, when Maya selects it, then Roadsmith rejects it
  before mapping with a message asking Maya to re-save the file as UTF-8.
- [ ] **US-001-AC17** — Given a UTF-8 file that starts with a byte order mark
  and has the header "Title", when the mapping step opens, then title is
  pre-mapped to "Title".
- [ ] **US-001-AC18** — Given Maya has reached the preview, when Maya activates
  confirm twice in quick succession, then each accepted row creates exactly one
  item.
- [ ] **US-001-AC19** — Given item creation fails partway through an import,
  when the failure occurs, then no item from that import remains and Roadsmith
  tells Maya the import failed.
- [ ] **US-001-AC20** — Given Maya uses only the keyboard, when Maya selects a
  file, maps columns, and confirms, then the import completes without a
  pointer device.

## Edge Cases

- **Rows whose cells are all empty**: Skipped without counting or reporting
  them (VAL-08).
- **Every row rejected**: The preview shows 0 rows to import and confirm is
  unavailable (SUM-03).
- **Unclosed quote at row 40 of 61**: Rows 2–39 import. One error names row 40
  and says the rest of the source could not be read (VAL-11).
- **Long row**: Cells beyond the header width are ignored with a warning
  naming the row (VAL-10).
- **Two columns headed "Notes"**: Shown as "Notes (column 2)" and
  "Notes (column 4)" so each can be mapped (MAP-08).
- **Value starting with `=`**: Stored and shown as the literal text, never
  evaluated (VAL-13).
- **Decimal comma**: Effort `2,5` is not a number, so the item is created
  without Effort and a warning names the row (VAL-05, VAL-06).
- **Wrong delimiter detected**: Maya changes it in the preview and the
  preview refreshes (IMP-05).

## Test Scenarios

| Scenario | AC ID | Input / State | Action | Expected Result |
|----------|-------|---------------|--------|-----------------|
| Exact header match | US-001-AC1 | File headers `Title,Description` | Select file | Title mapped to "Title"; description mapped to "Description" |
| Synonym header | US-001-AC2 | File headers `Name,Notes,Points` | Select file | Title mapped to "Name"; Notes and Points unmapped |
| Title unmapped | US-001-AC3 | Mapping with title cleared | Continue | Stays on mapping; "title is required" message |
| PRD FR-1 sketch, items | US-001-AC4 | `q1-backlog.csv`: header + 61 rows; row 17 Name empty; Name→title, Notes→description | Confirm | 60 items in "Q1 2027 Plan" |
| PRD FR-1 sketch, errors | US-001-AC5 | Same as above | Confirm | 1 rejected row: row 17, "title missing" |
| Cancel at preview | US-001-AC6 | Backlog has 3 items; preview shows 60 rows | Cancel | Backlog still has the same 3 items |
| Paste from spreadsheet | US-001-AC7 | Pasted `Title⇥Effort` header plus `Dark mode⇥3` (⇥ is a tab) | Paste | Columns "Title" and "Effort"; row 2 values `Dark mode` and `3` |
| Semicolon file | US-001-AC8 | `Title;Description` header plus `Audit log;Track admin actions` | Select file | Two columns; row 2 title `Audit log` |
| Quoted comma | US-001-AC9 | Row `"Billing, phase 2",Move plans` with headers `Title,Description` | Confirm | Item title `Billing, phase 2`; description `Move plans` |
| Quoted line break | US-001-AC10 | Row 3 description `"Line one` / `line two"` across two lines | Confirm | One item; description has two lines; next record is row 4 |
| Bad Effort value, item | US-001-AC11 | Row 5 title `Usage alerts`, Effort `abc` | Confirm | Item `Usage alerts` created with Effort empty |
| Bad Effort value, warning | US-001-AC12 | Same as above | Confirm | Warning: row 5, Effort, `abc` |
| Duplicate title | US-001-AC13 | Existing item `SSO for enterprise tier`; source row `sso for Enterprise tier` | Confirm | Backlog has both items; warning names the existing item |
| Header only | US-001-AC14 | File content `Title,Description` and nothing else | Select file | "No data rows found"; mapping step not shown |
| Too many rows | US-001-AC15 | 5,001 data rows | Select file | Rejected; message states 5,000-row limit |
| Non-UTF-8 file | US-001-AC16 | Windows-1252 file with title `Café menu` | Select file | Rejected; message asks to re-save as UTF-8 |
| Byte order mark | US-001-AC17 | UTF-8 file starting with BOM, header `Title` | Select file | Title mapped to "Title" |
| Double confirm | US-001-AC18 | Preview with 10 accepted rows | Activate confirm twice within 200 ms | Exactly 10 new items |
| Failure partway | US-001-AC19 | Preview with 60 accepted rows; storage fails after item 30 | Confirm | 0 new items; "import failed" message |
| Keyboard only | US-001-AC20 | `q1-backlog.csv` | Complete import with Tab, Enter, Space, and arrow keys | 60 items created |

## Dependencies

- **Stories**: None. US-002 builds on items this story creates.
- **Feature Spec**: FEAT-001 — Backlog Intake.
- **Feature Requirements**: IMP-01–IMP-12, MAP-01–MAP-08, VAL-01–VAL-13,
  SUM-01–SUM-08.
- **PRD Requirements**: FR-1.
- **External**: A signed-in workspace member with an open roadmap (FR-18).
  RICE input ranges from the Prioritization subsystem (FR-4) for VAL-05. The
  exact import interface belongs in a Contract written in Design.

## Out of Scope

- XLSX, Numbers, or Google Sheets files, and direct Linear, Jira, or GitHub
  import (PRD P2-3).
- Updating existing items from a re-imported file.
- Importing goals, goal links, dependencies, or period placements.
- Saved mapping templates and undo of a confirmed import.
- Editing items after import (US-002).
