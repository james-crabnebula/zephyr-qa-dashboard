---
title: "Accessibility Audit Report"
date: 2026-02-12
category: a11y
tags: [accessibility, wcag, a11y, axe-core, keyboard, screen-reader, 0.1.4-80b9f15]
author: Q (Automated)
status: complete
build_version: 0.1.4-80b9f15
platform: macOS arm64
---

# Accessibility Audit Report

| Field | Value |
|-------|-------|
| **Build** | 0.1.4-80b9f15 |
| **Date** | 12 February 2026 |
| **Standard** | WCAG 2.1 AA |
| **Tool** | axe-core 4.x + custom Playwright checks |
| **Platform** | macOS arm64 |
| **Tester** | Q (Automated) |

---

## Executive Summary

| Severity | Count | Notes |
|----------|-------|-------|
| Critical | 7 | `button-name` on 6 pages + Settings |
| Serious | 1 | `label` in Settings |
| Moderate | 0 | |
| Minor | 0 | |
| **Total** | **8** | Same issue repeated per page |

**Note:** The 8 violations are the **same `button-name` issue** detected on each page (sidebar buttons are global). Unique issues: **2 axe violations** + **3 keyboard/focus issues** = **5 distinct problems**.

**Verdict:** WCAG 2.1 AA compliance NOT achieved. Five criteria fail:
- 1.3.1 Info and Relationships (form inputs missing labels)
- 2.1.1 Keyboard (focus trap)
- 2.4.1 Bypass Blocks (no skip link)
- 2.4.7 Focus Visible (some elements lack focus state)
- 4.1.2 Name, Role, Value (13 buttons without names)

---

## Critical Findings

### A11Y-01 — Buttons without accessible names

| Field | Value |
|-------|-------|
| **Severity** | `CRITICAL` |
| **WCAG** | 4.1.2 Name, Role, Value |
| **Impact** | Screen reader users cannot identify button purpose |
| **Count** | 13 buttons in sidebar (same on all pages), 14 in Settings |
| **Confidence** | HIGH — axe-core detected |

**What's broken:** Icon-only buttons in the sidebar and navigation have no accessible names. Screen readers announce them as "button" with no indication of what they do.

**Affected elements:**
- New conversation button (`data-testid="new-conversation-button"`)
- Sidebar toggle buttons (`.ring-sidebar-ring`)
- Icon buttons (`.w-6`)
- Menu buttons in sidebar

#### Investigation Prompt

```
Buttons missing accessible names detected by axe-core.

Look for:
1. Icon-only buttons without aria-label
2. Buttons using only icons (no text content)
3. data-testid="new-conversation-button" - needs aria-label
4. Sidebar toggle buttons - need aria-label="Toggle sidebar"
```

#### Validation Criteria

- [ ] All buttons have accessible names (aria-label or visible text)
- [ ] axe-core reports 0 button-name violations
- [ ] Screen reader announces button purpose

---

### A11Y-02 — Focus trap in navigation

| Field | Value |
|-------|-------|
| **Severity** | `CRITICAL` |
| **WCAG** | 2.1.1 Keyboard |
| **Impact** | Keyboard users get stuck, cannot navigate |
| **Location** | Sidebar menu button |
| **Confidence** | HIGH — reproduced |

**What's broken:** When tabbing through the interface, focus gets trapped at the sidebar menu button. Users cannot continue navigating with Tab key.

**Trapped at:** `BUTTON.peer/menu-button`

#### Investigation Prompt

```
Focus trap at sidebar menu button.

Look for:
1. Event handlers preventing Tab propagation
2. Focus management in menu components
3. tabindex manipulation on menu open/close
4. ARIA menu pattern implementation
```

#### Validation Criteria

- [ ] Tab key navigates through entire interface without trapping
- [ ] Focus moves logically through all interactive elements
- [ ] No keyboard-only user gets stuck

---

### A11Y-03 — No skip link

| Field | Value |
|-------|-------|
| **Severity** | `SERIOUS` |
| **WCAG** | 2.4.1 Bypass Blocks |
| **Impact** | Keyboard users must tab through entire nav every page |
| **Confidence** | HIGH — not present |

**What's broken:** There is no "Skip to main content" link. Keyboard and screen reader users must tab through the entire sidebar navigation on every page before reaching the main content area.

#### Investigation Prompt

```
Skip link missing.

Look for:
1. No skip link at document start
2. Implement: <a href="#main" class="sr-only focus:not-sr-only">Skip to content</a>
3. Add id="main" to main content container
```

#### Validation Criteria

- [ ] Skip link appears as first focusable element
- [ ] Skip link visible when focused
- [ ] Activating skip link moves focus to main content

---

## Serious Findings

### A11Y-04 — Form inputs without labels

