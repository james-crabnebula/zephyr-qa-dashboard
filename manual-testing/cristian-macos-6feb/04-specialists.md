---
title: "Step 4: Specialists"
tester: "Cristian Vogel"
role: "Rust / TS Dev"
platform: "macOS ARM64 Tahoe 26.1"
date: "2026-02-06"
suite: "04-specialists"
session: "6-feb"
source: "CRISTIAN_Zephyr_Agency.docx"
---
# Step 4: Specialists

*AI specialist configuration, templates, search and filtering*

### SPEC-01: Page Load

**Pre-condition:** Authenticated

| **Step** | **Action** | **Expected Result** | **Result** |
|----|----|----|----|
| 1 | Click Specialists in sidebar Manage section | URL shows /specialists | P |
| 2 | Observe page header | Shows Specialists with + button | P |
| 3 | Observe main content area | Shows specialist list or empty state | P |
| 4 | Observe sidebar | Find a specialist link visible | P |

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<thead>
<tr>
<th><p><strong>Notes:</strong></p>
<p>Looking good!</p></th>
</tr>
</thead>
<tbody>
</tbody>
</table>

### SPEC-02: Search Input

**Pre-condition:** On /specialists, at least one specialist exists

| **Step** | **Action** | **Expected Result** | **Result** |
|----|----|----|----|
| 1 | Locate search input | Input with placeholder Search specialists | P |
| 2 | Note current specialist count | Record how many visible | 15 |
| 3 | Click into search input | Cursor appears, input focused | P |
| 4 | Type partial name of existing specialist | Text appears in input | P |
| 5 | Observe filtering | Matching specialists remain visible | P |
| 6 | Verify non-matching items disappear | List shows only matching results | P |
| 7 | Type xyznonexistent123 | Nonsense search term entered | P |
| 8 | Observe empty result | No specialists found or empty list | P |
| 9 | Clear search input | Full list restored, original count matches | P |

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<thead>
<tr>
<th><p><strong>Notes:</strong></p>
<p>Works good and fast</p></th>
</tr>
</thead>
<tbody>
</tbody>
</table>

### SPEC-03: Create Specialist Button

| **Step** | **Action** | **Expected Result** | **Result** |
|----|----|----|----|
| 1 | Locate create button (+ icon in header) | Button visible in Specialists header | P |
| 2 | Click create button | Create a Specialist view opens | P |
| 3 | Observe Recommended dropdown | Shows Recommended 4 with category options | Shows 15 |
| 4 | Observe Browse templates by category | Category filter buttons visible | P |
| 5 | Click a category | Category highlights, templates filter | P |
| 6 | Locate Clear filters button | Button to reset all filters | P |

| **Notes:** |
|------------|
