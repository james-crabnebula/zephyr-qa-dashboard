---
title: "Settings Test Report"
date: 2026-02-09
category: features
tags: [settings, preferences, configuration]
author: James Barclay
status: complete
build_version: 0.1.4
---

# Settings — Area Report

| Field | Value |
|-------|-------|
| **Suite** | 02-settings |
| **Test IDs** | SET-02, SET-04, SET-06, SET-08, SET-09 |
| **Platforms Tested** | macOS ARM64, Windows 11 |
| **Sessions** | Alex (macOS, Day 1), Cristian (macOS, Day 1 + Day 2), Robin (Win11, Day 2) |

---

## Suite Results

| Metric | Value |
|--------|-------|
| Total steps | 127 |
| Passed | 105 |
| Failed | 13 |
| Blocked | 1 |
| N/A | 8 |
| **Pass rate** | **88.2%** |

### Per-Session Breakdown

| Session | Steps | Pass | Fail | Blocked | N/A |
|---------|-------|------|------|---------|-----|
| Alex (macOS, Day 1) | 22 | 19 | 2 | 0 | 1 |
| Cristian (macOS, Day 1) | 22 | 21 | 0 | 0 | 1 |
| Cristian (macOS, Day 2) | 22 | 21 | 0 | 0 | 1 |
| Robin (Win11, Day 2) | 61 | 44 | 11 | 1 | 5 |

Windows 11 accounts for 11 of 13 failures. macOS sessions were largely clean. Three HIGH issues affect Windows exclusively.

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

### SET-04 — Dark theme makes sidebar illegible (white text on white)

| Field | Value |
|-------|-------|
| **Severity** | `HIGH` |
| **Test ID** | SET-04 |
| **Platforms** | Windows 11 |
| **Found by** | Robin van Boven |

**What's broken:** The dark theme does not apply correctly. The sidebar becomes white text on a white background, making it unreadable. A Ctrl+R reload temporarily fixes it.

**Tester evidence:**
- Robin: *"Dark theme does not properly apply. Particularly the sidebar becomes white-on-white and hard to use. Using Ctrl+R (Windows) to reload helps temporarily."*

**Cross-platform:** Windows 11 only. Alex noted a related light mode sidebar issue on macOS but not as severe.

#### Investigation Prompt

```
Search for theme switching logic and CSS custom properties (CSS variables) for
dark mode. On Windows 11, toggling to dark theme causes the sidebar to become
white text on white background — completely illegible. Ctrl+R reload
temporarily fixes it.

Check:
1. Theme toggle handler — when the user switches theme, how are CSS variables
   updated? Look for a theme store/signal and the mechanism that applies the
   class or data attribute to the root element.
2. Sidebar component styles — check if the sidebar has hardcoded colours that
   override theme variables, or if it reads from a different set of variables
   than the main content area.
3. Timing issue — the fact that Ctrl+R fixes it suggests the theme class is
   applied but the sidebar component doesn't re-render its styles. Look for
   a SolidJS reactivity gap where the sidebar reads the theme value at mount
   time only.
4. Platform-specific rendering — this is Windows-only, so check if there is
   any platform-specific CSS or if the Tauri webview on Windows handles CSS
   custom properties differently.

Expected: Dark theme applies consistently to all components including sidebar,
without requiring a page reload.
```

#### Test

Write regression tests that assert:
- Toggling to dark theme updates all sidebar text and background colours immediately — no element remains in light-theme colours.
- Toggling back to light theme restores all colours correctly.
- Theme state persists across navigation (switching pages does not reset the theme).
- No reload (Ctrl+R) is required for the theme to take full effect.

#### Validate

- [ ] Toggle to dark theme on Windows 11. Sidebar text is readable (light on dark).
- [ ] Toggle back to light theme. Sidebar returns to normal.
- [ ] Navigate between sections. Theme remains consistent.
- [ ] No Ctrl+R required.

#### Checkpoint

