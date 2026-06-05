# Chapter 4: Detailed Description of Components (SDD) — HomeLodge Booking Homestay System

This chapter provides the complete package diagram, per-subsystem class diagrams, method algorithms, and sequence diagrams for each use case scenario.

---

## 4.1 Complete Package Diagram

The HomeLodge system is organised into the following packages, reflecting the Laravel MVC + Service Layer architecture:

```
┌───────────────────────────────────────────────────────────────────┐
│                        HomeLodge System                           │
│                                                                   │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐              │
│  │    Views     │  │ Controllers │  │  Services   │              │
│  │   (Blade)   │──│             │──│             │              │
│  └─────────────┘  └──────┬──────┘  └──────┬──────┘              │
│                          │                 │                      │
│                   ┌──────▼─────────────────▼──────┐              │
│                   │         Models (Eloquent)      │              │
│                   └──────────────┬─────────────────┘              │
│                                  │                                │
│                   ┌──────────────▼─────────────────┐              │
│                   │      Database (MySQL/MariaDB)   │              │
│                   └────────────────────────────────┘              │
│                                                                   │
│  Packages (by Module):                                           │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐           │
│  │  P001:   │ │  P002:   │ │  P003:   │ │  P004:   │           │
│  │  Auth    │ │ Homestay │ │ Booking  │ │ Payment  │           │
│  └──────────┘ └──────────┘ └──────────┘ └──────────┘           │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐           │
│  │  P005:   │ │  P006:   │ │  P007:   │ │  P008:   │           │
│  │Notificat.│ │  Chat    │ │ User Mgmt│ │ Role&Perm│           │
│  └──────────┘ └──────────┘ └──────────┘ └──────────┘           │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐           │
│  │  P009:   │ │  P010:   │ │  P011:   │ │  P012:   │           │
│  │ Settings │ │ Audit Log│ │ QR Code  │ │Reporting │           │
│  └──────────┘ └──────────┘ └──────────┘ └──────────┘           │
│  ┌──────────┐                                                    │
│  │  P013:   │                                                    │
│  │ Feedback │                                                    │
│  └──────────┘                                                    │
└───────────────────────────────────────────────────────────────────┘
```

> **Note:** Replace this ASCII diagram with a proper UML package diagram (Draw.io or equivalent) for the final submission.

---

## 4.2 Detailed Description

### 4.2.1 P001: Authentication Subsystem

Each subsystem follows the three-layer pattern: **View → Controller → Model/Service**.

#### 4.2.1.1 Class Diagram

> **Note:** Include a UML class diagram showing: `User`, `AuthController`, `LoginController`, `RegisterController`, `PasswordResetController`, `ProfileController`, and `AuthService`.

**Key Classes:**

- **User** — Eloquent model representing a system user (guest or admin).
- **AuthController** — Handles login, logout, and session management.
- **RegisterController** — Handles user registration (email/password and Google SSO).
- **PasswordResetController** — Handles forgot-password and password change flows.
- **ProfileController** — Handles profile viewing and updating.

**Entity: User**

| Entity Name | User |
| :--- | :--- |
| **Method Name** | register |
| **Input** | name, email, password, password_confirmation |
| **Output** | User object or validation error |
| **Algorithm** | 1. Start 2. Validate input fields (email format, password rules AUTH-05) 3. Check if email already exists 4. If exists, return error "Email already registered" 5. Hash password with bcrypt 6. Create User record with role "User" 7. Send email verification 8. Return success confirmation 9. End |

| Entity Name | User |
| :--- | :--- |
| **Method Name** | login |
| **Input** | email, password |
| **Output** | Redirect to dashboard or error message |
| **Algorithm** | 1. Start 2. Validate input fields 3. Check if account exists and is active 4. If account is locked, return "Account locked" with remaining lockout time 5. Verify password against bcrypt hash 6. If incorrect, increment failed_login_count 7. If failed_login_count >= max_attempts (from Settings), lock account 8. If correct, reset failed_login_count, create session 9. If must_change_password flag is set, redirect to change password page 10. Redirect to role-appropriate dashboard 11. End |

#### 4.2.1.2 Sequence Diagrams

**a) SD001: User Registration (Email/Password)**

> **Note:** Include a UML sequence diagram showing: Guest → RegisterView → RegisterController → User Model → Database → Email Service

**b) SD002: User Login**

> **Note:** Include a UML sequence diagram showing: User → LoginView → AuthController → User Model → Database → Session

