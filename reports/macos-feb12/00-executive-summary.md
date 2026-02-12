---
title: "macOS 12 Feb — Executive Summary"
date: 2026-02-12
category: daily
tags: [macos, darwin, arm64, automated, 0.1.4-80b9f15, executive-summary]
author: Q (Automated)
status: complete
build_version: 0.1.4-80b9f15
platform: Darwin-arm64
---

# Zephyr Agency Nightly — QA Executive Summary

| Field | Value |
|-------|-------|
| **Version** | Zephyr Agency Nightly v0.1.4-80b9f15 |
| **Test Date** | 12 February 2026 |
| **QA Lead** | James Barclay (Q-A Methodology) |
| **Tester** | Q (Automated E2E) |
| **Platform** | macOS ARM64 (Apple Silicon) |
| **Test Steps** | 226 executed across 4 suites |

---

## How This Report Works

This report pack contains findings from automated E2E testing. It connects QA findings directly to your dev workflow.

### What was tested

Automated Playwright tests ran against Zephyr Agency Nightly v0.1.4-80b9f15 on macOS ARM64. Testing was black-box — no source access, only the shipped nightly build. Tests covered smoke checks, baseline functionality, regression detection, and security verification.

### Report structure

| File | Audience | Contains |
|------|----------|----------|
| `00-executive-summary.md` | Leadership | Verdict, numbers, recommendations |
| `01-authentication.md` — `08-installation.md` | Dev team | Issues with investigation prompts |

### The remediation workflow

Each issue includes a remediation section built on a six-phase workflow:

```
PRE-FLIGHT → INVESTIGATE → FIX → TEST → VALIDATE → CHECKPOINT
```

| Phase | What to do |
|-------|-----------|
| **PRE-FLIGHT** | Reproduce the bug. Confirm you can see the behaviour described. |
| **INVESTIGATE** | Copy the investigation prompt into your AI coding tool. |
| **FIX** | Implement the changes identified during investigation. |
| **TEST** | Write a regression test covering the fix. |
| **VALIDATE** | Verify the fix against the validation criteria. |
| **CHECKPOINT** | Confirm no regressions. Mark the issue as resolved. |

---

## Verdict

Zephyr Agency Nightly v0.1.4-80b9f15 on macOS has **two CRITICAL issues** that need immediate attention: the Package Manager registry is unavailable, and the Tasks page structure has changed (no kanban columns found). Authentication tests are blocked due to OAuth automation limitations — manual testing required. Overall pass rate is 71.7%.

---

## Severity Breakdown

| Severity | Count | Description |
|----------|-------|-------------|
| **CRITICAL** | 2 | Blocks core functionality |
| **HIGH** | 4 | Significant feature broken (auth blocked) |
| **MEDIUM** | 5 | Feature works but behaviour deviates |
| **LOW** | 5 | Cosmetic, UX polish, or test infrastructure |
| **Total** | **16** | Unique failures (deduplicated) |

---

## Pass Rate by Suite

| Suite | Tests | Pass | Fail | Skip | Pass Rate |
|-------|-------|------|------|------|-----------|
| Smoke | 46 | 46 | 0 | 0 | **100%** |
| Baseline | 86 | 74 | 0 | 12 | **100%** |
| Regressions | 80 | 32 | 30 | 18 | **51.6%** |
| Security | 14 | 10 | 2 | 2 | **83.3%** |
| **Total** | **226** | **162** | **32** | **32** | **71.7%** |

---

## CRITICAL Issues (Fix Immediately)

| ID | Area | Issue | Platforms |
|----|------|-------|-----------|
| SET-08 | Settings | Package Manager registry shows "unavailable" error | macOS |
| TASK-03 | Tasks | Tasks page has no kanban columns (To Do, In Progress, Done) | macOS |

---

## HIGH Issues (Blocked — Manual Testing Required)

| ID | Area | Issue | Reason |
|----|------|-------|--------|
| AUTH-01 | Authentication | Unauthenticated access tests failing | OAuth uses system browser |
| AUTH-03 | Authentication | Auth0 signup test failing | Can't automate external OAuth |
| AUTH-04 | Authentication | Google OAuth popup test failing | Can't automate external OAuth |
| CHAT-11 | Chat | Message persistence test failing | May be timing or real bug |

---

## MEDIUM Issues

| ID | Area | Issue |
|----|------|-------|
| SEC-01 | Security | XSS test selector inconsistent |
| SPEC-04 | Specialists | Count badges not visible |
| WORK-03 | Workflows | Visual Editor link not found |
| WORK-06 | Workflows | Execute button not found |

---

## LOW Issues

| ID | Area | Issue |
|----|------|-------|
| CHAT-03 | Chat | Placeholder mentions "tasks" in chat input |
| SET-02 | Settings | 11 tabs found (Language, Sandbox unexpected) |
| INST-05 | Installation | Sidebar toggle not found |
| WORK-02 | Workflows | Quick Start section not visible |
| WORK-04 | Workflows | Refresh button not found |

---

## Recommendations

1. **Immediate:** Fix SET-08 (Package Manager) and verify TASK-03 (Tasks structure)
2. **Manual testing:** All authentication flows need human verification
3. **Verify:** Are Language and Sandbox tabs in Settings intentional new features?
4. **Update tests:** Workflow page selectors need updating for new UI

---

## Evidence

| Type | Location |
|------|----------|
| JSON Results | `macOS/2026-02-12-0854-*-darwin-arm64-results.json` |
| Screenshots | `e2e-test/test-results/` |
| Database | `results/qa.db` |

---

*Report generated 12 Feb 2026 by Q (Automated)*
