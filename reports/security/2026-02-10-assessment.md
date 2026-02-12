---
title: "Security Assessment"
date: 2026-02-10
category: security
tags: [security, xss, auth, critical]
author: James Barclay
status: complete
build_version: 0.1.4-d1b149a
---

# Security Assessment — Zephyr Agency Nightly

**Document:** SECURITY-ASSESSMENT-2026-02-10.md
**Version:** 0.1.4-d1b149a
**Assessment Date:** 2026-02-10
**Source:** Manual testing (Robin, Windows 11, 6 Feb 2026)
**Classification:** Security-First Review

---

## Executive Summary

This document consolidates all security-relevant findings from manual testing that were **not previously elevated to formal bug reports**. Per project severity definitions, security issues are **CRITICAL** and should **block release**.

| Severity | Count | Action |
|----------|-------|--------|
| **CRITICAL** | 4 | Block release |
| **HIGH** | 2 | Must fix before release |
| **MEDIUM** | 1 | Should fix |

**Reference:** `docs/QA-PLAN-EXECUTIVE-SUMMARY.md:344` defines CRITICAL as "Crash, data loss, **security** → Block release"

---

## CRITICAL Severity (Block Release)

### SEC-01: XSS — Unsanitized Markdown in Task Descriptions

| Field | Value |
|-------|-------|
| **ID** | SEC-01 |
| **Severity** | CRITICAL |
| **Category** | Cross-Site Scripting (XSS) |
| **Area** | Tasks (`/tasks`) |
| **Source** | `results/manual-testing/robin-win11-6feb/07-tasks.md:236` |

**Description:**
Task description markdown is rendered without sanitization. Raw HTML/JavaScript can be injected and executed in the application context.

**Impact:**
- Arbitrary JavaScript execution in user's session
- Session hijacking potential
- Data exfiltration via malicious task content
- If tasks sync across users: stored XSS affecting all team members

**Evidence:**
> "Description markdown **is not sanitized**. This is a security vulnerability."
> Screenshots: `07-tasks-media/media/image4.png`, `07-tasks-media/media/image6.png`

**Recommendation:**
Sanitize all user-supplied markdown before rendering. Use a library like DOMPurify or sanitize-html. Implement Content Security Policy (CSP) headers.

---

### SEC-02: Privacy — IP Address Exposed in QR Code Authentication

| Field | Value |
|-------|-------|
| **ID** | SEC-02 |
| **Severity** | CRITICAL |
| **Category** | Privacy / GDPR Violation |
| **Area** | Authentication (`/auth`) |
| **Source** | `results/manual-testing/robin-win11-6feb/01-authentication.md:159` |

**Description:**
QR code authentication URL contains plaintext IP address visible on screen:
```
zephyr://qr-auth?code=...&ip=...&country=NL
```

**Impact:**
- GDPR Article 4(1) classifies IP addresses as personal data
- Shoulder-surfing exposure during screen sharing, presentations, or streaming
- Geographic location disclosure via IP geolocation
- Potential legal liability in EU jurisdictions

**Evidence:**
> "Note this plain text IP address may have GDPR implications. Especially if leaked through over-the-shoulder watching or streaming / screen sharing."
> Tester noted they had to mask the QR code for privacy reasons.

**Recommendation:**
- Remove IP from QR code URL (use server-side session binding instead)
- If IP is required, hash or encrypt it
- Add privacy warning before displaying QR code
- Consider GDPR compliance review for all authentication flows

---

### SEC-03: Data Loss — Offline Messages Lost on App Restart

| Field | Value |
|-------|-------|
| **ID** | SEC-03 |
| **Severity** | CRITICAL |
| **Category** | Data Loss |
| **Area** | Chat (`/chat`) |
| **Source** | `results/manual-testing/robin-win11-6feb/03-chat.md:248` |

**Description:**
Messages composed while offline are lost when the application restarts. The local-first architecture fails to persist unsent messages.

**Impact:**
- User work product destroyed without warning
- Undermines local-first promise (Loro CRDT should persist locally)
- Trust erosion: users cannot rely on drafts surviving crashes
- Potential loss of critical communications

**Evidence:**
> "There's no indication that messages are queued, though the messages are preserved locally. When connection is restored, it seems to be OK. But when restarting the application all offline messages are lost."

**Recommendation:**
- Persist all message drafts to local CRDT before restart
- Implement application shutdown hooks to flush pending state
- Add visual indicator for unsent/unsynced messages
- Test crash recovery scenarios

---

### SEC-04: Authentication — Signup Flow Redirects to 403

| Field | Value |
|-------|-------|
| **ID** | SEC-04 |
| **Severity** | CRITICAL |
| **Category** | Broken Authentication |
| **Area** | Authentication (`/auth`) |
| **Source** | `results/manual-testing/robin-win11-6feb/01-authentication.md:112` |

