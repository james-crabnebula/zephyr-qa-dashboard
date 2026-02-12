---
title: "macOS 12 Feb — Specialists"
date: 2026-02-12
category: daily
tags: [macos, darwin, arm64, automated, 0.1.4-80b9f15, specialists]
author: Q (Automated)
status: complete
build_version: 0.1.4-80b9f15
platform: Darwin-arm64
---

# Specialists — Area Report

| Field | Value |
|-------|-------|
| **Suite** | 04-specialists |
| **Test IDs** | SPEC-04, SPEC-06, SPEC-07 |
| **Platform Tested** | macOS ARM64 |
| **Tester** | Q (Automated) |

---

## Suite Results

| Metric | Value |
|--------|-------|
| Total tests | 6 |
| Passed | 4 |
| Failed | 2 |
| **Pass rate** | **66.7%** |

---

## Issues

### SPEC-04 — Specialist count badges not visible

| Field | Value |
|-------|-------|
| **Severity** | `MEDIUM` |
| **Test ID** | SPEC-04 |
| **Platform** | macOS ARM64 |
| **Confidence** | MEDIUM — could be DOM selector issue |

**What's broken:** The Specialists page should display count badges showing how many specialists exist in each category. Test found 0 count badges.

**Tester evidence:**
- Automation: *"Count badges must be visible"*
- Rendered items found: > 0
- Count badges found: 0

#### Investigation Prompt

```
Search for count badge implementation on the Specialists page.

The page renders specialist items, but no count badges are visible.
Either:
1. Badges are rendered but with different class/selector than expected
2. Badges are not implemented
3. Badges only appear under certain conditions

Look for:
- Badge components in Specialists view
- Count display logic
- CSS that might hide badges
```

#### Validation Criteria

- [ ] Specialists page shows count badges
- [ ] Badge counts match actual rendered items

---

## Passing Tests

| Test ID | Description | Status |
|---------|-------------|--------|
| SPEC-06 | Create Specialist shows creation form | PASS |
| SPEC-07 | Specialist creation has category options | PASS |

---

*Report generated 12 Feb 2026 by Q (Automated)*
