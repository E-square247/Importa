### Part 1: Technical Documentation (`DOCS.md`)

# Importa PWA – Technical Reference Manual

## 1. System Overview
**Importa** is a specialized Progressive Web App (PWA) designed for high-efficiency retail environments. It bridges the gap between physical inventory handling and digital ecommerce administration. The system is built on an **Offline-First** architecture, allowing staff to capture product data, scan barcodes, and manage stock in low-connectivity areas (e.g., freezers, warehouses) with automatic synchronization when connectivity is restored.

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
