# Chapter 8: Test Cases (STD) + Appendix A â€” HomeLodge Booking Homestay System

This chapter lists the test cases for the HomeLodge system, organised by subsystem and use case. For each test case, the input data, expected output, and step-by-step procedure are provided.

> **Note:** "Actual Results" and "Pass/Fail" columns are to be filled in during testing.

---

## 8.1 TC001: Test Authentication Subsystem: User Registration (UC-AUTH-01)

This test contains the following test cases:
1. TC001_01: Test User Registration â€” Successful (SD-AUTH-01)
2. TC001_02: Test User Registration â€” Duplicate Email
3. TC001_03: Test User Registration â€” Invalid Password

### 8.1.1 TC001_01: Test User Registration â€” Successful (SD-AUTH-01)

| Field | Detail |
| :--- | :--- |
| **Test Case ID** | TC001_01 |
| **Test Case Name** | Successful User Registration |
| **Prerequisites** | The email "newguest@example.com" is not registered in the system. |
| **Test Data** | Name: "John Doe", Email: "newguest@example.com", Password: "Pass@123", Confirm Password: "Pass@123" |

| Step # | Step Details | Expected Results | Actual Results | Pass/Fail |
| :--- | :--- | :--- | :--- | :--- |
| 1 | Navigate to the registration page. | The registration form is displayed with fields: Name, Email, Password, Confirm Password. | | |
| 2 | Fill in all fields with the test data. | All fields accept input. Password shows real-time strength indicator. | | |
| 3 | Click the "Register" button. | A success message is displayed: "Registration successful." The user is redirected to the login page or dashboard. A verification email is sent. | | |
| 4 | Check the database. | A new User record exists with email "newguest@example.com", role "Guest", status "active". | | |

### 8.1.2 TC001_02: Test User Registration â€” Duplicate Email

| Field | Detail |
| :--- | :--- |
| **Test Case ID** | TC001_02 |
| **Test Case Name** | Registration with Existing Email |
| **Prerequisites** | The email "existing@example.com" is already registered. |
| **Test Data** | Name: "Jane Doe", Email: "existing@example.com", Password: "Pass@123", Confirm Password: "Pass@123" |

| Step # | Step Details | Expected Results | Actual Results | Pass/Fail |
| :--- | :--- | :--- | :--- | :--- |
| 1 | Navigate to the registration page. | The registration form is displayed. | | |
| 2 | Fill in all fields with the test data. | All fields accept input. | | |
| 3 | Click the "Register" button. | An error message is displayed below the email field: "The email has already been taken." No new record is created. | | |

### 8.1.3 TC001_03: Test User Registration â€” Invalid Password

| Field | Detail |
| :--- | :--- |
| **Test Case ID** | TC001_03 |
| **Test Case Name** | Registration with Invalid Password |
| **Prerequisites** | None. |
| **Test Data** | Name: "Test User", Email: "test@example.com", Password: "weak", Confirm Password: "weak" |

| Step # | Step Details | Expected Results | Actual Results | Pass/Fail |
| :--- | :--- | :--- | :--- | :--- |
| 1 | Navigate to the registration page. | The registration form is displayed. | | |
| 2 | Enter "weak" in the password field. | The real-time password indicator shows unmet criteria (uppercase, digit, special character, minimum length). | | |
| 3 | Click the "Register" button. | Validation errors appear below the password field listing all unmet criteria. The submit is rejected. | | |

---

## 8.2 TC002: Test Authentication Subsystem: User Login (UC-AUTH-02)

This test contains the following test cases:
1. TC002_01: Test Successful Login (SD-AUTH-02)
2. TC002_02: Test Failed Login â€” Wrong Password
3. TC002_03: Test Account Lockout

### 8.2.1 TC002_01: Test Successful Login (SD-AUTH-02)

| Field | Detail |
| :--- | :--- |
| **Test Case ID** | TC002_01 |
| **Test Case Name** | Successful User Login |
| **Prerequisites** | User "guest@example.com" exists with password "Pass@123", status is "active". |
| **Test Data** | Email: "guest@example.com", Password: "Pass@123" |

| Step # | Step Details | Expected Results | Actual Results | Pass/Fail |
| :--- | :--- | :--- | :--- | :--- |
| 1 | Navigate to the login page. | The login form is displayed. | | |
| 2 | Enter the test credentials. | Fields accept input. | | |
| 3 | Click "Login". | The user is redirected to the guest dashboard (Landing Page). A session is created. | | |

### 8.2.2 TC002_02: Test Failed Login â€” Wrong Password

| Field | Detail |
| :--- | :--- |
| **Test Case ID** | TC002_02 |
| **Test Case Name** | Login with Wrong Password |
| **Prerequisites** | User "guest@example.com" exists, max_attempts = 5, current failed_login_count = 0. |
| **Test Data** | Email: "guest@example.com", Password: "WrongPass1!" |

| Step # | Step Details | Expected Results | Actual Results | Pass/Fail |
| :--- | :--- | :--- | :--- | :--- |
| 1 | Navigate to the login page. | The login form is displayed. | | |
| 2 | Enter incorrect credentials. | Fields accept input. | | |
| 3 | Click "Login". | An error message is displayed: "Invalid credentials. 4 attempts remaining." The failed_login_count increments to 1. | | |

### 8.2.3 TC002_03: Test Account Lockout

| Field | Detail |
| :--- | :--- |
| **Test Case ID** | TC002_03 |
| **Test Case Name** | Account Lockout After Max Failed Attempts |
| **Prerequisites** | User "guest@example.com" exists, max_attempts = 5, failed_login_count = 4 (one more attempt triggers lockout). |
| **Test Data** | Email: "guest@example.com", Password: "WrongPass1!" |

| Step # | Step Details | Expected Results | Actual Results | Pass/Fail |
| :--- | :--- | :--- | :--- | :--- |
| 1 | Attempt login with wrong password. | Error: "Your account has been locked. Please try again after X minutes." The user status changes to "locked", locked_until is set. | | |
| 2 | Attempt login again with correct password. | Error: "Your account is locked until [time]." Login is denied. | | |
| 3 | Wait for lockout duration to expire. | After the lockout period, attempt login with correct credentials. Login succeeds. | | |