**c) SD003: Google SSO Login**

> **Note:** Include a UML sequence diagram showing: User → LoginView → SocialiteController → Google OAuth → User Model → Database → Session

**d) SD004: Forgot Password**

> **Note:** Include a UML sequence diagram showing: User → ForgotPasswordView → PasswordResetController → User Model → Email Service

---

### 4.2.2 P002: Homestay Management Subsystem

#### 4.2.2.1 Class Diagram

> **Note:** Include a UML class diagram showing: `HomestayUnit`, `HousePolicy`, `HomestayImage`, `HomestayController`, `HomestayService`.

**Entity: HomestayUnit**

| Entity Name | HomestayUnit |
| :--- | :--- |
| **Method Name** | createUnit |
| **Input** | name, description, location, base_price, deposit, check_in_time, check_out_time, images[], policies[] |
| **Output** | HomestayUnit object or validation error |
| **Algorithm** | 1. Start 2. Validate all input fields 3. Create HomestayUnit record 4. Upload and store images (link to unit) 5. Copy default house policies from Settings to the new unit 6. If custom policies provided, add those as well 7. Return success confirmation with unit details 8. End |

| Entity Name | HomestayUnit |
| :--- | :--- |
| **Method Name** | deleteUnit |
| **Input** | unit_id |
| **Output** | Success confirmation or error |
| **Algorithm** | 1. Start 2. Find HomestayUnit by ID 3. Check for confirmed future bookings 4. If future bookings exist, return error "Cannot delete unit with future bookings" (HS-03) 5. Delete associated images, policies, and unit record 6. Return success confirmation 7. End |

#### 4.2.2.2 Sequence Diagrams

**a) SD005: Create Homestay Unit**

> **Note:** Include a UML sequence diagram showing: Admin → HomestayView → HomestayController → HomestayService → HomestayUnit Model → Database

**b) SD006: Browse Homestay Listings (Guest)**

> **Note:** Include a UML sequence diagram showing: Guest → LandingPageView → HomestayController → HomestayUnit Model → Database → View

---

### 4.2.3 P003: Booking Subsystem

#### 4.2.3.1 Class Diagram

> **Note:** Include a UML class diagram showing: `Booking`, `BookingController`, `BookingService`, `HomestayUnit`.

**Entity: Booking**

| Entity Name | Booking |
| :--- | :--- |
| **Method Name** | createBooking |
| **Input** | user_id, unit_id, check_in_date, check_in_time, check_out_date, check_out_time |
| **Output** | Booking object with booking number, or error |
| **Algorithm** | 1. Start 2. Validate input fields 3. Check availability for the unit and date range (BK-U-03) 4. If unavailable, return error "The selected date has been booked!" (BK-U-04) 5. Generate unique booking number (format: BK-YYYYMMDD-NNN) (BK-U-06) 6. Create Booking record with status "pending_payment" 7. Create Bill record with unique bill number 8. Set payment hold deadline (BK-H-01) 9. Send booking confirmation notification 10. Return booking details 11. End |

| Entity Name | Booking |
| :--- | :--- |
| **Method Name** | cancelBooking |
| **Input** | booking_id, cancelled_by (guest or admin) |
| **Output** | Cancellation confirmation with refund info |
| **Algorithm** | 1. Start 2. Find Booking by ID 3. Calculate days until check-in 4. Determine refund percentage: if < 3 days → 0%, if >= 7 days → 25%, if >= 14 days → 50% (configurable from Settings) 5. Update booking status to "cancelled" 6. If refund > 0, initiate refund process (3–5 business days) 7. Release the blocked dates 8. Send cancellation notification to guest and admin 9. Return cancellation confirmation with refund amount and timeline 10. End |

#### 4.2.3.2 Sequence Diagrams

**a) SD007: Guest Creates a Booking**

> **Note:** Include a UML sequence diagram showing: Guest → BookingFormView → BookingController → BookingService → Booking Model → Bill Model → NotificationService

**b) SD008: Guest Cancels a Booking**

> **Note:** Include a UML sequence diagram showing: Guest → BookingDetailView → BookingController → BookingService → Booking Model → PaymentService → NotificationService

**c) SD009: Auto-Cancel Unpaid Booking (Scheduled Job)**

> **Note:** Include a UML sequence diagram showing: Scheduler → BookingService → Booking Model → NotificationService

---

### 4.2.4 P004: Payment Subsystem

#### 4.2.4.1 Class Diagram

