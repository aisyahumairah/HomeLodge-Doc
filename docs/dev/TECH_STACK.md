# Technology Stack
## HomeLodge – Booking Homestay System

| Field | Detail |
|---|---|
| **Document Version** | 1.0 |
| **Status** | Draft |
| **Last Updated** | 2026-03-03 |

---

## Table of Contents

1. [Overview](#1-overview)
2. [Backend](#2-backend)
3. [Frontend](#3-frontend)
4. [Database](#4-database)
5. [Real-Time Communication](#5-real-time-communication)
6. [Authentication](#6-authentication)
7. [Payment Gateway](#7-payment-gateway)
8. [Notifications](#8-notifications)
9. [QR Code & Door Access](#9-qr-code--door-access)
10. [Calendar Integration](#10-calendar-integration)
11. [Infrastructure & Deployment](#11-infrastructure--deployment)
12. [Development Tools](#12-development-tools)
13. [Architectural Decisions (ADR)](#13-architectural-decisions-adr)

---

## 1. Overview

HomeLodge is built as a **monolithic web application** using the **Laravel** PHP framework. The frontend is rendered primarily server-side with Blade templates, enhanced with JavaScript for interactivity. This architecture was chosen for development simplicity and a small team size.

```
┌──────────────────────────────────────────────────────┐
│                   Web Browser (Client)                │
│          Blade Views + Alpine.js + Vite               │
└────────────────────────┬─────────────────────────────┘
                         │ HTTPS
┌────────────────────────▼─────────────────────────────┐
│               Laravel Application (PHP)               │
│   Routes → Controllers → Services → Models           │
│                   Queue Workers                       │
└────┬───────────────────┬──────────────────────────────┘
     │                   │
     ▼                   ▼
┌─────────┐       ┌──────────────┐
│  MySQL  │       │ Redis (Cache │
│  (DB)   │       │  & Queues)   │
└─────────┘       └──────────────┘
```

---

## 2. Backend

| Component | Technology | Version |
|---|---|---|
| Framework | [Laravel](https://laravel.com) | 11.x |
| Language | PHP | 8.2+ |
| Package Manager | Composer | Latest |

### Key Laravel Packages

| Package | Purpose |
|---|---|
| `laravel/sanctum` or `laravel/breeze` | Authentication scaffolding |
| `spatie/laravel-permission` | Role and permission management |
| `spatie/laravel-activitylog` | Audit logging |
| `barryvdh/laravel-dompdf` | PDF generation for bills and receipts |
| `simplesoftwareio/simple-qrcode` | QR code generation |
| `laravel/socialite` | Google OAuth 2.0 / SSO |
| `laravel/reverb` | WebSocket server for real-time chat |

---

## 3. Frontend

| Component | Technology |
|---|---|
| Templating | Laravel Blade |
| Interactivity | Alpine.js |
| Build Tool | Vite |
| CSS Framework | Bootstrap 5 / Custom CSS |
| Icons | Font Awesome / Bootstrap Icons |

> **Note:** Heavy client-side interactions (calendar, chat, real-time updates) use Alpine.js and Laravel Echo (backed by Reverb or Pusher).

---

## 4. Database

| Component | Technology |
|---|---|
| Primary Database | MySQL 8.x / MariaDB 10.x |
| ORM | Eloquent (Laravel built-in) |
| Migrations | Laravel Migrations |
| Seeders | Laravel Seeders (for roles, permissions, default settings) |

See [DB_SCHEMA.md](./DB_SCHEMA.md) for the full entity-relationship documentation and table definitions.

---

## 5. Real-Time Communication

| Component | Technology | Rationale |
|---|---|---|
| WebSocket Server | [Laravel Reverb](https://reverb.laravel.com) | First-party Laravel solution, self-hosted, no extra cost |
| Client Library | Laravel Echo + `pusher-js` | Works with Reverb out of the box |

**Fallback:** [Pusher](https://pusher.com) (hosted service) if self-hosted Reverb proves operationally complex.

---

## 6. Authentication

| Feature | Technology |
|---|---|
| Session-based Auth | Laravel built-in session/guard |
| Google SSO | `laravel/socialite` + Google OAuth 2.0 |
| Password Hashing | bcrypt (Laravel default) |
| Password Reset Tokens | Laravel built-in password reset |

---

## 7. Payment Gateway

| Component | Detail |
|---|---|
| Gateway | **To Be Determined (TBD)** |
| Integration Type | Server-side API + Webhook callback |
| Webhook Handling | Laravel route + signature verification; idempotent processing |

> **Recommendation:** Consider [Billplz](https://www.billplz.com) (Malaysia-based), [Stripe](https://stripe.com), or [Toyyibpay](https://toyyibpay.com) depending on regional availability and fee structure.

---

## 8. Notifications

| Channel | Technology |
|---|---|
| Email | Laravel Mail + SMTP (configurable via system settings) |
| In-App | Laravel database notifications |
| Push (future) | N/A — out of scope for v1.0 |

---

## 9. QR Code & Door Access

| Component | Technology |
|---|---|
| QR Code Generation | `simplesoftwareio/simple-qrcode` (Laravel package) |
| QR Code Display | Rendered in Blade view as SVG/PNG |
| Physical Lock | Smart lock device (hardware; vendor TBD) |

> The QR code contains a unique, time-limited token. The smart lock manufacturer's SDK/API must be integrated to validate QR codes at the door.

---

## 10. Calendar Integration

| Component | Technology |
|---|---|
| Service | [Google Calendar API](https://developers.google.com/calendar) |
| Auth | Google OAuth 2.0 (via `laravel/socialite`) |
| Scope | `https://www.googleapis.com/auth/calendar.events` |

---

## 11. Infrastructure & Deployment

| Component | Detail |
|---|---|
| Web Server | Nginx or Apache |
| PHP Process Manager | PHP-FPM |
| Queue Worker | Laravel Queues with Redis driver |
| Task Scheduler | Laravel Scheduler (via OS cron: `php artisan schedule:run`) |
| Caching | Redis (session, cache, queues) |
| File Storage | Local disk (or S3-compatible for production) |
| Environment | Linux (Ubuntu 22.04 LTS recommended) |
| SSL/TLS | Let's Encrypt (Certbot) |

### Scheduled Jobs

| Job | Schedule | Purpose |
|---|---|---|
| Auto-cancel unpaid bookings | Every hour | Cancel bookings where payment window has expired |
| Upcoming booking reminders | Daily | Notify guests and admin of upcoming stays |
| QR code expiry / regeneration | Triggered at checkout time | Invalidate old QR codes |
| Payment reminders | Daily | Notify guests with pending payment |

---

## 12. Development Tools

| Tool | Purpose |
|---|---|
| Git | Version control |
| GitHub / GitLab | Repository hosting and code review |
| PHPUnit | Unit and feature testing |
| Laravel Pint | PHP code style fixer |
| Pest PHP | (Optional) expressive testing framework |
| Postman | API and webhook testing |
| TablePlus / DBeaver | Database GUI client |
| VS Code / PhpStorm | IDE |

---

## 13. Architectural Decisions (ADR)

### ADR-001 – Monolithic over Microservices

**Decision:** Build HomeLodge as a single monolithic Laravel application.

**Rationale:** The team is small, the feature set is well-defined, and the system does not require independent scaling of individual services. A monolith reduces operational complexity and allows for faster initial development.

---

### ADR-002 – Server-Side Rendering with Blade over SPA

**Decision:** Use Laravel Blade templates for server-side rendering instead of a separate Vue/React SPA.

**Rationale:** Blade is simpler, well-integrated with Laravel, and sufficient for the UI complexity of this application. Alpine.js handles lightweight client-side interactivity without the overhead of a full SPA framework.

---

### ADR-003 – Laravel Reverb for WebSockets

**Decision:** Use Laravel Reverb as the WebSocket server for real-time chat.

**Rationale:** Reverb is a first-party Laravel package with native integration. It avoids third-party subscription costs (unlike Pusher) and is simpler to set up than raw `socket.io`.

---

### ADR-004 – Spatie Permission for RBAC

**Decision:** Use the `spatie/laravel-permission` package for role-based access control.

**Rationale:** The system requires a flexible RBAC system where admins can create custom roles and assign permissions. Spatie's package is the industry standard for this in the Laravel ecosystem, saving significant development time.
