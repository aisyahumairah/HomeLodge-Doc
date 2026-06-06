# Chapter 2: Specific Requirements (SRS) — HomeLodge Booking Homestay System

This chapter lists all software requirements for HomeLodge at a level of detail sufficient for design, implementation, and testing. Each requirement is externally perceivable by a user, an operator, or an external system. Requirements are organised by interface type and then by functional module.

---

## 2.1 External Interface Requirements

### 2.1.1 User Interfaces

HomeLodge has two separate interfaces — one for guests and one for administrators.

**A. Guest (User) Interface**

The guest side uses a top navigation bar layout:

- A fixed top navbar shows the system logo, navigation links (Home, My Bookings, Notifications), and a user avatar dropdown for profile and logout access.
- The content area is full width but capped at 1200 px and centred.
- A footer sits at the bottom.
- The navbar stays visible on scroll.

The following table describes the main screens a guest interacts with:

| Screen / Feature | Description |
| :--- | :--- |
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
| :--- | :--- |
| Dashboard | Cards showing total bookings, revenue, occupancy rate, and cancellation rate. Below the cards are booking trend charts (with daily/weekly/monthly toggles), revenue charts, and a feedback summary. |
| Booking Calendar | A month-view calendar where dates are colour-coded: green for available, red for booked, grey for admin-blocked, amber for pending payment. Clicking a date opens the booking detail or a creation form. |
| Booking Management | A filterable table (status, booking ID, date range) with buttons for view, edit, cancel, and delete on each row. |
| Payment / Billing Lists | Tabular views of bills and payments. Both can be filtered by ID, date range, and status. Bills and receipts can be regenerated from here. |
| User Management | A table of all users with status indicators, role labels, and buttons for edit, delete, activate/deactivate, and password reset. |
| Roles and Permissions | List and detail views for roles and permissions. The system warns the admin if they try to delete a role or permission that is still in use. |
| System Settings | A grouped settings page covering SMTP, Security, Payment, Notification, Extension Charges, and Default Policies. Changes are saved to the database immediately. |
| Audit Logs | A read-only table of audit entries showing timestamp, user, action, affected record, and change details. The admin can filter but not modify entries. |
| QR Code Management | Lists QR codes per booking with their status, validity period, and controls for regeneration and housekeeping workflow. |
| Chat | A split-panel view with conversations on the left and the active chat on the right. Unread badges appear on conversations with new messages. |

**General UI Conventions:**

- The typeface is **Inter** (Google Fonts) across all screens.
- Spacing follows an **8 px base grid**.
- Status badges are pill-shaped and colour-coded: green for Confirmed, amber for Pending Payment, red for Cancelled, grey for Blocked, blue for Completed, teal for Extended.
- Buttons darken or gain a shadow on hover. Disabled buttons are dimmed to half opacity. Buttons in a loading state show a spinner.
- Form labels are placed above input fields. Validation errors appear below the field in red. Required fields have an asterisk.
- Tables use alternating row shading, sortable column headers, pagination, and icon buttons with tooltips for row actions.
- Toast notifications appear at the top right and disappear after 5 seconds.
- Confirmation modals for destructive actions (delete, cancel) use a red confirm button.
- The system is designed to meet **WCAG 2.1 Level AA** accessibility standards, with a minimum 4.5 : 1 colour contrast ratio for body text, full keyboard navigation, and ARIA labels on icon-only buttons.

**Logical Interface Characteristics**

The system separates its two interfaces entirely by role. When a guest logs in, the application loads the top-navbar layout and only the guest-facing pages are reachable. When an admin logs in, the sidebar-based back-office layout loads instead. A guest cannot reach an admin page by editing the URL; the middleware checks the user's role on every request and returns a 403 Forbidden response if the role does not match the route. The reverse is also true — an admin accessing a guest-only page is redirected to the admin dashboard.

Both interfaces follow a standard request–response model. The user performs an action (clicking a button, submitting a form, picking a date on the calendar), the server processes it, and the browser either updates the same page or redirects to another. Chat and notifications are the two exceptions; these use a persistent WebSocket connection so that new messages and alerts appear without the user having to refresh.

Session behaviour is the same for both roles. After a period of inactivity (the duration is set by the admin in System Settings), the session expires and the user is sent back to the login page. If the user had a form open with unsaved data at that point, that data is lost. The system does not attempt to recover unsaved form input after a session timeout.

**Screen Format and Layout Requirements**

| Characteristic | Guest Interface | Admin Interface |
| :--- | :--- | :--- |
| Layout model | Top navbar, full-width content area (max 1200 px, centred), footer | Fixed left sidebar, top header bar, fluid content area |
| Minimum supported screen width | 320 px (mobile phones) | 768 px (tablets); sidebar collapses to icons below this width |
| Grid system | 8 px base grid for spacing and component alignment | 8 px base grid for spacing and component alignment |
| Typography | Inter (Google Fonts), base size 16 px, line height 1.5 | Inter (Google Fonts), base size 14 px for tables and dense data views, 16 px for body text |
| Colour usage | Light background with the primary brand colour on call-to-action buttons and links | Light background with a neutral sidebar; same brand colour for accents and action buttons |

Reports generated by the system (bills, receipts, analytics exports) use an A4 page format when exported to PDF. Each PDF includes a header with the system name and logo, the document title, a generation timestamp, and the report body in tabular or summary form. CSV exports contain raw column headers and data rows with no visual formatting.

The menu structures are fixed per role and cannot be rearranged by the user:

- **Guest navbar:** Home, My Bookings, Notifications (a bell icon with an unread count badge), and a user avatar dropdown leading to Profile and Logout.
- **Admin sidebar:** Dashboard, Homestays, Bookings, Payments, QR Access, Guests, Users, Access Control (Roles, Permissions), Communication (Chat, Notifications), and System (Settings, Audit Logs).

There are no programmable function keys. Every action in the system is performed through on-screen buttons, links, and form controls.

**Interface Optimisation**

- Form fields use appropriate input controls rather than free text wherever possible. Dates are selected from a date picker, times from a time picker, and statuses from dropdowns.
- Destructive actions always require a second confirmation step via a modal dialog with a red-coloured confirm button.
- The booking calendar communicates availability through colour fills so that an admin or guest can scan an entire month at a glance.
- Error messages appear directly below the input field that caused them. If the first error is off-screen, the page scrolls to it automatically. Each error message states what went wrong and what to do.
- Success and failure feedback appears as toast notifications at the top right corner (auto-dismiss after 5 seconds).
- On the admin dashboard, summary cards load first while charts render in the background.
- Long tables paginate at 10 rows by default with sortable column headers and icon-button row actions with tooltips.
- On mobile devices, all touch targets are at least 44 × 44 pixels.

**Usability Requirements**

