---
title: "Tasks Test Report"
date: 2026-02-09
category: features
tags: [tasks, kanban, project-management]
author: James Barclay
status: complete
build_version: 0.1.4
---

# Tasks — Area Report

| Field | Value |
|-------|-------|
| **Suite** | 07-tasks |
| **Test IDs** | TASK-03, TASK-05, TASK-09, TASK-10 |
| **Platforms Tested** | macOS ARM64, Windows 11 |
| **Sessions** | Alex (macOS, Day 1), Cristian (macOS, Day 2), Robin (Win11, Day 2) |

---

## Suite Results

| Metric | Value |
|--------|-------|
| Total steps | 133 |
| Passed | 83 |
| Failed | 10 |
| Blocked | 6 |
| N/A | 34 |
| **Pass rate** | **83.8%** |

### Per-Session Breakdown

| Session | Steps | Pass | Fail | Blocked | N/A |
|---------|-------|------|------|---------|-----|
| Alex (macOS, Day 1) | 30 | 27 | 0 | 0 | 3 |
| Cristian (macOS, Day 2) | 30 | 25 | 0 | 5 | 0 |
| Robin (Win11, Day 2) | 43 | 31 | 10 | 1 | 1 |

Alex had zero failures on macOS. Cristian's 5 blocked steps are all from the Kanban crash (TASK-05). Robin found 10 failures covering Kanban, offline, deletion, and empty state issues.

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

### TASK-05 — Task Kanban drag-and-drop broken; state corruption after crash

| Field | Value |
|-------|-------|
| **Severity** | `HIGH` |
| **Test ID** | TASK-05 |
| **Platforms** | Windows 11, macOS ARM64 |
| **Found by** | Robin van Boven (Win11), Cristian Vogel (macOS) |

**What's broken:** Task cards cannot be dragged between Kanban columns. On macOS, a crash corrupted the Kanban state persistently — even after reloading and relaunching, dragging any card throws: `Uncaught (in promise) Error: invalid order key head: undefined`. The state corruption survives app restarts. Task descriptions also render raw markdown instead of formatted text.

**Tester evidence:**
- Robin (Win11): *"Card does not move."*
- Cristian (macOS Day 2): *"PREVIOUS CRASH, BROKE KANBAN even after reloads/relaunches. Shows cards, dragging from any column: Uncaught (in promise) Error: invalid order key head: undefined. Prob persistent state corruption?"*

**Cross-platform:** Broken on both Windows 11 (drag fails silently) and macOS ARM64 (persistent state corruption after crash).

#### Investigation Prompt

```
Search for the Kanban board component and its drag-and-drop implementation.
Two related issues:

1. Drag-and-drop fails — cards cannot be moved between columns. On Windows 11,
   nothing happens. On macOS, it throws:
   "Uncaught (in promise) Error: invalid order key head: undefined"

2. Persistent state corruption — after a crash on macOS, the Kanban state
   became permanently corrupted. Even after reload/relaunch, the error persists.
   This means the corrupted order keys are stored in the local database.

Check:
1. Order key generation — the "invalid order key head: undefined" error
   suggests a fractional indexing or order key library is being passed an
   undefined value. Search for the order key generation function and check
   what happens when a column has no existing items (head is undefined).
2. State persistence — Kanban card positions are stored locally. Find the
   storage mechanism (SQLite, localStorage, etc.) and check for corrupted
   entries with undefined/null order keys.
3. Recovery — there is no way for users to recover from corrupted state.
   Add error handling that detects invalid order keys and regenerates them,
   or provide a "reset board" option.
4. Raw markdown rendering — task description cards show raw markdown (e.g.,
   **bold** instead of bold). Check if the card component is missing a
   markdown renderer.

Expected: Cards drag between columns reliably. Crash recovery does not corrupt
persistent state. Task descriptions render formatted markdown.
```

#### Test

Write regression tests that assert:
- Dragging a task card from one Kanban column to another updates the card's column and persists the change.
- Order keys are always valid after a drag operation — no undefined or null order keys in the persisted state.
- If order keys are corrupted (e.g., undefined), the app recovers gracefully (regenerates keys) rather than throwing an unhandled error.
- Task descriptions render formatted markdown (bold, links, lists) rather than raw markdown syntax.

#### Validate

- [ ] Drag a card between columns on Windows 11. Card moves and stays in the new column.
- [ ] Drag a card between columns on macOS. Same result.
- [ ] Restart the app. Card positions are preserved correctly.
- [ ] Task descriptions show formatted text, not raw markdown.
- [ ] Simulate corrupted order keys. App recovers without crashing.

#### Checkpoint

Kanban drag-and-drop works on all platforms. State corruption is handled gracefully. Markdown renders in task cards. No regression in task creation or editing.

---

### TASK-03 — Task empty state shows pre-existing tasks

| Field | Value |
|-------|-------|
| **Severity** | `MEDIUM` |
| **Test ID** | TASK-03 |
| **Platforms** | Windows 11 |
| **Found by** | Robin van Boven |

**What's broken:** When navigating to Tasks for the first time, pre-existing tasks are already visible instead of an empty state with an onboarding prompt.

**Tester evidence:**
- Robin: *"There are already tasks there."*