Theme switching works on all platforms without reload. Sidebar colours are correct in both themes. No regression in other themed components.

---

### SET-09 — GitHub token settings link throws ACL error

| Field | Value |
|-------|-------|
| **Severity** | `HIGH` |
| **Test ID** | SET-09 |
| **Platforms** | Windows 11 |
| **Found by** | Robin van Boven |

**What's broken:** Clicking the GitHub Token Settings link in the settings panel does nothing. Console shows: `Uncaught (in promise) Command plugin:shell|open not allowed by ACL`.

**Tester evidence:**
- Robin: *"Clicking the GitHub Token Settings link does not respond. The console shows error: Uncaught (in promise) Command plugin:shell|open not allowed by ACL."*

**Cross-platform:** Windows 11 only. May affect other platforms but not tested there for this specific action.

#### Investigation Prompt

```
Search for Tauri ACL configuration and the shell plugin permissions. Clicking
"GitHub Token Settings" in the settings page throws:
"Command plugin:shell|open not allowed by ACL"

Check:
1. Tauri capability/permission files — look in src-tauri/capabilities/ or
   tauri.conf.json for the shell plugin ACL. The "shell:allow-open" permission
   must be granted for the app to open external URLs.
2. The specific command call — search for shell.open or invoke('plugin:shell|open')
   in the frontend code. This is the Tauri v2 pattern for opening URLs in the
   default browser.
3. Capability scope — if the permission exists, check if it has a URL scope
   restriction that excludes the GitHub settings URL.
4. Platform-specific capabilities — Tauri v2 allows per-platform capability
   files. Check if Windows has a different or missing capability set.

Fix: Add "shell:allow-open" to the appropriate capability file, or expand the
scope to include the GitHub URL pattern. Example:
  { "identifier": "shell:allow-open", "allow": [{ "url": "https://github.com/**" }] }
```

#### Test

Write regression tests that assert:
- Clicking the GitHub Token Settings link opens the expected URL in the default browser without errors.
- No ACL errors appear in the console when clicking any external link in the settings page.
- The shell:open permission is present in the capability configuration for all platforms.

#### Validate

- [ ] Click "GitHub Token Settings" on Windows 11. Default browser opens with the correct URL.
- [ ] No console errors.
- [ ] Test any other external links in settings — all should open without ACL errors.

#### Checkpoint

All external links in settings open correctly on all platforms. Tauri ACL permits shell:open for required URL patterns. No regression in other shell plugin functionality.

---

### SET-08 — Package Manager: Registry unavailable, no packages listed

| Field | Value |
|-------|-------|
| **Severity** | `HIGH` |
| **Test ID** | SET-08 |
| **Platforms** | Windows 11 |
| **Found by** | Robin van Boven |

**What's broken:** The Browse tab in the Package Manager says "Registry Unavailable" and returns no results. The package management feature is completely non-functional.

**Tester evidence:**
- Robin: *"Browse tab says Registry Unavailable and returns no results."*

**Cross-platform:** Windows 11 only. Not specifically tested on macOS for this flow.

#### Investigation Prompt

```
Search the codebase for the package manager registry integration and the
"Registry Unavailable" error message. The Browse tab shows this message and
returns no package results.

Check:
1. Registry URL configuration — where is the package registry URL defined?
   Is it hardcoded, in a config file, or fetched from a remote config? It may
   be pointing to a development/staging URL that is not accessible.
2. Network request — find the API call that fetches the package registry. Check
   if it is making the request at all (network tab / Tauri devtools) and what
   response it receives (404, 500, CORS error, timeout).
3. Error handling — search for the "Registry Unavailable" string to find the
   component that renders it. Trace back to see what condition triggers this
   state — is it a catch block, a null response, or a feature flag?
4. Authentication — does the registry API require an auth token? If the
   package registry is behind authentication, check if the token is being
   sent in the request headers.

Expected: Browse tab fetches and displays available packages from the registry.
```

