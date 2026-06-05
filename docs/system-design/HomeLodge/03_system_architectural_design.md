# Chapter 3: System Architectural Design (SDD) — HomeLodge Booking Homestay System

This chapter describes the architectural style chosen for HomeLodge, the rationale behind it, and the high-level component model that shows how the system's responsibilities are partitioned and how subsystems collaborate.

---

## 3.1 Architecture Style and Rationale

**Chosen Style:** Model–View–Controller (MVC) with a Service Layer

HomeLodge follows the **MVC architectural pattern** as implemented natively by the Laravel framework, augmented with a **Service Layer** for complex business logic.

| Layer | Responsibility | HomeLodge Implementation |
| :--- | :--- | :--- |
| **Model** | Encapsulates the data and business rules. Manages database interactions through the ORM. | Eloquent models (e.g., `User`, `Booking`, `HomestayUnit`, `Payment`, `QrCode`, `ChatMessage`, `Feedback`) with relationships, scopes, accessors, and mutators. |
| **View** | Presents data to the user and captures user input. | Laravel Blade templates with Alpine.js for client-side interactivity. Separate layout files for guest (top-navbar) and admin (sidebar) interfaces. |
| **Controller** | Receives user requests, coordinates the Model and View, and returns responses. | Laravel controllers organised by module (e.g., `BookingController`, `PaymentController`, `HomestayController`). |
| **Service Layer** | Encapsulates complex business logic that spans multiple models or involves external integrations. | Service classes (e.g., `BookingService`, `PaymentService`, `QrCodeService`, `NotificationService`) called by controllers. |

**Rationale:**

1. **Framework alignment:** Laravel is built around MVC. Using MVC means the team works with the framework rather than against it, reducing development friction and making it easier for new team members to onboard.
2. **Separation of concerns:** Each layer has a single, clear responsibility. The View handles presentation, the Controller handles request routing, the Model handles data, and the Service Layer handles orchestration. This separation makes the codebase easier to maintain, test, and extend.
3. **Testability:** Controllers remain thin (delegating to services), making unit testing of business logic straightforward without requiring full HTTP request cycles.
4. **Scalability:** The layered architecture allows individual components (e.g., the WebSocket server, the queue worker, the web server) to be scaled independently.
5. **Proven pattern:** MVC is one of the most widely used and well-understood architectural patterns in web development, with extensive community support and documentation.

---

## 3.2 Component Model

The HomeLodge system is decomposed into the following high-level subsystems:

```
┌─────────────────────────────────────────────────────────────────────┐
│                          Client Layer                               │
│  ┌──────────────────┐  ┌──────────────────┐  ┌──────────────────┐  │
│  │  Guest Browser   │  │  Admin Browser   │  │  Smart Lock HW   │  │
│  │  (Blade/Alpine)  │  │  (Blade/Alpine)  │  │  (QR Scanner)    │  │
│  └────────┬─────────┘  └────────┬─────────┘  └────────┬─────────┘  │
└───────────┼──────────────────────┼──────────────────────┼───────────┘
            │ HTTPS                │ HTTPS                │ HTTPS
┌───────────┼──────────────────────┼──────────────────────┼───────────┐
│           ▼                      ▼                      ▼           │
│  ┌─────────────────────────────────────────────────────────────┐    │
│  │                    Web Server (Nginx)                        │    │
│  │                    + PHP-FPM (Laravel)                       │    │
│  └──────────┬──────────────────────────────────────────────────┘    │
│             │                                                       │
│  ┌──────────▼──────────────────────────────────────────────────┐    │
│  │                 Application Layer (Laravel MVC)              │    │
│  │  ┌───────────┐ ┌───────────┐ ┌───────────┐ ┌────────────┐  │    │
│  │  │   Auth    │ │  Booking  │ │  Payment  │ │  Homestay  │  │    │
│  │  │  Module   │ │  Module   │ │  Module   │ │  Module    │  │    │
│  │  └───────────┘ └───────────┘ └───────────┘ └────────────┘  │    │
│  │  ┌───────────┐ ┌───────────┐ ┌───────────┐ ┌────────────┐  │    │
│  │  │   Chat    │ │   User    │ │   Role &  │ │  Settings  │  │    │
│  │  │  Module   │ │  Mgmt     │ │  Perm     │ │  Module    │  │    │
│  │  └───────────┘ └───────────┘ └───────────┘ └────────────┘  │    │
│  │  ┌───────────┐ ┌───────────┐ ┌───────────┐ ┌────────────┐  │    │
│  │  │Notification│ │  Audit   │ │  QR Code  │ │ Reporting  │  │    │
│  │  │  Module   │ │  Log     │ │  Module   │ │ & Analytics│  │    │
│  │  └───────────┘ └───────────┘ └───────────┘ └────────────┘  │    │
│  │  ┌───────────┐                                              │    │
│  │  │ Feedback  │                                              │    │
│  │  │  Module   │                                              │    │
│  │  └───────────┘                                              │    │
│  └─────────────────────────────────────────────────────────────┘    │
│             │              │              │                         │
│  ┌──────────▼──┐  ┌───────▼────┐  ┌──────▼──────┐                 │
│  │   MySQL /   │  │   Redis    │  │   Laravel   │                 │
│  │   MariaDB   │  │ (Cache /   │  │   Reverb    │                 │
│  │ (Primary DB)│  │  Queue /   │  │ (WebSocket) │                 │
│  │             │  │  Session)  │  │             │                 │
│  └─────────────┘  └────────────┘  └─────────────┘                 │
│                                                                     │
│                        Server Layer                                 │
└─────────────────────────────────────────────────────────────────────┘
            │                              │
  ┌─────────▼──────────┐    ┌─────────────▼──────────────┐
  │  Payment Gateway   │    │  Google APIs                │
  │  (Billplz/Stripe/  │    │  (OAuth 2.0, Calendar v3)  │
  │   Toyyibpay)       │    │                             │
  └────────────────────┘    └────────────────────────────┘
              External Services
```

