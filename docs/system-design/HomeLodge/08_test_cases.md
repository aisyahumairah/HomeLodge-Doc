# Chapter 8: Test Cases (STD) + Appendix A — HomeLodge Booking Homestay System

This chapter lists the test cases for the HomeLodge system, organised by subsystem and use case. For each test case, the input data, expected output, and step-by-step procedure are provided.

> **Note:** "Actual Results" and "Pass/Fail" columns are to be filled in during testing.

---

## 8.1 TC001: Test Authentication Subsystem: User Registration (UC-AUTH-01)

This test contains the following test cases:
1. TC001_01: Test User Registration — Successful (SD-AUTH-01)
2. TC001_02: Test User Registration — Duplicate Email
3. TC001_03: Test User Registration — Invalid Password

### 8.1.1 TC001_01: Test User Registration — Successful (SD-AUTH-01)

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

### 8.1.2 TC001_02: Test User Registration — Duplicate Email

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

### 8.1.3 TC001_03: Test User Registration — Invalid Password

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
2. TC002_02: Test Failed Login — Wrong Password
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

### 8.2.2 TC002_02: Test Failed Login — Wrong Password

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
1. TC003_01: Test Create Homestay Unit — Successful (SD-HS-03)
2. TC003_02: Test Delete Unit with Future Bookings (SD-HS-05)

### 8.3.1 TC003_01: Test Create Homestay Unit — Successful (SD-HS-03)

| Field | Detail |
| :--- | :--- |
| **Test Case ID** | TC003_01 |
| **Test Case Name** | Successful Homestay Unit Creation |
| **Prerequisites** | Admin is logged in. Default house policies (No Pets, No Durians, No Smoking) exist in system settings. |
| **Test Data** | Name: "Unit A", Description: "A cozy unit", Location: "Johor Bahru", Base Price: 150.00, Deposit: 50.00, Check-in: 15:00, Check-out: 12:00, Images: 2 files |

| Step # | Step Details | Expected Results | Actual Results | Pass/Fail |
| :--- | :--- | :--- | :--- | :--- |
| 1 | Navigate to Homestays → Create New. | The creation form is displayed. | | |
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
1. TC004_01: Test Submit Booking — Successful (SD-BK-02)
2. TC004_02: Test Booking Creation — Date Unavailable (SD-BK-01)
3. TC004_03: Test Auto-Cancel Expired Booking (SD-BK-07)

### 8.4.1 TC004_01: Test Submit Booking — Successful (SD-BK-02)

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

### 8.4.2 TC004_02: Test Booking Creation — Date Unavailable (SD-BK-01)

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
2. TC005_02: Test Payment Webhook — Success (SD-PAY-01)
3. TC005_03: Test Payment Webhook — Duplicate (SD-PAY-01)

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

### 8.5.2 TC005_02: Test Payment Webhook — Success (SD-PAY-01)

| Field | Detail |
| :--- | :--- |
| **Test Case ID** | TC005_02 |
| **Test Case Name** | Webhook Processing — Successful Payment |
| **Prerequisites** | Booking `BK-20260701-001` exists with status "awaiting_payment". |
| **Test Data** | Webhook payload: { gateway_reference: "GW-001", status: "success", signature: valid } |

| Step # | Step Details | Expected Results | Actual Results | Pass/Fail |
| :--- | :--- | :--- | :--- | :--- |
| 1 | Send a POST request to the webhook endpoint with the test payload. | The system verifies the signature and returns HTTP 200. | | |
| 2 | Verify database records. | Bill marked "paid". Payment record created with gateway_reference "GW-001". Booking status "confirmed". QR code generated. | | |
| 3 | Verify notifications. | Guest receives in-app and email notification of successful payment. | | |

### 8.5.3 TC005_03: Test Payment Webhook — Duplicate (SD-PAY-01)

| Field | Detail |
| :--- | :--- |
| **Test Case ID** | TC005_03 |
| **Test Case Name** | Webhook Processing — Duplicate Payment |
| **Prerequisites** | Payment with gateway_reference "GW-001" already exists. |
| **Test Data** | Same webhook payload as TC005_02 |

| Step # | Step Details | Expected Results | Actual Results | Pass/Fail |
| :--- | :--- | :--- | :--- | :--- |
| 1 | Send the duplicate webhook POST. | The system returns HTTP 200 without creating a duplicate Payment record. | | |

---

## 8.6 TC006: Test QR Code & Access Subsystem: Receive & Use QR Code (UC-QR-01)

This test contains the following test cases:
1. TC006_01: Test QR Code Generation on Booking Confirmation (SD-QR-01)
2. TC006_02: Test Booking Extension — Successful (SD-QR-03 & SD-QR-04)
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

### 8.6.2 TC006_02: Test Booking Extension — Successful (SD-QR-03 & SD-QR-04)

