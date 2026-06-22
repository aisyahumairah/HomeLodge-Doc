# HomeLodge — PSM I Presentation Slides
**Department of Software Engineering | Final Year Project**

> 11 slides · 10–12 minutes · No animations needed · Text is big, bold, and readable

---

## SLIDE 1 — Title

**HomeLodge: A Web-Based Homestay Booking Management System for Multi-Unit Operators**

- Student: [Your Name]
- Supervisor: [Supervisor Name]
- Bachelor of Software Engineering
- Session: 2025 / 2026

---

## SLIDE 2 — Project Introduction

Managing multiple homestay units through messaging apps and phone calls is unsustainable.

**What happens when there's no system:**

| Problem | What goes wrong |
|---|---|
| No shared availability record | Two guests book the same dates |
| Manual payment collection | No receipts, no refund process |
| Physical key handover | Operator must be present at every check-in |
| Scattered chat threads | No link between conversation and booking |
| Manual reminders | Missed notifications, lapsed bookings |

One property is manageable. Three or more running at the same time — that's where things break.

---

## SLIDE 3 — Project Aim

**Aim:**
To develop a web-based homestay booking management system that handles reservations, payment, property access, and guest communication — all in one place — for operators managing multiple units.

**Not another marketplace.**
A private management tool that the operator owns, configures, and controls.

Three things that set HomeLodge apart from Airbnb or Booking.com:
- No commission — zero per-booking marketplace fees
- Full control — operator owns the system and all data
- Private listing — no mandatory public marketplace exposure

---

## SLIDE 4 — Project Objectives

**01 — Study & Analyse**
Gather and document all functional and non-functional requirements for HomeLodge.

**02 — Design**
Produce the system architecture, database schema, and UI covering all modules and both user roles.

**03 — Develop**
Build the HomeLodge web application with 11 functional modules as defined by the requirements.

**04 — Test**
Verify correctness, security, and usability against the stated requirements.

**11 Modules:**
Authentication · Homestay Management · Booking · Payment · Notification · Chat · User Management · Role & Permission · System Settings · Audit Logs · QR Code Door Access

---

## SLIDE 5 — Project Scope

**What the system covers:**

1. **Platform type** — Web-based application accessible via desktop or mobile browser
2. **User roles** — Two roles supported:
   - **Guest** — browses units, submits bookings, makes payments, receives QR code, chats with admin, leaves feedback
   - **Admin** — manages homestay units, bookings, payments, users, roles, system settings, and reports
3. **Language** — English only
4. **Multi-unit support** — Multiple homestay units managed under a single administrator account
5. **11 Functional Modules:**

| # | Module |
|---|---|
| 1 | Authentication |
| 2 | Homestay Management |
| 3 | Booking |
| 4 | Payment |
| 5 | Notification |
| 6 | Chat |
| 7 | User Management |
| 8 | Role & Permission |
| 9 | System Settings |
| 10 | Audit Logs |
| 11 | QR Code Door Access |

6. **QR Code** — Designed for compatible smart lock devices; physical lock installation is outside scope
7. **Payment** — Online gateway (Billplz / Toyyibpay / Stripe); specific provider selected at deployment
8. **No multi-language support** in this version

---

## SLIDE 6 — Background Study: Why Existing Systems Fall Short

| Feature | Airbnb | Booking.com | Agoda | HomeLodge |
|---|---|---|---|---|
| Online availability calendar | ✓ | ✓ | ✓ | ✓ |
| Online payment processing | ✓ | ✓ | ✓ | ✓ |
| In-app guest-to-host messaging | ✓ | ✓ | ✓ | ✓ |
| QR code door access | ✗ | ✗ | ✗ | ✓ |
| Booking extension management | ✗ | ✗ | ✗ | ✓ |
| Role-based access control | ✗ | ✗ | ✗ | ✓ |
| Private, non-marketplace operation | ✗ | ✗ | ✗ | ✓ |
| Immutable audit log | ✗ | ✗ | ✗ | ✓ |
| No per-booking commission | ✗ | ✗ | ✗ | ✓ |

All three platforms solve the basics. None give operators a private, controllable system they actually own.

---

