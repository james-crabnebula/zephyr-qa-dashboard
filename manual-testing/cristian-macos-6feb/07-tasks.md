---
title: "Step 7: Tasks"
tester: "Cristian Vogel"
role: "Rust / TS Dev"
platform: "macOS ARM64 Tahoe 26.1"
date: "2026-02-06"
suite: "07-tasks"
session: "6-feb"
source: "CRISTIAN_Zephyr_Agency.docx"
---
# Step 7: Tasks

*Kanban board, task management, drag and drop, persistence*

### TASK-01: Page Load

**Pre-condition:** Authenticated

| **Step** | **Action** | **Expected Result** | **Result** |
|----|----|----|----|
| 1 | Click Tasks in sidebar Manage section | URL shows /tasks | P |
| 2 | Observe page layout | Kanban board visible | P |
| 3 | Count visible columns | 5 columns | P |

| **Notes:** |
|------------|

### TASK-02: Kanban Columns

| **Step** | **Action**                | **Expected Result**    | **Result** |
|----------|---------------------------|------------------------|------------|
| 1        | Read first column header  | Backlog with count     | P          |
| 2        | Read second column header | To Do with count       | P          |
| 3        | Read third column header  | In Progress with count | P          |
| 4        | Read fourth column header | Blocked with count     | P          |
| 5        | Read fifth column header  | Done with count        | P          |

| **Notes:** |
|------------|

### TASK-03: Create Task

| **Step** | **Action** | **Expected Result** | **Result** |
|----|----|----|----|
| 1 | Note current task count in target column | Record count (e.g., 0) | P |
| 2 | Locate way to create task | \+ button, Add task link, or click in column | P |
| 3 | Trigger task creation | Form or inline editor appears | P |
| 4 | Observe task form | Title field minimum | P |
| 5 | Enter task title Test task | Text entered | P |
| 6 | Save task | Task appears in appropriate column | P |
| 7 | Verify column header count increments | Count now shows +1 | P |

| **Notes:** |
|------------|

### TASK-04: Task Drag and Drop

**Pre-condition:** At least one task exists

| **Step** | **Action** | **Expected Result** | **Result** |
|----|----|----|----|
| 1 | Note which column the task is in | Record source column | P |
| 2 | Click and hold on task card | Card becomes draggable | P |
| 3 | Drag to different column | Visual feedback shows target | P |
| 4 | Release card | Card moves to new column | P |
| 5 | Observe column counts | Source −1, target +1 | P |

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<thead>
<tr>
<th><p><strong>Notes:</strong></p>
<p><strong><mark>CRASH</mark>: Uncaught (in promise) Error: invalid order key head: undefined</strong></p>
<p>Action: Dragged a card to <strong>DONE. Heard an error sound maybe? Front end is broken, cannot continue. Relaunching. On quit, got seg fault.</strong></p>
<p>Drag and drop works fine though.</p></th>
</tr>
</thead>
<tbody>
</tbody>
</table>

### TASK-05: Task Persistence (Local-First)

**Pre-condition:** Task has been moved via drag-drop

| **Step** | **Action** | **Expected Result** | **Result** |
|----|----|----|----|
| 1 | Note current task position (which column) | Record column name | B |
| 2 | Reload page (Cmd+R or browser refresh) | Page reloads | B |
| 3 | Navigate back to /tasks if needed | Tasks page loads | B |
| 4 | Verify task remains in new column | Task position persisted, not reverted | B |
| 5 | Verify column counts are correct | Counts match pre-reload state | B |

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<thead>
<tr>
<th><p><strong>Notes:</strong></p>
<p>PREVIOUS CRASH, BROKE KANBAN even after reloads / relaunches<br />
Shows cards, dragging from any column Uncaught (in promise) Error: invalid order key head: undefined<br />
Prob persistent state corruption?</p></th>
</tr>
</thead>
<tbody>
</tbody>
</table>

### TASK-06: Task Detail View

**Pre-condition:** At least one task exists

| **Step** | **Action**           | **Expected Result**                | **Result** |
|----------|----------------------|------------------------------------|------------|
| 1        | Click on task card   | Detail view or modal opens         | P          |
| 2        | Observe task details | Title, description, status visible | P          |
| 3        | Locate edit options  | Edit button or inline editing      | P          |
| 4        | Locate close option  | Close button or click outside      | P          |
| 5        | Close detail view    | Returns to kanban view             | P          |

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<thead>
<tr>
<th><p><strong>Notes:</strong></p>
<p><strong>BUG : Create new task. Add title. It does not get added on first creation. Need to re-open card, edit title and then it persists.</strong></p></th>
</tr>
</thead>
<tbody>
</tbody>
</table>
