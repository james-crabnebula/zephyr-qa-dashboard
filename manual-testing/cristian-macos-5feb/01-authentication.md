---
title: "Step 1: Authentication"
tester: "Cristian Vogel"
role: "Rust / TS Dev, QA Tester"
platform: "macOS ARM64 Tahoe 26.1"
date: "2026-02-05"
suite: "01-authentication"
session: "5-feb"
source: "CRISTIAN Zephyr_Agency_Testing_Guide_single_pass_template.docx"
---
# Step 1: Authentication

*Sign-in flows, OAuth providers, session management*

### AUTH-01: Unauthenticated Navigation

**Pre-condition:** Unauthenticated, app just launched

| **Step** | **Action** | **Expected Result** | **Result** |
|----|----|----|----|
| 1 | Observe sidebar | Channels visible but content not accessible | **F** |
| 2 | Observe Manage section | Specialists, Knowledge, Workflows, Tasks not accessible | ?? |
| 3 | Try to navigate | Content protected, requires sign-in | **F** |

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<thead>
<tr>
<th><p><strong>Notes:</strong></p>
<ul>
<li><p>Channels visible, content readable, chat post is accepted and appears as Anonymous User. That’s weird, it should just tell me I am not logged in.</p></li>
<li><p>Not sure what ‘Manage Section’ is referring to?</p></li>
</ul></th>
</tr>
</thead>
<tbody>
</tbody>
</table>

### AUTH-02: Sign In Button Location

| **Step** | **Action** | **Expected Result** | **Result** |
|----|----|----|----|
| 1 | Observe sidebar bottom | Sign In button visible | **P** |
| 2 | Observe button text | Sign In with Click to authenticate subtext | **P** |

| **Notes:** |
|------------|

### 

### 

### AUTH-03: Sign In Page (Auth0)

| **Step** | **Action** | **Expected Result** | **Result** |
|----|----|----|----|
| 1 | Click Sign In button in sidebar | Browser opens to Auth0 login page | **P** |
| 2 | Observe URL | zephyr-dev-eu.eu.auth0.com/... | **P** |
| 3 | Observe heading | Welcome | **P** |
| 4 | Observe subheading | Log in to zephyr-dev-eu to continue to Zephyr Agency Tauri | **P** |
| 5 | Observe email input | Email address field with Continue button | **P** |
| 6 | Observe Sign up link | Don't have an account? Sign up | **P** |
| 7 | Observe OAuth providers | 4 buttons: Google, GitHub, Microsoft, Zephyr Okta Dev | **P** |

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<thead>
<tr>
<th><p><strong>Notes:</strong></p>
<p>Tested QR code in app login launcher. QR translates to<br />
<br />
zephyr://qr-auth?code=192f69f3-4483-4495-88a4-9473b12cdc01&amp;ip=90.184.70.215&amp;country=DK</p>
<p>doesn’t launch link automatically</p></th>
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

### 

### AUTH-04: Google OAuth Flow

**Pre-condition:** On Auth0 login page

| **Step** | **Action** | **Expected Result** | **Result** |
|----|----|----|----|
| 1 | Click Continue with Google | Google sign-in page loads | **P** |
| 2 | Select/enter Google account | Google authenticates | **P** |
| 3 | Complete authentication | Redirects back to app | **P** |
| 4 | Observe app state | User authenticated, avatar visible in sidebar | **F** |
| 5 | Observe browser window | Auth0 window stays open (must manually close) | **F** |

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<thead>
<tr>
<th><p><strong>Notes:</strong></p>
<p>Auth0 window ( Google ) is unresponsive, progress bar animating. Does not close. App is focussed again, but is not past the auth, still on QR code / launcher panel.<br />
Clicked on Sign In again, link opens in browser which correctly returns to app and I am authed. Basically, didn’t work the first time… 😶</p></th>
</tr>
</thead>
<tbody>
</tbody>
</table>

### AUTH-05: Authenticated State

**Pre-condition:** Successfully authenticated

| **Step** | **Action**             | **Expected Result** | **Result** |
|----------|------------------------|---------------------|------------|
| 1        | Observe sidebar bottom | User avatar visible | **P**      |
| 2        | Observe user name      | Display name shown  | **P**      |
| 3        | Observe username       | @handle shown       | **P**      |
| 4        | Sign In button         | No longer visible   | **P**      |

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<thead>
<tr>
<th><p><strong>Notes:</strong></p>
<p>Tried a chat post, worked fine too, with my name no longer Anonymous User</p></th>
</tr>
</thead>
<tbody>
</tbody>
</table>

### AUTH-06: User Menu

| **Step** | **Action** | **Expected Result** | **Result** |
|----|----|----|----|
| 1 | Click user avatar/name area | User menu opens | **P** |
| 2 | Observe menu options | Settings and Sign out visible | **P** |

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<thead>
<tr>
<th><p><strong>Notes:</strong></p>
<p>Now I see ‘Manage’ section</p></th>
</tr>
</thead>
<tbody>
</tbody>
</table>
