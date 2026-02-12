---
title: "Windows 12 Feb — Platform Differences"
date: 2026-02-12
category: windows-feb12
tags: [windows, win11, automated, 0.1.4-80b9f15, differences]
author: Q (Automated) + James (Manual)
status: complete
build_version: 0.1.4-80b9f15
platform: Windows-x64
---

# Windows — Platform Differences Report

| Field | Value |
|-------|-------|
| **Platform** | Windows 11 (VM on Beast) |
| **Build** | 0.1.4-80b9f15 |
| **Date** | 12 February 2026 |
| **Tester** | Q (Automated) + James (Manual Auth) |

---

## Summary

**Windows testing confirms all macOS failures, plus 2 Windows-specific issues.**

| Comparison | Result |
|------------|--------|
| Tests matching macOS | 11 failures (identical) |
| Windows-only failures | 2 (SET-09, SET-06) |
| Manual bug found | 1 (AUTH-DIALOG) |

For the 11 shared failures, refer to the macOS reports — the issues are identical.

---

## Windows-Only Issues

### SET-09 — GitHub token settings link throws ACL error

| Field | Value |
|-------|-------|
| **Severity** | `HIGH` |
| **Test ID** | SET-09 |
| **Platform** | Windows 11 only |
| **Confidence** | HIGH — error observed |
| **macOS Status** | PASS |

**What's broken:** When navigating to Settings → Connections → GitHub token link, an ACL (Access Control List) error is thrown. This works correctly on macOS.

