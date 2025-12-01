# Importa
Progressive Web App for supermarket product onboarding
Onboard Supermarket OS 🛒
![alt text](https://img.shields.io/badge/build-passing-brightgreen)
![alt text](https://img.shields.io/badge/license-MIT-blue)
![alt text](https://img.shields.io/badge/TypeScript-100%25-blue)
![alt text](https://img.shields.io/badge/PWA-Ready-orange)
The offline-first, AI-powered operating system for modern retail staff.
Onboard Supermarket OS is a Progressive Web App designed to replace clunky handheld inventory devices. It allows non-technical staff to capture products, scan barcodes, and manage stock using their personal devices or company tablets, even inside walk-in freezers without Wi-Fi.
⚡ Key Features
📶 Offline-First Architecture: Built on IndexedDB. Capture data anywhere; sync automatically when connection restores.
🤖 AI Autopilot: Upload a photo, and the system auto-generates the product name, description, nutritional info, and SEO tags using GPT-4 Vision.
📷 Integrated Scanning: Built-in Barcode (EAN/UPC) and QR code scanner using the device camera.
🛡️ Role-Based Workflows:
Agents: Capture and submit drafts.
Managers: Tinder-style "Swipe to Approve" interface for quality control.
Admins: Full system configuration and analytics.
📱 Native Feel: Installable to Home Screen (PWA) with haptic feedback and smooth transitions.
🛠️ Technology Stack
Frontend: React, Vite, TypeScript, Tailwind CSS
PWA Engine: vite-plugin-pwa, Workbox
Local DB: Dexie.js (IndexedDB wrapper)
Server: Node.js, Express
Database: PostgreSQL
AI: OpenAI API
