---
title: "macOS 12 Feb — Settings"
date: 2026-02-12
category: daily
tags: [macos, darwin, arm64, automated, 0.1.4-80b9f15, settings]
author: Q (Automated)
status: complete
build_version: 0.1.4-80b9f15
platform: Darwin-arm64
---

# Settings — Area Report

| Field | Value |
|-------|-------|
| **Suite** | 02-settings |
| **Test IDs** | SET-02, SET-04, SET-06, SET-08, SET-09 |
| **Platform Tested** | macOS ARM64 |
| **Tester** | Q (Automated) |

---

## Suite Results

| Metric | Value |
|--------|-------|
| Total tests | 10 (5 test IDs × 2 modes) |
| Passed | 6 |
| Failed | 4 |
| Skipped | 2 |
| **Pass rate** | **60%** |

---

## How to Use This Report

Each issue below follows a structured remediation workflow:

```
PRE-FLIGHT → INVESTIGATE → FIX → TEST → VALIDATE → CHECKPOINT
```

---

## Issues

### SET-08 — Package Manager Browse tab shows registry error

| Field | Value |
|-------|-------|
| **Severity** | `CRITICAL` |
| **Test ID** | SET-08 |
| **Platform** | macOS ARM64 |
| **Confidence** | HIGH — error clearly visible |

**What's broken:** When navigating to Settings → Package Manager → Browse tab, the registry shows an "unavailable" error. Users cannot browse or install packages.

**Tester evidence:**
- Automation: *"SET-08 BUG: Package registry shows 'unavailable' error"*
- Screenshot: `test-results/regressions-settings.regre-8ee63-oes-not-show-registry-error-headless/test-failed-1.png`

**Cross-platform:** Needs verification on Windows and Linux.

#### Investigation Prompt

```
Search for Package Manager implementation, specifically the Browse tab and
registry connection logic. On macOS, the Package Manager → Browse tab shows
an "unavailable" error instead of listing available packages.

Look for:
1. Registry URL configuration (may be hardcoded or from config)
2. Network request error handling in Package Manager component
3. Any authentication/API key requirements for the registry
4. CORS or network security settings that might block the request

The error text contains "unavailable" — search for this string to find the
error handling code path.

Expected behaviour: Browse tab should display a list of available packages
from the registry.
```

#### What to Assert (Regression Test)

```javascript
test('SET-08: Package Manager Browse tab loads packages', async () => {
  // Navigate to Settings → Package Manager
  await zephyr.openSettings();
  await page.click('[data-testid="settings-tab-packagemanager"]');

  // Click Browse tab
  await page.click('text=Browse');
  await page.waitForTimeout(2000);

  // Should NOT show "unavailable" error
  const content = await page.textContent('body');
  expect(content.toLowerCase()).not.toContain('unavailable');

  // Should show package list OR loading state
  const hasPackages = await page.$('[class*="package"]');
  const hasLoading = content.includes('Loading');
  expect(hasPackages || hasLoading).toBeTruthy();
});
```

#### Validation Criteria

- [ ] Package Manager → Browse tab loads without error
- [ ] Package list is visible OR clear loading state
- [ ] No "unavailable" error message

---

### SET-02 — Settings has unexpected number of tabs

| Field | Value |
|-------|-------|
| **Severity** | `LOW` |
| **Test ID** | SET-02 |
| **Platform** | macOS ARM64 |
| **Confidence** | HIGH — count verified |

**What's observed:** Settings dialog has 11 tabs. Test expected 9 tabs. Two unexpected tabs found: Language, Sandbox.

**Tester evidence:**
- Automation: *"SET-02 BUG: Found 2 unexpected tab(s): Language, Sandbox. Total tabs: 11, Expected: 9"*

**Is this a bug?** Possibly not — these may be intentional new features. Needs clarification from dev team.

#### Investigation Prompt

```
Check if Language and Sandbox tabs in Settings are intentional new features.

If intentional:
- Update test to expect 11 tabs
- Add Language and Sandbox to expected tabs list

If not intentional:
- These tabs should be hidden or removed
- Check settings/tabs configuration
```

#### Validation Criteria

- [ ] Confirm Language tab is intentional OR remove
- [ ] Confirm Sandbox tab is intentional OR remove
- [ ] Update test to match intended tab count

---

## Passing Tests

| Test ID | Description | Status |
|---------|-------------|--------|
| SET-06 | Billing section has content | PASS |
| SET-09 | GitHub token link no ACL error | PASS |

---

*Report generated 12 Feb 2026 by Q (Automated)*
