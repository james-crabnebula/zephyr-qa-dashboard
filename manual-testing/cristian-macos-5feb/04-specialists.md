---
title: "Step 4: Specialists"
tester: "Cristian Vogel"
role: "Rust / TS Dev, QA Tester"
platform: "macOS ARM64 Tahoe 26.1"
date: "2026-02-05"
suite: "04-specialists"
session: "5-feb"
source: "CRISTIAN Zephyr_Agency_Testing_Guide_single_pass_template.docx"
---
# Step 4: Specialists

*AI specialist configuration, templates, search and filtering*

### SPEC-01: Page Load

**Pre-condition:** Authenticated

| **Step** | **Action** | **Expected Result** | **Result** |
|----|----|----|----|
| 1 | Click Specialists in sidebar Manage section | URL shows /specialists |  |
| 2 | Observe page header | Shows Specialists with + button |  |
| 3 | Observe main content area | Shows specialist list or empty state |  |
| 4 | Observe sidebar | Find a specialist link visible |  |

| **Notes:** |
|------------|

### SPEC-02: Search Input

**Pre-condition:** On /specialists, at least one specialist exists

| **Step** | **Action** | **Expected Result** | **Result** |
|----|----|----|----|
| 1 | Locate search input | Input with placeholder Search specialists |  |
| 2 | Note current specialist count | Record how many visible |  |
| 3 | Click into search input | Cursor appears, input focused |  |
| 4 | Type partial name of existing specialist | Text appears in input |  |
| 5 | Observe filtering | Matching specialists remain visible |  |
| 6 | Verify non-matching items disappear | List shows only matching results |  |
| 7 | Type xyznonexistent123 | Nonsense search term entered |  |
| 8 | Observe empty result | No specialists found or empty list |  |
| 9 | Clear search input | Full list restored, original count matches |  |

| **Notes:** |
|------------|

### SPEC-03: Create Specialist Button

| **Step** | **Action** | **Expected Result** | **Result** |
|----|----|----|----|
| 1 | Locate create button (+ icon in header) | Button visible in Specialists header |  |
| 2 | Click create button | Create a Specialist view opens |  |
| 3 | Observe Recommended dropdown | Shows Recommended 4 with category options |  |
| 4 | Observe Browse templates by category | Category filter buttons visible |  |
| 5 | Click a category | Category highlights, templates filter |  |
| 6 | Locate Clear filters button | Button to reset all filters |  |

| **Notes:** |
|------------|
