---
title: "Manual Testing Results"
date: "2026-02-09"
version: "1.0"
---

# Manual Testing Results

Structured test results from baseline QA testing of Zephyr Agency Nightly v0.1.4.

Three testers, two days, four platforms, 70 test cases across 8 suites.

---

## Quick Start

### Browse results

```
results/manual-testing/
  robin-win11-6feb/          # Priority: deep dive, 42 screenshots
  robin-linux-5feb/          # 3 Linux distros, setup only (all failed)
  alex-macos-5feb/           # Full run, front-end perspective
  cristian-macos-5feb/       # Partial Day 1
  cristian-macos-6feb/       # Full Day 2 run
```

Each folder contains one markdown file per suite (`00-pre-launch-setup.md` through `07-tasks.md`) with YAML frontmatter and images in a `-media/` folder alongside.

### Query the database

```bash
sqlite3 results/manual-testing/testing.db

# All failures
SELECT session_id, suite, test_id, notes FROM test_results WHERE result = 'F';

# Pass rate by suite
SELECT suite, result, COUNT(*) FROM test_results GROUP BY suite, result;

# All issues by severity
SELECT severity, title, platforms FROM issues ORDER BY severity;

# Cross-platform comparison for a test
SELECT t.test_id, s.tester, s.platform, t.result, t.notes
FROM test_results t JOIN sessions s ON t.session_id = s.id
WHERE t.test_id = 'AUTH-01';

# All images for a suite
SELECT i.file_path, i.context FROM images i
JOIN test_results t ON i.test_result_id = t.id
WHERE t.suite = '01-authentication';
```

### Export for delivery

```bash
# CSV export of all failures
sqlite3 -header -csv testing.db \
  "SELECT s.tester, s.platform, t.suite, t.test_id, t.result, t.notes
   FROM test_results t JOIN sessions s ON t.session_id = s.id
   WHERE t.result IN ('F','B')
   ORDER BY t.suite, t.test_id;" > failures.csv

# JSON export of all issues
sqlite3 -json testing.db \
  "SELECT * FROM issues ORDER BY severity, suite;" > issues.json
```

---

## What's In Here

### Sessions

| ID | Tester | Platform | Date | Files | Images |
|----|--------|----------|------|-------|--------|
| `robin-win11-6feb` | Robin van Boven | Windows 11 (x86_64, VM) | 2026-02-06 | 8 | 42 |
| `robin-linux-5feb` | Robin van Boven | Linux (amd64) - 3 distros | 2026-02-05 | 3 | 1 |
| `alex-macos-5feb` | Alex Lohr | macOS ARM64 | 2026-02-05 | 9 | 0 |
| `cristian-macos-5feb` | Cristian Vogel | macOS ARM64 Tahoe 26.1 | 2026-02-05 | 9 | 1 |
| `cristian-macos-6feb` | Cristian Vogel | macOS ARM64 Tahoe 26.1 | 2026-02-06 | 9 | 3 |

### Suites

| Suite | Name | Test IDs |
|-------|------|----------|
| `00-pre-launch-setup` | Installation | INST-01 through INST-06 |
| `01-authentication` | Authentication | AUTH-01 through AUTH-11 |
| `02-settings` | Settings | SET-01 through SET-13 |
| `03-chat` | Chat | CHAT-01 through CHAT-11 |
| `04-specialists` | Specialists | SPEC-01 through SPEC-07 |
| `05-knowledge` | Knowledge Garden | KNOW-01 through KNOW-07 |
| `06-workflows` | Workflows | WORK-01 through WORK-07 |
| `07-tasks` | Tasks | TASK-01 through TASK-10 |

### Result Codes

| Code | Meaning |
|------|---------|
| `P` | Pass |
| `F` | Fail |
| `B` | Blocked |
| (blank) | Not tested / skipped |

---

## Database Schema

**File:** `testing.db` (SQLite 3)

### sessions

| Column | Type | Description |
|--------|------|-------------|
| `id` | TEXT PK | Session identifier (e.g. `robin-win11-6feb`) |
| `tester` | TEXT | Tester name |
| `role` | TEXT | Tester role |
| `platform` | TEXT | Platform tested |
| `date` | TEXT | ISO date (YYYY-MM-DD) |
| `folder` | TEXT | Relative path to results folder |

### test_results

