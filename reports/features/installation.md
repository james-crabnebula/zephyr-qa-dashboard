---
title: "Installation Test Report"
date: 2026-02-09
category: features
tags: [installation, setup, launch]
author: James Barclay
status: complete
build_version: 0.1.4
---

# Installation & Pre-Launch Setup — Area Report

| Field | Value |
|-------|-------|
| **Suite** | 00-pre-launch-setup |
| **Test IDs** | INST-04, INST-05, INST-06 |
| **Platforms Tested** | macOS ARM64, Windows 11, Ubuntu 24.04, Fedora 42, Pop!_OS 22.04 |
| **Sessions** | Alex (macOS, Day 1), Cristian (macOS, Day 1 + Day 2), Robin (Win11, Day 2), Robin (Linux x3, Day 1) |

---

## Suite Results

| Metric | Value |
|--------|-------|
| Total steps | 147 |
| Passed | 67 |
| Failed | 10 |
| Blocked | 1 |
| N/A | 69 |
| **Pass rate** | **85.9%** |

The high N/A count (69) is due to Linux sessions where the app would not launch — only installer-level steps could be tested.

### Per-Session Breakdown

| Session | Steps | Pass | Fail | Blocked | N/A |
|---------|-------|------|------|---------|-----|
| Alex (macOS, Day 1) | 21 | 9 | 6 | 1 | 5 |
| Cristian (macOS, Day 1) | 21 | 16 | 0 | 0 | 5 |
| Cristian (macOS, Day 2) | 21 | 16 | 0 | 0 | 5 |
| Robin (Win11, Day 2) | 21 | 14 | 1 | 0 | 6 |
| Robin (Ubuntu 24.04) | 21 | 4 | 1 | 0 | 16 |
| Robin (Fedora 42) | 21 | 4 | 1 | 0 | 16 |
| Robin (Pop!_OS 22.04) | 21 | 4 | 1 | 0 | 16 |

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

### INST-04 — Linux: App fails to launch on all three tested distros

| Field | Value |
|-------|-------|
| **Severity** | `CRITICAL` |
| **Test ID** | INST-04 |
| **Platforms** | Linux (Ubuntu 24.04, Fedora 42, Pop!_OS 22.04) |
| **Found by** | Robin van Boven |

**What's broken:** Zephyr Agency fails to start on all three tested Linux distributions. Each distro fails differently:

- **Ubuntu 24.04:** Blocked by AppArmor (`kernel.apparmor_restrict_unprivileged_userns`). Applying the workaround (`sysctl`) yields a white screen with 100% CPU usage.
- **Fedora 42 (Wayland):** X Error `BadWindow` — app does not start.
- **Pop!_OS 22.04:** Blank window opens, then crashes.

Terminal output shows `Loaded 0 specialists in 0ms` across all distros. Linux is completely unusable.

**Tester evidence:**
- Robin tested all three distros systematically. Ubuntu had the most detailed investigation with AppArmor workaround attempted. All three showed `Loaded 0 specialists in 0ms` in terminal output.

**Cross-platform:** Linux only. macOS and Windows launch successfully.

#### Investigation Prompt

```
The application fails to launch on all three tested Linux distributions. Three
different failure modes suggest multiple platform-specific issues.

1. Ubuntu 24.04 — AppArmor blocks unprivileged user namespaces:
   Search for the Tauri bundler configuration and AppImage/deb packaging.
   Ubuntu 24.04+ restricts unprivileged user namespaces by default. The Tauri
   webview (WebKitGTK) may require user namespaces for its sandbox. Check:
   - Is the app packaged as AppImage, deb, or both?
   - Does the AppImage use --no-sandbox or set up the sandbox correctly?
   - After bypassing AppArmor, white screen + 100% CPU suggests the webview
     starts but the frontend never loads. Check if the dist/index.html and
     assets are bundled correctly in the Linux build.

2. Fedora 42 (Wayland) — X Error BadWindow:
   - The app is trying to use X11 on a Wayland session. Check if the Tauri
     app forces X11 or if there is a DISPLAY/WAYLAND_DISPLAY issue.
   - Try running with GDK_BACKEND=wayland or check if WebKitGTK on Fedora 42
     supports the Wayland backend.
   - Check Tauri's Linux build target and whether it links against
     libwebkit2gtk for the correct display server.

3. Pop!_OS 22.04 — Blank window then crash:
   - Similar to Ubuntu but older base. Check WebKitGTK version compatibility.
   - Review crash logs/core dumps if available.

4. Common across all — "Loaded 0 specialists in 0ms":
   - This terminal output suggests the Rust backend initialises but the
     frontend never connects or loads data. Check the IPC bridge between
     Tauri backend and webview frontend.

Expected: Application launches and displays the frontend on all three Linux
distributions. If Linux support is not yet ready, document it as a known
limitation.
```

#### Test

Write regression tests that assert:
- The application launches and displays the main UI on Ubuntu 24.04 without requiring sysctl workarounds.
- The application launches on Fedora 42 under both Wayland and X11 sessions.
- The application launches on Pop!_OS 22.04 without crashing.
- The frontend assets (index.html, JS, CSS) are correctly bundled and loaded in the Linux build.
- If AppArmor or sandbox configuration is required, it is handled automatically by the installer/package.

#### Validate

- [ ] Launch on Ubuntu 24.04 (fresh install, no sysctl changes). App displays the UI.
- [ ] Launch on Fedora 42 (Wayland session). App displays the UI.
- [ ] Launch on Pop!_OS 22.04. App displays the UI without crashing.
- [ ] All three show actual content (not a white screen) and load specialists.

#### Checkpoint

