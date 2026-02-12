---
title: "Step 6: Workflows"
tester: "Cristian Vogel"
role: "Rust / TS Dev, QA Tester"
platform: "macOS ARM64 Tahoe 26.1"
date: "2026-02-05"
suite: "06-workflows"
session: "5-feb"
source: "CRISTIAN Zephyr_Agency_Testing_Guide_single_pass_template.docx"
---
# Step 6: Workflows

*Visual workflow editor, n8n import, execution*

**Note:** The URL is /workflow (singular), not /workflows.

### WORK-01: Page Load

**Pre-condition:** Authenticated

| **Step** | **Action** | **Expected Result** | **Result** |
|----|----|----|----|
| 1 | Click Workflows in sidebar | URL shows /workflow (singular) |  |
| 2 | Observe page header | Shows Workflows with refresh and + buttons |  |
| 3 | Observe main content area | Shows workflow list or empty state with Quick Start |  |

| **Notes:** |
|------------|

### WORK-02: Empty State & Quick Start

**Pre-condition:** On /workflow, no workflows created

| **Step** | **Action** | **Expected Result** | **Result** |
|----|----|----|----|
| 1 | Observe left panel | No workflows yet message |  |
| 2 | Observe right panel | Quick Start guide visible |  |
| 3 | Observe Quick Start steps | 3 numbered steps shown |  |
| 4 | Read step 1 | Create Workflow - Click + to design |  |
| 5 | Read step 2 | Add Nodes - Drag nodes from palette |  |
| 6 | Read step 3 | Save & Execute - Save and test |  |

| **Notes:** |
|------------|

### WORK-03: Visual Editor Launch

| **Step** | **Action** | **Expected Result** | **Result** |
|----|----|----|----|
| 1 | Click Visual Editor button | Editor opens |  |
| 2 | Observe migration prompt (may appear) | Enable Collaborative Editing dialog |  |
| 3 | Dismiss or accept prompt | Click Skip for Now or Migrate Now |  |
| 4 | Observe toolbar | Back, name field, Dataflow dropdown, New, Import, Export, Save, Execute |  |
| 5 | Observe Node Palette (left) | Triggers (4), Actions (20) categories |  |
| 6 | Observe canvas (center) | Drag or click nodes from the left |  |
| 7 | Locate Back button (top-left) | ← Back returns to workflow list |  |

| **Notes:** |
|------------|

### WORK-04: Refresh Workflows

| **Step** | **Action** | **Expected Result** | **Result** |
|----|----|----|----|
| 1 | Locate refresh button in header | Refresh/reload icon |  |
| 2 | Click refresh button | List refreshes |  |
| 3 | Observe success message | Green dialog shows Loaded X workflows |  |

| **Notes:** |
|------------|
