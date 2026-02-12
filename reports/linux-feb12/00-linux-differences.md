---
title: "Linux 12 Feb — Platform Differences"
date: 2026-02-12
category: linux-feb12
tags: [linux, ubuntu, automated, 0.1.4-80b9f15, differences]
author: Q (Automated)
status: complete
build_version: 0.1.4-80b9f15
platform: Linux-x64
---

# Linux — Platform Differences Report

| Field | Value |
|-------|-------|
| **Platform** | Linux x64 (Ubuntu 24.04 on Beast) |
| **Build** | 0.1.4-80b9f15 |
| **Date** | 12 February 2026 |
| **Tester** | Q (Automated via VNC) |

---

## Summary

**Linux testing confirms all macOS failures, plus 1 Linux-specific requirement.**

| Comparison | Result |
|------------|--------|
| Smoke tests | 44 pass, 2 timeout (screenshot) |
| Regression tests | 4 pass, 21 fail (SKIP_AUTH mode) |
| Linux-specific | 1 (sandbox flag required) |
| Manual bugs | 0 (no manual testing) |

For the shared failures, refer to the macOS reports — the issues are identical.

---

## Linux-Specific Issues

### LINUX-01 — App requires --no-sandbox flag to launch

| Field | Value |
|-------|-------|
| **Severity** | `HIGH` |
| **Platform** | Linux (Ubuntu 24.04+) |
| **Confidence** | HIGH — required for launch |
| **macOS/Windows Status** | N/A |

**What's broken:** The Zephyr AppImage cannot launch on Ubuntu 24.04 without the `--no-sandbox` flag. This is due to Ubuntu's unprivileged user namespace restrictions (`kernel.unprivileged_userns_clone=0`).

**Error without flag:**
```
[62convey:1]: crash_log_path = /tmp/electron-crashpad-gLEyBo/crash.dmp
unshare(CLONE_NEWPID): EPERM
The Linux kernel is configured to reject CLONE_NEWPID requests.
```

**Workaround:** Launch with:
```bash
./zephyr-agency_0.1.4-80b9f15_amd64.AppImage --no-sandbox
```

**Root cause:** Ubuntu 24.04 restricts unprivileged user namespaces by default for security. Chromium-based apps (Electron/Tauri) use sandboxing that requires this capability.

#### Investigation Prompt

```
The Zephyr AppImage requires --no-sandbox on Ubuntu 24.04+ to launch.

Look for:
1. Electron/Tauri sandbox configuration
2. AppImage packaging settings for Linux
3. SUID sandbox helper binary inclusion
4. Whether Chromium sandbox can be disabled by default on Linux

Options:
1. Include SUID sandbox helper in AppImage (requires setuid root)
2. Detect user namespace restriction and auto-disable sandbox
3. Document the --no-sandbox requirement for affected distros
4. Package as .deb/.rpm with proper sandbox setup

Reference:
- https://chromium.googlesource.com/chromium/src/+/master/docs/linux/sandboxing.md
- Ubuntu 24.04 apparmor-profiles-extra package
```

#### What to Assert (Regression Test)

```javascript
test('LINUX-01: App launches on Linux without manual intervention', async () => {
  // This is a packaging/distribution test
  // The AppImage should either:
  // 1. Launch without --no-sandbox flag, OR
  // 2. Auto-detect restriction and handle gracefully

  // Manual verification needed for each Linux distro
  // - Ubuntu 24.04: Currently requires --no-sandbox
  // - Fedora 40: Untested
  // - Debian 12: Untested
});
```

#### Validation Criteria

- [ ] AppImage launches on Ubuntu 24.04 without --no-sandbox
- [ ] OR: Error message clearly explains the requirement
- [ ] OR: Documentation covers Linux sandbox requirements

---

### LINUX-02 — Screenshot capture times out

| Field | Value |
|-------|-------|
| **Severity** | `LOW` |
| **Platform** | Linux x64 |
| **Confidence** | MEDIUM — may be VNC-related |
| **macOS/Windows Status** | PASS |

**What's broken:** The smoke test for "captures current state screenshot" times out on Linux. This may be VNC display configuration or a real issue.

**Tester evidence:**
- Automation: Test status = `timedOut` (2 instances)

**Note:** This could be test infrastructure (VNC display) rather than app issue. Needs verification on physical Linux machine.

#### Validation Criteria

- [ ] Screenshot capture works on physical Linux machine
- [ ] OR: Confirmed as VNC/test infrastructure issue

---

## Shared Failures (See macOS Reports)

These issues are **identical across all platforms**. Refer to the macOS area reports for full details:

| ID | Issue | Severity | macOS Report |
|----|-------|----------|--------------|
| SET-08 | Package Manager "registry unavailable" | HIGH | 02-settings.md |
| TASK-03 | Kanban columns not found | MEDIUM | 07-tasks.md |
| SPEC-04 | Specialist count badges missing | MEDIUM | 04-specialists.md |
| WORK-06 | Execute button not found | MEDIUM | 06-workflows.md |
| CHAT-03 | Chat placeholder mentions "tasks" | LOW | 03-chat.md |
| INST-05 | Sidebar toggle not found | LOW | 08-installation.md |
| SET-02 | New Settings tabs (Language, Sandbox) | LOW | 02-settings.md |
| WORK-02 | Quick Start section not found | LOW | 06-workflows.md |
| WORK-03 | Visual Editor link not found | LOW | 06-workflows.md |
| WORK-04 | Refresh button not found | LOW | 06-workflows.md |

**Auth-related failures (expected in SKIP_AUTH mode):**

| ID | Issue | Note |
|----|-------|------|
| AUTH-01 | Unauthenticated content access | Requires manual auth |
| AUTH-03 | Auth0 signup flow | Requires manual auth |
| AUTH-04 | Google OAuth flow | Requires manual auth |
| KNOW-01 | Knowledge icon visibility | May require auth |
| KNOW-07 | Empty state text | May require auth |

---

## Test Environment

| Component | Details |
|-----------|---------|
| Host | Beast (100.71.79.25) |
| OS | Ubuntu 24.04 LTS |
| Display | VNC on port 5900 |
| CDP | Port 9222 (SSH tunnel to Mac) |
| Launch | `./zephyr.AppImage --no-sandbox --disable-gpu` |
| Auth Mode | SKIP_AUTH (unauthenticated) |

---

## Recommendations

### P1 — Linux-Specific Fixes

1. **LINUX-01** — Investigate AppImage sandbox requirements for Ubuntu 24.04+
   - Options: SUID helper, auto-detection, or documentation

### P2 — Cross-Platform Fixes

See macOS reports for shared issues (SET-08, TASK-03, etc.).

### P3 — Test Infrastructure

1. **LINUX-02** — Verify screenshot issue on physical Linux (not VNC)

---

*Report generated 12 Feb 2026*
*Tester: Q (Automated via VNC)*
