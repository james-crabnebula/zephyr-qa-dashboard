---
title: "Step 0: Pre-Launch Setup"
tester: "Alex Lohr"
role: "Front-End Expert"
platform: "macOS ARM64"
date: "2026-02-05"
suite: "00-pre-launch-setup"
session: "5-feb"
source: "AlexLohr-macOS-Complete.docx"
---
# Step 0: Installation

*Download, install, and first launch of Zephyr Agency*

### INST-01: Download

**Pre-condition:** Download link from Quick Start guide

<table>
<colgroup>
<col style="width: 9%" />
<col style="width: 29%" />
<col style="width: 40%" />
<col style="width: 20%" />
</colgroup>
<thead>
<tr>
<th style="text-align: center;"><strong>Step</strong></th>
<th><strong>Action</strong></th>
<th><strong>Expected Result</strong></th>
<th style="text-align: center;"><strong>Result</strong></th>
</tr>
<tr>
<th style="text-align: center;">1</th>
<th>Click the download link for your platform</th>
<th>Download starts in your browser</th>
<th>P</th>
</tr>
<tr>
<th style="text-align: center;">2</th>
<th>Wait for download to complete</th>
<th>File downloaded: .dmg (macOS), .msi/.exe (Windows), .AppImage/.deb (Linux)</th>
<th><p><del>(F) - got a .<a href="http://tar.gz"><u>tar.gz</u></a> instead of a dmg, contents still runnable app</del></p>
<p>P</p></th>
</tr>
<tr>
<th style="text-align: center;">3</th>
<th>Check the downloaded file size</th>
<th>File is not 0 bytes and appears complete</th>
<th>P</th>
</tr>
</thead>
<tbody>
</tbody>
</table>

| **Notes:** |
|------------|

### INST-02: Install — macOS

**Pre-condition:** INST-01 complete, .dmg file downloaded. Skip if not on macOS.

<table>
<colgroup>
<col style="width: 9%" />
<col style="width: 29%" />
<col style="width: 40%" />
<col style="width: 20%" />
</colgroup>
<thead>
<tr>
<th style="text-align: center;"><strong>Step</strong></th>
<th><strong>Action</strong></th>
<th><strong>Expected Result</strong></th>
<th style="text-align: center;"><strong>Result</strong></th>
</tr>
<tr>
<th style="text-align: center;">1</th>
<th>Double-click the .dmg file</th>
<th>Disk image mounts, Finder window opens</th>
<th><p><del>(F) - was unpacked into an app image</del></p>
<p>P</p></th>
</tr>
<tr>
<th style="text-align: center;">2</th>
<th>Drag Zephyr Agency to Applications folder</th>
<th>App copies to /Applications</th>
<th><p><del>Was able to copy into Apps folder manually</del></p>
<p>P</p></th>
</tr>
<tr>
<th style="text-align: center;">3</th>
<th>Eject the disk image</th>
<th>Disk image unmounts cleanly</th>
<th><p><del>B (not DMG)</del></p>
<p>P</p></th>
</tr>
</thead>
<tbody>
</tbody>
</table>

| **Notes:** |
|------------|

### INST-03: Install — Windows

**Pre-condition:** INST-01 complete, installer downloaded. Skip if not on Windows.

| **Step** | **Action** | **Expected Result** | **Result** |
|----|----|----|----|
| 1 | Run the downloaded installer (.msi or .exe) | Installer window appears |  |
| 2 | Follow the installation prompts | Installation completes without errors |  |
| 3 | Check Start Menu or Desktop for shortcut | Shortcut is present |  |

| **Notes:** |
|------------|

### INST-04: Install — Linux

**Pre-condition:** INST-01 complete, package downloaded. Skip if not on Linux.

| **Step** | **Action** | **Expected Result** | **Result** |
|----|----|----|----|
| 1 | Install via package manager (.deb) or make AppImage executable | Installation completes or AppImage is executable |  |
| 2 | Verify app appears in launcher or can run from terminal | App is launchable |  |

| **Notes:** |
|------------|

### 

### 

### INST-05: First Launch

**Pre-condition:** Installation complete

| **Step** | **Action** | **Expected Result** | **Result** |
|----|----|----|----|
| 1 | Launch Zephyr Agency from applications | App begins to load | P |
| 2 | If macOS blocks: System Settings → Privacy & Security → Open Anyway | Security prompt clears, app launches | \(P\) |
| 3 | Wait for app window to appear | Main window visible within 10 seconds | P |
| 4 | Observe the window | Sidebar visible on left with navigation items | ~~F~~ P |
| 5 | Look for Sign In option | Sign In button visible at bottom of sidebar | ~~F~~ P |

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<thead>
<tr>
<th><p><strong>Notes:</strong></p>
<p><del>It shows only an empty window without a side bar.</del></p></th>
</tr>
</thead>
<tbody>
</tbody>
</table>

### 

### 

### 

### 

### 

### 

### 

### 

### 

### INST-06: Verify Installation

**Pre-condition:** App has launched successfully

| **Step** | **Action** | **Expected Result** | **Result** |
|----|----|----|----|
| 1 | Check app launched without crashing | Window appeared, no crash dialog | P |
| 2 | Observe sidebar | Left panel with navigation items is visible | ~~F~~ P |
| 3 | Check for error dialogs | No modal errors or warnings on launch | (Inspect -\> Console shows some errors) P |
| 4 | Try resizing window | Window resizes smoothly | P |
| 5 | Try fullscreen | App enters and exits fullscreen cleanly | P |

| **Notes:** |
|------------|
