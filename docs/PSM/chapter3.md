# CHAPTER 3: METHODOLOGY

## 3.1 Introduction

This chapter describes how HomeLodge was developed. It justifies the selection of a hybrid methodology that applies the Waterfall model to planning and analysis, and the Agile model to implementation and testing. Within the Agile phase, the Scrum framework structures sprint cycles while a Kanban Board tracks individual tasks. The chapter then walks through each phase of the methodology, lists the hardware and software used during development, describes the technologies in the system, and closes with the full input and output specifications for all modules.

---

## 3.2 Justification of Methodology Selection

Section 2.5 reviewed four software development methodologies: Waterfall, Agile, Rapid Application Development (RAD), and the Iterative model. A hybrid of Waterfall and Agile was selected. The reasons follow from the constraints of this project.

The planning and analysis phases of HomeLodge need a structured, document-driven approach. All functional and non-functional requirements had to be gathered, reviewed, and documented in full before any implementation began. The system has thirteen defined modules, each with specific acceptance criteria, and those requirements were fixed before development started. Under these conditions, producing formal documentation and a complete system design before writing any code was both necessary and practical. Waterfall imposes this discipline: each phase must be completed and documented before the next begins. That constraint aligns with the academic deliverables this report must produce.

The implementation and testing phases need a different approach. Developing thirteen modules inside a single undivided implementation phase offers no way to track progress at the task level, manage build order, or spot stalled work before it disrupts the schedule. Agile addresses this by dividing development into time-boxed sprints under the Scrum framework. At the start of each sprint, a defined set of modules or features is selected. Progress is tracked on a Kanban Board that shows each task in one of three states: To Do, In Progress, or Done. Bottlenecks become visible immediately.

The hybrid model keeps Waterfall's formal documentation structure for the phases that need it, and applies Agile's iterative delivery and task-level tracking where they are needed.

---

## 3.3 Software Development Methodology

HomeLodge was developed using a hybrid methodology. Waterfall governs the planning and analysis phases; Agile governs implementation and testing. The Scrum framework structures sprint cycles during the Agile phase, and a Kanban Board tracks each task within every sprint. Figure 3.1 illustrates the overall methodology structure.

The phases are described below.

### 3.3.1 Phase 1: Requirements Analysis (Waterfall)

This phase followed the Waterfall approach. All user and system requirements for HomeLodge were collected and documented before implementation began. Two documents were produced: the User Requirements Specification (URS) and the Product Requirements Document (PRD). The requirements were derived from three sources: an examination of how small-scale homestay operators currently manage bookings, the recurring failures of that manual process, and a comparison of what existing platforms provide against what HomeLodge specifically needs.

The output was a complete requirements specification covering both user roles (Guest and Admin) across thirteen functional modules: Authentication, Homestay Management, Booking, Payment, Notification, Chat, User Management, Role and Permission Management, System Settings, Audit Logs, QR Code Door Access, Reporting and Analytics, and Guest Feedback. The outputs were reviewed and approved before Phase 2 began.

### 3.3.2 Phase 2: System Design (Waterfall)

This phase also followed Waterfall. With the requirements fixed, three design outputs were produced.

The first was the database schema. Each table was designed to support at least one functional requirement. Foreign key constraints, soft delete columns, and index definitions were all established at this stage, before any migration files were written.

The second was the use case diagram, which documents how each user role interacts with each module based on the requirements from Phase 1.

The third was the user interface design. Page layouts for the guest interface and the admin interface were sketched, covering the booking flow, the admin dashboard, and the QR code management workflow.

No module development started until the schema, use case diagram, and interface designs were finalised.

### 3.3.3 Phase 3: Implementation (Agile with Scrum and Kanban Board)

Implementation applied the Agile approach. Development was organised into sprints under the Scrum framework, with each sprint targeting a defined set of modules. Sprint planning determined which modules to develop based on data dependencies. For example, the Payment module was not scheduled until the Booking module was functional, because payment records reference confirmed booking records.

Within each sprint, a Kanban Board tracked task progress. Each task was a card on the board, moved through the following columns as work progressed:

| Column | Description |
|---|---|
| To Do | Tasks planned for the current sprint that have not yet started |
| In Progress | Tasks actively under development |
| In Review / Testing | Tasks completed and under self-review or unit testing |
| Done | Tasks that meet the acceptance criteria defined in the PRD |

A module was considered complete when it met its acceptance criteria. Its card was then moved to Done and the next sprint began.

