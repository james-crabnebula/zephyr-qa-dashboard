---
title: "Step 0: Pre-Launch Setup (Fedora Workstation 42)"
tester: "Robin van Boven"
role: "Senior Engineer"
platform: "Linux (amd64) - Fedora Workstation 42"
date: "2026-02-05"
suite: "00-pre-launch-setup"
session: "5-feb"
source: "Robin-Linux_fedora-Step0-setup - Robin Van Boven.docx"
---
|                 |                          |
|-----------------|--------------------------|
| **Tester Name** | Robin van Boven          |
| **Role**        | Senior Engineer          |
| **Platform**    | Linux (amd64)            |
| **Date**        | Feb 5<sup>th</sup>, 2026 |

# Step 0: Installation

*Download, install, and first launch of Zephyr Agency Nightly*

|  |  |
|----|----|
| **Area** | Installation & First Launch |
| **Pre-condition** | Internet connection, download link from Quick Start guide |
| **Time** | 5–10 minutes |

**INST-01: Download**

**Pre-condition:** *Download link from Quick Start guide*

|  |  |  |  |
|:--:|:--:|:--:|:--:|
| **Step** | **Action** | **Expected** | **Result** |
| 1 | Click the download link for your platform from the Quick Start guide | Download starts in your browser | OK |
| 2 | Wait for download to complete | File downloaded: .dmg (macOS), .msi or .exe (Windows), .AppImage or .deb (Linux) | OK |
| 3 | Check the downloaded file size | File is not 0 bytes and appears complete | OK |

**Notes:**

|     |
|-----|
|     |

**INST-02: Install — macOS**

**Pre-condition:** *INST-01 complete, .dmg file downloaded. Skip if not on macOS.*

|  |  |  |  |
|:--:|:--:|:--:|:--:|
| **Step** | **Action** | **Expected** | **Result** |
| 1 | Double-click the .dmg file | Disk image mounts, Finder window opens |  |
| 2 | Drag Zephyr Agency Nightly to Applications folder | App copies to /Applications |  |
| 3 | Eject the disk image | Disk image unmounts cleanly |  |

**Notes:**

|     |
|-----|
|     |

**INST-03: Install — Windows**

**Pre-condition:** *INST-01 complete, installer downloaded. Skip if not on Windows.*

|  |  |  |  |
|:--:|:--:|:--:|:--:|
| **Step** | **Action** | **Expected** | **Result** |
| 1 | Run the downloaded installer (.msi or .exe) | Installer window appears |  |
| 2 | Follow the installation prompts | Installation completes without errors |  |
| 3 | Check Start Menu or Desktop for Zephyr Agency shortcut | Shortcut is present |  |

**Notes:**

|     |
|-----|
|     |

**INST-04: Install — Linux**

**Pre-condition:** *INST-01 complete, package downloaded. Skip if not on Linux.*

|  |  |  |  |
|:--:|:--:|:--:|:--:|
| **Step** | **Action** | **Expected** | **Result** |
| 1 | Install via your package manager (.deb) or make the .AppImage executable (chmod +x) | Installation completes or AppImage is executable | OK |
| 2 | Verify the app appears in your application launcher or can be run from terminal | App is launchable | FAIL |

**Notes:**

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<tbody>
<tr>
<td><p>Does not start at all.<br />
Ran from terminal the output is:<br />
<br />
$ ./Zephyr\ Agency_0.1.4-e2ddd7b_amd64.AppImage</p>
<p>WARNING: Loaded 0 specialists in 0ms. Check that bundled specialists exist in src-tauri/specialists/</p>
<p>[LifecycleStore] Initialized</p>
<p>[MintingStore] Initialized</p>
<p>[ChangelogStore] Initialized</p>
<p>[SpawnManager] Initialized</p>
<p>[EnrichmentStore] Initialized</p>
<p>[GithubState] Initialized</p>
<p>[OAuth Server] Starting persistent multi-provider OAuth server...</p>
<p>WARNING: radv is not a conformant Vulkan implementation, testing use only.</p>
<p>[Auth] Initializing auth state (keyring access may prompt)...</p>
<p>[Auth] JWT validator initialized with signature verification</p>
<p>[Auth] Config dir: "/home/beanow/.config"</p>
<p>[Auth] No existing token found (user needs to login)</p>
<p>[Auth] Auth state initialized successfully</p>
<p>[ApiKey] Pre-loading OpenRouter API key (may prompt for keychain)...</p>
<p>[ApiKey] No OpenRouter API key found: API key not found: openrouter_api_key</p>
<p>lukas Some(Object {"endpoints": Array [String("https://cdn.crabnebula.app/update/zephyr-cloud/zephyr-agency/{{target}}-{{arch}}/{{current_version}}")], "pubkey": String("dW50cnVzdGVkIGNvbW1lbnQ6IG1pbmlzaWduIHB1YmxpYyBrZXk6IENENEUwNDczM0Q0NDQ0RjQKUldUMFJFUTljd1JPemFDWFFNcGFzZE9RNWFTMFBWUFA4dzkwanZPbktHd2Zia2RlSnlobW0xZXMK")})</p>
<p>[Init] Starting initialization...</p>
<p>[CustomSpecialistStore] Initialized with 0 custom specialists</p>
<p>[CustomSpecialistStore] Loaded 0 custom specialists into routing</p>
<p>[ModelManager] Initialized. VCV model ID: sentence-transformers/all-MiniLM-L6-v2</p>
<p>[VcvCache] Initialized at "/home/beanow/.config/zephyr-agency/vcv_cache"</p>
<p>[Embedding] Using StubEmbeddingService (lazy initialization)</p>
<p>[Embedding] VCV model will be downloaded in background after app launch</p>
<p>[VcvGenerator] Generated 0 VCVs (0 cache hits, 0 new) in 0.00ms using stub</p>
<p>[SemanticRouter] Initialized with 0 indexed specialists (real embeddings: false)</p>
<p>[ModelManager] Starting background VCV model download...</p>
<p>[ToolRegistry] Registered 21 specialist tools</p>
<p>[Identity] Device ID initialized: 8a5d2c46...</p>
<p>[Workflow] Database initialized</p>
<p>[Summarization] WARNING: No OpenRouter API key available, using placeholder</p>
<p>[Summarization] Service initialized with OpenRouter LLM provider</p>
<p>[CRDT] Persistence layer initialized at: /home/beanow/.config/zephyr-agency/workspaces/zephyr/crdt.db</p>
<p>[CRDT] Manager initialized with peer ID: peer-8a5d2c46-cd31-4032-98dd-0cd65282c094</p>
<p>[CRDT] No persisted data found, starting with empty state</p>
<p>[SpecialistRuntime] Initialized with tool support and Knowledge Garden access control</p>
<p>[CRDT] Sync manager created for org: default</p>
<p>[CRDT] Initialized successfully with Knowledge Garden access control</p>
<p>[VoiceProviders] Registered Groq Whisper provider (via worker proxy)</p>
<p>[CRDT] Starting WebSocket connection...</p>
<p>[CRDT] Starting sync loop...</p>
<p>[VoiceSessionState] Initialized with voice providers</p>
<p>[Init] Initialization complete</p>
<p>[DeepLink] Deep link OAuth enabled (production mode or USE_DEEP_LINK=1)</p>
<p>data directory is not yet implemented</p>
<p>[PackageManager] Initialized successfully</p>
<p>X Error of failed request: BadWindow (invalid Window parameter)</p>
<p>Major opcode of failed request: 12 (X_ConfigureWindow)</p>
<p>Resource id in failed request: 0x2</p>
<p>Serial number of failed request: 7</p>
<p>Current serial number in output stream: 11</p></td>
</tr>
</tbody>
</table>

