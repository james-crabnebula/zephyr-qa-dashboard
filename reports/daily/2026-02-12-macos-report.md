# macOS Test Report

**Build:** 0.1.4-80b9f15
**Platform:** macOS (Apple Silicon)
**Date:** 12 February 2026
**Tester:** Automated (Q)

---

## Summary

We ran the full automated test suite against the new macOS build. The app launches and basic functionality works, but we found some issues that need attention.

**Pass rate: 71.7%**

| Suite | Result |
|-------|--------|
| Smoke | All 46 tests passed |
| Baseline | All 74 tests passed (12 skipped) |
| Regressions | 32 passed, 30 failed |
| Security | 10 passed, 2 failed |

---

## What's Working

- App launches successfully
- Navigation between all main sections works
- Settings dialog opens and closes properly
- Most security checks pass (QR codes, workspace isolation, API key handling)
- Knowledge Garden displays correctly
- Specialist creation flow works

---

## What's Broken

### Critical

**Package Manager not working**
When you go to Settings → Package Manager → Browse, it shows "unavailable" error. Users can't browse or install packages.

**Tasks page has changed**
The Tasks page no longer shows the expected kanban columns (To Do, In Progress, Done). Either the design changed or something broke.

### High Priority

**Chat messages may not persist**
When sending a message and reloading the page, the message doesn't appear. This could be a timing issue in the test or an actual bug — needs manual verification.

### Medium Priority

**Workflow page navigation issues**
Several tests couldn't find the Visual Editor link, Execute button, or Quick Start section. The page structure may have changed.

**Specialist count badges missing**
The count badges on the Specialists page aren't showing up.

### Low Priority

**Chat placeholder text**
The chat input says "Start a task or invite some people..." — it mentions tasks, but this is a chat input. Minor copy issue.

**New Settings tabs**
Found 2 new tabs in Settings: Language and Sandbox. Test expected 9 tabs, found 11. If these are intentional new features, just need to update the test.

---

## Blocked — Needs Manual Testing

**All authentication tests failed**
The OAuth/Auth0 login uses the system browser, which our automation can't control. These all need manual testing:
- Sign in / Sign out flow
- Google OAuth popup
- Auth0 signup
- Unauthenticated access protection

---

## Recommendations

1. **Check the Package Manager** — Is the registry supposed to be available?
2. **Check the Tasks page** — Was the kanban view intentionally removed or changed?
3. **Manual auth testing** — Someone needs to manually test all login flows
4. **Test chat persistence** — Send a message, reload, check if it's still there

---

## Files

All raw test data is in this folder:
- `2026-02-12-0854-smoke-darwin-arm64-results.json`
- `2026-02-12-0854-baseline-darwin-arm64-results.json`
- `2026-02-12-0854-regressions-darwin-arm64-results.json`
- `2026-02-12-0854-security-darwin-arm64-results.json`

---

*Report generated 12 Feb 2026*
