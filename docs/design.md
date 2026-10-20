System & UI/UX Design Specification

Project: Smart Posture & Ergonomics IoT Monitoring System
Hardware Core: ESP32 + Sharp GP2Y0A21YK0F + LDR + MPU6050 + LM358 + 74HC74
Cloud & Web: Firebase Realtime Database + Next.js / React (Hosted on Vercel)
Visual Style: Clean Medical

1. Hardware Architecture & Signal Conditioning
1.1 Sensor & IC Lineup
Distance Sensor (Sharp GP2Y0A21YK0F):
Operating range: 10–80 cm.
Non-linear inverse voltage output.
Signal conditioning: RC Low-pass filter to eliminate high-frequency ripple/noise + LM358 (Op-Amp 1) as Voltage Follower (Buffer) into ESP32 ADC.
Ambient Light (LDR):
Voltage divider with fixed precision resistor.
Measures desk/workstation lux levels (good lighting vs. eye strain conditions).
Inertial Measurement Unit (MPU6050):
Interface: I2C (SDA: GPIO21, SCL: GPIO22, Address 0x68).
Senses 3-axis acceleration & angular velocity.
Roll and Pitch calculated via trigonometry: 
Pitch
=
atan2
(
𝐴
𝑦
,
𝐴
𝑥
2
+
𝐴
𝑧
2
)
×
180
𝜋
Pitch=atan2(A
y
	​

,
A
x
2
	​

+A
z
2
	​

	​

)×
π
180
	​

 
Roll
=
atan2
(
−
𝐴
𝑥
,
𝐴
𝑧
)
×
180
𝜋
Roll=atan2(−A
x
	​

,A
z
	​

)×
π
180
	​

Hardware State Logic (LM358 & 74HC74):
LM358 (Unit 2): Comparator comparing distance/tilt threshold against analog voltage reference to output clean digital edges.
74HC74 (Dual D Flip-Flop): Hardware latch preserving physical state (SAFE, WARNING, ALARM) triggered on positive clock edges, ensuring physical hardware state verification.
ESP32 Interfacing: ESP32 reads Q outputs of 74HC74 via GPIO digital inputs to sync the verified physical hardware state with Firebase.
2. Cloud Data Architecture (Firebase Realtime Database)
/posture_device
  ├── /live
  │     ├── distance_cm: 54.2
  │     ├── pitch_deg: 5.1
  │     ├── roll_deg: -1.2
  │     ├── ambient_lux: 420
  │     ├── state: "SAFE" | "WARNING" | "ALARM"
  │     ├── hardware_flags: { q1: 0, q2: 0 }
  │     ├── posture_score: 95
  │     └── timestamp: 1774076400
  ├── /controls
  │     ├── target_distance_min: 40.0
  │     ├── target_angle_max: 15.0
  │     ├── alarm_delay_sec: 5
  │     ├── snooze_trigger: false
  │     └── calibrate_trigger: false
  ├── /daily_stats
  │     ├── total_sitting_minutes: 245
  │     ├── warning_count: 8
  │     ├── alarm_count: 3
  │     └── avg_score: 88
  └── /logs
        └── /$log_id
              ├── timestamp: 1774076380
              ├── state: "ALARM"
              └── message: "Head pitched forward > 20° for 5 seconds"
