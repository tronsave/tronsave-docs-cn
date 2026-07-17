---
name: keep-mac-awake
description: Mac is configured to never system-sleep (keep apps running while locked)
metadata: 
  node_type: memory
  type: project
  originSessionId: a706fcbb-acc0-4a18-add2-36fa67c9866e
---

User wants apps/dev-servers/builds to keep running while the Mac is locked, on battery AND AC (set up 2026-06-10).

Implemented as a persistent user LaunchAgent (no sudo needed):
- File: `~/Library/LaunchAgents/com.louisai.keepawake.plist`
- Runs `/usr/bin/caffeinate -i -m -s` with `RunAtLoad` + `KeepAlive` (auto-starts at login, auto-restarts).
- `-i` prevents idle system sleep (works on battery), `-m` disk, `-s` system-sleep on AC.

Limitation: does NOT prevent clamshell (lid-close) sleep — that needs `sudo pmset -a disablesleep 1`. Screen still locks and display can still sleep; only system sleep is blocked.

Disable: `launchctl bootout gui/$(id -u)/com.louisai.keepawake && rm ~/Library/LaunchAgents/com.louisai.keepawake.plist`

Related: [[build-environment]] (the projects this keeps running).
