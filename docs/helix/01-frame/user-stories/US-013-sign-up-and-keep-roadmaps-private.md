---
ddx:
  id: US-013
  authoring:
    home: repo
  depends_on:
    - FEAT-005
---

# US-013: Sign Up and Keep Roadmaps Private

**Feature**: FEAT-005 — Accounts and Workspaces
**Feature Requirements**: ACC-01, ACC-02, ACC-03, ACC-04, ACC-05, ACC-06, ACC-07, ACC-08, ACC-09, ACC-10, ISO-01, ISO-02, ISO-03, ISO-04, ISO-06
**PRD Requirements**: FR-18
**Priority**: P0

## Story

**As a** Maya, the sole PM at a 12-person B2B SaaS company
**I want** to sign up and build my quarter's roadmap in a workspace only I
can reach
**So that** no other Roadsmith user can list, read, or change my
customer-confidential roadmap.

## Context

Maya plans one team's quarter and brings the roadmap to a leadership review.
The roadmap holds unannounced priorities, so Maya will not put it in Roadsmith
unless it stays private. This story is the P0 slice of FEAT-005: sign-up into
a personal workspace, sign-in and sign-out, and server-side workspace isolation
(PRD acceptance sketch P0-7). Invitations and shared workspaces are US-014.

## Walkthrough

Testers need two browsers (or one normal and one private window) and no
existing accounts for the two addresses below.

1. Maya opens Roadsmith while signed out and chooses to sign up.
2. Roadsmith shows a sign-up form asking for name, email address, and password.
3. Maya enters name `Maya`, email `maya@northwind.example`, and a 14-character
   password, then submits.
4. Roadsmith opens a workspace named `Maya's workspace` with an empty roadmap
   list. The members list shows Maya as owner.
5. Maya creates a roadmap named `Q1 2027 Plan` and adds item `Billing v2`.
6. Maya signs out. Roadsmith shows the sign-in page.
7. Maya pastes the address of `Q1 2027 Plan` into the browser. Roadsmith
   directs Maya to sign in and shows no roadmap content.
8. Maya signs in with `maya@northwind.example` and the same password.
   Roadsmith shows `Maya's workspace` listing `Q1 2027 Plan`.
9. In the second browser, a tester signs up as `Tom`,
   `tom@contoso.example`. Roadsmith opens `Tom's workspace` with an empty
   roadmap list.
10. The tester opens the address of `Q1 2027 Plan` copied from Maya's browser.
    Roadsmith shows the same not-found page it shows for a made-up roadmap
    address.
11. Maya reloads `Q1 2027 Plan` in the first browser. The roadmap and item
    `Billing v2` are unchanged.

## Acceptance Criteria

- [ ] **US-013-AC1** — Given no account exists for `maya@northwind.example`,
  when Maya completes sign-up with that address and a 14-character password,
  then Roadsmith opens a new workspace `Maya's workspace` with Maya listed as
  its owner.
- [ ] **US-013-AC2** — Given Maya is on the sign-up form, when Maya submits an
  8-character password, then sign-up is refused with a message stating the
  12-character minimum.
- [ ] **US-013-AC3** — Given an account exists for `maya@northwind.example`,
  when someone signs up with `MAYA@Northwind.example`, then sign-up is refused
  with an offer to sign in instead.
- [ ] **US-013-AC4** — Given Maya has signed out, when Maya signs in with the
  correct email address and password, then Roadsmith shows `Maya's workspace`
  listing `Q1 2027 Plan`.
- [ ] **US-013-AC5** — Given an account exists for `maya@northwind.example`,
  when a sign-in uses that address with a wrong password, then the error
  message is identical to the one shown for `nobody@northwind.example`, which
  has no account.
- [ ] **US-013-AC6** — Given 5 failed sign-in attempts for
  `maya@northwind.example` in the last 15 minutes, when a sixth attempt uses
  the correct password, then sign-in is refused with a message stating when
  to retry.
- [ ] **US-013-AC7** — Given Maya has signed out, when the address of
  `Q1 2027 Plan` is opened in that browser, then Roadsmith directs to sign-in
  without showing roadmap content.
- [ ] **US-013-AC8** — Given Maya owns `Q1 2027 Plan` and Tom is a member of
  `Tom's workspace` only, when Tom views the roadmap list, then
  `Q1 2027 Plan` is absent.
- [ ] **US-013-AC9** — Given Maya owns `Q1 2027 Plan` and Tom is a member of
  `Tom's workspace` only, when Tom opens the roadmap's address, then Tom sees
  the same not-found response as for a roadmap that does not exist.