#### Test

Write regression tests that assert:
- The package registry API is reachable and returns a valid response.
- The Browse tab displays at least one package when the registry is available.
- When the registry is genuinely unavailable, the error message is clear and includes guidance (not just "Registry Unavailable").

#### Validate

- [ ] Open Package Manager on Windows 11. Browse tab shows packages.
- [ ] If registry is intentionally not yet deployed, the UI clearly indicates this is a known limitation.

#### Checkpoint

Package manager connects to registry and displays packages, or clearly communicates the feature's availability status. No regression in other settings functionality.

---

### SET-06 — Billing/Plan section entirely mocked with non-functional controls

| Field | Value |
|-------|-------|
| **Severity** | `MEDIUM` |
| **Test ID** | SET-06 |
| **Platforms** | Windows 11 |
| **Found by** | Robin van Boven |

**What's broken:** The Billing/Plan section displays placeholder data throughout. Change plan button, billing method, usage metrics, next billing date, and invoices are all mock data. Clicking "view" on invoices shows a toast but no actual invoice.

**Tester evidence:**
- Robin: *"FAILs because all information is mocked. Clicking view on invoices shows a toast, but no invoice."*

**Cross-platform:** Windows 11 only tested. Likely the same on all platforms (mock data is a code issue, not platform-specific).

#### Investigation Prompt

```
Search for the billing/plan settings component and any mock data or placeholder
values. The entire Billing/Plan section shows non-functional mock data:
placeholder plan name, fake usage metrics, fake billing date, and invoice
"view" buttons that show a toast but no content.

Check:
1. Is this intentionally mocked for the nightly? Look for TODO comments,
   feature flags, or environment checks that gate the billing integration.
2. If billing is not yet implemented, the UI should clearly indicate this
   (e.g., "Coming soon" or hide the section entirely) rather than displaying
   mock data that appears real but does nothing.
3. Search for the invoice view handler — it shows a toast on click, suggesting
   there is a handler that catches the missing functionality.

This may be a known work-in-progress. If so, consider hiding the section from
the nightly build or adding a clear "Not yet available" banner.
```

#### Test

Write a test that asserts either: (a) the billing section displays real data from the billing API, or (b) if billing is not yet implemented, the section is hidden or clearly marked as "Coming soon" — not presented as functional with mock data.

#### Validate

- [ ] Billing section either shows real data or is clearly marked as unavailable.
- [ ] No mock/placeholder data presented as if it were real.

#### Checkpoint

Billing section accurately represents its implementation status. No user confusion about whether billing is functional.

---

### SET-02 — Settings section count mismatch (10 vs expected 9)

| Field | Value |
|-------|-------|
| **Severity** | `LOW` |
| **Test ID** | SET-02 |
| **Platforms** | Windows 11 |
| **Found by** | Robin van Boven |

**What's broken:** Settings page shows 10 sections instead of the expected 9. The additional section is "Language".

**Tester evidence:**
- Robin: *"Also includes Language."*

**Cross-platform:** Windows 11 tested. Alex also noted this on macOS (2 failures on settings steps).

#### Investigation Prompt

```
Search for the settings page section definitions. The settings page shows 10
sections including a "Language" section that is not in the specification
(expected 9 sections). Check if the Language section was added intentionally
and the specification needs updating, or if it should be hidden/removed.

This is low priority — likely just a spec-vs-implementation drift rather than
a bug.
```

#### Test

Write a test that asserts the settings page section count matches the specification. If Language was intentionally added, update the spec and the test together.

#### Validate

- [ ] Settings page section count matches the current specification.

#### Checkpoint

Spec and implementation agree on settings section list. No orphaned or undocumented sections.

---

*5 issues in Settings. 3 HIGH (theme, ACL, registry), 1 MEDIUM (mock billing), 1 LOW (section count). Screenshots: see `robin-win11-6feb/02-settings-media/` (8 images).*
