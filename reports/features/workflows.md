---
title: "Workflows Test Report"
date: 2026-02-09
category: features
tags: [workflows, n8n, automation]
author: James Barclay
status: complete
build_version: 0.1.4
---

# Workflows — Area Report

| Field | Value |
|-------|-------|
| **Suite** | 06-workflows |
| **Test IDs** | WORK-02, WORK-03, WORK-04, WORK-05, WORK-06 |
| **Platforms Tested** | macOS ARM64, Windows 11 |
| **Sessions** | Alex (macOS, Day 1), Cristian (macOS, Day 2), Robin (Win11, Day 2) |

---

## Suite Results

| Metric | Value |
|--------|-------|
| Total steps | 92 |
| Passed | 63 |
| Failed | 5 |
| Blocked | 3 |
| N/A | 21 |
| **Pass rate** | **88.7%** |

### Per-Session Breakdown

| Session | Steps | Pass | Fail | Blocked | N/A |
|---------|-------|------|------|---------|-----|
| Alex (macOS, Day 1) | 19 | 18 | 1 | 0 | 0 |
| Cristian (macOS, Day 2) | 19 | 16 | 1 | 0 | 2 |
| Robin (Win11, Day 2) | 35 | 29 | 3 | 3 | 0 |

The visual editor has issues on both platforms. Drag-and-drop is unreliable everywhere. Windows has additional layout and feedback issues.

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

### WORK-05 — Workflow visual editor layout breaks at different window sizes

| Field | Value |
|-------|-------|
| **Severity** | `MEDIUM` |
| **Test ID** | WORK-05 |
| **Platforms** | Windows 11 |
| **Found by** | Robin van Boven |

**What's broken:** The visual editor's layout breaks at non-standard window sizes. On small windows, components overlap or become unusable. On large windows, the "Drag or click nodes" instruction text goes off-screen.

**Tester evidence:**
- Robin: *"On small windows layout breaks. On large windows the Drag or click nodes instruction goes off screen."*

**Cross-platform:** Windows 11 only tested.

#### Investigation Prompt

```
Search for the workflow visual editor component and its responsive layout. The
editor breaks at both small and large window sizes:
- Small windows: layout breaks, components overlap
- Large windows: instruction text goes off-screen

Check:
1. Container sizing — is the editor canvas sized with fixed pixels or
   responsive units? Look for overflow:hidden that may clip content.
2. Instruction overlay positioning — the "Drag or click nodes" text uses
   absolute or fixed positioning that doesn't account for canvas size.
3. Minimum dimensions — add min-width/min-height to prevent the layout from
   collapsing at small sizes.
4. Canvas viewport — if using a canvas library (e.g., SolidJS Flow, ReactFlow
   equivalent), check its viewport configuration and auto-fit behaviour.

Expected: Visual editor adapts to all reasonable window sizes without layout
breaks.
```

#### Test

Write a test that asserts the workflow visual editor renders correctly at small (800x600), medium (1280x720), and large (1920x1080) window sizes. No elements should overlap, clip, or go off-screen at any supported size.

#### Validate

- [ ] Resize window to small, medium, and large on Windows 11. Layout is usable at all sizes.
- [ ] Instruction text remains visible and within bounds.

#### Checkpoint

Visual editor is responsive at all supported window sizes. No regression in node placement or interaction.

---

### WORK-06 — Workflow Execute button produces no feedback

| Field | Value |
|-------|-------|
| **Severity** | `MEDIUM` |
| **Test ID** | WORK-06 |
| **Platforms** | Windows 11 |
| **Found by** | Robin van Boven |

**What's broken:** Clicking the Execute button on a workflow produces no visible feedback — no loading state, no success/error toast, no status change. Unclear if the workflow executed at all.

**Tester evidence:**
- Robin: Execute button provides no success/error feedback. No status indication after clicking.

**Cross-platform:** Windows 11 only tested.

#### Investigation Prompt

```
Search for the workflow Execute button handler. Clicking Execute produces no
visible feedback on Windows 11.

Check:
1. Click handler — does it dispatch an action, make an API call, or trigger
   a Tauri command? Check if the handler is wired up at all.
2. Loading state — is there a loading/executing state that should show a
   spinner or progress indicator?
3. Response handling — after execution completes (or fails), is there a toast,
   status badge, or log output that should appear?
4. n8n integration — if workflows execute via n8n, check the n8n API call and
   whether the response is processed.

Expected: Execute button shows loading state during execution, then displays
success/error feedback (toast, status badge, or execution log).
```

#### Test

Write a test that asserts: clicking Execute shows a loading/progress indicator, and upon completion displays either a success or error notification. The button should be disabled or show a spinner while execution is in progress.

#### Validate

- [ ] Click Execute on Windows 11. Loading indicator appears.
- [ ] After execution, a success or error notification is displayed.
- [ ] Button state prevents double-click during execution.

#### Checkpoint

Execute button provides clear feedback at every stage. No regression in workflow editing.

---

### WORK-03 — Visual editor: drag-and-drop janky; double-click creates duplicate nodes