**Cross-platform:** Windows 11 only tested. May be that sample/demo data is seeded, or tasks persist from a previous session.

#### Investigation Prompt

```
Search for the Tasks section initial state and any seed/demo data. When a new
user navigates to Tasks for the first time, pre-existing tasks are already
visible instead of an empty state.

Check:
1. Demo/sample tasks — are demo tasks seeded on first launch or on account
   creation? If so, consider whether this is intentional onboarding or
   confusing.
2. Shared state — if tasks are synced from a server, check if the user is
   seeing another user's tasks or shared workspace tasks.
3. Empty state component — does an empty state component exist? If so, check
   the condition that triggers it (e.g., tasks.length === 0 may not be true
   if demo tasks are seeded).

Expected: Either a clean empty state with onboarding, or clearly labelled
sample tasks that users understand are examples.
```

#### Test

Write a test that asserts: for a new user, the Tasks section either shows a clear empty state with onboarding guidance, or any pre-existing tasks are clearly labelled as examples/demos.

#### Validate

- [ ] New user sees an appropriate initial Tasks view — either empty state or clearly labelled demo tasks.

#### Checkpoint

Initial Tasks experience is clear and intentional. No regression in task display for existing users.

---

### TASK-09 — No task delete option; only archive available

| Field | Value |
|-------|-------|
| **Severity** | `MEDIUM` |
| **Test ID** | TASK-09 |
| **Platforms** | Windows 11 |
| **Found by** | Robin van Boven |

**What's broken:** Users cannot permanently delete tasks. The only option is "Archive", which hides the task but allows it to be restored. No permanent deletion path exists.

**Tester evidence:**
- Robin: *"There is no delete option. But there is archive. Which hides the task, but can be restored."*

**Cross-platform:** Windows 11 only tested. Likely by design across all platforms.

#### Investigation Prompt

```
Search for the task context menu or action menu. There is no "Delete" option —
only "Archive" is available.

Check:
1. Is this intentional? Some apps use archive-only to prevent data loss. If so,
   this is a design decision, not a bug — but should be documented.
2. If delete should exist, check if it was removed or never implemented. Look
   for a delete handler or API endpoint that exists but isn't exposed in the UI.
3. Consider adding a "Delete permanently" option within the archive view, so
   users can clean up archived items they no longer need.

This may be by design. Verify with product requirements.
```

#### Test

If delete is an intended feature, write a test that asserts: the task context menu includes a "Delete" option, and selecting it permanently removes the task after confirmation. If archive-only is by design, write a test that asserts the archive/restore flow works correctly.

#### Validate

- [ ] Task deletion or archive-only behaviour matches the product requirements.
- [ ] If archive-only: archive and restore both work correctly.
- [ ] If delete is added: delete requires confirmation and permanently removes the task.

#### Checkpoint

Task lifecycle (create, edit, archive, delete/restore) matches product requirements. No regression in task management.

---

### TASK-10 — Tasks do not load while offline

| Field | Value |
|-------|-------|
| **Severity** | `MEDIUM` |
| **Test ID** | TASK-10 |
| **Platforms** | Windows 11 |
| **Found by** | Robin van Boven |

**What's broken:** Tasks fail to load when the application is offline. Related to CHAT-11 (offline messages lost). The app does not appear to have local-first data availability for tasks.

**Tester evidence:**
- Robin: Detailed 9-step reproduction showing tasks do not load when offline. Related to the broader offline data persistence issue (CHAT-11).

**Cross-platform:** Windows 11 only tested. Likely affects all platforms.

#### Investigation Prompt

```
Search for the task loading mechanism and offline data strategy. Tasks do not
load at all when the app is offline.

Check:
1. Task data source — are tasks fetched from a remote API on every load, or
   is there a local cache/database? If remote-only, offline access is
   impossible without a caching layer.
2. Cache/sync strategy — if there is a local cache, is it populated on first
   load and used as a fallback when the network is unavailable?
3. Related to CHAT-11 — the same offline data architecture likely affects both
   chat messages and tasks. A unified approach to local-first data access
   would fix both issues.
4. Loading state — when offline, does the UI show a loading spinner forever,
   an error message, or nothing? The user should see cached data with an
   "offline" indicator.

Expected: Previously loaded tasks are available offline from local storage.
New tasks created offline are queued for sync.
```

#### Test

Write regression tests that assert:
- Previously loaded tasks are available from local cache when the app is offline.
- The UI displays an "offline" indicator when the network is unavailable.
- Tasks created offline are queued for sync and persisted locally.
- When connectivity is restored, offline-created tasks sync to the server.

#### Validate

- [ ] Go offline. Open Tasks. Previously loaded tasks are visible.
- [ ] An offline indicator is shown.
- [ ] Create a task offline. It persists locally. Going online syncs it.

#### Checkpoint

Tasks are accessible offline. Offline-created tasks sync when connectivity returns. No regression in online task management. Consider implementing alongside CHAT-11 for a unified offline strategy.

---

*4 issues in Tasks. 1 HIGH (Kanban corruption), 3 MEDIUM (empty state, no delete, offline). Screenshots: see `robin-win11-6feb/07-tasks-media/` (5 images).*
