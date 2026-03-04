# HomeLodge — Homestay Booking System
## 1. Executive Summary

**HomeLodge** is a web-based Homestay Booking Management System that digitises and automates the end-to-end booking process for a homestay property. The system serves two primary audiences: **guests (users)** who wish to book the homestay, and **administrators** who manage operations.

The system consolidates booking management, online payment processing, real-time communication, email/system notifications, and QR-code-based smart door lock access into a single unified platform.

---

## 2. Product Goals & Objectives

| # | Goal | Description |
|---|---|---|
| G1 | Streamline Bookings | Eliminate manual booking processes; allow guests to self-serve bookings online |
| G2 | Secure Payments | Integrate a reliable payment gateway for real-time booking payment processing |
| G3 | Automate Access | Use QR code-based smart locks to provide time-bounded, automated physical access |
| G4 | Improve Communication | Enable real-time admin–guest chat and automated notification systems |
| G5 | Administrative Control | Provide administrators with full control over users, roles, permissions, and system settings |
| G6 | Enforce Security | Implement account lockout, session management, password policies, and full audit logging |

---

## 3. Scope

### In Scope

- Guest user registration, authentication, and profile management
- Admin registration, authentication, and profile management
- Online booking creation, viewing, modification, and cancellation
- Payment processing via payment gateway (including deposit and refunds)
- QR code generation and regeneration for smart door lock access
- Real-time chat between guests and admins (WebSocket)
- Email and in-system notifications (reminders, booking confirmations, payment receipts)
- Admin management of users, roles, permissions, system settings, and audit logs
- Booking calendar view for admins
- Automated booking expiry for unpaid bookings (1-day payment window)
- Automated refund rules based on cancellation timing
- Password management: change, reset, forced change on admin reset

### Out of Scope (v1.0)

- Multi-property / multi-room management
- Mobile native apps (iOS / Android)
- Third-party calendar integration (Google Calendar, iCal)
- Dynamic pricing / seasonal pricing engine
- Customer loyalty / points system

---

## 4. Stakeholders

| Role | Responsibility |
|---|---|
| **Product Owner** | Defines requirements, prioritises features, accepts deliverables |
| **System Administrator** | Manages users, roles, permissions, and system configurations |
| **Developer Team** | Designs, builds, and maintains the system |
| **QA Team** | Verifies features against PRD acceptance criteria |
| **Guests (End Users)** | Books and uses the homestay; primary end-user of the system |

---

## 5. User Roles

| Role | Description |
|---|---|
| **User (Guest)** | Standard registered user; can make bookings and payments |
| **Admin** | Full system access; manages all operations and configurations |

> Role permissions are managed dynamically via the Role & Permission module. New roles can be created and assigned as needed.

---

## 6. System Modules Overview

### Guest Modules

| Module | Summary |
|---|---|
| Authentication | Login, logout, register, forgot/reset password, profile management, password change |
| Booking | Search availability, create/view/cancel bookings |
| Payment | Make payment via gateway, view bills and receipts, payment history |
| Notifications | In-system and email notifications (reminders, booking, payment) |
| Chat | Real-time messaging with admin |

### Admin Modules

| Module | Summary |
|---|---|
| Authentication | Login, logout, profile management, password change |
| Booking Management | Calendar view, create/view/edit/delete/cancel bookings, booking-on-behalf |
| Payment Management | View billing and payment lists, regenerate bills |
| User Management | Create, edit, delete, activate/deactivate users; password reset |
| Role Management | Create, edit, delete roles; assign permissions |
| Permission Management | Create, edit, delete permissions |
| System Settings | SMTP, security (lockout, sessions), notifications, refund config, billing config |
| Audit Logs | View full audit trail of system activity |
| Notifications | In-system and email notification for admin-relevant events |
| Chat | Real-time messaging with guests |
| QR Code Door Lock | Manage and regenerate QR codes for door lock access |

---

## 7. Assumptions & Constraints

| # | Assumption / Constraint |
|---|---|
| A1 | The homestay is a single property (single unit). Multi-room support is out of scope for v1. |
| A2 | Payment gateway integration will use a third-party provider (e.g., Stripe, Billplz, Toyyibpay). |
| A3 | The QR door lock hardware is compatible with the chosen software protocol (e.g., TTLock, QRLock). |
| A4 | The system will be accessible via modern web browsers (Chrome, Firefox, Edge, Safari). |
| A5 | Internet connectivity is required for all system functions (no offline mode). |
| A6 | All dates and times are stored in UTC and displayed in the local timezone. |
| A7 | Check-in time is fixed at 3:00 PM. Check-out time is fixed at 12:00 PM (noon). |
| A8 | Payment refund processing time is 3–5 business days. |

---