3. Web Dashboard Design ("Clean Medical" Aesthetics)
3.1 Design Principles & Color Palette
Concept: Calm, clinical, ergonomic, distraction-free medical grade interface.
Background: Soft Clinical Off-White (#F8FAFC, #F1F5F9).
Surface / Cards: Pure White (#FFFFFF) with subtle cool-gray borders (#E2E8F0) and soft ambient shadows (0 4px 12px rgba(0,0,0,0.03)).
Primary Brand / Trust: Teal & Medical Slate Blue (#0D9488, #0284C7).
Semantic Status Palette:
SAFE: Clinical Mint Green (#10B981 / background #ECFDF5)
WARNING: Warm Amber (#F59E0B / background #FFFBEB)
ALARM: Crimson Coral (#EF4444 / background #FEF2F2)
Typography: Inter or Plus Jakarta Sans with clear numerical hierarchy (Tabular figures for real-time sensor metrics).
3.2 Responsive & Presentation Modes
Mobile View: Single-column card stack with quick status badge and quick-action Calibrate/Snooze buttons floating at bottom.
Desktop View: 3-column modular dashboard (Live telemetry & 3D avatar on left/center, controls & stats on right).
Projector / Presentation Mode: High-contrast full-screen toggle with extra-large typography, prominent live avatar tilt, and real-time event ticker visible from 5+ meters.
4. UI Component Architecture
5. Next.js Frontend Folder Structure
text
frontend/
├── public/
│   ├── icons/                  # Custom SVG icons (spine, chair, eye, etc.)
│   └── sounds/                 # Alert audio cues (alert.mp3, beep.mp3)
├── src/
│   ├── app/                    # Next.js App Router
│   │   ├── layout.tsx          # Root Layout (Theme, Font, Context Provider)
│   │   ├── page.tsx            # Main Dashboard View (Desktop/Mobile)
│   │   ├── projector/          # Projector Presentation Mode (/projector)
│   │   │   └── page.tsx
│   │   └── globals.css         # Tailwind & Clean Medical variables
│   ├── components/             # Modular UI Components
│   │   ├── layout/             # Header, Status Banner, Connection Status
│   │   │   ├── Header.tsx
│   │   │   └── StatusBanner.tsx
│   │   ├── dashboard/          # Metric telemetry display cards
│   │   │   ├── VitalCardsGrid.tsx
│   │   │   ├── DistanceCard.tsx
│   │   │   ├── TiltCard.tsx
│   │   │   ├── LightCard.tsx
│   │   │   └── ScoreCard.tsx
│   │   ├── avatar/             # 2D/SVG Posture Silhouette
│   │   │   ├── PostureAvatar.tsx
│   │   │   └── PostureSkeleton.tsx
│   │   ├── charts/             # Live and historical telemetry charts
│   │   │   ├── RealtimeChart.tsx
│   │   │   └── HistoryAnalytics.tsx
│   │   ├── controls/           # Bidirectional control panel (ESP32 setpoints)
│   │   │   ├── ControlPanel.tsx
│   │   │   ├── ThresholdSliders.tsx
│   │   │   └── ActionButtons.tsx
│   │   ├── logs/               # Audit log & daily insights
│   │   │   ├── EventLogTable.tsx
│   │   │   └── DailyStatsSummary.tsx
│   │   └── ui/                 # Design System Primitives
│   │       ├── Badge.tsx
│   │       ├── Button.tsx
│   │       ├── Card.tsx
│   │       ├── Modal.tsx
│   │       └── Slider.tsx
│   ├── contexts/               # Global Context Providers
│   │   ├── FirebaseContext.tsx
│   │   └── LanguageContext.tsx
│   ├── hooks/                  # Custom React Hooks
│   │   ├── useFirebaseDevice.ts
│   │   ├── usePostureScore.ts
│   │   └── useTranslation.ts
│   ├── lib/                    # Library Initializations & Utilities
│   │   ├── firebase.ts
│   │   └── utils.ts
│   ├── locales/                # Translation Dictionaries
│   │   ├── en.json
│   │   └── th.json
│   └── types/                  # TypeScript Definitions
│       ├── device.ts
│       └── logs.ts
├── .env.example
├── .env.local
├── next.config.ts
├── tailwind.config.ts
├── tsconfig.json
└── package.json
4.1 Detailed Component Specifications

Header & Global Controls:

System connection indicator (Connected to ESP32 / Offline).
Language selector (TH / EN).
Projector mode toggle (Full screen high-contrast view).

Vital Metrics Grid (Cards):

Distance Card: Shows current reading in cm with visual safe-distance gauge (optimal: 45–65 cm).
Tilt Card: Shows Pitch and Roll angles with color-coded deviation indicators.
Lighting Card: Ambient illuminance level (Lux) with recommendation indicator ("Good", "Too Dim", "Glare").
Ergonomic Score Card: Circular radial progress (0–100) calculated from posture stability and duration.

Dynamic Posture Avatar (Animated Visualization):

Vector SVG/Canvas representation of a seated person viewed from the side profile.
Spine curvature and neck/head angle transform dynamically via CSS/Canvas matrix rotations using real-time pitch_deg and roll_deg.
Color of avatar skeleton transitions dynamically between Mint Green (SAFE), Amber (WARNING), and Red (ALARM).

Historical Telemetry Chart:

Multi-axis or synchronized time-series chart (Recharts / Chart.js) tracking:
Neck tilt angle vs. threshold.
Distance from screen over time.
Lighting intensity over session.

Bi-Directional IoT Control Panel:

Distance Threshold Slider: Adjusts minimum safe distance (e.g. 35–60 cm).
Angle Threshold Slider: Adjusts maximum permissible forward lean (e.g. 10–25°).
Alarm Delay (Debounce): Time in seconds before warning turns into alarm (3–15 sec).
Calibrate Neutral Button: Sends command flag to ESP32 to zero current MPU6050 reading as "Ideal Upright Posture".
Snooze Button: Temporarily silences alarm for 2 minutes.

Event Log & Daily Statistics:

Daily sitting time and alarm count breakdown.
Chronological table showing exact timestamp, triggered sensor condition, and duration.