---
title: "Step 5: Knowledge Garden"
tester: "Alex Lohr"
role: "Front-End Expert"
platform: "macOS ARM64"
date: "2026-02-05"
suite: "05-knowledge"
session: "5-feb"
source: "AlexLohr-macOS-Complete.docx"
---
# Step 5: Knowledge Garden

*Knowledge plots, file uploads, document management*

### KNOW-01: Page Load

**Pre-condition:** Authenticated

| **Step** | **Action** | **Expected Result** | **Result** |
|----|----|----|----|
| 1 | Click Knowledge Garden in sidebar | URL shows /knowledge | P |
| 2 | Observe page header | Shows Knowledge Garden with + button | P |
| 3 | Observe main content area | Shows plot list or empty state | P |

| **Notes:** |
|------------|

### KNOW-02: Empty State

**Pre-condition:** On /knowledge, no plots created

| **Step** | **Action** | **Expected Result** | **Result** |
|----|----|----|----|
| 1 | Observe main content area | Seedling/plant icon visible | P |
| 2 | Observe heading | Cultivate your first Knowledge Plot | P |
| 3 | Observe description | We will use your plot to enrich responses | P |
| 4 | Observe CTA button | Create Plot button visible (purple) | P |

| **Notes:** |
|------------|

### KNOW-03: Create Plot

| **Step** | **Action** | **Expected Result** | **Result** |
|----|----|----|----|
| 1 | Click Create Plot button | Category selection appears | P |
| 2 | Observe categories | Personal and Workspaces options | P (F\*) |
| 3 | Select a category | Name input appears | P (F\*\*) |
| 4 | Enter plot name and confirm | Plot created | P |

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<thead>
<tr>
<th><p><strong>Notes:</strong></p>
<p>* The Personal/Workspace options are only selectable by the radio input, not the label text</p>
<p>** Name input already present</p></th>
</tr>
</thead>
<tbody>
</tbody>
</table>

### KNOW-04: Plot Detail View

**Pre-condition:** Plot selected

| **Step** | **Action** | **Expected Result** | **Result** |
|----|----|----|----|
| 1 | Observe plot header | Plot name shown | P |
| 2 | Observe empty state | No items added with drag-drop area | P |
| 3 | Locate Add item dropdown | Dropdown with File and Folder options | P |
| 4 | Click Add item | Shows File or Folder upload options | P |

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<thead>
<tr>
<th><p><strong>Notes:</strong></p>
<p>File upload works too, but longer file titles exceed the length of the file component (consider shortening)</p></th>
</tr>
</thead>
<tbody>
</tbody>
</table>