| Field | Value |
|-------|-------|
| **Severity** | `SERIOUS` |
| **WCAG** | 1.3.1 Info and Relationships |
| **Impact** | Screen readers don't announce field purpose |
| **Count** | 1 textarea per page |
| **Confidence** | HIGH — axe-core detected |

**What's broken:** The message textarea on Chat/Specialists pages has no associated label. Screen readers cannot tell users what the field is for.

**Affected element:** `textarea[name="message"]`

#### Investigation Prompt

```
Textarea missing label.

Look for:
1. textarea without associated <label>
2. Missing aria-label or aria-labelledby
3. Solution: <label for="message" class="sr-only">Message</label>
```

#### Validation Criteria

- [ ] All form inputs have associated labels
- [ ] axe-core reports 0 label violations
- [ ] Screen reader announces "Message" or equivalent when focused

---

### A11Y-05 — Focus visibility issues

| Field | Value |
|-------|-------|
| **Severity** | `SERIOUS` |
| **WCAG** | 2.4.7 Focus Visible |
| **Impact** | Keyboard users cannot see where they are |
| **Count** | 2 elements |
| **Confidence** | MEDIUM — visual check needed |

**What's broken:** Some buttons have no visible focus indicator. Keyboard users cannot tell which element is currently focused.

**Affected elements:**
- `BUTTON.group` — no visible focus
- Menu buttons after certain states

#### Investigation Prompt

```
Missing visible focus indicators.

Look for:
1. CSS :focus styles being overridden
2. outline: none without replacement focus style
3. Focus states on .group class buttons
4. Solution: :focus-visible { outline: 2px solid blue; }
```

#### Validation Criteria

- [ ] All interactive elements have visible focus state
- [ ] Focus ring visible in both light and dark modes
- [ ] 3:1 contrast ratio on focus indicator

---

## Per-Page Summary

| Page | Violations | Buttons w/o Names | Forms w/o Labels |
|------|------------|-------------------|------------------|
| Main Shell | 1 | 13 | 0 |
| Chat | 1 | 13 | 1 |
| Specialists | 1 | 13 | 1 |
| Knowledge | 1 | 13 | 1 |
| Workflows | 1 | 13 | 1 |
| Tasks | 1 | 13 | 1 |
| Settings | 2 | 14 | 1 |

**Note:** The 13 button issues are consistent across pages — they're in the global sidebar/shell, not page-specific.

---

## Manual Verification Needed

### UI-03 — Color contrast (Quick Start)

axe-core did not detect the contrast issue reported by manual testers (2.68:1 ratio in Workflows Quick Start). This requires manual verification with a contrast checker tool.

**Manual check:** Workflows → Quick Start → Step numbers
**Expected:** Contrast ratio < 4.5:1 (WCAG fail)

---

## WCAG 2.1 AA Compliance Matrix

| Criterion | Status | Notes |
|-----------|--------|-------|
| 1.1.1 Non-text Content | PASS | All images have alt text |
| 1.3.1 Info and Relationships | **FAIL** | Form inputs missing labels |
| 1.4.3 Contrast (Minimum) | Review | UI-03 needs manual check |
| 2.1.1 Keyboard | **FAIL** | Focus trap detected |
| 2.4.1 Bypass Blocks | **FAIL** | No skip link |
| 2.4.4 Link Purpose | PASS | Links have descriptive text |
| 2.4.6 Headings and Labels | PASS | Heading hierarchy correct |
| 2.4.7 Focus Visible | **FAIL** | Some elements lack focus state |
| 4.1.2 Name, Role, Value | **FAIL** | 13 buttons without names |

---

## Recommendations

### P1 — Must Fix (Critical)

1. **A11Y-01** — Add `aria-label` to all icon-only buttons
2. **A11Y-02** — Fix focus trap in sidebar menu

### P2 — Should Fix (Serious)

3. **A11Y-03** — Add skip link to bypass navigation
4. **A11Y-04** — Add labels to form inputs
5. **A11Y-05** — Add visible focus states to all buttons

### P3 — Should Verify

6. **UI-03** — Manually verify Quick Start contrast ratio

---

## Confidence Assessment

| Finding | Confidence | Reasoning |
|---------|------------|-----------|
| A11Y-01 Buttons | HIGH | axe-core detection + reproducible |
| A11Y-02 Focus trap | HIGH | Automated Tab test + reproducible |
| A11Y-03 Skip link | HIGH | DOM inspection confirms absence |
| A11Y-04 Form labels | HIGH | axe-core detection |
| A11Y-05 Focus visible | MEDIUM | Needs manual visual confirmation |

---

*Report generated 12 February 2026*
*Jimmy's Workflow: PRE-FLIGHT → IMPLEMENT → VALIDATE → CHECKPOINT*
*Tool: axe-core 4.x injected via Playwright CDP connection*