**INST-05: First Launch**

**Pre-condition:** *Installation complete (INST-02, INST-03, or INST-04)*

|  |  |  |  |
|:--:|:--:|:--:|:--:|
| **Step** | **Action** | **Expected** | **Result** |
| 1 | Launch Zephyr Agency Nightly from your applications | App begins to load |  |
| 2 | If macOS Gatekeeper blocks the app: go to System Settings → Privacy & Security → click "Open Anyway" | Security prompt clears, app launches |  |
| 3 | Wait for the app window to appear | Main window visible within 10 seconds |  |
| 4 | Observe the window | Sidebar visible on the left with navigation items |  |
| 5 | Look for a Sign In option | "Sign In" button visible at bottom of sidebar |  |

**Notes:**

|     |
|-----|
|     |

**INST-06: Verify Installation**

**Pre-condition:** *App has launched successfully (INST-05)*

|  |  |  |  |
|:--:|:--:|:--:|:--:|
| **Step** | **Action** | **Expected** | **Result** |
| 1 | Check the app launched without crashing | Window appeared, no crash dialog |  |
| 2 | Observe the sidebar | Left panel with navigation items is visible |  |
| 3 | Check for error dialogs | No modal errors or warning dialogs on launch |  |
| 4 | Try resizing the window (drag corners or edges) | Window resizes smoothly |  |
| 5 | Try fullscreen (green button on macOS, F11 on Windows/Linux) | App enters and exits fullscreen cleanly |  |

**Notes:**

|     |
|-----|
|     |

**INST-07: Record Your Environment**

*Fill in the details below. This helps us reproduce any issues you find.*

<table>
<colgroup>
<col style="width: 31%" />
<col style="width: 68%" />
</colgroup>
<tbody>
<tr>
<td><strong>App Version</strong></td>
<td>0.1.4-e2ddd7b</td>
</tr>
<tr>
<td><strong>OS Version</strong></td>
<td>Fedora Workstation 42 /<br />
NixOS Plasma 6 /<br />
Bazzite latest</td>
</tr>
<tr>
<td><strong>Chip / Architecture</strong></td>
<td>x86_64</td>
</tr>
<tr>
<td><strong>Network</strong></td>
<td>WiFi 6</td>
</tr>
</tbody>
</table>

**Notes:**

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<tbody>
<tr>
<td>For the Fedora example:<br />
CPU: Ryzen 7 9800X3D<br />
GPU: RX 9070 XT<br />
RAM: 128GB<br />
<br />
Wayland display session, KDE<br />
Mutliple distros, multiple machines, all the same issue with Wayland.</td>
</tr>
</tbody>
</table>

# Setup Guidance

*If you hit any of these issues during installation, try the workaround below before marking as Blocked.*

|  |  |
|----|----|
| **Issue** | **Workaround** |
| macOS Gatekeeper blocks the app | System Settings → Privacy & Security → Open Anyway |
| macOS "App is damaged" error | Open Terminal and run: xattr -cr "/Applications/Zephyr Agency Nightly.app" |
| Windows SmartScreen warning | Click "More info" → "Run anyway" |
| Linux AppImage won’t run | chmod +x ZephyrAgency.AppImage then run again |

# General Notes and Observations

*Record any overall impressions, patterns, or issues noticed during installation.*

|     |
|-----|
|     |

If all checks pass, proceed to **Step 1: Authentication**.

If the app fails to launch or crashes on start, record the issue in the General Notes above and report it in the Zulip QA channel before continuing.