> **Note:** Replace this ASCII diagram with a proper UML component diagram (Draw.io or equivalent) for the final submission.

**Subsystem Descriptions:**

| Subsystem | Responsibilities |
| :--- | :--- |
| **Auth Module** | User registration, login/logout, password management, Google SSO, account lockout, and profile management. |
| **Homestay Module** | CRUD operations on homestay units, image management, house policy management, and public listing/detail views for guests. |
| **Booking Module** | Booking lifecycle management — date selection, availability checking, booking creation, cancellation, refund calculation, and auto-cancellation of unpaid bookings. |
| **Payment Module** | Bill generation, payment gateway integration (redirect + webhook), receipt generation, and payment history tracking. |
| **Notification Module** | In-app notifications (database), email notifications (SMTP), scheduled reminders, and Google Calendar synchronisation. |
| **Chat Module** | Real-time messaging over WebSocket between guests and admin. Message persistence and conversation management. |
| **User Management Module** | Admin CRUD on user accounts, password resets, account activation/deactivation. |
| **Role & Permission Module** | RBAC management using Spatie Laravel Permission — role and permission CRUD, assignment, and guard checks. |
| **Settings Module** | Database-backed system configuration — SMTP, security, payment, extension charges, default policies, and general settings. |
| **Audit Log Module** | Read-only logging of all user actions, admin changes, and system events via Spatie Activity Log. |
| **QR Code Module** | QR code generation, invalidation, regeneration (guest and housekeeping), and booking extension workflow. |
| **Reporting & Analytics Module** | Dashboard metrics (bookings, revenue, occupancy, cancellation rate), charts (Chart.js), and PDF/CSV export. |
| **Feedback Module** | Guest star ratings and reviews, admin replies, and feedback moderation. |

**How Subsystems Collaborate:**

- The **Booking Module** depends on the **Homestay Module** (to check unit availability), the **Payment Module** (to create bills and process payments), the **Notification Module** (to send booking confirmations and reminders), and the **QR Code Module** (to generate door access codes upon confirmation).
- The **Payment Module** communicates with the external **Payment Gateway** via server-side API calls and receives asynchronous updates via webhooks. Upon successful payment, it triggers the **Booking Module** to confirm the booking and the **QR Code Module** to generate a QR code.
- The **QR Code Module** depends on the **Booking Module** for booking dates and the **Settings Module** for extension charge rates and payment window durations.
- The **Notification Module** is used cross-cutting by almost every other module to deliver in-app, email, and calendar notifications.
- The **Audit Log Module** is cross-cutting — it hooks into Eloquent model events across all modules and records changes automatically.
- The **Auth Module** works with the **Role & Permission Module** to enforce access control at the middleware level.
- The **Settings Module** is read by multiple modules at runtime (Auth reads lockout settings, Booking reads payment hold durations, QR Code reads extension windows, Notification reads SMTP config).
- **Redis** serves as the shared infrastructure for caching (settings, frequently accessed data), session storage, and the queue driver (for email jobs, scheduled tasks, and webhook processing).
- **Laravel Reverb** provides the WebSocket infrastructure consumed by the **Chat Module** and the **Notification Module** for real-time delivery.

---

*— End of Chapter 3: System Architectural Design —*
