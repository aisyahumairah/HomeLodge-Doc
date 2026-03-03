# Development Tasks
## HomeLodge – Booking Homestay System

| Field | Detail |
|---|---|
| **Document Version** | 1.0 |
| **Status** | Active |
| **Last Updated** | 2026-03-03 |

---

## How to Use This Document

- Tasks are grouped by module.
- Each task references the relevant requirement ID(s) from [PRD.md](./PRD.md) and/or [URS.md](./URS.md).
- Status options: `🔲 Todo` | `🔄 In Progress` | `✅ Done` | `🚫 Blocked`
- Update this file as tasks are picked up and completed.

---

## Module 1 – Authentication

| ID | Task | Requirement Refs | Status | Notes |
|---|---|---|---|---|
| TASK-AUTH-01 | Implement email/password registration | AUTH-01, URS-U-AUTH-01 | 🔲 Todo | |
| TASK-AUTH-02 | Implement Google SSO (OAuth 2.0) login and registration | AUTH-01, URS-U-AUTH-02 | 🔲 Todo | |
| TASK-AUTH-03 | Implement login with session management | AUTH-02, URS-U-AUTH-03 | 🔲 Todo | |
| TASK-AUTH-04 | Implement logout | AUTH-03, URS-U-AUTH-04 | 🔲 Todo | |
| TASK-AUTH-05 | Implement "Forgot Password" email flow | AUTH-04, URS-U-AUTH-05 | 🔲 Todo | |
| TASK-AUTH-06 | Enforce password complexity rules | AUTH-05, URS-U-AUTH-06 | 🔲 Todo | 8–12 chars, upper, lower, number, symbol |
| TASK-AUTH-07 | Add show/hide password toggle and strength indicator | AUTH-06, URS-U-AUTH-07 | 🔲 Todo | |
| TASK-AUTH-08 | Force password change redirect for admin-reset accounts | AUTH-07, URS-U-AUTH-03 | 🔲 Todo | Block other pages until done |
| TASK-AUTH-09 | Implement failed login attempt tracking and account lockout | AUTH-08, AUTH-09 | 🔲 Todo | Auto-unlock after lockout duration |
| TASK-AUTH-10 | Implement profile view and update page | AUTH-11, URS-U-AUTH-08 | 🔲 Todo | |

---

## Module 2 – Booking

| ID | Task | Requirement Refs | Status | Notes |
|---|---|---|---|---|
| TASK-BK-01 | Build availability calendar UI | BK-U-01, BK-A-01 | 🔲 Todo | Show available/booked/blocked dates |
| TASK-BK-02 | Implement date and time selection | BK-U-02 | 🔲 Todo | |
| TASK-BK-03 | Implement real-time availability check on date selection | BK-U-03 | 🔲 Todo | |
| TASK-BK-04 | Show unavailability message and disable submit if booked | BK-U-04 | 🔲 Todo | |
| TASK-BK-05 | Implement booking submission for users | BK-U-06 | 🔲 Todo | |
| TASK-BK-06 | Build user booking list, history, and detail pages | BK-U-07 | 🔲 Todo | |
| TASK-BK-07 | Implement user booking cancellation with refund logic | BK-U-08 | 🔲 Todo | Apply cancellation policy tiers |
| TASK-BK-08 | Implement 1-day payment hold and auto-cancellation | BK-H-01, BK-H-02, BK-H-03 | 🔲 Todo | Scheduled job required |
| TASK-BK-09 | Admin: create booking on behalf of user | BK-A-02 | 🔲 Todo | |
| TASK-BK-10 | Admin: view, edit, delete bookings | BK-A-04 | 🔲 Todo | |
| TASK-BK-11 | Admin: cancel booking with refund | BK-A-05 | 🔲 Todo | |
| TASK-BK-12 | Admin: filter booking list by status, ID, date range | BK-A-06 | 🔲 Todo | |
| TASK-BK-13 | Admin: date blocking feature | BK-A-07 | 🔲 Todo | Hide reason from guest view |

---

## Module 3 – Payment

| ID | Task | Requirement Refs | Status | Notes |
|---|---|---|---|---|
| TASK-PAY-01 | Integrate payment gateway (TBD) | PAY-U-01 | 🔲 Todo | Gateway TBD |
| TASK-PAY-02 | Calculate total including deposit on bill | PAY-U-02 | 🔲 Todo | |
| TASK-PAY-03 | Auto-generate bill number and payment number | PAY-A-01 | 🔲 Todo | |
| TASK-PAY-04 | Generate and display bill (PDF) | PAY-U-02, PAY-A-04 | 🔲 Todo | |
| TASK-PAY-05 | Generate and display receipt (PDF) after payment | PAY-U-03, PAY-A-05 | 🔲 Todo | |
| TASK-PAY-06 | Implement payment webhook handler | PAY-U-05 | 🔲 Todo | Idempotent processing required |
| TASK-PAY-07 | User: view payment history with filters | PAY-U-04 | 🔲 Todo | Filter by date range and status |
| TASK-PAY-08 | Admin: view billing and payment lists with filters | PAY-A-02, PAY-A-03 | 🔲 Todo | |
| TASK-PAY-09 | Admin: regenerate bill and receipt | PAY-A-04, PAY-A-05 | 🔲 Todo | |

---

## Module 4 – Notifications

