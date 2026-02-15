---
name: cy-mobile-use
description: Control Android devices programmatically via Droidrun Portal. Use for UI automation (tap, swipe, input), app management (launch, list), screen capture, and accessibility tree inspection. Supports HTTP REST API and WebSocket JSON-RPC protocols.
---

# Mobile Use - Android Device Control

## Overview

This skill enables programmatic control of Android devices through the **Droidrun Portal** app. It provides dual API interfaces (HTTP REST and WebSocket JSON-RPC) for comprehensive device automation including UI interaction, app management, and screen capture.

## Quick Start

### Prerequisites

1. **Connect via ADB**:
   ```bash
   adb devices  # Should show your device
   ```
2. **Set up port forwarding**:
   ```bash
   adb forward tcp:8080 tcp:8080  # HTTP API
   adb forward tcp:8081 tcp:8081  # WebSocket API
   ```
3. **Get authentication token**:
   ```bash
   adb shell content query --uri content://com.droidrun.portal/auth_token
   ```

### Setup Commands
```bash
# Port forwarding
adb forward tcp:8080 tcp:8080  # HTTP
adb forward tcp:8081 tcp:8081  # WebSocket

# Get auth token
adb shell content query --uri content://com.droidrun.portal/auth_token
```

## API Methods

### HTTP API (Port 8080)

**Authentication**: Header `Authorization: Bearer TOKEN`

**GET Endpoints** (no auth required for `/ping`):
```bash
curl http://localhost:8080/ping
curl -H "Authorization: Bearer TOKEN" http://localhost:8080/version
curl -H "Authorization: Bearer TOKEN" http://localhost:8080/state
curl -H "Authorization: Bearer TOKEN" http://localhost:8080/a11y_tree
curl -H "Authorization: Bearer TOKEN" http://localhost:8080/packages
curl -H "Authorization: Bearer TOKEN" http://localhost:8080/screenshot > screenshot.png
```

**POST Endpoints**:
```bash
# Tap
curl -X POST -H "Authorization: Bearer TOKEN" \
  -d "x=540&y=1200" http://localhost:8080/tap

# Swipe
curl -X POST -H "Authorization: Bearer TOKEN" \
  -d "startX=540&startY=1500&endX=540&endY=600&duration=300" \
  http://localhost:8080/swipe

# Launch app
curl -X POST -H "Authorization: Bearer TOKEN" \
  -d "package=com.twitter.android" http://localhost:8080/app

# Keyboard input (base64 encoded)
curl -X POST -H "Authorization: Bearer TOKEN" \
  -d "base64_text=SGVsbG8gV29ybGQ=&clear=true" \
  http://localhost:8080/keyboard/input

# Press key (e.g., Enter = 66, Backspace = 67)
curl -X POST -H "Authorization: Bearer TOKEN" \
  -d "key_code=66" http://localhost:8080/keyboard/key
```

### WebSocket API (Port 8081)

**Connection**:
```bash
wscat -c "ws://localhost:8081/?token=TOKEN"
```

**JSON-RPC Format**:

Request format:
```json
{"id": "<request_id>", "method": "<method_name>", "params": {<method_params>}}
```

Success response format:
```json
{"id": "<request_id>", "status": "success", "result": <result_data>}
```

Error response format:
```json
{"id": "<request_id>", "status": "error", "error": "<error_message>"}
```

**Examples**:
```json
// Tap at coordinates
{"id": "1", "method": "tap", "params": {"x": 540, "y": 1200}}
// Response: {"id": "1", "status": "success", "result": "Tap performed at (540, 1200)"}

// Swipe gesture
{"id": "2", "method": "swipe", "params": {"startX": 540, "startY": 1500, "endX": 540, "endY": 600, "duration": 300}}
// Response: {"id": "2", "status": "success", "result": "Swipe performed"}

// Get current time
{"id": "3", "method": "time", "params": {}}
// Response: {"id": "3", "status": "success", "result": 1770980930404}

// List installed packages
{"id": "4", "method": "packages", "params": {}}
// Response: {"id": "4", "status": "success", "result": [{"packageName": "...", "label": "...", ...}]}

// Capture screenshot (returns base64 PNG)
{"id": "5", "method": "screenshot", "params": {"hideOverlay": true}}
// Response: {"id": "5", "status": "success", "result": "iVBORw0KGgo..."} (base64 encoded PNG)

// Launch app
{"id": "6", "method": "app", "params": {"package": "com.twitter.android"}}
// Response: {"id": "6", "status": "success", "result": "Started app com.twitter.android"}

// Get device state
{"id": "7", "method": "state", "params": {}}
// Response: {"id": "7", "status": "success", "result": {"a11y_tree": {...}, "phone_state": {...}, "device_context": {...}}}

// Get app version
{"id": "8", "method": "version", "params": {}}
// Response: {"id": "8", "status": "success", "result": "0.5.5"}

// Input text (base64 encoded)
{"id": "9", "method": "keyboard/input", "params": {"base64_text": "SGVsbG8gV29ybGQ=", "clear": true}}
// Response: {"id": "9", "status": "success", "result": "input done via IME (clear=true)"}

// Press key
{"id": "10", "method": "keyboard/key", "params": {"key_code": 66}}
// Response: {"id": "10", "status": "success", "result": "Enter handled (no focused element)"}
```

**Supported Methods** (✅ Tested):

| Method | Description | Params |
|--------|-------------|--------|
| `tap` | Tap at screen coordinates | `x` (int), `y` (int) |
| `swipe` | Swipe gesture | `startX`, `startY`, `endX`, `endY` (int), `duration` (int, ms) |
| `app` | Launch application | `package` (string, package name) |
| `state` | Get device state (UI tree, etc.) | None or `{}` |
| `packages` | List installed packages | None or `{}` |
| `screenshot` | Capture screen (returns base64 PNG) | `hideOverlay` (boolean, optional) |
| `time` | Get Unix timestamp | None or `{}` |
| `version` | Get app version info | None or `{}` |
| `keyboard/input` | Input text (base64 encoded) | `base64_text` (string), `clear` (boolean, optional) |
| `keyboard/key` | Press physical key | `key_code` (int, Android keycode) |

**⚠️ Important Notes**:

1. **Screenshot data size**: Returns ~3-4MB base64 data. WebSocket client must configure `max_size` > 4MB.

2. **Response format**: Not standard JSON-RPC. Uses `status` field instead of `error` field for errors:
   - Success: `{"id": "...", "status": "success", "result": ...}`
   - Error: `{"id": "...", "status": "error", "error": "message"}`

## Common Key Codes

| Key | Code |
|-----|------|
| Enter | 66 |
| Backspace | 67 |
| Tab | 61 |
| Escape | 111 |
| Home | 3 |
| Back | 4 |
| Up | 19 |
| Down | 20 |
| Left | 21 |
| Right | 22 |