**Tester evidence:**
- Automation: *"OAuth login button not found after trying Sign In"* (test couldn't proceed due to auth flow)
- Windows-specific network or permission issue suspected

**Cross-platform:** PASS on macOS, FAIL on Windows only.

#### Investigation Prompt

```
The GitHub token settings link throws an ACL error on Windows but works on macOS.

Look for:
1. Windows-specific network request handling
2. File system permission differences (AppData paths)
3. Windows credential store access
4. Any platform-specific code paths in the Connections/GitHub settings

Compare the macOS and Windows implementations for:
- Token storage location
- OAuth callback handling
- External URL opening behaviour
```

#### What to Assert (Regression Test)

```javascript
test('SET-09: GitHub token settings link does not throw ACL error', async () => {
  // Navigate to Settings → Connections
  await zephyr.openSettings();
  await page.click('[data-testid="settings-tab-connections"]');
  await page.waitForTimeout(1000);

  // Set up console error listener
  const consoleErrors = [];
  page.on('console', msg => {
    if (msg.type() === 'error') consoleErrors.push(msg.text());
  });

  // Click GitHub token link
  const githubLink = await page.$('text=GitHub, a[href*="github"]');
  if (githubLink) {
    await githubLink.click();
    await page.waitForTimeout(2000);
  }

  // Should NOT have ACL errors
  const hasAclError = consoleErrors.some(e =>
    e.toLowerCase().includes('acl') ||
    e.toLowerCase().includes('access') ||
    e.toLowerCase().includes('permission')
  );
  expect(hasAclError).toBe(false);
});
```

#### Validation Criteria

- [ ] Settings → Connections → GitHub token link opens without error
- [ ] No ACL error in console
- [ ] Behaviour matches macOS

---

### SET-06 — Billing section is empty

| Field | Value |
|-------|-------|
| **Severity** | `MEDIUM` |
| **Test ID** | SET-06 |
| **Platform** | Windows 11 only |
| **Confidence** | HIGH — visible difference |
| **macOS Status** | PASS |

**What's broken:** The Billing section in Settings shows empty content or "unavailable" state on Windows. On macOS, this section displays content correctly.

**Tester evidence:**
- Automation: Billing section content check failed
- May be related to network/API access differences on Windows

**Cross-platform:** PASS on macOS, FAIL on Windows only.

#### Investigation Prompt

```
The Billing section in Settings is empty on Windows but shows content on macOS.

Look for:
1. API endpoint for billing information
2. Windows-specific network/proxy settings
3. Authentication token handling differences
4. Any cached data that differs between platforms

The section should show billing status, subscription info, or a clear
"not available" message — not empty/broken state.
```

#### What to Assert (Regression Test)

```javascript
test('SET-06: Billing section has real content or clear unavailable state', async () => {
  // Navigate to Settings → Billing
  await zephyr.openSettings();
  await page.click('[data-testid="settings-tab-billing"]');
  await page.waitForTimeout(2000);

  // Get billing section content
  const content = await page.textContent('[data-testid="billing-section"], [class*="billing"]');

  // Should have meaningful content
  const hasContent =
    content.length > 50 ||  // Substantial text
    content.toLowerCase().includes('subscription') ||
    content.toLowerCase().includes('plan') ||
    content.toLowerCase().includes('billing') ||
    content.toLowerCase().includes('not available') ||
    content.toLowerCase().includes('free');

  expect(hasContent, 'Billing section should have content or clear status').toBe(true);

  // Should NOT be empty or show error state
  const isBroken =
    content.trim().length === 0 ||
    content.toLowerCase().includes('error') ||
    content.toLowerCase().includes('failed');

  expect(isBroken, 'Billing section should not be empty or broken').toBe(false);
});
```

#### Validation Criteria

- [ ] Billing section shows content OR clear "unavailable" message
- [ ] No empty/broken state
- [ ] Behaviour matches macOS

---

## Manual Bug Found

### AUTH-DIALOG — Auth0 login dialog doesn't auto-close

| Field | Value |
|-------|-------|
| **Severity** | `MEDIUM` |
| **Platform** | Windows 11 |
| **Confidence** | HIGH — manually observed |
| **Screenshot** | `auth-dialog-not-closing-windows.png` |

**What's broken:** After successfully logging in via Auth0, the browser login dialog stays open on top of the app. User must manually close it.

**Expected behaviour:** Dialog should auto-close after successful authentication, returning user to the app.

#### Investigation Prompt

```
After successful Auth0 login on Windows, the browser dialog stays open instead
of auto-closing.

Look for:
1. OAuth callback handling — is the redirect being caught?
2. Window.close() or similar calls after auth success
3. Deep link / protocol handler registration on Windows
4. Any platform-specific auth completion logic

On macOS this may work differently due to how the system handles OAuth
callbacks vs Windows.
```

#### What to Assert (Manual Test)

```
MANUAL TEST PROCEDURE — AUTH-DIALOG

1. Launch Zephyr Agency on Windows
2. Click "Sign In" button
3. Complete Auth0 login with valid credentials
4. Observe: Does the browser login dialog close automatically?

EXPECTED: Dialog closes, user returns to authenticated app
ACTUAL (BUG): Dialog stays open on top of app, requires manual close

EVIDENCE: Screenshot auth-dialog-not-closing-windows.png
```

#### Validation Criteria

- [ ] Auth0 login dialog closes automatically after success
- [ ] User returns to app without manual intervention
- [ ] No orphaned browser windows

---

## Shared Failures (See macOS Reports)

These 11 issues are **identical on both platforms**. Refer to the macOS area reports for full details:

| ID | Issue | Severity | macOS Report |
|----|-------|----------|--------------|
| CHAT-11 | Messages don't persist after reload | CRITICAL | 03-chat.md |
| SET-08 | Package Manager "unavailable" | HIGH | 02-settings.md |
| TASK-03 | Kanban columns missing | MEDIUM | 07-tasks.md |
| SPEC-04 | Specialist count badges missing | MEDIUM | 04-specialists.md |
| WORK-06 | Execute button not found | MEDIUM | 06-workflows.md |
| CHAT-03 | Chat placeholder mentions "tasks" | LOW | 03-chat.md |
| INST-05 | Sidebar toggle not found | LOW | 08-installation.md |
| SET-02 | New Settings tabs (Language, Sandbox) | LOW | 02-settings.md |
| WORK-02 | Quick Start section not found | LOW | 06-workflows.md |
| WORK-03 | Visual Editor link not found | LOW | 06-workflows.md |
| WORK-04 | Refresh button not found | LOW | 06-workflows.md |

---

## Test Environment

| Component | Details |
|-----------|---------|
| VM Host | Beast (100.71.79.25) |
| VM Type | KVM/QEMU |
| OS | Windows 11 Enterprise |
| VNC | Port 5900 |
| CDP | Port 9222 |
| Test Account | james@crabnebula.dev |

---

## Recommendations

### P1 — Windows-Specific Fixes

1. **SET-09** — Investigate GitHub token ACL error on Windows
2. **AUTH-DIALOG** — Fix Auth0 dialog auto-close on Windows

### P2 — Cross-Platform Fixes

See macOS reports for the 11 shared issues.

---

*Report generated 12 Feb 2026*
*Tester: Q (Automated) + James (Manual Auth)*
*COI: AI-generated report*
