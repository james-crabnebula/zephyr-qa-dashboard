---
title: "UI/UX Manual Testing - Consolidated Report"
date: 2026-02-12
category: daily
tags: [ui, ux, manual, a11y, wcag, macos, windows, linux, 0.1.4-80b9f15]
author: Alex Lohr, Cristián Vogel, Robin Van Boven
status: complete
build_version: 0.1.4-80b9f15
platform: All (macOS, Windows, Linux)
---

# UI/UX — Manual Testing Consolidated Report

| Field | Value |
|-------|-------|
| **Build** | 0.1.4-80b9f15 |
| **Date** | 12 February 2026 |
| **Platforms** | macOS, Windows 11, Linux |
| **Testers** | Alex Lohr, Cristián Vogel, Robin Van Boven |

---

## Summary

Three testers performed manual UI/UX testing across all platforms. They found 16 issues ranging from layout bugs to accessibility failures.

| Severity | Count |
|----------|-------|
| HIGH | 4 |
| MEDIUM | 6 |
| LOW | 6 |

---

## How to Use This Report

Each issue includes tester evidence (direct quotes), investigation hints, and validation criteria. Developers can use the investigation prompts to locate the problem and the validation criteria to confirm fixes.

---

## HIGH Severity Issues

### UI-01 — Window resizing breaks layout

| Field | Value |
|-------|-------|
| **Severity** | `HIGH` |
| **Platform** | All |
| **Confidence** | HIGH — multiple testers |

**What's broken:** When you resize the window too narrow while the sidebar is open, the header layout breaks. On Windows, the content overflows and the user area drops off the screen entirely.

**Tester evidence:**
- Alex: "making the window too narrow breaks the layout of the header if the sidebar is visible"
- Cristián: "RESIZING IS BUGGY. PAGE CONTENT RESIZES TO DIFFERENT DIMENSIONS, OVERFLOWS"

**Workaround:** Collapse the sidebar before resizing the window narrow.

#### Investigation Prompt

```
Window resizing breaks layout when sidebar is open.

Look for:
1. CSS flex/grid constraints on header when sidebar visible
2. Min-width handling on main content area
3. Overflow behaviour on resize events
4. Windows-specific resize handling differences
```

#### Validation Criteria

- [ ] Window can resize to minimum without layout breaking
- [ ] Header remains intact at all widths
- [ ] Sidebar state doesn't affect resize behaviour

---

### UI-02 — Taskbar covers user area in fullscreen (Windows)

| Field | Value |
|-------|-------|
| **Severity** | `HIGH` |
| **Platform** | Windows 11 only |
| **Confidence** | HIGH |

**What's broken:** In fullscreen mode on Windows, the user avatar and Sign In button are hidden behind the Windows taskbar. Users cannot access these controls.

**Tester evidence:**
- Cristián: "WHEN IN FULL SCREEN, USER AREA IS COVERED BY WINDOWS TASK BAR"
- Cristián: "AVATAR/NAME AREA AND SIGN IN COVERED BY WINDOWS TASK BAR IN FULL SCREEN MODE"

#### Investigation Prompt

```
Windows taskbar covers bottom UI elements in fullscreen.

Look for:
1. Fullscreen mode implementation on Windows
2. Window bounds calculation not accounting for taskbar
3. Tauri window configuration for Windows fullscreen
4. Compare with macOS fullscreen handling
```

#### Validation Criteria

- [ ] User area visible in fullscreen on Windows
- [ ] Sign In button accessible in fullscreen
- [ ] No UI elements hidden by taskbar

---

### UI-03 — Contrast too low in Workflows Quick Start

| Field | Value |
|-------|-------|
| **Severity** | `HIGH` (WCAG fail) |
| **Platform** | All |
| **Confidence** | HIGH — measured |

**What's broken:** The step numbers in the Quick Start section have a contrast ratio of 2.68. WCAG requires a minimum of 4.5 for normal text. This fails accessibility compliance.

**Tester evidence:**
- Alex: "The contrast of the numbers in Quick Start is way too low (2.68, as opposed to the minimum of 4.5 - white gets a contrast ratio of 14.43 and also looks better)"

