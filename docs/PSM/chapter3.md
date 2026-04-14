# CHAPTER 3: METHODOLOGY

## 3.1 Introduction

This chapter describes the approach used to develop HomeLodge. It begins by justifying the selection of the Waterfall model as the development methodology and then details each phase involved. The chapter then lists the hardware and software used during development, followed by a description of the technologies integrated into the system. It closes with a detailed specification of the system's inputs and outputs across all modules.

---

## 3.2 Justification of Methodology Selection

As established in Section 2.5, four software development methodologies were considered for this project: Waterfall, Agile, Rapid Application Development (RAD), and the Iterative model. Waterfall was selected. The reasons are specific to this project's constraints.

HomeLodge was designed with a fixed scope. All functional and non-functional requirements were gathered, reviewed, and documented in full before any implementation began. The system has thirteen defined modules, each with clear acceptance criteria. These requirements did not change between the design phase and the implementation phase. Under these conditions, the adaptive behaviour that Agile and RAD are built around — frequent scope adjustment, working software delivered in short cycles, sustained stakeholder review between cycles — becomes unnecessary overhead rather than a benefit.

The Waterfall model structures development into sequential phases: requirements analysis, system design, implementation, testing, and deployment. Each phase produces a documented output, and the next phase does not begin until the previous one is complete. This structure suits a project where all requirements are known at the start and where changing them mid-development would require coordinated changes to several documents, database tables, and module interfaces simultaneously.

There is a second practical reason. The phases of the Waterfall model map directly onto the chapters of this report. The requirements analysis phase corresponds to the requirements gathered in Chapter 3. The system design phase corresponds to the database schema and module design also documented in this chapter. The implementation phase corresponds to Chapter 4. Testing corresponds to Chapter 5. A methodology that produces these specific outputs at defined intervals suits an academic project where documentation and software carry equal weight.

---

## 3.3 Software Development Methodology

HomeLodge was developed following the Waterfall model, divided into five sequential phases as described below.

### 3.3.1 Phase 1: Requirements Analysis

This phase involved collecting and documenting all user and system requirements for HomeLodge. The User Requirements Specification (URS) and Product Requirements Document (PRD) were produced during this phase. Requirements were gathered by examining how small-scale homestay operators currently manage bookings, identifying the recurring failures of the manual process, and comparing what existing platforms provide against what is specifically needed.

The output of this phase was a full requirements specification covering both user roles — Guest and Admin — across thirteen functional modules: Authentication, Homestay Management, Booking, Payment, Notification, Chat, User Management, Role and Permission Management, System Settings, Audit Logs, QR Code Door Access, Reporting and Analytics, and Guest Feedback.

### 3.3.2 Phase 2: System Design

With the requirements fixed, the system design phase produced three outputs.

The first was the database schema. Each table was designed to support at least one functional requirement. Foreign key constraints, soft delete columns, and index definitions were established at this stage, before any migration files were written.

The second was the use case diagram. This documents the interactions each user role has with each module, based on the requirements gathered in Phase 1.

The third was the user interface design. Page layouts for both the guest interface and the admin interface were sketched, covering the booking flow, the admin dashboard, and the QR code management workflow.

### 3.3.3 Phase 3: Implementation

The implementation phase translated the design outputs into working code. Development followed a module-by-module approach, starting with Authentication, then Homestay Management, then Booking, and proceeding through the remaining modules in an order that respected data dependencies — for example, the Payment module was not started until the Booking module was functional, because payment records reference confirmed booking records.

Each module was developed until it met the acceptance criteria defined in the PRD, at which point development of the next module began.

### 3.3.4 Phase 4: Testing

Once all modules were implemented, the system was tested against the functional and non-functional requirements. Testing covered authentication flows, booking availability checks, payment integration, QR code generation and expiry, extension auto-cancellation, role-based access enforcement, and notification delivery.

The full test results and discussion are documented in Chapter 5.

### 3.3.5 Phase 5: Deployment

The completed system was deployed to a server environment with Nginx, PHP-FPM, MySQL 8, and Redis. Queue workers were started for background job processing, and the Laravel Scheduler was configured to run at one-minute intervals through the server's cron service. SSL/TLS was enabled using Let's Encrypt.

---

## 3.4 Description of Technologies Used

The complete technology justification is provided in Section 2.6. This section lists the technologies used in HomeLodge and their roles within the system.

### 3.4.1 Backend: Laravel 11 and PHP 8.2

