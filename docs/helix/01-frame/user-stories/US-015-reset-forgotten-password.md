---
ddx:
  id: US-015
  authoring:
    home: repo
  depends_on:
    - FEAT-005
---

# US-015: Reset a Forgotten Password

**Feature**: FEAT-005 — Accounts and Workspaces
**Feature Requirements**: ACC-11, ACC-12, ACC-13
**PRD Requirements**: FR-20
**Priority**: P1

## Story

**As a** Maya, the sole PM at a 12-person B2B SaaS company
**I want** to reset my password from the sign-in page when I've forgotten it
**So that** I can get back into my roadmap before the leadership review
without anyone else being able to take over my account.

## Context

Maya signs in to Roadsmith a few times a quarter, mostly around planning. A
forgotten password the day before a review must not lock her out of
`Q1 2027 Plan`. Reset must not reveal which email addresses have accounts, and
it must end sessions that might belong to someone who learned the old
password. This story depends on the outbound email service that FR-19
invitations also use (US-014).

## Walkthrough

Testers need an inbox they can read for `maya@northwind.example` (a test mail
catcher is fine). The account exists with password `plum-river-42!`, and Maya
is also signed in on a second browser.

1. Maya opens the sign-in page while signed out and chooses "Forgot password".
2. Roadsmith asks for an email address. Maya enters `maya@northwind.example`
   and submits.
3. Roadsmith shows a confirmation that says a link is on its way if an account
   exists for that address.
4. An email arrives at `maya@northwind.example` with a reset link.
5. Maya opens the link. Roadsmith asks for a new password.
6. Maya enters `cedar-harbor-77!` and submits.
7. Roadsmith signs Maya in and shows `Maya's workspace` listing `Q1 2027 Plan`.
8. In the second browser, Maya's earlier session is directed to sign in on its
   next request.
9. Maya opens the same reset link again. Roadsmith says the link is no longer
   valid.

## Acceptance Criteria

- [ ] **US-015-AC1** — Given an account exists for `maya@northwind.example`,
  when a reset is requested for that address, then one email with a reset link
  is sent to that address.
- [ ] **US-015-AC2** — Given no account exists for `nobody@northwind.example`,
  when a reset is requested for that address, then the on-screen response is
  identical to the response for an existing account.
- [ ] **US-015-AC3** — Given no account exists for `nobody@northwind.example`,
  when a reset is requested for that address, then no email is sent.
- [ ] **US-015-AC4** — Given Maya opens an unused reset link sent less than 60
  minutes ago, when she sets a 14-character password, then she is signed in
  with the new password.
- [ ] **US-015-AC5** — Given Maya has reset her password, when she signs in
  with the old password, then sign-in is refused.
- [ ] **US-015-AC6** — Given Maya has another active session in a second
  browser, when she completes a reset, then that other session is directed to
  sign in on its next request.
- [ ] **US-015-AC7** — Given a reset link has already been used, when it is
  opened again, then Roadsmith reports the link is no longer valid and allows
  no password change.
- [ ] **US-015-AC8** — Given a reset link was sent 61 minutes ago, when it is
  opened, then Roadsmith reports the link is no longer valid and allows no
  password change.
- [ ] **US-015-AC9** — Given Maya opens a valid reset link, when she submits
  an 8-character password, then the reset is refused with a message stating
  the 12-character minimum and the link stays usable.

## Edge Cases

- **Two reset requests in a row**: both links work until one is used. Using
  either one invalidates the other (assumption; consistent with single-use
  links in ACC-11).
- **Email service unavailable**: the on-screen response is unchanged, so
  account existence still is not revealed. The failure is recorded for
  operators.
- **Reset while locked out by sign-in throttling**: a successful reset lifts
  the sign-in lockout for that account.
- **Malformed address**: `maya@` is refused before submission, as on sign-up.

## Test Scenarios

| Scenario | AC ID | Input / State | Action | Expected Result |
|----------|-------|---------------|--------|-----------------|
| Reset email sent | US-015-AC1 | Account `maya@northwind.example` exists | Request reset for `maya@northwind.example` | Exactly 1 email to that address containing a reset link |
| No enumeration on screen | US-015-AC2 | No account for `nobody@northwind.example` | Request reset for it and for Maya's address | On-screen responses are identical text |
| No email for unknown address | US-015-AC3 | No account for `nobody@northwind.example` | Request reset | Mail catcher receives 0 emails |
| Successful reset | US-015-AC4 | Link sent at 10:00, unused | Open at 10:30; set `cedar-harbor-77!` | Signed in; `Maya's workspace` shown |
| Old password rejected | US-015-AC5 | Reset done | Sign in with `plum-river-42!` | Refused with the standard sign-in error |
| Other sessions ended | US-015-AC6 | Second browser signed in as Maya | Complete reset in first browser; reload second | Second browser directed to sign in |
| Single-use link | US-015-AC7 | Link used at 10:30 | Open same link at 10:31 | "No longer valid"; no password form |
| Expired link | US-015-AC8 | Link sent at 10:00, unused | Open at 11:01 | "No longer valid"; no password form |
| Weak new password | US-015-AC9 | Valid link | Submit `short123` | Refused; 12-character minimum named; link still opens the form |

## Dependencies

- **Stories**: US-013 (accounts and sign-in exist); shares the email
  dependency with US-014.
- **Feature Spec**: FEAT-005 — Accounts and Workspaces
- **Feature Requirements**: ACC-11, ACC-12, ACC-13
- **PRD Requirements**: FR-20
- **External**: an outbound email service (selected in Design).

## Out of Scope

- Changing a password while signed in, and changing the account email.
- Multi-factor authentication and recovery codes.
- Reset by SMS or security questions.
