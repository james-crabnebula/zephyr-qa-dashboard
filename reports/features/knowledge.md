---
title: "Knowledge Garden Test Report"
date: 2026-02-09
category: features
tags: [knowledge, rag, documents]
author: James Barclay
status: complete
build_version: 0.1.4
---

# Knowledge Garden — Area Report

| Field | Value |
|-------|-------|
| **Suite** | 05-knowledge |
| **Test IDs** | KNOW-01, KNOW-06, KNOW-07 |
| **Platforms Tested** | macOS ARM64, Windows 11 |
| **Sessions** | Alex (macOS, Day 1), Cristian (macOS, Day 2), Robin (Win11, Day 2) |

---

## Suite Results

| Metric | Value |
|--------|-------|
| Total steps | 71 |
| Passed | 52 |
| Failed | 4 |
| Blocked | 0 |
| N/A | 15 |
| **Pass rate** | **92.9%** (best of all suites) |

### Per-Session Breakdown

| Session | Steps | Pass | Fail | Blocked | N/A |
|---------|-------|------|------|---------|-----|
| Alex (macOS, Day 1) | 15 | 15 | 0 | 0 | 0 |
| Cristian (macOS, Day 2) | 15 | 15 | 0 | 0 | 0 |
| Robin (Win11, Day 2) | 26 | 22 | 4 | 0 | 0 |

Knowledge Garden is the strongest area. macOS had zero failures. All 4 failures are on Windows 11 and all are minor.

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

### KNOW-06 — Knowledge Garden sidebar missing count badges

| Field | Value |
|-------|-------|
| **Severity** | `MEDIUM` |
| **Test ID** | KNOW-06 |
| **Platforms** | Windows 11 |
| **Found by** | Robin van Boven |

**What's broken:** Sidebar category sections in the Knowledge Garden do not display item count badges. Users cannot see how many items exist in each category without clicking into it.

**Tester evidence:**
- Robin: *"There are no counts."*

**Cross-platform:** Windows 11 only. macOS passed.

#### Investigation Prompt

```
Search for the Knowledge Garden sidebar component and its category count
rendering. The sidebar categories do not show item count badges.

Check:
1. Is the count data available in the component props/store but not rendered?
2. Is there a badge/count component that exists but is conditionally hidden?
3. Is the count fetched asynchronously and the UI doesn't update when it
   arrives?

Expected: Each sidebar category shows a count badge (e.g., "Documents (5)",
"Links (3)").
```

#### Test

Write a test that asserts each Knowledge Garden sidebar category displays a count badge that matches the actual number of items in that category.

#### Validate

- [ ] Each sidebar category shows a count badge on Windows 11.
- [ ] Counts update when items are added or removed.

#### Checkpoint

Count badges visible and accurate for all categories. No regression in sidebar navigation.

---

### KNOW-07 — Knowledge Garden empty state differs from specification

| Field | Value |
|-------|-------|
| **Severity** | `MEDIUM` |
| **Test ID** | KNOW-07 |
| **Platforms** | Windows 11 |
| **Found by** | Robin van Boven |

**What's broken:** The empty state caption and available options differ from the expected design. The "Add item" dropdown display and functionality also don't match specification.

**Tester evidence:**
- Robin: *"Caption and options are different."*

**Cross-platform:** Windows 11 only. macOS passed.

#### Investigation Prompt

```
Search for the Knowledge Garden empty state component. The empty state copy
and action buttons/options differ from the design specification.

Check:
1. Compare the current empty state text and CTAs against the design spec or
   Figma mockups.
2. Look for the "Add item" dropdown — its display and options don't match
   specification.
3. This may be a spec-vs-implementation drift where the dev team iterated on
   the design. Verify with design whether the current implementation or the
   spec is correct.

Expected: Empty state matches the approved design specification.
```

#### Test

Write a test that asserts the Knowledge Garden empty state displays the specified caption text and action options. If the spec has changed, update both the test and the spec together.

#### Validate

- [ ] Empty state text and options match the current approved specification.
- [ ] "Add item" dropdown displays correct options.

#### Checkpoint

Empty state matches spec. Spec and implementation are in sync. No regression in Knowledge Garden content views.

---

### KNOW-01 — Knowledge Garden icon has transparency artifacts

| Field | Value |
|-------|-------|
| **Severity** | `LOW` |
| **Test ID** | KNOW-01 |
| **Platforms** | Windows 11 |
| **Found by** | Robin van Boven |

**What's broken:** The Knowledge Garden icon in the sidebar has white artifacts around its rounded corners, indicating improper transparency handling.

**Tester evidence:**
- Robin: *"Icon has improper transparency. White artifacts around the rounded corners."*

**Cross-platform:** Windows 11 only. macOS rendered correctly.

#### Investigation Prompt

```
Search for the Knowledge Garden sidebar icon asset. On Windows 11 it shows
white artifacts around the rounded corners, suggesting the PNG/SVG has a white
matte or the transparency is not properly handled.

Check:
1. If it's a PNG, ensure it has proper alpha channel transparency (not a white
   background with rounded corners cut out).
2. If it's an SVG, check for a white fill on the background path.
3. Windows webview may render transparency differently — check if the icon
   uses CSS border-radius on a non-transparent container.

Fix: Re-export the icon with proper transparency, or use an SVG with no
background fill.
```

#### Test

Write a test that asserts the Knowledge Garden sidebar icon renders without visible artifacts on all platforms. For asset-based icons, verify the source file has proper alpha channel transparency.

#### Validate

- [ ] Knowledge Garden icon renders cleanly on Windows 11 — no white artifacts.
- [ ] Verify on macOS (should already be clean).

#### Checkpoint

Icon renders correctly on all platforms. No regression in other sidebar icons.

---

*3 issues in Knowledge Garden. 2 MEDIUM (counts, empty state), 1 LOW (icon). All Windows 11 only — macOS was clean. Best suite overall at 92.9% pass rate. Screenshots: see `robin-win11-6feb/05-knowledge-media/` (5 images).*
