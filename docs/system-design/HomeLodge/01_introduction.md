# Chapter 1: Introduction — HomeLodge Booking Homestay System

---

## 1.1 Purpose

This System Documentation (SD) describes the **HomeLodge Booking Homestay System**. It serves as the combined Software Requirements Specification (SRS), Software Design Description (SDD), and Software Test Documentation (STD) for the project. Every functional requirement, interface specification, behavioural constraint, architectural decision, component design, data structure, and test case that the development team needs is recorded in this document.

The document is written so that:

- **Designers** can derive the system architecture from it.
- **Developers** can implement against it.
- **Testers** can build test cases from the stated requirements.
- **Project managers** and the **client** may use it to confirm that what will be built matches what was agreed upon.

| Audience | How They Use This Document |
| :--- | :--- |
| Software Developers | Read the module requirements and interface descriptions to guide implementation. |
| System Designers / Architects | Use the requirements as input when designing the architecture. |
| Quality Assurance / Testers | Derive test cases from the requirements and verify that the system meets each one. |
| Project Managers | Track scope and check that every requirement is addressed during development. |
| Stakeholders / Client | Confirm the documented requirements match the desired product. |

---

## 1.2 Scope

The software product specified in this document is **HomeLodge**, a web-based homestay booking management system. HomeLodge is built as a single web application accessible through desktop and mobile browsers, and it is intended for small to medium homestay operators who manage one or more properties. Many of these operators currently rely on manual methods such as spreadsheets, phone calls, and physical key handovers to handle reservations. HomeLodge replaces those manual workflows with a digital platform that covers the full reservation lifecycle — from the point a guest registers an account and browses available units, through booking and online payment, all the way to a QR code that unlocks the homestay door on arrival.

The system supports **two user roles**:

- **Guests** can register (using email/password or Google Single Sign-On), browse multiple homestay unit listings, view per-unit availability calendars, submit bookings, pay through a third-party payment gateway, receive in-app and email notifications, chat with the operator in real time over WebSocket, and leave star ratings and written reviews after their stay.

- **Administrators** have access to a back-office dashboard where they manage homestay unit profiles (each with its own pricing, images, check-in/check-out times, and house policies), handle all booking operations (including creating bookings on behalf of guests, blocking dates, and filtering by status or date range), view and regenerate bills and receipts, manage user accounts, define roles and permissions, and configure every operational parameter through a settings panel.

The system also generates a unique, time-limited QR code for each confirmed booking so that guests can access the property without a physical key, and it automatically invalidates and regenerates these codes at check-out. If a guest needs to extend their stay, the administrator initiates an extension request; the system checks availability, calculates the additional charge from configurable rates, and gives the guest a set window to pay before reverting the booking to original dates.

A tiered cancellation and refund policy applies to all bookings: no refund if the guest cancels within three days of the stay, a 25 % refund if cancelled one week before, and a 50 % refund if cancelled two weeks or more before. These percentages are configurable by the administrator, and refunds are processed within three to five business days.

Additionally, all operational parameters — from SMTP credentials and security thresholds to refund percentages and extension charge rates — are stored in the database and managed through the admin panel rather than being hardcoded. A read-only audit log records every user action, administrative change, and system event for accountability. An analytics dashboard gives the administrator visibility into total bookings, revenue, occupancy rates, and cancellation rates, with the option to export reports in PDF or CSV format. Bookings also sync to Google Calendar for both guests and administrators.

**What HomeLodge does NOT do:**

- It does not provide native iOS or Android mobile applications; all access is through a web browser.
- It does not integrate with third-party Online Travel Agency (OTA) platforms such as Airbnb or Booking.com.
- Push notifications through native mobile services are excluded from the initial release.

**Objective:** The objective of HomeLodge is to give homestay operators a single platform that automates booking management, payment collection, and property access while providing guests with a straightforward self-service experience. By digitising these processes, the system reduces the administrative burden on operators, gives guests immediate confirmation and contactless check-in, and produces structured financial records (bills, receipts, reports) alongside a complete audit trail.