| ID | Requirement |
| :--- | :--- |
| UI-USE-01 | A guest who has never used the system before can register an account, browse the unit listings, and submit a booking (up to the payment gateway redirect) within 10 minutes, with no prior training or written instruction. |
| UI-USE-02 | A returning guest can log in and reach their booking history page within 30 seconds. |
| UI-USE-03 | An administrator who has received 1 hour of hands-on training can perform any single CRUD operation on homestay units, bookings, or user accounts within 3 minutes. |
| UI-USE-04 | Every validation error message states the specific problem and tells the user how to fix it. The system never shows a bare "Validation failed" or "Error" notice without additional context. |
| UI-USE-05 | The system displays short error messages by default. Where a longer explanation is available, a "More details" link expands the full message inline. |
| UI-USE-06 | All interactive elements (buttons, links, form inputs, dropdowns) are reachable using the Tab key and activated with the Enter or Space key. No feature is accessible only by mouse. |
| UI-USE-07 | Every page loads its initial visible content within 3 seconds on a standard broadband connection of 10 Mbps or faster. |
| UI-USE-08 | Colour is never the only way to communicate information. Every status badge pairs its colour with a text label, and every chart includes a legend or data labels. |
| UI-USE-09 | Confirmation dialogs for irreversible actions require the user to click a clearly labelled button inside the modal. Pressing Enter without clicking does not trigger the destructive action. |
| UI-USE-10 | A session timeout warning appears on screen 2 minutes before the session expires, giving the user an opportunity to extend it by clicking a "Stay Logged In" button. |

---

### 2.1.2 Hardware Interfaces

HomeLodge interfaces with one category of hardware:

**QR Code Smart Lock**

| Characteristic | Detail |
| :--- | :--- |
| Device Type | A QR code-compatible smart lock mounted on the door of each homestay unit. |
| Supported Devices | Cloud-connected smart locks that accept smartphone-scanned QR codes (e.g., QRLock, TTlock, or equivalent). |
| How It Works | HomeLodge generates a unique, encrypted, time-limited token for each booking and encodes it as a QR code. The guest scans the code with their phone camera or a companion app. The smart lock checks the token against the backend (or a synchronised local store) and unlocks the door if the token is valid. |
| Data Exchanged | The QR code contains a secure token (stored in the `qr_codes.token` column) validated against the database, including the validity window (`valid_from` to `valid_until`). |
| Validity Window | The code is active from check-in (default 3:00 PM) to check-out (default 12:00 PM). After check-out, the code expires and the system regenerates one for housekeeping or for the next guest. |
| Admin Controls | The admin can regenerate a QR code at any time for housekeeping. Once housekeeping is marked complete, a fresh code is generated for the next booking. |
| Vendor Integration | The smart lock vendor's SDK or API must be integrated to validate tokens at the door. The specific vendor has not been finalised. |

**Client Hardware:**

No special hardware is needed on the guest's side. HomeLodge is accessed through a web browser on any desktop, laptop, tablet, or smartphone that meets these minimum requirements:

- A modern browser (Chrome, Firefox, Safari, or Edge — latest two major versions).
- An internet connection that can handle HTTPS and WebSocket traffic.
- A screen width of 320 px or more (the layout is responsive).

---

### 2.1.3 Software Interfaces

HomeLodge depends on the following external software:

**A. Backend Framework and Runtime**

| Software | Version | What It Does |
| :--- | :--- | :--- |
| Laravel | 11.x | PHP web framework handling routing, controllers, models, authentication, queuing, and task scheduling. |
| PHP | 8.2+ | Server-side language. |
| Composer | Latest | PHP dependency manager. |

**B. Database**

| Software | Version | What It Does |
| :--- | :--- | :--- |
| MySQL | 8.x | Primary relational database. |
| MariaDB | 10.x | Supported alternative to MySQL. |
| Eloquent ORM | Built-in (Laravel) | Object-relational mapping layer. All queries go through Eloquent models and migrations. |

**C. Frontend**

| Software | Version | What It Does |
| :--- | :--- | :--- |
| Laravel Blade | Built-in (Laravel) | Server-side template engine for HTML rendering. |
| Alpine.js | Latest | Lightweight JavaScript framework for client-side interactions like form validation and UI toggles. |
| Vite | Latest | Build tool for asset compilation, bundling, and hot module replacement. |
| Bootstrap 5 / Custom CSS | 5.x | CSS framework plus a custom design system for consistent styling. |
| Chart.js | Latest | JavaScript charting library used in the analytics dashboard for trend lines, bar charts, and pie charts. |
| Font Awesome / Bootstrap Icons | Latest | Icon libraries used throughout the interface. |

**D. Authentication and Authorisation Packages**

| Package | Identifier | What It Does | How the System Uses It |
| :--- | :--- | :--- | :--- |
| Laravel Breeze / Sanctum | `laravel/breeze` or `laravel/sanctum` | Authentication scaffolding (login, registration, password reset, email verification). | Provides session-based authentication with CSRF protection. Includes middleware guards for route protection. |
| Laravel Socialite | `laravel/socialite` | Google OAuth 2.0 for SSO. | Connects to Google's OAuth endpoints, exchanges tokens, and retrieves the user's profile (name, email, Google ID). |
| Spatie Laravel Permission | `spatie/laravel-permission` | RBAC management. | Creates the `roles`, `permissions`, and pivot tables. Adds Eloquent traits for assigning roles and permissions to users. Includes middleware for route-level access checks. |

**E. Operational Packages**

| Package | Identifier | What It Does | How the System Uses It |
| :--- | :--- | :--- | :--- |
| Spatie Laravel Activity Log | `spatie/laravel-activitylog` | Audit logging. | Hooks into Eloquent model events (create, update, delete) and writes entries to the `activity_log` table with before/after state in JSON. |
| SimpleSoftwareIO QR Code | `simplesoftwareio/simple-qrcode` | QR code generation. | Generates QR codes as SVG or PNG images rendered in Blade views. Encodes the booking's unique, secure token. |
| Barryvdh Laravel DomPDF | `barryvdh/laravel-dompdf` | PDF generation. | Converts Blade views into downloadable PDF files for bills, receipts, and reports. |
| Maatwebsite Excel | `maatwebsite/excel` | CSV and Excel export. | Produces downloadable spreadsheets from query results. Used in the reporting module. |

**F. Real-Time Communication**

| Package | Identifier | What It Does | How the System Uses It |
| :--- | :--- | :--- | :--- |
| Laravel Reverb | `laravel/reverb` | WebSocket server. | Self-hosted server implementing the Pusher protocol. Broadcasts events to connected browser clients in real time. |
| Laravel Echo + `pusher-js` | Client-side | WebSocket client. | Subscribes to broadcast channels (chat, notifications) and handles incoming messages in the browser. |

If hosting Laravel Reverb turns out to be too complex, the team may switch to Pusher, a hosted alternative.

**G. Payment Gateway**

| Item | Detail |
| :--- | :--- |
| Provider | To be determined. Candidates include Billplz (Malaysia), Stripe, and Toyyibpay. |
| Integration Approach | Server-side API calls plus webhook callbacks. |
| How It Works | The system sends a request to the gateway API with the bill amount and booking reference. The gateway returns a URL and the guest is redirected there to pay. After payment, the gateway sends an HTTPS POST to a dedicated webhook route in the Laravel app. The handler verifies the gateway's signature, processes the payment (using the `gateway_reference` field to prevent duplicates), and updates the booking and bill status. |

**H. Calendar Integration**

| Item | Detail |
| :--- | :--- |
| Service | Google Calendar API v3 |
| Authentication | Google OAuth 2.0 (through `laravel/socialite`), scope `https://www.googleapis.com/auth/calendar.events` |
| How It Works | The system creates, updates, and deletes Google Calendar events that correspond to bookings. Events include check-in/check-out dates, times, and the homestay unit name. |

**I. Caching and Queue Infrastructure**

| Software | Version | What It Does |
| :--- | :--- | :--- |
| Redis | Latest | In-memory store for caching, session data, and queue jobs (used as the Laravel queue driver). |

---

### 2.1.4 Communication Interfaces

The system uses the following protocols:

**A. HTTPS**