**Quick fix:** Change the number color to white (#FFFFFF) which would achieve a contrast ratio of 14.43.

#### Validation Criteria

- [ ] Step numbers have contrast ratio >= 4.5
- [ ] Text remains readable on all backgrounds
- [ ] WCAG AA compliance achieved

---

### UI-04 — Channel content visible before authentication

| Field | Value |
|-------|-------|
| **Severity** | `HIGH` (Security) |
| **Platform** | Windows 11 |
| **Confidence** | HIGH |

**What's broken:** Users can see channel content before they've logged in. This is a security concern — unauthenticated users should not have access to any workspace content.

**Tester evidence:**
- Robin: "Channel content is visible before authentication"

#### Investigation Prompt

```
Channel content visible before auth — potential security issue.

Look for:
1. Route guards on channel views
2. Authentication state checks before rendering content
3. Server-side vs client-side auth enforcement
4. Compare behaviour across platforms
```

#### Validation Criteria

- [ ] No channel content visible when unauthenticated
- [ ] Protected routes redirect to login
- [ ] Consistent behaviour across platforms

---

## MEDIUM Severity Issues

### UI-05 — Sign-in button hard to find

| Field | Value |
|-------|-------|
| **Severity** | `MEDIUM` |
| **Platform** | All |
| **Confidence** | HIGH |

**What's broken:** The Sign In button in the lower left corner appears late and has no visual indication to draw the user's attention. New users struggle to find it.

**Tester evidence:**
- Alex: "The sign-in button in the lower left corner registers rather late; a subtle animation would help users to find it faster"
- Alex: "Button hard to find, use a subtle animation to ensure visibility"

**Suggestion:** Add a subtle pulse or fade-in animation when the button appears.

#### Validation Criteria

- [ ] Sign-in button clearly visible on first launch
- [ ] Animation draws attention without being distracting

---

### UI-06 — Close button (X) appears/disappears (Windows)

| Field | Value |
|-------|-------|
| **Severity** | `MEDIUM` |
| **Platform** | Windows 11 |
| **Confidence** | HIGH |

**What's broken:** During window resize on Windows, the close button (X) flickers in and out of view at the center top of the window.

**Tester evidence:**
- Cristián: "CLOSE WINDOW ICON (X) APPEARS AND DISAPPEARS IN THE CENTER TOP OF WINDOW"

#### Validation Criteria

- [ ] Close button remains stable during resize
- [ ] No flickering or position jumping

---

### UI-07 — Node palette bottom cut off (85px)

| Field | Value |
|-------|-------|
| **Severity** | `MEDIUM` |
| **Platform** | All |
| **Confidence** | HIGH — CSS identified |

**What's broken:** The bottom 85 pixels of the workflow node palette are inaccessible. Users cannot scroll to see all available nodes.

**Tester evidence:**
- Alex: "The bottom of the node palette is inaccessible due to a positioning error - a parent div has class='flex h-screen', but is -85px, thus 85px of its content is cut off"

**Quick fix:** Add `pb-[85px]` to the parent div.

#### Validation Criteria

- [ ] All nodes in palette are accessible
- [ ] Palette scrolls to show all content

---

### UI-08 — Sidebar toggle hidden on narrow resize

| Field | Value |
|-------|-------|
| **Severity** | `MEDIUM` |
| **Platform** | All |
| **Confidence** | HIGH |

**What's broken:** When you resize the window to minimum width with the sidebar open, the sidebar toggle icon becomes hidden. Users get stuck with no way to collapse the sidebar.

**Tester evidence:**
- Cristián: "Resizing to minimum with drawer open still conflicts/hides the Sidebar Open/Close trigger icon"

#### Validation Criteria

- [ ] Sidebar toggle always visible
- [ ] Toggle accessible at all window sizes

---

### UI-09 — No keyboard shortcuts

| Field | Value |
|-------|-------|
| **Severity** | `MEDIUM` |
| **Platform** | All |
| **Confidence** | HIGH |

**What's broken:** The app has no keyboard shortcuts for common actions. Power users and accessibility-dependent users cannot navigate efficiently.

**Tester evidence:**
- Alex: "A11y could be improved, there are no hot-keys"

#### Validation Criteria

- [ ] Common actions have keyboard shortcuts
- [ ] Shortcuts documented in help/settings

---

### UI-10 — Auth window stays open after login

| Field | Value |
|-------|-------|
| **Severity** | `MEDIUM` |
| **Platform** | All |
| **Confidence** | HIGH |

**What's broken:** After completing OAuth login, the browser authentication window stays open. Users must manually close it.

**Tester evidence:**
- Alex: "The auth window staying open could have security implications; maybe add a small toast notification that the user should now close remaining browser windows"

**Suggestion:** Add a toast notification reminding users to close the browser window, or auto-close if possible.

#### Validation Criteria

- [ ] Auth window closes automatically after login, OR
- [ ] Toast notification reminds user to close browser window

---

## LOW Severity Issues

### UI-11 — User menu lacks visual definition

| Field | Value |
|-------|-------|
| **Severity** | `LOW` |
| **Platform** | All |

**What's broken:** The user menu dropdown lacks visual definition — no border, shadow, or background distinction. Users may not recognize it as a menu.

**Tester evidence:**
- Alex: "Menu is not very defined optically; a border, shadow or lighter background would make it more recognizable"

---

### UI-12 — F11 doesn't toggle fullscreen (Windows)

| Field | Value |
|-------|-------|
| **Severity** | `LOW` |
| **Platform** | Windows 11 |

**What's broken:** Pressing F11 does nothing. Users expect F11 to toggle fullscreen on Windows.

**Tester evidence:**
- Cristián: "F11 does not make full screen window. 'Maximise' does"

---

### UI-13 — No link to documentation

| Field | Value |
|-------|-------|
| **Severity** | `LOW` |
| **Platform** | All |

**What's broken:** There's no internal link to help or documentation. Users have no way to learn about features.

**Tester evidence:**
- Alex: "no internal link to documentation"

---

### UI-14 — + icon next to Filters misleading

| Field | Value |
|-------|-------|
| **Severity** | `LOW` |
| **Platform** | All |

**What's broken:** On the Tasks page, the + icon appears next to "Filters" making users think it adds a filter, not a task.

**Tester evidence:**
- Alex: "The +-icon next to Filters will make users think that they can use it to add a filter. Maybe add another 'Tasks' headline above Filters to avoid this confusion."

---

### UI-15 — Knowledge Garden auto-enters first item

| Field | Value |
|-------|-------|
| **Severity** | `LOW` |
| **Platform** | All |

**What's broken:** Knowledge Garden automatically navigates into the first item instead of showing a list or empty state.

**Tester evidence:**
- Alex: "Page automatically entered first personal item 'most played games', which is also reflected in the URL; one sees the plot instead of a list or an empty state"

---

### UI-16 — Black bar at bottom of window (Windows VM)

| Field | Value |
|-------|-------|
| **Severity** | `LOW` |
| **Platform** | Windows 11 (VM) |

**What's broken:** A black bar appears at the bottom of the window that doesn't belong there.

**Tester evidence:**
- Robin: "A black bar at the bottom of the window, which seems to not belong there"

---

## Positive Feedback

The testers also noted several positives:

- Overall look and feel is good
- Navigation structure is logical
- Visual editor works well once accessed
- App feels reasonably performant

---

## Recommendations

### P1 — Must Fix
1. **UI-04** — Content visible before auth (security)
2. **UI-01** — Resize layout breaks (all platforms)
3. **UI-03** — WCAG contrast failure (accessibility)

### P2 — Should Fix
4. **UI-02** — Taskbar covers user area (Windows)
5. **UI-07** — Node palette CSS fix (quick win)
6. **UI-06** — Close button flickering (Windows)

### P3 — Nice to Have
7. **UI-05** — Sign-in button animation
8. **UI-09** — Keyboard shortcuts
9. **UI-11** — Menu visual styling

---

*Report generated 12 Feb 2026*
*Source: Manual testing by Alex Lohr, Cristián Vogel, Robin Van Boven*