---

## 8.3 TC003: Test Homestay Management Subsystem: Create Unit (UC-HS-03)

This test contains the following test cases:
1. TC003_01: Test Create Homestay Unit â€” Successful (SD-HS-03)
2. TC003_02: Test Delete Unit with Future Bookings (SD-HS-05)

### 8.3.1 TC003_01: Test Create Homestay Unit â€” Successful (SD-HS-03)

| Field | Detail |
| :--- | :--- |
| **Test Case ID** | TC003_01 |
| **Test Case Name** | Successful Homestay Unit Creation |
| **Prerequisites** | Admin is logged in. Default house policies (No Pets, No Durians, No Smoking) exist in system settings. |
| **Test Data** | Name: "Unit A", Description: "A cozy unit", Location: "Johor Bahru", Base Price: 150.00, Deposit: 50.00, Check-in: 15:00, Check-out: 12:00, Images: 2 files |

| Step # | Step Details | Expected Results | Actual Results | Pass/Fail |
| :--- | :--- | :--- | :--- | :--- |
| 1 | Navigate to Homestays â†’ Create New. | The creation form is displayed. | | |
| 2 | Fill in all fields with test data and upload images. | All fields accept input. Images preview correctly. | | |
| 3 | Click "Save". | Success toast: "Homestay unit created successfully." The unit appears in the list as "active". | | |
| 4 | View the new unit's detail page. | Default house policies (No Pets, No Durians, No Smoking) are automatically applied. | | |

### 8.3.2 TC003_02: Test Delete Unit with Future Bookings (SD-HS-05)

| Field | Detail |
| :--- | :--- |
| **Test Case ID** | TC003_02 |
| **Test Case Name** | Attempt to Delete Unit with Future Bookings |
| **Prerequisites** | Admin is logged in. "Unit A" has a confirmed booking for a future date. |
| **Test Data** | Unit: "Unit A" |

| Step # | Step Details | Expected Results | Actual Results | Pass/Fail |
| :--- | :--- | :--- | :--- | :--- |
| 1 | Navigate to the unit list and click "Delete" on "Unit A". | A confirmation modal appears with a red "Delete" button. | | |
| 2 | Click the "Delete" button in the modal. | Error message: "Cannot delete unit with future bookings." The unit is not deleted. | | |

---

## 8.4 TC004: Test Booking Subsystem: Submit Booking (UC-BK-02)

This test contains the following test cases:
1. TC004_01: Test Submit Booking â€” Successful (SD-BK-02)
2. TC004_02: Test Booking Creation â€” Date Unavailable (SD-BK-01)
3. TC004_03: Test Auto-Cancel Expired Booking (SD-BK-07)

### 8.4.1 TC004_01: Test Submit Booking â€” Successful (SD-BK-02)

| Field | Detail |
| :--- | :--- |
| **Test Case ID** | TC004_01 |
| **Test Case Name** | Successful Booking Creation |
| **Prerequisites** | Guest is logged in. "Unit A" is active. Dates 2026-07-01 to 2026-07-03 are available. |
| **Test Data** | Unit: "Unit A", Check-in: 2026-07-01 15:00, Check-out: 2026-07-03 12:00 |

| Step # | Step Details | Expected Results | Actual Results | Pass/Fail |
| :--- | :--- | :--- | :--- | :--- |
| 1 | Navigate to "Unit A" detail page. | The availability calendar shows 2026-07-01 and 2026-07-02 in green. | | |
| 2 | Select check-in and check-out dates. | Availability is confirmed in real time. Submit button is enabled. | | |
| 3 | Click "Confirm Booking". | Booking is created with a unique booking number (e.g., `BK-20260701-001`). Status: "awaiting_payment". A bill is generated. | | |
| 4 | Check My Bookings page. | The new booking appears with status badge "Pending Payment" (amber). | | |

### 8.4.2 TC004_02: Test Booking Creation â€” Date Unavailable (SD-BK-01)

| Field | Detail |
| :--- | :--- |
| **Test Case ID** | TC004_02 |
| **Test Case Name** | Booking Creation with Unavailable Date |
| **Prerequisites** | Guest is logged in. "Unit A" dates 2026-07-01 to 2026-07-03 are already booked. |
| **Test Data** | Unit: "Unit A", Check-in: 2026-07-01, Check-out: 2026-07-03 |

| Step # | Step Details | Expected Results | Actual Results | Pass/Fail |
| :--- | :--- | :--- | :--- | :--- |
| 1 | Navigate to "Unit A" detail page. | Calendar shows 2026-07-01 and 2026-07-02 in red (booked). | | |
| 2 | Select the booked dates. | Error message: "The selected date has been booked! Please choose another date". Submit button is disabled. | | |

### 8.4.3 TC004_03: Test Auto-Cancel Expired Booking (SD-BK-07)

| Field | Detail |
| :--- | :--- |
| **Test Case ID** | TC004_03 |
| **Test Case Name** | Auto-Cancellation of Unpaid Booking |
| **Prerequisites** | A booking exists with status "awaiting_payment". Payment deadline has passed. |
| **Test Data** | Booking: `BK-20260701-001`, payment_deadline: expired |

| Step # | Step Details | Expected Results | Actual Results | Pass/Fail |
| :--- | :--- | :--- | :--- | :--- |
| 1 | The scheduled job runs. | The booking status changes to "cancelled". The dates are reopened on the calendar. | | |
| 2 | Check guest notifications. | The guest receives a notification: "Your booking BK-20260701-001 has been cancelled due to non-payment." | | |

---

## 8.5 TC005: Test Payment Subsystem: Make Payment (UC-PAY-01)

This test contains the following test cases:
1. TC005_01: Test Successful Payment (SD-PAY-01)
2. TC005_02: Test Payment Webhook â€” Success (SD-PAY-01)
3. TC005_03: Test Payment Webhook â€” Duplicate (SD-PAY-01)

### 8.5.1 TC005_01: Test Successful Payment (SD-PAY-01)