| Column | Type | Description |
|--------|------|-------------|
| `id` | INTEGER PK | Auto-increment |
| `session_id` | TEXT FK | References sessions.id |
| `suite` | TEXT | Suite identifier (e.g. `01-authentication`) |
| `test_id` | TEXT | Test case ID (e.g. `AUTH-01`) |
| `step_num` | INTEGER | Step number within test case |
| `action` | TEXT | What the tester does |
| `expected` | TEXT | Expected result |
| `result` | TEXT | P, F, B, or NULL |
| `notes` | TEXT | Tester observations |
| `source_file` | TEXT | Relative path to markdown file |

### images

| Column | Type | Description |
|--------|------|-------------|
| `id` | INTEGER PK | Auto-increment |
| `test_result_id` | INTEGER FK | References test_results.id (NULL if general) |
| `session_id` | TEXT FK | References sessions.id |
| `suite` | TEXT | Suite identifier |
| `file_path` | TEXT | Relative path to image file |
| `context` | TEXT | What the image shows |

### issues

| Column | Type | Description |
|--------|------|-------------|
| `id` | INTEGER PK | Auto-increment |
| `session_id` | TEXT FK | References sessions.id |
| `suite` | TEXT | Suite where found |
| `test_id` | TEXT | Related test case ID (NULL if general) |
| `severity` | TEXT | CRITICAL, HIGH, MEDIUM, LOW |
| `title` | TEXT | Short description |
| `description` | TEXT | Full detail including tester notes |
| `platforms` | TEXT | Comma-separated platforms affected |

---

## Provenance

### Source material

Original DOCX files are on Desktop at `~/Desktop/Zephyr QA/`. They are untouched.

| Source | Contents |
|--------|----------|
| `Zephyr QA 5 feb/` | 2 zip files containing Robin Linux setup (3 DOCX) + Alex complete (1 DOCX) + Cristian Day 1 (1 DOCX) |
| `Zephyr QA 6 Feb/` | Robin Win11 full run (8 DOCX) + Cristian Day 2 (1 DOCX, plus duplicate) |

### Conversion

DOCX to Markdown via pandoc:

```bash
pandoc "$f" -t gfm --wrap=none --standalone --extract-media="$name-media" -o "$name.md"
```

Images extracted to `-media/` folders alongside each markdown file. YAML frontmatter added with tester, platform, date, suite, and source DOCX reference.

### Frontmatter format

```yaml
---
title: "Step 1: Authentication"
tester: "Robin van Boven"
role: "Senior Engineer"
platform: "Windows 11 (x86_64, VM)"
date: "2026-02-06"
suite: "01-authentication"
session: "6-feb"
source: "Robin-Linux_win11-Step1-auth - Robin Van Boven.docx"
---
```

---

## File Structure

```
results/manual-testing/
  README.md                              # This file
  testing.db                             # SQLite database
  robin-win11-6feb/
    00-pre-launch-setup.md               # + 00-pre-launch-setup-media/
    01-authentication.md                 # + 01-authentication-media/
    02-settings.md                       # + 02-settings-media/
    03-chat.md                           # + 03-chat-media/
    04-specialists.md                    # + 04-specialists-media/
    05-knowledge.md                      # + 05-knowledge-media/
    06-workflows.md                      # + 06-workflows-media/
    07-tasks.md                          # + 07-tasks-media/
  robin-linux-5feb/
    00-pre-launch-setup-fedora.md
    00-pre-launch-setup-popos.md
    00-pre-launch-setup-ubuntu24.md      # + 00-pre-launch-setup-ubuntu24-media/
  alex-macos-5feb/
    00-pre-launch-setup.md
    01-authentication.md
    02-settings.md
    03-chat.md
    04-specialists.md
    05-knowledge.md
    06-workflows.md
    07-tasks.md
    general-observations.md
  cristian-macos-5feb/
    00-pre-launch-setup.md               # + 00-pre-launch-setup-media/
    01-authentication.md
    02-settings.md
    03-chat.md
    04-specialists.md
    05-knowledge.md
    06-workflows.md
    07-tasks.md
    general-observations.md
  cristian-macos-6feb/
    00-pre-launch-setup.md
    01-authentication.md
    02-settings.md
    03-chat.md
    04-specialists.md
    05-knowledge.md
    06-workflows.md
    06-workflows-media/
    07-tasks.md
    general-observations.md
```

---

*Generated: 2026-02-09*
*Branch: manual-testing-restructure-2026-02-05*