| Field | Detail |
| :--- | :--- |
| **Test Case ID** | TC006_02 |
| **Test Case Name** | Successful Booking Extension |
| **Prerequisites** | Admin is logged in. Booking `BK-20260701-001` is confirmed. Dates 2026-07-03 to 2026-07-04 are available. |
| **Test Data** | New check-out: 2026-07-04 12:00, Extension charge rate: RM 150/night |

| Step # | Step Details | Expected Results | Actual Results | Pass/Fail |
| :--- | :--- | :--- | :--- | :--- |
| 1 | Admin navigates to QR Code Management and clicks "Extend" for the booking. | The extension form appears. | | |
| 2 | Admin selects new check-out date 2026-07-04. | System checks availability — no conflict. Extension charge calculated: RM 150.00. | | |
| 3 | Admin confirms the extension. | Extension record created with status "awaiting_payment". Additional bill generated. Payment deadline set (60 minutes). Guest notified. | | |
| 4 | Guest pays the extension charge within the window. | Extension status → "paid". Booking check-out updated to 2026-07-04 12:00. QR code valid_until updated. | | |

### 8.6.3 TC006_03: Test Extension Auto-Cancel (SD-QR-05)

| Field | Detail |
| :--- | :--- |
| **Test Case ID** | TC006_03 |
| **Test Case Name** | Extension Auto-Cancelled Due to Non-Payment |
| **Prerequisites** | Extension exists with status "awaiting_payment". Payment deadline has passed. |
| **Test Data** | Extension for booking `BK-20260701-001`, payment_deadline: expired |

| Step # | Step Details | Expected Results | Actual Results | Pass/Fail |
| :--- | :--- | :--- | :--- | :--- |
| 1 | The scheduled job runs. | Extension status → "expired". Booking reverts to original check-out (2026-07-03 12:00). QR code valid_until remains at original value. | | |
| 2 | Guest is notified. | Notification: "Your extension request has been cancelled due to non-payment. Your check-out remains at 2026-07-03 12:00 PM." | | |

---

## 8.7 TC007: Test Guest Feedback Subsystem: Submit Rating & Feedback (UC-FB-01)

This test contains the following test cases:
1. TC007_01: Test Submit Feedback — Successful (SD-FB-01)
2. TC007_02: Test Submit Feedback — Booking Not Completed
3. TC007_03: Test Duplicate Feedback

### 8.7.1 TC007_01: Test Submit Feedback — Successful (SD-FB-01)

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

### 8.7.2 TC007_02: Test Submit Feedback — Booking Not Completed

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

# Appendix A: Traceability Matrix

The table below traces each test case back to its use case / sequence diagram and package.

| Test Case ID | Use Case ID / Sequence Diagram ID | Package ID |
| :--- | :--- | :--- |
| TC001 for Authentication Subsystem | | P001 |
| TC001_01 | UC-AUTH-01 / SD-AUTH-01 | P001 |
| TC001_02 | UC-AUTH-01 / SD-AUTH-01 (alternate) | P001 |
| TC001_03 | UC-AUTH-01 / SD-AUTH-01 (exception) | P001 |
| TC002 for Authentication Subsystem | | P001 |
| TC002_01 | UC-AUTH-02 / SD-AUTH-02 | P001 |
| TC002_02 | UC-AUTH-02 / SD-AUTH-02 (alternate) | P001 |
| TC002_03 | UC-AUTH-02 / SD-AUTH-02 (exception) | P001 |
| TC003 for Homestay Management Subsystem | | P002 |
| TC003_01 | UC-HS-03 / SD-HS-03 | P002 |
| TC003_02 | UC-HS-05 / SD-HS-05 (exception) | P002 |
| TC004 for Booking Subsystem | | P003 |
| TC004_01 | UC-BK-02 / SD-BK-02 | P003 |
| TC004_02 | UC-BK-01 / SD-BK-01 (alternate) | P003 |
| TC004_03 | UC-BK-07 / SD-BK-07 | P003 |
| TC005 for Payment Subsystem | | P004 |
| TC005_01 | UC-PAY-01 / SD-PAY-01 | P004 |
| TC005_02 | UC-PAY-01 / SD-PAY-01 (webhook) | P004 |
| TC005_03 | UC-PAY-01 / SD-PAY-01 (webhook alternate) | P004 |
| TC006 for QR Code & Access Subsystem | | P010 |
| TC006_01 | UC-QR-01 / SD-QR-01 | P010 |
| TC006_02 | UC-QR-03 & UC-QR-04 / SD-QR-03 & SD-QR-04 | P010 |
| TC006_03 | UC-QR-05 / SD-QR-05 | P010 |
| TC007 for Guest Feedback Subsystem | | P012 |
| TC007_01 | UC-FB-01 / SD-FB-01 | P012 |
| TC007_02 | UC-FB-01 / SD-FB-01 (exception) | P012 |
| TC007_03 | UC-FB-01 / SD-FB-01 (alternate) | P012 |

---

*— End of Chapter 8: Test Cases + Appendix A: Traceability Matrix —*
