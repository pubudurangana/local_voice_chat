# local_voice_chat
Conference call, Voice Chat App which can run locally in your organization without needing Internet Connection. 


# Voice Chat Pro - comprehensive User Manual

This manual consolidates all installation, troubleshooting, and advanced usage guides for Voice Chat Pro, including historical bug fix logs and design documents.

---

## 📑 Table of Contents
1. [Installation & Deployment](#1-installation--deployment)
2. [Network & Firewall Setup](#2-network--firewall-setup-important)
3. [Mobile Usage](#3-mobile-usage)
4. [Advanced Features (Wake Lock)](#4-advanced-features)
5. [Troubleshooting & Diagnostics](#5-troubleshooting--diagnostics)
6. [Development History & Technical Notes](#6-development-history--technical-notes)

---

## 1. Installation & Deployment

### Moving to a New Machine
1. **Copy the Folder**: Transfer the entire `voice-chat-app` folder to the new Windows PC.
2. **Install Node.js**: Download and install from [nodejs.org](https://nodejs.org/).
3. **Install Dependencies**:
   Open a terminal in the folder and run:
   ```bash
   npm install
   ```
4. **Start Server**:
   ```bash
   npm start
   ```

### Quick Start
- **Start**: Run `npm start`.
- **Access**: `https://[YOUR-IP]:3001` or `https://localhost:3001`.
- **SSL**: Accept self-signed certificate warning.
- **Port**: Default is 3001.

---

## 2. Network & Firewall Setup (IMPORTANT)

**Issue**: You might experience "One-way Audio" (you hear them, they don't hear you) if devices are on different subnets (e.g., WiFi vs LAN).

### ✅ Automatic Fix (Recommended)
We have included a script to configure Windows Firewall automatically.

1. Locate `FIREWALL-FIX.ps1` in the project folder.
2. **Right-Click** -> **Run with PowerShell** (Run as Administrator).
3. Restart the server (`npm start`).

### 🔧 Manual Firewall Rules
If you cannot run the script, open *Windows Defender Firewall* and allow:
- **App**: `node.exe` (Allow Inbound UDP/TCP)
- **Port**: `3001` (TCP)
- **UDP Range**: `1024-65535` (Inbound, for WebRTC Audio)

### 🔒 HTTPS & Security
The app uses self-signed certificates for HTTPS (required for Microphone access).
- **Browser Warning**: You will see "Your connection is not private".
- **Action**: Click **Advanced** -> **Proceed to [IP] (unsafe)**.
- **Certificates**: If needed, regenerate keys using `openssl` (see `ssl/` folder).

---

## 3. Mobile Usage

### connecting with Android/iOS
1. **Connect to WiFi**: Phone must be on the same network/VPN as the server.
2. **Open Browser**: Use **Chrome** (Android) or **Safari** (iOS).
3. **Enter URL**: `https://[SERVER-IP]:3001` (e.g., `https://10.198.0.120:3001`).
4. **Permissions**:
   - Accept the "Not Secure" warning.
   - **Allow Microphone** access when prompted.

**Mobile Tips:**
- Use **Headphones** to prevent echo.
- Keep the specific browser tab **open and active**.
- If audio cuts out, refresh the page.

---

## 4. Advanced Features

### ☀️ Wake Lock (Screen Keep-Awake)
To prevent your phone from sleeping and cutting the connection, we use the **Screen Wake Lock API**:
- **Smart Activation**: Wake lock only activates when *other users* are in the room. If you are alone, the screen is allowed to sleep to save battery.
- **Indicator**: Look for "☀️ Screen Awake" at the bottom right.
- **Fallback**: For older browsers (like Firefox Android), we use an invisible video loop to keep the screen on.
- **Troubleshooting**: If screen dims, tap the screen once or check Android "Battery Optimization" settings.

Target browsers: Chrome/Edge (Android), Safari (iOS), Samsung Internet.

### 🔊 Audio Processing
- **Echo Cancellation**: Enabled by default.
- **Noise Suppression**: Filters background hum.
- **Auto Gain**: Normalizes volume levels.

### 🎨 UI/UX Features
- **Visual Feedback**: Audio bars animate when users speak.
- **Private Rooms**: Create isolated rooms for secure conversations.
- **Responsive Design**: fully functional on Desktop and Mobile.
- **Theme**: Dark mode interface with easy-to-read cards.

---

## 5. Troubleshooting & Diagnostics

### 🕵️ In-App Diagnostics (New!)
If connections fail, use the built-in diagnostic panel:
1. Press **`Shift + D`** on your keyboard.
2. Check the panel in the bottom-right:
   - **ICE State**: Should be `connected`.
   - **RX (kb)**: Should be increasing (audio data is arriving).
   - **Packets**: If `0` rx, Firewall is likely blocking audio.

### Common Issues

| Symptom | Cause | Solution |
|---------|-------|----------|
| **Can't Connect** | Server down or Firewall | Check `npm start` looks good. Run `FIREWALL-FIX.ps1`. |
| **One-way Audio** | UDP blocked | Run `FIREWALL-FIX.ps1`. Check Router ACLs if on different VLANs. |
| **"Not Secure"** | Self-signed Cert | Click "Advanced" -> "Proceed" in browser. |
| **Echo** | Mic picking up Speaker | Use headphones. Lower volume. |
| **Mobile Disconnects** | Screen off | Keep screen on or use Chrome/Safari which supports Wake Lock. |
| **Private Room Fail** | Button issues | Fixed in v1.1.0 using event delegation. |
| **Emoji Issues** | CSS Conflict | Fixed by removing ::first-letter selector in style.css. |

---

## 6. Development History & Technical Notes

### Fix Log & Improvements

#### 🐛 Bug Fixes
- **Private Room Buttons**: Fixed an issue where "Join" buttons inside dynamically created private room cards were not clickable. Moved from inline `onclick` to event delegation.
- **Emoji Rendering**: Fixed a CSS bug where the first letter of messages was being styled incorrectly, breaking emoji display. Removed `::first-letter` pseudo-element.
- **Samsung Wake Lock**: Fixed an issue where Samsung Internet browser wasn't respecting the Wake Lock API. Added a specific workaround using `visibilitychange` events.
- **CSS Selectors**: Cleaned up global CSS selectors that were affecting unintended elements (e.g., `p`, `span`).

#### 🚀 Enhancements
- **Smart Wake Lock**: Changed logic to only keep screen awake when *needed* (i.e., when others are in the room). Otherwise, allows screen to sleep.
- **Diagnostics**: Added `Shift+D` panel for network debugging.
- **Firewall Script**: Automation script (`FIREWALL-FIX.ps1`) for easier network setup.
- **Rebranding**: Project codename "NetSecure Talk" merged into main branch as "Voice Chat Pro".

#### 🧪 Testing
Validated on:
- **Desktop**: Windows 10/11 (Chrome, Edge, Firefox).
- **Mobile**: Android 13 (Chrome, Samsung Internet), iOS 16 (Safari).
- **Network**: Tested across LAN subnets (10.x.x.x <-> 10.x.x.x).

*Note: This section summarizes contents from previously separate technical logs (BUG_FIXES_LOG.txt, UI_UX_ENHANCEMENTS.txt, etc) which have now been archived.*
