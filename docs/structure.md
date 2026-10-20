frontend/
├── public/
│   ├── favicon.ico
│   ├── icons/                  # SVG ไอคอนเฉพาะทาง (spine, chair, eye, etc.)
│   └── sounds/                 # เสียงแจ้งเตือน (alert.mp3, beep.mp3)
├── src/
│   ├── app/                    # Next.js App Router
│   │   ├── layout.tsx          # Root Layout (Theme, Font, Context Provider)
│   │   ├── page.tsx            # Main Dashboard View (Desktop/Mobile)
│   │   ├── projector/          # หน้า Projector Presentation Mode เฉพาะ
│   │   │   └── page.tsx
│   │   └── globals.css         # Tailwind & Clean Medical Design Variables
│   │
│   ├── components/             # UI Components (แยกเป็นหมวดหมู่ชัดเจน)
│   │   ├── layout/             # โครงสร้างหลักของหน้า
│   │   │   ├── Header.tsx      # แถบด้านบน: Connection status, Lang switcher, Projector toggle
│   │   │   └── StatusBanner.tsx# แถบสถานะใหญ่ SAFE / WARNING / ALARM
│   │   ├── dashboard/          # การ์ดแสดงค่าเรียลไทม์
│   │   │   ├── VitalCardsGrid.tsx
│   │   │   ├── DistanceCard.tsx# การ์ดระยะ Sharp IR (cm)
│   │   │   ├── TiltCard.tsx    # การ์ดมุมก้ม/เงย/เอียง (Pitch/Roll)
│   │   │   ├── LightCard.tsx   # การ์ดความสว่าง (Lux)
│   │   │   └── ScoreCard.tsx   # วงกลมคะแนนท่านั่ง (0-100)
│   │   ├── avatar/             # อวตารคนนั่งจำลองมุมจริง
│   │   │   ├── PostureAvatar.tsx       # SVG/Canvas ที่หมุนตาม pitch_deg & roll_deg
│   │   │   └── PostureSkeleton.tsx    # ลายเส้นกระดูกสันหลังและศีรษะ
│   │   ├── charts/             # กราฟย้อนหลัง (Recharts / Chart.js)
│   │   │   ├── RealtimeChart.tsx      # กราฟเส้นแบบ Streaming
│   │   │   └── HistoryAnalytics.tsx   # กราฟแท่ง/สถิติรายวัน
│   │   ├── controls/           # ส่วนสั่งการกลับไปยัง ESP32
│   │   │   ├── ControlPanel.tsx       # คอนเทนเนอร์รวม
│   │   │   ├── ThresholdSliders.tsx   # สไลเดอร์ปรับระยะ, มุม, หน่วงเวลาเตือน
│   │   │   └── ActionButtons.tsx      # ปุ่ม Calibrate, Snooze Alarm
│   │   ├── logs/               # ประวัติการเตือน
│   │   │   ├── EventLogTable.tsx      # ตาราง Log เหตุการณ์เตือน
│   │   │   └── DailyStatsSummary.tsx  # สรุปยอดเตือน/เวลานั่งวันนี้
│   │   └── ui/                 # Reusable UI primitives (ปุ่ม, การ์ด, โมดอล, แบดจ์)
│   │       ├── Badge.tsx
│   │       ├── Button.tsx
│   │       ├── Card.tsx
│   │       ├── Modal.tsx
│   │       └── Slider.tsx
│   │
│   ├── contexts/               # Global State Providers
│   │   ├── FirebaseContext.tsx # Context จัดการ Realtime Database & Live Subscriptions
│   │   └── LanguageContext.tsx # Context จัดการสลับภาษา (TH / EN)
│   │
│   ├── hooks/                  # Custom React Hooks
│   │   ├── useFirebaseDevice.ts# Hook อ่านค่าสด (/live) และส่งคำสั่ง (/controls)
│   │   ├── usePostureScore.ts  # Hook คำนวณคะแนนหักตามมุมและระยะทาง
│   │   └── useTranslation.ts   # Hook ดึงข้อความตามภาษาที่เลือก
│   │
│   ├── lib/                    # Configurations & Third-party Libraries
│   │   ├── firebase.ts         # Init Firebase App & Realtime Database instance
│   │   └── utils.ts            # Helper functions (cn, formatters, clamp, math)
│   │
│   ├── locales/                # ไฟล์พจนานุกรมแปลภาษา
│   │   ├── en.json             # ภาษาอังกฤษ
│   │   └── th.json             # ภาษาไทย
│   │
│   └── types/                  # TypeScript Data Models
│       ├── device.ts           # Type สำหรับ Sensor data, Controls, State (SAFE/WARNING/ALARM)
│       └── logs.ts             # Type สำหรับ Event Logs และ Daily Stats
│
├── .env.example                # Template ตัวแปร Firebase Config (NEXT_PUBLIC_FIREBASE_...)
├── .env.local                  # Environment Variables จริง (ห้าม push ขึ้น Git)
├── next.config.ts
├── tailwind.config.ts          # กำหนดสี Clean Medical Palette (Mint, Amber, Slate, etc.)
├── tsconfig.json
└── package.json