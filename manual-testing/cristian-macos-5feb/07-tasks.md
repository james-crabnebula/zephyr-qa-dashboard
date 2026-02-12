---
title: "Step 7: Tasks"
tester: "Cristian Vogel"
role: "Rust / TS Dev, QA Tester"
platform: "macOS ARM64 Tahoe 26.1"
date: "2026-02-05"
suite: "07-tasks"
session: "5-feb"
source: "CRISTIAN Zephyr_Agency_Testing_Guide_single_pass_template.docx"
---
# Step 7: Tasks

*Kanban board, task management, drag and drop, persistence*

### TASK-01: Page Load

**Pre-condition:** Authenticated

| **Step** | **Action** | **Expected Result** | **Result** |
|----|----|----|----|
| 1 | Click Tasks in sidebar Manage section | URL shows /tasks |  |
| 2 | Observe page layout | Kanban board visible |  |
| 3 | Count visible columns | 5 columns |  |

| **Notes:** |
|------------|

### TASK-02: Kanban Columns

| **Step** | **Action**                | **Expected Result**    | **Result** |
|----------|---------------------------|------------------------|------------|
| 1        | Read first column header  | Backlog with count     |            |
| 2        | Read second column header | To Do with count       |            |
| 3        | Read third column header  | In Progress with count |            |
| 4        | Read fourth column header | Blocked with count     |            |
| 5        | Read fifth column header  | Done with count        |            |

| **Notes:** |
|------------|

### TASK-03: Create Task

| **Step** | **Action** | **Expected Result** | **Result** |
|----|----|----|----|
| 1 | Note current task count in target column | Record count (e.g., 0) |  |
| 2 | Locate way to create task | \+ button, Add task link, or click in column |  |
| 3 | Trigger task creation | Form or inline editor appears |  |
| 4 | Observe task form | Title field minimum |  |
| 5 | Enter task title Test task | Text entered |  |
| 6 | Save task | Task appears in appropriate column |  |
| 7 | Verify column header count increments | Count now shows +1 |  |

| **Notes:** |
|------------|

### TASK-04: Task Drag and Drop

**Pre-condition:** At least one task exists

| **Step** | **Action** | **Expected Result** | **Result** |
|----|----|----|----|
| 1 | Note which column the task is in | Record source column |  |
| 2 | Click and hold on task card | Card becomes draggable |  |
| 3 | Drag to different column | Visual feedback shows target |  |
| 4 | Release card | Card moves to new column |  |
| 5 | Observe column counts | Source −1, target +1 |  |

| **Notes:** |
|------------|

### TASK-05: Task Persistence (Local-First)

**Pre-condition:** Task has been moved via drag-drop

| **Step** | **Action** | **Expected Result** | **Result** |
|----|----|----|----|
| 1 | Note current task position (which column) | Record column name |  |
| 2 | Reload page (Cmd+R or browser refresh) | Page reloads |  |
| 3 | Navigate back to /tasks if needed | Tasks page loads |  |
| 4 | Verify task remains in new column | Task position persisted, not reverted |  |
| 5 | Verify column counts are correct | Counts match pre-reload state |  |

| **Notes:** |
|------------|

### TASK-06: Task Detail View

**Pre-condition:** At least one task exists

| **Step** | **Action**           | **Expected Result**                | **Result** |
|----------|----------------------|------------------------------------|------------|
| 1        | Click on task card   | Detail view or modal opens         |            |
| 2        | Observe task details | Title, description, status visible |            |
| 3        | Locate edit options  | Edit button or inline editing      |            |
| 4        | Locate close option  | Close button or click outside      |            |
| 5        | Close detail view    | Returns to kanban view             |            |

| **Notes:** |
|------------|
