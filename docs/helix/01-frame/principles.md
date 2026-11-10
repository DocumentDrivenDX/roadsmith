---
ddx:
  id: principles
  authoring:
    home: repo
---

# Project Principles

These principles guide judgment calls across all HELIX activities. They are not
requirements, concerns, ADRs, workflow rules, or process enforcement. They are
lenses applied when choosing between two valid options.

This document was bootstrapped from HELIX defaults during the Frame pass on
2026-11-10, at autonomy `high`. Principle 8 and the Experience Principles are
**assumptions** inferred from the product vision and PRD. The operator was
not interviewed. Confirm, edit, or remove any of them. The only constraint:
principles cannot negate HELIX mechanics (artifact hierarchy, activity gates,
tracker semantics).

## Principles

1. **Design for change** — Prefer structures that are easy to modify over
   structures that are easy to describe today. This changes decisions when a
   tidy short-term model would make likely product changes expensive.

2. **Design for simplicity** — Start with the minimal viable approach.
   Additional complexity requires justification. This changes decisions when a
   generalized solution has no current requirement behind it.

3. **Validate your work** — Every change should be verified through the most
   appropriate means available (tests, type checks, manual verification). This
   changes decisions when speed and evidence pull against each other.

4. **Make intent explicit** — Code, configuration, and documentation should
   make the *why* visible, not just the *what*. This changes decisions when an
   implicit convention would save words but hide rationale.

5. **Prefer reversible decisions** — When uncertain, choose the option that
   is easiest to undo or change later. This changes decisions when confidence
   is low and both options satisfy current requirements.

6. **Spec is the contract** — The governing artifact stack is the source of
   truth; code is a projection of it. Compare and reproduce from the spec, and
   keep traceability bidirectional (no material code surface without a governing
   artifact; no acceptance criterion without an exercising test). This changes
   decisions when code and spec diverge — fix the projection or update the
   contract in the same change, rather than letting them drift.

7. **Deliverable over machinery** — Ship the product unit; process machinery is
   a derivative (bound it, freeze it, deliver). Deferred rigor is a tracker or
   parking-lot item, not a redesign tranche. Do not skip tests, product ACs,
   claims-vs-reality, or scope-discipline. When process rounds outrun delivered
   units, stop and deliver.

8. **Deterministic core, AI at the edge** *(assumption)* — Scoring, ranking,
   sequencing, and plan checks are plain, testable code. The LLM only
   proposes changes, and that core validates every proposal. This changes
   decisions whenever a feature could be built "by asking the model": build
   the deterministic rule first, and add AI only to draft or critique on top
   of it.

## Experience Principles

1. **Show the reason** *(assumption)* — Every rank, placement, and violation
   shows why it is so: score inputs and evidence, blockers, and capacity
   used. This changes decisions when a cleaner screen would hide the inputs
   behind a number.

2. **The PM decides** *(assumption)* — AI output appears as proposals and
   diffs that the PM accepts or rejects. Nothing the AI produces changes the
   roadmap without an explicit accept. This changes decisions when an
   auto-apply would be faster.

3. **Useful in one sitting** *(assumption)* — Prefer defaults and inference
   (column mapping, horizon periods, auto-sequencing) over upfront
   configuration. This changes decisions when a setup step could be
   replaced by a sensible default the PM can edit later.

## Tension Resolution

When principles pull in opposite directions, document the resolution strategy
here. Each entry should name the two principles, describe when they conflict,
and state how to decide.

- **Deliverable over machinery vs. Validate your work**: keep real evidence;
  do not invent gates for completeness theater.
- **Deliverable over machinery vs. Spec is the contract**: product specs stay
  authoritative; freeze process meta-work mid-delivery, not required product ACs.
- **Design for simplicity vs. Deterministic core, AI at the edge**: an
  AI-only shortcut may look simpler. Choose the deterministic rule whenever
  the result must be trusted for a decision, such as a rank, a placement, or
  a violation. Accept AI-only behavior only for drafting text the PM reviews.
- **Useful in one sitting vs. The PM decides**: when a default is an AI
  proposal, show it as a proposal even during setup. Speed never removes the
  accept step for AI-originated changes.
