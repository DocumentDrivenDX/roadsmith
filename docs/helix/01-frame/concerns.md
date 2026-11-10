---
ddx:
  id: concerns
  authoring:
    home: repo
---

# Project Concerns

Project Concerns declare active cross-cutting context for downstream work. They
are not principles, requirements, ADRs, test plans, or implementation tasks.

Selected at genesis (2026-11-10) for Roadsmith, an interactive web app for
product managers that calls an LLM for roadmap drafting and critique. The
operator accepted the shipped stack defaults and kept the full seeded quality
list with no pruning. The Frame pass (2026-11-10, autonomy high) added `auth`,
`admin-console`, and `security-owasp` as recorded assumptions, based on the
PRD's account and workspace requirements.

**Source Applicability**: source; Roadsmith will be built as handwritten
TypeScript web application code. Module boundaries are defined in Design.

## Active Concerns

| Concern | Source | Areas | Why Active | Key Practices |
|---------|--------|-------|------------|---------------|
| react-nextjs | library; slot `frontend-framework`; `shipped-default` | `area:ui` | Roadsmith is an interactive web UI; the operator accepted the shipped frontend default | App Router; Server Components by default, `"use client"` only for interactive widgets (board, drag-to-sequence, editors); Tailwind `@theme` tokens |
| typescript-bun | library; slot `language-runtime`; `shipped-default` | `area:ui`, `area:api`, `area:data` | One language across UI, API, and AI orchestration; operator accepted the shipped runtime default | Strict TS (no `any`); one `zod` config schema in `env.ts`; `bun test` / `bun run` scripts |
| e2e-playwright | library; slot `e2e-framework`; `shipped-default` | `area:ui` | Core flows (capture → prioritize → sequence → AI draft) need real-browser proof | Run each core flow end to end; assert current-location cues; CI integration |
| auth-local-sessions | library; slot `auth-provider`; `shipped-default` | `area:api` | Roadmaps are private team data and need signed-in users; operator accepted the shipped default | One auth interface with a documented swap path to OIDC; salted, work-factored password hashing; HttpOnly server-side sessions |
| testing | library; `shipped-default` | `area:ui`, `area:api`, `area:data` | Software will be implemented; prioritization scoring and dependency ordering need deterministic tests | Tests cite requirement IDs; property-based tests for scoring and dependency ordering; real-path harness per surface |
| verification | library; `shipped-default` | all | Completion claims need run evidence | Plan the proof up front; completion contract with run references |
| modularity-and-encapsulation | library; `shipped-default` (source-code baseline) | all | Handwritten source; the prioritization, sequencing, and AI-drafting domains must stay separable | Architecture records a module boundary map, integration owners, construction policy, and a project-local boundary check command |
| code-shape-ceilings | library; `shipped-default` | all | Handwritten source from the first commit | Function, class, and file size ceilings enforced by lint and a file-size script; ceilings only go down |
| scope-discipline | library; `shipped-default` | all | Buildable product; guards against hollow or padded work items | Scope gate before claiming done; verify against the boundary, not the summary |
| o11y-otel | library; `shipped-default` | `area:api`, `area:ui` | Running web service plus LLM calls whose latency, cost, and failures need diagnosis | Applicable sections: service signals, frontend boundaries, GenAI operations (model call spans, token usage). Agent run diagnostics apply to development runners only |
| a11y-wcag-aa | library; `shipped-default` | `area:ui` | Interactive web UI; roadmap boards and drag interactions must work by keyboard and with screen readers | WCAG 2.x AA; keyboard alternatives to drag-and-drop; accessibility checks in testing |
| ux-interaction-patterns | library; `shipped-default` | `area:ui` | Interactive UI baseline independent of the component library | Shared interaction behavior for forms, dialogs, lists, and boards; UX quality gates |
| auth | library; `assumption` (Frame, autonomy high) | `area:api`, `area:data`, `area:ui` | Account-based product: workspaces own roadmaps and only members may read or change them (PRD FR-18, FR-19) | A real principal can sign up, sign in, and act only within their workspace; session issuance, rotation, and revocation; isolation enforced server-side through the principal |
| admin-console | library; `assumption` (Frame, autonomy high) | `area:ui`, `area:api` | The PM manages mutable domain objects (items, goals, dependencies, periods) and lifecycle state (period close) through the UI | CRUD over every core domain object and its lifecycle actions is usable from the UI; the primary PM workflow runs end to end through the UI |
| security-owasp | library; `assumption` (Frame, autonomy high) | `area:api`, `area:ui` | Accounts, invitations, and confidential roadmap content sent to an LLM need hardening | OWASP-aligned input validation, session and credential handling, and treatment of LLM output as untrusted input |
| datastore-postgresql | project-local; slot `datastore`; `operator-override` via [concerns.local.yml](concerns.local.yml); [concern](concerns/datastore-postgresql/concern.md), [practices](concerns/datastore-postgresql/practices.md) | `area:data`, `area:api`, `area:infra` | The operator chose PostgreSQL on 2026-11-10. HELIX has no library filler for this slot | Migrations only through the project command; every roadmap query scoped to the principal's workspace; integration tests against real PostgreSQL; an exercised restore |
| relational-data-modeling | library; `shipped-default` (relational storage signal) | `area:data` | Items, scores, dependency edges, periods, and workspaces are stored relationally in PostgreSQL | 3NF/BCNF by default; declared foreign keys with deliberate referential actions; constraints in the schema; indexes justified by access patterns; versioned, reversible migrations |
| model-gateway | library; provider Anthropic Claude (`operator-override`, 2026-11-10) | `area:api`, `area:infra` | AI-assisted drafting and critique call Anthropic Claude; the product names models by alias, not by vendor endpoint | Alias set with an owner; per-environment alias-to-route resolution; deploy gate with one call per alias; evals rerun on any alias rebind; retry ownership in one layer |

**Pending selections** (resolved in Frame/Design, not at genesis):

- `architecture-style` slot: no default; select on signal during Design.
- `deploy-target` slot: undecided; open question for Design.

## Project Overrides

No overrides at genesis.

| Concern | Practice | Override | Authority |
|---------|----------|----------|-----------|
| — | — | — | — |

## Area Labels

This project uses the following area labels for concern scoping:

- `area:ui` — user-facing web interface (roadmap board, prioritization views, AI drafting panel)
- `area:api` — backend endpoints, auth, and LLM orchestration
- `area:data` — persistence of initiatives, scores, dependencies, and roadmaps
- `area:infra` — deployment, CI/CD, model gateway routes

## Concern Conflicts

| Conflict | Resolution |
|----------|------------|
| react-nextjs vs. typescript-bun | Known composed-concern friction is documented in both practices files; follow the react-nextjs guidance for the web app build and typescript-bun for scripts, tests, and config |

## Observability Applicability

`o11y-otel` applies to service signals for the API, frontend boundaries for
the web UI, and GenAI operations for LLM drafting and critique calls. Agent run
diagnostics apply only to development runners.

## Formal Methods Applicability

`formal-methods` is not selected.