| Field | Detail |
| :--- | :--- |
| **Test Case ID** | TC005_01 |
| **Test Case Name** | Successful Payment via Gateway |
| **Prerequisites** | Guest has a booking with status "awaiting_payment" and an unpaid bill. |
| **Test Data** | Booking: `BK-20260701-001`, Amount: RM 350.00 |

| Step # | Step Details | Expected Results | Actual Results | Pass/Fail |
| :--- | :--- | :--- | :--- | :--- |
| 1 | Click "Pay Now" on the booking detail page. | Guest is redirected to the payment gateway page. | | |
| 2 | Complete payment on the gateway. | Guest is redirected back to HomeLodge with a success confirmation page. | | |
| 3 | Verify booking status. | Booking status changes to "confirmed". Bill status changes to "paid". A Payment record is created. A QR code is generated. | | |

### 8.5.2 TC005_02: Test Payment Webhook â€” Success (SD-PAY-01)

| Field | Detail |
| :--- | :--- |
| **Test Case ID** | TC005_02 |
| **Test Case Name** | Webhook Processing â€” Successful Payment |
| **Prerequisites** | Booking `BK-20260701-001` exists with status "awaiting_payment". |
| **Test Data** | Webhook payload: { gateway_reference: "GW-001", status: "success", signature: valid } |

| Step # | Step Details | Expected Results | Actual Results | Pass/Fail |
| :--- | :--- | :--- | :--- | :--- |
| 1 | Send a POST request to the webhook endpoint with the test payload. | The system verifies the signature and returns HTTP 200. | | |
| 2 | Verify database records. | Bill marked "paid". Payment record created with gateway_reference "GW-001". Booking status "confirmed". QR code generated. | | |
| 3 | Verify notifications. | Guest receives in-app and email notification of successful payment. | | |

### 8.5.3 TC005_03: Test Payment Webhook â€” Duplicate (SD-PAY-01)

| Field | Detail |
| :--- | :--- |
| **Test Case ID** | TC005_03 |
| **Test Case Name** | Webhook Processing â€” Duplicate Payment |
| **Prerequisites** | Payment with gateway_reference "GW-001" already exists. |
| **Test Data** | Same webhook payload as TC005_02 |

| Step # | Step Details | Expected Results | Actual Results | Pass/Fail |
| :--- | :--- | :--- | :--- | :--- |
| 1 | Send the duplicate webhook POST. | The system returns HTTP 200 without creating a duplicate Payment record. | | |

---

## 8.6 TC006: Test QR Code & Access Subsystem: Receive & Use QR Code (UC-QR-01)

This test contains the following test cases:
1. TC006_01: Test QR Code Generation on Booking Confirmation (SD-QR-01)
2. TC006_02: Test Booking Extension â€” Successful (SD-QR-03 & SD-QR-04)
3. TC006_03: Test Extension Auto-Cancel (SD-QR-05)

### 8.6.1 TC006_01: Test QR Code Generation (SD-QR-01)

| Field | Detail |
| :--- | :--- |
| **Test Case ID** | TC006_01 |
| **Test Case Name** | QR Code Generated After Payment |
| **Prerequisites** | Booking `BK-20260701-001` payment just confirmed. |
| **Test Data** | Check-in: 2026-07-01 15:00, Check-out: 2026-07-03 12:00 |

| Step # | Step Details | Expected Results | Actual Results | Pass/Fail |
| :--- | :--- | :--- | :--- | :--- |
| 1 | View the booking detail page. | A QR code image is displayed. | | |
| 2 | Verify QR code record in database. | Token is unique, valid_from = 2026-07-01 15:00, valid_until = 2026-07-03 12:00, purpose = "guest", status = "active". | | |

### 8.6.2 TC006_02: Test Booking Extension â€” Successful (SD-QR-03 & SD-QR-04)

| Field | Detail |
| :--- | :--- |
| **Test Case ID** | TC006_02 |
| **Test Case Name** | Successful Booking Extension |
| **Prerequisites** | Admin is logged in. Booking `BK-20260701-001` is confirmed. Dates 2026-07-03 to 2026-07-04 are available. |
| **Test Data** | New check-out: 2026-07-04 12:00, Extension charge rate: RM 150/night |

| Step # | Step Details | Expected Results | Actual Results | Pass/Fail |
| :--- | :--- | :--- | :--- | :--- |
| 1 | Admin navigates to QR Code Management and clicks "Extend" for the booking. | The extension form appears. | | |
| 2 | Admin selects new check-out date 2026-07-04. | System checks availability â€” no conflict. Extension charge calculated: RM 150.00. | | |
| 3 | Admin confirms the extension. | Extension record created with status "awaiting_payment". Additional bill generated. Payment deadline set (60 minutes). Guest notified. | | |
| 4 | Guest pays the extension charge within the window. | Extension status â†’ "paid". Booking check-out updated to 2026-07-04 12:00. QR code valid_until updated. | | |

### 8.6.3 TC006_03: Test Extension Auto-Cancel (SD-QR-05)

| Field | Detail |
| :--- | :--- |
| **Test Case ID** | TC006_03 |
| **Test Case Name** | Extension Auto-Cancelled Due to Non-Payment |
| **Prerequisites** | Extension exists with status "awaiting_payment". Payment deadline has passed. |
| **Test Data** | Extension for booking `BK-20260701-001`, payment_deadline: expired |

| Step # | Step Details | Expected Results | Actual Results | Pass/Fail |
| :--- | :--- | :--- | :--- | :--- |
| 1 | The scheduled job runs. | Extension status â†’ "expired". Booking reverts to original check-out (2026-07-03 12:00). QR code valid_until remains at original value. | | |
| 2 | Guest is notified. | Notification: "Your extension request has been cancelled due to non-payment. Your check-out remains at 2026-07-03 12:00 PM." | | |

---

## 8.7 TC007: Test Guest Feedback Subsystem: Submit Rating & Feedback (UC-FB-01)

This test contains the following test cases:
1. TC007_01: Test Submit Feedback â€” Successful (SD-FB-01)
2. TC007_02: Test Submit Feedback â€” Booking Not Completed
3. TC007_03: Test Duplicate Feedback

