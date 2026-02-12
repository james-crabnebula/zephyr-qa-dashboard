---
title: "Authentication Test Report"
date: 2026-02-09
category: features
tags: [auth, oauth, login, auth0]
author: James Barclay
status: complete
build_version: 0.1.4
---

# Authentication — Area Report

| Field | Value |
|-------|-------|
| **Suite** | 01-authentication |
| **Test IDs** | AUTH-01 through AUTH-04 |
| **Platforms Tested** | macOS ARM64, Windows 11 |
| **Sessions** | Alex (macOS, Day 1), Cristian (macOS, Day 1 + Day 2), Robin (Win11, Day 2) |

---

## Suite Results

| Metric | Value |
|--------|-------|
| Total steps | 108 |
| Passed | 89 |
| Failed | 7 |
| Blocked | 8 |
| N/A | 4 |
| **Pass rate** | **85.6%** |

### Per-Session Breakdown

| Session | Steps | Pass | Fail | Blocked | N/A |
|---------|-------|------|------|---------|-----|
| Alex (macOS, Day 1) | 23 | 23 | 0 | 0 | 0 |
| Cristian (macOS, Day 1) | 23 | 18 | 4 | 0 | 1 |
| Cristian (macOS, Day 2) | 23 | 20 | 1 | 0 | 2 |
| Robin (Win11, Day 2) | 39 | 28 | 2 | 8 | 1 |

Alex had zero auth failures on macOS. Cristian and Robin both encountered issues — Cristian found the security defect (AUTH-01), Robin found the flow and OAuth issues.

---

## How to Use This Report

Each issue below follows a structured remediation workflow:

```
PRE-FLIGHT → INVESTIGATE → FIX → TEST → VALIDATE → CHECKPOINT
```

**For each issue:**

1. **PRE-FLIGHT** — Reproduce the bug on the affected platform. Confirm you see the described behaviour.
2. **INVESTIGATE** — Copy the investigation prompt (fenced code block) into your AI coding tool (Claude, Cursor, Copilot). It will guide your search through the codebase.
3. **FIX** — Implement the changes identified during investigation.
4. **TEST** — Write a regression test covering the fix. Each issue includes what to assert so the bug cannot silently return.
5. **VALIDATE** — Verify the fix against the validation criteria listed for the issue.
6. **CHECKPOINT** — Confirm no regressions in related functionality and mark the issue resolved.

Work through issues in severity order: CRITICAL and HIGH first.

---

## Issues

### AUTH-01 — Unauthenticated users can access and post content

| Field | Value |
|-------|-------|
| **Severity** | `CRITICAL` |
| **Test ID** | AUTH-01 |
| **Platforms** | macOS ARM64, Windows 11 |
| **Found by** | Cristian Vogel (Day 1, confirmed Day 2), Robin van Boven (Win11) |

**What's broken:** Before signing in, channel content is fully visible and readable. Unauthenticated users can post chat messages which appear attributed to "Anonymous User".

**Tester evidence:**
- Cristian (Day 1): *"Channels visible, content readable, chat post is accepted and appears as Anonymous User."*
- Cristian (Day 2): *"Unauth can do everything except see the manage and settings."*
- Robin (Win11): *"Channel content is visible before authentication."*