| Item | Detail |
| :--- | :--- |
| Protocol | HTTPS (TLS 1.2 or higher) |
| Usage | All traffic between the browser and the server is encrypted: page loads, form submissions, API calls, file downloads. |
| Certificate | Provisioned through Let's Encrypt (Certbot) or an equivalent certificate authority. |
| Enforcement | HTTP requests are redirected to HTTPS. Every route requires HTTPS. |

**B. WebSocket**

| Item | Detail |
| :--- | :--- |
| Protocol | WebSocket (RFC 6455) over TLS (wss://) |
| Usage | Provides the real-time channel for the chat module and live notification delivery. |
| Implementation | Laravel Reverb runs the WebSocket server, implementing the Pusher protocol. On the client side, Laravel Echo manages channel subscriptions and event handling. |
| Channel Security | Private channels are used for user-specific conversations and notifications. Subscriptions are authenticated through Laravel's broadcasting auth endpoint. |

**C. SMTP**

| Item | Detail |
| :--- | :--- |
| Protocol | SMTP with TLS or SSL encryption |
| Usage | Sends all outgoing emails: registration confirmation, password resets, payment notifications, booking reminders, and general alerts. |
| Configuration | The admin configures SMTP settings (host, port, username, password, encryption) through the System Settings page. The values are stored in the `settings` table. Email notifications can be enabled or disabled globally. |

**D. Webhook (HTTP POST)**

| Item | Detail |
| :--- | :--- |
| Protocol | HTTPS POST |
| Usage | The payment gateway sends POST requests to a dedicated HomeLodge endpoint whenever a payment status changes (success, failure, or refund). |
| Security | The system validates each webhook using the gateway's signature verification to confirm authenticity and prevent replays. |
| Duplicate Handling | Processing is idempotent. If the gateway sends the same webhook twice for the same payment, only one record is created. |

**E. Google Calendar API**

| Item | Detail |
| :--- | :--- |
| Protocol | REST over HTTPS |
| Usage | The system creates, updates, and deletes calendar events in the user's Google Calendar to mirror their bookings. |
| Authentication | OAuth 2.0 tokens obtained during the Google SSO login. |
| Data Format | JSON payloads following the Google Calendar API v3 specification. |

**F. Internal Server Communication**

| Path | Detail |
| :--- | :--- |
| Web Server → Application | Nginx or Apache proxies requests to PHP-FPM over FastCGI. |
| Application → Database | Laravel connects to MySQL/MariaDB on port 3306 over the local network. |
| Application → Redis | Laravel connects to Redis on port 6379 for caching, sessions, and queues. |

---

## 2.2 System Features

The system features include the following 12 functional modules. For every module, the document states the inputs the system receives, the processing it performs, and the outputs it produces.

> **Note:** Use case diagrams, activity diagrams, domain model class diagrams, and state machine diagrams should be included here. Refer to `/docs/dev/USE_CASE_DIAGRAMS.md` and `/docs/dev/USE_CASE_DESCRIPTIONS.md` for the complete set. Placeholder references are noted below; replace with actual diagram images.

---

### 2.2.1 Authentication Module

This module handles registration, login, logout, password management, profile updates, and account security for both guests and admins. It is the front door of the system — every user must go through it before they can do anything else.

**Use Cases:** UC-AUTH-01, UC-AUTH-02, UC-AUTH-03, UC-AUTH-04, UC-AUTH-05, UC-AUTH-06

**Inputs:**
- A registration form (full name, email, and password) or a Google SSO request.
- Login credentials (email and password) or a Google SSO button click.
- A logout action.
- A forgot-password form with the registered email.
- A new password form (with confirmation) from a reset link.
- A change-password form with current password, new password, and confirmation.
- Updated profile information (name, phone number, avatar image).
- A forced password change form after an admin-initiated reset.

**Processing:**

| ID | Requirement |
| :--- | :--- |
| AUTH-01 | Guests can register using email/password. New accounts are assigned the "Guest" role by default. The system validates email format, uniqueness, and password strength (8–12 characters with at least one uppercase, one lowercase, one digit, and one special character). |
| AUTH-02 | If a person signs in with Google SSO for the first time and has no existing HomeLodge account, the system creates one automatically with the "Guest" role. If an account with the same email already exists, the Google identity is linked to it. |
| AUTH-03 | Registered users and admins log in with email/password or Google SSO. The system verifies credentials against the stored bcrypt hash or validates the Google OAuth token, then checks the account is active and not locked. |
| AUTH-04 | After login, the system checks whether the user is required to change their password. If so, the user is redirected to the forced password change page and all other pages are blocked until the change is completed. |
| AUTH-05 | Logging out terminates the active session and clears any "remember me" tokens. Subsequent browser back-navigation shows the login page rather than protected content. |
| AUTH-06 | The forgot-password flow sends a time-limited reset link (default validity: 60 minutes) to the registered email. The link is single-use; the system marks it as used after the password is reset. If the account was locked, the lock is cleared after the reset. |
| AUTH-07 | Passwords must be 8 to 12 characters long and include at least one uppercase letter, one lowercase letter, one digit, and one special character. The password field has a show/hide toggle and a real-time indicator showing which criteria are met. |
| AUTH-08 | After exceeding the maximum number of failed login attempts (configurable in System Settings; default: 5), the account is temporarily locked for the configured lockout duration (default: 30 minutes). |
| AUTH-09 | Locked accounts unlock automatically once the lockout duration has passed, or immediately when the user completes the forgot-password flow or an admin resets the password. |
| AUTH-10 | Users and admins can view and update their own profile (name, phone number, avatar). They can also change their own password by entering the current password followed by the new password and confirmation. |
| AUTH-11 | When an admin resets a user's password, the system flags the account to require a forced password change at next login. The user must set a new personal password before they can access any other page. The new password cannot be the same as the temporary one. |

**Outputs:**
- A registration confirmation message and redirect to the login page, or a field-level validation error.
- A redirect to the appropriate dashboard (guest or admin) on successful login, or a generic error message without revealing which field is wrong.
- A redirect to the login page after logout.
- A password reset email with a time-limited link (the same generic message is shown regardless of whether the email exists, to protect privacy).
- A confirmation that the password was changed, or a list of unmet strength criteria.
- A confirmation that the profile was updated.
- A redirect to the forced password change page if the flag is active; all other pages are inaccessible until this is done.

---

### 2.2.2 Homestay Management Module

This module lets administrators manage multiple homestay properties and lets guests browse available units. Management operations are admin-only; guests only have read access on the public side.

**Use Cases:** UC-HS-01, UC-HS-02, UC-HS-03, UC-HS-04, UC-HS-05, UC-HS-06

**Inputs:**
- Admin submits a form with unit name, description, location, images, base price per night, deposit, and default check-in/check-out times.
- Admin optionally sets a per-unit extension payment window (overrides the system-wide default).
- Admin edits an existing unit's details, policies, or images.
- Admin deactivates or soft-deletes a unit.
- Admin adds, edits, or removes house policies on a specific unit.
- Guest opens the homestay listing page.
- Guest clicks on a unit card to view its detail page.

**Processing:**

| ID | Requirement |
| :--- | :--- |
| HS-01 | Guests can browse all active units on the listing page. Each unit is displayed as a card showing its name, main photo, base price per night, location, and average guest rating (calculated from visible reviews only). |
| HS-02 | Guests can view the full detail page of a selected unit, including: all photos in a gallery, full description, location, base price, deposit amount, check-in/check-out times, house rules, average rating with individual guest reviews, and a real-time availability calendar. |
| HS-03 | The availability calendar on the unit detail page shows dates colour-coded as: available (can be booked), booked (reserved by another guest), temporarily held (another guest is in the process of paying), or blocked (made unavailable by admin). |
| HS-04 | Admin can create a homestay unit by supplying its name, description, location, base price, deposit, check-in/check-out times, and photos. |
| HS-05 | When a new unit is created, the system automatically copies all active default house rules (e.g., No Pets, No Durians, No Smoking) to the new unit's rules list. |
| HS-06 | Admin can optionally set a per-unit extension payment window (in minutes) at creation time. This overrides the system-wide default for that unit only. |
| HS-07 | Admin can edit any existing unit's details (name, description, pricing, times, photos, house rules, extension payment window). Changes take effect immediately on the guest-facing pages. |
| HS-08 | Admin can deactivate or soft-delete a unit. A unit with confirmed future bookings cannot be deactivated or deleted — the system blocks the action and lists the conflicting bookings. |
| HS-09 | Deactivated units are hidden from the guest listing page. No new bookings can be made for them. All historical data is retained. |
| HS-10 | Admin can view a complete list of all units (active and inactive), including each unit's status, number of upcoming confirmed bookings, base price, and action buttons. |
| HS-11 | Availability is tracked per unit, so different units can be booked for the same dates without conflict. |
| HS-12 | Each unit has its own set of house rules. The admin can add, edit, or remove rules on a per-unit basis after creation. |
| HS-13 | The default house rules copied to new units are managed at the system level (see Section 2.2.9) and are not hardcoded. |

**Outputs:**
- A public listing of active units, each showing its name, main photo, location, base price, and average rating.
- A unit detail page with a full gallery, description, pricing, house rules, availability calendar, and guest reviews.
- Confirmation of unit creation, update, or deactivation; an audit log entry is recorded for each.
- A validation error listing conflicting bookings if the admin tries to deactivate or delete a unit with confirmed future bookings.

---

### 2.2.3 Booking Module

This module covers the full booking lifecycle: date selection, availability checking, booking creation, confirmation, cancellation, and refund processing. Every booking belongs to a specific homestay unit.

**Use Cases:** UC-BK-01, UC-BK-02, UC-BK-03, UC-BK-04, UC-BK-05, UC-BK-06, UC-BK-07

**Inputs:**
- Guest views the availability calendar for a unit and selects check-in and check-out dates.
- Guest reviews the booking summary and submits the booking.
- Guest requests a cancellation from the booking detail page.
- Admin creates a booking on behalf of a guest or for themselves.
- Admin edits or deletes an existing booking.
- Admin cancels a booking on behalf of a guest.
- Admin blocks a date range on a unit (with an optional internal note).
- Admin filters the booking list by status, booking reference, unit, or date range.
- A scheduled job runs at regular intervals to check for overdue unpaid bookings.

**Processing:**

*Availability and Date Selection (UC-BK-01):*

| ID | Requirement |
| :--- | :--- |
| BK-01 | The availability calendar displays dates in four colour-coded states: available, booked (reserved by another guest), temporarily held (another guest is mid-payment), and blocked (admin-restricted). |
| BK-02 | The system checks availability in real time when the guest selects a date range. If any date in the range is unavailable, the system highlights the conflict and asks the guest to re-select. |
| BK-03 | The check-out date must be after the check-in date. Selecting a check-out before check-in triggers a validation message. |
| BK-04 | On valid date selection, the booking summary updates with the total number of nights and estimated cost (nightly rate × nights + deposit). |

*Booking Submission (UC-BK-02):*

| ID | Requirement |
| :--- | :--- |
| BK-05 | When the guest confirms a booking, the system performs a second availability check to prevent two guests submitting for the same dates simultaneously. |
| BK-06 | A successful submission creates a booking in "awaiting payment" status with a one-day payment deadline (configurable in System Settings). |
| BK-07 | The system automatically generates a unique booking number (e.g., `BK-20260303-001`) and a bill with a unique bill number. |
| BK-08 | The guest receives an in-app and email notification containing the bill and payment deadline. |

*Viewing Bookings (UC-BK-03, UC-BK-04):*

| ID | Requirement |
| :--- | :--- |
| BK-09 | Guests can view all their bookings in two sections: "Current" (active and upcoming) and "History" (completed and cancelled). Each entry shows unit name, dates, status, and total cost. |
| BK-10 | Guests can open a booking detail page showing: unit name and photo, check-in/check-out date and time, total amount, payment status, booking status, applicable cancellation policy and estimated refund, and the QR access code (for confirmed bookings). |
| BK-11 | Admins can view all bookings system-wide, with filtering by status, date range, unit, or booking reference, and can switch between a list view and the all-unit booking calendar view. |

*Cancellation (UC-BK-05):*

| ID | Requirement |
| :--- | :--- |
| BK-12 | Guests and admins can cancel a booking that is in "awaiting payment" or "confirmed" status and has not yet been checked in. |
| BK-13 | Before cancellation is confirmed, the system calculates and displays the applicable refund amount based on the tiered cancellation policy (configured in System Settings). |
| BK-14 | If the booking was in "awaiting payment" status (no payment made), no refund is calculated. The booking is simply cancelled. |
| BK-15 | After cancellation, the booking status is set to "cancelled," the previously reserved dates are released, a cancellation notification is sent to the guest and admin, and an audit log entry is recorded. |

*Cancellation Policy (configurable in System Settings):*

| Days Before Check-In | Refund |
| :--- | :--- |
| More than 14 days | 100 % |
| 7 to 14 days | 50 % |
| Less than 7 days | 0 % |

Refunds are processed through the payment service. The refund tiers and percentages are configurable by the admin in System Settings.

*Admin Booking Management (UC-BK-06):*

| ID | Requirement |
| :--- | :--- |
| BK-16 | Admin can create a booking on behalf of any registered guest (for walk-in or phone reservations) or for themselves. The system checks availability in real time and generates a bill; the guest is notified to pay. |
| BK-17 | Admin can edit any booking's details (dates, times). If dates are changed, the system checks availability for the new range. The guest is notified of changes. |
| BK-18 | Admin can delete a booking record. This action is permanent; the system requires confirmation, releases the dates, notifies the guest, and records an audit log entry. |
| BK-19 | Admin can block a date range on a unit with an optional internal note (reason). Blocked dates appear as unavailable to guests; the internal reason is not shown to guests. Blocking is prevented if confirmed bookings already exist in the range. |

*Auto-Cancellation (UC-BK-07):*

| ID | Requirement |
| :--- | :--- |
| BK-20 | A scheduled job runs approximately every hour to find all bookings in "awaiting payment" status whose one-day payment deadline has passed. |
| BK-21 | For each overdue booking: the status is set to "cancelled," the temporarily reserved dates are released, a cancellation notification is sent to the guest (in-app and email), and an audit log entry is recorded. |

**Outputs:**
- Real-time availability status as the guest selects dates; booking summary with nights count and estimated cost.
- A booking confirmation with the generated booking number and bill, or a conflict error if dates became unavailable at submission time.
- A guest booking list (current and history) and a booking detail page with full information including the QR code for confirmed bookings.
- A cancellation confirmation dialog showing the refund amount; a cancellation notification sent to guest and admin after confirmation.
- An auto-cancellation notification for unpaid bookings whose deadline has passed.

---

### 2.2.4 Payment Module

This module handles payments through an external gateway, generates bills and payment numbers, issues receipts, and tracks payment history.

**Use Cases:** UC-PAY-01, UC-PAY-02, UC-PAY-03

**Inputs:**
- Guest clicks "Pay Now" from the booking detail page or the bill notification.
- The payment gateway sends an HTTPS webhook after a payment succeeds or fails.
- Guest views their payment and billing history.
- Admin views billing and payment lists with optional filters.
- Admin regenerates a bill or receipt.

**Processing:**

*Making a Payment (UC-PAY-01):*

| ID | Requirement |
| :--- | :--- |
| PAY-01 | Guests pay through the configured online payment service. The system creates a payment request and redirects the guest to the secure payment page hosted by the gateway. |
| PAY-02 | After the guest completes payment, the payment service sends a signed webhook notification to HomeLodge. The system verifies the webhook's signature before processing. |
| PAY-03 | On confirmed successful payment: the payment is recorded, the booking status is updated to "confirmed," a QR access code is generated for the guest (see Section 2.2.11), and a PDF receipt is created. |
| PAY-04 | The system sends a booking confirmation notification (in-app and email) to the guest containing the receipt and the QR code. The admin is also notified of the new confirmed booking. |
| PAY-05 | If the payment is declined or fails, the payment is recorded as failed. The guest is returned to HomeLodge with an error message and a "Try Again" option. The booking remains in "awaiting payment" status. |
| PAY-06 | Webhook processing is idempotent. If the gateway sends a duplicate confirmation, the system detects it and ignores the duplicate without creating a second payment record. |
| PAY-07 | If a webhook notification cannot be verified as genuine, the system rejects it and records a security alert. |

*Viewing Records (UC-PAY-02):*

| ID | Requirement |
| :--- | :--- |
| PAY-08 | Guests can view their payment history showing: payment number, booking reference, date, amount, and status. Guests can view or download the itemised bill (nightly rate, number of nights, deposit, total) and the receipt for completed payments. |
| PAY-09 | The system generates a unique bill number (e.g., `BILL-20260303-001`) and a unique payment number (e.g., `PAY-20260303-001`) for each transaction. |
| PAY-10 | Admins can view the billing list (all bills) and the payment list (all transactions) system-wide. Each entry shows: reference number, guest name, unit, amount, status, and date. Admins can filter by date range, reference number, and status. |

*Regenerating Documents (UC-PAY-03):*

| ID | Requirement |
| :--- | :--- |
| PAY-11 | Admin can regenerate a bill for any booking. The new document reflects the latest booking and billing data. |
| PAY-12 | Admin can regenerate a receipt for any completed payment. Optionally, the regenerated document can be resent to the guest by email. |

**Outputs:**
- Redirect to the payment gateway's secure payment page.
- A success confirmation page (with receipt and QR code) or a failure page with a "Try Again" option, shown after returning from the gateway.
- Booking status updated to "confirmed," bill marked "paid," and QR code generated when the payment webhook is verified.
- Downloadable PDF receipt accessible from the booking detail page.
- Filtered billing and payment lists for both guests and admins.

---

### 2.2.5 Notification Module

This module ensures that everyone stays informed about important events — from booking confirmations to payment reminders and check-in alerts. Notifications are delivered both within the app and by email. Confirmed bookings can also be synced to the user's Google Calendar.

**Use Cases:** UC-NOTIF-01, UC-NOTIF-02

**Inputs:**
- System events: booking created, booking confirmed, payment received or failed, booking cancelled (by user or auto-cancellation), extension charge issued, extension confirmed or cancelled, QR code generated.
- Scheduled daily jobs for payment deadline reminders and check-in/check-out reminders.
- Admin toggles the global email notification setting in System Settings.
- User connects (or has connected) their Google account via OAuth.

**Processing:**

*In-App and Email Notifications (UC-NOTIF-01):*

| ID | Requirement |
| :--- | :--- |
| NOTIF-01 | In-app notifications are stored in the database and delivered in real time via WebSocket to online users. The bell icon badge count updates without requiring a page refresh. |
| NOTIF-02 | Clicking a notification marks it as read and navigates the user to the relevant page (e.g., the booking detail). The notification panel shows read and unread entries with the newest first. |
| NOTIF-03 | Email notifications are sent for the same events as in-app notifications, provided the global email toggle is enabled. If email is disabled globally, only in-app notifications are delivered. |
| NOTIF-04 | A scheduled daily job finds bookings in "awaiting payment" status where the payment deadline is approaching and sends a reminder to the guest with the booking reference, amount due, deadline, and a direct payment link. |
| NOTIF-05 | A scheduled daily job finds confirmed bookings with check-in or check-out dates within the reminder window (e.g., 1 day before) and sends reminders to both the guest and the admin. |
| NOTIF-06 | Admins receive QR code reminders tied to upcoming check-in and check-out events. |
| NOTIF-07 | Admin can enable or disable email notifications globally through System Settings. When disabled, all outgoing emails are suppressed; in-app notifications continue normally. |

*Google Calendar Integration (UC-NOTIF-02):*

| ID | Requirement |
| :--- | :--- |
| NOTIF-08 | When a booking is confirmed, the system creates a Google Calendar event for any user (guest or admin) who has connected their Google account. The event spans from check-in to check-out and includes the unit name and booking reference in the description. |
| NOTIF-09 | If the booking dates are updated (e.g., after a date extension), the system updates the corresponding Google Calendar event. If a booking is cancelled, the event is deleted. |
| NOTIF-10 | If a user has not connected their Google Calendar, the system skips the calendar step silently. No error is shown to the user. |

**Outputs:**
- A real-time badge count on the notification bell and a dropdown panel of recent notifications (read and unread).
- Emails delivered to registered addresses for all key system events (when enabled).
- Automated reminder emails for pending payments and upcoming check-ins/check-outs.
- Google Calendar events created, updated, or deleted to mirror the booking schedule.

---

### 2.2.6 Chat Module

This module provides a built-in messaging system so that guests and administrators can communicate directly within HomeLodge. Messages are delivered instantly and all conversations are saved for future reference.

**Use Cases:** UC-CHAT-01, UC-CHAT-02

**Inputs:**
- A guest or admin types a message in the chat input and clicks "Send" (or presses Enter).
- A guest or admin opens the chat page to view the conversation history.

**Processing:**

| ID | Requirement |
| :--- | :--- |
| CHAT-01 | Guests can send messages to the admin. Admins can receive messages from any guest and reply. Each guest has exactly one conversation thread with the admin. |
| CHAT-02 | The system saves every message with the sender's identity, recipient's identity, message content, and timestamp. |
| CHAT-03 | Chat is delivered over WebSocket (Laravel Reverb). Messages appear in the recipient's chat window in real time if they are online. |
| CHAT-04 | If the recipient is not online, the message is stored and visible the next time they sign in. An unread count badge is shown on the chat icon. |
| CHAT-05 | The send button is disabled when the input field is blank. Empty messages cannot be sent. |
| CHAT-06 | When a real-time connection is lost, the interface shows a "Reconnecting…" indicator. Messages are still persisted and visible on the next page load. |
| CHAT-07 | On the chat page, messages are displayed in chronological order (oldest to newest). Messages sent by the current user are shown on the right; received messages on the left. Each message shows the sender's name and timestamp. |
| CHAT-08 | When a user opens the chat page, all unread messages in that conversation are marked as read. |

**Outputs:**
- Messages delivered in real time to the recipient's chat window if they are online.
- Full conversation history available to both parties on the chat page.
- Unread badges on the chat icon or conversation list for new messages.
- A typing indicator showing when the other party is composing a message.

---

### 2.2.7 User Management Module

Admin-only module for managing user accounts, roles, and permissions. It controls who can access the system and what they can do within it.

**Use Cases:** UC-USR-01, UC-USR-02, UC-USR-03, UC-USR-04, UC-USR-05

**Inputs:**
- Admin fills out a user creation form (name, email, role assignment).
- Admin edits user information (name, email, phone, role).
- Admin toggles a user's active/inactive status.
- Admin initiates a password reset for a user.
- Admin creates, edits, or deletes a role and assigns/removes permissions.
- Admin creates, edits, or deletes an individual permission.

**Processing:**

*User Account Management (UC-USR-01, UC-USR-02, UC-USR-03):*

| ID | Requirement |
| :--- | :--- |
| USR-01 | Admin can manually create a new user account by providing the person's name, email, and role. The system generates a temporary password for the account. |
| USR-02 | The new account is immediately flagged to require a forced password change on first sign-in. The user receives an email with the temporary password and a link to sign in. |
| USR-03 | Admin can edit user information (name, email, phone number, role assignment). If the role is changed, the new permissions take effect immediately. |
| USR-04 | Admin can deactivate a user account. Deactivation prevents the user from signing in and immediately ends any active sessions for that user. |
| USR-05 | Admin can reactivate a deactivated account, restoring the user's ability to sign in. |
| USR-06 | Admin can soft-delete a user account. The record is archived rather than permanently erased, preserving referential integrity. |
| USR-07 | Admin can reset a user's password in two ways: send a password reset link to the user's registered email, or immediately set the password to the system default temporary value. |
| USR-08 | After an admin-initiated password reset, the system flags the account to require a forced password change at next sign-in. If the account was locked, the lock is removed and the failed sign-in counter is reset. |

*Role Management (UC-USR-04):*

| ID | Requirement |
| :--- | :--- |
| ROLE-01 | Admin can create a role by providing a name and optional description. Role names must be unique. |
| ROLE-02 | Admin can edit a role's name or description. |
| ROLE-03 | Admin can assign or remove permissions on a role via a permission checklist. Changes take effect immediately for all users with that role. |
| ROLE-04 | Admin can delete a role only if no users currently have it assigned. If users still hold the role, the system blocks deletion and shows the count of affected users. |

*Permission Management (UC-USR-05):*

| ID | Requirement |
| :--- | :--- |
| PERM-01 | Admin can create a permission by providing a name and optional description. Permission names must be unique. |
| PERM-02 | Admin can edit a permission's name or description. |
| PERM-03 | Admin can delete a permission only if it is not currently attached to any role. If it is, the system blocks deletion and shows how many roles use it. |

The system ships with two default roles:

| Role | Description |
| :--- | :--- |
| Admin | Full access to every module and all settings. |
| Guest | Access limited to booking, payment, notification, chat, and feedback modules. |

**Outputs:**
- Confirmation of account creation (with a notification email to the new user), update, deactivation, or deletion; an audit log entry is recorded for each.
- A password reset email sent to the user, or a confirmation that the temporary password was applied; the user is notified in-app and by email.
- Confirmation of role or permission creation, update, or deletion; an audit log entry is recorded.
- An error message if the admin tries to delete a role that is still assigned to users, or a permission that is still attached to roles.
- Updated user, role, and permission lists reflecting all changes.

---

### 2.2.8 System Settings Module

Admin-only module for configuring all system-wide operational parameters. Every setting is stored in the `settings` database table and applied at runtime. No values are hardcoded. Changes made here affect the entire system.

**Use Cases:** UC-SET-01, UC-SET-02

**Inputs:**
- Admin updates email (SMTP) settings and optionally tests the connection.
- Admin updates security parameters (max login attempts, lockout duration, session timeout).
- Admin updates tiered cancellation and refund policy settings.
- Admin updates payment and billing options (payment service credentials, bill prefix, initial booking payment window).
- Admin updates extension charge rates (per hour, per night).
- Admin updates the system-wide default extension payment window.
- Admin toggles the global email notification switch.
- Admin updates general settings (system name, logo).
- Admin adds, edits, or removes default house rules.

**Processing:**

*Email (Outgoing Mail):*

| ID | Requirement |
| :--- | :--- |
| SET-SMTP-01 | Admin can set the mail server address, port, username, password, encryption type, and sender name/address for outgoing emails. |
| SET-SMTP-02 | Admin can optionally test the email connection before saving. If the test fails, the system shows the error and allows correction without overwriting saved settings. |

*Security:*

| ID | Requirement |
| :--- | :--- |
| SET-SEC-01 | Admin can set the maximum number of failed sign-in attempts before account lockout (default: 5). |
| SET-SEC-02 | Admin can set the lockout duration in minutes (default: 30). |
| SET-SEC-03 | Admin can set the session timeout in minutes (default: 120). |

*Cancellation and Refund Policy:*

| ID | Requirement |
| :--- | :--- |
| SET-REF-01 | Admin can configure tiered refund rules. Each tier specifies a days-before-check-in threshold and the corresponding refund percentage (e.g., >14 days: 100%; 7–14 days: 50%; <7 days: 0%). |
| SET-REF-02 | The system validates that refund tiers do not overlap and that all percentages are between 0 and 100. |

*Payment and Billing:*

| ID | Requirement |
| :--- | :--- |
| SET-PAY-01 | Admin can configure payment service credentials, the bill number format/prefix, and the initial booking payment window (default: 1 day). |

*Extension Charges:*

| ID | Requirement |
| :--- | :--- |
| SET-EXT-01 | Admin can set the extra charge rate per hour for same-day time extensions. |
| SET-EXT-02 | Admin can set the extra charge rate per night for overnight date extensions. |
| SET-EXT-03 | Admin can set the system-wide default extension payment window in minutes (default: 60 minutes). Individual units can override this value. |

*Email Notifications:*

| ID | Requirement |
| :--- | :--- |
| SET-NOTIF-01 | Admin can globally enable or disable email notifications. When disabled, all outgoing emails are suppressed; in-app notifications continue normally. |

*General:*

| ID | Requirement |
| :--- | :--- |
| SET-GEN-01 | Admin can update general settings such as the system name and logo. |

*Default House Policies (UC-SET-02):*

| ID | Requirement |
| :--- | :--- |
| SET-POL-01 | Admin can add new default house rules, edit existing ones, or remove them from the default list. |
| SET-POL-02 | The initial pre-loaded defaults are: No Pets Allowed, No Durians, and No Smoking. The admin can modify these at any time. |
| SET-POL-03 | Changes to the default rules affect only new homestay units created after the change. Existing units retain their current rules and are not retroactively updated. |

**Outputs:**
- A confirmation message when settings are saved. Changes take effect immediately across the system without restarting the application.
- A validation error for any invalid input (e.g., negative numbers, overlapping refund tiers, percentages outside 0–100).
- An email test result (success or error message) when the admin tests the SMTP connection.

---

### 2.2.9 Audit Logs Module

Admin-only module that records a complete, permanent, tamper-proof audit trail of everything that happens in the system. This provides accountability, transparency, and helps the administrator track who did what and when.

**Use Cases:** UC-AUDIT-01, UC-AUDIT-02

**Inputs:**
- Any significant action by a user, admin, or the system automatically triggers a log entry (no manual input required).
- Admin opens the audit log page.
- Admin applies one or more filter criteria (date range, event type, user name).

**Processing:**

*Viewing and Filtering (UC-AUDIT-01):*

| ID | Requirement |
| :--- | :--- |
| AUDIT-01 | Admin can view the complete audit trail in reverse chronological order (newest first), paginated for readability. |
| AUDIT-02 | Admin can filter the log by date range, event type, and user name. Filters can be cleared to return to the full log. |
| AUDIT-03 | Log entries are read-only. No one, including admins, can edit or delete any entry. |

*Automatic Event Logging (UC-AUDIT-02):*

| ID | Requirement |
| :--- | :--- |
| AUDIT-04 | The system automatically records a log entry for every significant event: user actions (sign-in, sign-out, booking submission, payment, feedback), admin actions (managing users, changing settings, editing bookings), and system actions (auto-cancelling expired bookings, expiring QR codes, processing webhooks). |
| AUDIT-05 | System-initiated events are recorded with the actor shown as "System" rather than a user name. |

Each entry records:
- Timestamp (when it happened).
- Actor (user name or "System" for automated actions).
- Event type (sign-in, create, update, delete, cancel, etc.).
- Affected record (model type and ID, e.g., Booking #BK-20260303-001).
- Change details (before/after values stored as JSON where applicable).

**Outputs:**
- A paginated, filterable, read-only table of audit entries.
- Each entry displays the timestamp, actor, event type, affected record, and change details.

---

### 2.2.10 QR Code and Access Module

This module manages the digital QR codes that allow guests physical access to their booked homestay units. QR codes are automatically generated when a booking is confirmed and automatically expired at check-out. The module also handles the housekeeping transition between guests and booking extensions (when a guest wants to stay longer).

**Use Cases:** UC-QR-01, UC-QR-02, UC-QR-03, UC-QR-04, UC-QR-05

**Inputs:**
- A booking status changes to "confirmed" (triggered by successful payment).
- A scheduled job runs and finds QR codes whose `valid_until` time has passed.
- Admin generates a housekeeping QR code and sets its validity period.
- Admin marks housekeeping as complete.
- Admin initiates a booking extension (selects extension type and new check-out date/time).
- Guest pays the extension charge through the payment service.
- A scheduled job runs and finds extension records whose payment deadline has passed.

**Processing:**

*Guest QR Code — Receive and Use (UC-QR-01):*

| ID | Requirement |
| :--- | :--- |
| QR-01 | When a booking is confirmed, the system automatically generates a unique QR code with a secure, encrypted token. The code is valid from the check-in date/time to the check-out date/time. |
| QR-02 | The QR code is delivered to the guest via an in-app notification and by email. It is also accessible at any time from the booking detail page. |
| QR-03 | The guest presents the QR code to the smart lock scanner at the homestay door. The lock checks the token and grants access only if the code is active and the current time is within the valid window. |
| QR-04 | If the QR code is expired, inactive, or revoked, access is denied and the lock displays the appropriate status message. |

*Housekeeping Cycle (UC-QR-02):*

| ID | Requirement |
| :--- | :--- |
| QR-05 | A scheduled job runs regularly around the time of check-outs, finds all active QR codes whose `valid_until` time has passed, sets them to "expired," and updates the corresponding booking status to "completed." |
| QR-06 | Admin can generate a temporary housekeeping QR code for a unit by setting a validity window (e.g., valid for 4 hours). The code is tagged with `housekeeping` type and displayed for the admin to share with cleaning staff. |
| QR-07 | When admin marks housekeeping as complete, the housekeeping QR code is expired. If a next confirmed booking exists for the unit, the system automatically generates a new guest QR code and delivers it to the next guest via in-app notification and email. |
| QR-08 | If there is no next confirmed booking, the system skips QR code generation. No action is required from the admin. |

*Booking Extension — Initiate (UC-QR-03):*

| ID | Requirement |
| :--- | :--- |
| QR-09 | Admin can initiate a booking extension for an active confirmed booking. The extension type is either a time extension (later check-out on the same day) or a date extension (additional overnight nights). |
| QR-10 | Before processing, the system checks that the extended period does not conflict with another confirmed booking on the same unit. If a conflict exists, the system shows the conflicting booking details and blocks the extension. |
| QR-11 | The additional charge is calculated from configurable rates: (hours extended) × hourly rate for time extensions; (nights added) × nightly rate for date extensions. |
| QR-12 | The system creates an extension record in "awaiting payment" status, generates an extension bill, and sets the payment deadline based on the unit's extension payment window (or the system-wide default of 60 minutes if the unit has no custom setting). |
| QR-13 | The guest is notified with the extension charge amount, payment deadline, and a direct link to pay. The QR code validity is NOT updated at this point. |

*Booking Extension — Pay (UC-QR-04):*

| ID | Requirement |
| :--- | :--- |
| QR-14 | The guest pays the extension charge through the online payment service before the deadline. |
| QR-15 | On confirmed payment: the extension status is set to "confirmed," the booking's check-out date and time are updated to the new extended values, and the QR code's `valid_until` timestamp is extended to match. |
| QR-16 | The guest receives a confirmation notification: "Your stay has been extended. Your QR code is now valid until [new check-out date/time]." An audit log entry is recorded. |

*Extension Auto-Cancellation (UC-QR-05):*

| ID | Requirement |
| :--- | :--- |
| QR-17 | A scheduled job runs every few minutes to find extension records in "awaiting payment" status whose payment deadline has passed. |
| QR-18 | For each overdue extension: the extension status is set to "cancelled," the booking's check-out date and time are reverted to the original values (stored at the time the extension was created), and the QR code is not modified (it already reflects the original check-out time). |
| QR-19 | The guest is notified that the extension was cancelled because payment was not received, and informed of the original check-out date and time. An audit log entry is recorded. |

**Outputs:**
- QR code delivered to the guest via in-app notification and email upon booking confirmation; accessible from the booking detail page at any time.
- A temporary housekeeping QR code displayed for the admin to share with cleaning staff.
- A new guest QR code generated and delivered to the next guest after housekeeping is marked complete.
- An extension bill generated and a payment notification sent to the guest.
- Booking dates and QR code `valid_until` updated after extension payment is confirmed.
- Automatic revert to original check-out time and a notification to the guest if the extension payment deadline passes without payment.

---

### 2.2.11 Reporting and Analytics Module

Admin-only module that presents operational data on a dashboard and allows detailed report viewing and export. It helps with decision-making by showing trends in bookings, revenue, and guest satisfaction.

**Use Cases:** UC-RPT-01, UC-RPT-02, UC-RPT-03

**Inputs:**
- Admin opens the Reporting and Analytics section.
- Admin applies filters (date range, homestay unit, payment status).
- Admin toggles the booking trends chart between daily, weekly, and monthly views.
- Admin clicks "Export PDF" or "Export CSV" on a report page.

**Processing:**

*Analytics Dashboard (UC-RPT-01):*

| ID | Requirement |
| :--- | :--- |
| RPT-01 | Admin can view a summary dashboard displaying key performance indicators: total bookings (current month and year-to-date), total revenue (current month and year-to-date), occupancy rate (percentage of available dates that were booked), cancellation rate (percentage of bookings cancelled), and average guest rating (from all visible reviews). |
| RPT-02 | The dashboard displays a booking trends line chart showing volumes over time; the admin can toggle between daily, weekly, and monthly views. |
| RPT-03 | The dashboard displays a per-unit booking breakdown bar chart comparing booking counts, occupancy, and revenue across different units. |
| RPT-04 | The dashboard includes a guest feedback and rating summary per unit, showing average score, review count, and rating distribution. |
| RPT-05 | Admin can filter all dashboard data by date range or specific unit. |

*Revenue Report (UC-RPT-02):*

| ID | Requirement |
| :--- | :--- |
| RPT-06 | Admin can view a detailed revenue report filtered by date range, homestay unit, and payment status. The report shows total revenue, an itemised breakdown by booking, and a summary by unit. |

*Report Export (UC-RPT-03):*

| ID | Requirement |
| :--- | :--- |
| RPT-07 | Admin can export any report view (revenue, booking breakdown, feedback summary) as a PDF document or a CSV spreadsheet file for offline use, sharing, or archiving. |
| RPT-08 | PDF exports use an A4 page format with a header showing the system name, document title, and generation timestamp. CSV exports contain raw column headers and data rows with no formatting. |

**Outputs:**
- A dashboard with live summary metric cards and interactive charts (booking trends, per-unit breakdown, feedback summary).
- A filtered revenue report view with itemised and unit-level breakdowns.
- A PDF or CSV file automatically downloaded to the admin's device.

---

### 2.2.12 Guest Feedback Module

Guests leave star ratings and written reviews after completing a stay. Admins can respond to reviews, moderate inappropriate content, and the system automatically displays the calculated average rating on public-facing unit pages.

**Use Cases:** UC-FB-01, UC-FB-02, UC-FB-03, UC-FB-04

**Inputs:**
- Guest navigates to a completed booking and submits a star rating (1–5, required) and an optional written comment.
- Guest views their previously submitted reviews.
- Admin views all reviews system-wide with optional filters.
- Admin replies to a review.
- Admin hides or restores a review.

**Processing:**

*Submit Feedback (UC-FB-01):*

| ID | Requirement |
| :--- | :--- |
| FB-01 | Guests can submit a 1-to-5 star rating (required) and an optional written comment for a homestay unit. Submission is only possible for bookings in "completed" status. |
| FB-02 | Only one feedback entry is allowed per booking. Once submitted, the review cannot be edited by the guest. The "Leave a Review" button is replaced with "View Your Review" after submission. |
| FB-03 | On submission, the system saves the review (linked to the booking, unit, and guest, with visibility set to "visible") and recalculates the unit's average rating. |

*View Own Feedback (UC-FB-02):*

| ID | Requirement |
| :--- | :--- |
| FB-04 | Guests can view all reviews they have previously submitted across their completed bookings. Each entry shows the unit name, stay dates, star rating, comment, submission date, and any admin reply. Reviews are read-only. |

*Admin Feedback Management (UC-FB-03):*

| ID | Requirement |
| :--- | :--- |
| FB-05 | Admin can view all guest reviews system-wide (including hidden ones), with filtering by unit or visibility status. Each entry shows: guest name, unit, booking reference, rating, comment, date, visibility status, and admin reply status. |
| FB-06 | Admin can reply to any review. The reply is saved and displayed alongside the guest's review on the unit detail page. If an existing reply is edited, the reply date is updated. |
| FB-07 | Admin can hide a review with inappropriate content. The system asks for confirmation before hiding. Hidden reviews are removed from the public unit detail page and excluded from the average rating calculation. |
| FB-08 | Admin can restore a hidden review. The system sets it back to "visible" and recalculates the unit's average rating. |

*Display Average Rating (UC-FB-04):*

| ID | Requirement |
| :--- | :--- |
| FB-09 | The system automatically calculates and displays the average star rating for each unit on both the listing page and the unit detail page. Only visible (non-hidden) reviews are included in the calculation. |
| FB-10 | The display format is: "X.X ★ (N reviews)." If a unit has no visible reviews, the system displays "No reviews yet." |
| FB-11 | The average rating updates automatically whenever a new review is submitted, or an existing review is hidden or restored by the admin. |

**Outputs:**
- A thank-you confirmation message after feedback is submitted; the unit's average rating is recalculated immediately.
- A read-only list of the guest's own reviews, including any admin replies.
- An admin feedback management table with all reviews (visible and hidden) and moderation controls.
- An updated average rating and review count displayed on the unit listing card and unit detail page.

---

## 2.3 Performance and Other Requirements

| ID | Requirement |
| :--- | :--- |
| PERF-01 | Every page must load its initial visible content within 3 seconds on a standard 10 Mbps broadband connection (ref. UI-USE-07). |
| PERF-02 | The system must support at least 100 concurrent users without degradation of response time below the 3-second threshold. |
| PERF-03 | WebSocket messages (chat and notifications) must be delivered within 1 second of the triggering event under normal load. |
| PERF-04 | Scheduled jobs (auto-cancellation, QR regeneration, payment reminders) must execute within their defined intervals without requiring manual intervention. |
| PERF-05 | PDF and CSV report exports must complete within 10 seconds for datasets of up to 10,000 records. |
| QUAL-01 | **Maintainability:** The codebase follows Laravel conventions (MVC pattern, Eloquent ORM, service classes) so that a new developer familiar with Laravel can become productive within one week. |
| QUAL-02 | **Reliability:** The system must achieve 99.5 % uptime per month, excluding scheduled maintenance windows. |
| QUAL-03 | **Portability:** The system runs on any server supporting PHP 8.2+, MySQL 8.x / MariaDB 10.x, Redis, and a standard web server (Nginx or Apache). |
| QUAL-04 | **Reusability:** Common functionality (notifications, PDF generation, QR code generation) is implemented as reusable service classes. |

---

## 2.4 Design Constraints

| Constraint | Description |
| :--- | :--- |
| Technology Stack | The system must be built using Laravel 11.x (PHP 8.2+), MySQL 8.x / MariaDB 10.x, Redis, Blade + Alpine.js + Bootstrap 5, and Vite. The choice was mandated by the project sponsor and the team's existing expertise. |
| IEEE Standards | The SD follows the structure recommended by IEEE Std 830-1998 (SRS), IEEE Std 1016-1998 (SDD), and IEEE Std 829-2008 (STD), adapted for the SECJ2203 course at UTM School of Computing. |
| Browser Support | The system must support the latest two major versions of Chrome, Firefox, Safari, and Edge. Internet Explorer is not supported. |
| Hosting | The system is self-hosted; no cloud-managed services (e.g., AWS Lambda, Google App Engine) are used in the initial release. |
| Single Codebase | Guest and admin interfaces share a single Laravel application. Separation is achieved through middleware-based route guards, not separate deployments. |

---

## 2.5 Software System Attributes

| Attribute | Requirement |
| :--- | :--- |
| **Security** | All communication is encrypted via HTTPS (TLS 1.2+). Passwords are hashed with bcrypt. CSRF tokens protect every form submission. RBAC controls access at the route level. Account lockout prevents brute-force attacks. Audit logs provide a complete, tamper-proof trail. |
| **Usability** | The system meets WCAG 2.1 Level AA (4.5 : 1 contrast ratio, full keyboard navigation, ARIA labels). Usability requirements UI-USE-01 through UI-USE-10 are defined in Section 2.1.1. |
| **Availability** | Target uptime is 99.5 % per month. Scheduled maintenance must be communicated at least 24 hours in advance. |
| **Scalability** | The system architecture supports horizontal scaling of the web server layer and Redis caching to handle increased load. |
| **Data Integrity** | All database operations use transactions where multiple tables are affected. Webhook processing is idempotent to prevent duplicate records. Soft deletes are used for user accounts to preserve referential integrity. |

---

*— End of Chapter 2: Specific Requirements —*