Application launches on all three target Linux distributions. Frontend loads and renders. If Linux is descoped, this is documented as a known limitation with a clear timeline. No regression on macOS or Windows.

---

### INST-06 — Fullscreen mode breaks window layout with black artifacts

| Field | Value |
|-------|-------|
| **Severity** | `HIGH` |
| **Test ID** | INST-06 |
| **Platforms** | Windows 11 |
| **Found by** | Robin van Boven |

**What's broken:** Entering fullscreen changes the app layout but stays within the existing window bounds (doesn't actually go fullscreen). Exiting fullscreen leaves black areas until the window is manually resized. Additional issues observed: 100% CPU usage on launch, window close requires double-clicking the X button, keyboard shortcuts don't work until the UI is clicked (focus issue), and a black bar appears at the bottom at certain window sizes.

**Tester evidence:**
- Robin: *"Full screen is bugged, it changes the app layout but stays within the bounds of the existing window. Returning from full screen gives black areas, until the window is resized."*
- Also noted: 100% CPU on launch, double X-click to close, keybinds need UI click first, black bar at bottom.

**Cross-platform:** Windows 11 only. macOS fullscreen worked correctly.

#### Investigation Prompt

```
Search for fullscreen handling in the Tauri window configuration and any
frontend fullscreen toggle. Multiple window management issues on Windows 11:

1. Fullscreen doesn't fill screen — look for the Tauri window.setFullscreen()
   call or the window configuration in tauri.conf.json. On Windows, the
   webview may not resize to match the fullscreen window bounds. Check if
   the webview container has a fixed size that doesn't update on fullscreen.
2. Black artifacts after exiting fullscreen — the webview doesn't resize back
   to the window dimensions. This is a known Tauri/WRY issue on Windows. Check
   for a resize event handler that forces a webview layout recalculation.
3. 100% CPU on launch — check for animation loops, polling, or a rendering
   loop that runs continuously. Profile with Tauri DevTools.
4. Double-click to close — the window close button requires two clicks. Check
   if the first click is being consumed by a focus event (the window may not
   have focus and the first click activates the window instead of closing it).
5. Keyboard focus — keybinds don't work until UI is clicked. The webview may
   not receive focus automatically on Windows. Check window.focus() calls and
   Tauri's window focus configuration.

Expected: Fullscreen fills the entire screen, exit returns to correct
dimensions, single-click close, keyboard shortcuts work immediately.
```

#### Test

Write regression tests that assert:
- Entering fullscreen fills the entire screen, not just the existing window bounds.
- Exiting fullscreen restores the window to its previous dimensions with no black artifacts.
- Window close works with a single click on the X button.
- Keyboard shortcuts work immediately after the app launches (without requiring a UI click first).
- CPU usage is at a reasonable level (not 100%) during idle.

#### Validate

- [ ] Enter fullscreen on Windows 11. App fills the entire screen.
- [ ] Exit fullscreen. Window returns to previous size, no black areas.
- [ ] Click X once. Window closes.
- [ ] Press a keyboard shortcut immediately after launch. It works.

#### Checkpoint

Window management works correctly on Windows 11. Fullscreen, close, and keyboard focus all behave as expected. No regression on macOS.

---

### INST-05 — Sidebar collapse becomes unresponsive after first launch

| Field | Value |
|-------|-------|
| **Severity** | `LOW` |
| **Test ID** | INST-05 |
| **Platforms** | macOS ARM64 |
| **Found by** | Cristian Vogel |

**What's broken:** After first launch, collapsing the sidebar and then trying to re-open it fails — the sidebar toggle becomes unresponsive. This occurred in the unauthenticated state.

**Tester evidence:**
- Cristian (Day 1): *"BLOCKER: not authenticated, collapsed sidebar, was not able to open again, sidebar open trigger became unresponsive."*

**Cross-platform:** macOS ARM64 only. Not reproduced on Windows (Robin's sidebar worked).

#### Investigation Prompt

```
Search for the sidebar toggle component and its state management. On macOS,
collapsing the sidebar while unauthenticated makes the toggle unresponsive —
the sidebar cannot be reopened.

Check:
1. Toggle state — is the sidebar open/closed state stored in a signal/store?
   Check if the toggle handler is still accessible when the sidebar is
   collapsed (the toggle button may be inside the sidebar and collapse with it).
2. Auth state interaction — this happened while unauthenticated. Check if the
   sidebar component has different behaviour or a different toggle element
   when the user is not signed in.
3. Click target — when the sidebar is collapsed, is the toggle area still
   clickable? It may be covered by another element or have zero width.
4. CSS transition — a CSS transition on the sidebar width may complete and
   remove the toggle element from the clickable area.

Expected: Sidebar can always be toggled open/closed regardless of auth state.
The toggle control must remain accessible when the sidebar is collapsed.
```

#### Test

Write a test that asserts:
- The sidebar can be collapsed and reopened in both authenticated and unauthenticated states.
- The toggle control remains clickable when the sidebar is collapsed (click target has non-zero dimensions).

#### Validate

- [ ] On macOS, collapse the sidebar while unauthenticated. Click the toggle. Sidebar reopens.
- [ ] Repeat while authenticated. Same result.

#### Checkpoint

Sidebar toggle works in all states on all platforms. No regression in sidebar navigation.

---

*3 issues in Installation & Setup. 1 CRITICAL (Linux launch failure), 1 HIGH (Windows fullscreen), 1 LOW (sidebar toggle). Screenshots: see `robin-linux-5feb/00-pre-launch-setup-ubuntu24-media/` (1 image), `robin-win11-6feb/00-pre-launch-setup-media/` (3 images), `cristian-macos-5feb/00-pre-launch-setup-media/` (1 image).*