**Cross-platform:** Confirmed on macOS ARM64 and Windows 11. Not tested on Linux (app doesn't launch).

#### Investigation Prompt

```
Search the codebase for authentication guards and route protection. The issue
is that unauthenticated users can view all channel content AND post chat
messages as "Anonymous User" before signing in.

Check the following:
1. Frontend route guards — are all routes wrapped in an auth check? Look for
   a SolidJS router guard or middleware that should redirect to login.
2. Content rendering — does the chat/channel component fetch and display data
   before auth state is confirmed? There may be a race condition where content
   renders before the auth check completes.
3. Backend/API — is the message posting endpoint protected? An unauthenticated
   POST should be rejected server-side, not accepted with an "Anonymous User"
   attribution.
4. WebSocket connections — if chat uses WebSockets, check whether the
   connection is established without a valid auth token.

Expected behaviour: No content visible until authentication is confirmed. POST
requests from unauthenticated clients should be rejected. The app should show
only the login/signup screen before auth completes.
```

#### Test

Write regression tests that assert:
- An unauthenticated user sees only the login/signup screen — no channel content, no chat messages, no sidebar navigation to content areas.
- An unauthenticated POST to the message endpoint is rejected (not accepted as "Anonymous User").
- The auth guard redirects to login before any content component mounts or fetches data.

These tests should fail against the current code and pass after the fix.

#### Validate

- [ ] Launch the app without signing in. No channel content is visible.
- [ ] Attempt to post a message while unauthenticated. It is rejected.
- [ ] Sign in. Content becomes visible. Messages post with the authenticated user's identity.
- [ ] Test on both macOS and Windows 11.

#### Checkpoint

Auth guard protects all content routes. No content renders before authentication completes. Server rejects unauthenticated requests. No regression in the authenticated user experience.

---

### AUTH-03 — Auth flow: 403 redirect, spam folder confirmation, browser window leak

| Field | Value |
|-------|-------|
| **Severity** | `HIGH` |
| **Test ID** | AUTH-03 |
| **Platforms** | Windows 11 |
| **Found by** | Robin van Boven |

**What's broken:** The email/password signup flow has multiple friction points: the confirmation email lands in spam, the password policy is incompatible with 1Password's default generator (requires special characters), completing the flow redirects to a 403 page, and the Auth0 browser window is left open after the callback.

**Tester evidence:**
- Robin: *"Initial signup sent a confirmation code to spam folder. Choosing a password has a policy incompatible with 1password default generator (requiring special characters). With a password set, redirected to a 403 page. Had to restart the authentication flow from the Zephyr Agency app. It leaves the browser window open after the code is passed back to the app."*

**Cross-platform:** Only tested on Windows 11. Auth flow may behave differently on macOS (Alex had zero auth failures). Cristian used a different auth path.

#### Investigation Prompt

```
Search the codebase for the Auth0 integration and callback handling. Multiple
issues in the email/password signup flow on Windows 11:

1. 403 redirect after password set — check the Auth0 callback URL configuration.
   The redirect URI may be misconfigured or the callback handler may not be
   properly registered for Windows. Look for platform-specific URL scheme
   handling in the Tauri configuration (tauri.conf.json or Cargo.toml deep link
   config).
2. Browser window not closing — after Auth0 completes the callback and sends
   the code back to the app, the browser window stays open. Search for the
   Auth0 authorize redirect flow and check whether there is a post-callback
   page or script that closes the window (window.close() or a redirect to a
   "you can close this" page).
3. Spam folder — this is likely an Auth0/email provider configuration issue,
   not a code issue. Check if Auth0 email templates use a verified sender domain.
4. Password policy — review Auth0 tenant password policy settings. The policy
   requires special characters which conflicts with common password manager
   defaults. Consider relaxing to NIST 800-63B recommendations (length over
   complexity).

Expected: Signup completes without 403, browser window closes after callback,
confirmation email reaches inbox.
```

#### Test

Write regression tests that assert:
- The Auth0 callback URL is correctly handled on all platforms — the redirect completes without a 403.
- After the auth callback, the browser window is closed (or the user is shown a "you can close this" page).
- The full signup flow (email, confirmation, password, login) completes end-to-end without manual intervention beyond checking email.

#### Validate

- [ ] Complete email/password signup on Windows 11. No 403 redirect.
- [ ] Auth0 browser window closes after callback.
- [ ] Confirmation email arrives in inbox (not spam) — this requires Auth0 config, not a code fix.
- [ ] Password can be set using a password manager's default generator.

#### Checkpoint

Email/password signup flow completes without errors on Windows 11. Browser window cleaned up after auth callback. No regression in existing OAuth flows.

---

### AUTH-04 — Google OAuth window unresponsive during auth flow

| Field | Value |
|-------|-------|
| **Severity** | `HIGH` |
| **Test ID** | AUTH-04 |
| **Platforms** | macOS ARM64, Windows 11 |
| **Found by** | Cristian Vogel (macOS), Robin van Boven (Win11) |

**What's broken:** The Google OAuth popup via Auth0 becomes unresponsive. The progress bar animates but the window never completes the flow. The app refocuses but remains on the lock screen.

**Tester evidence:**
- Cristian (Day 1): *"Auth0 window (Google) is unresponsive, progress bar animating. Does not close. App is focussed again, but is not past the unauth lock screen."*
- Robin (Win11): Blocked on Google OAuth entirely, used email/password workaround.

**Cross-platform:** Confirmed broken on both macOS ARM64 and Windows 11.

#### Investigation Prompt

```
Search the codebase for Google OAuth / Auth0 social connection handling. The
Google OAuth popup becomes unresponsive on both macOS and Windows — progress
bar animates indefinitely, window never closes, app stays on lock screen.

Check:
1. Auth0 Google social connection configuration — is the Google OAuth client
   ID and redirect URI correctly set? The popup may be failing silently.
2. Token exchange — after Google auth completes, Auth0 should redirect back
   to the app. Check if the redirect URI scheme is being intercepted by Tauri's
   deep link handler. Look for tauri-plugin-deep-link or custom URL scheme
   registration.
3. PKCE flow — if using Authorization Code + PKCE, verify the code_verifier
   and code_challenge are being generated and stored correctly across the
   popup window lifecycle.
4. Window focus handling — the app refocuses but stays on the lock screen,
   suggesting the auth callback was received but the token was not properly
   extracted or stored. Check the auth state management after callback.

Expected: Google OAuth popup completes, closes, and the app transitions past
the lock screen to the authenticated state.
```

#### Test

Write regression tests that assert:
- Google OAuth flow completes and the app transitions to the authenticated state.
- The OAuth popup window closes after the auth callback.
- The auth token is correctly stored and the lock screen is dismissed.
- Timeout handling: if OAuth takes longer than a reasonable threshold, the user sees an error rather than an indefinite spinner.

#### Validate

- [ ] Sign in via Google OAuth on macOS. Popup completes, closes, app shows authenticated state.
- [ ] Sign in via Google OAuth on Windows 11. Same result.
- [ ] If OAuth fails, user sees a clear error message, not an unresponsive window.

#### Checkpoint

Google OAuth completes on both macOS and Windows. No hanging popups. Auth state transitions correctly after callback. No regression in email/password auth.

---

### AUTH-02 — Sign-in dialog offset by collapsed sidebar position

| Field | Value |
|-------|-------|
| **Severity** | `MEDIUM` |
| **Test ID** | AUTH-02 |
| **Platforms** | Windows 11 |
| **Found by** | Robin van Boven |

**What's broken:** The sign-in modal appears offset from centre, shifted by the width of where the collapsed sidebar would be. The layout calculation doesn't account for the sidebar being hidden on the auth screen.

**Tester evidence:**
- Robin: *"Probably intended, but clicking Sign In does not directly go to browser. Instead showing these options. Note it is offset by where the sidebar would have been."*

**Cross-platform:** Windows 11 only. Not observed on macOS.

#### Investigation Prompt

```
Search for the sign-in dialog/modal component and its positioning CSS. On
Windows 11, the sign-in dialog appears offset to the right, as if the layout
is reserving space for the collapsed sidebar even though the sidebar should be
hidden on the unauthenticated screen.

Check:
1. The modal's CSS positioning — is it centred relative to the viewport or
   relative to a main content area that accounts for sidebar width?
2. Sidebar state during auth — is the sidebar component mounted but hidden
   (taking up layout space) on the lock/auth screen? It should be unmounted
   or have display:none rather than visibility:hidden or width:0.
3. Layout grid/flex — if using CSS Grid or Flexbox for the app shell, check
   whether the sidebar column is present in the grid template when the user
   is unauthenticated.

Expected: Sign-in dialog centred in the full window, with no offset from
sidebar layout.
```

#### Test

Write a test that asserts the sign-in dialog is horizontally centred in the window when no user is authenticated. The dialog's left offset should be symmetric with its right offset relative to the viewport.

#### Validate

- [ ] Launch app on Windows 11. Sign-in dialog appears centred in the window.
- [ ] No visible offset from sidebar layout space.

#### Checkpoint

Sign-in dialog centred on all platforms. No regression in sidebar behaviour after authentication.

---

*4 issues in Authentication. 1 CRITICAL (security), 2 HIGH (flow broken), 1 MEDIUM (layout). Screenshots: see `robin-win11-6feb/01-authentication-media/` (8 images).*
