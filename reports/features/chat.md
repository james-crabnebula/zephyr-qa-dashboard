---
title: "Chat Test Report"
date: 2026-02-09
category: features
tags: [chat, messaging, conversation]
author: James Barclay
status: complete
build_version: 0.1.4
---

# Chat — Area Report

| Field | Value |
|-------|-------|
| **Suite** | 03-chat |
| **Test IDs** | CHAT-01, CHAT-03, CHAT-06, CHAT-08, CHAT-11 |
| **Platforms Tested** | macOS ARM64, Windows 11 |
| **Sessions** | Alex (macOS, Day 1), Cristian (macOS, Day 1 + Day 2), Robin (Win11, Day 2) |

---

## Suite Results

| Metric | Value |
|--------|-------|
| Total steps | 98 |
| Passed | 63 |
| Failed | 18 |
| Blocked | 4 |
| N/A | 13 |
| **Pass rate** | **74.1%** (worst of all suites) |

### Per-Session Breakdown

| Session | Steps | Pass | Fail | Blocked | N/A |
|---------|-------|------|------|---------|-----|
| Alex (macOS, Day 1) | 17 | 14 | 2 | 1 | 0 |
| Cristian (macOS, Day 1) | 17 | 4 | 0 | 0 | 13 |
| Cristian (macOS, Day 2) | 17 | 14 | 3 | 0 | 0 |
| Robin (Win11, Day 2) | 47 | 31 | 13 | 3 | 0 |

Chat is the weakest area in the entire application. Two CRITICAL issues — the core AI feature is non-functional and offline messages are lost.

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

### CHAT-06 — AI chat messaging completely non-functional

| Field | Value |
|-------|-------|
| **Severity** | `CRITICAL` |
| **Test ID** | CHAT-06 |
| **Platforms** | Windows 11, macOS ARM64 |
| **Found by** | Robin van Boven (Win11), Alex Lohr (macOS), Cristian Vogel (macOS Day 2) |

**What's broken:** Sending messages to the AI assistant produces no response. The first click sometimes does nothing at all (message doesn't even appear). Subsequent attempts show the user's message but no AI response and no loading indicators. No model selector is visible (CHAT-04). The attachment button is non-functional (CHAT-05).

**Tester evidence:**
- Robin (Win11): *"First time sending any message (clicking the arrow), nothing happened. My own message did not appear, no AI response. Subsequent attempt, only my message appears. No reply, no loading indicators."*
- Alex (macOS): AI response fails to generate.
- Cristian (Day 2): *"No response."*

**Cross-platform:** Broken on all tested platforms (Windows 11, macOS ARM64). This is the application's core feature and it does not work anywhere.

#### Investigation Prompt

```
Search the codebase for the AI chat message handling — this is the core feature
and it is completely broken on all platforms. Sending a message to the AI
produces no response.

Symptoms:
- First message click sometimes does nothing (message not sent)
- Subsequent messages appear in the UI but no AI response arrives
- No loading indicators shown
- No model selector visible in the UI
- Attachment button non-functional

Check:
1. Message send handler — find the function that sends user messages. Verify it
   dispatches to the AI backend. Check if it awaits a response or fires and
   forgets.
2. AI backend integration — is the AI provider (OpenRouter, OpenAI, or local)
   configured and reachable? Check for API key configuration, endpoint URLs,
   and error handling. If the API call fails silently, that would explain the
   missing response.
3. Streaming response handler — if responses are streamed (SSE or WebSocket),
   check the event listener is registered and processing chunks. A missing
   or broken stream handler would show the user message but no AI response.
4. Model selection — search for model selector component. If no model is
   selected/defaulted, the API call may fail because no model parameter is
   sent.
5. Error swallowing — look for try/catch blocks around the AI API call that
   catch errors without surfacing them to the UI. The absence of loading
   indicators or error messages suggests errors are being swallowed.
6. First-click issue — the first click doing nothing suggests a state
   initialization problem. Check if the chat component needs to establish a
   connection or session before the first message can be sent.

Expected: User sends message, loading indicator appears, AI response streams
back and renders in the chat.
```

#### Test

Write regression tests that assert:
- Sending a message to the AI produces a visible response in the chat window.
- A loading indicator appears while waiting for the AI response.
- The first message in a new session sends successfully (no silent failure on first click).
- If the AI backend is unavailable or misconfigured, the user sees a clear error message — not silence.
- The model selector is visible and a default model is selected.

