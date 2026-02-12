---
title: "Step 2: Settings"
tester: "Cristian Vogel"
role: "Rust / TS Dev"
platform: "macOS ARM64 Tahoe 26.1"
date: "2026-02-06"
suite: "02-settings"
session: "6-feb"
source: "CRISTIAN_Zephyr_Agency.docx"
---
# Step 2: Settings

*Settings modal, API key configuration, workspace preferences*

### SET-01: Open Settings

**Pre-condition:** Authenticated, on any page

| **Step** | **Action** | **Expected Result** | **Result** |
|----|----|----|----|
| 1 | Locate user menu button in sidebar | User avatar/name at bottom of sidebar | P |
| 2 | Click user menu button | Menu opens | P |
| 3 | Click Settings in menu | Settings modal opens | P |
| 4 | Observe modal | Settings panel with sections visible | P |

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<thead>
<tr>
<th><p><strong>Notes:</strong></p>
<ul>
<li><p>SETTINGS MODAL still ‘behind’ prompt input text area, this is wrong.</p></li>
</ul></th>
</tr>
</thead>
<tbody>
</tbody>
</table>

### SET-02: Settings Sections

**Pre-condition:** Settings modal open

| **Step** | **Action** | **Expected Result** | **Result** |
|----|----|----|----|
| 1 | Observe left navigation | List of sections visible | P |
| 2 | Count sections | 9 sections | 10 |
| 3 | Read section names | Details, Appearance, Members, Billing, Connections, Packages, GitHub, Personal, Permissions | P + language |

| **Notes:** |
|------------|

### SET-03: Connections Section (API Keys)

**Pre-condition:** Settings modal open

**IMPORTANT:** The API key must be entered here to enable AI features tested in later steps.

| **Step** | **Action** | **Expected Result** | **Result** |
|----|----|----|----|
| 1 | Click Connections in settings nav | Connections section loads | P |
| 2 | Observe OpenRouter section | Sign in with OpenRouter button OR Use API Key Instead | P |
| 3 | Click Use API Key Instead | API key input field appears | P |
| 4 | Enter the API key from Quick Start guide | Key accepted, connection status updates | P |
| 5 | Observe connection status | Connection status badge shown | P |

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<thead>
<tr>
<th><p><strong>Notes:</strong></p>
<p>App persisted the API key from yesterday, still valid, 334 models found.</p></th>
</tr>
</thead>
<tbody>
</tbody>
</table>

### SET-04: Appearance Section (Persistence)

| **Step** | **Action** | **Expected Result** | **Result** |
|----|----|----|----|
| 1 | Click Appearance in settings nav | Appearance section loads | P |
| 2 | Note current theme state | Record: Light/Dark/System | P |
| 3 | If theme toggle exists, toggle it | UI updates accordingly | P |
| 4 | Close Settings modal | Modal closes | P |
| 5 | Reload app (Cmd+R) | App reloads | P |
| 6 | Re-open Settings → Appearance | Check theme state | P |
| 7 | Verify theme persisted | Theme matches what was set in step 3 | P |

| **Notes:** |
|------------|

### SET-05: Close Settings

| **Step** | **Action**          | **Expected Result**      | **Result** |
|----------|---------------------|--------------------------|------------|
| 1        | Locate close button | X button in modal header | P          |
| 2        | Click close button  | Modal closes             | P          |
| 3        | Observe page        | Returns to previous view | P          |

| **Notes:** |
|------------|