### 8.7.1 TC007_01: Test Submit Feedback â€” Successful (SD-FB-01)

| Field | Detail |
| :--- | :--- |
| **Test Case ID** | TC007_01 |
| **Test Case Name** | Successful Feedback Submission |
| **Prerequisites** | Guest is logged in. Booking `BK-20260701-001` has status "completed". No feedback exists for this booking. |
| **Test Data** | Rating: 5, Comment: "Excellent stay! Highly recommended." |

| Step # | Step Details | Expected Results | Actual Results | Pass/Fail |
| :--- | :--- | :--- | :--- | :--- |
| 1 | Navigate to the booking detail page. | A feedback form is displayed (since booking is "completed"). | | |
| 2 | Select 5 stars and enter the comment. | Fields accept input. | | |
| 3 | Click "Submit Feedback". | Success toast: "Thank you for your feedback!" Feedback record created in database. | | |
| 4 | Check the unit listing page. | The average rating for the unit is updated to include this feedback. | | |

### 8.7.2 TC007_02: Test Submit Feedback â€” Booking Not Completed

| Field | Detail |
| :--- | :--- |
| **Test Case ID** | TC007_02 |
| **Test Case Name** | Feedback Submission for Non-Completed Booking |
| **Prerequisites** | Booking status is "confirmed" (not yet completed). |
| **Test Data** | N/A |

| Step # | Step Details | Expected Results | Actual Results | Pass/Fail |
| :--- | :--- | :--- | :--- | :--- |
| 1 | Navigate to the booking detail page. | The feedback form is NOT displayed. A message says "Feedback can be submitted after your stay is completed." | | |

### 8.7.3 TC007_03: Test Duplicate Feedback

| Field | Detail |
| :--- | :--- |
| **Test Case ID** | TC007_03 |
| **Test Case Name** | Attempt to Submit Duplicate Feedback |
| **Prerequisites** | Feedback already exists for booking `BK-20260701-001`. |
| **Test Data** | N/A |

| Step # | Step Details | Expected Results | Actual Results | Pass/Fail |
| :--- | :--- | :--- | :--- | :--- |
| 1 | Navigate to the booking detail page. | The submitted feedback is displayed (read-only). The feedback form is replaced by a "You have already submitted feedback" message. | | |

---

## 8.8 TC008: Test Notification Subsystem: Receive System Notification (UC-NOTIF-01)

This test contains the following test cases:
1. TC008_01: Test In-App Notification Received Successfully
2. TC008_02: Test Email Notification Delivery

### 8.8.1 TC008_01: Test In-App Notification Received Successfully

| Field | Detail |
| :--- | :--- |
| **Test Case ID** | TC008_01 |
| **Test Case Name** | In-App Notification Received Successfully |
| **Prerequisites** | User is logged in. A triggering event occurs (e.g., booking confirmed). |
| **Test Data** | Event: Booking Confirmation |

| Step # | Step Details | Expected Results | Actual Results | Pass/Fail |
| :--- | :--- | :--- | :--- | :--- |
| 1 | System triggers notification event. | Bell icon badge updates with unread count in real-time. | | |
| 2 | Click on the bell icon. | Notifications panel opens, showing the newest notification first. | | |
| 3 | Click the notification. | Notification is marked as read, user is redirected to the booking detail page. | | |

### 8.8.2 TC008_02: Test Email Notification Delivery

| Field | Detail |
| :--- | :--- |
| **Test Case ID** | TC008_02 |
| **Test Case Name** | Email Notification Delivery |
| **Prerequisites** | User has a valid email. Email settings are enabled globally (UC-SET-01). |
| **Test Data** | Event: Booking Confirmation |

| Step # | Step Details | Expected Results | Actual Results | Pass/Fail |
| :--- | :--- | :--- | :--- | :--- |
| 1 | System triggers notification event. | System checks if email notifications are enabled. | | |
| 2 | Check user's email inbox. | An email detailing the booking confirmation is received. | | |

---

## 8.9 TC009: Test Chat Subsystem: Send / Receive Messages (UC-CHAT-01)

This test contains the following test cases:
1. TC009_01: Test Guest Sends Message to Admin
2. TC009_02: Test Admin Replies to Guest

### 8.9.1 TC009_01: Test Guest Sends Message to Admin

| Field | Detail |
| :--- | :--- |
| **Test Case ID** | TC009_01 |
| **Test Case Name** | Guest Sends Message to Admin |
| **Prerequisites** | Guest is logged in. |
| **Test Data** | Message: "Is early check-in allowed?" |

| Step # | Step Details | Expected Results | Actual Results | Pass/Fail |
| :--- | :--- | :--- | :--- | :--- |
| 1 | Guest navigates to Chat and opens conversation with Admin. | Chat window is displayed. | | |
| 2 | Guest types message and clicks Send. | Message appears in guest's chat history. | | |
| 3 | Admin checks their chat inbox. | Admin receives the message from the guest. | | |

### 8.9.2 TC009_02: Test Admin Replies to Guest

| Field | Detail |
| :--- | :--- |
| **Test Case ID** | TC009_02 |
| **Test Case Name** | Admin Replies to Guest |
| **Prerequisites** | Admin is logged in. Guest has sent a message. |
| **Test Data** | Message: "Yes, early check-in is subject to availability." |

| Step # | Step Details | Expected Results | Actual Results | Pass/Fail |
| :--- | :--- | :--- | :--- | :--- |
| 1 | Admin opens the conversation with the Guest. | Chat window is displayed with guest's previous messages. | | |
| 2 | Admin types reply and clicks Send. | Message appears in admin's chat history. | | |
| 3 | Guest checks their chat window. | Guest receives the reply from the admin in real-time. | | |

---

## 8.10 TC010: Test User & Access Management Subsystem: Manage Roles (UC-USR-04)

This test contains the following test cases:
1. TC010_01: Test Admin Creates and Assigns New Role
2. TC010_02: Test Attempt Unauthorized Access

### 8.10.1 TC010_01: Test Admin Creates and Assigns New Role

