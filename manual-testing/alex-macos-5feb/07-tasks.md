---
title: "Step 7: Tasks"
tester: "Alex Lohr"
role: "Front-End Expert"
platform: "macOS ARM64"
date: "2026-02-05"
suite: "07-tasks"
session: "5-feb"
source: "AlexLohr-macOS-Complete.docx"
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
| 5 | Enter task title Test task | Text entered | P / F\* |
| 6 | Save task | Task appears in appropriate column | P |
| 7 | Verify column header count increments | Count now shows +1 | P |

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<thead>
<tr>
<th><p><strong>Notes:</strong></p>
<ul>
<li><p>The + button at the right of “Filter” looks like you can create a new filter. Maybe consider a Task headline with a sub-headline “Filter task view”</p></li>
<li><p>Clicking in the column does not do anything</p></li>
</ul>
<p>* Changes to tasks are not saved (other title, etc.)</p></th>
</tr>
</thead>
<tbody>
</tbody>
</table>

### TASK-04: Task Drag and Drop

**Pre-condition:** At least one task exists

| **Step** | **Action** | **Expected Result** | **Result** |
|----|----|----|----|
| 1 | Note which column the task is in | Record source column | P |
| 2 | Click and hold on task card | Card becomes draggable | P |
| 3 | Drag to different column | Visual feedback shows target | P / F\* |
| 4 | Release card | Card moves to new column | P / F\* |
| 5 | Observe column counts | Source −1, target +1 | P |

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<thead>
<tr>
<th><p><strong>Notes:</strong></p>
<p>* Moving a task to an already filled column does not work and yields a console error “Invalid order key head: undefined”; error blocks further functionality</p></th>
</tr>
</thead>
<tbody>
</tbody>
</table>

### TASK-05: Task Persistence (Local-First)

**Pre-condition:** Task has been moved via drag-drop

| **Step** | **Action** | **Expected Result** | **Result** |
|----|----|----|----|
| 1 | Note current task position (which column) | Record column name | P |
| 2 | Reload page (Cmd+R or browser refresh) | Page reloads | P |
| 3 | Navigate back to /tasks if needed | Tasks page loads | P |
| 4 | Verify task remains in new column | Task position persisted, not reverted | P |
| 5 | Verify column counts are correct | Counts match pre-reload state | P |

| **Notes:** |
|------------|

### TASK-06: Task Detail View

**Pre-condition:** At least one task exists

| **Step** | **Action**           | **Expected Result**                | **Result** |
|----------|----------------------|------------------------------------|------------|
| 1        | Click on task card   | Detail view or modal opens         | P          |
| 2        | Observe task details | Title, description, status visible | P          |
| 3        | Locate edit options  | Edit button or inline editing      | P          |
| 4        | Locate close option  | Close button or click outside      | P          |
| 5        | Close detail view    | Returns to kanban view             | P          |

| **Notes:** |
|------------|
