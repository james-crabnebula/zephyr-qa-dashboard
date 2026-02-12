---
title: "Linux Distribution Issues"
date: 2026-02-09
category: bugs
tags: [linux, appimage, distribution]
author: James Barclay
status: open
build_version: 0.1.4-58a7392
---

# Bug Report: Linux Nightly Distribution Issues

**Product:** Zephyr Agency Nightly
**Version:** 0.1.4-58a7392
**Package Format:** AppImage (Linux x86_64)
**Date:** 2026-02-09
**Reported By:** James Barclay (Q-A)
**Category:** Nightly Distribution / Packaging

---

## Summary

The Linux nightly AppImage (v0.1.4-58a7392) has multiple packaging and distribution defects that prevent it from functioning correctly across standard Linux environments. These are not application-level bugs — they relate to how the nightly is compiled, bundled, and distributed for Linux targets.

The app launches on some configurations but exhibits critical failures on others. No tested Linux environment produced a fully functional experience.

---

## Environment Matrix

| Environment | Display | Result |
|-------------|---------|--------|
| Fedora (bare metal) | Wayland | **Crash on launch** |
| Ubuntu 24.04 (VM) | X11 | Launches with issues (see below) |
| Pop!_OS 22.04 (bare metal) | X11 | Launches with issues (see below) |
| Ubuntu 24.04 (Docker/Xvfb) | Virtual | Launches, functional issues |
| WSL (Fabian's dev env) | X11/virt | Appears to work |

---

## Issues

### DIST-01: Wayland crash on launch

**Severity:** Critical
**Environments:** Fedora Wayland (confirmed), likely all Wayland compositors

The AppImage crashes immediately when launched under a Wayland display server. Wayland is the default display protocol on Fedora, Ubuntu 22.04+, and most modern Linux distributions.

**Confirmed:** Crash persists with `--disable-gpu`. This is NOT a GPU rendering issue — it is a display protocol / Ozone platform issue in the Chromium layer. The Wayland backend either isn't compiled into the build or fails during protocol negotiation.

**Workaround to test:** `--ozone-platform=x11` (forces XWayland, bypasses native Wayland entirely)

**Impact:** App is unusable on the majority of current Linux desktops without manually forcing XWayland.

**Expected:** Chromium-based apps should either support Wayland natively (`--ozone-platform=wayland`) or gracefully fall back to XWayland. The recommended Tauri/Chromium approach is `--ozone-platform-hint=auto` which selects the best available backend.

---

### DIST-02: GPU context creation failure loop (100% CPU)

**Severity:** Critical
**Environments:** All non-WSL Linux environments tested

The GPU process fails to create an EGL shared context and retries infinitely:

```
ERROR:gpu_channel_manager.cc:915 ContextResult::kFatalFailure: Failed to create shared context for virtualization.
ERROR:shared_image_stub.cc:580 SharedImageStub: unable to create context
ERROR:gpu_channel.cc:710 GpuChannel: Failed to create SharedImageStub
ERROR:egl_util.cc:92 EGL Driver message (Error) eglCreateContext: eglCreateContext
ERROR:gl_context_egl.cc:355 eglCreateContext failed with error EGL_BAD_ATTRIBUTE
```

This loop repeats continuously, consuming 100% CPU in the background even when the app appears idle.

**Workaround:** Launch with `--disable-gpu`
**Root cause:** No retry limit or software rendering fallback when GPU context creation fails. The Chromium GPU process needs either `--disable-gpu-sandbox`, a fallback to SwiftShader/software rendering, or a maximum retry count.

**Note:** This does not reproduce in WSL because WSL virtualises GPU access differently.

---

### DIST-03: AppImage FUSE mount failure

**Severity:** Medium
**Environments:** All tested (container and bare metal)

```
Zephyr Agency_0.1.4_amd64.AppImage: failed to utilize FUSE during startup!
2%3%4%7%14%...100%
```

The AppImage cannot mount via FUSE and falls back to full self-extraction to a temporary directory. The fallback is designed to produce the same filesystem layout, so this should be seamless. However:

- Slower startup (full extraction before app runs)
- Additional disk usage in `/tmp`

**Note (from Fabian):** The FUSE fallback is supposed to be seamless with identical filesystem layout. This means DIST-04 (missing specialists) is a build-time bundling issue, not a FUSE path resolution issue. Still worth investigating why FUSE fails on systems with `libfuse2` available, but lower priority than the other issues.

---

### DIST-04: Bundled specialists missing from AppImage

**Severity:** High
**Environments:** All Linux

```
WARNING: Loaded 0 specialists in 0ms. Check that bundled specialists exist in src-tauri/specialists/
```

The specialists directory is not included in the Linux AppImage bundle. The warning message references a source tree path (`src-tauri/specialists/`) rather than a Tauri resource path, suggesting the resource resolution may also be incorrect.

**Tauri context:** Resources must be declared in `tauri.conf.json > bundle > resources` to be included in the AppImage. At runtime, resources should be resolved via `app.path().resolve("specialists", BaseDirectory::Resource)` — not hardcoded paths. Linux AppImage resource directory is `${APPDIR}/usr/lib/${exe_name}`, which differs from macOS (`Contents/Resources/`).

**Impact:** No specialists available on any Linux installation. Feature is completely non-functional.

---

### DIST-05: Application metadata shows "Unknown"

**Severity:** Medium
**Environments:** All Linux

The app window title and/or desktop entry display "Unknown" instead of "Zephyr Agency". This indicates the `.desktop` file, `WM_CLASS`, or AppImage metadata is not correctly set during the build.

**Expected:** Window title should read "Zephyr Agency" (or "Zephyr Agency Nightly" for nightly channel). The AppImage should include a valid `.desktop` entry and icon.

---

### DIST-06: Ubuntu 24 AppArmor conflict

**Severity:** Medium
**Environments:** Ubuntu 24.04

Ubuntu 24.04's default AppArmor policies conflict with the application. The AppImage itself has no sandbox — the conflict is with either CEF's internal Chromium sandbox or Zephyr's VFS sandbox layer. AppArmor restricts the syscalls and filesystem access these sandboxes rely on.

**Expected:** Identify which sandbox (CEF Chromium or Zephyr VFS) is triggering AppArmor denials. Check `dmesg` / `journalctl` for AppArmor `DENIED` entries. May need `--no-sandbox` for the Chromium layer or an AppArmor profile that permits the VFS sandbox operations.

---

### DIST-07: Deep link registration fails across Linux browsers

**Severity:** Medium
**Environments:** Ubuntu 24 (Snap Firefox), Pop!_OS (Debian Firefox)

OAuth deep link callback (`zephyr://` or equivalent) does not register correctly:

- **Snap Firefox (Ubuntu):** Redirects to app store instead of handling the deep link. Snap sandboxing whitelists specific URI schemes.
- **Debian Firefox (Pop!_OS):** Reports "https not understood" — no prompt on redirect.
- **Inconsistent:** Deep link worked on second attempt in one test, suggesting a timing or registration race.

**Impact:** Login flow is broken on the two most common Linux Firefox packaging formats. Users cannot authenticate.

**Expected:** Deep link handler should be registered via `.desktop` file `MimeType` field and `xdg-mime`. The AppImage build pipeline should include this registration or provide a post-install hook.

---

### DIST-08: Double close required to exit

**Severity:** Low
**Environments:** Observed on Ubuntu/Pop!_OS

The app does not fully exit on first window close. User must close twice (or force-kill the process). Likely the background GPU error loop (DIST-02) or a tray icon / background process keeping the app alive after the window is destroyed.

---

## Recommendations

### Immediate (blocks Linux nightly usability)

1. **Add `--disable-gpu` as default launch flag** in the AppImage `.desktop` entry until GPU context handling is fixed. This resolves DIST-02 (100% CPU) and may help with DIST-01 (Wayland).

2. **Add specialists to `bundle.resources`** in `tauri.conf.json` and verify resource path resolution uses the Tauri API. This resolves DIST-04.

3. **Set application metadata** (WM_CLASS, .desktop Name field, AppImage metadata). This resolves DIST-05.

### Short-term (before public Linux release)

4. **Test Wayland support** with `--ozone-platform=wayland` or ensure XWayland fallback works cleanly. Consider adding `--ozone-platform-hint=auto` to launch flags.

5. **Investigate FUSE mount failure** — confirm `libfuse2` dependency is documented and test that the self-extraction fallback preserves all resource paths.

6. **Test deep link registration** on Snap Firefox, Flatpak Firefox, and Debian-packaged Firefox. May need `xdg-open` handler registration in the AppImage runtime.

7. **Add AppArmor profile** or document workaround for Ubuntu 24+.

### Build pipeline

8. **Add a Linux smoke test to the nightly CI** that launches the AppImage in a clean Ubuntu 24.04 environment (not WSL) and verifies: window title, specialist count > 0, no GPU error loop, clean exit on close. Our Beast Docker test infrastructure can serve as a baseline.

---

## Reproduction

### Automated (Beast Docker)

```bash
ssh jimmyb@beast
cd ~/zephyr-e2e
./automation/beast/run-tests.sh regressions
```

Results in `~/zephyr-e2e/results/` — confirms DIST-03, DIST-04, and GPU-related errors.

### Manual (bare metal / VM)

```bash
chmod +x "Zephyr Agency_0.1.4_amd64.AppImage"
./"Zephyr Agency_0.1.4_amd64.AppImage"            # Observe DIST-01 through DIST-08
./"Zephyr Agency_0.1.4_amd64.AppImage" --disable-gpu  # Workaround for DIST-02
```

---

*Report generated from Q-A automated testing on Beast (Ubuntu 24.04, Docker) and manual testing by Robin (Beanow) across Fedora, Ubuntu 24, and Pop!_OS 22.*