| Field | Detail |
| :--- | :--- |
| **Test Case ID** | TC010_01 |
| **Test Case Name** | Admin Creates and Assigns New Role |
| **Prerequisites** | Super Admin is logged in. |
| **Test Data** | Role Name: "Support Staff", Permissions: ["view-bookings", "reply-chat"] |

| Step # | Step Details | Expected Results | Actual Results | Pass/Fail |
| :--- | :--- | :--- | :--- | :--- |
| 1 | Navigate to User Management â†’ Roles & Permissions. | Role management page is displayed. | | |
| 2 | Click "Create Role", fill test data, and submit. | Role is created successfully. | | |
| 3 | Navigate to Users list, edit a user, and assign "Support Staff" role. | User is updated successfully with the new role. | | |

### 8.10.2 TC010_02: Test Attempt Unauthorized Access

| Field | Detail |
| :--- | :--- |
| **Test Case ID** | TC010_02 |
| **Test Case Name** | Attempt Unauthorized Access |
| **Prerequisites** | User logged in with "Support Staff" role. |
| **Test Data** | Target Action: "delete-unit" |

| Step # | Step Details | Expected Results | Actual Results | Pass/Fail |
| :--- | :--- | :--- | :--- | :--- |
| 1 | User attempts to navigate to the Homestay Unit deletion endpoint/UI. | The UI button is hidden or disabled. | | |
| 2 | User directly accesses the delete URL. | System returns a 403 Forbidden error. | | |

---

## 8.11 TC011: Test System Settings Subsystem: Configure System Settings (UC-SET-01)

This test contains the following test cases:
1. TC011_01: Test Admin Updates General Settings Successfully

### 8.11.1 TC011_01: Test Admin Updates General Settings Successfully

| Field | Detail |
| :--- | :--- |
| **Test Case ID** | TC011_01 |
| **Test Case Name** | Admin Updates General Settings Successfully |
| **Prerequisites** | Admin is logged in. |
| **Test Data** | Setting: "Email Notifications Enabled", Value: "False" |

| Step # | Step Details | Expected Results | Actual Results | Pass/Fail |
| :--- | :--- | :--- | :--- | :--- |
| 1 | Navigate to System Settings. | Settings form is displayed with current values. | | |
| 2 | Toggle "Email Notifications Enabled" to False and click Save. | Success message displayed. Settings are updated in the database. | | |
| 3 | Trigger a notification event. | In-app notification is sent, but no email is sent. | | |

---

## 8.12 TC012: Test Audit Logs Subsystem: View / Filter Audit Trail (UC-AUDIT-01)

This test contains the following test cases:
1. TC012_01: Test System Automatically Logs Event (UC-AUDIT-02)
2. TC012_02: Test Admin Filters Audit Logs by Date Range

### 8.12.1 TC012_01: Test System Automatically Logs Event (UC-AUDIT-02)

| Field | Detail |
| :--- | :--- |
| **Test Case ID** | TC012_01 |
| **Test Case Name** | System Automatically Logs Event |
| **Prerequisites** | Admin is logged in. |
| **Test Data** | Action: Create a new Homestay Unit. |

| Step # | Step Details | Expected Results | Actual Results | Pass/Fail |
| :--- | :--- | :--- | :--- | :--- |
| 1 | Admin performs the create unit action. | Action completes successfully. | | |
| 2 | Navigate to Audit Logs page. | A new log entry is visible showing the admin's name, action ("Unit Created"), timestamp, and IP address. | | |

### 8.12.2 TC012_02: Test Admin Filters Audit Logs by Date Range

| Field | Detail |
| :--- | :--- |
| **Test Case ID** | TC012_02 |
| **Test Case Name** | Admin Filters Audit Logs by Date Range |
| **Prerequisites** | Audit logs exist for various dates. |
| **Test Data** | Start Date: Yesterday, End Date: Today |

| Step # | Step Details | Expected Results | Actual Results | Pass/Fail |
| :--- | :--- | :--- | :--- | :--- |
| 1 | Navigate to Audit Logs page. | Complete list of logs is displayed. | | |
| 2 | Select Start Date and End Date, then click Filter. | The list is refreshed to only show logs within the selected date range. | | |

---

## 8.13 TC013: Test Reporting & Analytics Subsystem: View Revenue Report (UC-RPT-02)

This test contains the following test cases:
1. TC013_01: Test View Revenue Report with Date Filter
2. TC013_02: Test Export Revenue Report to PDF (UC-RPT-03)

### 8.13.1 TC013_01: Test View Revenue Report with Date Filter

| Field | Detail |
| :--- | :--- |
| **Test Case ID** | TC013_01 |
| **Test Case Name** | View Revenue Report with Date Filter |
| **Prerequisites** | Admin is logged in. Booking data exists. |
| **Test Data** | Date Range: "Current Month" |

| Step # | Step Details | Expected Results | Actual Results | Pass/Fail |
| :--- | :--- | :--- | :--- | :--- |
| 1 | Navigate to Reporting â†’ Revenue Report. | Default revenue charts and data tables are displayed. | | |
| 2 | Select "Current Month" from the date filter. | Report updates to reflect revenue generated in the current month only. | | |

### 8.13.2 TC013_02: Test Export Revenue Report to PDF (UC-RPT-03)

| Field | Detail |
| :--- | :--- |
| **Test Case ID** | TC013_02 |
| **Test Case Name** | Export Revenue Report to PDF |
| **Prerequisites** | Admin is on the Revenue Report page. |
| **Test Data** | Action: Click "Export to PDF" |

| Step # | Step Details | Expected Results | Actual Results | Pass/Fail |
| :--- | :--- | :--- | :--- | :--- |
| 1 | Click "Export to PDF" button. | System generates a PDF document containing the currently filtered report data. | | |
| 2 | Open the downloaded PDF. | PDF content matches the data displayed on the screen. | | |

---

# Appendix A: Traceability Matrix

The table below traces each test case back to its use case / sequence diagram and package.

