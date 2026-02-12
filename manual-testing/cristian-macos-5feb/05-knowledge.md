---
title: "Step 5: Knowledge Garden"
tester: "Cristian Vogel"
role: "Rust / TS Dev, QA Tester"
platform: "macOS ARM64 Tahoe 26.1"
date: "2026-02-05"
suite: "05-knowledge"
session: "5-feb"
source: "CRISTIAN Zephyr_Agency_Testing_Guide_single_pass_template.docx"
---
# Step 5: Knowledge Garden

*Knowledge plots, file uploads, document management*

### KNOW-01: Page Load

**Pre-condition:** Authenticated

| **Step** | **Action** | **Expected Result** | **Result** |
|----|----|----|----|
| 1 | Click Knowledge Garden in sidebar | URL shows /knowledge |  |
| 2 | Observe page header | Shows Knowledge Garden with + button |  |
| 3 | Observe main content area | Shows plot list or empty state |  |

| **Notes:** |
|------------|

### KNOW-02: Empty State

**Pre-condition:** On /knowledge, no plots created

| **Step** | **Action** | **Expected Result** | **Result** |
|----|----|----|----|
| 1 | Observe main content area | Seedling/plant icon visible |  |
| 2 | Observe heading | Cultivate your first Knowledge Plot |  |
| 3 | Observe description | We will use your plot to enrich responses |  |
| 4 | Observe CTA button | Create Plot button visible (purple) |  |

| **Notes:** |
|------------|

### KNOW-03: Create Plot

| **Step** | **Action** | **Expected Result** | **Result** |
|----|----|----|----|
| 1 | Click Create Plot button | Category selection appears |  |
| 2 | Observe categories | Personal and Workspaces options |  |
| 3 | Select a category | Name input appears |  |
| 4 | Enter plot name and confirm | Plot created |  |

| **Notes:** |
|------------|

### KNOW-04: Plot Detail View

**Pre-condition:** Plot selected

| **Step** | **Action** | **Expected Result** | **Result** |
|----|----|----|----|
| 1 | Observe plot header | Plot name shown |  |
| 2 | Observe empty state | No items added with drag-drop area |  |
| 3 | Locate Add item dropdown | Dropdown with File and Folder options |  |
| 4 | Click Add item | Shows File or Folder upload options |  |

| **Notes:** |
|------------|
