<!-- BEGIN:nextjs-agent-rules -->

## This is NOT the Next.js you know

This version has breaking changes — APIs, conventions, and file structure may all differ from your training data. Read the relevant guide in `node_modules/next/dist/docs/` (resolved from this file's directory; in monorepos the `next` package may not be visible from the repo root) before writing any code. Heed deprecation notices.

This block is written and re-added by `next dev` — verify at `node_modules/next/dist/server/lib/generate-agent-files.js`. Removing it from a diff only re-creates the uncommitted change; committing it with your work keeps the tree clean.

<!-- END:nextjs-agent-rules -->

<!-- This is rules for Agent to follow when making changes in this project ( Important - to Agent ) -->

---
## 2. Agent Roles & Responsibilities
### 2.1 Hardware / IoT Specialist (ESP32)
- Interface with posture sensors (distance/ultrasonic/ToF, IMU/accelerometer tilt, ambient light sensor).
- Implement robust Wi-Fi connectivity supporting Mobile Hotspot and Campus Wi-Fi (handling NAT / port considerations).
- Establish low-latency bidirectional sync with Firebase Realtime Database:
  - Stream live sensor metrics, status (`SAFE`, `WARNING`, `ALARM`), and posture quality score.
  - Listen for remote command updates (threshold adjustments: distance, angle, alert delay; remote Snooze and Calibrate triggers).
- Maintain compatibility with ESP32 Arduino Core 2.x and 3.x.
### 2.2 Frontend & Full-Stack Web Architect
- Build a responsive web application deployable on **Vercel**.
- Implement Firebase Client SDK integration for instant live data sync without polling.
- Render interactive and visually informative dashboards:
  - **Live Sensor Telemetry:** Distance, ambient light, tilt angle, status badge (`SAFE` / `WARNING` / `ALARM`), real-time posture score (0–100).
  - **Dynamic Posture Avatar:** Real-time 2D/SVG or Canvas avatar tilting and shifting according to physical pitch/roll angles.
  - **Historical Telemetry Charts:** Interactive time-series trends (distance, light intensity, posture score over time).
  - **Daily Aggregates & Insights:** Total sitting duration, alert frequencies, ergonomic score summary.
  - **Alert & Event Logs:** Chronological audit trail of warnings and alarms with timestamps.
  - **Two-way Control Panel:** Threshold sliders/inputs, "Calibrate Neutral Position" trigger, and "Snooze Alarm" button synced back to ESP32 via Firebase.
- Provide clean internationalization (i18n) for **TH / EN**.
### 2.3 Quality Assurance & Delivery Assistant
- Ensure code modularity, strict error handling, and network disconnect recovery.
- Maintain persistent documentation: [`agent.md`](file:///C:/Users/gumaz/.gemini/antigravity/scratch/posture-iot-monitor/agent.md), [`design.md`](file:///C:/Users/gumaz/.gemini/antigravity/scratch/posture-iot-monitor/design.md), and [`change_log.md`](file:///C:/Users/gumaz/.gemini/antigravity/scratch/posture-iot-monitor/change_log.md).
---
## 3. System Architecture & Data Flow
```mermaid
flowchart LR
    ESP[ESP32 Microcontroller] <-->|Bidirectional Realtime Sync| FB[(Firebase Realtime Database)]
    FB <-->|WebSocket / Web SDK| WEB[Clean Medical Dashboard\n(Vercel)]
    WEB --> USER[User / Presentation Screen]
```
### Data Schema (Firebase Realtime Database)
```json
{
  "device": {
    "status": {
      "state": "SAFE",
      "score": 92,
      "distance_cm": 52.4,
      "tilt_pitch": 4.2,
      "tilt_roll": -1.1,
      "ambient_lux": 340,
      "last_updated": 1774076160
    },
    "controls": {
      "target_distance_min": 40.0,
      "target_angle_max": 15.0,
      "alarm_delay_sec": 5,
      "snooze_trigger": false,
      "calibrate_trigger": false
    },
    "history": {
      "-NxyzLogID": {
        "timestamp": 1774076100,
        "event": "ALARM_TRIGGERED",
        "reason": "Pitch angle exceeded 20 deg for > 5s"
      }
    }
  }
}
```
---

## 4. Workflows & Guidelines for AI Agent Execution
1. **Always reference the Clean Medical design guidelines:** Avoid cluttered dark-mode cyber themes unless toggled; prioritize soft whites, mint/teal clinical accents, crisp typography, and accessibility.
2. **Handle network drops gracefully:** Ensure the ESP32 code reconnects automatically to Wi-Fi/Firebase and queues critical alerts.
3. **Keep `change_log.md` updated** after every milestone, feature addition, or configuration modification.

