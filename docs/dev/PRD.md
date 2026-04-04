# Product Requirements Document (PRD)
## HomeLodge – Booking Homestay System

| Field | Detail |
|---|---|
| **Document Version** | 1.2 |
| **Status** | Draft |
| **Last Updated** | 2026-04-05 |
| **Owner** | Product Team |

---

## Table of Contents

1. [Purpose](#1-purpose)
2. [Product Vision](#2-product-vision)
3. [Scope](#3-scope)
4. [User Personas](#4-user-personas)
5. [Functional Requirements](#5-functional-requirements)
   - [5.1 Authentication Module](#51-authentication-module)
   - [5.2 Booking Module](#52-booking-module)
   - [5.3 Payment Module](#53-payment-module)
   - [5.4 Notification Module](#54-notification-module)
   - [5.5 Chat Module](#55-chat-module)
   - [5.6 User Management Module](#56-user-management-module)
   - [5.7 Role & Permission Module](#57-role--permission-module)
   - [5.8 System Settings Module](#58-system-settings-module)
   - [5.9 Audit Logs Module](#59-audit-logs-module)
   - [5.10 QR Code Door Access Module](#510-qr-code-door-access-module)
   - [5.11 Homestay Management Module](#511-homestay-management-module)
   - [5.12 Reporting & Analytics Module](#512-reporting--analytics-module)
   - [5.13 Guest Feedback Module](#513-guest-feedback-module)
6. [Non-Functional Requirements](#6-non-functional-requirements)
7. [Assumptions & Constraints](#7-assumptions--constraints)
8. [Out of Scope](#8-out-of-scope)

---

## 1. Purpose

This Product Requirements Document (PRD) defines the features, behaviour, and acceptance criteria for **HomeLodge**, a web-based homestay booking management system. It serves as the primary reference for the development team and stakeholders throughout the development lifecycle.

---

## 2. Product Vision

To provide a simple, reliable, and modern homestay booking platform that enables guests to discover multiple homestay units, compare availability, and reserve accommodation, while giving operators (admins) full visibility and control over all properties, bookings, payments, and access management.

---

## 3. Scope

**In Scope:**
- Web application accessible via desktop and mobile browsers.
- Two user roles: **User (Guest)** and **Admin**.
- Multi-property support: the admin can manage multiple homestay units within the same system.
- End-to-end booking flow from registration → unit selection → booking → payment → check-in → check-out.
- Automated QR code generation for physical door access, including admin-controlled validity extension.
- Real-time chat between users and admin.
- Email and in-app notification system.
- Reporting and analytics dashboard for operational insights.
- Per-homestay unit policies and house rules (fully configurable, with system-level defaults).
- Guest feedback and rating system.
- All operational parameters and charges are end-to-end configurable via system settings (no hardcoded values).

**Out of Scope:**
- Mobile native application (iOS/Android).
- Third-party OTA (Online Travel Agency) channel integration.

---

## 4. User Personas

### Persona 1 – Guest (User)
- **Description:** A traveller or local who wants to book the homestay for leisure or personal use.
- **Goals:** Easily check availability, make a booking, pay securely, and access the property on arrival.
- **Pain Points:** Unclear availability, complex payment steps, difficulty contacting the host.

### Persona 2 – Admin (Homestay Operator)
- **Description:** The property owner or manager responsible for running day-to-day operations.
- **Goals:** View all bookings at a glance, manage payments, control user access, and handle housekeeping transitions efficiently.
- **Pain Points:** Manual tracking of bookings, difficulty communicating with guests, managing physical key handover.

---

## 5. Functional Requirements

### 5.1 Authentication Module

Applies to both **Users** and **Admins** unless otherwise stated.

| ID | Requirement | Priority |
|---|---|---|
| AUTH-01 | Users and admins can register an account using email/password or Google SSO. | High |
| AUTH-02 | Users and admins can log in using registered credentials or Google SSO. | High |
| AUTH-03 | Users and admins can log out of the system. | High |
| AUTH-04 | A "Forgot Password" flow sends a password-reset token link to the registered email. | High |
| AUTH-05 | Password must meet complexity criteria: 8–12 characters, at least one uppercase letter, one lowercase letter, one number, and one special character. | High |
| AUTH-06 | The password input field includes a show/hide toggle and a real-time strength indicator. | Medium |
| AUTH-07 | Users forced to reset their password (by admin) cannot access other pages until the password is changed. | High |
| AUTH-08 | Accounts are temporarily locked after exceeding the configured number of failed login attempts. | High |
| AUTH-09 | Locked accounts are automatically unlocked after the configured lockout duration expires. | High |
| AUTH-10 | A locked account can be unlocked early by the user resetting via "Forgot Password" or by admin intervention. | High |
| AUTH-11 | Users and admins can view and update their own profile information. | Medium |

---

### 5.2 Booking Module

#### User Capabilities

| ID | Requirement | Priority |
|---|---|---|
| BK-U-01 | Users can view a calendar showing available and unavailable dates. | High |
| BK-U-02 | Users can select a check-in and check-out date and time. | High |
| BK-U-03 | The system performs real-time availability checks upon date selection. | High |
| BK-U-04 | If the date is unavailable, the system displays: *"The selected date has been booked! Please choose another date"* and disables the submit button. | High |
| BK-U-05 | If the date is available, the submit button is enabled. | High |
| BK-U-06 | Users can submit (create) a booking. | High |
| BK-U-07 | Users can view their current booking list, booking history, and individual booking details. | High |
| BK-U-08 | Users can cancel their own bookings, subject to the cancellation policy. | High |

#### Admin Capabilities

| ID | Requirement | Priority |
|---|---|---|
| BK-A-01 | Admin can view a booking calendar showing booked, temporarily blocked, and available dates. | High |
| BK-A-02 | Admin can create bookings on behalf of registered users. | High |
| BK-A-03 | Admin can create their own bookings. | Medium |
| BK-A-04 | Admin can view, edit, and delete any booking. | High |
| BK-A-05 | Admin can cancel bookings on behalf of users. | High |
| BK-A-06 | Admin can filter the booking list by status, booking ID, and date range. | Medium |
| BK-A-07 | Admin can block specific dates to prevent user bookings. The user only sees the date is blocked, not the reason. | Medium |

#### Cancellation Policy

| Cancellation Timing | Refund |
|---|---|
| Less than 3 days before booking | No refund |
| 1 week before booking | 25% refund |
| 2 weeks before booking | 50% refund |

- Refunds are processed within **3–5 business days**.

#### Payment Hold & Auto-Cancellation

| ID | Requirement | Priority |
|---|---|---|
| BK-H-01 | Upon booking submission, the date is temporarily blocked for **1 day** pending payment. | High |
| BK-H-02 | If payment is completed within 1 day, the booking is confirmed. | High |
| BK-H-03 | If payment is not completed within 1 day, the booking is automatically cancelled and the date is reopened. | High |

---

### 5.3 Payment Module

#### User Capabilities

| ID | Requirement | Priority |
|---|---|---|
| PAY-U-01 | Users can make payments via the configured payment gateway; the gateway calculates the total amount including deposit. | High |
| PAY-U-02 | Users can view the payment bill generated for their booking. | High |
| PAY-U-03 | Users can view and download the payment receipt after a successful payment. | High |
| PAY-U-04 | Users can view their payment history, filtered by date range and payment status. | Medium |
| PAY-U-05 | Users receive a notification (in-app and email) upon payment success or failure via webhook. | High |

#### Admin Capabilities

| ID | Requirement | Priority |
|---|---|---|
| PAY-A-01 | The system auto-generates a unique bill number and payment number for each transaction. | High |
| PAY-A-02 | Admin can view the billing list, filtered by ID and date range. | Medium |
| PAY-A-03 | Admin can view the payment list, filtered by ID, date range, and status. | Medium |
| PAY-A-04 | Admin can regenerate a bill for a booking. | Medium |
| PAY-A-05 | Admin can regenerate a receipt for a completed payment. | Medium |

---

### 5.4 Notification Module

| ID | Requirement | Priority |
|---|---|---|
| NOTIF-01 | Users and admins receive in-app notifications for key system events. | High |
| NOTIF-02 | Users and admins receive email notifications for key system events. | High |
| NOTIF-03 | Users receive payment reminders for bookings with pending payment. | High |
| NOTIF-04 | Users and admins receive reminders for upcoming check-in/check-out bookings. | Medium |
| NOTIF-05 | Admins receive QR code reminders related to check-in and check-out events. | Medium |
| NOTIF-06 | Bookings are integrated with **Google Calendar** and visible to both users and admins. | Medium |
| NOTIF-07 | Admin can enable or disable email notifications via system settings. | Medium |

---

### 5.5 Chat Module

| ID | Requirement | Priority |
|---|---|---|
| CHAT-01 | Users can initiate and send chat messages to the admin. | Medium |
| CHAT-02 | Admins can receive and reply to messages from users. | Medium |
| CHAT-03 | Chat is implemented using **WebSocket** for real-time communication. | Medium |

---

### 5.6 User Management Module

*Admin only.*

| ID | Requirement | Priority |
|---|---|---|
| USR-01 | Admin can create new user accounts and assign them the default **User** role. | High |
| USR-02 | Admin can edit existing user information. | High |
| USR-03 | Admin can delete user accounts. | High |
| USR-04 | Admin can reset a user's password by sending a reset link via email or resetting it to the default password (`Abc@123`). | High |
| USR-05 | Resetting a user's password forces the user to change their password on next login. | High |
| USR-06 | Admin can activate or deactivate any user account. | High |

---

### 5.7 Role & Permission Module

*Admin only.*

| ID | Requirement | Priority |
|---|---|---|
| ROLE-01 | Admin can create, edit, and delete roles. | High |
| ROLE-02 | Admin can assign one or more permissions to a role. | High |
| ROLE-03 | A role that is currently assigned to one or more users **cannot** be deleted. | High |
| PERM-01 | Admin can create, edit, and delete permissions. | High |
| PERM-02 | A permission that is currently attached to one or more roles **cannot** be deleted. | High |

---

### 5.8 System Settings Module

*Admin only.*

#### SMTP Configuration

| ID | Requirement | Priority |
|---|---|---|
| SET-SMTP-01 | Admin can configure SMTP settings for outgoing system emails. | High |

#### Security Settings

| ID | Requirement | Priority |
|---|---|---|
| SET-SEC-01 | Admin can configure the account lockout duration. | High |
| SET-SEC-02 | Admin can configure the session timeout duration. | High |
| SET-SEC-03 | Admin can configure the maximum number of failed login attempts before account lockout. | High |

#### Other Settings

| ID | Requirement | Priority |
|---|---|---|
| SET-GEN-01 | Admin can manage general system settings (e.g., system name, logo). | Low |
| SET-GEN-02 | Admin can configure the payment refund policy parameters (e.g., refund percentages and timing windows). | High |
| SET-GEN-03 | Admin can configure payment and billing options. | High |
| SET-GEN-04 | Admin can enable or disable email notification delivery system-wide. | Medium |

#### Booking Extension Charges

| ID | Requirement | Priority |
|---|---|---|
| SET-EXT-01 | Admin can configure the extra charge rate applied when a guest extends their booking time (per hour). | High |
| SET-EXT-02 | Admin can configure the extra charge rate applied when a guest extends their booking date (per additional night). | High |
| SET-EXT-03 | All extension charge configurations are stored in the system and applied dynamically — no values are hardcoded. | High |
| SET-EXT-04 | Admin can configure the system-wide **default extension payment window** (in minutes) that guests have to pay the extension charge before it expires; the default value is **60 minutes (1 hour)**. | High |

#### Default Homestay Policies

| ID | Requirement | Priority |
|---|---|---|
| SET-POL-01 | Admin can configure the system-level default house policies applied to all newly created homestay units. | High |
| SET-POL-02 | The default policies are pre-seeded as: **No Pets Allowed**, **No Durians**, **No Smoking**; these can be modified by the admin. | Medium |

---

### 5.9 Audit Logs Module

*Admin only.*

| ID | Requirement | Priority |
|---|---|---|
| AUDIT-01 | Admin can view a comprehensive audit trail of all system activities. | High |
| AUDIT-02 | Audit logs capture user actions, administrative changes, authentication events, and system events. | High |
| AUDIT-03 | Audit logs are read-only and cannot be modified or deleted by any user. | High |

---

### 5.10 QR Code Door Access Module

| ID | Requirement | Priority |
|---|---|---|
| QR-01 | The system generates a unique QR code for each confirmed booking. | High |
| QR-02 | The QR code grants door access from **check-in (3:00 PM)** to **check-out (12:00 PM)**. | High |
| QR-03 | The QR code is automatically invalidated and regenerated after the check-out time has passed. | High |
| QR-04 | Admin can manually regenerate a QR code for housekeeping purposes. | High |
| QR-05 | After housekeeping is marked as complete, the system generates a new QR code for the next booking. | High |
| QR-06 | Each booking is guaranteed to have a unique and distinct QR code. | High |
| QR-07 | Admin can initiate a booking extension request when a guest requests to extend their check-out time or booking date. | High |
| QR-08 | Before processing an extension, the system automatically checks availability for the extended period (no conflicting bookings). | High |
| QR-09 | Upon admin approval of an extension, the system creates an **extension record** in `pending_payment` status and generates an additional charge bill for the guest. | High |
| QR-10 | Extensions of booking time or date incur extra charges; the charge rates are configurable by the admin in System Settings. | High |
| QR-11 | The guest must pay the extension charge within a **configurable payment window** (per homestay unit; system-wide default is **1 hour**). | High |
| QR-12 | The QR code validity is **not** automatically extended at the time of the extension request — it is only updated **after** the extension payment is confirmed. | High |
| QR-13 | If the guest does not pay within the configured payment window, the extension is automatically **cancelled**; the booking reverts to the original check-out date and time. | High |
| QR-14 | After an extension payment is confirmed, the system updates the booking record with the new dates/times and regenerates/extends the QR code validity accordingly. | High |

---

### 5.11 Homestay Management Module

*Admin only (unless stated otherwise).*

| ID | Requirement | Priority |
|---|---|---|
| HS-01 | Admin can create a new homestay unit with name, description, location, images, and pricing. | High |
| HS-02 | Admin can edit the details of any existing homestay unit. | High |
| HS-03 | Admin can deactivate or delete a homestay unit. A unit with confirmed future bookings cannot be deleted. | High |
| HS-04 | Admin can view all homestay units in a list, with status (active/inactive) and summary of upcoming bookings. | High |
| HS-05 | Guests can browse all active homestay units and view the details and availability calendar for each unit before booking. | High |
| HS-06 | Availability checks and bookings are scoped per homestay unit, so multiple units can be booked on the same date without conflict. | High |
| HS-07 | Admin can upload and manage multiple images per homestay unit. | Medium |
| HS-08 | Admin can set a base price, deposit amount, and check-in/check-out times per homestay unit. | High |
| HS-09 | Each homestay unit has its own configurable house policies and rules (e.g., no smoking, no pets, no durians, cleanliness requirements). | High |
| HS-10 | The system provides a set of default policies applied to all newly created units: **No Pets Allowed**, **No Durians**, **No Smoking**. Admins can add, edit, or remove policies per unit. | High |
| HS-11 | Guests can view the house policies for a unit before making a booking. | High |
| HS-12 | Admin can manage system-level default policies that are pre-applied to every new homestay unit; these defaults are configurable and not hardcoded. | Medium |
| HS-13 | Admin can configure a **per-unit extension payment window** (in minutes) that overrides the system-wide default when guests make extension requests for that unit. | High |

---

### 5.12 Reporting & Analytics Module

*Admin only.*

| ID | Requirement | Priority |
|---|---|---|
| RPT-01 | Admin can access a reporting and analytics dashboard displaying key operational metrics. | High |
| RPT-02 | The dashboard shows summary statistics: total bookings, revenue, occupancy rate, and cancellation rate. | High |
| RPT-03 | Admin can view booking trends over time (daily, weekly, monthly). | High |
| RPT-04 | Admin can view revenue reports filtered by date range, homestay unit, and payment status. | High |
| RPT-05 | Admin can view a breakdown of bookings by homestay unit. | Medium |
| RPT-06 | Admin can view guest feedback and rating summaries per homestay unit. | Medium |
| RPT-07 | Admin can export reports (e.g., PDF or CSV) for offline analysis. | Medium |

---

### 5.13 Guest Feedback Module

| ID | Requirement | Priority |
|---|---|---|
| FB-U-01 | Guests can submit a rating (e.g., 1–5 stars) and written feedback for a homestay unit after their stay is completed. | High |
| FB-U-02 | Guests can only submit feedback for bookings that have been completed (checked out). | High |
| FB-U-03 | Each guest can submit only one feedback entry per completed booking. | High |
| FB-U-04 | Guests can view their previously submitted feedback. | Medium |
| FB-A-01 | Admin can view all feedback and ratings submitted for each homestay unit. | High |
| FB-A-02 | Admin can respond to guest feedback on behalf of the homestay. | Medium |
| FB-A-03 | Admin can moderate (hide or flag) feedback that violates the platform's content guidelines. | Medium |
| FB-A-04 | The average rating per homestay unit is displayed on the unit's public listing page. | High |

---

| ID | Requirement | Category |
|---|---|---|
| NFR-01 | The system must be accessible on modern desktop and mobile web browsers. | Compatibility |
| NFR-02 | All sensitive data (passwords, payment info) must be encrypted at rest and in transit (HTTPS/TLS). | Security |
| NFR-03 | Password hashing must use a strong algorithm (e.g., bcrypt). | Security |
| NFR-04 | The system must handle concurrent booking requests without creating double bookings. | Reliability |
| NFR-05 | Payment webhook processing must be idempotent to avoid duplicate payment records. | Reliability |
| NFR-06 | Page load times should be under 3 seconds for standard operations on a typical broadband connection. | Performance |
| NFR-07 | The system must log all authentication and administrative actions for auditability. | Compliance |

---

## 7. Assumptions & Constraints

- Multiple homestay units are supported; each unit has its own availability, pricing, QR code access, and house policies.
- Payment gateway integration is handled by a third-party provider; specific provider TBD.
- Google Calendar integration uses the Google Calendar API with OAuth 2.0.
- WebSocket chat relies on a self-hosted or cloud-based service (e.g., Laravel Reverb or Pusher).
- QR code door access assumes a compatible smart lock device is installed on-site.
- All system configurations (charges, policies, security settings, etc.) are managed via the admin panel with no hardcoded values.
- Guest feedback can only be submitted after a booking is marked as completed (checked out).

---

## 8. Out of Scope

- Native iOS or Android mobile applications.
- Direct integration with OTA platforms (Airbnb, Booking.com, etc.).
