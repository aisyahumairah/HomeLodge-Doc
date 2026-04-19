# System Documentation (SD)
## HomeLodge – Booking Homestay System

| Field | Detail |
|---|---|
| **Document Version** | 1.0 |
| **Status** | Draft |
| **Date** | 2026-04-19 |
| **Prepared By** | Development Team |
| **Approved By** | — |

---

## Table of Contents

1. [Introduction](#1-introduction)
   - [1.1 Purpose](#11-purpose)
   - [1.2 Scope](#12-scope)
   - [1.3 Definitions, Acronyms and Abbreviations](#13-definitions-acronyms-and-abbreviations)
   - [1.4 References](#14-references)
   - [1.5 Overview](#15-overview)
2. [Specific Requirements](#2-specific-requirements)
   - [2.1 External Interface Requirements](#21-external-interface-requirements)
     - [2.1.1 User Interfaces](#211-user-interfaces)
     - [2.1.2 Hardware Interfaces](#212-hardware-interfaces)
     - [2.1.3 Software Interfaces](#213-software-interfaces)
     - [2.1.4 Communication Interfaces](#214-communication-interfaces)
   - [2.2 System Features](#22-system-features)

---

## 1. Introduction

### 1.1 Purpose

This System Documentation (SD) describes the HomeLodge Booking Homestay System. It doubles as the Software Requirements Specification (SRS) for the project: every functional requirement, interface specification, and behavioural constraint that the development team needs is recorded here.

The document is written so that designers can derive system architecture from it, developers can implement against it, and testers can build test cases from the stated requirements. Project managers and the client may also use it to confirm that what will be built matches what was agreed upon.

| Audience | How they use this document |
|---|---|
| Software Developers | Read the module requirements and interface descriptions to guide implementation. |
| System Designers / Architects | Use the requirements as input when designing the architecture. |
| Quality Assurance / Testers | Derive test cases from the requirements and verify that the system meets each one. |
| Project Managers | Track scope and check that every requirement is addressed during development. |
| Stakeholders / Client | Confirm the documented requirements match the desired product. |

---

### 1.2 Scope

The software product specified in this document is HomeLodge, a web based homestay booking management system. HomeLodge is built as a single web application accessible through desktop and mobile browsers, and it is intended for small to medium homestay operators who manage one or more properties. Many of these operators currently rely on manual methods such as spreadsheets, phone calls, and physical key handovers to handle reservations. HomeLodge replaces those manual workflows with a digital platform that covers the full reservation lifecycle, from the point a guest registers an account and browses available units, through booking and online payment, all the way to a QR code that unlocks the homestay door on arrival.

The system supports two user roles. Guests can register (using email/password or Google Single Sign-On), browse multiple homestay unit listings, view per-unit availability calendars, submit bookings, pay through a third-party payment gateway, receive in-app and email notifications, chat with the operator in real time over WebSocket, and leave star ratings and written reviews after their stay. Administrators have access to a back-office dashboard where they manage homestay unit profiles (each with its own pricing, images, check-in/check-out times, and house policies), handle all booking operations (including creating bookings on behalf of guests, blocking dates, and filtering by status or date range), view and regenerate bills and receipts, manage user accounts, define roles and permissions, and configure every operational parameter through a settings panel. The system also generates a unique, time-limited QR code for each confirmed booking so that guests can access the property without a physical key, and it automatically invalidates and regenerates these codes at check-out. If a guest needs to extend their stay, the administrator initiates an extension request; the system checks availability, calculates the additional charge from configurable rates, and gives the guest a set window to pay before reverting the booking to original dates. A tiered cancellation and refund policy applies to all bookings: no refund if the guest cancels within three days of the stay, a 25% refund if cancelled one week before, and a 50% refund if cancelled two weeks or more before. These percentages are configurable by the administrator, and refunds are processed within three to five business days. Additionally, all operational parameters, from SMTP credentials and security thresholds to refund percentages and extension charge rates, are stored in the database and managed through the admin panel rather than being hardcoded. A read-only audit log records every user action, administrative change, and system event for accountability. An analytics dashboard gives the administrator visibility into total bookings, revenue, occupancy rates, and cancellation rates, with the option to export reports in PDF or CSV format. Bookings also sync to Google Calendar for both guests and administrators.

There are several things HomeLodge does not do. It does not provide native iOS or Android mobile applications; all access is through a web browser. It does not integrate with third-party Online Travel Agency platforms such as Airbnb or Booking.com. Push notifications through native mobile services are also excluded from the initial release.

The objective of HomeLodge is to give homestay operators a single platform that automates booking management, payment collection, and property access while providing guests with a straightforward self-service experience. By digitising these processes, the system reduces the administrative burden on operators, gives guests immediate confirmation and contactless check-in, and produces structured financial records (bills, receipts, reports) alongside a complete audit trail. The requirements documented in this SD are traceable to the Product Requirements Document (PRD) v1.2 and the User Requirements Specification (URS) v1.2, both of which were written prior to this document and define the product vision, user stories, and requirement identifiers referenced throughout.

---

### 1.3 Definitions, Acronyms and Abbreviations

| Term / Acronym | Definition |
|---|---|
| SD | System Documentation, this document. |
| SRS | Software Requirements Specification, the portion of this SD that details functional and non-functional requirements. |
| PRD | Product Requirements Document, a higher-level specification defining product features and acceptance criteria. |
| URS | User Requirements Specification, a document capturing user needs in the form of user stories. |
| HomeLodge | The web based booking homestay system described in this document. |
| Guest / User | A registered person who uses the system to browse units, make bookings, and pay. |
| Admin | The homestay operator or manager with full administrative access. |
| Homestay Unit | One managed property in the system, with its own profile, pricing, policies, and availability. |
| Booking | A reservation for a specific date range at a specific homestay unit. |
| QR Code | Quick Response Code, a machine-readable label generated per booking for door access. |
| Booking Extension | A request to push back the check-out time or date, creating a pending charge the guest must pay within a set window. |
| Extension Payment Window | The time limit (configurable per unit, default 60 minutes) for a guest to pay the extension charge before the request is auto-cancelled. |
| Bill | A payment request document generated when a booking is created. |
| Receipt | A confirmation document generated after a payment succeeds. |
| Refund | Returning part of the payment to the guest after a cancellation. |
| Webhook | An HTTP callback the payment gateway uses to notify the system of payment outcomes. |
| SSO | Single Sign-On, here referring to Google OAuth 2.0. |
| OAuth 2.0 | An authorisation framework for delegated access, used for Google SSO and Calendar integration. |
| RBAC | Role-Based Access Control, restricting system access by assigning roles and permissions. |
| SMTP | Simple Mail Transfer Protocol, the protocol for sending outgoing emails. |
| WebSocket | A protocol for two-way, persistent communication over a single TCP connection, used for chat. |
| Lockout | A temporary block on a user account after too many failed login attempts. |
| Housekeeping | Cleaning and preparing the property between bookings. |
| OTA | Online Travel Agency, third-party platforms like Airbnb or Booking.com. |
| CRUD | Create, Read, Update, Delete, the four basic data operations. |
| API | Application Programming Interface. |
| HTTPS | HTTP Secure, encrypted HTTP traffic using TLS. |
| TLS/SSL | Transport Layer Security / Secure Sockets Layer, cryptographic protocols for secure network communication. |
| MySQL | An open source relational database used as HomeLodge's primary data store. |
| Laravel | A PHP web application framework, the backend of HomeLodge. |
| Blade | Laravel's templating engine for server-side HTML rendering. |
| Alpine.js | A lightweight JavaScript framework for client-side interactivity. |
| Vite | A build tool for bundling frontend assets. |
| Redis | An in-memory data store used for caching, sessions, and queues. |
| Eloquent | Laravel's built-in ORM. |
| Laravel Reverb | A first-party Laravel WebSocket server package. |
| House Policies | Rules per homestay unit that guests must follow (e.g., no smoking, no pets). |
| Feedback | A star rating and written review a guest submits after their stay. |
| Extension Charge | The extra fee for extending a booking, calculated from a configurable rate. |
| PDF | Portable Document Format, used for bills, receipts, and report exports. |
| CSV | Comma-Separated Values, a format for exporting tabular data. |

---

### 1.4 References

The table below lists every document referenced in this SD. Internal project documents are located in the project repository; external references are available at the URLs shown.

| # | Document Title | Version | Date | Source / Location |
|---|---|---|---|---|
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

---

### 1.5 Overview

This document has two main sections.

Section 1 (Introduction) covers the purpose and audience of the SD, the scope of the HomeLodge product, a glossary of terms, references, and this structural overview.

Section 2 (Specific Requirements) contains the actual requirements. It is split into two parts:

- Section 2.1 (External Interface Requirements) describes how the system connects to the outside world. This covers the user interfaces for both guest and admin roles (2.1.1), the QR code smart lock hardware (2.1.2), all third-party software and packages the system depends on (2.1.3), and the communication protocols in use such as HTTPS, WebSocket, SMTP, and webhooks (2.1.4).

- Section 2.2 (System Features) walks through every functional module in the system. For each module, the document states what inputs the system accepts, what processing it performs, and what outputs it produces. The modules covered are Authentication, Homestay Management, Booking, Payment, Notification, Chat, User Management, Role and Permission Management, System Settings, Audit Logging, QR Code Door Access, Reporting and Analytics, and Guest Feedback.

---

## 2. Specific Requirements

This section lists all software requirements for HomeLodge at a level of detail sufficient for design, implementation, and testing. Each requirement is externally perceivable by a user, an operator, or an external system. Requirements are organised by interface type and then by functional module.

---

### 2.1 External Interface Requirements

#### 2.1.1 User Interfaces

HomeLodge has two separate interfaces, one for guests and one for administrators.

**A. Guest (User) Interface**

The guest side uses a top navigation bar layout:

- A fixed top navbar shows the system logo, navigation links (Home, My Bookings, Notifications), and a user avatar dropdown for profile and logout access.
- The content area is full width but capped at 1200px and centred.
- A footer sits at the bottom.
- The navbar stays visible on scroll.

The following table describes the main screens a guest interacts with:

| Screen / Feature | Description |
|---|---|
| Landing Page | Lists all active homestay units as browsable cards. Each card shows a thumbnail image, the unit name, location, base price, and average star rating. |
| Unit Detail Page | Displays the full description, an image gallery, the availability calendar for that unit, house policies in checklist format, pricing details, and check-in/check-out times. A "Book Now" button is placed prominently. |
| Booking Form | Includes date and time pickers for check-in and check-out. The system validates availability as the guest selects dates. If the date is taken, an error message appears and the submit button is disabled. |
| My Bookings | A table listing current, upcoming, and past bookings. Each row shows the booking number, unit name, dates, a colour-coded status badge, and action buttons (View, Cancel). |
| Booking Detail | Shows all booking information, payment status, the QR code (once the booking is confirmed), and a feedback form (once the stay is completed). |
| Payment Page | Summarises the bill and redirects the guest to the payment gateway. On return, it shows a success or failure confirmation. |
| Chat | A messaging view where guest messages appear on the right in the primary colour and admin replies appear on the left in grey. Messages have timestamps and are delivered in real time. |
| Notifications | A bell icon in the navbar with a badge count. Clicking it opens a dropdown of recent notifications, each with a timestamp and a link to the relevant page. |

**B. Admin Interface**

The admin side uses a fixed left sidebar paired with a top header bar:

- The sidebar groups menu items by function (Dashboard, Homestays, Bookings, Payments, QR Access, Guests, Users, Access Control, Communication, System).
- The top header shows the current page title, a notification bell, and quick-action buttons.
- On tablets, the sidebar collapses to icons only. On phones, it becomes a hamburger menu.

The table below describes the main admin screens:

| Screen / Feature | Description |
|---|---|
| Dashboard | Cards showing total bookings, revenue, occupancy rate, and cancellation rate. Below the cards are booking trend charts (with daily/weekly/monthly toggles), revenue charts, and a feedback summary. |
| Booking Calendar | A month view calendar where dates are colour-coded: green for available, red for booked, grey for admin-blocked, amber for pending payment. Clicking a date opens the booking detail or a creation form. |
| Booking Management | A filterable table (status, booking ID, date range) with buttons for view, edit, cancel, and delete on each row. |
| Payment / Billing Lists | Tabular views of bills and payments. Both can be filtered by ID, date range, and status. Bills and receipts can be regenerated from here. |
| User Management | A table of all users with status indicators, role labels, and buttons for edit, delete, activate/deactivate, and password reset. |
| Roles and Permissions | List and detail views for roles and permissions. The system warns the admin if they try to delete a role or permission that is still in use. |
| System Settings | A grouped settings page covering SMTP, Security, Payment, Notification, Extension Charges, and Default Policies. Changes are saved to the database immediately. |
| Audit Logs | A read-only table of audit entries showing timestamp, user, action, affected record, and change details. The admin can filter but not modify entries. |
| QR Code Management | Lists QR codes per booking with their status, validity period, and controls for regeneration and housekeeping workflow. |
| Chat | A split-panel view with conversations on the left and the active chat on the right. Unread badges appear on conversations with new messages. |

**General UI conventions:**

- The typeface is Inter (Google Fonts) across all screens.
- Spacing follows an 8px base grid.
- Status badges are pill-shaped and colour-coded: green for Confirmed, amber for Pending Payment, red for Cancelled, grey for Blocked, blue for Completed, teal for Extended.
- Buttons darken or gain a shadow on hover. Disabled buttons are dimmed to half opacity. Buttons in a loading state show a spinner.
- Form labels are placed above input fields. Validation errors appear below the field in red. Required fields have an asterisk.
- Tables use alternating row shading, sortable column headers, pagination, and icon buttons with tooltips for row actions.
- Toast notifications appear at the top right and disappear after 5 seconds.
- Confirmation modals for destructive actions (delete, cancel) use a red confirm button.
- The system is designed to meet WCAG 2.1 Level AA accessibility standards, with a minimum 4.5:1 colour contrast ratio for body text, full keyboard navigation, and ARIA labels on icon-only buttons.

---

#### 2.1.2 Hardware Interfaces

HomeLodge interfaces with one category of hardware:

**QR Code Smart Lock**

| Characteristic | Detail |
|---|---|
| Device Type | A QR code-compatible smart lock mounted on the door of each homestay unit. |
| Supported Devices | Cloud-connected smart locks that accept smartphone-scanned QR codes (e.g., QRLock, TTlock, or equivalent). |
| How It Works | HomeLodge generates a unique, encrypted, time-limited token for each booking and encodes it as a QR code. The guest scans the code with their phone camera or a companion app. The smart lock checks the token against the backend (or a synchronised local store) and unlocks the door if the token is valid. |
| Data Exchanged | The QR code contains a secure token (stored in the `qr_codes.token` column) validated against the database, including the validity window (`valid_from` to `valid_until`). |
| Validity Window | The code is active from check-in (default 3:00 PM) to check-out (default 12:00 PM). After check-out, the code expires and the system regenerates one for housekeeping or for the next guest. |
| Admin Controls | The admin can regenerate a QR code at any time for housekeeping. Once housekeeping is marked complete, a fresh code is generated for the next booking. |
| Vendor Integration | The smart lock vendor's SDK or API must be integrated to validate tokens at the door. The specific vendor has not been finalised. |

**Client Hardware:**

No special hardware is needed on the guest's side. HomeLodge is accessed through a web browser on any desktop, laptop, tablet, or smartphone that meets these minimum requirements:

- A modern browser (Chrome, Firefox, Safari, or Edge, latest two major versions).
- An internet connection that can handle HTTPS and WebSocket traffic.
- A screen width of 320px or more (the layout is responsive).

---

#### 2.1.3 Software Interfaces

HomeLodge depends on the following external software:

**A. Backend framework and runtime**

| Software | Version | What it does |
|---|---|---|
| Laravel | 11.x | PHP web framework handling routing, controllers, models, authentication, queuing, and task scheduling. |
| PHP | 8.2+ | Server-side language. |
| Composer | Latest | PHP dependency manager. |

**B. Database**

| Software | Version | What it does |
|---|---|---|
| MySQL | 8.x | Primary relational database. |
| MariaDB | 10.x | Supported alternative to MySQL. |
| Eloquent ORM | Built-in (Laravel) | Object-relational mapping layer. All queries go through Eloquent models and migrations. |

**C. Frontend**

| Software | Version | What it does |
|---|---|---|
| Laravel Blade | Built-in (Laravel) | Server-side template engine for HTML rendering. |
| Alpine.js | Latest | Lightweight JavaScript framework for client-side interactions like form validation and UI toggles. |
| Vite | Latest | Build tool for asset compilation, bundling, and hot module replacement. |
| Bootstrap 5 / Custom CSS | 5.x | CSS framework plus a custom design system for consistent styling. |
| Chart.js | Latest | JavaScript charting library used in the analytics dashboard for trend lines, bar charts, and pie charts. |
| Font Awesome / Bootstrap Icons | Latest | Icon libraries used throughout the interface. |

**D. Authentication and authorisation packages**

| Package | Identifier | What it does | How the system uses it |
|---|---|---|---|
| Laravel Breeze / Sanctum | `laravel/breeze` or `laravel/sanctum` | Authentication scaffolding (login, registration, password reset, email verification). | Provides session-based authentication with CSRF protection. Includes middleware guards for route protection. |
| Laravel Socialite | `laravel/socialite` | Google OAuth 2.0 for SSO. | Connects to Google's OAuth endpoints, exchanges tokens, and retrieves the user's profile (name, email, Google ID). |
| Spatie Laravel Permission | `spatie/laravel-permission` | RBAC management. | Creates the `roles`, `permissions`, and pivot tables. Adds Eloquent traits for assigning roles and permissions to users. Includes middleware for route-level access checks. |

**E. Operational packages**

| Package | Identifier | What it does | How the system uses it |
|---|---|---|---|
| Spatie Laravel Activity Log | `spatie/laravel-activitylog` | Audit logging. | Hooks into Eloquent model events (create, update, delete) and writes entries to the `activity_log` table with before/after state in JSON. |
| SimpleSoftwareIO QR Code | `simplesoftwareio/simple-qrcode` | QR code generation. | Generates QR codes as SVG or PNG images rendered in Blade views. Encodes the booking's unique, secure token. |
| Barryvdh Laravel DomPDF | `barryvdh/laravel-dompdf` | PDF generation. | Converts Blade views into downloadable PDF files for bills, receipts, and reports. |
| Maatwebsite Excel | `maatwebsite/excel` | CSV and Excel export. | Produces downloadable spreadsheets from query results. Used in the reporting module. |

**F. Real-time communication**

| Package | Identifier | What it does | How the system uses it |
|---|---|---|---|
| Laravel Reverb | `laravel/reverb` | WebSocket server. | Self-hosted server implementing the Pusher protocol. Broadcasts events to connected browser clients in real time. |
| Laravel Echo + `pusher-js` | Client-side | WebSocket client. | Subscribes to broadcast channels (chat, notifications) and handles incoming messages in the browser. |

If hosting Laravel Reverb turns out to be too complex, the team may switch to Pusher, a hosted alternative.

**G. Payment gateway**

| Item | Detail |
|---|---|
| Provider | To be determined. Candidates include Billplz (Malaysia), Stripe, and Toyyibpay. |
| Integration approach | Server-side API calls plus webhook callbacks. |
| How it works | The system sends a request to the gateway API with the bill amount and booking reference. The gateway returns a URL and the guest is redirected there to pay. After payment, the gateway sends an HTTPS POST to a dedicated webhook route in the Laravel app. The handler verifies the gateway's signature, processes the payment (using the `gateway_reference` field to prevent duplicates), and updates the booking and bill status. |

**H. Calendar integration**

| Item | Detail |
|---|---|
| Service | Google Calendar API v3 |
| Authentication | Google OAuth 2.0 (through `laravel/socialite`), scope `https://www.googleapis.com/auth/calendar.events` |
| How it works | The system creates, updates, and deletes Google Calendar events that correspond to bookings. Events include check-in/check-out dates, times, and the homestay unit name. |

**I. Caching and queue infrastructure**

| Software | Version | What it does |
|---|---|---|
| Redis | Latest | In-memory store for caching, session data, and queue jobs (used as the Laravel queue driver). |

---

#### 2.1.4 Communication Interfaces

The system uses the following protocols:

**A. HTTPS**

| Item | Detail |
|---|---|
| Protocol | HTTPS (TLS 1.2 or higher) |
| Usage | All traffic between the browser and the server is encrypted: page loads, form submissions, API calls, file downloads. |
| Certificate | Provisioned through Let's Encrypt (Certbot) or an equivalent certificate authority. |
| Enforcement | HTTP requests are redirected to HTTPS. Every route requires HTTPS. |

**B. WebSocket**

| Item | Detail |
|---|---|
| Protocol | WebSocket (RFC 6455) over TLS (wss://) |
| Usage | Provides the real-time channel for the chat module and live notification delivery. |
| Implementation | Laravel Reverb runs the WebSocket server, implementing the Pusher protocol. On the client side, Laravel Echo manages channel subscriptions and event handling. |
| Channel security | Private channels are used for user-specific conversations and notifications. Subscriptions are authenticated through Laravel's broadcasting auth endpoint. |

**C. SMTP**

| Item | Detail |
|---|---|
| Protocol | SMTP with TLS or SSL encryption |
| Usage | Sends all outgoing emails: registration confirmation, password resets, payment notifications, booking reminders, and general alerts. |
| Configuration | The admin configures SMTP settings (host, port, username, password, encryption) through the System Settings page. The values are stored in the `settings` table. Email notifications can be enabled or disabled globally. |

**D. Webhook (HTTP POST)**

| Item | Detail |
|---|---|
| Protocol | HTTPS POST |
| Usage | The payment gateway sends POST requests to a dedicated HomeLodge endpoint whenever a payment status changes (success, failure, or refund). |
| Security | The system validates each webhook using the gateway's signature verification to confirm authenticity and prevent replays. |
| Duplicate handling | Processing is idempotent. If the gateway sends the same webhook twice for the same payment, only one record is created. |

**E. Google Calendar API**

| Item | Detail |
|---|---|
| Protocol | REST over HTTPS |
| Usage | The system creates, updates, and deletes calendar events in the user's Google Calendar to mirror their bookings. |
| Authentication | OAuth 2.0 tokens obtained during the Google SSO login. |
| Data format | JSON payloads following the Google Calendar API v3 specification. |

**F. Internal server communication**

| Path | Detail |
|---|---|
| Web Server to Application | Nginx or Apache proxies requests to PHP-FPM over FastCGI. |
| Application to Database | Laravel connects to MySQL/MariaDB on port 3306 over the local network. |
| Application to Redis | Laravel connects to Redis on port 6379 for caching, sessions, and queues. |

---

### 2.2 System Features

This section describes each functional module in the system. For every module, the document states the inputs the system receives, the processing it performs, and the outputs it produces.

---

#### 2.2.1 Authentication Module

This module handles registration, login, logout, password management, profile updates, and account security for both guests and admins.

**Inputs:**
- A registration form (email and password) or a Google SSO request.
- Login credentials or a Google SSO button click.
- A logout action.
- A forgot-password form with the registered email.
- A change-password form with old password, new password, and confirmation.
- Updated profile information.

**Processing:**

| ID | Requirement |
|---|---|
| AUTH-01 | Users and admins can register using email/password or Google SSO. New accounts are assigned the "User" role by default. |
| AUTH-02 | Users and admins log in with their credentials or Google SSO. The system checks the supplied password against the stored bcrypt hash, or validates the Google OAuth token. |
| AUTH-03 | Logging out terminates the active session. |
| AUTH-04 | The forgot-password flow sends a time-limited reset token to the registered email address. |
| AUTH-05 | Passwords must be 8 to 12 characters long and include at least one uppercase letter, one lowercase letter, one digit, and one special character. |
| AUTH-06 | The password field has a show/hide toggle and a real-time indicator showing which criteria the typed password meets. |
| AUTH-07 | If an admin resets a user's password, the user must change it at next login. Until they do, they cannot access any other page, even by editing the URL. |
| AUTH-08 | After exceeding the maximum number of failed login attempts (set by the admin in System Settings), the account is temporarily locked. |
| AUTH-09 | Locked accounts unlock automatically once the lockout duration has passed. |
| AUTH-10 | A locked account can also be unlocked if the user goes through the forgot-password flow or if an admin resets the password. |
| AUTH-11 | Users and admins can view and update their own profile (name, email, phone, avatar). |

**Outputs:**
- A registration confirmation or an error.
- A redirect to the appropriate dashboard on successful login, or an error with the number of attempts remaining.
- A redirect to the login page after logout.
- A password reset email, or an error if the address is not in the system.
- A confirmation that the password was changed, or a validation error listing unmet criteria.
- A confirmation that the profile was updated.

---

#### 2.2.2 Homestay Management Module

This module lets administrators manage multiple homestay properties and lets guests browse available units. Management operations are admin-only; guests only have read access on the public side.

**Inputs:**
- Admin submits a form with unit name, description, location, images, base price, deposit, and check-in/check-out times.
- Admin edits an existing unit.
- Admin deactivates or deletes a unit.
- Admin uploads or manages unit images.
- Admin adds, edits, or removes house policies for a unit.
- Admin sets the per-unit extension payment window.
- Guest browses the listing of active units.
- Guest views a unit's detail page.

**Processing:**

| ID | Requirement |
|---|---|
| HS-01 | Admin can create a homestay unit with its name, description, location, images, and pricing. |
| HS-02 | Admin can edit any existing unit. |
| HS-03 | Admin can deactivate or delete a unit. A unit that has confirmed future bookings cannot be deleted. |
| HS-04 | Admin can view all units in a list showing each unit's status (active or inactive) and a count of upcoming bookings. |
| HS-05 | Guests can browse all active units and view each unit's details and availability calendar. |
| HS-06 | Availability is tracked per unit, so different units can be booked for the same dates without conflict. |
| HS-07 | Admin can upload and manage multiple images per unit. |
| HS-08 | Admin can set the base price, deposit, and default check-in/check-out times per unit. |
| HS-09 | Each unit has its own set of configurable house policies (e.g., no smoking, no pets, no durians). |
| HS-10 | When a new unit is created, the system copies a set of default policies (No Pets Allowed, No Durians, No Smoking) to it. The admin can then add, edit, or remove policies individually. |
| HS-11 | Guests see the house policies on the unit detail page in a "House Rules" section before they proceed to book. |
| HS-12 | The default policies that get copied to new units are managed at the system level and are not hardcoded. The admin can change them. |
| HS-13 | Admin can set a per-unit extension payment window (in minutes) that overrides the system-wide default. |

**Outputs:**
- Confirmation of creation, update, or deletion.
- A validation error if the admin tries to delete a unit with future bookings.
- A public listing of active units showing images, price, location, and average rating.
- A detail page per unit with description, gallery, calendar, and policies.

---

#### 2.2.3 Booking Module

This module covers the full booking lifecycle: date selection, availability checking, booking creation, confirmation, cancellation, and refund processing. Every booking belongs to a specific homestay unit.

**Inputs:**
- Guest picks check-in and check-out dates from a unit's availability calendar.
- Guest submits a booking form.
- Guest requests a cancellation.
- Admin views the calendar, creates bookings (own or on behalf), edits, deletes, or cancels bookings.
- Admin filters the booking list by status, booking ID, or date range.
- Admin blocks dates.
- A scheduled job checks for bookings whose payment deadline has passed.

**Processing:**

*Guest side:*

| ID | Requirement |
|---|---|
| BK-U-01 | Guests can view a calendar for each unit showing which dates are available and which are taken. |
| BK-U-02 | Guests can pick a check-in and check-out date and time. |
| BK-U-03 | The system checks availability in real time as the guest selects dates. |
| BK-U-04 | If the selected date is unavailable, the system shows the message "The selected date has been booked! Please choose another date" and disables the submit button. |
| BK-U-05 | If the date is available and the guest has filled in all required fields, the submit button is enabled. |
| BK-U-06 | Submitting a booking generates a unique booking number (e.g., `BK-20260303-001`). |
| BK-U-07 | Guests can view their current bookings, booking history, and individual booking details. |
| BK-U-08 | Guests can cancel their own bookings. The applicable refund policy is shown before they confirm. |

*Admin side:*

| ID | Requirement |
|---|---|
| BK-A-01 | Admin can view a calendar that shows booked, temporarily blocked, and available dates for each unit. |
| BK-A-02 | Admin can create a booking on behalf of a registered guest. |
| BK-A-03 | Admin can create a booking for themselves. |
| BK-A-04 | Admin can view, edit, and delete any booking. |
| BK-A-05 | Admin can cancel a booking on behalf of a guest. |
| BK-A-06 | Admin can filter the booking list by status, booking ID, and date range. |
| BK-A-07 | Admin can block specific dates to prevent bookings. Guests see only that the date is blocked, not the reason. |

*Cancellation policy:*

| When the guest cancels | Refund |
|---|---|
| Less than 3 days before the booking | No refund (0%) |
| 1 week before the booking | 25% |
| 2 weeks or more before the booking | 50% |

Refunds are processed within 3 to 5 business days. The refund percentages are configurable by the admin in System Settings.

*Payment hold and auto-cancellation:*

| ID | Requirement |
|---|---|
| BK-H-01 | When a booking is submitted, the selected date is temporarily held for 1 day (configurable) while the guest makes payment. |
| BK-H-02 | If payment is received within that window, the booking status changes to "confirmed". |
| BK-H-03 | If payment is not received, a scheduled job automatically cancels the booking and reopens the date. |

**Outputs:**
- Availability status for the selected dates.
- Booking confirmation with the generated booking number, or a validation error.
- An updated booking list.
- A cancellation confirmation showing the refund amount and timeline.
- An auto-cancellation notification for unpaid bookings.

---

#### 2.2.4 Payment Module

This module handles payments through an external gateway, generates bills and payment numbers, issues receipts, and tracks payment history.

**Inputs:**
- Guest clicks the pay button from the booking detail page.
- The payment gateway sends a webhook after payment succeeds or fails.
- Guest views payment history with optional filters.
- Admin views billing and payment lists with optional filters.
- Admin regenerates a bill or receipt.

**Processing:**

*Guest side:*

| ID | Requirement |
|---|---|
| PAY-U-01 | Guests can pay through the configured payment gateway. The gateway calculates the total (including deposit). |
| PAY-U-02 | Guests can view the bill generated for their booking. |
| PAY-U-03 | After a successful payment, guests can view and download a PDF receipt. |
| PAY-U-04 | Guests can view their payment history and filter it by date range and payment status. |
| PAY-U-05 | Guests receive in-app and email notifications when a payment succeeds or fails, triggered by the webhook. |

*Admin side:*

| ID | Requirement |
|---|---|
| PAY-A-01 | The system generates a unique bill number (e.g., `BILL-20260303-001`) and a unique payment number (e.g., `PAY-20260303-001`) for each transaction. |
| PAY-A-02 | Admin can view the billing list and filter by bill ID and date range. |
| PAY-A-03 | Admin can view the payment list and filter by payment ID, date range, and status. |
| PAY-A-04 | Admin can regenerate a bill for a booking. |
| PAY-A-05 | Admin can regenerate a receipt for a completed payment. |

**Outputs:**
- Redirect to the payment gateway with the bill amount.
- A success or failure page after returning from the gateway.
- Booking status updated to "confirmed" and bill marked "paid" when the webhook arrives.
- Downloadable PDF receipt.
- Filtered billing and payment lists.

---

#### 2.2.5 Notification Module

This module sends in-app and email notifications for system events, reminders, and links bookings to Google Calendar.

**Inputs:**
- System events such as booking creation, payment confirmation or failure, approaching check-in dates, and pending payment deadlines.
- Admin toggles email notifications on or off in System Settings.
- User or admin authenticates with Google Calendar.

**Processing:**

| ID | Requirement |
|---|---|
| NOTIF-01 | Users and admins receive in-app notifications for system events. These are stored in Laravel's database notification table. |
| NOTIF-02 | Users and admins receive email notifications for the same events, sent via SMTP. |
| NOTIF-03 | Guests with pending payments receive a daily reminder from a scheduled job. |
| NOTIF-04 | Both guests and admins get reminders before upcoming check-ins and check-outs. |
| NOTIF-05 | Admins receive QR code reminders tied to check-in and check-out events. |
| NOTIF-06 | Bookings sync to Google Calendar. Calendar events are created, updated, and deleted as bookings change. Both guests and admins can see these events. |
| NOTIF-07 | Admin can turn email notifications on or off globally through System Settings. |

**Outputs:**
- A badge count on the notification bell and a dropdown list of recent notifications.
- Emails delivered to registered addresses.
- Google Calendar events that reflect the booking schedule.

---

#### 2.2.6 Chat Module

This module provides real-time messaging between guests and the admin over WebSocket.

**Inputs:**
- A guest or admin types and sends a chat message.
- A guest or admin opens the chat interface.

**Processing:**

| ID | Requirement |
|---|---|
| CHAT-01 | Guests can send messages to the admin. |
| CHAT-02 | Admins can receive messages and reply. |
| CHAT-03 | Chat runs on WebSocket (Laravel Reverb). Messages are saved to the `chat_messages` table. Each guest has one conversation thread with the admin (one row in `chat_conversations`). |

**Outputs:**
- Messages appear in real time in the chat interface.
- Message history is stored and can be retrieved later.
- Unread badges appear on conversations with new messages.
- A typing indicator shows when the other party is composing a message.

---

#### 2.2.7 User Management Module

Admin-only module for managing user accounts.

**Inputs:**
- Admin fills out a user creation form.
- Admin edits user information.
- Admin deletes a user.
- Admin resets a user's password (via email link or by setting the default password).
- Admin activates or deactivates a user.

**Processing:**

| ID | Requirement |
|---|---|
| USR-01 | Admin can create accounts. New accounts get the "User" role by default. |
| USR-02 | Admin can edit user information. |
| USR-03 | Admin can delete accounts (soft delete, the record is archived rather than erased). |
| USR-04 | Admin can reset a password in two ways: send a reset link to the user's email, or set the password to the default value (`Abc@123`). |
| USR-05 | After a password reset, the `must_change_password` flag is set. The user must choose a new password at their next login. |
| USR-06 | Admin can activate or deactivate accounts. Deactivated users cannot log in. |

**Outputs:**
- Confirmation of account creation, update, deletion, or status change.
- A reset email sent to the user, or a confirmation that the password was set to the default.
- The user list is updated to reflect the change.

---

#### 2.2.8 Role and Permission Module

Admin-only module for managing roles and permissions.

**Inputs:**
- Admin creates, edits, or deletes a role.
- Admin assigns or removes permissions on a role.
- Admin creates, edits, or deletes a permission.

**Processing:**

| ID | Requirement |
|---|---|
| ROLE-01 | Admin can create, edit, and delete roles. |
| ROLE-02 | Admin can assign one or more permissions to a role. |
| ROLE-03 | The system prevents deletion of a role that is currently assigned to at least one user. A warning is displayed. |
| PERM-01 | Admin can create, edit, and delete permissions. |
| PERM-02 | The system prevents deletion of a permission that is attached to at least one role. A warning is displayed. |

The system ships with two default roles:

| Role | Description |
|---|---|
| Admin | Full access to every module and all settings. |
| User | Guest-level access limited to booking, payment, notification, chat, and feedback. |

**Outputs:**
- Confirmation of the creation, update, or deletion.
- An error message if the admin tries to delete a role or permission that is still in use.
- Updated role and permission lists.

---

#### 2.2.9 System Settings Module

Admin-only module for configuring all operational parameters. Every setting is stored in the `settings` database table and applied at runtime. No values are hardcoded.

**Inputs:**
- Admin updates SMTP settings.
- Admin updates security settings (lockout duration, session timeout, max login attempts).
- Admin updates payment and refund parameters.
- Admin toggles email notifications.
- Admin updates extension charge rates (per hour, per night).
- Admin updates the default extension payment window.
- Admin manages default house policies.
- Admin updates general settings (system name, logo).

**Processing:**

*SMTP:*

| ID | Requirement |
|---|---|
| SET-SMTP-01 | Admin can set SMTP host, port, username, password, and encryption type for outgoing emails. |

*Security:*

| ID | Requirement |
|---|---|
| SET-SEC-01 | Admin can set the lockout duration in minutes. |
| SET-SEC-02 | Admin can set the session timeout in minutes. |
| SET-SEC-03 | Admin can set how many failed login attempts it takes before lockout. |

*Payment and refund:*

| ID | Requirement |
|---|---|
| SET-GEN-02 | Admin can set refund percentages for the 3-day, 1-week, and 2-week cancellation windows. |
| SET-GEN-03 | Admin can set payment and billing options, including the payment hold duration (default 24 hours). |

*Extension charges:*

| ID | Requirement |
|---|---|
| SET-EXT-01 | Admin can set the extra charge per hour for time extensions. |
| SET-EXT-02 | Admin can set the extra charge per night for date extensions. |
| SET-EXT-03 | All extension charge values are stored in the database and applied dynamically. |
| SET-EXT-04 | Admin can set the system-wide default extension payment window in minutes (default: 60 minutes). |

*Default house policies:*

| ID | Requirement |
|---|---|
| SET-POL-01 | Admin can change the default policies that are copied to every new homestay unit. |
| SET-POL-02 | The initial defaults are No Pets Allowed, No Durians, and No Smoking. The admin can modify these. |

*General:*

| ID | Requirement |
|---|---|
| SET-GEN-01 | Admin can update general settings like the system name and logo. |
| SET-GEN-04 | Admin can turn email notifications on or off across the whole system. |

**Outputs:**
- Confirmation that settings were saved.
- Changes take effect immediately without restarting the application.
- Validation errors for invalid input.

---

#### 2.2.10 Audit Logs Module

Admin-only module that records a complete, read-only audit trail of everything that happens in the system.

**Inputs:**
- Any create, update, or delete action by a user or the system triggers a log entry automatically.
- Admin opens the audit log page.
- Admin filters the log.

**Processing:**

| ID | Requirement |
|---|---|
| AUDIT-01 | Admin can view the full audit trail. |
| AUDIT-02 | The log captures user actions, admin changes, authentication events (login, logout, failed attempts, lockouts), and system events (auto-cancellation, QR regeneration). |
| AUDIT-03 | Log entries are read-only. No one, including admins, can edit or delete them. |

Each entry records:
- When it happened (timestamp).
- Who did it (the `causer`).
- What they did (action type: create, update, delete, login, etc.).
- What was affected (model type and ID).
- What changed (before/after values stored as JSON).

**Outputs:**
- A paginated, filterable table of audit entries.
- Each entry shows the timestamp, user, action, target, and details of what changed.

---

#### 2.2.11 QR Code Door Access Module

This module generates, invalidates, and regenerates QR codes for physical door access. It also handles booking extensions that change the QR code's validity period.

**Inputs:**
- A booking is confirmed (triggered by payment).
- Check-out time arrives (triggered by a scheduled job).
- Admin marks housekeeping as complete.
- Admin starts an extension request.
- Guest pays the extension charge.
- The extension payment deadline passes without payment (triggered by a scheduled job).

**Processing:**

| ID | Requirement |
|---|---|
| QR-01 | The system generates a unique QR code with a secure token for each confirmed booking. |
| QR-02 | The QR code is valid from check-in (default 3:00 PM) to check-out (default 12:00 PM). |
| QR-03 | After check-out time passes, the QR code is automatically invalidated and a new one is generated. |
| QR-04 | Admin can regenerate a QR code for housekeeping (the code gets tagged with purpose `housekeeping`). |
| QR-05 | When housekeeping is marked complete, the system generates a new QR code for the next guest (purpose `guest`). |
| QR-06 | Every booking gets a unique, distinct QR code token. |
| QR-07 | Admin can start a booking extension when a guest asks to stay longer (extend the check-out time or date). |
| QR-08 | Before approving, the system checks that the extended period does not conflict with another booking on the same unit. |
| QR-09 | If the admin approves, the system creates an extension record with status `pending_payment`, generates an additional bill, and sets a payment deadline based on the unit's extension payment window. |
| QR-10 | The extra charge is calculated from configurable rates: charge per hour for time extensions, charge per night for date extensions. |
| QR-11 | The guest must pay within the extension payment window (per unit default, or the system-wide default of 60 minutes). |
| QR-12 | The QR code is not extended when the extension is requested. The validity is only updated after the guest pays. |
| QR-13 | If the guest does not pay in time, the extension is cancelled automatically. The booking reverts to the original check-out date and time, and the guest is notified. |
| QR-14 | After the extension payment is confirmed, the booking dates are updated and the QR code's `valid_until` timestamp is extended to match the new check-out. |

**Outputs:**
- QR code shown on the guest's booking detail page.
- QR code regenerated for housekeeping or for the next guest.
- Extension bill created and payment notification sent to the guest.
- Booking dates and QR code updated after extension payment.
- Automatic revert and notification if the extension payment was not made.

---

#### 2.2.12 Reporting and Analytics Module

Admin-only module that presents operational data on a dashboard and allows report export.

**Inputs:**
- Admin opens the Reports and Analytics page.
- Admin applies filters (date range, homestay unit, payment status).
- Admin switches between daily, weekly, and monthly trend views.
- Admin clicks the export button for PDF or CSV.

**Processing:**

| ID | Requirement |
|---|---|
| RPT-01 | Admin can view an analytics dashboard with summary metrics. |
| RPT-02 | The dashboard displays total bookings, total revenue, occupancy rate, and cancellation rate. |
| RPT-03 | Booking trends are shown as a line chart with daily, weekly, and monthly toggles. |
| RPT-04 | Revenue reports can be filtered by date range, homestay unit, and payment status. |
| RPT-05 | A bar chart breaks down bookings by homestay unit. |
| RPT-06 | The dashboard includes a summary of guest feedback and average ratings per unit. |
| RPT-07 | Admin can export reports as PDF or CSV files. |

**Outputs:**
- A dashboard with summary cards and interactive charts.
- Filtered report views.
- Downloadable PDF or CSV files.

---

#### 2.2.13 Guest Feedback Module

Guests leave ratings and reviews after their stay. Admins can respond and moderate.

**Inputs:**
- Guest goes to a completed booking and submits a star rating (1 to 5) and optional written feedback.
- Admin views feedback for a unit.
- Admin replies to a review.
- Admin hides or flags a review.

**Processing:**

*Guest side:*

| ID | Requirement |
|---|---|
| FB-U-01 | Guests can submit a 1-to-5 star rating and a written review for a homestay unit after their stay is completed (booking status: `completed`). |
| FB-U-02 | Feedback can only be submitted for completed bookings. |
| FB-U-03 | One feedback entry per booking. |
| FB-U-04 | Guests can view the feedback they have submitted previously. |

*Admin side:*

| ID | Requirement |
|---|---|
| FB-A-01 | Admin can view all feedback and ratings for each unit. |
| FB-A-02 | Admin can reply to a guest's feedback. The reply is stored in `admin_reply`. |
| FB-A-03 | Admin can hide feedback that violates content guidelines by toggling `is_visible`. |
| FB-A-04 | The average rating for each unit is calculated from visible feedback and shown on the public listing page. |

**Outputs:**
- Confirmation that the feedback was submitted.
- The average star rating appears on the unit listing page.
- Admin replies are visible alongside the guest's review.
- Hidden feedback is removed from public view.

---

*— End of System Documentation —*