## SLIDE 7 — Development Methodology

A hybrid approach: Waterfall for planning, Agile for building.

```
01  Requirements Analysis  →  Waterfall
         ↓
02  System Design          →  Waterfall
         ↓
03  Implementation         →  Agile (Scrum + Kanban Board)
         ↓
04  Testing                →  Agile (Scrum + Kanban Board)
         ↓
05  Deployment             →  Waterfall (sequential checklist)
```

**Why hybrid?**
- Waterfall → formal PSM documentation is required. Requirements and design must be fixed and written up before any code.
- Agile → 12 modules need incremental, trackable delivery. A single unbroken implementation phase has no way to spot stalled work.

---

## SLIDE 8 — System Requirements & Technology Stack

**Hardware (Development)**

| Component | Specification |
|---|---|
| Laptop | Intel Core i5, 8 GB RAM |
| Storage | 256 GB SSD minimum |
| Internet | Broadband, min. 10 Mbps |
| Server | 2 vCPU, 4 GB RAM, 20 GB SSD |

**Technology Stack**

| Layer | Technology |
|---|---|
| Backend | Laravel 13 · PHP 8.3 |
| Frontend | Blade · Alpine.js · Bootstrap 5 |
| Database | MySQL 8 · Eloquent ORM |
| Real-time | Laravel Reverb (WebSocket) · Laravel Echo |
| Auth / RBAC | laravel/socialite · spatie/laravel-permission |
| QR Code | simplesoftwareio/simple-qrcode (local, no API call) |
| Payment | Billplz / Toyyibpay / Stripe (webhook-driven) |

---

## SLIDE 9 — PSM I Results: Analysis & Design Completed

**Use Case Model**
- 4 Actors: Guest, Admin, Payment Gateway, Google Calendar API
- 12 Modules · 47 Use Cases documented
- All use cases traced back to URS and PRD requirements

**System Architecture**
- Client: Blade + Alpine.js + Bootstrap 5 + Laravel Echo
- Server: Nginx → PHP-FPM → Laravel 11 + Reverb WebSocket
- Data: MySQL 8 (primary) · Redis 7 (cache / queues / sessions)
- External: Payment Gateway · Google OAuth + Calendar · SMTP

**Database Design**
- 16 tables with foreign key constraints and soft deletes
- Key tables: bookings, booking_extensions, payments, qr_codes
- settings table uses key-value model — no schema migration needed for new config
- Idempotent webhook handling via unique gateway_reference index

**User Interface Design**
- Guest: top navbar, linear booking flow (browse → book → pay → QR code)
- Admin: fixed left sidebar, 10 navigation groups
- Responsive across mobile, tablet, and desktop breakpoints
- Inter font, 8px grid, colour-coded booking status badges

---

## SLIDE 10 — Conclusion

**Problem Statement:**

Managing multiple homestay units without a proper system quickly turns into a tangle of messaging apps, phone calls, and manual records. This approach stops working as the business grows. Tracking availability by hand across several properties often leads to double-bookings. Payments get lost because there is no clear way to handle collections and refunds, and relying on physical keys forces the operator to be present for every check-in.

HomeLodge centralizes guest communication, payment tracking, and property access. Instead of cross-referencing WhatsApp threads against paper calendars, operators can see exactly who is arriving and when. The system also makes it easier to assign daily tasks, like cleaning schedules and check-in coordination.

By moving everything online, the system removes the paperwork and cuts down the mistakes that happen when running properties manually.

**PSM I delivered:**
- Complete requirements spec (URS + PRD)
- Full system design: architecture, ER diagram, UI wireframes
- 47 use cases across 12 modules documented and traced
- Hybrid methodology selected and justified
- Technology stack evaluated and selected

**PSM II plan:**
Implement all 11 modules → integrate payment gateway, WebSocket chat, QR door access → run unit, integration, and UAT testing → deploy on Nginx + MySQL + Redis

---

## SLIDE 11 — Thank You / Q&A

**HomeLodge**
A Web-Based Homestay Booking Management System

Thank you for your attention.

**Open for Questions.**

---
*[Student Name] | [Supervisor Name] | Department of Software Engineering | PSM I — 2025/2026*