- [ ] **US-013-AC10** — Given Maya owns `Q1 2027 Plan` and Tom is a member of
  `Tom's workspace` only, when Tom's session sends a change to item
  `Billing v2` directly to the server, then `Billing v2` is unchanged when
  Maya next opens the roadmap.
- [ ] **US-013-AC11** — Given Maya's session has had no activity for 14 days,
  when Maya opens Roadsmith, then Roadsmith directs Maya to sign in.
- [ ] **US-013-AC12** — Given Maya has signed up and signed in, when the
  stored account data, application logs, and telemetry are searched for the
  password text, then the password text is not found.
- [ ] **US-013-AC13** — Given Maya signed in 30 days ago and has used
  Roadsmith every day since, when Maya opens Roadsmith, then Roadsmith directs
  Maya to sign in.

## Edge Cases

- **Session ends mid-edit**: Maya's next change is refused and Maya is directed
  to sign in. Changes saved before the session ended remain.
- **Sign-up submitted twice**: A double-click on submit creates one account
  and one `Maya's workspace`.
- **Malformed email address**: `maya@` is refused before submission with a
  message asking for a complete email address.
- **Sign-in while already signed in**: Opening the sign-in page with a valid
  session takes Maya to the workspace instead.
- **Lockout expiry**: 15 minutes after the fifth failed attempt, a correct
  sign-in succeeds.

## Test Scenarios

| Scenario | AC ID | Input / State | Action | Expected Result |
|----------|-------|---------------|--------|-----------------|
| Happy-path sign-up | US-013-AC1 | No account for `maya@northwind.example` | Sign up with `Maya`, `maya@northwind.example`, password `plum-river-42!` | `Maya's workspace` opens, empty; Maya listed as owner |
| Short password | US-013-AC2 | Sign-up form | Submit password `short123` (8 chars) | Refused; message names 12-character minimum; no account created |
| Duplicate email, different case | US-013-AC3 | Account `maya@northwind.example` exists | Sign up with `MAYA@Northwind.example` | Refused; sign-in offered; still one account |
| Sign back in | US-013-AC4 | Maya signed out; roadmap `Q1 2027 Plan` exists | Sign in with `maya@northwind.example`, `plum-river-42!` | `Maya's workspace` lists `Q1 2027 Plan` |
| No account enumeration | US-013-AC5 | Account `maya@northwind.example` exists; none for `nobody@northwind.example` | Sign in as Maya with `wrong-pass-0000`; then as `nobody@northwind.example` | Both error messages are identical text |
| Throttled sign-in | US-013-AC6 | 5 wrong passwords for Maya at 10:00–10:04 | Correct password at 10:05 | Refused; message states retry after 10:19 |
| Throttle lifted | US-013-AC6 | Same as above | Correct password at 10:20 | Signed in |
| Signed-out access | US-013-AC7 | Maya signed out | Open `Q1 2027 Plan` address | Sign-in page; no roadmap content in the response |
| List isolation | US-013-AC8 | Tom in `Tom's workspace` only | Tom views roadmap list | Empty list; `Q1 2027 Plan` absent |
| Direct-read isolation | US-013-AC9 | Tom in `Tom's workspace` only | Tom opens `Q1 2027 Plan` address; then a made-up roadmap address | Both responses identical not-found |
| Direct-write isolation | US-013-AC10 | `Billing v2` Effort = 3; Tom signed in | Tom's session sends Effort = 8 for `Billing v2` to the server | Refused; Maya sees Effort = 3 |
| Idle expiry | US-013-AC11 | Maya's last activity 2026-11-01 09:00 | Open Roadsmith 2026-11-15 09:01 | Directed to sign in |
| Absolute expiry | US-013-AC13 | Maya signed in 2026-10-01 09:00; active daily | Open Roadsmith 2026-10-31 09:01 | Directed to sign in |
| Password never readable | US-013-AC12 | Maya signed up with `plum-river-42!` | Search stored account data, logs, and telemetry for `plum-river-42!` | Zero matches |

## Dependencies

- **Stories**: None. Roadmap and item creation in step 5 uses the create
  actions of the roadmap features; this story verifies only their isolation.
- **Feature Spec**: FEAT-005 — Accounts and Workspaces
- **Feature Requirements**: ACC-01–ACC-10, ISO-01–ISO-04, ISO-06
- **PRD Requirements**: FR-18
- **External**: None. The authentication interface Contract is defined in
  Design. No email delivery is needed.

## Out of Scope

- Inviting or removing teammates, and choosing among several workspaces
  (US-014).
- Password reset (FEAT-005 ACC-11 to ACC-13, P1).
- Email verification, multi-factor authentication, and single sign-on (SSO)
  or OpenID Connect (OIDC) sign-in.
- Account deletion and email address change.