### 3.3.4 Phase 4: Testing (Agile with Scrum and Kanban Board)

Testing ran iteratively alongside implementation and immediately after it, consistent with the Agile approach. Each module was tested against its functional and non-functional requirements as soon as it was implemented, rather than deferring all testing to the end.

Testing tasks were tracked on the Kanban Board as separate cards alongside implementation tasks. Defects were identified, fixed, and re-tested within the same sprint or the next. The areas tested included authentication flows, booking availability checks, payment integration, QR code generation and expiry, extension auto-cancellation, role-based access enforcement, and notification delivery.

The full test results are documented in Chapter 5.

### 3.3.5 Phase 5: Deployment (Waterfall)

The completed system was deployed to a server running Nginx, PHP-FPM, MySQL 8, and Redis. Queue workers were started for background job processing. The Laravel Scheduler was configured to run at one-minute intervals through the server's cron service, and SSL/TLS was enabled using Let's Encrypt. Deployment followed a sequential checklist, consistent with the Waterfall structure, since all prior phases were already complete.

---

## 3.4 Description of Technologies Used

The complete technology justification is in Section 2.6. This section lists the technologies used in HomeLodge and what each one does within the system.

### 3.4.1 Backend: Laravel 11 and PHP 8.2

Laravel is the application framework. It handles routing, controller logic, database access through Eloquent ORM, background job queuing, scheduled tasks, and email delivery. The application is written in PHP 8.2.

The following Laravel packages are used:

| Package | Role in HomeLodge |
|---|---|
| `laravel/breeze` | Authentication scaffolding (login, registration, password reset) |
| `laravel/socialite` | Google OAuth 2.0 login |
| `spatie/laravel-permission` | Role and permission management |
| `spatie/laravel-activitylog` | Immutable audit log |
| `barryvdh/laravel-dompdf` | PDF generation for bills, receipts, and report exports |
| `simplesoftwareio/simple-qrcode` | QR code generation |
| `laravel/reverb` | WebSocket server for real-time chat |
| `maatwebsite/excel` | CSV and Excel export for the reporting module |

### 3.4.2 Frontend: Blade, Alpine.js, and Bootstrap 5

Laravel Blade is the server-side templating engine. It processes view files on the server and sends complete HTML to the browser. Alpine.js adds lightweight client-side behaviour where the browser needs to hold state between user interactions: the booking date calendar, the real-time notification indicator, and the chat interface. Bootstrap 5 provides the grid layout system and pre-built UI components used across all pages. Chart.js, loaded via CDN, renders the charts on the reporting dashboard.

### 3.4.3 Database: MySQL 8 and Eloquent ORM

MySQL 8 stores all relational data. Eloquent ORM maps PHP model classes to database tables and traverses the relationships between related records (bookings to users, bookings to QR codes, bookings to payment records, and so on). Laravel Migrations manage schema changes. Laravel Seeders populate the database with initial roles, permissions, system settings, and default homestay policies.

### 3.4.4 Real-Time Communication: Laravel Reverb and Laravel Echo

Laravel Reverb runs as a self-hosted WebSocket server process alongside the Laravel application. It manages connection lifecycles and routes broadcast events to connected clients. Laravel Echo is the JavaScript library on the browser side: it opens a WebSocket connection, subscribes to the relevant broadcast channel, and passes incoming events to the chat interface.

### 3.4.5 Authentication and Access Control

Session-based authentication uses Laravel's built-in authentication guard. Google Single Sign-On is implemented through `laravel/socialite`, which handles the OAuth 2.0 redirect, token exchange, and user account lookup. The `spatie/laravel-permission` package implements role-based access control: permissions are assigned to roles, roles are assigned to users, and every route or controller action that requires a specific permission checks it through middleware.

### 3.4.6 QR Code Generation

The `simplesoftwareio/simple-qrcode` package generates QR codes locally as SVG images. No external API call is involved. Each QR code encodes a unique secure token that is valid from the guest's check-in time to their check-out time. The token is stored in the `qr_codes` table, and the Laravel Scheduler enforces its expiry.

### 3.4.7 Payment Gateway

Payments are processed through an online payment gateway via a server-side API integration. When a payment event occurs, the gateway sends a webhook callback to HomeLodge, and the application updates the booking and billing records accordingly. Webhook processing is idempotent: if the same notification arrives more than once, the system produces the same result without creating duplicate records. The specific gateway is selected at deployment based on regional availability and fee structure. Billplz, Toyyibpay, and Stripe are the shortlisted options, and all three work with the webhook-driven integration model the system uses.

