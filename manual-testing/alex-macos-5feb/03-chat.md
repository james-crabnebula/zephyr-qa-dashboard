---
title: "Step 3: Chat"
tester: "Alex Lohr"
role: "Front-End Expert"
platform: "macOS ARM64"
date: "2026-02-05"
suite: "03-chat"
session: "5-feb"
source: "AlexLohr-macOS-Complete.docx"
---
# Step 3: Chat

*Chat interface, channels, messaging, AI responses*

**Note:** The UI calls conversations 'Channels', not 'Chats'. The + button creates a new Channel.

### CHAT-01: Page Load

**Pre-condition:** Authenticated, API key configured

| **Step** | **Action** | **Expected Result** | **Result** |
|----|----|----|----|
| 1 | Click + next to Channels in sidebar | New channel created, URL shows /chat | (P)\* |
| 2 | Observe page title | Shows New Chat with \# icon | P |
| 3 | Observe main content area | Shows Start a conversation heading | P |
| 4 | Observe suggestion prompts | 6 prompt buttons visible | P |

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<thead>
<tr>
<th><p><strong>Notes:</strong></p>
<p>* One can see the URL in the console, and it is indeed https://tauri.localhost/workspace/zephyr/chat</p></th>
</tr>
</thead>
<tbody>
</tbody>
</table>

### CHAT-02: Message Input

| **Step** | **Action** | **Expected Result** | **Result** |
|----|----|----|----|
| 1 | Locate message input area | Textarea with placeholder Type a message... | (P)\* |
| 2 | Click into textarea | Cursor appears, textarea focused | P |
| 3 | Type Hello | Text appears in textarea | P |
| 4 | Observe send button | Purple/colored send button visible (arrow icon) | P |
| 5 | Clear text | Textarea empty | P |

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<thead>
<tr>
<th><p><strong>Notes:</strong></p>
<p>* It actually reads “Start a task or invite some people to a chat with @”.</p></th>
</tr>
</thead>
<tbody>
</tbody>
</table>

### CHAT-03: Send Message

**Pre-condition:** Authenticated with working API connection

| **Step** | **Action** | **Expected Result** | **Result** |
|----|----|----|----|
| 1 | Type Hello, this is a test message in message input | Text visible in input | P |
| 2 | Click send button (purple arrow) | Message appears in chat area | P |
| 3 | Observe response area | AI response begins generating | F |
| 4 | Wait for completion (max 30 seconds) | Response completes with visible text content | F |
| 5 | Verify response is not empty | Response contains actual text | B |

| **Notes:** |
|------------|

### CHAT-04: New Channel

| **Step** | **Action** | **Expected Result** | **Result** |
|----|----|----|----|
| 1 | Locate + button next to Channels in sidebar | Plus icon visible | P |
| 2 | Click button | New channel created, appears in list | P |
| 3 | Observe chat area | Empty conversation, suggestion prompts shown | P |

| **Notes:** |
|------------|
