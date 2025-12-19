# Onboard Importa 🛒

![Build Status](https://img.shields.io/badge/build-passing-brightgreen) ![License](https://img.shields.io/badge/license-MIT-blue) ![TypeScript](https://img.shields.io/badge/TypeScript-100%25-blue) ![PWA](https://img.shields.io/badge/PWA-Ready-orange)

**The offline-first, AI-powered operating system for modern retail staff.**

> **Onboard Importa** is a Progressive Web App designed to replace clunky handheld inventory devices. It allows non-technical staff to capture products, scan barcodes, and manage stock using their personal devices or company tablets, even inside walk-in freezers without Wi-Fi.

---

## ⚡ Key Features

*   **📶 Offline-First Architecture:** Built on IndexedDB. Capture data anywhere; sync automatically when connection restores.
*   **🤖 AI Autopilot:** Upload a photo, and the system auto-generates the product name, description, nutritional info, and SEO tags using GPT-4 Vision.
*   **📷 Integrated Scanning:** Built-in Barcode (EAN/UPC) and QR code scanner using the device camera.
*   **🛡️ Role-Based Workflows:**
    *   **Agents:** Capture and submit drafts.
    *   **Managers:** Tinder-style "Swipe to Approve" interface for quality control.
    *   **Admins:** Full system configuration and analytics.
*   **📱 Native Feel:** Installable to Home Screen (PWA) with haptic feedback and smooth transitions.

---

## 🛠️ Technology Stack

*   **Frontend:** React, Vite, TypeScript, Tailwind CSS
*   **PWA Engine:** `vite-plugin-pwa`, Workbox
*   **Local DB:** Dexie.js (IndexedDB wrapper)
*   **Server:** Node.js, Express
*   **Database:** PostgreSQL
*   **AI:** OpenAI API

---

## 🚀 Quick Start

### Prerequisites
*   Node.js v18+
*   PostgreSQL 15+

### Installation

1.  **Clone the Repo**
    ```bash
    git clone https://github.com/yourusername/onboard-supermarket-os.git
    cd onboard-supermarket-os
    ```

2.  **Setup Server**
    ```bash
    cd server
    npm install
    cp .env.example .env
    # Update .env with your Postgres & OpenAI credentials
    npm run dev
    ```

3.  **Setup Client (PWA)**
    ```bash
    cd client
    npm install
    npm run dev
    ```

4.  **Access App**
    *   Open `http://localhost:5173` in your browser.
    *   To test PWA features, use Chrome DevTools > Application > Service Workers.

---

## 📂 Project Structure

```text
├── client/                 # React PWA
│   ├── src/db/             # Local Offline Database Config
│   ├── src/pages/          # Role-specific Dashboards
│   └── vite.config.ts      # PWA Manifest Configuration
├── server/                 # Node.js API
│   ├── src/controllers/    # AI & Product Logic
│   └── src/middleware/     # RBAC Security Layers
└── database/               # SQL Init Scripts
```

---

## 🔒 Security Notice

This application is designed for **internal enterprise use**.
*   It does not include public sign-up forms.
*   User accounts must be seeded by an Admin or via the invite-link system.
*   Role-Based Access Control (RBAC) is enforced on both client and server.

## 🤝 Contributing

1.  Fork the Project
2.  Create your Feature Branch (`git checkout -b feature/AmazingFeature`)
3.  Commit your Changes (`git commit -m 'Add some AmazingFeature'`)
4.  Push to the Branch (`git push origin feature/AmazingFeature`)
5.  Open a Pull Request

## 📄 License

Distributed under the MIT License. See `LICENSE` for more information.

(See more in documentation)