### 3.4.8 Notifications

Email notifications are sent through Laravel Mail using SMTP credentials that the administrator configures in the system settings. The administrator can switch SMTP providers without modifying any application code. In-app notifications use Laravel's database notification driver: each notification is stored as a database record and loaded when the user opens any page.

### 3.4.9 Scheduled Jobs

The Laravel Scheduler runs the following background jobs automatically:

| Job | Schedule | Purpose |
|---|---|---|
| Auto-cancel unpaid bookings | Every hour | Cancel bookings where the 24-hour payment window has expired |
| Upcoming stay reminders | Daily | Notify guests and admin of check-ins within the next 24 hours |
| QR code expiry | Triggered at checkout time | Invalidate QR codes whose `valid_until` timestamp has passed |
| Payment reminders | Daily | Notify guests with pending payment |
| Extension auto-revert | Every 1–5 minutes | Check for expired extension payment deadlines and revert bookings to original dates |

---

## 3.5 System Requirements Analysis

### 3.5.1 Hardware Justification

#### 3.5.1.1 Hardware Required

The following hardware was used during development and is required for deployment.

**Development Hardware**

| Component | Specification | Purpose |
|---|---|---|
| Laptop / Desktop | Intel Core i5 or equivalent, 8 GB RAM | Running the development environment, Laragon local server, and IDE |
| Storage | 256 GB SSD minimum | Storing source code, database files, and media uploads |
| Internet Connection | Broadband, minimum 10 Mbps | Accessing package repositories, payment gateway sandbox, and cloud services |

**Deployment Hardware (Server)**

| Component | Specification | Purpose |
|---|---|---|
| Server CPU | 2 vCPU minimum | Running Nginx, PHP-FPM, Laravel queue workers, and the Reverb WebSocket process |
| Server RAM | 2 GB minimum, 4 GB recommended | Supporting concurrent requests, Redis caching, and queue processing |
| Server Storage | 20 GB SSD minimum | Database files, media uploads, application logs |
| Smart Lock Device | QR-code-compatible smart lock | Physical door access controlled by the QR code module |

The smart lock hardware is not developed as part of this project. The system generates the QR code and the token it encodes; the lock manufacturer's firmware or SDK validates the token at the door.

### 3.5.2 Software Justification

#### 3.5.2.1 Software Required

The following software is required for development and deployment.

**Development Software**

| Software | Version | Purpose |
|---|---|---|
| PHP | 8.2 | Backend runtime language |
| Composer | Latest | PHP package manager |
| Laragon | Latest | Local development environment (bundles Nginx, MySQL, and PHP for Windows) |
| MySQL | 8.x | Relational database for local development |
| Redis | 7.x | In-memory store for caching, queues, and session management |
| Node.js | 18+ | JavaScript runtime required for Vite asset compilation |
| npm | Latest | JavaScript package manager |
| Git | Latest | Version control |
| Visual Studio Code / PhpStorm | Latest | IDE |
| TablePlus / DBeaver | Latest | Database GUI client for inspecting tables during development |
| Postman | Latest | API and webhook testing |

**Deployment Software**

| Software | Version | Purpose |
|---|---|---|
| Ubuntu Server | 22.04 LTS | Operating system |
| Nginx | Latest stable | Web server |
| PHP-FPM | 8.2 | PHP process manager |
| Composer | Latest | PHP package manager (installed on server) |
| MySQL | 8.x | Production database |
| Redis | 7.x | Cache, queue driver, and session store |
| Node.js + npm | 18+ | Required to run `npm run build` during deployment |
| Let's Encrypt (Certbot) | Latest | SSL/TLS certificate provisioning |

### 3.5.3 Input Specification

This section lists every input the system accepts, organised by module. An input is any data submitted to the system through a form, a button action, or an external callback.

**Authentication Module**

| Input | Type | Source | Validation |
|---|---|---|---|
| Email address | Text | Guest / Admin registration and login form | Required, valid email format, unique on registration |
| Password | Text | Guest / Admin registration and login form | Required; 8–12 characters, at least one uppercase, one lowercase, one number, one special character |
| Google OAuth response | Redirect callback | Google OAuth 2.0 | Access token and user profile provided by Google; validated by `laravel/socialite` |
| Password reset token | URL token | Email link sent by system | Validated against hashed token stored in database; expires after use |

**Homestay Management Module**