> **Note:** Include a UML class diagram showing: `Payment`, `Bill`, `PaymentController`, `PaymentService`, `WebhookController`.

**Entity: Payment**

| Entity Name | Payment |
| :--- | :--- |
| **Method Name** | processPayment |
| **Input** | booking_id |
| **Output** | Redirect URL to payment gateway |
| **Algorithm** | 1. Start 2. Find Booking and associated Bill 3. Calculate total amount (base price + deposit) 4. Send API request to payment gateway with amount and booking reference 5. Receive redirect URL from gateway 6. Return redirect URL to guest 7. End |

| Entity Name | Payment |
| :--- | :--- |
| **Method Name** | handleWebhook |
| **Input** | Webhook payload (gateway_reference, status, signature) |
| **Output** | HTTP 200 response to gateway |
| **Algorithm** | 1. Start 2. Verify webhook signature 3. If signature invalid, return 403 4. Check if payment already processed (idempotency via gateway_reference) 5. If already processed, return 200 (no duplicate) 6. If status = success: update Bill to "paid", create Payment record, update Booking to "confirmed", generate QR code, send notifications 7. If status = failure: update Bill status, send failure notification 8. Return HTTP 200 9. End |

#### 4.2.4.2 Sequence Diagrams

**a) SD010: Guest Makes Payment**

> **Note:** Include a UML sequence diagram showing: Guest → PaymentView → PaymentController → PaymentService → Payment Gateway API → Redirect

**b) SD011: Payment Webhook Processing**

> **Note:** Include a UML sequence diagram showing: Payment Gateway → WebhookController → PaymentService → Bill Model → Payment Model → BookingService → QrCodeService → NotificationService

---

### 4.2.5 P005: Notification Subsystem

#### 4.2.5.1 Class Diagram

> **Note:** Include a UML class diagram showing: `Notification` (Laravel built-in), `NotificationService`, `NotificationController`, `GoogleCalendarService`.

#### 4.2.5.2 Sequence Diagrams

**a) SD012: Send Booking Confirmation Notification**

> **Note:** Include a UML sequence diagram showing: BookingService → NotificationService → Database Notification → SMTP Email → Google Calendar API

---

### 4.2.6 P006: Chat Subsystem

#### 4.2.6.1 Class Diagram

> **Note:** Include a UML class diagram showing: `ChatConversation`, `ChatMessage`, `ChatController`, `ChatService`.

#### 4.2.6.2 Sequence Diagrams

**a) SD013: Guest Sends Chat Message**

> **Note:** Include a UML sequence diagram showing: Guest → ChatView → ChatController → ChatMessage Model → Laravel Reverb Broadcast → Admin ChatView

---

### 4.2.7 P007: User Management Subsystem

#### 4.2.7.1 Class Diagram

> **Note:** Include a UML class diagram showing: `User`, `UserController`, `UserService`.

#### 4.2.7.2 Sequence Diagrams

**a) SD014: Admin Creates User Account**

> **Note:** Include a UML sequence diagram

**b) SD015: Admin Resets User Password**

> **Note:** Include a UML sequence diagram

---

### 4.2.8 P008: Role and Permission Subsystem

#### 4.2.8.1 Class Diagram

> **Note:** Include a UML class diagram showing: `Role`, `Permission`, `RoleController`, `PermissionController` (Spatie models).

#### 4.2.8.2 Sequence Diagrams

**a) SD016: Admin Creates Role**

> **Note:** Include a UML sequence diagram

**b) SD017: Admin Assigns Permission to Role**

> **Note:** Include a UML sequence diagram

---

### 4.2.9 P009: System Settings Subsystem

#### 4.2.9.1 Class Diagram

> **Note:** Include a UML class diagram showing: `Setting`, `DefaultHousePolicy`, `SettingsController`, `SettingsService`.

#### 4.2.9.2 Sequence Diagrams

**a) SD018: Admin Updates SMTP Settings**

> **Note:** Include a UML sequence diagram

**b) SD019: Admin Updates Refund Percentages**

> **Note:** Include a UML sequence diagram

---

### 4.2.10 P010: Audit Log Subsystem

#### 4.2.10.1 Class Diagram

> **Note:** Include a UML class diagram showing: `Activity` (Spatie Activity Log model), `AuditLogController`.

#### 4.2.10.2 Sequence Diagrams

**a) SD020: System Records Audit Entry**

> **Note:** Include a UML sequence diagram showing: Any Model Event → Spatie Activity Logger → activity_log Table

**b) SD021: Admin Views Audit Log**

