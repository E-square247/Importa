### Part 1: Technical Documentation (`DOCS.md`)

# Onboard Supermarket PWA – Technical Reference Manual

## 1. System Overview
**Onboard Supermarket OS** is a specialized Progressive Web App (PWA) designed for high-efficiency retail environments. It bridges the gap between physical inventory handling and digital ecommerce administration. The system is built on an **Offline-First** architecture, allowing staff to capture product data, scan barcodes, and manage stock in low-connectivity areas (e.g., freezers, warehouses) with automatic synchronization when connectivity is restored.

### 1.1 Core Objectives
*   **Zero-Latency Data Entry:** Local database caching ensures instant UI feedback.
*   **Role-Based Security:** Strict separation of duties between Agents, Managers, and Admins.
*   **AI-Assisted Workflow:** Automated generation of product descriptions and categorization via OpenAI.

---

## 2. Architecture

### 2.1 Tech Stack
*   **Frontend:** React 18, TypeScript, Vite.
*   **State Management:** TanStack Query (Server State), React Context (Auth).
*   **Offline Layer:** Dexie.js (IndexedDB wrapper), Workbox (Service Workers).
*   **UI Framework:** Tailwind CSS, Framer Motion.
*   **Backend:** Node.js, Express, TypeScript.
*   **Database:** PostgreSQL 15 (with `uuid-ossp` extension).
*   **AI Integration:** OpenAI GPT-4 Vision API.

### 2.2 Data Flow Diagram
1.  **Capture:** User captures data (Image/Barcode) → Saved immediately to **IndexedDB**.
2.  **Queue:** Background worker monitors network status.
3.  **Sync:** On connection, data is pushed to REST API (`POST /products`).
4.  **Enrichment:** Server triggers OpenAI for description generation.
5.  **Persist:** Data saved to PostgreSQL with `approval_status: pending`.

---

## 3. Role-Based Access Control (RBAC)

The system implements a rigid permission matrix enforced by both the API (`requireRole` middleware) and Client (`<RoleGuard>` component).

| Feature | Sales Agent | Store Manager | System Admin |
| :--- | :---: | :---: | :---: |
| **Product Entry** | Create/Draft | Create/Edit | Full Access |
| **Publishing** | ❌ (Submit Only) | ✅ (Approve/Reject) | ✅ |
| **Price Visibility** | ❌ (Hidden) | ✅ (Retail Only) | ✅ (Cost + Retail) |
| **User Mgmt** | ❌ | ❌ | ✅ |

---

## 4. Local Storage & Sync Strategy

### 4.1 Schema (Dexie.js)
The client-side database (`SupermarketDB`) stores drafts to ensure work is never lost.
```typescript
interface LocalProduct {
  tempId: string;   // UUID generated on client
  sku: string;
  images: Blob[];   // Raw image data stored in browser
  status: 'draft' | 'pending_sync';
  timestamp: number;
}
```

### 4.2 Synchronization Logic
*   **Trigger:** `window.addEventListener('online')` and `setInterval` (30s).
*   **Conflict Resolution:** Last Write Wins (Server Timestamp priority).
*   **Error Handling:** Failed uploads remain in IndexedDB with an error flag for manual retry.

---

## 5. API Reference (Key Endpoints)

### Authentication
*   `POST /api/auth/login` - Returns JWT + User Role.
*   `POST /api/auth/magic-link` - Sends secure entry link (Device Fingerprinting).

### Products
*   `POST /api/products` - Accepts Multipart Form Data (Images + JSON).
    *   *Agents:* Auto-sets status to `pending`.
    *   *Managers:* Auto-sets status to `approved`.
*   `GET /api/products/pending` - Returns items requiring Manager review.
*   `PATCH /api/products/:id/review` - Payload: `{ status: 'approved' | 'rejected', reason: '...' }`.

### AI Services
*   `POST /api/ai/generate` - Payload: `{ imageBase64 }`. Returns `{ description, tags, nutrition }`.

---

## 6. Deployment & Security

### 6.1 The "Hidden Portal" Strategy
To maintain security, the PWA is **not** distributed via public App Stores.
1.  **Host:** Subdomain (e.g., `staff.store.com`) behind a VPN or IP Whitelist.
2.  **Access:** Physical QR Codes placed in secure staff zones.
3.  **Manifest:** `manifest.json` configured for `display: standalone` to mimic native app behavior.

### 6.2 Environment Variables
```env
DATABASE_URL=postgres://user:pass@host:5432/db
JWT_SECRET=super_secure_secret
OPENAI_API_KEY=sk-...
ALLOWED_ORIGINS=https://staff.yourdomain.com
```

---
---

### Part 2: GitHub Repository (`README.md`)

Save this file as `README.md` in the root of your repository.

# Importa 🛒

![Build Status](https://img.shields.io/badge/build-passing-brightgreen) ![License](https://img.shields.io/badge/license-MIT-blue) ![TypeScript](https://img.shields.io/badge/TypeScript-100%25-blue) ![PWA](https://img.shields.io/badge/PWA-Ready-orange)

**The offline-first, AI-powered operating system for modern retail staff.**

> **Importa** is a Progressive Web App designed to replace clunky handheld inventory devices. It allows non-technical staff to capture products, scan barcodes, and manage stock using their personal devices or company tablets, even inside walk-in freezers without Wi-Fi.

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