#### Validate

- [ ] Send a message on macOS. AI responds with visible content.
- [ ] Send a message on Windows 11. AI responds with visible content.
- [ ] First message in a fresh session works (no need to click twice).
- [ ] Loading indicator visible while response is being generated.
- [ ] If AI backend is unreachable, an error message appears.

#### Checkpoint

AI chat is functional on all platforms. Messages send on first click. Responses stream back and render. Errors are surfaced to the user. No regression in channel-based (human-to-human) chat.

---

### CHAT-11 — Offline messages lost on app restart

| Field | Value |
|-------|-------|
| **Severity** | `CRITICAL` |
| **Test ID** | CHAT-11 |
| **Platforms** | Windows 11 |
| **Found by** | Robin van Boven |

**What's broken:** Messages composed while offline are preserved in the local session but lost when the app is restarted. There is no indication that messages are queued for later delivery. When connection is restored during the same session, messages appear to sync, but restarting the app wipes all offline messages.

**Tester evidence:**
- Robin: *"There is no indication that messages are queued, though the messages are preserved locally. When connection is restored, it seems to be OK. But when restarting the application all offline messages are lost."*

**Cross-platform:** Tested on Windows 11 only. Likely affects all platforms (data persistence is a backend issue).

#### Investigation Prompt

```
Search the codebase for offline message handling and local message persistence.
Messages composed offline are preserved during the session but LOST when the
app is restarted.

Check:
1. Message storage — where are chat messages stored locally? If they are only
   in memory (a SolidJS store/signal), they will be lost on restart. Check if
   messages are written to SQLite, IndexedDB, or the filesystem.
2. Offline queue — is there an outbox/queue for messages that haven't been
   synced to the server? If messages are only marked as "pending" in memory,
   they need to be persisted to survive restarts.
3. App startup message loading — when the app starts, does it load messages
   from the local store? If it only fetches from the server and the server
   never received the offline messages, they will appear lost.
4. Sync mechanism — when connectivity is restored, how are offline messages
   sent? Check if this uses a persistent queue (survives restart) or a
   volatile one (in-memory only).

Expected: Messages composed offline are persisted to local storage immediately,
queued for sync, and survive app restarts. A visual indicator should show
which messages are pending sync.
```

#### Test

Write regression tests that assert:
- Messages composed while offline are persisted to local storage immediately (not just held in memory).
- After app restart while offline, previously composed messages are still visible.
- When connectivity is restored, offline messages are synced to the server.
- A visual indicator shows which messages are pending sync.

#### Validate

- [ ] Go offline. Send messages. Quit the app. Reopen the app. Messages are still visible.
- [ ] Go online. Offline messages sync to the server.
- [ ] Pending messages show a visual indicator (clock icon, "sending..." label, or similar).

#### Checkpoint

Offline messages survive app restarts. Sync queue is persistent. Visual indicators communicate message state. No regression in online message delivery.

---

### CHAT-01 — Page titles show "New Chat" across multiple sections

| Field | Value |
|-------|-------|
| **Severity** | `MEDIUM` |
| **Test ID** | CHAT-01 |
| **Platforms** | Windows 11 |
| **Found by** | Robin van Boven |

**What's broken:** Multiple pages display "# New Chat" as the title instead of their actual section name. Chat shows "New Channel", Specialists shows "New Chat", Knowledge Garden shows "New Chat", Workflows shows "New Chat", Tasks shows "New Chat". Systematic routing or title rendering bug.

**Tester evidence:**
- Robin: Page titles default to "New Chat" across Chat, Specialists, Knowledge, Workflows, and Tasks sections.

**Cross-platform:** Windows 11 only. macOS sessions all passed CHAT-01.

#### Investigation Prompt

```
Search for the page title rendering logic. On Windows 11, multiple sections
display "New Chat" as the page title instead of their actual names (Chat shows
"New Channel", Specialists/Knowledge/Workflows/Tasks all show "New Chat").

Check:
1. Route-based title — is the page title derived from the current route? Look
   for a document.title setter or a title component that reads from the router.
2. Default state — "New Chat" appears to be a default/initial value that is
   not being overwritten when navigating to other sections. Check the title
   store/signal initialisation.
3. Platform-specific — this is Windows 11 only. Check if there is a timing
   difference in route resolution on Windows that causes the title to not
   update.

Expected: Each section displays its own title (Chat, Specialists, Knowledge
Garden, Workflows, Tasks).
```

