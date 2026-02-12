---
title: "Step 1: Authentication"
tester: "Alex Lohr"
role: "Front-End Expert"
platform: "macOS ARM64"
date: "2026-02-05"
suite: "01-authentication"
session: "5-feb"
source: "AlexLohr-macOS-Complete.docx"
---
# Step 1: Authentication

*Sign-in flows, OAuth providers, session management*

### AUTH-01: Unauthenticated Navigation

**Pre-condition:** Unauthenticated, app just launched

| **Step** | **Action** | **Expected Result** | **Result** |
|----|----|----|----|
| 1 | Observe sidebar | Channels visible but content not accessible | P |
| 2 | Observe Manage section | Specialists, Knowledge, Workflows, Tasks not accessible | P |
| 3 | Try to navigate | Content protected, requires sign-in | P |

| **Notes:** |
|------------|

### AUTH-02: Sign In Button Location

| **Step** | **Action** | **Expected Result** | **Result** |
|----|----|----|----|
| 1 | Observe sidebar bottom | Sign In button visible | P |
| 2 | Observe button text | Sign In with Click to authenticate subtext | P |

| **Notes:** |
|------------|

### 

### 

### AUTH-03: Sign In Page (Auth0)

| **Step** | **Action** | **Expected Result** | **Result** |
|----|----|----|----|
| 1 | Click Sign In button in sidebar | Browser opens to Auth0 login page | P |
| 2 | Observe URL | zephyr-dev-eu.eu.auth0.com/... | P |
| 3 | Observe heading | Welcome | P |
| 4 | Observe subheading | Log in to zephyr-dev-eu to continue to Zephyr Agency Tauri | P |
| 5 | Observe email input | Email address field with Continue button | P |
| 6 | Observe Sign up link | Don't have an account? Sign up | P |
| 7 | Observe OAuth providers | 4 buttons: Google, GitHub, Microsoft, Zephyr Okta Dev | P |

| **Notes:** |
|------------|

### 

### 

### 

### 

### 

### 

### 

### 

### 

### 

### AUTH-04: Google OAuth Flow

**Pre-condition:** On Auth0 login page

| **Step** | **Action** | **Expected Result** | **Result** |
|----|----|----|----|
| 1 | Click Continue with Google | Google sign-in page loads | P |
| 2 | Select/enter Google account | Google authenticates | P |
| 3 | Complete authentication | Redirects back to app | P |
| 4 | Observe app state | User authenticated, avatar visible in sidebar | P |
| 5 | Observe browser window | Auth0 window stays open (must manually close) | P |

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<thead>
<tr>
<th><p><strong>Notes:</strong></p>
<p>The sign-in page in the browser shows no sign that the process is already finished and that the window can be closed.</p></th>
</tr>
</thead>
<tbody>
</tbody>
</table>

### AUTH-05: Authenticated State

**Pre-condition:** Successfully authenticated

| **Step** | **Action**             | **Expected Result** | **Result** |
|----------|------------------------|---------------------|------------|
| 1        | Observe sidebar bottom | User avatar visible | P          |
| 2        | Observe user name      | Display name shown  | P          |
| 3        | Observe username       | @handle shown       | P          |
| 4        | Sign In button         | No longer visible   | P          |

| **Notes:** |
|------------|

### AUTH-06: User Menu

| **Step** | **Action** | **Expected Result** | **Result** |
|----|----|----|----|
| 1 | Click user avatar/name area | User menu opens | P |
| 2 | Observe menu options | Settings and Sign out visible | P |

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<thead>
<tr>
<th><p><strong>Notes:</strong></p>
<p>The menu is poorly differentiated from the background. A border and/or a shadow would improve that.</p></th>
</tr>
</thead>
<tbody>
</tbody>
</table>