The requirements documented in this SD are traceable to the Product Requirements Document (PRD) v1.2 and the User Requirements Specification (URS) v1.2, both of which were written prior to this document and define the product vision, user stories, and requirement identifiers referenced throughout.

---

## 1.3 Definitions, Acronyms and Abbreviations

| Term / Acronym | Definition |
| :--- | :--- |
| SD | System Documentation — this document. |
| SRS | Software Requirements Specification — the portion of this SD that details functional and non-functional requirements. |
| SDD | Software Design Description — the portion of this SD that covers architecture, component design, data design, and interface design. |
| STD | Software Test Documentation — the portion of this SD that contains test cases and the traceability matrix. |
| PRD | Product Requirements Document — a higher-level specification defining product features and acceptance criteria. |
| URS | User Requirements Specification — a document capturing user needs in the form of user stories. |
| HomeLodge | The web-based booking homestay system described in this document. |
| Guest / User | A registered person who uses the system to browse units, make bookings, and pay. |
| Admin | The homestay operator or manager with full administrative access. |
| Homestay Unit | One managed property in the system, with its own profile, pricing, policies, and availability. |
| Booking | A reservation for a specific date range at a specific homestay unit. |
| QR Code | Quick Response Code — a machine-readable label generated per booking for door access. |
| Booking Extension | A request to push back the check-out time or date, creating a pending charge the guest must pay within a set window. |
| Extension Payment Window | The time limit (configurable per unit, default 60 minutes) for a guest to pay the extension charge before the request is auto-cancelled. |
| Bill | A payment request document generated when a booking is created. |
| Receipt | A confirmation document generated after a payment succeeds. |
| Refund | Returning part of the payment to the guest after a cancellation. |
| Webhook | An HTTP callback the payment gateway uses to notify the system of payment outcomes. |
| SSO | Single Sign-On — here referring to Google OAuth 2.0. |
| OAuth 2.0 | An authorisation framework for delegated access, used for Google SSO and Calendar integration. |
| RBAC | Role-Based Access Control — restricting system access by assigning roles and permissions. |
| SMTP | Simple Mail Transfer Protocol — the protocol for sending outgoing emails. |
| WebSocket | A protocol for two-way, persistent communication over a single TCP connection, used for chat. |
| Lockout | A temporary block on a user account after too many failed login attempts. |
| Housekeeping | Cleaning and preparing the property between bookings. |
| OTA | Online Travel Agency — third-party platforms like Airbnb or Booking.com. |
| CRUD | Create, Read, Update, Delete — the four basic data operations. |
| API | Application Programming Interface. |
| HTTPS | HTTP Secure — encrypted HTTP traffic using TLS. |
| TLS/SSL | Transport Layer Security / Secure Sockets Layer — cryptographic protocols for secure network communication. |
| MySQL | An open-source relational database used as HomeLodge's primary data store. |
| Laravel | A PHP web application framework — the backend of HomeLodge. |
| Blade | Laravel's templating engine for server-side HTML rendering. |
| Alpine.js | A lightweight JavaScript framework for client-side interactivity. |
| Vite | A build tool for bundling frontend assets. |
| Redis | An in-memory data store used for caching, sessions, and queues. |
| Eloquent | Laravel's built-in ORM (Object-Relational Mapping). |
| Laravel Reverb | A first-party Laravel WebSocket server package. |
| House Policies | Rules per homestay unit that guests must follow (e.g., no smoking, no pets). |
| Feedback | A star rating and written review a guest submits after their stay. |
| Extension Charge | The extra fee for extending a booking, calculated from a configurable rate. |
| PDF | Portable Document Format — used for bills, receipts, and report exports. |
| CSV | Comma-Separated Values — a format for exporting tabular data. |

---

## 1.4 References

The table below lists every document referenced in this SD. Internal project documents are located in the project repository; external references are available at the URLs shown.