| Field | Value |
|-------|-------|
| **Severity** | `LOW` |
| **Test ID** | WORK-03 |
| **Platforms** | macOS ARM64 |
| **Found by** | Cristian Vogel, Alex Lohr |

**What's broken:** Dragging nodes from the palette is unreliable — clicking works but dragging doesn't. Double-clicking (an intuitive gesture) creates two nodes stacked on top of each other. Patch wires are black on a black background, making them invisible.

**Tester evidence:**
- Cristian: *"Drag and drop is a bit janky. Click works better. Double click (which is intuitive) creates TWO nodes on top of each other, suggest a slight offset so that the user can see that double-clicking is not the right gesture. PATCH WIRE IS BLACK ON BLACK BG."*
- Alex: *"Dragging does not work, clicking does."*

**Cross-platform:** Confirmed on macOS ARM64 by both testers. Drag issues likely present on all platforms.

#### Investigation Prompt

```
Search for the workflow visual editor node creation and drag-and-drop handling.
Three UX issues:

1. Drag from palette unreliable — dragging a node from the palette to the
   canvas often fails. Clicking works. Check the drag event handlers and
   whether they conflict with the canvas pan/scroll gestures.
2. Double-click creates duplicates — double-clicking a palette item creates
   two nodes stacked at the same position. The click handler fires twice. Add
   debouncing or ignore the second click if a node was just created at the
   same position.
3. Black wires on black background — patch/connection wires are black, which
   is invisible against the dark editor background. Check wire stroke colour
   and ensure it contrasts with the canvas background in both light and dark
   themes.

Expected: Drag from palette works reliably, double-click creates only one node
(or is prevented), and wires are visible against the background.
```

#### Test

Write regression tests that assert:
- Dragging a node from the palette to the canvas creates exactly one node at the drop position.
- Double-clicking a palette item creates exactly one node (not two stacked).
- Connection wires are visible against both light and dark canvas backgrounds (contrast check).

#### Validate

- [ ] Drag a node from palette on macOS. One node appears at the drop position.
- [ ] Double-click a palette item. Only one node is created.
- [ ] Connection wires are visible in both themes.

#### Checkpoint

Node creation is reliable via both drag and click. Wires are visible. No regression in node editing or deletion.

---

### WORK-02 — Workflow Quick Start step 2 text inaccurate

| Field | Value |
|-------|-------|
| **Severity** | `LOW` |
| **Test ID** | WORK-02 |
| **Platforms** | macOS ARM64 |
| **Found by** | Cristian Vogel |

**What's broken:** Quick Start step 2 says "Add Nodes — Drag nodes from palette" but dragging doesn't work. Only clicking works to add nodes.

**Tester evidence:**
- Cristian: Step 2 says "Drag nodes from palette" but dragging does not work; only clicking works.

**Cross-platform:** macOS ARM64 tested. If drag is fixed (WORK-03), this text becomes accurate. Otherwise, update the text.

#### Investigation Prompt

```
Search for the workflow Quick Start / onboarding text, specifically step 2.
It says "Add Nodes — Drag nodes from palette" but dragging doesn't work.

Either:
1. Fix the drag-and-drop (see WORK-03) so the instruction is accurate, or
2. Update the text to "Add Nodes — Click nodes from the palette" to match
   actual behaviour.
```

#### Test

Write a test that asserts the Quick Start instruction text matches the actual interaction method for adding nodes. If it says "drag", dragging must work. If dragging is not supported, the text must say "click".

#### Validate

- [ ] Quick Start step 2 text matches the actual way to add nodes.

#### Checkpoint

Onboarding text is accurate. No regression in Quick Start flow.

---

### WORK-04 — Workflow refresh success dialog appears "ghosted"

| Field | Value |
|-------|-------|
| **Severity** | `LOW` |
| **Test ID** | WORK-04 |
| **Platforms** | macOS ARM64 |
| **Found by** | Cristian Vogel |

**What's broken:** The green success dialog/toast that appears after refreshing a workflow has incorrect opacity or rendering, appearing "ghosted" or semi-transparent.

**Tester evidence:**
- Cristian: *"Green dialog is ghosted?"*

**Cross-platform:** macOS ARM64 only. May be a rendering quirk.

#### Investigation Prompt

```
Search for the workflow refresh success toast/dialog component. It appears
semi-transparent ("ghosted") on macOS.

Check:
1. Toast opacity/animation — is there a CSS transition that starts at 0
   opacity and isn't completing?
2. Backdrop — check if the toast has a backdrop-filter or background that
   isn't rendering correctly.
3. Z-index — the toast may be behind a semi-transparent overlay.

Low priority cosmetic issue.
```

#### Test

Write a test that asserts the success toast renders at full opacity and is clearly legible when displayed.

#### Validate

- [ ] Refresh a workflow on macOS. Success toast is fully opaque and legible.

#### Checkpoint

Toast renders correctly. No regression in other toast/notification components.

---

*5 issues in Workflows. 2 MEDIUM (layout, execute feedback), 3 LOW (drag UX, text, toast). Screenshots: see `robin-win11-6feb/06-workflows-media/` (6 images), `cristian-macos-6feb/06-workflows-media/` (3 images).*
