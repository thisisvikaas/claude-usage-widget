# Claude Usage Mac Widget

A lightweight macOS desktop widget that shows your Claude usage in real time — with pace tracking and reset countdown.

<p align="center">
  <img src="assets/screenshot.png" width="720" alt="Claude Usage Mac Widget Screenshot" />
</p>

<p align="center">
  <img src="https://img.shields.io/badge/macOS-13%2B-black" />
  <img src="https://img.shields.io/badge/Swift-5.9-orange" />
  <img src="https://img.shields.io/badge/License-MIT-green" />
</p>

---

## Why This Exists

Claude usage limits are easy to hit without noticing.

This widget keeps the important signals visible at all times:

- Current usage percentage  
- Pace vs time elapsed  
- Reset countdown  
- Clear status color (green / orange / red)  

No tab switching. No guesswork.

---

## Features

- Live progress ring with usage %
- Pace tracking (actual vs expected usage)
- Reset countdown timer
- Always-on-top floating panel
- Draggable (remembers position)
- Auto-refresh (every 30 seconds)
- Session expiry detection
- Minimal, translucent macOS-native UI

---

## Quick Start (2 Minutes)

### Requirements

- macOS 13+
- Xcode Command Line Tools  
  ```bash
  xcode-select --install
  ```

---

### 1. Clone & Build

```bash
git clone https://github.com/rishiatlan/Claude-Usage-Mac-Widget.git
cd Claude-Usage-Mac-Widget
chmod +x build.sh run.sh setup.sh generate-icon.sh
./build.sh
```

Launch the app:

```bash
open build/ClaudeUsage.app
```

---

### 2. Setup Credentials (Recommended)

```bash
./setup.sh
```

`setup.sh` will:

1. Prompt for your `sessionKey`
2. Automatically fetch your Organization ID
3. Validate credentials with a test call
4. Save configuration
5. Offer to launch the widget

If your session expires later, simply run `./setup.sh` again.

---

## Usage

| Action | How |
|--------|------|
| Open Settings | Right-click → **Settings** |
| Refresh | Right-click → **Refresh** |
| Quit | Right-click → **Quit** |
| Move widget | Click + drag |
| Change metric | Settings → Display Metric |

---

## Choosing a Metric

| Metric | Best For |
|--------|----------|
| 5-Hour (Recommended) | Daily pacing awareness |
| 7-Day (All Models) | Weekly budgeting |
| 7-Day (Sonnet Only) | Heavy Sonnet usage tracking |

---

<details>
<summary><strong>Understanding Claude Limits</strong></summary>

Claude enforces two independent limits.

### 5-Hour Rolling Window
- Tracks the last 5 hours of usage
- Automatically recovers as time passes
- Most useful for daily management

### 7-Day Weekly Limit
- Total weekly allowance
- Hard reset once per week

---

### Status Colors (Pace Tracking)

The widget compares actual usage vs expected usage based on elapsed time.

| Color | Meaning |
|-------|---------|
| Green | More than 5% below expected pace |
| Orange | Within ±5% of expected pace |
| Red | More than 5% above expected pace |

</details>

---

<details>
<summary><strong>Manual Setup (Alternative)</strong></summary>

### Get Your Session Key

1. Open claude.ai (logged in)
2. Open Developer Tools
3. Go to Application → Cookies
4. Copy `sessionKey`

### Get Your Organization ID

1. Open Developer Tools → Network
2. Send any message in Claude
3. Find a request containing `/organizations/`
4. Copy the UUID

Paste both values into the widget Settings.

</details>

---

<details>
<summary><strong>How It Works</strong></summary>

The widget:

- Calls:
  ```
  GET https://claude.ai/api/organizations/{orgId}/usage
  ```
- Authenticates using the `sessionKey` cookie
- Refreshes every ~30 seconds
- Renders a floating panel using:
  - SwiftUI
  - NSPanel (always-on-top window)

The app is intentionally lightweight and minimal.

</details>

---

## Troubleshooting

| Issue | Fix |
|-------|-----|
| “Setup Needed” | Run `./setup.sh` |
| “Session Expired” | Re-run setup with fresh `sessionKey` |
| Widget not visible | Check Activity Monitor → relaunch app |
| Data not loading | Likely authentication issue — re-run setup |

---

## Roadmap

- Menu bar mode
- Launch at login
- Optional usage history graph
- Signed / notarized build

---

## License

MIT
