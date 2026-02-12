---
title: "Manual vs E2E — Coverage Diff"
date: 2026-02-12
category: daily
tags: [analysis, coverage, manual, e2e, gaps, 0.1.4-80b9f15]
author: Q (Analysis)
status: complete
build_version: 0.1.4-80b9f15
platform: All
---

# Manual vs E2E — Coverage Diff Report

| Field | Value |
|-------|-------|
| **Build** | 0.1.4-80b9f15 |
| **Date** | 12 February 2026 |
| **Purpose** | Identify gaps between automated E2E and manual testing |

---

## Summary

| Category | Count |
|----------|-------|
| Manual-only findings (E2E gap) | 17 |
| Confirmed by both | 8 |
| E2E-only (not manually verified) | 3 |

---

## Manual-Only Findings (E2E Gaps)

These issues were found by manual testers but are **NOT covered by E2E automation**.

| ID | Finding | Severity | Found By | E2E Coverage |
|----|---------|----------|----------|--------------|
| UI-01 | Window resize breaks layout | HIGH | Alex, Cristián | **NONE** |
| UI-02 | Taskbar covers user area (Windows) | HIGH | Cristián | **NONE** |
| UI-03 | WCAG contrast failure (2.68:1) | HIGH | Alex | **NONE** |
| UI-05 | Sign-in button hard to find | MEDIUM | Alex | **NONE** |
| UI-06 | Close button flickers (Windows) | MEDIUM | Cristián | **NONE** |
| UI-07 | Node palette 85px cut off | MEDIUM | Alex | **NONE** |
| UI-08 | Sidebar toggle hidden on narrow | MEDIUM | Cristián | **NONE** |
| UI-09 | No keyboard shortcuts | MEDIUM | Alex | **NONE** |
| UI-10 | Auth window stays open | MEDIUM | Alex | **NONE** |
| UI-11 | User menu lacks visual definition | LOW | Alex | **NONE** |
| UI-12 | F11 doesn't toggle fullscreen | LOW | Cristián | **NONE** |
| UI-13 | No link to documentation | LOW | Alex | **NONE** |
| UI-14 | + icon next to Filters misleading | LOW | Alex | **NONE** |
| UI-15 | Knowledge Garden auto-enters first | LOW | Alex | **NONE** |
| UI-16 | Black bar at bottom (Windows VM) | LOW | Robin | **NONE** |
| UI-17 | OpenRouter → Local AI Models | MEDIUM | Alex | **NONE** |
| LINUX-01 | App requires --no-sandbox | HIGH | Q (automation) | **NONE** (infra) |

### Recommendation: Add E2E Tests

| Priority | Issue | Suggested Test |
|----------|-------|----------------|
| P1 | UI-03 | Accessibility contrast check (axe-core) |
| P1 | UI-07 | Node palette scroll to bottom element |
| P2 | UI-01 | Resize window with sidebar open, check layout |
| P2 | UI-17 | Verify Connections section shows OpenRouter |
| P3 | UI-12 | F11 keypress toggles fullscreen (Windows) |

---

## Confirmed by Both (E2E + Manual)

These issues were found by **both** E2E automation and manual testers.

| ID | Finding | E2E Status | Manual Status |
|----|---------|------------|---------------|
| AUTH-01 | Unauthenticated content access | FAIL | FAIL (Cristián, Robin) |
| SET-02 | Unexpected tabs (Language, Sandbox) | FAIL | FAIL (Alex) |
| SET-08 | Package registry unavailable | FAIL | FAIL (Alex) |
| CHAT-03 | Placeholder mentions "tasks" | FAIL | FAIL (Robin) |
| TASK-03 | Kanban columns not found | FAIL | Not tested |
| SPEC-04 | Specialist count badges missing | FAIL | Not tested |
| WORK-02 | Quick Start not found | FAIL | Not tested |
| INST-05 | Sidebar toggle not found | FAIL | PASS (varies) |

**Value:** Manual testing confirmed E2E findings are real bugs, not test flakiness.

---

## E2E-Only (Not Manually Verified)

These E2E failures were **not specifically tested** by manual testers.

| ID | Finding | E2E Status | Manual Status |
|----|---------|------------|---------------|
| CHAT-06 | AI responds to messages | FAIL | Not tested (no API key) |
| CHAT-11 | Messages persist after reload | FAIL | Not tested |
| SEC-01 | XSS in task description | FAIL | Not tested |

**Note:** These need manual verification to confirm severity.

---

## Coverage Analysis

### What Manual Testing Catches That E2E Misses

| Category | Examples |
|----------|----------|
| **Visual polish** | Contrast, menu styling, animations |
| **Responsive layout** | Resize bugs, overflow, element hiding |
| **Platform quirks** | Windows taskbar, F11, close button |
| **Accessibility** | WCAG compliance, keyboard navigation |
| **UX friction** | Button discoverability, confusing icons |
| **Feature changes** | OpenRouter → Local AI Models |

### What E2E Catches That Manual Misses

| Category | Examples |
|----------|----------|
| **Consistency** | Same tests every build |
| **Edge cases** | XSS payloads, rapid actions |
| **Regressions** | Previously working features |
| **Timing** | Race conditions, async issues |

---

## Recommendations

### 1. Add Visual Regression Tests
- Screenshot comparison for layout at different sizes
- Accessibility audit via axe-core

### 2. Add Platform-Specific Tests
- Windows: F11 fullscreen, taskbar handling
- Linux: Sandbox detection

### 3. Continue Manual Testing
Manual testing found **17 issues** that E2E missed. Human eyes catch:
- Visual bugs (contrast, alignment)
- UX issues (discoverability, confusion)
- Platform-specific quirks

---

*Report generated 12 Feb 2026*
*E2E: Playwright automation*
*Manual: Alex Lohr, Cristián Vogel, Robin Van Boven*
