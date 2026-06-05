# Chapter 6: Interface Design (SDD) — HomeLodge Booking Homestay System

This chapter describes the interfaces of the HomeLodge system.

---

## 6.1 Overview of Interface

The HomeLodge system's interfaces can be classified into the following categories:

### External Interfaces

External interfaces connect the HomeLodge application to systems and services outside its own codebase.

| Interface | Type | Description |
| :--- | :--- | :--- |
| Payment Gateway API | Software / External | Server-side REST API integration with the payment provider (Billplz, Stripe, or Toyyibpay). The system sends payment requests and receives webhook callbacks. |
| Google OAuth 2.0 | Software / External | Delegates authentication to Google's OAuth endpoints for Single Sign-On. The system exchanges authorisation codes for access tokens and retrieves user profile data. |
| Google Calendar API v3 | Software / External | REST API for creating, updating, and deleting calendar events in the user's Google Calendar to mirror bookings. Uses OAuth 2.0 tokens for authentication. |
| SMTP Server | Software / External | Outbound email delivery via the configured SMTP provider. Used for registration confirmations, password resets, payment notifications, and booking reminders. |
| Smart Lock Vendor API/SDK | Hardware / External | Integration with the QR code smart lock's cloud API for token validation at the door. The specific vendor and SDK have not been finalised. |

### Internal Interfaces

Internal interfaces connect the components within the HomeLodge application stack.

| Interface | Type | Description |
| :--- | :--- | :--- |
| Web Server → PHP-FPM | Software / Internal | Nginx (or Apache) proxies HTTP/HTTPS requests to PHP-FPM over the FastCGI protocol. |
| Laravel → MySQL/MariaDB | Software / Internal | The application connects to the database on port 3306 via Eloquent ORM. All queries, inserts, updates, and deletes go through Eloquent models and migrations. |
| Laravel → Redis | Software / Internal | The application connects to Redis on port 6379 for three purposes: caching (settings, query results), session storage, and job queue management. |
| Laravel → Laravel Reverb | Software / Internal | The application dispatches broadcast events to the Reverb WebSocket server, which then pushes them to connected browser clients via the Pusher protocol. |
| Laravel Queue Worker | Software / Internal | A background process that consumes jobs from the Redis queue (email sending, webhook processing, scheduled tasks). |
| Laravel Scheduler | Software / Internal | Runs scheduled tasks (auto-cancellation of unpaid bookings, QR code regeneration, payment reminders, extension deadline checks) at defined intervals via cron. |

### Physical Interfaces

| Interface | Description |
| :--- | :--- |
| QR Code Smart Lock | A physical device mounted on each homestay unit's door. The guest scans a QR code (displayed on their booking detail page) using their phone camera or a companion app. The lock validates the token against the backend and unlocks the door if valid. The QR code token has a time-limited validity window (check-in to check-out). |
| Client Devices | No specialised hardware required. Guests and admins access the system through any device with a modern web browser (Chrome, Firefox, Safari, Edge — latest two major versions) and a screen width of 320 px or more. |

### Logical Interfaces

| Interface | Description |
| :--- | :--- |
| Guest Interface | A top-navbar layout accessible only to users with the "User" role. Pages include: Landing Page, Unit Detail, Booking Form, My Bookings, Booking Detail, Payment, Chat, and Notifications. Middleware enforces role-based access — guests cannot reach admin pages even by URL manipulation (returns 403 Forbidden). |
| Admin Interface | A fixed left-sidebar layout accessible only to users with the "Admin" role. Pages include: Dashboard, Booking Calendar, Booking Management, Payment/Billing Lists, User Management, Roles & Permissions, System Settings, Audit Logs, QR Code Management, and Chat. Admins accessing guest-only pages are redirected to the admin dashboard. |
| WebSocket Channel | Real-time communication channel for chat messages and live notifications. Uses private channels authenticated through Laravel's broadcasting auth endpoint. Each user subscribes only to their own channels. |
| Webhook Endpoint | A dedicated HTTPS POST endpoint that receives payment status updates from the payment gateway. The endpoint verifies the gateway's signature, processes the payment idempotently, and returns HTTP 200. |

### Hardware (H/W) Interface

| Interface | Description |
| :--- | :--- |
| QR Code Smart Lock | Cloud-connected smart locks (e.g., QRLock, TTlock, or equivalent) that accept scanned QR codes for door access. The lock communicates with the HomeLodge backend to validate tokens. See Section 2.1.2 for full details. |

### Software (S/W) Interface

All software interfaces are detailed in Section 2.1.3 (Software Interfaces). A summary:

| Category | Components |
| :--- | :--- |
| Backend | Laravel 11.x, PHP 8.2+, Composer |
| Database | MySQL 8.x / MariaDB 10.x, Eloquent ORM |
| Frontend | Blade, Alpine.js, Vite, Bootstrap 5, Chart.js, Font Awesome / Bootstrap Icons |
| Auth & RBAC | Laravel Breeze / Sanctum, Socialite, Spatie Laravel Permission |
| Operations | Spatie Activity Log, SimpleSoftwareIO QR Code, Barryvdh DomPDF, Maatwebsite Excel |
| Real-Time | Laravel Reverb, Laravel Echo + pusher-js |
| Infrastructure | Redis (cache, session, queue) |

---

*— End of Chapter 6: Interface Design —*