**Description:**
New user signup via email/password flow redirects to a 403 Forbidden page after password creation.

**Impact:**
- Users cannot complete registration
- First-run experience broken
- Potential user abandonment
- May indicate misconfigured Auth0 rules or ACL

**Evidence:**
> "Initial signup sent a confirmation code to spam folder. Choosing a password has a policy incompatible with 1password default generator (requiring special characters). With a password set, redirected to a 403 page."

**Recommendation:**
- Review Auth0 redirect rules and allowed callback URLs
- Test complete signup flow in all supported auth methods
- Ensure password policy is documented and compatible with password managers

---

## HIGH Severity (Must Fix Before Release)

### SEC-05: Data Isolation — Tasks Shared Across All Workspaces

| Field | Value |
|-------|-------|
| **ID** | SEC-05 |
| **Severity** | HIGH |
| **Category** | Data Isolation Failure |
| **Area** | Tasks (`/tasks`) |
| **Source** | `results/manual-testing/robin-win11-6feb/07-tasks.md:239` |

**Description:**
Tasks created in one workspace are visible in all other workspaces. No workspace-level data isolation.

**Impact:**
- Confidential project information visible across unrelated workspaces
- If workspaces are shared with different team members: data leakage between teams
- Violates principle of least privilege
- Could expose sensitive client information in agency contexts

**Evidence:**
> "Tasks are also the same across all workspaces."

**Recommendation:**
- Implement workspace-scoped data storage for tasks
- Audit other features (Knowledge, Workflows) for same issue
- Add workspace filter to all data queries

---

### SEC-06: ACL Misconfiguration — Shell Command Blocked But Error Exposed

| Field | Value |
|-------|-------|
| **ID** | SEC-06 |
| **Severity** | HIGH |
| **Category** | Information Disclosure |
| **Area** | Settings (`/settings`) |
| **Source** | `results/manual-testing/robin-win11-6feb/02-settings.md:259` |

**Description:**
Clicking "GitHub Token Settings" link triggers console error:
```
Uncaught (in promise) Command plugin:shell|open not allowed by ACL
```

**Impact:**
- Internal Tauri ACL configuration leaked to console
- Reveals security boundary information to potential attackers
- Indicates incomplete feature (link exists but doesn't work)
- Poor user experience (silent failure)

**Evidence:**
> "Clicking the 'GitHub Token Settings' link does not respond. The console shows error: Uncaught (in promise) Command plugin:shell|open not allowed by ACL"

**Recommendation:**
- Either enable the shell:open command in ACL, or remove the link
- Catch ACL errors and display user-friendly message
- Audit all external links for same issue

---

## MEDIUM Severity (Should Fix)

### SEC-07: Credential Handling — Password Manager Intercepts API Key

| Field | Value |
|-------|-------|
| **ID** | SEC-07 |
| **Severity** | MEDIUM |
| **Category** | Credential Handling |
| **Area** | Settings (`/settings`) |
| **Source** | `results/manual-testing/robin-win11-6feb/02-settings.md:209` |

**Description:**
Browser password manager (Chromium-based) offers to save the OpenRouter API key when entered, and opens internal Chromium password manager page.

**Impact:**
- API keys may be stored in browser password manager alongside regular passwords
- Users may not realize API keys are being saved
- If browser is compromised, API keys are exposed
- Mixing credential types reduces security posture

**Evidence:**
> "Password manager offers to save the API key. Clicking the Password Manager link on this prompt, opens up Chromium at URL chrome://password-manager/passwords"

**Recommendation:**
- Add `autocomplete="off"` to API key input fields
- Consider using Tauri's secure storage (keychain) instead of Chromium
- Document API key storage location for users

---

## Tracking Status

| ID | Severity | Status | Filed In |
|----|----------|--------|----------|
| SEC-01 | CRITICAL | **NEW** | This document |
| SEC-02 | CRITICAL | **NEW** | This document |
| SEC-03 | CRITICAL | **NEW** | This document |
| SEC-04 | CRITICAL | **NEW** | This document |
| SEC-05 | HIGH | **NEW** | This document |
| SEC-06 | HIGH | **NEW** | This document |
| SEC-07 | MEDIUM | **NEW** | This document |

---

## Appendix: Source Files

All findings extracted from:
- `results/manual-testing/robin-win11-6feb/01-authentication.md`
- `results/manual-testing/robin-win11-6feb/02-settings.md`
- `results/manual-testing/robin-win11-6feb/03-chat.md`
- `results/manual-testing/robin-win11-6feb/07-tasks.md`

---

## Next Steps

1. **Immediate:** Triage with CrabNebula dev team
2. **Prioritize:** SEC-01 (XSS) and SEC-03 (data loss) are highest risk
3. **Review:** Audit other areas for similar patterns
4. **Track:** Add to formal issue tracker with SECURITY label

---

*Generated: 2026-02-10*
*QA Lead: James Barclay*
*Assessment: Q*