| Input | Type | Source | Validation |
|---|---|---|---|
| Homestay name | Text | Admin form | Required, max 255 characters |
| Description | Long text | Admin form | Optional |
| Location | Text | Admin form | Required |
| Base price | Decimal | Admin form | Required, greater than zero |
| Deposit amount | Decimal | Admin form | Required, zero or positive |
| Check-in time | Time | Admin form | Required |
| Check-out time | Time | Admin form | Required |
| Images | File upload | Admin form | Optional; accepted formats: JPG, PNG, WebP; max 5 MB per file |
| House policy text | Text | Admin form | Required per policy entry |
| Extension payment window | Integer (minutes) | Admin form | Required, minimum 1 |

**Booking Module**

| Input | Type | Source | Validation |
|---|---|---|---|
| Selected homestay unit | Selection | Guest or Admin | Required; unit must be active |
| Check-in date | Date | Guest or Admin form | Required; must not be in the past |
| Check-out date | Date | Guest or Admin form | Required; must be after check-in date |
| Check-in time | Time | Guest or Admin form | Required |
| Check-out time | Time | Guest or Admin form | Required |
| Guest selection (admin only) | Selection | Admin form | Required when admin creates a booking on behalf of a user |
| Cancellation confirmation | Button action | Guest or Admin | Triggers cancellation and applies the configured refund policy |
| Blocked date range | Date range | Admin form | Required; internal reason is optional |

**Payment Module**

| Input | Type | Source | Validation |
|---|---|---|---|
| Payment initiation | Button action | Guest | Triggers redirect to payment gateway |
| Payment result webhook | HTTP POST | Payment gateway | Payload is verified against gateway signature before processing |
| Bill regeneration request | Button action | Admin | Requires a valid existing booking |

**QR Code Door Access Module**

| Input | Type | Source | Validation |
|---|---|---|---|
| Extension type | Selection (time / date) | Admin form | Required |
| Extended check-out date | Date | Admin form | Required for date extension; must not conflict with an existing booking |
| Extended check-out time | Time | Admin form | Required for time extension |
| Housekeeping complete | Button action | Admin | Triggers QR code regeneration for the next booking |
| QR code regeneration request | Button action | Admin | Operator-initiated, for housekeeping or administrative purposes |

**User Management Module**

| Input | Type | Source | Validation |
|---|---|---|---|
| New user name | Text | Admin form | Required |
| New user email | Text | Admin form | Required; unique |
| Role assignment | Selection | Admin form | Required |
| Password reset action | Button action | Admin | Resets password to default (`Abc@123`) and flags `must_change_password` |

**Role and Permission Module**

| Input | Type | Source | Validation |
|---|---|---|---|
| Role name | Text | Admin form | Required; unique |
| Permission name | Text | Admin form | Required; unique |
| Permission assignment | Selection (multi) | Admin form | One or more permissions assigned to a role |

**System Settings Module**

| Input | Type | Source | Validation |
|---|---|---|---|
| SMTP configuration fields | Text | Admin form | Host, port, username, password, encryption method |
| Lockout duration | Integer (minutes) | Admin form | Required, positive integer |
| Session timeout | Integer (minutes) | Admin form | Required, positive integer |
| Maximum login attempts | Integer | Admin form | Required, positive integer |
| Extension charge per hour | Decimal | Admin form | Required, positive |
| Extension charge per night | Decimal | Admin form | Required, positive |
| Default extension payment window | Integer (minutes) | Admin form | Required, minimum 1 |
| Refund policy parameters | Decimal (percentage) and Integer (days) | Admin form | Required; percentages between 0 and 100 |

**Guest Feedback Module**

| Input | Type | Source | Validation |
|---|---|---|---|
| Star rating | Integer (1–5) | Guest form | Required; only available after booking status is `completed` |
| Written comment | Long text | Guest form | Optional |

**Chat Module**

| Input | Type | Source | Validation |
|---|---|---|---|
| Chat message | Text | Guest or Admin | Required, non-empty |

### 3.5.4 Output Specification

This section lists every output the system produces. An output is any information the system displays, generates, transmits, or stores in response to a user action or a scheduled event.

**Authentication Module**

| Output | Description |
|---|---|
| Session token | Active session established upon successful login |
| Password reset email | Email containing a time-limited reset link sent to the registered address |
| Password strength indicator | Real-time feedback displayed in the password input field during registration |
| Account lockout message | Displayed when the login attempt limit is exceeded; includes remaining lockout duration |