| Test Case ID | Test Case Description | Use Case ID / Sequence Diagram ID | Package ID |
| :--- | :--- | :--- | :--- |
| **TC-AUTH-01 (TC001) â€” Register Account** | | | **P001** |
| TC001_01_01 | Register Account â€” Normal Flow (Successful Registration) | UC-AUTH-01 / SD001 | P001 |
| TC001_01_02 | Register Account â€” Alternate Flow (Validation Failures) | UC-AUTH-01 / SD001 | P001 |
| TC001_01_03 | Register Account â€” Exception Flow (System Error) | UC-AUTH-01 / SD001 | P001 |
| **TC-AUTH-02 (TC001_02) â€” Login** | | | **P001** |
| TC001_02_01 | Login â€” Normal Flow (Email & Password) | UC-AUTH-02 / SD002 | P001 |
| TC001_02_02 | Login â€” Normal Flow (Google SSO) | UC-AUTH-02 / SD003 | P001 |
| TC001_02_03 | Login â€” Alternate Flow (Wrong Creds, Deactivated, Locked, Google Cancelled) | UC-AUTH-02 / SD002 | P001 |
| TC001_02_04 | Login â€” Exception Flow (System Down, Google Service Down) | UC-AUTH-02 / SD002 | P001 |
| TC001_04_01 | Login â€” Normal Flow (Forced Password Change) | UC-AUTH-02 / SD002 | P001 |
| **TC-AUTH-03 (TC001_03) â€” Logout** | | | **P001** |
| TC001_03_01 | Logout â€” Normal Flow (Successful Sign-Out) | UC-AUTH-03 / SD004 | P001 |
| TC001_03_02 | Logout â€” Exception Flow (Session Already Expired) | UC-AUTH-03 / SD004 | P001 |
| **TC-AUTH-04 (TC001_04) â€” Forgot Password** | | | **P001** |
| TC001_04_01 | Forgot Password â€” Normal Flow (Successful Reset) | UC-AUTH-04 / SD005 | P001 |
| TC001_04_02 | Forgot Password â€” Alternate Flow (Validation Failures) | UC-AUTH-04 / SD005 | P001 |
| TC001_04_03 | Forgot Password â€” Exception Flow (Reset Link Expired) | UC-AUTH-04 / SD005 | P001 |
| **TC-AUTH-05 (TC001_05) â€” View / Update Profile** | | | **P001** |
| TC001_05_01 | View / Update Profile â€” Normal Flow | UC-AUTH-05 / SD006 | P001 |
| TC001_05_02 | View / Update Profile â€” Alternate Flow | UC-AUTH-05 / SD006 | P001 |
| TC001_05_03 | View / Update Profile â€” Exception Flow | UC-AUTH-05 / SD006 | P001 |
| **TC-AUTH-06 (TC001_06) â€” Force Change Password** | | | **P001** |
| TC001_06_01 | Force Change Password â€” Normal Flow | UC-AUTH-06 / SD007 | P001 |
| TC001_06_02 | Force Change Password â€” Alternate Flow | UC-AUTH-06 / SD007 | P001 |
| TC001_06_03 | Force Change Password â€” Exception Flow | UC-AUTH-06 / SD007 | P001 |
| **TC-HS-01 (TC002_01) â€” Browse Homestay Units** | | | **P002** |
| TC002_01_01 | Browse Homestay Units â€” Normal Flow | UC-HS-01 / SD008 | P002 |
| TC002_01_02 | Browse Homestay Units â€” Alternate Flow (No Active Units) | UC-HS-01 / SD008 | P002 |
| **TC-HS-02 (TC002_02) â€” View Unit Details & Availability** | | | **P002** |
| TC002_02_01 | View Unit Details & Availability â€” Normal Flow | UC-HS-02 / SD009 | P002 |
| **TC-HS-03 (TC002_03) â€” Create Homestay Unit** | | | **P002** |
| TC002_03_01 | Create Homestay Unit â€” Normal Flow | UC-HS-03 / SD010 | P002 |
| TC002_03_02 | Create Homestay Unit â€” Alternate Flow (Required Field Missing) | UC-HS-03 / SD010 | P002 |
| TC002_03_03 | Create Homestay Unit â€” Exception Flow (Invalid Image File) | UC-HS-03 / SD010 | P002 |
| **TC-HS-04 (TC002_04) â€” Edit Homestay Unit** | | | **P002** |
| TC002_04_01 | Edit Homestay Unit â€” Normal Flow | UC-HS-04 / SD011 | P002 |
| TC002_04_02 | Edit Homestay Unit â€” Alternate Flow (Invalid Data) | UC-HS-04 / SD011 | P002 |
| **TC-HS-05 (TC002_05) â€” Deactivate / Delete Unit** | | | **P002** |
| TC002_05_01 | Deactivate / Delete Unit â€” Normal Flow | UC-HS-05 / SD012 | P002 |
| TC002_05_02 | Deactivate / Delete Unit â€” Alternate Flow (Has Future Bookings) | UC-HS-05 / SD012 | P002 |
| TC002_05_03 | Deactivate / Delete Unit â€” Exception Flow (Admin Cancels) | UC-HS-05 / SD012 | P002 |
| **TC-HS-06 (TC002_06) â€” View All Units List** | | | **P002** |
| TC002_06_01 | View All Units List â€” Normal Flow | UC-HS-06 / SD013 | P002 |
| **TC-BK-01 (TC003_01) â€” View Availability & Select Dates** | | | **P003** |
| TC003_01_01 | View Availability & Select Dates â€” Normal Flow | UC-BK-01 / SD014 | P003 |
| TC003_01_02 | View Availability & Select Dates â€” Alternate Flow (Dates Unavailable) | UC-BK-01 / SD014 | P003 |
| **TC-BK-02 (TC003_02) â€” Submit Booking** | | | **P003** |
| TC003_02_01 | Submit Booking â€” Normal Flow | UC-BK-02 / SD015 | P003 |
| TC003_02_02 | Submit Booking â€” Alternate Flow (Dates Became Unavailable) | UC-BK-02 / SD015 | P003 |
| TC003_02_03 | Submit Booking â€” Exception Flow | UC-BK-02 / SD015 | P003 |
| **TC-BK-03 (TC003_03) â€” View Bookings** | | | **P003** |
| TC003_03_01 | View Bookings â€” Normal Flow (Guest) | UC-BK-03 / SD016 | P003 |
| TC003_03_02 | View Bookings â€” Normal Flow (Admin) | UC-BK-03 / SD016 | P003 |
| **TC-BK-04 (TC003_04) â€” View Booking Details** | | | **P003** |
| TC003_04_01 | View Booking Details â€” Normal Flow | UC-BK-04 / SD017 | P003 |
| **TC-BK-05 (TC003_05) â€” Cancel Booking** | | | **P003** |
| TC003_05_01 | Cancel Booking â€” Normal Flow (With Refund) | UC-BK-05 / SD018 | P003 |
| TC003_05_02 | Cancel Booking â€” Alternate Flow (Awaiting Payment, No Refund) | UC-BK-05 / SD018 | P003 |
| TC003_05_03 | Cancel Booking â€” Exception Flow (User Cancels Confirmation) | UC-BK-05 / SD018 | P003 |
| **TC-BK-06 (TC003_06) â€” Manage Booking (Admin)** | | | **P003** |
| TC003_06_01 | Manage Booking Admin â€” Normal Flow (Create) | UC-BK-06 / SD019 | P003 |
| TC003_06_02 | Manage Booking Admin â€” Normal Flow (Edit) | UC-BK-06 / SD019 | P003 |
| TC003_06_03 | Manage Booking Admin â€” Normal Flow (Delete & Block) | UC-BK-06 / SD019 | P003 |
| TC003_06_04 | Manage Booking Admin â€” Alternate Flow (Blocked Dates Have Bookings) | UC-BK-06 / SD019 | P003 |
| **TC-BK-07 (TC003_07) â€” Auto-Cancel Expired Booking** | | | **P003** |
| TC003_07_01 | Auto-Cancel Expired Booking â€” Normal Flow | UC-BK-07 / SD020 | P003 |
| **TC-PAY-01 (TC004_01) â€” Make Payment** | | | **P004** |
| TC004_01_01 | Make Payment â€” Normal Flow | UC-PAY-01 / SD021 | P004 |
| TC004_01_02 | Make Payment â€” Alternate Flow | UC-PAY-01 / SD021 | P004 |
| TC004_01_03 | Make Payment â€” Exception Flow (Webhook / Duplicate) | UC-PAY-01 / SD024 | P004 |
| **TC-PAY-02 (TC004_02) â€” View Payment & Billing Records** | | | **P004** |
| TC004_02_01 | View Payment & Billing Records â€” Normal Flow (Guest) | UC-PAY-02 / SD022 | P004 |
| TC004_02_02 | View Payment & Billing Records â€” Normal Flow (Admin) | UC-PAY-02 / SD022 | P004 |
| **TC-PAY-03 (TC004_03) â€” Regenerate Bill / Receipt** | | | **P004** |
| TC004_03_01 | Regenerate Bill / Receipt â€” Normal Flow | UC-PAY-03 / SD023 | P004 |
| **TC-NOTIF-01 (TC005_01) â€” Receive System Notification** | | | **P005** |
| TC005_01_01 | Receive System Notification â€” Normal Flow (In-App) | UC-NOTIF-01 / SD025 | P005 |
| TC005_01_02 | Receive System Notification â€” Normal Flow (Email) | UC-NOTIF-01 / SD025 | P005 |
| TC005_01_03 | Receive System Notification â€” Normal Flow (Reminders) | UC-NOTIF-01 / SD025 | P005 |
| TC005_01_04 | Receive System Notification â€” Alternate Flow | UC-NOTIF-01 / SD025 | P005 |
| TC005_01_05 | Receive System Notification â€” Exception Flow (Notifications Disabled) | UC-NOTIF-01 / SD025 | P005 |
| **TC-NOTIF-02 (TC005_02) â€” Google Calendar Integration** | | | **P005** |
| TC005_02_01 | Google Calendar Integration â€” Normal Flow | UC-NOTIF-02 / SD026 | P005 |
| TC005_02_02 | Google Calendar Integration â€” Alternate Flow (Not Connected) | UC-NOTIF-02 / SD026 | P005 |
| TC005_02_03 | Google Calendar Integration â€” Exception Flow | UC-NOTIF-02 / SD026 | P005 |
| **TC-CHAT-01 (TC006_01) â€” Send / Receive Messages** | | | **P006** |
| TC006_01_01 | Send / Receive Messages â€” Normal Flow (Real-Time Delivery) | UC-CHAT-01 / SD027 | P006 |
| TC006_01_02 | Send / Receive Messages â€” Alternate Flow | UC-CHAT-01 / SD027 | P006 |
| TC006_01_03 | Send / Receive Messages â€” Exception Flow | UC-CHAT-01 / SD027 | P006 |
| **TC-CHAT-02 (TC006_02) â€” View Chat History** | | | **P006** |
| TC006_02_01 | View Chat History â€” Normal Flow | UC-CHAT-02 / SD028 | P006 |
| **TC-USR-01 (TC007_01) â€” Create User Account** | | | **P007** |
| TC007_01_01 | Create User Account â€” Normal Flow | UC-USR-01 / SD029 | P007 |
| TC007_01_02 | Create User Account â€” Alternate Flow (Duplicate Email) | UC-USR-01 / SD029 | P007 |
| **TC-USR-02 (TC007_02) â€” Edit / Activate / Deactivate User** | | | **P007** |
| TC007_02_01 | Edit / Activate / Deactivate User â€” Normal Flow (Edit) | UC-USR-02 / SD030 | P007 |
| TC007_02_02 | Edit / Activate / Deactivate User â€” Normal Flow (Deactivate) | UC-USR-02 / SD030 | P007 |
| **TC-USR-03 (TC007_03) â€” Reset User Password** | | | **P007** |
| TC007_03_01 | Reset User Password â€” Normal Flow | UC-USR-03 / SD031 | P007 |
| **TC-USR-04 (TC008_01) â€” Manage Roles** | | | **P008** |
| TC008_01_01 | Manage Roles â€” Normal Flow (Create & Edit) | UC-USR-04 / SD032 | P008 |
| TC008_01_02 | Manage Roles â€” Normal Flow (Assign & Delete) | UC-USR-04 / SD032 | P008 |
| TC008_01_03 | Manage Roles â€” Alternate Flow (Unauthorized Access) | UC-USR-04 / SD032 | P008 |
| **TC-USR-05 (TC008_02) â€” Manage Permissions** | | | **P008** |
| TC008_02_01 | Manage Permissions â€” Normal Flow | UC-USR-05 / SD033 | P008 |
| TC008_02_02 | Manage Permissions â€” Alternate Flow | UC-USR-05 / SD033 | P008 |
| **TC-SET-01 (TC009_01) â€” Configure System Settings** | | | **P009** |
| TC009_01_01 | Configure System Settings â€” Normal Flow | UC-SET-01 / SD034 | P009 |
| TC009_01_02 | Configure System Settings â€” Alternate Flow (Invalid Setting Value) | UC-SET-01 / SD034 | P009 |
| **TC-SET-02 (TC009_02) â€” Manage Default Homestay Policies** | | | **P009** |
| TC009_02_01 | Manage Default Homestay Policies â€” Normal Flow | UC-SET-02 / SD035 | P009 |
| **TC-AUDIT-01 (TC010_01) â€” View / Filter Audit Trail** | | | **P010** |
| TC010_01_01 | View / Filter Audit Trail â€” Normal Flow | UC-AUDIT-01 / SD036 | P010 |
| **TC-AUDIT-02 (TC010_02) â€” Automatic Event Logging** | | | **P010** |
| TC010_02_01 | Automatic Event Logging â€” Normal Flow (System Action) | UC-AUDIT-02 / SD037 | P010 |
| TC010_02_02 | Automatic Event Logging â€” Normal Flow (User Action) | UC-AUDIT-02 / SD037 | P010 |
| **TC-QR-01 (TC011_01) â€” Receive & Use QR Code** | | | **P011** |
| TC011_01_01 | Receive & Use QR Code â€” Normal Flow | UC-QR-01 / SD038 | P011 |
| TC011_01_02 | Receive & Use QR Code â€” Alternate Flow (QR Expired / Inactive) | UC-QR-01 / SD038 | P011 |
| **TC-QR-02 (TC011_02) â€” Manage Housekeeping Cycle** | | | **P011** |
| TC011_02_01 | Manage Housekeeping Cycle â€” Normal Flow (Housekeeping QR) | UC-QR-02 / SD039 | P011 |
| TC011_02_02 | Manage Housekeeping Cycle â€” Normal Flow (QR Expiry & Next Guest) | UC-QR-02 / SD039 | P011 |
| TC011_02_03 | Manage Housekeeping Cycle â€” Alternate Flow | UC-QR-02 / SD039 | P011 |
| **TC-QR-03 (TC011_03) â€” Initiate Booking Extension** | | | **P011** |
| TC011_03_01 | Initiate Booking Extension â€” Normal Flow | UC-QR-03 / SD040 | P011 |
| TC011_03_02 | Initiate Booking Extension â€” Alternate Flow (Dates Unavailable) | UC-QR-03 / SD040 | P011 |
| **TC-QR-04 (TC011_04) â€” Pay Extension Charge** | | | **P011** |
| TC011_04_01 | Pay Extension Charge â€” Normal Flow | UC-QR-04 / SD041 | P011 |
| **TC-QR-05 (TC011_05) â€” Auto-Cancel Extension** | | | **P011** |
| TC011_05_01 | Auto-Cancel Extension â€” Normal Flow | UC-QR-05 / SD042 | P011 |
| **TC-RPT-01 (TC012_01) â€” View Analytics Dashboard** | | | **P012** |
| TC012_01_01 | View Analytics Dashboard â€” Normal Flow | UC-RPT-01 / SD043 | P012 |
| **TC-RPT-02 (TC012_02) â€” View Revenue Report** | | | **P012** |
| TC012_02_01 | View Revenue Report â€” Normal Flow | UC-RPT-02 / SD044 | P012 |
| **TC-RPT-03 (TC012_03) â€” Export Report** | | | **P012** |
| TC012_03_01 | Export Report â€” Normal Flow | UC-RPT-03 / SD045 | P012 |
| **TC-FB-01 (TC013_01) â€” Submit Rating & Feedback** | | | **P013** |
| TC013_01_01 | Submit Rating & Feedback â€” Normal Flow | UC-FB-01 / SD046 | P013 |
| TC013_01_02 | Submit Rating & Feedback â€” Alternate Flow (Ineligible) | UC-FB-01 / SD046 | P013 |
| TC013_01_03 | Submit Rating & Feedback â€” Exception Flow (Duplicate) | UC-FB-01 / SD046 | P013 |
| **TC-FB-02 (TC013_02) â€” View Submitted Feedback** | | | **P013** |
| TC013_02_01 | View Submitted Feedback â€” Normal Flow | UC-FB-02 / SD047 | P013 |
| **TC-FB-03 (TC013_03) â€” View / Manage All Feedback (Admin)** | | | **P013** |
| TC013_03_01 | View / Manage All Feedback â€” Normal Flow (View) | UC-FB-03 / SD048 | P013 |
| TC013_03_02 | View / Manage All Feedback â€” Normal Flow (Reply) | UC-FB-03 / SD048 | P013 |
| TC013_03_03 | View / Manage All Feedback â€” Normal Flow (Hide & Restore) | UC-FB-03 / SD048 | P013 |
| TC013_03_04 | View / Manage All Feedback â€” Alternate Flow | UC-FB-03 / SD048 | P013 |
| **TC-FB-04 (TC013_04) â€” Display Average Rating** | | | **P013** |
| TC013_04_01 | Display Average Rating â€” Normal Flow | UC-FB-04 / SD049 | P013 |
| TC013_04_02 | Display Average Rating â€” Alternate Flow (No Visible Reviews) | UC-FB-04 / SD049 | P013 |

---

*â€” End of Chapter 8: Test Cases + Appendix A: Traceability Matrix â€”*
