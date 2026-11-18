# Ethica

<div align="center">
  <img src="assets/ethica-logo.png" alt="Ethica Logo" width="128" height="128" />
  <h3>True Multi-Seat Background Desktop Streaming for Windows</h3>
  <p><b>One machine, two independent seats — stream a full background Windows desktop to Artemis or Moonlight with zero visual distraction and zero input theft.</b></p>

  [![Latest Release](https://img.shields.io/github/v/release/lupixele/Ethica-Release?include_prereleases&color=blue&label=Latest%20Release)](https://github.com/lupixele/Ethica-Release/releases/latest)
  [![Platform](https://img.shields.io/badge/Platform-Windows%2010%20%7C%2011%20x64-0078D6.svg)](https://github.com/lupixele/Ethica-Release)
  [![License](https://img.shields.io/badge/Distribution-Binary%20Release-green.svg)](https://github.com/lupixele/Ethica-Release)
</div>

---

## ⚡ Overview

**Ethica** turns your single Windows PC into a true dual-seat workstation. 

While you play games, browse, or code at your primary desk, a secondary local Windows user (e.g., `EthicaUser` or any local account) runs an isolated, full-featured Windows desktop completely in the background. The background desktop is captured on a hardware-accelerated virtual display and streamed over ultra-low-latency GameStream / NVENC protocol directly to **Artemis** or **Moonlight** on a phone, tablet, or secondary laptop.

- **No Windows on your screen**: The background session runs completely headlessly.
- **No focus or input interference**: Your keyboard, mouse, and gamepads remain 100% locked to your foreground tasks.
- **Hardware accelerated**: Uses your discrete GPU (NVIDIA RTX/GTX, AMD Radeon, Intel Arc) for smooth high-FPS rendering and NVENC/AMF video encoding.
- **True native scaling**: Custom resolutions (e.g., 2214×1080, 2400×1080) and native Windows DPI scaling inside the remote session ensure crisp mobile UI elements without blurry bilinear stretching.

---

## 📥 Download

Get the latest installer from the [GitHub Releases page](https://github.com/lupixele/Ethica-Release/releases/latest):

| File | Version | Platform | SHA-256 Checksum |
|---|---|---|---|
| [`Ethica-Setup-1.0.5.exe`](https://github.com/lupixele/Ethica-Release/releases/download/v1.0.5/Ethica-Setup-1.0.5.exe) | **v1.0.5** | Windows 10/11 x64 | `49B520789988375B624D94B811EAEA99BBBA097E165AC9628452EC2C72EDF558` |

> *The installer is also tracked directly in the root of this release repository.*

---

## 🏗️ Architecture

```
       Your Phone / Tablet                             Your Host PC
   ┌─────────────────────────┐          WiFi       ┌─────────────────────────────────────────────────┐
   │ Artemis / Moonlight App │ ◄─────────────────► │ Apollo Streaming Engine (:56587 / :56588)       │
   └─────────────────────────┘                     │  - NVENC / AMF Hardware Video Encoder           │
                                                   │  - Opus Audio Passthrough                       │
                                                   └────────────────────────┬────────────────────────┘
                                                                            │ Captures
                                                                            ▼
                                                   ┌─────────────────────────────────────────────────┐
                                                   │ Headless Virtual Display Adapter                │
                                                   │  - Native Phone Aspect Ratio (e.g. 2214x1080)   │
                                                   │  - Internal DPI Scaling (e.g. 150%, 175%)       │
                                                   └────────────────────────┬────────────────────────┘
                                                                            │ Renders
                                                                            ▼
                                                   ┌─────────────────────────────────────────────────┐
                                                   │ Secondary Windows User Session (e.g. "EthicaUser")   │
                                                   │  - Isolated Terminal Services Background RDP         │
                                                   │  - Independent Explorer, Apps, & Audio Engine        │
                                                   └─────────────────────────────────────────────────┘
```

The underlying session is maintained invisibly through Windows Terminal Services. Because the RDP harness operates headlessly, no mirrored window ever interrupts your desktop.

---

## ✨ Features

- **True Headless Operation**: The remote session lives independently. Nothing minimizes your full-screen games or steals focus.
- **On-Demand "View Desktop"**: Need to check what the background user is doing? Click **View Desktop** in the Ethica dashboard to pop up a live look-through window. Closing it leaves the session running undisturbed.
- **Smart GPU Routing**: Automatically binds to your primary high-performance discrete GPU on dual-GPU Optimus laptops, preventing DXGI pipeline stall and driver crashes.
- **Port-Based Firewall Integration**: Installer configures port-based inbound firewall rules so changes to binary locations never break network discovery or streaming.
- **Dual Audio Support**: Audio plays cleanly through your phone's client while your host PC speakers can simultaneously output your foreground games or music.
- **Live Tray & Service Monitor**: Ethica runs as a managed Windows service (`EthicaService`) with a lightweight WPF dashboard in the system tray.

---

## 🚀 Quick Start Guide

### 1. Requirements
* **Operating System**: Windows 10 or Windows 11 (64-bit).
  * *Note: Multi-seat concurrent user sessions require Windows Terminal Services support (standard on Windows Pro / Enterprise / Education, or enabled via multi-session RDP configuration on Home editions).*
* **GPU**: NVIDIA GeForce RTX/GTX, AMD Radeon, or Intel Arc.
* **Client Device**: Android or iOS device running **Artemis** (recommended) or **Moonlight Game Streaming**.

### 2. Setup Secondary Windows User
1. Open Windows **Settings** → **Accounts** → **Other users**.
2. Click **Add account** and select *"I don't have this person's sign-in information"*, then *"Add a user without a Microsoft account"*.
3. Name the account (e.g., `EthicaUser`) and assign a secure password.
4. Sign into that account once on your PC to let Windows initialize its profile directory, then sign back out to your primary account.

### 3. Install Ethica
1. Download and run [`Ethica-Setup-1.0.4.exe`](https://github.com/lupixele/Ethica-Release/releases/latest).
2. The installer automatically registers `EthicaService`, provisions firewall rules for streaming ports (`56587`, `56588`, `56500`), and stages the virtual display driver.

### 4. Start Streaming
1. Launch **Ethica** from your Start Menu or Desktop shortcut.
2. In the dashboard:
   - Enter your secondary account **Username** (e.g., `EthicaUser`) and **Password**.
   - Set your preferred resolution (e.g., `2214` × `1080`) and display scaling percentage.
3. Click **Start Session**.
4. Open **Artemis** or **Moonlight** on your phone:
   - Your PC will appear automatically in the client list.
   - Tap your PC to connect. If pairing for the first time, enter the PIN displayed in Artemis into the Apollo web manager (`https://localhost:56588` or through the Ethica tray).
   - Tap **Desktop** to immediately control your background Windows seat!

---

## 🛠️ Configuration

Configuration is stored in `%ProgramData%\Ethica\ethica.json` and is preserved across all updates and reinstalls:

```jsonc
{
  "UserName": "EthicaUser",         // Secondary Windows account username
  "ServerName": "Ethica-Background",
  "EnginePort": 56587,              // Streaming port
  "Width": 2214,                    // Remote desktop width (matches phone screen)
  "Height": 1080,                   // Remote desktop height
  "Scale": 175,                     // Internal Windows DPI scaling percentage
  "RdpClient": 0                    // 0 = FreeRDP (recommended), 1 = mstsc
}
```

---

## 📋 Release Notes

### v1.0.5 (Current)
- **Automated Teardown on Uninstall**: The uninstaller now completely purges all virtual display adapters (`ROOT\DISPLAY\0000`), uninstalls driver packages (`sudovda.inf`) from the Windows Driver Store via `pnputil`, removes all `*Ethica*` firewall rules, cleans up `%ProgramData%\Ethica`, and automatically restarts Windows Explorer to cleanly flush DWM and RPC stub caches. Zero orphan artifacts remain after uninstallation.
- **Permanent Elimination of `DWMFRAMEINTERVAL`**: Removed legacy registry injection from runtime session launch and added proactive purge of `DWMFRAMEINTERVAL` across Terminal Server WinStations and RDP-Tcp keys, permanently eliminating the "The stub received bad data" (`RPC_S_INVALID_STUB_DATA` 1783) error on Windows 11.
- **Enhanced Process & Session Cleanup**: Uninstallation terminates all running background streaming engine and FreeRDP processes before removing files, preventing locked binaries.

### v1.0.4
- **Resolved "Remember Password" App Crash**: Granted full write permissions on `%ProgramData%\Ethica` via Inno Setup and `icacls`, and added defensive exception handling in `ConfigStore.Save` to ensure standard non-elevated desktop accounts can safely store settings without permission errors.
- **Bundled OpenSSL Legacy Provider (`legacy.dll`)**: Bundled `ossl-modules/legacy.dll` with FreeRDP and set `OPENSSL_MODULES` to support MD4 hashing for Windows NLA / NTLM authentication, eliminating `ERRCONNECT_LOGON_FAILURE`.
- **Primary vs. Secondary Account Guard**: The UI now actively prevents users from selecting the active foreground Windows account (which causes immediate RDP session collision and error 131092) and guides the user to select their dedicated secondary background account.
- **Friendly Exit Codes**: Mapped error `131092` to a clear, actionable authentication diagnostic in the dashboard.

### v1.0.3
- **Firewall Rule Synchronisation**: Updated installer firewall allow rules to cover dedicated Apollo streaming ports (`56580-56595`) and the control API port (`56500`).
- **Install-Time Engine State Purge**: Added automated cleanup of stale engine state, personal configuration files, and old certificates during installation/upgrade so existing installs upgrade impartially.

### v1.0.2
- **Resolved "The Stub Received Bad Data" RPC Errors**: Eliminated legacy RemoteFX registry overrides (`fEnableHardwareGraphicsCapture` and `fEnableRemoteFXAdv`) and `UserPreferencesMask` writes that caused explorer RPC stub data corruption on file/app launches in modern Windows 10/11 (build 26100+). Installer actively purges these legacy keys.
- **Eliminated Duo Manager Conflicts**: Relocated Apollo engine ports to `56587`, `56585`, and `56588` to avoid colliding with Duo Manager's default ports (`47984`, `47990`). EthicaService process supervision now strictly targets `apollo.exe` and will never kill Duo Manager's `sunshine.exe` background processes.
- **Virtual Display Revert on Disconnect**: Enabled `dd_config_revert_on_disconnect` so SudoVDA virtual displays release immediately upon session termination, preventing orphan displays from locking resources.
- **Sanitized Staging Packaging**: Purged developer machine configurations, logs, and client certificates from the engine staging package to guarantee clean out-of-the-box installs on fresh systems.

### v1.0.1
- **Resolved Video Error on Optimus Laptops**: Fixed a critical adapter binding issue where virtual display adapters (`SudoMaker Virtual Display Adapter`) could override the discrete GPU, causing encoder failure during Apollo stream initialization.
- **Discrete GPU Auto-Resolution**: The service now dynamically detects and binds the primary hardware rendering GPU (`NVIDIA GeForce RTX` / `AMD Radeon`) to ensure zero-lag NVENC encoding.
- **Port-Based Firewall Architecture**: Fixed connection drops and mDNS discovery issues by provisioning persistent port-based rules (`56500`, `56587`, `56588`).
- **FreeRDP Default Pipeline**: Hardened default session attachment with native DPI scaling and audio redirection.

### v1.0.0
- Initial stable release with isolated background multi-seat streaming, hidden RDP lifecycle manager, and bundled virtual display drivers.

---

## 🔒 Security & Privacy Notice

Ethica encrypts session credentials locally using the Windows Data Protection API (DPAPI). Password hashes are never transmitted off your machine. The application source code is private; this repository is the official public release distribution channel for compiled binaries and setup packages.

---

## 🤝 Support & Issues

If you run into issues or have questions regarding display scaling or client pairing:
- Open an issue on this repository: [Issues](https://github.com/lupixele/Ethica-Release/issues)
- Check that your client device is on the same local network subnet or configured over Tailscale/WireGuard.