#### Test

Write a test that asserts each section displays the correct title when navigated to. Navigate to Chat, Specialists, Knowledge, Workflows, and Tasks — each should show its own section name, not "New Chat".

#### Validate

- [ ] Navigate to each section on Windows 11. Each shows the correct title.
- [ ] Verify on macOS as well (should already pass).

#### Checkpoint

All sections display correct titles on all platforms. No regression in navigation.

---

### CHAT-08 — Disconnect banner flickers at ~0.5 Hz

| Field | Value |
|-------|-------|
| **Severity** | `MEDIUM` |
| **Test ID** | CHAT-08 |
| **Platforms** | Windows 11 |
| **Found by** | Robin van Boven |

**What's broken:** The connection status banner rapidly toggles visibility approximately every 2 seconds, creating a distracting flicker effect.

**Tester evidence:**
- Robin: *"The banner will flicker regularly every other second."*

**Cross-platform:** Windows 11 only tested.

#### Investigation Prompt

```
Search for the connection status banner / disconnect notification component.
On Windows 11, it flickers on/off at approximately 0.5 Hz (every ~2 seconds).

Check:
1. Connection status polling — is the app polling a health endpoint or
   WebSocket heartbeat? If the connection check oscillates between
   success/failure, the banner will flicker.
2. Debouncing — the connection state change should be debounced (e.g., only
   show "disconnected" after 5+ seconds of no connection, only hide after
   stable connection for 3+ seconds).
3. Reactivity — if using a SolidJS signal for connection state, check that
   the signal is not being set on every poll response (which would trigger
   re-renders even when the state hasn't changed).

Expected: Banner shows when disconnected and hides when connected, with
debouncing to prevent flicker.
```

#### Test

Write a test that asserts the connection banner does not toggle visibility more than once within a 5-second window under stable network conditions. The banner state should be debounced.

#### Validate

- [ ] On Windows 11, the disconnect banner does not flicker during normal use.
- [ ] Banner appears steadily when offline, disappears steadily when online.

#### Checkpoint

Connection banner is stable. No flicker. No regression in connection state detection.

---

### CHAT-03 — Chat placeholder text confusing; send button icon misleading

| Field | Value |
|-------|-------|
| **Severity** | `LOW` |
| **Test ID** | CHAT-03 |
| **Platforms** | Windows 11 |
| **Found by** | Robin van Boven |

**What's broken:** The placeholder text says "Start a task or invite some people to chat with @" which conflates different features. After clearing text, the textarea shows the placeholder instead of being empty. The send button uses an up-arrow icon which reads as "scroll to top" rather than "send".

**Tester evidence:**
- Robin: *"Start a task or invite some people to chat with @ as placeholder. After clearing all text, the textarea shows the placeholder, rather than being empty. The send button is an UP arrow. This icon means return to top to me, not send."*

**Cross-platform:** Windows 11 only tested. Likely a universal UX issue.

#### Investigation Prompt

```
Search for the chat input textarea component and its placeholder text. Two UX
issues:
1. Placeholder text "Start a task or invite some people to chat with @" is
   confusing — it conflates task creation and chat in a single prompt.
2. Send button uses an up-arrow icon which users interpret as "scroll to top",
   not "send". Consider using a paper-plane or right-arrow icon instead.

These are UX polish items, not bugs. Low priority.
```

#### Test

Write a test that asserts the send button's icon and aria-label communicate "send message" intent. Verify the placeholder text is clear and contextually appropriate for the chat input.

#### Validate

- [ ] Send button icon is recognisable as "send" (not "scroll to top").
- [ ] Placeholder text accurately describes what the input does.

#### Checkpoint

Chat input UX is clear and unambiguous. No regression in message sending functionality.

---

*5 issues in Chat. 2 CRITICAL (AI broken, offline data loss), 2 MEDIUM (titles, flicker), 1 LOW (UX text). Chat is the weakest area at 74.1% pass rate. Screenshots: see `robin-win11-6feb/03-chat-media/` (3 images).*
