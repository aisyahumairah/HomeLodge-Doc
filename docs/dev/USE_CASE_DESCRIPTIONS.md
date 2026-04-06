# Use Case Descriptions
## HomeLodge – Booking Homestay System

| Field | Detail |
|---|---|
| **Document Version** | 1.0 |
| **Status** | Draft |
| **Last Updated** | 2026-04-05 |
| **Reference** | USE_CASE_DIAGRAMS.md, PRD.md, URS.md |

---

## Table of Contents

1. [Authentication Module](#1-authentication-module)
2. [Homestay Management Module](#2-homestay-management-module)
3. [Booking Module](#3-booking-module)
4. [Payment Module](#4-payment-module)
5. [Notification Module](#5-notification-module)
6. [Chat Module](#6-chat-module)
7. [User Management Module](#7-user-management-module)
8. [Role & Permission Module](#8-role--permission-module)
9. [System Settings Module](#9-system-settings-module)
10. [Audit Logs Module](#10-audit-logs-module)
11. [QR Code Door Access Module](#11-qr-code-door-access-module)
12. [Reporting & Analytics Module](#12-reporting--analytics-module)
13. [Guest Feedback Module](#13-guest-feedback-module)

---

## 1. Authentication Module

---

### UC-AUTH-01: Register Account (Email/Password)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-AUTH-01 |
| **Use Case Name** | Register Account (Email/Password) |
| **Actor(s)** | Guest |
| **Description** | A new user registers for a HomeLodge account using their email address and a chosen password. |
| **Preconditions** | The user does not already have an account. |
| **Postconditions** | A new user account is created with the Guest role. The user can log in. |
| **PRD Refs** | AUTH-01, AUTH-02 |

**Main Flow:**
1. Guest navigates to the registration page.
2. Guest enters name, email address, and password (with confirmation).
3. System validates inputs (format, uniqueness of email, password strength).
4. System creates the account, assigns the Guest role, and sends a verification email.
5. Guest is redirected to the login page with a success message.

**Alternative Flows:**
- **A1 – Email already registered:** System displays an error and prompts login or password reset.
- **A2 – Validation failure:** System highlights invalid fields and retains entered values.

---

### UC-AUTH-02: Register / Login via Google SSO

| Field | Detail |
|---|---|
| **Use Case ID** | UC-AUTH-02 |
| **Use Case Name** | Register / Login via Google SSO |
| **Actor(s)** | Guest, Admin |
| **Description** | A user authenticates using their Google account via OAuth 2.0. If no account exists, one is created automatically. |
| **Preconditions** | User has a valid Google account. |
| **Postconditions** | User is authenticated and redirected to the dashboard. |
| **PRD Refs** | AUTH-03 |

**Main Flow:**
1. User clicks "Continue with Google."
2. System redirects to Google OAuth consent screen.
3. User grants permission.
4. System receives Google profile data (name, email, google_id).
5. System finds or creates an account and logs the user in.

**Alternative Flows:**
- **A1 – Google auth rejected:** System displays an error and returns to the login page.

---

### UC-AUTH-03: Login

| Field | Detail |
|---|---|
| **Use Case ID** | UC-AUTH-03 |
| **Use Case Name** | Login |
| **Actor(s)** | Guest, Admin |
| **Description** | A registered user logs in with their email and password. |
| **Preconditions** | User has a valid, active account. |
| **Postconditions** | User is authenticated and redirected to their dashboard. |
| **PRD Refs** | AUTH-01, AUTH-05, AUTH-06 |

**Main Flow:**
1. User enters email and password.
2. System validates credentials.
3. System logs the user in and records `last_login_at`.
4. User is redirected to the dashboard.

**Alternative Flows:**
- **A1 – Invalid credentials:** System increments `failed_login_attempts` and shows an error.
- **A2 – Account locked:** System displays a lockout message and estimated unlock time (extends to UC-AUTH-09).
- **A3 – Must change password:** User is redirected to the forced password change screen (UC-AUTH-08).

---

### UC-AUTH-04: Logout

| Field | Detail |
|---|---|
| **Use Case ID** | UC-AUTH-04 |
| **Use Case Name** | Logout |
| **Actor(s)** | Guest, Admin |
| **Description** | The authenticated user ends their session. |
| **Preconditions** | User is logged in. |
| **Postconditions** | Session is invalidated. User is redirected to the login page. |
| **PRD Refs** | AUTH-04 |

**Main Flow:**
1. User clicks the logout button.
2. System invalidates the session token.
3. User is redirected to the login page.

---

### UC-AUTH-05: Forgot Password (Reset via Email)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-AUTH-05 |
| **Use Case Name** | Forgot Password |
| **Actor(s)** | Guest, Admin |
| **Description** | A user requests a password reset link to be sent to their registered email. |
| **Preconditions** | User has a registered email account. |
| **Postconditions** | User receives a reset link and can set a new password. |
| **PRD Refs** | AUTH-07 |

**Main Flow:**
1. User clicks "Forgot Password" on the login page.
2. User enters their registered email address.
3. System sends a time-limited reset link to the email.
4. User clicks the link and is taken to a reset password form.
5. User enters and confirms the new password.
6. System updates the password and invalidates the reset link.
7. If account was locked, lockout is lifted (early unlock).

---

### UC-AUTH-06: View / Update Profile

| Field | Detail |
|---|---|
| **Use Case ID** | UC-AUTH-06 |
| **Use Case Name** | View / Update Profile |
| **Actor(s)** | Guest, Admin |
| **Description** | Authenticated user views and updates their profile information. |
| **Preconditions** | User is logged in. |
| **Postconditions** | Profile information is updated in the system. |
| **PRD Refs** | AUTH-08 |

**Main Flow:**
1. User navigates to the profile page.
2. User edits name, phone number, or profile photo.
3. User submits the form.
4. System validates and saves the updated information.

---

### UC-AUTH-07: Show/Hide Password Toggle

| Field | Detail |
|---|---|
| **Use Case ID** | UC-AUTH-07 |
| **Use Case Name** | Show/Hide Password Toggle |
| **Actor(s)** | Guest |
| **Description** | User toggles visibility of the password field on login, registration, or password change forms. |
| **Preconditions** | A password input field is active on screen. |
| **Postconditions** | Password field toggles between `type="password"` and `type="text"`. |
| **PRD Refs** | AUTH-09 |

---

### UC-AUTH-08: Force Change Password (After Admin Reset)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-AUTH-08 |
| **Use Case Name** | Force Change Password |
| **Actor(s)** | Guest |
| **Description** | After an admin resets a user's password, the user is required to set a new password on next login before accessing the system. |
| **Preconditions** | Admin has reset the user's password; `must_change_password` flag is `true`. |
| **Postconditions** | User sets a new password; flag is cleared; user proceeds to dashboard. |
| **PRD Refs** | AUTH-10, USR-04 |

**Main Flow:**
1. User logs in with the admin-provided temporary password.
2. System detects `must_change_password = true`.
3. System redirects user to the forced password change page.
4. User enters and confirms a new password.
5. System saves the new password, clears the flag, and redirects to the dashboard.

---

### UC-AUTH-09: Account Lockout

| Field | Detail |
|---|---|
| **Use Case ID** | UC-AUTH-09 |
| **Use Case Name** | Account Lockout |
| **Actor(s)** | System |
| **Description** | The system automatically locks an account after a configurable number of consecutive failed login attempts. |
| **Preconditions** | `failed_login_attempts` reaches the configured `security.max_attempts` threshold. |
| **Postconditions** | Account is locked; `locked_until` is set; user cannot log in during lockout period. |
| **PRD Refs** | AUTH-05, AUTH-06, SET-SEC-01, SET-SEC-03 |

---

### UC-AUTH-10: Auto Unlock Account

| Field | Detail |
|---|---|
| **Use Case ID** | UC-AUTH-10 |
| **Use Case Name** | Auto Unlock Account |
| **Actor(s)** | System |
| **Description** | The system automatically unlocks a locked account after the configured lockout duration has passed, or immediately upon successful password reset. |
| **Preconditions** | Account is locked (`locked_until` is set). |
| **Postconditions** | `locked_until` is cleared; `failed_login_attempts` reset to 0; user can log in again. |
| **PRD Refs** | AUTH-05, AUTH-06, SET-SEC-01 |

---

## 2. Homestay Management Module

---

### UC-HS-01: Browse Homestay Units

| Field | Detail |
|---|---|
| **Use Case ID** | UC-HS-01 |
| **Use Case Name** | Browse Homestay Units |
| **Actor(s)** | Guest |
| **Description** | Guest views a list of all active homestay units available for booking. |
| **Preconditions** | At least one active homestay unit exists. |
| **Postconditions** | Guest can see unit names, thumbnails, base price, and location. |
| **PRD Refs** | HS-05 |

---

### UC-HS-02: View Unit Details & Availability

| Field | Detail |
|---|---|
| **Use Case ID** | UC-HS-02 |
| **Use Case Name** | View Unit Details & Availability |
| **Actor(s)** | Guest |
| **Description** | Guest views full details of a specific homestay unit, including description, images, pricing, check-in/out times, and an availability calendar. |
| **Preconditions** | Guest has selected a unit from the listing. |
| **Postconditions** | Guest can see real-time availability before booking. |
| **PRD Refs** | HS-05 |

---

### UC-HS-03: View House Policies

| Field | Detail |
|---|---|
| **Use Case ID** | UC-HS-03 |
| **Use Case Name** | View House Policies |
| **Actor(s)** | Guest |
| **Description** | Guest views the house rules and policies for a specific homestay unit before booking. |
| **Preconditions** | Unit has at least one active policy. |
| **Postconditions** | Guest is informed of rules they must follow during their stay (e.g., no smoking, no pets). |
| **PRD Refs** | HS-11, URS-U-POL-01 |

---

### UC-HS-04: Create Homestay Unit

| Field | Detail |
|---|---|
| **Use Case ID** | UC-HS-04 |
| **Use Case Name** | Create Homestay Unit |
| **Actor(s)** | Admin |
| **Description** | Admin creates a new homestay unit with all required details. Default policies are automatically applied on creation. |
| **Preconditions** | Admin is logged in with appropriate permission. |
| **Postconditions** | A new unit is saved and visible to guests. Default policies are copied to the unit's policy list. |
| **PRD Refs** | HS-01, HS-10 |

**Main Flow:**
1. Admin navigates to Homestays → Create New Unit.
2. Admin enters name, description, location, base price, deposit amount, check-in/out times, and extension payment window.
3. Admin uploads one or more images.
4. Admin submits the form.
5. System saves the unit and copies system-level default policies (No Pets, No Durians, No Smoking).
6. Unit appears in the admin list and guest listing.

---

### UC-HS-05: Edit Homestay Unit

| Field | Detail |
|---|---|
| **Use Case ID** | UC-HS-05 |
| **Use Case Name** | Edit Homestay Unit |
| **Actor(s)** | Admin |
| **Description** | Admin updates the details of an existing homestay unit. |
| **Preconditions** | The unit exists. Admin has edit permission. |
| **Postconditions** | Unit details are updated system-wide. |
| **PRD Refs** | HS-02 |

---

### UC-HS-06: Deactivate / Delete Unit

| Field | Detail |
|---|---|
| **Use Case ID** | UC-HS-06 |
| **Use Case Name** | Deactivate / Delete Unit |
| **Actor(s)** | Admin |
| **Description** | Admin deactivates or soft-deletes a homestay unit to prevent new bookings. |
| **Preconditions** | Unit has no confirmed future bookings. |
| **Postconditions** | Unit is hidden from guest listing. Existing bookings are unaffected. |
| **PRD Refs** | HS-03 |

**Alternative Flows:**
- **A1 – Unit has future bookings:** System blocks deletion and displays an error listing the conflicting bookings.

---

### UC-HS-07: Upload Unit Images

| Field | Detail |
|---|---|
| **Use Case ID** | UC-HS-07 |
| **Use Case Name** | Upload Unit Images |
| **Actor(s)** | Admin |
| **Description** | Admin uploads one or more images for a homestay unit during creation or editing. |
| **Preconditions** | Admin is on the unit create/edit form. |
| **Postconditions** | Images are stored and displayed on the unit detail page. |
| **PRD Refs** | HS-06 |

---

### UC-HS-08: Set Pricing & Check-in/out Times

| Field | Detail |
|---|---|
| **Use Case ID** | UC-HS-08 |
| **Use Case Name** | Set Pricing & Check-in/out Times |
| **Actor(s)** | Admin |
| **Description** | Admin configures pricing and default check-in/check-out times for a unit during creation or editing. |
| **Preconditions** | Admin is on the unit create/edit form. |
| **Postconditions** | Pricing and time values are saved and applied to new bookings for this unit. |
| **PRD Refs** | HS-04, HS-07, HS-08 |

---

### UC-HS-09: Manage Unit House Policies

| Field | Detail |
|---|---|
| **Use Case ID** | UC-HS-09 |
| **Use Case Name** | Manage Unit House Policies |
| **Actor(s)** | Admin |
| **Description** | Admin adds, edits, or removes individual house policies for a specific homestay unit. |
| **Preconditions** | Unit exists. Admin has edit permission. |
| **Postconditions** | Updated policies are immediately visible to guests viewing the unit. |
| **PRD Refs** | HS-09, HS-10 |

---

### UC-HS-10: Apply Default Policies (On Unit Creation)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-HS-10 |
| **Use Case Name** | Apply Default Policies on Creation |
| **Actor(s)** | System |
| **Description** | When a new homestay unit is created, the system automatically copies all active system-level default policies to the new unit's policy list. |
| **Preconditions** | System default policies are configured (via System Settings). |
| **Postconditions** | New unit has policies pre-populated; admin can modify them afterward. |
| **PRD Refs** | HS-10, HS-12, SET-POL-01, SET-POL-02 |

---

## 3. Booking Module

---

### UC-BK-01: View Availability Calendar

| Field | Detail |
|---|---|
| **Use Case ID** | UC-BK-01 |
| **Use Case Name** | View Availability Calendar |
| **Actor(s)** | Guest |
| **Description** | Guest views the monthly availability calendar for a specific homestay unit before selecting dates. |
| **Preconditions** | Guest has selected a unit. |
| **Postconditions** | Guest can see available, booked, blocked, and held dates highlighted. |
| **PRD Refs** | BK-U-02 |

---

### UC-BK-02: Select Check-in / Check-out Date & Time

| Field | Detail |
|---|---|
| **Use Case ID** | UC-BK-02 |
| **Use Case Name** | Select Check-in / Check-out Date & Time |
| **Actor(s)** | Guest |
| **Description** | Guest selects their desired check-in and check-out dates and times from the availability calendar. The system verifies availability in real time. |
| **Preconditions** | Guest is on the booking form for an active unit. |
| **Postconditions** | Selected dates are validated and passed to the booking summary. |
| **PRD Refs** | BK-U-02, BK-U-03 |

**Alternative Flows:**
- **A1 – Date unavailable:** System highlights the conflict and prompts re-selection.

---

### UC-BK-03: Check Date Availability (Real-time)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-BK-03 |
| **Use Case Name** | Check Date Availability (Real-time) |
| **Actor(s)** | System |
| **Description** | The system verifies in real time that no confirmed or pending bookings, blocked dates, or held dates conflict with the guest's selected range. |
| **Preconditions** | Guest has entered check-in and check-out dates. |
| **Postconditions** | System confirms availability or returns a conflict message. |
| **PRD Refs** | BK-U-03 |

---

### UC-BK-04: Submit Booking

| Field | Detail |
|---|---|
| **Use Case ID** | UC-BK-04 |
| **Use Case Name** | Submit Booking |
| **Actor(s)** | Guest |
| **Description** | Guest submits a booking request for a specific homestay unit on their selected dates. |
| **Preconditions** | Dates are available; guest is logged in. |
| **Postconditions** | Booking is created with `pending_payment` status. A bill is generated. Selected dates are temporarily held. |
| **PRD Refs** | BK-U-04 |

**Main Flow:**
1. Guest reviews the booking summary (unit, dates, price breakdown).
2. Guest confirms and submits the booking.
3. System creates the booking record and generates a bill.
4. System temporarily holds the dates (preventing other bookings).
5. Guest is redirected to the payment page.

---

### UC-BK-05: Temporary Hold (Payment Window)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-BK-05 |
| **Use Case Name** | Temporary Hold (Payment Window) |
| **Actor(s)** | System |
| **Description** | After a booking is submitted, the system places a temporary hold on the selected dates for the configured payment window (default 24 hours) to allow the guest to complete payment. |
| **Preconditions** | Booking has been submitted and is in `pending_payment` status. |
| **Postconditions** | Dates are marked as held; no other guest can book them during this window. |
| **PRD Refs** | BK-H-01 |

---

### UC-BK-06: Auto-Cancel Booking (Payment Timeout)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-BK-06 |
| **Use Case Name** | Auto-Cancel Booking (Payment Timeout) |
| **Actor(s)** | System |
| **Description** | If payment is not received within the configured payment window, the system automatically cancels the booking and releases the held dates. |
| **Preconditions** | Booking is in `pending_payment` status and the payment deadline has passed. |
| **Postconditions** | Booking is cancelled; dates are released; guest is notified. |
| **PRD Refs** | BK-H-01, BK-H-02 |

---

### UC-BK-07: View Current Bookings

| Field | Detail |
|---|---|
| **Use Case ID** | UC-BK-07 |
| **Use Case Name** | View Current Bookings |
| **Actor(s)** | Guest |
| **Description** | Guest views all their active (confirmed/pending) bookings. |
| **Preconditions** | Guest is logged in and has at least one booking. |
| **Postconditions** | Guest can see booking statuses and access booking details. |
| **PRD Refs** | BK-U-05 |

---

### UC-BK-08: View Booking History

| Field | Detail |
|---|---|
| **Use Case ID** | UC-BK-08 |
| **Use Case Name** | View Booking History |
| **Actor(s)** | Guest |
| **Description** | Guest views all past (completed/cancelled) bookings. |
| **Preconditions** | Guest is logged in. |
| **Postconditions** | Guest can review past stays, access receipts, and submit feedback for completed bookings. |
| **PRD Refs** | BK-U-06 |

---

### UC-BK-09: Cancel Booking

| Field | Detail |
|---|---|
| **Use Case ID** | UC-BK-09 |
| **Use Case Name** | Cancel Booking |
| **Actor(s)** | Guest |
| **Description** | Guest cancels a confirmed or pending booking. The system applies the configured refund policy. |
| **Preconditions** | Guest has a cancellable booking. |
| **Postconditions** | Booking status changes to `cancelled`. Refund (if applicable) is initiated. Guest is notified. |
| **PRD Refs** | BK-U-07, BK-U-08 |

**Main Flow:**
1. Guest navigates to the booking detail page and clicks "Cancel Booking."
2. System displays the cancellation policy and estimated refund amount.
3. Guest confirms cancellation.
4. System cancels the booking, releases the dates, and triggers the refund process.

---

### UC-BK-10: Block Dates

| Field | Detail |
|---|---|
| **Use Case ID** | UC-BK-10 |
| **Use Case Name** | Block Dates |
| **Actor(s)** | Admin |
| **Description** | Admin manually blocks specific dates on a homestay unit to prevent bookings (e.g., for maintenance). |
| **Preconditions** | Admin is logged in. The selected dates are not already confirmed-booked. |
| **Postconditions** | Dates appear as unavailable on the guest-facing calendar. |
| **PRD Refs** | BK-A-06 |

---

## 4. Payment Module

---

### UC-PAY-01: Make Payment (Online Gateway)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-PAY-01 |
| **Use Case Name** | Make Payment |
| **Actor(s)** | Guest, Payment Gateway |
| **Description** | Guest pays the outstanding bill for their booking via an online payment gateway. |
| **Preconditions** | Booking exists with `pending_payment` status. A bill has been generated. |
| **Postconditions** | Payment is recorded; booking status updates to `confirmed`; QR code is generated; guest receives a receipt. |
| **PRD Refs** | PAY-U-01, PAY-U-02 |

**Main Flow:**
1. Guest is redirected to the payment gateway.
2. Guest completes payment.
3. Gateway sends a webhook to the system.
4. System verifies the webhook, updates the payment record to `succeeded`, and confirms the booking.
5. System generates the QR code and sends a confirmation notification to the guest.

**Alternative Flows:**
- **A1 – Payment failed:** Gateway notifies system; record updated to `failed`; guest is prompted to retry.

---

### UC-PAY-02: View Payment Bill

| Field | Detail |
|---|---|
| **Use Case ID** | UC-PAY-02 |
| **Use Case Name** | View Payment Bill |
| **Actor(s)** | Guest |
| **Description** | Guest views the bill generated for their booking, showing itemised charges. |
| **Preconditions** | A bill has been generated for the booking. |
| **Postconditions** | Guest can review total amount due before paying. |
| **PRD Refs** | PAY-U-01 |

---

### UC-PAY-03: View / Download Receipt

| Field | Detail |
|---|---|
| **Use Case ID** | UC-PAY-03 |
| **Use Case Name** | View / Download Receipt |
| **Actor(s)** | Guest |
| **Description** | Guest views or downloads a PDF receipt after successful payment. |
| **Preconditions** | Payment has been confirmed (`succeeded`). |
| **Postconditions** | Guest has a payment receipt for their records. |
| **PRD Refs** | PAY-U-03 |

---

### UC-PAY-04: Process Payment Webhook

| Field | Detail |
|---|---|
| **Use Case ID** | UC-PAY-04 |
| **Use Case Name** | Process Payment Webhook |
| **Actor(s)** | Payment Gateway, System |
| **Description** | The payment gateway sends an HTTP callback to the system to notify it of a payment outcome. The system verifies the signature and updates records accordingly. |
| **Preconditions** | A payment was initiated and the gateway processed it. |
| **Postconditions** | Payment status updated; booking confirmed or kept pending; audit log entry created. |
| **PRD Refs** | PAY-A-04 |

---

### UC-PAY-05: Regenerate Bill / Receipt

| Field | Detail |
|---|---|
| **Use Case ID** | UC-PAY-05 |
| **Use Case Name** | Regenerate Bill / Receipt |
| **Actor(s)** | Admin |
| **Description** | Admin regenerates a bill or receipt PDF for a guest on demand. |
| **Preconditions** | The booking and payment records exist. |
| **Postconditions** | A fresh bill or receipt PDF is generated and available for download or re-sending. |
| **PRD Refs** | PAY-A-03 |

---

## 5. Notification Module

---

### UC-NOTIF-01: Receive In-App Notification

| Field | Detail |
|---|---|
| **Use Case ID** | UC-NOTIF-01 |
| **Use Case Name** | Receive In-App Notification |
| **Actor(s)** | Guest, Admin |
| **Description** | Users receive real-time in-app notifications (bell icon with badge) for system events such as booking confirmation, payment received, extension updates, and more. |
| **Preconditions** | User is logged in. A relevant system event has occurred. |
| **Postconditions** | Notification appears in the user's notification list; badge count increments. |
| **PRD Refs** | NOTIF-01, NOTIF-02 |

---

### UC-NOTIF-02: Receive Email Notification

| Field | Detail |
|---|---|
| **Use Case ID** | UC-NOTIF-02 |
| **Use Case Name** | Receive Email Notification |
| **Actor(s)** | Guest, Admin |
| **Description** | System sends email notifications for key events (booking confirmation, payment receipt, cancellation, extension bill). Email delivery can be toggled system-wide by Admin. |
| **Preconditions** | SMTP is configured and `notification.email_enabled` is `true`. |
| **Postconditions** | Email is delivered to user's inbox. |
| **PRD Refs** | NOTIF-02, SET-GEN-04 |

---

### UC-NOTIF-03: Receive Payment Reminder

| Field | Detail |
|---|---|
| **Use Case ID** | UC-NOTIF-03 |
| **Use Case Name** | Receive Payment Reminder |
| **Actor(s)** | Guest |
| **Description** | The system automatically sends reminders to guests with pending payments to complete their payment before the deadline. |
| **Preconditions** | Booking is in `pending_payment` status and deadline is approaching. |
| **Postconditions** | Guest is reminded via in-app and/or email notification. |
| **PRD Refs** | NOTIF-04 |

---

### UC-NOTIF-04: View Booking in Google Calendar

| Field | Detail |
|---|---|
| **Use Case ID** | UC-NOTIF-04 |
| **Use Case Name** | View Booking in Google Calendar |
| **Actor(s)** | Guest, Admin |
| **Description** | Confirmed bookings are synchronised to the user's Google Calendar, showing check-in and check-out as calendar events. |
| **Preconditions** | User has connected their Google account. Booking is confirmed. |
| **Postconditions** | Calendar event is created/updated in Google Calendar via the Google Calendar API. |
| **PRD Refs** | NOTIF-07 |

---

## 6. Chat Module

---

### UC-CHAT-01: Send Message

| Field | Detail |
|---|---|
| **Use Case ID** | UC-CHAT-01 |
| **Use Case Name** | Send Message |
| **Actor(s)** | Guest, Admin |
| **Description** | User sends a text message in real time to the other party (Guest ↔ Admin) via the built-in chat system. |
| **Preconditions** | Both parties have accounts. User is logged in. |
| **Postconditions** | Message is stored and delivered to the recipient in real time via WebSocket (Laravel Reverb). |
| **PRD Refs** | CHAT-01, CHAT-02 |

---

### UC-CHAT-02: Receive Message (Real-time)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-CHAT-02 |
| **Use Case Name** | Receive Message (Real-time) |
| **Actor(s)** | Guest, Admin |
| **Description** | User receives a new message instantly via WebSocket without needing to refresh the page. A typing indicator is shown while the other party composes. |
| **Preconditions** | Recipient is logged in and the chat page is active. |
| **Postconditions** | Message appears in the chat window; unread badge updates if chat is not in focus. |
| **PRD Refs** | CHAT-02 |

---

### UC-CHAT-03: View Chat History

| Field | Detail |
|---|---|
| **Use Case ID** | UC-CHAT-03 |
| **Use Case Name** | View Chat History |
| **Actor(s)** | Guest, Admin |
| **Description** | User views the full conversation history between the guest and admin. |
| **Preconditions** | At least one message has been exchanged. |
| **Postconditions** | All historical messages are displayed in chronological order. |
| **PRD Refs** | CHAT-03 |

---

## 7. User Management Module

---

### UC-USR-01: Create User Account

| Field | Detail |
|---|---|
| **Use Case ID** | UC-USR-01 |
| **Use Case Name** | Create User Account |
| **Actor(s)** | Admin |
| **Description** | Admin manually creates a new user account and assigns a role. |
| **Preconditions** | Email address is not already registered. |
| **Postconditions** | Account is created; user is notified with a temporary password; `must_change_password` is set to `true`. |
| **PRD Refs** | USR-01 |

---

### UC-USR-02: Edit User Account

| Field | Detail |
|---|---|
| **Use Case ID** | UC-USR-02 |
| **Use Case Name** | Edit User Account |
| **Actor(s)** | Admin |
| **Description** | Admin updates an existing user's name, email, or role assignment. |
| **Preconditions** | User account exists. |
| **Postconditions** | User details are updated in the system. |
| **PRD Refs** | USR-02 |

---

### UC-USR-03: Activate / Deactivate User  

| Field | Detail |
|---|---|
| **Use Case ID** | UC-USR-03 |
| **Use Case Name** | Activate / Deactivate User Account |
| **Actor(s)** | Admin |
| **Description** | Admin toggles a user's active status to grant or revoke system access. |
| **Preconditions** | User account exists. |
| **Postconditions** | If deactivated, user cannot log in. If reactivated, user can log in again. |
| **PRD Refs** | USR-03 |

---

### UC-USR-04: Reset User Password

| Field | Detail |
|---|---|
| **Use Case ID** | UC-USR-04 |
| **Use Case Name** | Reset User Password |
| **Actor(s)** | Admin |
| **Description** | Admin resets a user's password and forces them to change it on next login. |
| **Preconditions** | User account exists. |
| **Postconditions** | Password is reset; `must_change_password = true`; user is notified. |
| **PRD Refs** | USR-04 |

**Main Flow:**
1. Admin selects a user and clicks "Reset Password."
2. System generates a temporary password or sends a reset link.
3. System sets `must_change_password = true` on the user's record.
4. User receives notification with instructions.

---

## 8. Role & Permission Module

---

### UC-ROLE-01: Create / Edit / Delete Role

| Field | Detail |
|---|---|
| **Use Case ID** | UC-ROLE-01 |
| **Use Case Name** | Create / Edit / Delete Role |
| **Actor(s)** | Admin |
| **Description** | Admin manages roles (e.g., "Admin", "Guest") that group permissions together and can be assigned to users. |
| **Preconditions** | Admin is logged in with role management permission. |
| **Postconditions** | Role is created, updated, or deleted in the system. |
| **PRD Refs** | ROLE-01, ROLE-02 |

**Alternative Flows:**
- **A1 – Delete role assigned to users:** System blocks deletion and shows which users are affected (UC guards: Prevent Delete Role).

---

### UC-ROLE-02: Assign Permissions to Role

| Field | Detail |
|---|---|
| **Use Case ID** | UC-ROLE-02 |
| **Use Case Name** | Assign Permissions to Role |
| **Actor(s)** | Admin |
| **Description** | Admin assigns or revokes specific permissions for a role, controlling what actions users with that role can perform. |
| **Preconditions** | Role and permissions exist. |
| **Postconditions** | Role's permission set is updated. All users with this role gain or lose the affected permissions immediately. |
| **PRD Refs** | ROLE-03 |

---

### UC-ROLE-03: Create / Edit / Delete Permission

| Field | Detail |
|---|---|
| **Use Case ID** | UC-ROLE-03 |
| **Use Case Name** | Create / Edit / Delete Permission |
| **Actor(s)** | Admin |
| **Description** | Admin manages individual permission keys (e.g., `bookings.cancel`, `qr.extend`). |
| **Preconditions** | Admin is logged in with permission management access. |
| **Postconditions** | Permission is created, updated, or deleted. |
| **PRD Refs** | PERM-01, PERM-02 |

**Alternative Flows:**
- **A1 – Delete permission attached to a role:** System blocks deletion to prevent orphaned role configurations.

---

## 9. System Settings Module

---

### UC-SET-01: Configure SMTP Settings

| Field | Detail |
|---|---|
| **Use Case ID** | UC-SET-01 |
| **Use Case Name** | Configure SMTP Settings |
| **Actor(s)** | Admin |
| **Description** | Admin configures the outgoing email server settings (host, port, username, password, encryption) used for all system email delivery. |
| **Preconditions** | Admin is logged in. |
| **Postconditions** | SMTP credentials saved in the `settings` table; system uses them for subsequent emails. |
| **PRD Refs** | SET-SMTP-01 |

---

### UC-SET-02: Configure Security Settings

| Field | Detail |
|---|---|
| **Use Case ID** | UC-SET-02 |
| **Use Case Name** | Configure Security Settings |
| **Actor(s)** | Admin |
| **Description** | Admin configures account lockout duration, session timeout, and maximum failed login attempts. All values are stored in the settings table and applied dynamically. |
| **Preconditions** | Admin is logged in. |
| **Postconditions** | New security parameters take effect for all subsequent login attempts and sessions. |
| **PRD Refs** | SET-SEC-01, SET-SEC-02, SET-SEC-03 |

---

### UC-SET-03: Configure Refund Policy Parameters

| Field | Detail |
|---|---|
| **Use Case ID** | UC-SET-03 |
| **Use Case Name** | Configure Refund Policy Parameters |
| **Actor(s)** | Admin |
| **Description** | Admin sets refund percentage thresholds for cancellations at different time windows (e.g., <3 days = 0%, <1 week = 25%, <2 weeks = 50%). |
| **Preconditions** | Admin is logged in. |
| **Postconditions** | New refund values are stored and applied to future cancellations. |
| **PRD Refs** | SET-GEN-02 |

---

### UC-SET-04: Configure Extension Charge Rates

| Field | Detail |
|---|---|
| **Use Case ID** | UC-SET-04 |
| **Use Case Name** | Configure Extension Charge Rates |
| **Actor(s)** | Admin |
| **Description** | Admin sets the extra charge rate per hour (for time extension) and per additional night (for date extension). These rates are applied when calculating extension bills. |
| **Preconditions** | Admin is logged in. |
| **Postconditions** | Extension charge rates are stored in `settings` and used for all future extension billing. |
| **PRD Refs** | SET-EXT-01, SET-EXT-02, SET-EXT-03 |

---

### UC-SET-05: Configure Extension Payment Window

| Field | Detail |
|---|---|
| **Use Case ID** | UC-SET-05 |
| **Use Case Name** | Configure Extension Payment Window |
| **Actor(s)** | Admin |
| **Description** | Admin sets the system-wide default number of minutes a guest has to pay an extension charge before it is automatically cancelled. Default is 60 minutes. |
| **Preconditions** | Admin is logged in. |
| **Postconditions** | `extension.payment_window_minutes` stored in `settings`; used as fallback for units without a per-unit override. |
| **PRD Refs** | SET-EXT-04, URS-A-SET-08 |

---

### UC-SET-06: Manage Default Homestay Policies

| Field | Detail |
|---|---|
| **Use Case ID** | UC-SET-06 |
| **Use Case Name** | Manage Default Homestay Policies |
| **Actor(s)** | Admin |
| **Description** | Admin manages the list of system-level default policies that are automatically applied to every new homestay unit upon creation. |
| **Preconditions** | Admin is logged in. |
| **Postconditions** | Updated default policies are stored in `settings` under `policy.defaults` and applied to all future new units. |
| **PRD Refs** | SET-POL-01, SET-POL-02 |

---

## 10. Audit Logs Module

---

### UC-AUDIT-01: View Audit Trail

| Field | Detail |
|---|---|
| **Use Case ID** | UC-AUDIT-01 |
| **Use Case Name** | View Audit Trail |
| **Actor(s)** | Admin |
| **Description** | Admin views a chronological log of all system activities including user actions, admin changes, authentication events, and system events. |
| **Preconditions** | Admin is logged in with audit log access permission. |
| **Postconditions** | Admin can review historical activity for security and compliance purposes. |
| **PRD Refs** | AUDIT-01, AUDIT-02, AUDIT-03 |

---

### UC-AUDIT-02: Filter Audit Logs

| Field | Detail |
|---|---|
| **Use Case ID** | UC-AUDIT-02 |
| **Use Case Name** | Filter Audit Logs |
| **Actor(s)** | Admin |
| **Description** | Admin filters the audit log by date range, event type, or actor to quickly find relevant entries. |
| **Preconditions** | Admin is on the Audit Logs page. |
| **Postconditions** | Filtered log entries are displayed. |
| **PRD Refs** | AUDIT-01 |

---

### UC-AUDIT-03: Automatic Event Logging

| Field | Detail |
|---|---|
| **Use Case ID** | UC-AUDIT-03 |
| **Use Case Name** | Automatic Event Logging |
| **Actor(s)** | System |
| **Description** | The system automatically logs all key events (user actions, admin changes, login/logout, system events) to the audit trail using `spatie/laravel-activitylog`. Logs are read-only and cannot be modified or deleted. |
| **Preconditions** | A loggable event occurs anywhere in the system. |
| **Postconditions** | An immutable audit record is created with actor, action, target, and timestamp. |
| **PRD Refs** | AUDIT-02, AUDIT-03 |

---

## 11. QR Code Door Access Module

---

### UC-QR-01: Receive QR Code (Upon Booking Confirmation)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-QR-01 |
| **Use Case Name** | Receive QR Code Upon Booking Confirmation |
| **Actor(s)** | Guest |
| **Description** | After a booking is confirmed (payment received), the system automatically generates a unique QR code and delivers it to the guest via in-app notification and email. |
| **Preconditions** | Booking status has changed to `confirmed` after successful payment. |
| **Postconditions** | A unique QR code is stored in `qr_codes` and accessible to the guest on their booking detail page. |
| **PRD Refs** | QR-01, QR-06 |

---

### UC-QR-02: Use QR Code for Door Access

| Field | Detail |
|---|---|
| **Use Case ID** | UC-QR-02 |
| **Use Case Name** | Use QR Code for Door Access |
| **Actor(s)** | Guest |
| **Description** | Guest presents their QR code at the homestay door to gain physical access during the valid period (check-in to check-out). |
| **Preconditions** | QR code status is `active` and current time is within `valid_from` and `valid_until`. |
| **Postconditions** | Guest gains access to the property. |
| **PRD Refs** | QR-02 |

**Alternative Flows:**
- **A1 – QR code expired or invalid:** Access is denied.

---

### UC-QR-03: Auto-Invalidate QR Code (After Check-out)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-QR-03 |
| **Use Case Name** | Auto-Invalidate QR Code After Check-out |
| **Actor(s)** | System |
| **Description** | The system automatically invalidates the guest's QR code when the check-out time has passed. |
| **Preconditions** | Current time exceeds `qr_codes.valid_until`. |
| **Postconditions** | QR code status set to `expired`; guest can no longer use it for access. |
| **PRD Refs** | QR-03 |

---

### UC-QR-04: Regenerate QR Code (For Housekeeping)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-QR-04 |
| **Use Case Name** | Regenerate QR Code for Housekeeping |
| **Actor(s)** | Admin |
| **Description** | Admin manually regenerates a temporary QR code for housekeeping staff to access the property between bookings. |
| **Preconditions** | Previous booking has ended or QR has been invalidated. |
| **Postconditions** | A new housekeeping QR code is generated with a limited validity window. |
| **PRD Refs** | QR-04 |

---

### UC-QR-05: Auto-Generate QR Code (For Next Guest)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-QR-05 |
| **Use Case Name** | Auto-Generate QR Code for Next Guest |
| **Actor(s)** | System |
| **Description** | After admin marks housekeeping as complete, the system automatically generates a new QR code for the next confirmed booking on that unit. |
| **Preconditions** | Housekeeping is marked complete. A next confirmed booking exists. |
| **Postconditions** | New QR code is generated for the upcoming guest's booking. |
| **PRD Refs** | QR-05 |

---

### UC-QR-06: Initiate Booking Extension (Time or Date)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-QR-06 |
| **Use Case Name** | Initiate Booking Extension |
| **Actor(s)** | Admin |
| **Description** | Admin initiates an extension request on behalf of a guest who has requested to extend their check-out time (same day) or extend their booking by additional nights. The system checks availability, calculates the charge, creates an extension record, and notifies the guest to pay. |
| **Preconditions** | Booking is active (`confirmed`). No conflicting bookings exist for the extended period. |
| **Postconditions** | A `booking_extensions` record is created with `pending_payment` status. Extension bill is generated. Guest is notified. QR code is NOT yet updated. |
| **PRD Refs** | QR-07, QR-08, QR-09, QR-10 |

**Main Flow:**
1. Admin opens a booking and clicks "Extend Stay."
2. Admin inputs the new check-out date/time and selects extension type (time or date).
3. System checks availability for the extended period.
4. System calculates the extra charge (rate from `settings`).
5. System creates a `booking_extensions` record with `pending_payment` status.
6. System generates an additional charge bill.
7. System sets a payment deadline based on the unit's `extension_payment_window_minutes`.
8. System notifies the guest (in-app and email) with the bill amount and deadline.

**Alternative Flows:**
- **A1 – Availability conflict:** System displays conflicting booking details; admin selects a different date/time.

---

### UC-QR-07: Generate Extension Bill & Set Payment Deadline

| Field | Detail |
|---|---|
| **Use Case ID** | UC-QR-07 |
| **Use Case Name** | Generate Extension Bill & Set Payment Deadline |
| **Actor(s)** | System |
| **Description** | Upon extension initiation, the system calculates the total extra charge and generates a bill. The payment deadline is calculated using the relevant unit's `extension_payment_window_minutes` (fallback: system-wide default of 60 minutes). |
| **Preconditions** | Extension has been initiated by admin. Extension type and new checkout details are known. |
| **Postconditions** | Extension bill created. `payment_deadline` set on the `booking_extensions` record. |
| **PRD Refs** | QR-09, QR-10, QR-11, HS-13, SET-EXT-04 |

---

### UC-QR-08: Notify Guest of Extension Bill & Deadline

| Field | Detail |
|---|---|
| **Use Case ID** | UC-QR-08 |
| **Use Case Name** | Notify Guest of Extension Bill & Deadline |
| **Actor(s)** | System |
| **Description** | System sends the guest an in-app and email notification with the extension charge amount and the deadline by which they must pay to confirm the extension. |
| **Preconditions** | Extension record is in `pending_payment` status. |
| **Postconditions** | Guest is informed and can proceed to payment. |
| **PRD Refs** | QR-11, URS-U-EXT-01, URS-U-EXT-02 |

---

### UC-QR-09: Guest Pays Extension Charge

| Field | Detail |
|---|---|
| **Use Case ID** | UC-QR-09 |
| **Use Case Name** | Guest Pays Extension Charge |
| **Actor(s)** | Guest |
| **Description** | Guest pays the additional charge via the online payment gateway within the configured payment window to confirm the booking extension. |
| **Preconditions** | Extension is in `pending_payment` status. Payment deadline has not yet passed. |
| **Postconditions** | Payment recorded; extension status moves to `confirmed`; booking dates updated; QR code validity extended. |
| **PRD Refs** | QR-11, QR-14 |

---

### UC-QR-10: Confirm Extension (Update Booking + QR)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-QR-10 |
| **Use Case Name** | Confirm Extension (Update Booking + QR) |
| **Actor(s)** | System |
| **Description** | After the extension payment is confirmed, the system updates the booking record with new check-out dates/times and extends the QR code's `valid_until` timestamp accordingly. |
| **Preconditions** | Extension payment has been confirmed (webhook received). |
| **Postconditions** | `booking_extensions.status = confirmed`. Booking updated. QR `valid_until` updated. Guest notified of success. |
| **PRD Refs** | QR-12, QR-14 |

---

### UC-QR-11: Auto-Cancel Extension (Deadline Expired)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-QR-11 |
| **Use Case Name** | Auto-Cancel Extension (Deadline Expired) |
| **Actor(s)** | System |
| **Description** | A scheduled job runs every 1–5 minutes to check for extension records whose payment deadline has passed without a confirmed payment. Such extensions are automatically cancelled. |
| **Preconditions** | `booking_extensions.status = pending_payment` and `payment_deadline < now()`. |
| **Postconditions** | Extension status set to `cancelled`. Booking dates reverted to original. QR code NOT updated. Guest notified. |
| **PRD Refs** | QR-13, URS-U-EXT-03 |

---

### UC-QR-12: Revert Booking to Original Dates/Times

| Field | Detail |
|---|---|
| **Use Case ID** | UC-QR-12 |
| **Use Case Name** | Revert Booking to Original Dates/Times |
| **Actor(s)** | System |
| **Description** | When an extension is cancelled (deadline expired without payment), the system reverts the booking record to the original check-out date and time stored in the `booking_extensions` record. |
| **Preconditions** | Extension has been cancelled. `original_check_out_date` and `original_check_out_time` are stored. |
| **Postconditions** | Booking check-out date/time match original values. QR code `valid_until` remains at original checkout time. |
| **PRD Refs** | QR-13 |

---

### UC-QR-13: Configure Per-Unit Extension Payment Window

| Field | Detail |
|---|---|
| **Use Case ID** | UC-QR-13 |
| **Use Case Name** | Configure Per-Unit Extension Payment Window |
| **Actor(s)** | Admin |
| **Description** | Admin sets a custom extension payment window (in minutes) for a specific homestay unit. When set, this overrides the system-wide default when calculating the payment deadline for extension requests on this unit. |
| **Preconditions** | Admin is on the homestay unit edit page. |
| **Postconditions** | `homestays.extension_payment_window_minutes` updated. Future extension bills for this unit use the new window. |
| **PRD Refs** | HS-13, URS-A-HS-09, URS-A-QR-07 |

---

## 12. Reporting & Analytics Module

---

### UC-RPT-01: View Analytics Dashboard

| Field | Detail |
|---|---|
| **Use Case ID** | UC-RPT-01 |
| **Use Case Name** | View Analytics Dashboard |
| **Actor(s)** | Admin |
| **Description** | Admin views the main reporting dashboard displaying key performance indicators: total bookings, total revenue, occupancy rate, cancellation rate, and a summary of guest feedback ratings. |
| **Preconditions** | Admin is logged in with reporting access. |
| **Postconditions** | Dashboard loads with current data aggregated from the database. |
| **PRD Refs** | RPT-01, RPT-02 |

---

### UC-RPT-02: View Booking Trends Chart

| Field | Detail |
|---|---|
| **Use Case ID** | UC-RPT-02 |
| **Use Case Name** | View Booking Trends Chart |
| **Actor(s)** | Admin |
| **Description** | Admin views a line chart showing the number of bookings over time. Admin can toggle between daily, weekly, and monthly granularity. |
| **Preconditions** | Admin is on the analytics dashboard. |
| **Postconditions** | Chart renders using Chart.js based on data from the `bookings` table. |
| **PRD Refs** | RPT-03 |

---

### UC-RPT-03: View Revenue Report

| Field | Detail |
|---|---|
| **Use Case ID** | UC-RPT-03 |
| **Use Case Name** | View Revenue Report |
| **Actor(s)** | Admin |
| **Description** | Admin views a detailed revenue report with filtering by date range, homestay unit, and payment status. |
| **Preconditions** | Payment records exist in the system. |
| **Postconditions** | Filtered revenue totals are displayed. Admin can export to PDF or CSV. |
| **PRD Refs** | RPT-04 |

---

### UC-RPT-04: View Per-Unit Booking Breakdown

| Field | Detail |
|---|---|
| **Use Case ID** | UC-RPT-04 |
| **Use Case Name** | View Per-Unit Booking Breakdown |
| **Actor(s)** | Admin |
| **Description** | Admin views a breakdown of bookings segmented by homestay unit, showing totals and comparisons across units. |
| **Preconditions** | Multiple homestay units have booking records. |
| **Postconditions** | Bar chart and table show bookings per unit. |
| **PRD Refs** | RPT-05 |

---

### UC-RPT-05: View Feedback & Rating Summary

| Field | Detail |
|---|---|
| **Use Case ID** | UC-RPT-05 |
| **Use Case Name** | View Feedback & Rating Summary |
| **Actor(s)** | Admin |
| **Description** | Admin views an aggregated summary of guest ratings per unit (average score, total reviews, rating distribution). |
| **Preconditions** | Feedback records exist and are visible. |
| **Postconditions** | Rating summary with visual breakdown rendered on the dashboard. |
| **PRD Refs** | RPT-06 |

---

### UC-RPT-06: Export Report (PDF / CSV)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-RPT-06 |
| **Use Case Name** | Export Report |
| **Actor(s)** | Admin |
| **Description** | Admin exports any report view (revenue, bookings, feedback summary) as a downloadable PDF or CSV file. |
| **Preconditions** | Admin is viewing a report page. |
| **Postconditions** | File is generated and downloaded to the admin's device. |
| **PRD Refs** | RPT-07 |

---

## 13. Guest Feedback Module

---

### UC-FB-01: Submit Rating & Feedback (After Stay Completed)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-FB-01 |
| **Use Case Name** | Submit Rating & Feedback |
| **Actor(s)** | Guest |
| **Description** | After their stay is completed, a guest submits a star rating (1–5) and an optional written comment for the homestay unit they stayed in. |
| **Preconditions** | Booking status is `completed`. No feedback has been submitted for this booking yet. |
| **Postconditions** | Feedback record created in `feedbacks` table with `is_visible = true`. Average rating for the unit is updated. |
| **PRD Refs** | FB-U-01, FB-U-02, FB-U-03 |

**Main Flow:**
1. Guest navigates to My Bookings → Completed stays.
2. Guest clicks "Leave a Review" on a completed booking.
3. Guest selects a star rating (required) and optionally writes a comment.
4. Guest submits the form.
5. System saves the feedback and updates the unit's average rating.
6. Guest sees a thank-you confirmation.

**Alternative Flows:**
- **A1 – Rating not selected:** System highlights the star rating as required and blocks submission.
- **A2 – Feedback already submitted:** "Leave a Review" button is hidden; existing review is shown.

---

### UC-FB-02: View Submitted Feedback

| Field | Detail |
|---|---|
| **Use Case ID** | UC-FB-02 |
| **Use Case Name** | View Submitted Feedback |
| **Actor(s)** | Guest |
| **Description** | Guest views all feedback they have previously submitted, including the rating, comment, date, and any admin reply. |
| **Preconditions** | Guest has at least one submitted feedback. |
| **Postconditions** | Guest's own feedback entries are displayed in read-only format. |
| **PRD Refs** | FB-U-04 |

---

### UC-FB-03: View All Unit Feedback (Admin)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-FB-03 |
| **Use Case Name** | View All Unit Feedback |
| **Actor(s)** | Admin |
| **Description** | Admin views all feedback and ratings submitted for a specific homestay unit or across all units. |
| **Preconditions** | Admin is logged in with feedback management access. |
| **Postconditions** | Admin can see all reviews, ratings, visibility status, and any existing admin replies. |
| **PRD Refs** | FB-A-01 |

---

### UC-FB-04: Respond to Feedback

| Field | Detail |
|---|---|
| **Use Case ID** | UC-FB-04 |
| **Use Case Name** | Respond to Feedback |
| **Actor(s)** | Admin |
| **Description** | Admin writes and publishes a reply to a guest's feedback. The reply is shown alongside the original review. |
| **Preconditions** | Feedback exists and has no existing reply, or admin is editing an existing reply. |
| **Postconditions** | `admin_reply` and `replied_at` fields are populated on the feedback record. Reply is visible to guest. |
| **PRD Refs** | FB-A-02 |

---

### UC-FB-05: Moderate / Hide Feedback

| Field | Detail |
|---|---|
| **Use Case ID** | UC-FB-05 |
| **Use Case Name** | Moderate / Hide Feedback |
| **Actor(s)** | Admin |
| **Description** | Admin hides a feedback entry that violates content policies (e.g., offensive language). The feedback is no longer visible to guests on the listing page but is retained in the database. |
| **Preconditions** | Feedback exists with `is_visible = true`. |
| **Postconditions** | `is_visible` set to `false`; feedback removed from public listing; average rating recalculated excluding hidden reviews. |
| **PRD Refs** | FB-A-03 |

---

### UC-FB-06: Display Average Rating on Listing Page

| Field | Detail |
|---|---|
| **Use Case ID** | UC-FB-06 |
| **Use Case Name** | Display Average Rating on Listing Page |
| **Actor(s)** | System |
| **Description** | The system calculates and displays the average star rating for each homestay unit on the listing and unit detail pages, derived only from visible (`is_visible = true`) feedback records. |
| **Preconditions** | At least one visible feedback record exists for the unit. |
| **Postconditions** | Average rating is shown (e.g., "4.2 ★ from 18 reviews") on the unit card and detail page. |
| **PRD Refs** | FB-A-04 |

---

*End of Use Case Descriptions — HomeLodge v1.0*
