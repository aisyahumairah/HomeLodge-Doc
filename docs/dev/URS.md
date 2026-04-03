# User Requirements Specification (URS)
## HomeLodge – Booking Homestay System

| Field | Detail |
|---|---|
| **Document Version** | 1.0 |
| **Status** | Draft |
| **Last Updated** | 2026-03-03 |
| **Owner** | Product / Business Analysis Team |

---

## Table of Contents

1. [Introduction](#1-introduction)
2. [Stakeholders](#2-stakeholders)
3. [User Requirements – Guest (User)](#3-user-requirements--guest-user)
4. [User Requirements – Admin](#4-user-requirements--admin)
5. [System-Level Requirements](#5-system-level-requirements)
6. [Glossary](#6-glossary)

---

## 1. Introduction

### 1.1 Purpose

This User Requirements Specification (URS) captures the needs and expectations of the end users of the **HomeLodge** system. It describes *what* the users need the system to do, written from the user's perspective, without prescribing specific technical solutions.

This document serves as the basis for deriving functional requirements in the [Product Requirements Document (PRD)](./PRD.md).

### 1.2 System Overview

HomeLodge is a web-based homestay booking management system that allows guests to browse multiple homestay units, check availability, and book accommodation. It also provides the homestay operator (admin) with tools to manage multiple properties, bookings, guests, payments, and physical property access.

### 1.3 Document Conventions

- Requirements are identified with a unique ID (e.g., `URS-U-AUTH-01`).
- Prefix `U` = User (Guest); `A` = Admin; `SYS` = System-wide.
- Priority: **Must Have** | **Should Have** | **Nice to Have**

---

## 2. Stakeholders

| Stakeholder | Role | Interest |
|---|---|---|
| Guest (User) | End user who books the homestay | Simple, transparent, reliable booking experience |
| Admin | Homestay operator / manager | Full operational control and visibility |
| Development Team | Builds and maintains the system | Clear and unambiguous requirements |
| Payment Gateway Provider | Processes online payments | Correct API integration |

---

## 3. User Requirements – Guest (User)

### 3.1 Account & Authentication

| ID | User Requirement | Priority |
|---|---|---|
| URS-U-AUTH-01 | As a guest, I want to register an account using my email and password, so that I can access the booking system. | Must Have |
| URS-U-AUTH-02 | As a guest, I want to register or log in using my Google account, so that I don't have to create a separate password. | Should Have |
| URS-U-AUTH-03 | As a guest, I want to log in to the system securely, so that my personal information is protected. | Must Have |
| URS-U-AUTH-04 | As a guest, I want to log out of the system, so that my session is terminated when I'm done. | Must Have |
| URS-U-AUTH-05 | As a guest, I want to reset my password via a link sent to my registered email, so that I can regain access if I forget my password. | Must Have |
| URS-U-AUTH-06 | As a guest, I want to see the password requirements clearly when setting a new password, so that I know what format is expected. | Must Have |
| URS-U-AUTH-07 | As a guest, I want a show/hide toggle on the password field, so that I can verify what I'm typing. | Should Have |
| URS-U-AUTH-08 | As a guest, I want to view and update my profile information, so that my contact details remain current. | Should Have |

### 3.2 Booking

| ID | User Requirement | Priority |
|---|---|---|
| URS-U-BK-01 | As a guest, I want to browse all available homestay units and view their details, so that I can choose a property that suits my needs. | Must Have |
| URS-U-BK-02 | As a guest, I want to see which dates are available and which are not for a specific units, so that I can choose a suitable date before attempting to book. | Must Have |
| URS-U-BK-03 | As a guest, I want to select my preferred check-in and check-out dates and times for a chosen unit, so that I can define my stay. | Must Have |
| URS-U-BK-04 | As a guest, I want the system to immediately tell me if my selected dates are unavailable for a unit, so that I don't waste time on a booking that will fail. | Must Have |
| URS-U-BK-05 | As a guest, I want to submit my booking once availability is confirmed, so that I can secure the dates. | Must Have |
| URS-U-BK-06 | As a guest, I want to view a list of my current and upcoming bookings, so that I can keep track of my reservations. | Must Have |
| URS-U-BK-07 | As a guest, I want to view my booking history, so that I can reference past stays. | Should Have |
| URS-U-BK-08 | As a guest, I want to view the full details of each booking, so that I know what I have booked. | Must Have |
| URS-U-BK-09 | As a guest, I want to cancel a booking, so that I can change my plans when necessary. | Must Have |
| URS-U-BK-10 | As a guest, I want to be clearly informed of the cancellation policy and the refund I will receive before I confirm a cancellation, so that I can make an informed decision. | Must Have |

### 3.3 Payment

| ID | User Requirement | Priority |
|---|---|---|
| URS-U-PAY-01 | As a guest, I want to pay for my booking securely through an online payment gateway, so that I can confirm my reservation. | Must Have |
| URS-U-PAY-02 | As a guest, I want to see the total amount I need to pay, including any deposit, before I confirm payment. | Must Have |
| URS-U-PAY-03 | As a guest, I want to receive a digital receipt after successful payment, so that I have proof of my transaction. | Must Have |
| URS-U-PAY-04 | As a guest, I want to view my payment history and filter it by date range and status, so that I can track my spending. | Should Have |
| URS-U-PAY-05 | As a guest, I want to receive a notification when my payment is successful or fails, so that I know the outcome immediately. | Must Have |
| URS-U-PAY-06 | As a guest, I want to be reminded to pay for my booking if I haven't done so, so that I don't accidentally lose my reservation. | Must Have |

### 3.4 Notifications

| ID | User Requirement | Priority |
|---|---|---|
| URS-U-NOTIF-01 | As a guest, I want to receive notifications both inside the system and via email, so that I stay informed about my booking. | Must Have |
| URS-U-NOTIF-02 | As a guest, I want to receive a reminder before my stay begins, so that I can prepare for check-in. | Should Have |
| URS-U-NOTIF-03 | As a guest, I want my booking to appear on my Google Calendar, so that I can manage my schedule easily. | Should Have |

### 3.5 Chat

| ID | User Requirement | Priority |
|---|---|---|
| URS-U-CHAT-01 | As a guest, I want to chat with the admin in real time, so that I can ask questions or resolve issues quickly. | Should Have |

---

## 4. User Requirements – Admin

### 4.1 Account & Authentication

| ID | User Requirement | Priority |
|---|---|---|
| URS-A-AUTH-01 | As an admin, I want to log in securely using email/password or Google SSO. | Must Have |
| URS-A-AUTH-02 | As an admin, I want to reset my password using a link sent to my email. | Must Have |
| URS-A-AUTH-03 | As an admin, I want to view and update my own profile. | Should Have |

### 4.2 Booking Management

| ID | User Requirement | Priority |
|---|---|---|
| URS-A-BK-01 | As an admin, I want to see all bookings on a visual calendar, including confirmed, pending, and blocked dates. | Must Have |
| URS-A-BK-02 | As an admin, I want to create bookings on behalf of guests. | Must Have |
| URS-A-BK-03 | As an admin, I want to view, edit, and delete any booking in the system. | Must Have |
| URS-A-BK-04 | As an admin, I want to cancel a booking and apply the appropriate refund. | Must Have |
| URS-A-BK-05 | As an admin, I want to filter the booking list by status, booking ID, and date range, so that I can find specific bookings quickly. | Should Have |
| URS-A-BK-06 | As an admin, I want to block specific dates so that no one can book those dates. | Should Have |
| URS-A-BK-07 | As an admin, I want guests to only see that a date is blocked, not the reason, so that internal scheduling remains private. | Should Have |

### 4.3 Payment Management

| ID | User Requirement | Priority |
|---|---|---|
| URS-A-PAY-01 | As an admin, I want bill numbers and payment numbers to be generated automatically, so that I don't have to create them manually. | Must Have |
| URS-A-PAY-02 | As an admin, I want to view the billing list and filter it by ID and date range. | Should Have |
| URS-A-PAY-03 | As an admin, I want to view the payment list and filter it by ID, date range, and status. | Should Have |
| URS-A-PAY-04 | As an admin, I want to regenerate a bill or receipt when needed, so that I can correct errors or reissue documents. | Should Have |

### 4.4 User Management

| ID | User Requirement | Priority |
|---|---|---|
| URS-A-USR-01 | As an admin, I want to create, edit, and delete user accounts, so that I can manage who has access to the system. | Must Have |
| URS-A-USR-02 | As an admin, I want to reset a user's password (either by sending a reset link or resetting to a default), so that I can help users who are locked out. | Must Have |
| URS-A-USR-03 | As an admin, I want to force a user to change their password after I reset it, so that they set a secure personal password. | Must Have |
| URS-A-USR-04 | As an admin, I want to activate and deactivate user accounts, so that I can control who can log in. | Must Have |

### 4.5 Role & Permission Management

| ID | User Requirement | Priority |
|---|---|---|
| URS-A-ROLE-01 | As an admin, I want to create, edit, and delete roles, so that I can define different levels of access. | Must Have |
| URS-A-ROLE-02 | As an admin, I want to assign permissions to roles, so that access control is fine-grained. | Must Have |
| URS-A-ROLE-03 | As an admin, I want the system to prevent me from deleting a role that is assigned to a user, so that I don't accidentally break access. | Must Have |
| URS-A-PERM-01 | As an admin, I want to create, edit, and delete permissions, so that I can define what actions are allowed. | Must Have |
| URS-A-PERM-02 | As an admin, I want the system to prevent me from deleting a permission tied to a role, so that I don't break role configurations. | Must Have |

### 4.6 System Settings

| ID | User Requirement | Priority |
|---|---|---|
| URS-A-SET-01 | As an admin, I want to configure SMTP settings, so that the system can send emails. | Must Have |
| URS-A-SET-02 | As an admin, I want to configure security settings (lockout duration, session timeout, login attempts), so that I can tune the system's security posture. | Must Have |
| URS-A-SET-03 | As an admin, I want to configure refund policy parameters, so that the system enforces the correct cancellation rules. | Must Have |
| URS-A-SET-04 | As an admin, I want to toggle email notifications on or off globally, so that I can control notification delivery. | Should Have |

### 4.7 Audit Logs

| ID | User Requirement | Priority |
|---|---|---|
| URS-A-AUDIT-01 | As an admin, I want to view a complete audit trail of all system and user actions, so that I can investigate issues and ensure accountability. | Must Have |

### 4.8 QR Code Door Access

| ID | User Requirement | Priority |
|---|---|---|
| URS-A-QR-01 | As an admin, I want each confirmed booking to automatically generate a unique QR code for the guest, so that door access is managed without physical keys. | Must Have |
| URS-A-QR-02 | As an admin, I want to regenerate a QR code manually for housekeeping purposes, so that cleaning staff can access the property between bookings. | Must Have |
| URS-A-QR-03 | As an admin, I want the system to automatically generate a new QR code for the next guest after housekeeping is complete. | Must Have |

### 4.9 Notifications & Calendar

| ID | User Requirement | Priority |
|---|---|---|
| URS-A-NOTIF-01 | As an admin, I want to receive in-app and email notifications about new bookings and payments. | Must Have |
| URS-A-NOTIF-02 | As an admin, I want reminders for upcoming check-ins and check-outs. | Should Have |
| URS-A-NOTIF-03 | As an admin, I want all bookings to appear on my Google Calendar, so that I have a consolidated view of my schedule. | Should Have |

### 4.10 Chat

| ID | User Requirement | Priority |
|---|---|---|
| URS-A-CHAT-01 | As an admin, I want to receive and reply to chat messages from guests in real time. | Should Have |

### 4.11 Homestay Management

| ID | User Requirement | Priority |
|---|---|---|
| URS-A-HS-01 | As an admin, I want to create a new homestay unit with a name, description, location, images, and pricing, so that guests can find and book it. | Must Have |
| URS-A-HS-02 | As an admin, I want to edit the details of any existing homestay unit, so that the listing stays accurate. | Must Have |
| URS-A-HS-03 | As an admin, I want to deactivate or delete a homestay unit, so that unavailable properties are no longer shown to guests. | Must Have |
| URS-A-HS-04 | As an admin, I want to view all managed homestay units with their status and upcoming booking summary, so that I have a clear operational overview. | Must Have |
| URS-A-HS-05 | As an admin, I want to set the base price, deposit amount, and default check-in/check-out times per unit, so that pricing is managed individually per property. | Must Have |
| URS-A-HS-06 | As an admin, I want to upload and manage multiple images for each homestay unit, so that guests have a good visual representation of each property. | Should Have |

---

## 5. System-Level Requirements

| ID | Requirement | Priority |
|---|---|---|
| URS-SYS-01 | The system must be accessible via a standard web browser without requiring any special plugins or installations. | Must Have |
| URS-SYS-02 | The system must support responsive layouts for both desktop and mobile screens. | Must Have |
| URS-SYS-03 | The system must enforce HTTPS for all pages. | Must Have |
| URS-SYS-04 | The system must prevent double bookings for the same unit on the same dates. | Must Have |
| URS-SYS-05 | All user passwords must be stored in a hashed format; plain-text passwords must never be stored. | Must Have |

---

## 6. Glossary

| Term | Definition |
|---|---|
| **Booking** | A reservation made by a guest or admin for a specific date range at a homestay unit. |
| **Homestay Unit** | A single managed property within the HomeLodge system, with its own profile, pricing, and availability. |
| **Admin** | The homestay operator or manager who has full administrative access to the system. |
| **Guest / User** | A registered individual who uses the system to browse units and make bookings. |
| **QR Code** | A machine-readable code generated per booking that grants physical door access to the booked unit. |
| **Webhook** | An HTTP callback used by the payment gateway to notify the system of payment status changes. |
| **SSO** | Single Sign-On — in this context, authentication via Google OAuth 2.0. |
| **Lockout** | A state where a user account is temporarily prevented from logging in due to excessive failed attempts. |
| **Housekeeping** | The process of cleaning and preparing the property between bookings. |
| **Bill** | A document generated at booking time requesting payment from the guest. |
| **Receipt** | A document generated after successful payment confirming the transaction. |
