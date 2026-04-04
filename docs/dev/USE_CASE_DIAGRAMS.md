# Use Case Diagrams
## HomeLodge – Booking Homestay System

| Field | Detail |
|---|---|
| **Document Version** | 1.2 |
| **Status** | Draft |
| **Last Updated** | 2026-04-05 |
| **Owner** | Product / Business Analysis Team |

---

## Table of Contents

1. [System Overview](#1-system-overview)
2. [Authentication Module](#2-authentication-module)
3. [Homestay Management Module](#3-homestay-management-module)
4. [Booking Module](#4-booking-module)
5. [Payment Module](#5-payment-module)
6. [Notification Module](#6-notification-module)
7. [Chat Module](#7-chat-module)
8. [User Management Module](#8-user-management-module)
9. [Role & Permission Module](#9-role--permission-module)
10. [System Settings Module](#10-system-settings-module)
11. [Audit Logs Module](#11-audit-logs-module)
12. [QR Code Door Access Module](#12-qr-code-door-access-module)
13. [Reporting & Analytics Module](#13-reporting--analytics-module)
14. [Guest Feedback Module](#14-guest-feedback-module)

---

## 1. System Overview

This diagram shows a high-level view of all actors and their interaction areas within the HomeLodge system.

```mermaid
graph LR
    subgraph Actors
        Guest["Guest (User)"]
        Admin["Admin"]
        PG["Payment Gateway"]
        GCal["Google Calendar API"]
    end

    subgraph HomeLodge System
        AUTH["Authentication"]
        HS["Homestay Management"]
        BK["Booking"]
        PAY["Payment"]
        NOTIF["Notification"]
        CHAT["Chat"]
        USR["User Management"]
        ROLE["Role & Permission"]
        SET["System Settings"]
        AUDIT["Audit Logs"]
        QR["QR Code Door Access"]
        RPT["Reporting & Analytics"]
        FB["Guest Feedback"]
    end

    Guest --> AUTH
    Guest --> HS
    Guest --> BK
    Guest --> PAY
    Guest --> NOTIF
    Guest --> CHAT
    Guest --> FB

    Admin --> AUTH
    Admin --> HS
    Admin --> BK
    Admin --> PAY
    Admin --> NOTIF
    Admin --> CHAT
    Admin --> USR
    Admin --> ROLE
    Admin --> SET
    Admin --> AUDIT
    Admin --> QR
    Admin --> RPT
    Admin --> FB

    PG --> PAY
    GCal --> NOTIF
```

---

## 2. Authentication Module

Covers registration, login, logout, password management, and profile management for both Guest and Admin.

```mermaid
flowchart LR
    Guest["Guest"]
    Admin["Admin"]

    subgraph Authentication Module
        UC1["Register Account\n(Email/Password)"]
        UC2["Register/Login via\nGoogle SSO"]
        UC3["Login"]
        UC4["Logout"]
        UC5["Forgot Password\n(Reset via Email)"]
        UC6["View/Update Profile"]
        UC7["Show/Hide Password Toggle"]
        UC8["Force Change Password\n(After Admin Reset)"]
        UC9["Account Lockout\n(Exceeded Failed Attempts)"]
        UC10["Auto Unlock Account\n(After Lockout Duration)"]
    end

    Guest --- UC1
    Guest --- UC2
    Guest --- UC3
    Guest --- UC4
    Guest --- UC5
    Guest --- UC6
    Guest --- UC7
    Guest --- UC8

    Admin --- UC2
    Admin --- UC3
    Admin --- UC4
    Admin --- UC5
    Admin --- UC6

    UC3 -. "«extends»" .-> UC9
    UC9 -. "«extends»" .-> UC10
    UC5 -. "«extends»\n(Early Unlock)" .-> UC9
```

---

## 3. Homestay Management Module

Covers listing, creation, editing, and deactivation of homestay units by the admin, browsing of units by guests, and management of per-unit house policies.

```mermaid
flowchart LR
    Guest["Guest"]
    Admin["Admin"]

    subgraph Homestay Management Module
        UC1["Browse Homestay Units"]
        UC2["View Unit Details & Availability"]
        UC3["View House Policies"]
        UC4["Create Homestay Unit"]
        UC5["Edit Homestay Unit"]
        UC6["Deactivate / Delete Unit"]
        UC7["Upload Unit Images"]
        UC8["Set Pricing & Check-in/out Times"]
        UC9["View All Units List"]
        UC10["Manage Unit House Policies"]
        UC11["Apply Default Policies\n(On Unit Creation)"]
    end

    Guest --- UC1
    Guest --- UC2
    Guest --- UC3

    Admin --- UC4
    Admin --- UC5
    Admin --- UC6
    Admin --- UC7
    Admin --- UC8
    Admin --- UC9
    Admin --- UC10

    UC4 -. "«includes»" .-> UC7
    UC4 -. "«includes»" .-> UC8
    UC4 -. "«includes»" .-> UC11
    UC6 -. "«extends»\n(Blocked if future bookings exist)" .-> UC6
    UC10 -. "«extends»" .-> UC5
```

---

## 4. Booking Module

Covers booking creation, viewing, cancellation, calendar management, and date blocking. All bookings are scoped to a specific homestay unit.

```mermaid
flowchart LR
    Guest["Guest"]
    Admin["Admin"]
    System["System"]

    subgraph Booking Module
        UC1["View Availability Calendar"]
        UC2["Select Check-in/\nCheck-out Date & Time"]
        UC3["Check Date Availability\n(Real-time)"]
        UC4["Submit Booking"]
        UC5["View Current Bookings"]
        UC6["View Booking History"]
        UC7["View Booking Details"]
        UC8["Cancel Booking"]
        UC9["View Cancellation Policy\n& Refund Info"]

        UC10["View Booking Calendar\n(All Statuses)"]
        UC11["Create Booking\n(On Behalf of User)"]
        UC12["Edit Booking"]
        UC13["Delete Booking"]
        UC14["Cancel Booking\n(On Behalf of User)"]
        UC15["Filter Booking List"]
        UC16["Block Dates"]

        UC17["Temporary Hold\n(1-Day Payment Window)"]
        UC18["Auto-Cancel Booking\n(Payment Timeout)"]
    end

    Guest --- UC1
    Guest --- UC2
    Guest --- UC4
    Guest --- UC5
    Guest --- UC6
    Guest --- UC7
    Guest --- UC8

    UC2 -. "«includes»" .-> UC3
    UC8 -. "«includes»" .-> UC9

    Admin --- UC10
    Admin --- UC11
    Admin --- UC12
    Admin --- UC13
    Admin --- UC14
    Admin --- UC15
    Admin --- UC16

    UC4 -. "«includes»" .-> UC17
    System --- UC18
    UC17 -. "«extends»\n(Payment Not Received)" .-> UC18
```

---

## 5. Payment Module

Covers payment processing, billing, receipts, and refund handling.

```mermaid
flowchart LR
    Guest["Guest"]
    Admin["Admin"]
    PG["Payment\nGateway"]

    subgraph Payment Module
        UC1["Make Payment\n(Online Gateway)"]
        UC2["View Payment Bill"]
        UC3["View/Download\nPayment Receipt"]
        UC4["View Payment History"]
        UC5["Filter Payment History\n(Date/Status)"]

        UC6["View Billing List"]
        UC7["Filter Billing List\n(ID/Date)"]
        UC8["View Payment List"]
        UC9["Filter Payment List\n(ID/Date/Status)"]
        UC10["Regenerate Bill"]
        UC11["Regenerate Receipt"]

        UC12["Auto-Generate\nBill & Payment Number"]
        UC13["Process Payment\nWebhook"]
    end

    Guest --- UC1
    Guest --- UC2
    Guest --- UC3
    Guest --- UC4

    UC4 -. "«includes»" .-> UC5

    Admin --- UC6
    Admin --- UC8
    Admin --- UC10
    Admin --- UC11

    UC6 -. "«includes»" .-> UC7
    UC8 -. "«includes»" .-> UC9

    UC1 -. "«includes»" .-> UC12
    PG --- UC13
    PG --- UC1
```

---

## 6. Notification Module

Covers in-app notifications, email notifications, reminders, and Google Calendar integration.

```mermaid
flowchart LR
    Guest["Guest"]
    Admin["Admin"]
    GCal["Google\nCalendar API"]

    subgraph Notification Module
        UC1["Receive In-App\nNotification"]
        UC2["Receive Email\nNotification"]
        UC3["Receive Payment\nReminder"]
        UC4["Receive Check-in/\nCheck-out Reminder"]
        UC5["View Booking in\nGoogle Calendar"]
        UC6["Receive QR Code\nReminder"]
        UC7["Toggle Email\nNotifications"]
    end

    Guest --- UC1
    Guest --- UC2
    Guest --- UC3
    Guest --- UC4
    Guest --- UC5

    Admin --- UC1
    Admin --- UC2
    Admin --- UC4
    Admin --- UC5
    Admin --- UC6
    Admin --- UC7

    UC5 -. "«uses»" .-> GCal
```

---

## 7. Chat Module

Covers real-time messaging between Guest and Admin via WebSocket.

```mermaid
flowchart LR
    Guest["Guest"]
    Admin["Admin"]

    subgraph Chat Module
        UC1["Send Message"]
        UC2["Receive Message\n(Real-time)"]
        UC3["View Chat History"]
    end

    Guest --- UC1
    Guest --- UC2
    Guest --- UC3

    Admin --- UC1
    Admin --- UC2
    Admin --- UC3
```

---

## 8. User Management Module

Admin-only module for managing user accounts.

```mermaid
flowchart LR
    Admin["Admin"]

    subgraph User Management Module
        UC1["Create User Account"]
        UC2["Edit User Account"]
        UC3["Delete User Account"]
        UC4["Reset User Password\n(Link or Default)"]
        UC5["Activate/Deactivate\nUser Account"]
        UC6["Force Password Change\n(After Reset)"]
    end

    Admin --- UC1
    Admin --- UC2
    Admin --- UC3
    Admin --- UC4
    Admin --- UC5

    UC4 -. "«includes»" .-> UC6
```

---

## 9. Role & Permission Module

Admin-only module for managing roles and permissions.

```mermaid
flowchart LR
    Admin["Admin"]

    subgraph Role & Permission Module
        UC1["Create Role"]
        UC2["Edit Role"]
        UC3["Delete Role"]
        UC4["Assign Permissions\nto Role"]
        UC5["Create Permission"]
        UC6["Edit Permission"]
        UC7["Delete Permission"]
        UC8["Prevent Delete Role\n(If Assigned to User)"]
        UC9["Prevent Delete Permission\n(If Attached to Role)"]
    end

    Admin --- UC1
    Admin --- UC2
    Admin --- UC3
    Admin --- UC4
    Admin --- UC5
    Admin --- UC6
    Admin --- UC7

    UC3 -. "«extends»" .-> UC8
    UC7 -. "«extends»" .-> UC9
```

---

## 10. System Settings Module

Admin-only module for configuring system-wide settings.

```mermaid
flowchart LR
    Admin["Admin"]

    subgraph System Settings Module
        UC1["Configure SMTP\nSettings"]
        UC2["Configure Lockout\nDuration"]
        UC3["Configure Session\nTimeout"]
        UC4["Configure Max\nLogin Attempts"]
        UC5["Configure Refund\nPolicy Parameters"]
        UC6["Configure Payment &\nBilling Options"]
        UC7["Toggle Email\nNotifications"]
        UC8["Manage General Settings\n(Name, Logo)"]
        UC9["Configure Extension\nCharge Rates"]
        UC10["Manage Default\nHomestay Policies"]
    end

    Admin --- UC1
    Admin --- UC2
    Admin --- UC3
    Admin --- UC4
    Admin --- UC5
    Admin --- UC6
    Admin --- UC7
    Admin --- UC8
    Admin --- UC9
    Admin --- UC10
```

---

## 11. Audit Logs Module

Admin-only module for viewing the system audit trail.

```mermaid
flowchart LR
    Admin["Admin"]
    System["System"]

    subgraph Audit Logs Module
        UC1["View Audit Trail"]
        UC2["Filter Audit Logs"]
        UC3["Log User Actions"]
        UC4["Log Admin Changes"]
        UC5["Log Authentication\nEvents"]
        UC6["Log System Events"]
    end

    Admin --- UC1
    Admin --- UC2

    System --- UC3
    System --- UC4
    System --- UC5
    System --- UC6
```

---

## 12. QR Code Door Access Module

Covers QR code generation, invalidation, housekeeping workflow, and validity extension. Each QR code is scoped to a specific booking and homestay unit.

```mermaid
flowchart LR
    Admin["Admin"]
    Guest["Guest"]
    System["System"]

    subgraph QR Code Door Access Module
        UC1["Receive QR Code\n(Upon Booking Confirmation)"]
        UC2["Use QR Code for\nDoor Access"]
        UC3["Regenerate QR Code\n(For Housekeeping)"]
        UC4["Mark Housekeeping\nComplete"]
        UC5["Auto-Invalidate QR Code\n(After Check-out)"]
        UC6["Auto-Generate QR Code\n(For Next Guest)"]
        UC7["Initiate Booking Extension\n(Time or Date)"]
        UC8["Check Availability\nBefore Extension"]
        UC9["Generate Extension Bill\n& Set Payment Deadline"]
        UC10["Notify Guest of Extension\nBill + Deadline"]
        UC11["Guest Pays Extension\nCharge"]
        UC12["Confirm Extension\n(Update Booking + QR)"]
        UC13["Auto-Cancel Extension\n(Deadline Expired)"]
        UC14["Revert Booking to\nOriginal Dates/Times"]
        UC15["Configure Per-Unit\nExtension Payment Window"]
    end

    Guest --- UC1
    Guest --- UC2
    Guest --- UC11

    Admin --- UC3
    Admin --- UC4
    Admin --- UC7
    Admin --- UC15

    System --- UC5
    System --- UC6
    System --- UC13

    UC4 -. "«triggers»" .-> UC6
    UC5 -. "«triggers»" .-> UC3
    UC7 -. "«includes»" .-> UC8
    UC7 -. "«includes»" .-> UC9
    UC9 -. "«triggers»" .-> UC10
    UC11 -. "«extends»\n(Payment Confirmed)" .-> UC12
    UC13 -. "«includes»" .-> UC14
    UC15 -. "«extends»" .-> UC9
```

---

## 13. Reporting & Analytics Module

Admin-only module for viewing operational metrics and generating reports.

```mermaid
flowchart LR
    Admin["Admin"]

    subgraph Reporting & Analytics Module
        UC1["View Analytics Dashboard"]
        UC2["View Booking Trends\n(Daily/Weekly/Monthly)"]
        UC3["View Revenue Report"]
        UC4["Filter Reports\n(Date/Unit/Status)"]
        UC5["View Bookings\nBreakdown per Unit"]
        UC6["View Feedback &\nRating Summary"]
        UC7["Export Report\n(PDF/CSV)"]
    end

    Admin --- UC1
    Admin --- UC2
    Admin --- UC3
    Admin --- UC5
    Admin --- UC6
    Admin --- UC7

    UC3 -. "«includes»" .-> UC4
    UC1 -. "«includes»" .-> UC2
    UC1 -. "«includes»" .-> UC3
    UC1 -. "«includes»" .-> UC6
```

---

## 14. Guest Feedback Module

Covers post-stay feedback submission by guests and moderation/response by the admin.

```mermaid
flowchart LR
    Guest["Guest"]
    Admin["Admin"]

    subgraph Guest Feedback Module
        UC1["Submit Rating & Feedback\n(After Stay Completed)"]
        UC2["View Submitted Feedback"]
        UC3["View All Unit Feedback"]
        UC4["Respond to Feedback"]
        UC5["Moderate/Hide Feedback"]
        UC6["Display Average Rating\non Listing Page"]
    end

    Guest --- UC1
    Guest --- UC2

    Admin --- UC3
    Admin --- UC4
    Admin --- UC5

    UC1 -. "«extends»\n(Only after check-out)" .-> UC1
    UC6 -. "«derives from»" .-> UC1
```

---

## Traceability Matrix

The following table maps each use case diagram back to its source requirements in the URS and PRD.

| Module | URS References | PRD References |
|---|---|---|
| Authentication | URS-U-AUTH-01 to 08, URS-A-AUTH-01 to 03 | AUTH-01 to 11 |
| Homestay Management | URS-U-BK-01, URS-U-POL-01, URS-A-HS-01 to 09 | HS-01 to 13 |
| Booking | URS-U-BK-02 to 10, URS-A-BK-01 to 07 | BK-U-01 to 08, BK-A-01 to 07, BK-H-01 to 03 |
| Payment | URS-U-PAY-01 to 06, URS-A-PAY-01 to 04 | PAY-U-01 to 05, PAY-A-01 to 05 |
| Notification | URS-U-NOTIF-01 to 03, URS-A-NOTIF-01 to 03 | NOTIF-01 to 07 |
| Chat | URS-U-CHAT-01, URS-A-CHAT-01 | CHAT-01 to 03 |
| User Management | URS-A-USR-01 to 04 | USR-01 to 06 |
| Role & Permission | URS-A-ROLE-01 to 03, URS-A-PERM-01 to 02 | ROLE-01 to 03, PERM-01 to 02 |
| System Settings | URS-A-SET-01 to 08 | SET-SMTP-01, SET-SEC-01 to 03, SET-GEN-01 to 04, SET-EXT-01 to 04, SET-POL-01 to 02 |
| Audit Logs | URS-A-AUDIT-01 | AUDIT-01 to 03 |
| QR Code Door Access | URS-U-EXT-01 to 03, URS-A-QR-01 to 08 | QR-01 to 14 |
| Reporting & Analytics | URS-A-RPT-01 to 06 | RPT-01 to 07 |
| Guest Feedback | URS-U-FB-01 to 02, URS-A-FB-01 to 03 | FB-U-01 to 04, FB-A-01 to 04 |
