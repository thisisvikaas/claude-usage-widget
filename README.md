# Claude Usage Mac Widget

A floating macOS desktop widget that shows your Claude API usage at a glance. Always-on-top, translucent, draggable — lives on your desktop across all Spaces.

![macOS 13+](https://img.shields.io/badge/macOS-13%2B-blue) ![Swift](https://img.shields.io/badge/Swift-6-orange) ![License](https://img.shields.io/badge/license-MIT-green)

## What It Does

- **Circular progress ring** showing real-time utilization % with color-coded status
- **Smart pace tracking** — compares actual vs expected usage so you know if you're burning through limits too fast
- **Reset countdown** — "Resets in 4h 23m" so you know when limits refresh
- **Always visible** — floating widget stays on top of all windows, across all virtual desktops
- **Draggable** — click and drag to reposition anywhere on screen
- **Remembers position** — reopens exactly where you left it
- **Auto-refreshes** every 5 minutes

### Status Colors

| Color | Meaning |
|-------|---------|
| Green | On track — usage is below expected pace |
| Orange | Borderline — within ±5% of expected pace |
| Red | Exceeding — burning through limits faster than expected |

### Tracked Metrics

- 5-hour rolling limit
- 7-day limit (all models)
- 7-day limit (Sonnet only)

## Quick Start

### 1. Build

Requires macOS 13+ and Xcode Command Line Tools (`xcode-select --install`).

```bash
git clone https://github.com/rishiatlan/Claude-Usage-Mac-Widget.git
cd Claude-Usage-Mac-Widget
chmod +x build.sh run.sh generate-icon.sh
./build.sh
open build/ClaudeUsage.app
```

The widget will appear on your desktop automatically on first launch.

### 2. Get Your Claude Credentials

You need two values from [claude.ai](https://claude.ai):

#### Session Key

1. Open **claude.ai** in Chrome/Safari and make sure you're logged in
2. Open DevTools — `Cmd + Option + I`
3. Go to the **Application** tab (Chrome) or **Storage** tab (Safari)
4. In the left sidebar, expand **Cookies** → click **https://claude.ai**
5. Find the row named **`sessionKey`**
6. Copy the full value — it starts with `sk-ant-sid01-...`

#### Organization ID

1. Still in DevTools, switch to the **Network** tab
2. Send any message in a Claude chat
3. Look at the network requests — find any URL containing `/organizations/`
4. The UUID after `/organizations/` is your org ID
   - Example: `https://claude.ai/api/organizations/`**`a1b2c3d4-e5f6-7890-abcd-ef1234567890`**`/chat_conversations`
   - Your org ID is `a1b2c3d4-e5f6-7890-abcd-ef1234567890`

### 3. Configure

1. **Right-click** the widget → **Settings...**
2. Paste your **Session Key** and **Organization ID**
3. Select which metric to display
4. Click **Save**

The widget will immediately fetch your usage data and display it.

## Usage

| Action | How |
|--------|-----|
| Open Settings | Right-click widget → Settings... |
| Refresh data | Right-click widget → Refresh |
| Quit app | Right-click widget → Quit |
| Reposition | Click and drag the widget |
| Change metric | Settings → Display Metric dropdown |

### Environment Variable Fallback

If you prefer not to use the Settings UI, you can set environment variables:

```bash
export CLAUDE_SESSION_KEY="sk-ant-sid01-..."
export CLAUDE_ORGANIZATION_ID="your-org-uuid"
```

## How It Works

The app calls the Claude API usage endpoint every 5 minutes:

```
GET https://claude.ai/api/organizations/{orgId}/usage
Cookie: sessionKey={key}
```

It parses the response for your current utilization percentage, expected pace based on time elapsed in the billing window, and reset time — then renders it as a floating translucent widget using `NSPanel` with `.ultraThinMaterial` background.

### Architecture

Single-file Swift app (`ClaudeUsageApp.swift`) — no Xcode project, no dependencies, no frameworks beyond Cocoa and SwiftUI. Compiles with `swiftc` directly.

Key components:
- `FloatingWidgetPanel` — borderless `NSPanel` subclass (always-on-top, all Spaces, draggable)
- `WidgetView` — SwiftUI view with three states: setup, loading, data display
- `WidgetPanelController` — lifecycle manager with position/visibility persistence
- `AppDelegate` — data fetching, refresh timer, credential management

## Building from Source

```bash
# One-step build
./build.sh

# Manual build
mkdir -p build/ClaudeUsage.app/Contents/MacOS
swiftc ClaudeUsageApp.swift \
  -o build/ClaudeUsage.app/Contents/MacOS/ClaudeUsage \
  -framework Cocoa \
  -framework SwiftUI \
  -parse-as-library

# Run
open build/ClaudeUsage.app
```

## Troubleshooting

**Widget shows "Setup Needed"**
- Right-click → Settings → enter your session key and org ID

**Widget not appearing**
- The app runs as a background process (no dock icon). Check Activity Monitor for "ClaudeUsage"
- Try quitting and relaunching: `open build/ClaudeUsage.app`

**Data not loading**
- Session keys expire periodically — re-extract from claude.ai cookies
- Check that your org ID is correct
- Verify network connectivity

**Build fails with SwiftBridging error**
- Reinstall Command Line Tools: `sudo rm -rf /Library/Developer/CommandLineTools && xcode-select --install`

**Widget disappeared after restart**
- The app needs to be running for the widget to show. Enable "Launch at Login" in Settings, or add it to your Login Items manually.

## Privacy

- Credentials are stored locally in macOS UserDefaults
- No telemetry, no analytics, no data sent anywhere except to `claude.ai/api` for usage data
- Fully open source — read the single source file to verify

## Credits

Built on top of [claude-usage](https://github.com/amoga-org/claude-usage) by amoga-org. Desktop widget adaptation by [@rishiatlan](https://github.com/rishiatlan).

## License

MIT
