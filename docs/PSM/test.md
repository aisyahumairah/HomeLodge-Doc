# PSM Project Proposal
> Form Reference: PSM.SPACE.CI.03

---

## Section A — Student Information

| Field | Detail |
|---|---|
| **Student Name** | *(To be filled)* |
| **Student ID** | *(To be filled)* |
| **Programme** | *(To be filled)* |
| **Session / Semester** | *(To be filled)* |
| **Supervisor Name** | *(To be filled)* |

---

## Section B — Project Details

### Project Title

**HomeLodge: A Web-Based Homestay Booking Management System with QR Code Door Access and Real-Time Communication**

---

### Problem Background and Proposed Solution

The homestay accommodation sector, particularly among small and independent property operators, continues to rely on manual and fragmented processes to manage reservations, payments, and guest access. Booking requests are often handled through informal channels such as messaging applications or phone calls, making it difficult for operators to maintain accurate records, avoid double-bookings, and communicate consistently with guests. Payment collection is similarly unstructured, with no systematic mechanism to generate bills, track payment status, or enforce cancellation policies. Guest access to the property is typically managed through physical key handovers, which exposes the operator to logistical difficulties — particularly when check-in or check-out times do not align with the operator's availability, or when housekeeping transitions must occur between stays. These operational inefficiencies reduce service quality, increase the likelihood of booking conflicts, and create unnecessary administrative burden for the homestay operator.

HomeLodge addresses these challenges through a comprehensive, web-based homestay booking management system designed to digitalise and automate the full reservation lifecycle. The system allows guests to browse date availability, submit bookings, and make payments through an integrated online payment gateway — all within a single platform. An automated payment window of one day is enforced after each booking submission; if payment is not completed within this period, the booking is automatically cancelled and the date is released for other guests. The system generates unique QR codes for each confirmed booking, enabling guests to access the property digitally at check-in without requiring a physical key exchange. These QR codes are time-limited and automatically expire at checkout, with the ability for the admin to regenerate codes for housekeeping purposes. Guests and administrators are kept informed through an integrated notification system that delivers both in-app and email alerts for booking confirmations, payment status updates, and upcoming stay reminders. Additionally, a real-time chat module facilitates direct communication between guests and the administrator, reducing the need for out-of-system contact. For administrators, the system provides full visibility and control over all bookings, payments, user accounts, roles, permissions, and system-wide settings — supported by a comprehensive audit log that records all activities for accountability and traceability.

---

### Objectives

The objectives of the project are:

1. To study, collect, and analyse the functional and non-functional requirements for developing a web-based homestay booking management system.
2. To design the system architecture, database schema, and user interface for the HomeLodge system, encompassing all planned modules and two user roles.
3. To develop the HomeLodge web application with ten (10) functional modules, including booking management, payment processing, QR code door access, real-time chat, and role-based access control, following the defined requirements.
4. To test the developed system against the stated functional and non-functional requirements to verify correctness, security, and usability.

---

### Scope

The scope of the system is as follows:

1. The application will be a web-based system accessible via a modern desktop or mobile web browser.
2. The target users of this system are two (2) distinct roles: **Guest (User)** — individuals who wish to book the homestay accommodation — and **Admin** — the homestay operator responsible for managing all bookings, payments, access control, and system configuration.
3. The application will support one (1) language: English.
4. The system is designed for a single homestay property (single-unit deployment) in version 1.0.
5. This system will consist of ten (10) modules:
   - 5.1 Authentication Module
   - 5.2 Booking Module
   - 5.3 Payment Module
   - 5.4 Notification Module
   - 5.5 Chat Module
   - 5.6 User Management Module
   - 5.7 Role and Permission Module
   - 5.8 System Settings Module
   - 5.9 Audit Logs Module
   - 5.10 QR Code Door Access Module

---

### Project Requirements

| Field | Detail |
|---|---|
| **Software** | PHP 8.2+, Laravel 11.x, Composer, Node.js, NPM, Vite, MySQL 8.x / MariaDB 10.x, Redis, Visual Studio Code / PhpStorm, Git, Postman |
| **Hardware** | Windows / Linux / macOS operating system; minimum 8 GB RAM; minimum 50 GB available storage; internet connection for payment gateway and Google API integration |
| **Technology / Technique / Method / Algorithm** | Agile development methodology; MVC architectural pattern (Laravel); Role-Based Access Control (RBAC) using Spatie Laravel Permission; WebSocket real-time communication using Laravel Reverb; QR code generation using `simplesoftwareio/simple-qrcode`; Google OAuth 2.0 via Laravel Socialite; PDF generation using `barryvdh/laravel-dompdf`; audit logging using `spatie/laravel-activitylog`; bcrypt password hashing; payment webhook with idempotent processing; Laravel Blade server-side rendering with Alpine.js for client-side interactivity |
| **Network Elements** | Web server (Nginx or Apache) accessible via HTTPS; MySQL/MariaDB database server; Redis server for session management, job queues, and caching; Laravel Reverb WebSocket server; payment gateway API (external); Google Calendar API (external); SMTP mail server |
| **Security Elements** | HTTPS/TLS encryption for all data in transit; bcrypt password hashing for all stored passwords; MySQL database access restricted to application credentials; session authentication with configurable timeout; configurable account lockout after failed login attempts; role-based access control for all protected routes and actions; payment webhook signature verification; read-only immutable audit logs; forced password change upon admin-initiated password reset |
| **Project Type** | System Development |
| **Project Area** | Web Application Development — Homestay Property Management |

---

## Section C — Student Acknowledgement

I hereby declare that the information provided in this proposal is accurate and complete to the best of my knowledge. I understand the objectives, scope, and requirements of this project and commit to completing the stated work within the given timeframe.

| | |
|---|---|
| **Student Signature** | *(Signature)* |
| **Date** | *(Date)* |

---

## Section D — Supervisor Acknowledgement

I hereby acknowledge that I have reviewed this proposal and agree to supervise the student in completing this project.

| | |
|---|---|
| **Supervisor Signature** | *(Signature)* |
| **Date** | *(Date)* |

---

## Section F — For SC SPACE Coordinator Use Only

| Field | Detail |
|---|---|
| **Received By** | *(Name)* |
| **Date Received** | *(Date)* |
| **Status** | *(Approved / Revision Required / Rejected)* |
| **Remarks** | *(Remarks)* |