> **Note:** Include a UML sequence diagram

---

### 4.2.11 P011: QR Code Door Access Subsystem

#### 4.2.11.1 Class Diagram

> **Note:** Include a UML class diagram showing: `QrCode`, `BookingExtension`, `QrCodeController`, `QrCodeService`, `ExtensionService`.

**Entity: QrCode**

| Entity Name | QrCode |
| :--- | :--- |
| **Method Name** | generateQrCode |
| **Input** | booking_id |
| **Output** | QR code image (SVG/PNG) |
| **Algorithm** | 1. Start 2. Find Booking by ID 3. Generate a cryptographically secure random token 4. Set valid_from = check-in date/time, valid_until = check-out date/time 5. Create QrCode record (token, valid_from, valid_until, purpose = "guest", status = "active") 6. Encode token as QR code image using SimpleSoftwareIO 7. Return QR code image 8. End |

**Entity: BookingExtension**

| Entity Name | BookingExtension |
| :--- | :--- |
| **Method Name** | requestExtension |
| **Input** | booking_id, new_check_out_date, new_check_out_time |
| **Output** | Extension record with payment deadline, or conflict error |
| **Algorithm** | 1. Start 2. Find Booking and HomestayUnit 3. Check availability for the extended period (QR-08) 4. If conflict, return error "Extension conflicts with another booking" 5. Calculate extension charge (per hour or per night from Settings) (QR-10) 6. Determine payment window (unit-specific or system default) (QR-11) 7. Create BookingExtension record with status "pending_payment" 8. Create additional Bill for the extension charge 9. Set payment deadline = now + payment_window minutes 10. Send payment notification to guest 11. Return extension details 12. End |

#### 4.2.11.2 Sequence Diagrams

**a) SD022: Generate QR Code on Booking Confirmation**

> **Note:** Include a UML sequence diagram showing: PaymentService → QrCodeService → QrCode Model → Database

**b) SD023: Admin Initiates Booking Extension**

> **Note:** Include a UML sequence diagram showing: Admin → QrCodeView → ExtensionController → ExtensionService → BookingExtension Model → Bill Model → NotificationService

**c) SD024: Auto-Cancel Unpaid Extension (Scheduled Job)**

> **Note:** Include a UML sequence diagram showing: Scheduler → ExtensionService → BookingExtension Model → Booking Model → QrCode Model → NotificationService

---

### 4.2.12 P012: Reporting and Analytics Subsystem

#### 4.2.12.1 Class Diagram

> **Note:** Include a UML class diagram showing: `ReportController`, `ReportService`, `BookingExport`, `RevenueExport`.

#### 4.2.12.2 Sequence Diagrams

**a) SD025: Admin Views Dashboard**

> **Note:** Include a UML sequence diagram showing: Admin → DashboardView → ReportController → ReportService → Booking/Payment Models → Chart.js View

**b) SD026: Admin Exports Report (PDF/CSV)**

> **Note:** Include a UML sequence diagram showing: Admin → ReportView → ReportController → ReportService → DomPDF/Maatwebsite Excel → Download

---

### 4.2.13 P013: Guest Feedback Subsystem

#### 4.2.13.1 Class Diagram

> **Note:** Include a UML class diagram showing: `Feedback`, `FeedbackController`, `FeedbackService`.

**Entity: Feedback**

| Entity Name | Feedback |
| :--- | :--- |
| **Method Name** | submitFeedback |
| **Input** | booking_id, rating (1–5), comment (optional) |
| **Output** | Feedback confirmation or error |
| **Algorithm** | 1. Start 2. Find Booking by ID 3. Verify booking status is "completed" (FB-U-02) 4. Check if feedback already exists for this booking (FB-U-03) 5. If exists, return error "Feedback already submitted for this booking" 6. Create Feedback record (rating, comment, user_id, unit_id, booking_id) 7. Recalculate average rating for the unit (FB-A-04) 8. Return success confirmation 9. End |

#### 4.2.13.2 Sequence Diagrams

**a) SD027: Guest Submits Feedback**

> **Note:** Include a UML sequence diagram showing: Guest → BookingDetailView → FeedbackController → FeedbackService → Feedback Model → HomestayUnit (update avg rating)

**b) SD028: Admin Replies to Feedback**

> **Note:** Include a UML sequence diagram showing: Admin → FeedbackView → FeedbackController → Feedback Model (update admin_reply)

---

*— End of Chapter 4: Detailed Description of Components —*