Laravel is the application framework. It handles routing, controller logic, database access through Eloquent ORM, background job queuing, scheduled tasks, and email delivery. PHP 8.2 is the language in which the application is written.

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

Laravel Blade is the server-side templating engine. It processes view files on the server and delivers complete HTML to the browser. Alpine.js handles the lightweight client-side behaviour that requires state in the browser between user interactions, including the booking date calendar, the real-time notification indicator, and the chat interface. Bootstrap 5 provides the grid layout system and pre-built UI components used across all pages. Chart.js, loaded via CDN, renders the statistical charts on the reporting dashboard.

### 3.4.3 Database: MySQL 8 and Eloquent ORM

MySQL 8 stores all relational data for the system. Eloquent ORM manages the mapping between PHP model classes and database tables, and handles the relationship traversal between related records — bookings to users, bookings to QR codes, bookings to payment records, and so on. Laravel Migrations manage all schema changes, and Laravel Seeders populate the database with initial roles, permissions, system settings, and default homestay policies.

### 3.4.4 Real-Time Communication: Laravel Reverb and Laravel Echo

Laravel Reverb runs as a self-hosted WebSocket server process alongside the Laravel application. It manages connection lifecycles and routes broadcast events to connected clients. Laravel Echo is the JavaScript library that establishes a WebSocket connection in the browser, subscribes to the relevant broadcast channel, and delivers incoming events to the chat interface in real time.

### 3.4.5 Authentication and Access Control

Session-based authentication uses Laravel's built-in authentication guard. Google Single Sign-On is implemented through `laravel/socialite`, which manages the OAuth 2.0 redirect, token exchange, and user account lookup. The `spatie/laravel-permission` package implements role-based access control: permissions are assigned to roles, and roles are assigned to users. Every route and controller action that requires a specific permission uses a middleware check against this system.

### 3.4.6 QR Code Generation

The `simplesoftwareio/simple-qrcode` package generates QR codes locally within the application as SVG images. No external API call is made during QR code generation. Each QR code encodes a unique secure token that is valid from the guest's check-in time to their check-out time. The token is stored in the `qr_codes` table and its expiry is enforced by the Laravel Scheduler.

### 3.4.7 Payment Gateway

Payments are processed through an online payment gateway using a server-side API integration. The gateway sends a webhook callback to HomeLodge when a payment event occurs, and the application updates the booking and billing records based on the webhook payload. Webhook processing is idempotent, meaning if the same notification is received more than once, the system produces the same result without creating duplicate records. The specific gateway is selected at deployment based on regional availability and fee structure. Billplz, Toyyibpay, and Stripe are the shortlisted options; all three are compatible with the webhook-driven integration model used by the system.

### 3.4.8 Notifications

Email notifications are sent through Laravel Mail using SMTP credentials configured by the administrator through the system settings. The administrator can switch providers without modifying any application code. In-app notifications use Laravel's database notification driver: each notification is stored as a record and read by the application when the user loads any page.

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

The smart lock hardware is not developed as part of this project. The system generates the QR code and the token it encodes; the lock manufacturer's firmware or SDK is responsible for validating the token at the door.

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

This section lists all inputs accepted by the system across its modules. An input is any data that a user submits to the system, whether through a form, a button action, or an external callback.

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

This section lists all outputs produced by the system. An output is any information the system displays, generates, transmits, or stores in response to a user action or a scheduled event.

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

This chapter described the development approach used for HomeLodge. The Waterfall model was selected because the system requirements were fully defined before development began, the scope did not change once implementation started, and the phase structure produces the specific documentation outputs that this report requires. Each Waterfall phase — requirements analysis, system design, implementation, testing, and deployment — is completed sequentially, and the output of each phase forms the basis of the next.

The technologies used in the system are Laravel 11 with PHP 8.2 for the backend, Blade with Alpine.js and Bootstrap 5 for the frontend, MySQL 8 with Eloquent ORM for the database, and Laravel Reverb with Echo for real-time communication. Supporting packages handle authentication, role-based access control, QR code generation, audit logging, PDF generation, and notification delivery. The payload and scheduling structure of the application's background jobs are designed to handle auto-cancellation, QR expiry, and extension revert without manual administrative intervention.

The hardware requirements for deployment are modest: a two-vCPU server with four gigabytes of RAM is sufficient to run all application processes concurrently. The software requirements are standard for a Laravel application and are fully documented in Section 3.5.2. The input and output specifications in Sections 3.5.3 and 3.5.4 define, in exact terms, what data each module accepts from users and what it produces in return, covering all thirteen functional modules of HomeLodge.