**Homestay Management Module**

| Output | Description |
|---|---|
| Homestay unit listing | A list of all active units, visible to guests on the browse page |
| Unit detail page | Images, description, pricing, policies, and availability calendar for a single unit |
| Admin unit management list | All units with status (active/inactive) and upcoming booking summary |

**Booking Module**

| Output | Description |
|---|---|
| Availability check result | Confirmation that selected dates are available, or a message that the unit is already booked for the selected period |
| Booking confirmation | Booking record created with `pending_payment` status; selected dates held for 24 hours |
| Booking list | Guest's current and upcoming bookings; admin's full booking list across all units |
| Booking detail page | All booking information including dates, times, homestay unit, status, and amount |
| Booking calendar view | Visual calendar showing confirmed, blocked, and pending dates; available to both guests and admin |
| Cancellation confirmation | Updated booking status set to `cancelled`; refund calculation displayed to user before confirmation |

**Payment Module**

| Output | Description |
|---|---|
| Bill document (PDF) | Generated at booking creation; includes booking reference, amount, deposit, and total payable |
| Payment receipt (PDF) | Generated after successful payment and stored against the booking record |
| Payment status update | Booking status changes to `confirmed` after webhook reports a successful payment |
| Payment history list | Filterable list of payment records for the guest or admin |
| Auto-cancellation | Booking status set to `cancelled` automatically if payment is not received within 24 hours |

**QR Code Door Access Module**

| Output | Description |
|---|---|
| Guest QR code | Unique QR image displayed to the guest after booking is confirmed; valid from check-in to check-out |
| Housekeeping QR code | Separate code generated by admin between bookings; expires once housekeeping is marked complete |
| Extension charge bill | PDF bill generated for the guest when an extension request is approved by admin |
| QR code validity update | `valid_until` timestamp in `qr_codes` table extended after extension payment is confirmed |
| Extension revert | Booking reverted to original check-out date and time if payment is not received within the configured window |

**Notification Module**

| Output | Description |
|---|---|
| In-app notification | Stored as a database record; displayed in the notification panel when the user is active in the system |
| Email notification | Sent via SMTP for booking confirmation, payment success/failure, payment reminder, upcoming check-in/check-out reminder, and extension payment request |
| Google Calendar event | Booking added to the guest's and admin's Google Calendar upon booking confirmation |

**Reporting and Analytics Module**

| Output | Description |
|---|---|
| Summary statistics | Total bookings, revenue, occupancy rate, and cancellation rate displayed on the dashboard |
| Booking trend charts | Line or bar charts showing booking volume over daily, weekly, or monthly intervals |
| Revenue report | Filterable by date range, homestay unit, and payment status |
| Booking breakdown by unit | Comparison of booking counts and revenue across all managed units |
| Guest rating summary | Average rating and total feedback count per unit |
| Report export | PDF or CSV file containing the reported data for offline use |

**Audit Logs Module**

| Output | Description |
|---|---|
| Audit trail | Read-only timestamped records of all user actions, administrative changes, authentication events, and system events |

**Guest Feedback Module**

| Output | Description |
|---|---|
| Published feedback entry | Submitted rating and comment visible on the unit's listing page (subject to admin moderation) |
| Average unit rating | Aggregated star rating displayed on the homestay unit's public page |
| Admin reply | Response written by admin and displayed below the guest's feedback entry |

---

## 3.6 Chapter Summary

This chapter described how HomeLodge was developed. A hybrid methodology was selected: Waterfall for the planning and analysis phases, Agile for implementation and testing. The Waterfall phases produced the complete requirements specification and system design before any code was written. The Agile phases, structured through Scrum sprints and tracked on a Kanban Board, broke the thirteen modules into manageable increments. Deployment followed a sequential checklist once all modules had been implemented and tested.

The system runs on Laravel 11 with PHP 8.2, Blade with Alpine.js and Bootstrap 5, MySQL 8 with Eloquent ORM, and Laravel Reverb with Echo for real-time chat. Supporting packages cover authentication, role-based access control, QR code generation, audit logging, PDF generation, and notifications. Background jobs for auto-cancellation, QR expiry, and extension revert run on a schedule without manual intervention.

A two-vCPU server with four gigabytes of RAM is sufficient to run all application processes. The software requirements are standard for a Laravel deployment and are documented in Section 3.5.2. Sections 3.5.3 and 3.5.4 specify what data each of the thirteen modules accepts and what each one produces.
