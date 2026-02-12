---
title: "Specialists Test Report"
date: 2026-02-09
category: features
tags: [specialists, agents, ai]
author: James Barclay
status: complete
build_version: 0.1.4
---

# Specialists — Area Report

| Field | Value |
|-------|-------|
| **Suite** | 04-specialists |
| **Test IDs** | SPEC-04, SPEC-06, SPEC-07 |
| **Platforms Tested** | macOS ARM64, Windows 11 |
| **Sessions** | Alex (macOS, Day 1), Cristian (macOS, Day 2), Robin (Win11, Day 2) |

---

## Suite Results

| Metric | Value |
|--------|-------|
| Total steps | 95 |
| Passed | 61 |
| Failed | 9 |
| Blocked | 4 |
| N/A | 21 |
| **Pass rate** | **82.4%** |

### Per-Session Breakdown

| Session | Steps | Pass | Fail | Blocked | N/A |
|---------|-------|------|------|---------|-----|
| Alex (macOS, Day 1) | 19 | 19 | 0 | 0 | 0 |
| Cristian (macOS, Day 2) | 19 | 17 | 0 | 0 | 2 |
| Robin (Win11, Day 2) | 38 | 25 | 9 | 4 | 0 |

macOS passed cleanly. All 9 failures and 4 blocked steps are on Windows 11.

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

### SPEC-04 — Specialist list counts inaccurate

| Field | Value |
|-------|-------|
| **Severity** | `MEDIUM` |
| **Test ID** | SPEC-04 |
| **Platforms** | Windows 11 |
| **Found by** | Robin van Boven |

**What's broken:** The specialist list filter/toggle shows incorrect counts. The button claims 3 specialists but there are actually 6 plus 1 custom. The "Bundled Specialists" category header says 15 but actually contains 3.

**Tester evidence:**
- Robin: *"List counts are incorrect. Button says 3. Actually 6+1 custom. Bundled Specialists category says 15 but actually 3."*

**Cross-platform:** Windows 11 only. macOS sessions showed correct counts (all passed).

#### Investigation Prompt

```
Search for the specialist list component and its count calculations. The filter
toggle and category headers show wrong counts:
- Filter button says "3" but there are 6 bundled + 1 custom specialist
- "Bundled Specialists" category header says "15" but contains only 3

Check:
1. Count calculation — find where the specialist count is computed. It may be
   counting a different array or using a stale value.
2. Filter state — the filter toggle count may be showing the count from one
   filter state while displaying items from another.
3. Category count — the "Bundled Specialists" header count (15) suggests it
   may be counting items from a different source or including hidden items.
4. Data source — check if the specialist data is loaded from local config or
   an API. The count mismatch may be between the metadata count and the
   actually rendered items.

Expected: All counts match the actual number of visible specialists in each
category.
```

#### Test

Write a test that asserts the displayed count on the filter button and each category header matches the actual number of rendered specialist items in that category.

#### Validate

- [ ] Filter button count matches the number of visible specialists.
- [ ] Each category header count matches the number of items rendered in that category.
- [ ] Test on Windows 11.

#### Checkpoint

All specialist counts are accurate. No regression in specialist list rendering or filtering.

---

### SPEC-06 — Create Specialist bypasses expected creation flow

| Field | Value |
|-------|-------|
| **Severity** | `MEDIUM` |
| **Test ID** | SPEC-06 |
| **Platforms** | Windows 11 |
| **Found by** | Robin van Boven |

**What's broken:** Clicking "Create Specialist" immediately opens a blank specialist view with a chat prompt sidebar instead of showing a creation form or wizard. The view also doesn't handle narrow window sizes.

**Tester evidence:**
- Robin: *"Immediately goes to a blank Specialist view with chat prompt sidebar. This view also does not support narrow window sizes."*

**Cross-platform:** Windows 11 only tested. May be by design, but the expected flow was a creation form.

#### Investigation Prompt

```
Search for the "Create Specialist" button handler and the specialist creation
flow. Currently it skips any creation form and jumps directly to a blank
specialist view with a chat sidebar.

Check:
1. Is this the intended flow? If so, the button label "Create Specialist"
   is misleading — consider "New Specialist" or adding inline editing for
   the specialist name/description.
2. If a creation form/wizard was intended, check if it exists but is being
   bypassed (e.g., a missing route or a direct navigation to the specialist
   view).
3. Responsive layout — the specialist view doesn't handle narrow windows.
   Check for min-width or responsive breakpoints on this view.

Expected: Either a creation form/wizard appears, or the blank specialist view
has proper inline editing and handles all window sizes.
```

#### Test

Write a test that asserts: clicking "Create Specialist" either opens a creation form with name/description fields, or opens a specialist view that is functional at all supported window sizes (including narrow). Either path should allow the user to set a name before proceeding.

#### Validate

- [ ] Click "Create Specialist" on Windows 11. A clear creation flow begins.
- [ ] Narrow the window. Layout does not break.

#### Checkpoint

Specialist creation flow is clear and responsive. No regression in existing specialist views.

---

### SPEC-07 — Specialist creation: category/filter options missing

| Field | Value |
|-------|-------|
| **Severity** | `LOW` |
| **Test ID** | SPEC-07 |
| **Platforms** | Windows 11 |
| **Found by** | Robin van Boven |

**What's broken:** No category or filter options visible on the specialist creation/view screen. All steps for SPEC-07 were blocked because the expected UI elements are absent.

**Tester evidence:**
- Robin: *"There is nothing related to categories or filters on screen."*

**Cross-platform:** Windows 11 only tested.

#### Investigation Prompt

```
Search for specialist category and filter UI components. The specialist
creation/editing view has no category assignment or filter options visible.

Check if categories/filters are:
1. Not yet implemented (feature gap)
2. Hidden behind a different UI path
3. Only visible after the specialist is saved

If not yet implemented, this is a feature gap to track, not a bug.
```

#### Test

If categories/filters are an intended feature, write a test that asserts: when creating or editing a specialist, category and filter options are visible and functional. If the feature is intentionally deferred, document it as a known gap.

#### Validate

- [ ] Specialist creation/edit view shows category and filter options, or the feature is documented as not-yet-implemented.

#### Checkpoint

Feature status is clear. No ambiguity about whether categories/filters should exist.

---

*3 issues in Specialists. 2 MEDIUM (counts, creation flow), 1 LOW (missing filters). All found on Windows 11 only — macOS was clean. Screenshots: see `robin-win11-6feb/04-specialists-media/` (4 images).*