| # | Document Title | Version | Date | Source / Location |
| :--- | :--- | :--- | :--- | :--- |
| 1 | Product Requirements Document (PRD) – HomeLodge | 1.2 | 2026-04-05 | `/docs/dev/PRD.md` |
| 2 | User Requirements Specification (URS) – HomeLodge | 1.2 | 2026-04-05 | `/docs/dev/URS.md` |
| 3 | Technology Stack – HomeLodge | 1.2 | 2026-04-05 | `/docs/dev/TECH_STACK.md` |
| 4 | Database Schema – HomeLodge | 1.2 | 2026-04-05 | `/docs/dev/DB_SCHEMA.md` |
| 5 | Use Case Diagrams – HomeLodge | 1.2 | 2026-04-05 | `/docs/dev/USE_CASE_DIAGRAMS.md` |
| 6 | Use Case Descriptions – HomeLodge | 1.2 | 2026-04-05 | `/docs/dev/USE_CASE_DESCRIPTIONS.md` |
| 7 | UI/UX Design Guidelines – HomeLodge | 1.2 | 2026-04-05 | `/docs/dev/UI_UX.md` |
| 8 | Development Tasks – HomeLodge | — | — | `/docs/dev/TASKS.md` |
| 9 | HomeLodge System Requirements (Raw) | — | — | `/HomeLodge System.md` |
| 10 | HomeLodge Requirements (Raw) | — | — | `/homelodge.md` |
| 11 | Laravel Documentation | 11.x | 2026 | https://laravel.com/docs/11.x |
| 12 | Spatie Laravel Permission Documentation | Latest | — | https://spatie.be/docs/laravel-permission |
| 13 | Spatie Laravel Activity Log Documentation | Latest | — | https://spatie.be/docs/laravel-activitylog |
| 14 | Google Calendar API Documentation | v3 | — | https://developers.google.com/calendar |
| 15 | Laravel Reverb Documentation | Latest | — | https://reverb.laravel.com |
| 16 | Chart.js Documentation | Latest | — | https://www.chartjs.org/docs |
| 17 | IEEE Std 830-1998 | — | 1998 | IEEE Recommended Practice for Software Requirements Specifications |
| 18 | IEEE Std 1016-1998 | — | 1998 | IEEE Recommended Practice for Software Design Descriptions |
| 19 | IEEE Std 829-2008 | — | 2008 | IEEE Standard for Software and System Test Documentation |

---

## 1.5 Overview

This System Documentation is organised into the following chapters:

**Chapter 1 — Introduction** covers the purpose and audience of the SD, the scope of the HomeLodge product, a glossary of terms, references, and this structural overview.

**Chapter 2 — Specific Requirements (SRS)** contains the actual software requirements. It is split into two main parts:

- **Section 2.1 (External Interface Requirements)** describes how the system connects to the outside world. This covers the user interfaces for both guest and admin roles (2.1.1), the QR code smart lock hardware (2.1.2), all third-party software and packages the system depends on (2.1.3), and the communication protocols in use such as HTTPS, WebSocket, SMTP, and webhooks (2.1.4).

- **Section 2.2 (System Features)** walks through every functional module in the system. For each module, the document states what inputs the system accepts, what processing it performs, and what outputs it produces. The modules covered are: Authentication, Homestay Management, Booking, Payment, Notification, Chat, User Management, Role and Permission Management, System Settings, Audit Logging, QR Code Door Access, Reporting and Analytics, and Guest Feedback.

**Chapter 3 — System Architectural Design (SDD)** describes the chosen architectural style, its rationale, and the high-level component model showing how the system's responsibilities are partitioned and how subsystems collaborate.

**Chapter 4 — Detailed Description of Components (SDD)** provides the complete package diagram, class diagrams for each subsystem, method algorithms, and sequence diagrams for each use case scenario.

**Chapter 5 — Data Design (SDD)** describes how the system's information domain is transformed into data structures, lists all database entities, and provides a data dictionary with attribute-level detail.

**Chapter 6 — Interface Design (SDD)** describes the external, internal, physical, logical, hardware, and software interfaces of the system.

**Chapter 7 — Requirements Matrix** provides a cross-reference table tracing components and data structures back to the requirements in Chapter 2.

**Chapter 8 — Test Cases (STD) + Appendix A** contains the numbered test cases for each use case scenario and a traceability matrix linking test cases back to use cases, sequence diagrams, and packages.