| ID | Task | Requirement Refs | Status | Notes |
|---|---|---|---|---|
| TASK-NOTIF-01 | Build in-app notification system | NOTIF-01 | 🔲 Todo | |
| TASK-NOTIF-02 | Integrate transactional email sending | NOTIF-02 | 🔲 Todo | Via SMTP config |
| TASK-NOTIF-03 | Payment reminder notification (pending payment) | NOTIF-03 | 🔲 Todo | Scheduled job |
| TASK-NOTIF-04 | Upcoming booking reminder (check-in/check-out) | NOTIF-04 | 🔲 Todo | Scheduled job |
| TASK-NOTIF-05 | Admin QR code reminder before check-in/check-out | NOTIF-05 | 🔲 Todo | |
| TASK-NOTIF-06 | Google Calendar integration for bookings | NOTIF-06 | 🔲 Todo | Google Calendar API |

---

## Module 5 – Chat

| ID | Task | Requirement Refs | Status | Notes |
|---|---|---|---|---|
| TASK-CHAT-01 | Set up WebSocket server/service | CHAT-03 | 🔲 Todo | Laravel Reverb or Pusher |
| TASK-CHAT-02 | Build chat UI for users | CHAT-01 | 🔲 Todo | |
| TASK-CHAT-03 | Build chat UI for admin (inbox + reply) | CHAT-02 | 🔲 Todo | |

---

## Module 6 – User Management

| ID | Task | Requirement Refs | Status | Notes |
|---|---|---|---|---|
| TASK-USR-01 | Admin: create user with default role | USR-01 | 🔲 Todo | |
| TASK-USR-02 | Admin: edit user information | USR-02 | 🔲 Todo | |
| TASK-USR-03 | Admin: delete user | USR-03 | 🔲 Todo | Safeguard: warn if user has active bookings |
| TASK-USR-04 | Admin: reset password (email link or default) | USR-04, USR-05 | 🔲 Todo | Default: `Abc@123` |
| TASK-USR-05 | Admin: activate / deactivate users | USR-06 | 🔲 Todo | |

---

## Module 7 – Role & Permission Management

| ID | Task | Requirement Refs | Status | Notes |
|---|---|---|---|---|
| TASK-ROLE-01 | CRUD roles | ROLE-01 | 🔲 Todo | |
| TASK-ROLE-02 | Assign permissions to roles | ROLE-02 | 🔲 Todo | |
| TASK-ROLE-03 | Prevent deletion of role if assigned to users | ROLE-03 | 🔲 Todo | |
| TASK-PERM-01 | CRUD permissions | PERM-01 | 🔲 Todo | |
| TASK-PERM-02 | Prevent deletion of permission if attached to roles | PERM-02 | 🔲 Todo | |

---

## Module 8 – System Settings

| ID | Task | Requirement Refs | Status | Notes |
|---|---|---|---|---|
| TASK-SET-01 | Admin: SMTP configuration UI and save | SET-SMTP-01 | 🔲 Todo | |
| TASK-SET-02 | Admin: security settings (lockout, session, attempts) | SET-SEC-01–03 | 🔲 Todo | |
| TASK-SET-03 | Admin: configurable refund policy values | SET-GEN-02 | 🔲 Todo | |
| TASK-SET-04 | Admin: payment/billing configuration | SET-GEN-03 | 🔲 Todo | |
| TASK-SET-05 | Admin: toggle email notifications globally | SET-GEN-04 | 🔲 Todo | |

---

## Module 9 – Audit Logs

| ID | Task | Requirement Refs | Status | Notes |
|---|---|---|---|---|
| TASK-AUDIT-01 | Implement audit log event capture (auth, admin actions, bookings, payments) | AUDIT-01, AUDIT-02 | 🔲 Todo | |
| TASK-AUDIT-02 | Admin: audit log viewer with filtering | AUDIT-01 | 🔲 Todo | |
| TASK-AUDIT-03 | Ensure audit logs are immutable (no edit/delete) | AUDIT-03 | 🔲 Todo | |

---

## Module 10 – QR Code Door Access

| ID | Task | Requirement Refs | Status | Notes |
|---|---|---|---|---|
| TASK-QR-01 | Generate unique QR code upon booking confirmation | QR-01 | 🔲 Todo | |
| TASK-QR-02 | Invalidate and regenerate QR code after checkout time | QR-03 | 🔲 Todo | Scheduled job |
| TASK-QR-03 | Admin: manual QR regeneration for housekeeping | QR-04 | 🔲 Todo | |
| TASK-QR-04 | Auto-generate new QR after housekeeping completion | QR-05 | 🔲 Todo | |

---

## Cross-Cutting / Infrastructure

| ID | Task | Notes | Status |
|---|---|---|---|
| TASK-INFRA-01 | Set up project (Laravel + Vite) | Refer to TECH_STACK.md | 🔲 Todo |
| TASK-INFRA-02 | Configure database and run initial migrations | Refer to DB_SCHEMA.md | 🔲 Todo |
| TASK-INFRA-03 | Set up scheduled task runner (cron) for jobs | Auto-cancel, reminders, QR expiry | 🔲 Todo |
| TASK-INFRA-04 | Configure HTTPS / SSL certificate | NFR-02 | 🔲 Todo |
| TASK-INFRA-05 | Write feature tests for booking flow | | 🔲 Todo |
| TASK-INFRA-06 | Write feature tests for payment flow | | 🔲 Todo |
