# Chapter 8: Test Cases (STD) — HomeLodge Booking Homestay System

> **Note:** Actual Results and Pass/Fail columns are to be filled in during testing.

---

## 8.1 TC001: Test Authentication Subsystem: User Registration (UC-AUTH-01)

This test contains the following test cases:
1. TC001_01: Successful User Registration
2. TC001_02: Registration with Existing Email
3. TC001_03: Registration with Invalid Password

---

### 8.1.1 ## TC001_01: Successful User Registration

| Field | Detail |
|:------|:-------|
| **Test Case ID** | TC001_01 |
| **Test Case Name** | Successful User Registration |
| **Created By** | Aisyah Umairah |
| **Reviewed By** |  |
| **Version** | 1.0 |
| **QA Tester's Log** | Initial version - covers SD-AUTH-01 happy path. |
| **Tester's Name** | QA Tester |
| **Date Tested** |   |
| **Test Case (Pass/Fail/Not Executed)** |   |

**Prerequisites:**

1. The email newguest@example.com is not registered in the system.

**Test Data:**

1. Name: John Doe
2. Email: newguest@example.com
3. Password: Pass@123
4. Confirm Password: Pass@123

**Test Scenario:** Verify that a new user can register successfully with valid details.

| Step # | Step Details | Expected Results | Actual Results | Pass / Fail / Not Executed / Suspended |
|:------:|:-------------|:-----------------|:---------------|:--------------------------------------:|
| 1 | Navigate to the registration page. | Registration form displayed with fields: Name, Email, Password, Confirm Password. | As Expected | |
| 2 | Fill in all fields with the test data. | All fields accept input. Password shows real-time strength indicator. | As Expected | |
| 3 | Click the Register button. | Success message: Registration successful. User redirected to login/dashboard. Verification email sent. | As Expected | |
| 4 | Check the database. | New User record exists: email newguest@example.com, role Guest, status active. | As Expected | |

---

### 8.1.2 ## TC001_02: Registration with Existing Email

| Field | Detail |
|:------|:-------|
| **Test Case ID** | TC001_02 |
| **Test Case Name** | Registration with Existing Email |
| **Created By** | Aisyah Umairah |
| **Reviewed By** |  |
| **Version** | 1.0 |
| **QA Tester's Log** | Covers alternate path - duplicate email rejection. |
| **Tester's Name** | QA Tester |
| **Date Tested** |   |
| **Test Case (Pass/Fail/Not Executed)** |   |

**Prerequisites:**

1. The email existing@example.com is already registered.

**Test Data:**

1. Name: Jane Doe
2. Email: existing@example.com
3. Password: Pass@123
4. Confirm Password: Pass@123

**Test Scenario:** Verify that registration is rejected when the email address already exists in the system.

| Step # | Step Details | Expected Results | Actual Results | Pass / Fail / Not Executed / Suspended |
|:------:|:-------------|:-----------------|:---------------|:--------------------------------------:|
| 1 | Navigate to the registration page. | The registration form is displayed. | As Expected | |
| 2 | Fill in all fields with the test data. | All fields accept input. | As Expected | |
| 3 | Click the Register button. | Error: The email has already been taken. No new record is created. | As Expected | |

---

### 8.1.3 ## TC001_03: Registration with Invalid Password

| Field | Detail |
|:------|:-------|
| **Test Case ID** | TC001_03 |
| **Test Case Name** | Registration with Invalid Password |
| **Created By** | Aisyah Umairah |
| **Reviewed By** |  |
| **Version** | 1.0 |
| **QA Tester's Log** | Covers exception path - weak password validation. |
| **Tester's Name** | QA Tester |
| **Date Tested** |   |
| **Test Case (Pass/Fail/Not Executed)** |   |

**Prerequisites:**

1. None.

**Test Data:**

1. Name: Test User
2. Email: test@example.com
3. Password: weak
4. Confirm Password: weak

**Test Scenario:** Verify that registration is rejected when the password does not meet complexity requirements.

| Step # | Step Details | Expected Results | Actual Results | Pass / Fail / Not Executed / Suspended |
|:------:|:-------------|:-----------------|:---------------|:--------------------------------------:|
| 1 | Navigate to the registration page. | The registration form is displayed. | As Expected | |
| 2 | Enter weak in the password field. | Real-time indicator shows unmet criteria (uppercase, digit, special char, min length). | As Expected | |
| 3 | Click the Register button. | Validation errors appear listing all unmet criteria. Submit is rejected. | As Expected | |

---

## 8.2 TC002: Test Authentication Subsystem: User Login (UC-AUTH-02)

This test contains the following test cases:
1. TC002_01: Successful User Login
2. TC002_02: Login with Wrong Password
3. TC002_03: Account Lockout After Max Failed Attempts

---

### 8.2.1 ## TC002_01: Successful User Login

| Field | Detail |
|:------|:-------|
| **Test Case ID** | TC002_01 |
| **Test Case Name** | Successful User Login |
| **Created By** | Aisyah Umairah |
| **Reviewed By** |  |
| **Version** | 1.0 |
| **QA Tester's Log** | Covers SD-AUTH-02 happy path. |
| **Tester's Name** | QA Tester |
| **Date Tested** |   |
| **Test Case (Pass/Fail/Not Executed)** |   |

**Prerequisites:**

1. User guest@example.com exists with password Pass@123, status active.

**Test Data:**

1. Email: guest@example.com
2. Password: Pass@123

**Test Scenario:** Verify that a registered user can log in with valid credentials.

| Step # | Step Details | Expected Results | Actual Results | Pass / Fail / Not Executed / Suspended |
|:------:|:-------------|:-----------------|:---------------|:--------------------------------------:|
| 1 | Navigate to the login page. | The login form is displayed. | As Expected | |
| 2 | Enter the test credentials. | Fields accept input. | As Expected | |
| 3 | Click Login. | User redirected to the guest dashboard. A session is created. | As Expected | |

---

### 8.2.2 ## TC002_02: Login with Wrong Password

| Field | Detail |
|:------|:-------|
| **Test Case ID** | TC002_02 |
| **Test Case Name** | Login with Wrong Password |
| **Created By** | Aisyah Umairah |
| **Reviewed By** |  |
| **Version** | 1.0 |
| **QA Tester's Log** | Covers alternate path - invalid credentials. |
| **Tester's Name** | QA Tester |
| **Date Tested** |   |
| **Test Case (Pass/Fail/Not Executed)** |   |

**Prerequisites:**

1. User guest@example.com exists, max_attempts = 5, failed_login_count = 0.

**Test Data:**

1. Email: guest@example.com
2. Password: WrongPass1!

**Test Scenario:** Verify that an error is displayed and failed attempt count increments when wrong password is entered.

| Step # | Step Details | Expected Results | Actual Results | Pass / Fail / Not Executed / Suspended |
|:------:|:-------------|:-----------------|:---------------|:--------------------------------------:|
| 1 | Navigate to the login page. | The login form is displayed. | As Expected | |
| 2 | Enter incorrect credentials. | Fields accept input. | As Expected | |
| 3 | Click Login. | Error: Invalid credentials. 4 attempts remaining. failed_login_count increments to 1. | As Expected | |

---

### 8.2.3 ## TC002_03: Account Lockout After Max Failed Attempts

| Field | Detail |
|:------|:-------|
| **Test Case ID** | TC002_03 |
| **Test Case Name** | Account Lockout After Max Failed Attempts |
| **Created By** | Aisyah Umairah |
| **Reviewed By** |  |
| **Version** | 1.0 |
| **QA Tester's Log** | Covers exception path - account lockout. |
| **Tester's Name** | QA Tester |
| **Date Tested** |   |
| **Test Case (Pass/Fail/Not Executed)** |   |

**Prerequisites:**

1. User guest@example.com exists, max_attempts = 5, failed_login_count = 4.

**Test Data:**

1. Email: guest@example.com
2. Password: WrongPass1!

**Test Scenario:** Verify that the account is locked after the maximum number of consecutive failed login attempts.

| Step # | Step Details | Expected Results | Actual Results | Pass / Fail / Not Executed / Suspended |
|:------:|:-------------|:-----------------|:---------------|:--------------------------------------:|
| 1 | Attempt login with wrong password. | Error: Your account has been locked. Status changes to locked, locked_until is set. | As Expected | |
| 2 | Attempt login with correct password. | Error: Your account is locked until [time]. Login denied. | As Expected | |
| 3 | Wait for lockout duration to expire, then login with correct credentials. | Login succeeds after lockout period. | As Expected | |

---

## 8.3 TC003: Test Homestay Management Subsystem: Create Unit (UC-HS-03)

This test contains the following test cases:
1. TC003_01: Successful Homestay Unit Creation
2. TC003_02: Attempt to Delete Unit with Future Bookings

---

### 8.3.1 ## TC003_01: Successful Homestay Unit Creation

| Field | Detail |
|:------|:-------|
| **Test Case ID** | TC003_01 |
| **Test Case Name** | Successful Homestay Unit Creation |
| **Created By** | Aisyah Umairah |
| **Reviewed By** |  |
| **Version** | 1.0 |
| **QA Tester's Log** | Covers SD-HS-03 happy path. |
| **Tester's Name** | QA Tester |
| **Date Tested** |   |
| **Test Case (Pass/Fail/Not Executed)** |   |

**Prerequisites:**

1. Admin is logged in. Default house policies (No Pets, No Durians, No Smoking) exist in system settings.

**Test Data:**

1. Name: Unit A
2. Location: Johor Bahru
3. Base Price: RM 150.00, Deposit: RM 50.00
4. Check-in: 15:00, Check-out: 12:00, Images: 2 files

**Test Scenario:** Verify that an admin can create a new homestay unit and default policies are applied automatically.

| Step # | Step Details | Expected Results | Actual Results | Pass / Fail / Not Executed / Suspended |
|:------:|:-------------|:-----------------|:---------------|:--------------------------------------:|
| 1 | Navigate to Homestays then Create New. | The creation form is displayed. | As Expected | |
| 2 | Fill in all fields with test data and upload images. | All fields accept input. Images preview correctly. | As Expected | |
| 3 | Click Save. | Success toast: Homestay unit created successfully. Unit appears in list as active. | As Expected | |
| 4 | View the new unit's detail page. | Default house policies (No Pets, No Durians, No Smoking) are automatically applied. | As Expected | |

---

### 8.3.2 ## TC003_02: Attempt to Delete Unit with Future Bookings

| Field | Detail |
|:------|:-------|
| **Test Case ID** | TC003_02 |
| **Test Case Name** | Attempt to Delete Unit with Future Bookings |
| **Created By** | Aisyah Umairah |
| **Reviewed By** |  |
| **Version** | 1.0 |
| **QA Tester's Log** | Covers SD-HS-05 exception path. |
| **Tester's Name** | QA Tester |
| **Date Tested** |   |
| **Test Case (Pass/Fail/Not Executed)** |   |

**Prerequisites:**

1. Admin is logged in. Unit A has a confirmed booking for a future date.

**Test Data:**

1. Unit: Unit A

**Test Scenario:** Verify that a unit with future bookings cannot be deleted.

| Step # | Step Details | Expected Results | Actual Results | Pass / Fail / Not Executed / Suspended |
|:------:|:-------------|:-----------------|:---------------|:--------------------------------------:|
| 1 | Navigate to the unit list and click Delete on Unit A. | A confirmation modal appears with a red Delete button. | As Expected | |
| 2 | Click the Delete button in the modal. | Error: Cannot delete unit with future bookings. The unit is not deleted. | As Expected | |

---

## 8.4 TC004: Test Booking Subsystem: Submit Booking (UC-BK-02)

This test contains the following test cases:
1. TC004_01: Successful Booking Creation
2. TC004_02: Booking Creation with Unavailable Date
3. TC004_03: Auto-Cancellation of Unpaid Booking

---

### 8.4.1 ## TC004_01: Successful Booking Creation

| Field | Detail |
|:------|:-------|
| **Test Case ID** | TC004_01 |
| **Test Case Name** | Successful Booking Creation |
| **Created By** | Aisyah Umairah |
| **Reviewed By** |  |
| **Version** | 1.0 |
| **QA Tester's Log** | Covers SD-BK-02 happy path. |
| **Tester's Name** | QA Tester |
| **Date Tested** |   |
| **Test Case (Pass/Fail/Not Executed)** |   |

**Prerequisites:**

1. Guest is logged in. Unit A is active. Dates 2026-07-01 to 2026-07-03 are available.

**Test Data:**

1. Unit: Unit A
2. Check-in: 2026-07-01 15:00
3. Check-out: 2026-07-03 12:00

**Test Scenario:** Verify that a guest can create a booking for available dates.

| Step # | Step Details | Expected Results | Actual Results | Pass / Fail / Not Executed / Suspended |
|:------:|:-------------|:-----------------|:---------------|:--------------------------------------:|
| 1 | Navigate to Unit A detail page. | Availability calendar shows 2026-07-01 and 2026-07-02 in green. | As Expected | |
| 2 | Select check-in and check-out dates. | Availability confirmed in real time. Submit button enabled. | As Expected | |
| 3 | Click Confirm Booking. | Booking created: BK-20260701-001, status awaiting_payment. Bill generated. | As Expected | |
| 4 | Check My Bookings page. | New booking appears with status badge Pending Payment (amber). | As Expected | |

---

### 8.4.2 ## TC004_02: Booking Creation with Unavailable Date

| Field | Detail |
|:------|:-------|
| **Test Case ID** | TC004_02 |
| **Test Case Name** | Booking Creation with Unavailable Date |
| **Created By** | Aisyah Umairah |
| **Reviewed By** |  |
| **Version** | 1.0 |
| **QA Tester's Log** | Covers SD-BK-01 alternate path. |
| **Tester's Name** | QA Tester |
| **Date Tested** |   |
| **Test Case (Pass/Fail/Not Executed)** |   |

**Prerequisites:**

1. Guest is logged in. Unit A dates 2026-07-01 to 2026-07-03 are already booked.

**Test Data:**

1. Unit: Unit A
2. Check-in: 2026-07-01
3. Check-out: 2026-07-03

**Test Scenario:** Verify that a guest cannot book dates that are already reserved.

| Step # | Step Details | Expected Results | Actual Results | Pass / Fail / Not Executed / Suspended |
|:------:|:-------------|:-----------------|:---------------|:--------------------------------------:|
| 1 | Navigate to Unit A detail page. | Calendar shows 2026-07-01 and 2026-07-02 in red (booked). | As Expected | |
| 2 | Select the booked dates. | Error: The selected date has been booked! Please choose another date. Submit button disabled. | As Expected | |

---

### 8.4.3 ## TC004_03: Auto-Cancellation of Unpaid Booking

| Field | Detail |
|:------|:-------|
| **Test Case ID** | TC004_03 |
| **Test Case Name** | Auto-Cancellation of Unpaid Booking |
| **Created By** | Aisyah Umairah |
| **Reviewed By** |  |
| **Version** | 1.0 |
| **QA Tester's Log** | Covers SD-BK-07 exception path. |
| **Tester's Name** | QA Tester |
| **Date Tested** |   |
| **Test Case (Pass/Fail/Not Executed)** |   |

**Prerequisites:**

1. A booking exists with status awaiting_payment. Payment deadline has passed.

**Test Data:**

1. Booking: BK-20260701-001
2. payment_deadline: expired

**Test Scenario:** Verify that unpaid bookings are automatically cancelled when the payment deadline expires.

| Step # | Step Details | Expected Results | Actual Results | Pass / Fail / Not Executed / Suspended |
|:------:|:-------------|:-----------------|:---------------|:--------------------------------------:|
| 1 | The scheduled job runs. | Booking status changes to cancelled. Dates reopened on the calendar. | As Expected | |
| 2 | Check guest notifications. | Guest notified: Your booking BK-20260701-001 has been cancelled due to non-payment. | As Expected | |

---

## 8.5 TC005: Test Payment Subsystem: Make Payment (UC-PAY-01)

This test contains the following test cases:
1. TC005_01: Successful Payment via Gateway
2. TC005_02: Webhook Processing - Successful Payment
3. TC005_03: Webhook Processing - Duplicate Payment

---

### 8.5.1 ## TC005_01: Successful Payment via Gateway

| Field | Detail |
|:------|:-------|
| **Test Case ID** | TC005_01 |
| **Test Case Name** | Successful Payment via Gateway |
| **Created By** | Aisyah Umairah |
| **Reviewed By** |  |
| **Version** | 1.0 |
| **QA Tester's Log** | Covers SD-PAY-01 happy path. |
| **Tester's Name** | QA Tester |
| **Date Tested** |   |
| **Test Case (Pass/Fail/Not Executed)** |   |

**Prerequisites:**

1. Guest has booking BK-20260701-001 with status awaiting_payment and an unpaid bill.

**Test Data:**

1. Booking: BK-20260701-001
2. Amount: RM 350.00

**Test Scenario:** Verify that a guest can complete payment via the payment gateway.

| Step # | Step Details | Expected Results | Actual Results | Pass / Fail / Not Executed / Suspended |
|:------:|:-------------|:-----------------|:---------------|:--------------------------------------:|
| 1 | Click Pay Now on the booking detail page. | Guest redirected to the payment gateway page. | As Expected | |
| 2 | Complete payment on the gateway. | Guest redirected back to HomeLodge with a success confirmation page. | As Expected | |
| 3 | Verify booking status. | Booking changes to confirmed. Bill changes to paid. Payment record created. QR code generated. | As Expected | |

---

### 8.5.2 ## TC005_02: Webhook Processing - Successful Payment

| Field | Detail |
|:------|:-------|
| **Test Case ID** | TC005_02 |
| **Test Case Name** | Webhook Processing - Successful Payment |
| **Created By** | Aisyah Umairah |
| **Reviewed By** |  |
| **Version** | 1.0 |
| **QA Tester's Log** | Covers SD-PAY-01 webhook path. |
| **Tester's Name** | QA Tester |
| **Date Tested** |   |
| **Test Case (Pass/Fail/Not Executed)** |   |

**Prerequisites:**

1. Booking BK-20260701-001 exists with status awaiting_payment.

**Test Data:**

1. Webhook payload: { gateway_reference: GW-001, status: success, signature: valid }

**Test Scenario:** Verify that the system correctly processes a successful payment webhook from the gateway.

| Step # | Step Details | Expected Results | Actual Results | Pass / Fail / Not Executed / Suspended |
|:------:|:-------------|:-----------------|:---------------|:--------------------------------------:|
| 1 | Send a POST request to the webhook endpoint with the test payload. | System verifies signature and returns HTTP 200. | As Expected | |
| 2 | Verify database records. | Bill marked paid. Payment record created with gateway_reference GW-001. Booking confirmed. QR code generated. | As Expected | |
| 3 | Verify notifications. | Guest receives in-app and email notification of successful payment. | As Expected | |

---

### 8.5.3 ## TC005_03: Webhook Processing - Duplicate Payment

| Field | Detail |
|:------|:-------|
| **Test Case ID** | TC005_03 |
| **Test Case Name** | Webhook Processing - Duplicate Payment |
| **Created By** | Aisyah Umairah |
| **Reviewed By** |  |
| **Version** | 1.0 |
| **QA Tester's Log** | Covers SD-PAY-01 webhook alternate path. |
| **Tester's Name** | QA Tester |
| **Date Tested** |   |
| **Test Case (Pass/Fail/Not Executed)** |   |

**Prerequisites:**

1. Payment with gateway_reference GW-001 already exists.

**Test Data:**

1. Same webhook payload as TC005_02

**Test Scenario:** Verify that duplicate webhook calls do not create duplicate payment records.

| Step # | Step Details | Expected Results | Actual Results | Pass / Fail / Not Executed / Suspended |
|:------:|:-------------|:-----------------|:---------------|:--------------------------------------:|
| 1 | Send the duplicate webhook POST. | System returns HTTP 200 without creating a duplicate Payment record. | As Expected | |

---

## 8.6 TC006: Test QR Code & Access Subsystem: Receive & Use QR Code (UC-QR-01)

This test contains the following test cases:
1. TC006_01: QR Code Generated After Payment
2. TC006_02: Successful Booking Extension
3. TC006_03: Extension Auto-Cancelled Due to Non-Payment

---

### 8.6.1 ## TC006_01: QR Code Generated After Payment

| Field | Detail |
|:------|:-------|
| **Test Case ID** | TC006_01 |
| **Test Case Name** | QR Code Generated After Payment |
| **Created By** | Aisyah Umairah |
| **Reviewed By** |  |
| **Version** | 1.0 |
| **QA Tester's Log** | Covers SD-QR-01 happy path. |
| **Tester's Name** | QA Tester |
| **Date Tested** |   |
| **Test Case (Pass/Fail/Not Executed)** |   |

**Prerequisites:**

1. Booking BK-20260701-001 payment just confirmed.

**Test Data:**

1. Check-in: 2026-07-01 15:00
2. Check-out: 2026-07-03 12:00

**Test Scenario:** Verify that a QR code is automatically generated for the guest after payment confirmation.

| Step # | Step Details | Expected Results | Actual Results | Pass / Fail / Not Executed / Suspended |
|:------:|:-------------|:-----------------|:---------------|:--------------------------------------:|
| 1 | View the booking detail page. | A QR code image is displayed. | As Expected | |
| 2 | Verify QR code record in database. | Token is unique, valid_from = 2026-07-01 15:00, valid_until = 2026-07-03 12:00, purpose = guest, status = active. | As Expected | |

---

### 8.6.2 ## TC006_02: Successful Booking Extension

| Field | Detail |
|:------|:-------|
| **Test Case ID** | TC006_02 |
| **Test Case Name** | Successful Booking Extension |
| **Created By** | Aisyah Umairah |
| **Reviewed By** |  |
| **Version** | 1.0 |
| **QA Tester's Log** | Covers SD-QR-03 and SD-QR-04. |
| **Tester's Name** | QA Tester |
| **Date Tested** |   |
| **Test Case (Pass/Fail/Not Executed)** |   |

**Prerequisites:**

1. Admin logged in. Booking BK-20260701-001 confirmed. Dates 2026-07-03 to 2026-07-04 available.

**Test Data:**

1. New check-out: 2026-07-04 12:00
2. Extension charge rate: RM 150/night

**Test Scenario:** Verify that an admin can extend a booking and the guest is billed and notified correctly.

| Step # | Step Details | Expected Results | Actual Results | Pass / Fail / Not Executed / Suspended |
|:------:|:-------------|:-----------------|:---------------|:--------------------------------------:|
| 1 | Admin navigates to QR Code Management and clicks Extend. | The extension form appears. | As Expected | |
| 2 | Admin selects new check-out date 2026-07-04. | System checks availability - no conflict. Extension charge: RM 150.00. | As Expected | |
| 3 | Admin confirms the extension. | Extension record created, status awaiting_payment. Bill generated. Payment deadline set (60 min). Guest notified. | As Expected | |
| 4 | Guest pays the extension charge within the window. | Extension changes to paid. Booking check-out updated to 2026-07-04 12:00. QR valid_until updated. | As Expected | |

---

### 8.6.3 ## TC006_03: Extension Auto-Cancelled Due to Non-Payment

| Field | Detail |
|:------|:-------|
| **Test Case ID** | TC006_03 |
| **Test Case Name** | Extension Auto-Cancelled Due to Non-Payment |
| **Created By** | Aisyah Umairah |
| **Reviewed By** |  |
| **Version** | 1.0 |
| **QA Tester's Log** | Covers SD-QR-05 exception path. |
| **Tester's Name** | QA Tester |
| **Date Tested** |   |
| **Test Case (Pass/Fail/Not Executed)** |   |

**Prerequisites:**

1. Extension exists with status awaiting_payment. Payment deadline has passed.

**Test Data:**

1. Extension for booking BK-20260701-001
2. payment_deadline: expired

**Test Scenario:** Verify that an unpaid extension is automatically cancelled when the payment deadline expires.

| Step # | Step Details | Expected Results | Actual Results | Pass / Fail / Not Executed / Suspended |
|:------:|:-------------|:-----------------|:---------------|:--------------------------------------:|
| 1 | The scheduled job runs. | Extension changes to expired. Booking reverts to original check-out 2026-07-03 12:00. QR valid_until unchanged. | As Expected | |
| 2 | Guest is notified. | Notification: Your extension request has been cancelled due to non-payment. Check-out remains 2026-07-03 12:00 PM. | As Expected | |

---

## 8.7 TC007: Test Guest Feedback Subsystem: Submit Rating & Feedback (UC-FB-01)

This test contains the following test cases:
1. TC007_01: Successful Feedback Submission
2. TC007_02: Feedback for Non-Completed Booking
3. TC007_03: Attempt to Submit Duplicate Feedback

---

### 8.7.1 ## TC007_01: Successful Feedback Submission

| Field | Detail |
|:------|:-------|
| **Test Case ID** | TC007_01 |
| **Test Case Name** | Successful Feedback Submission |
| **Created By** | Aisyah Umairah |
| **Reviewed By** |  |
| **Version** | 1.0 |
| **QA Tester's Log** | Covers SD-FB-01 happy path. |
| **Tester's Name** | QA Tester |
| **Date Tested** |   |
| **Test Case (Pass/Fail/Not Executed)** |   |

**Prerequisites:**

1. Guest logged in. Booking BK-20260701-001 status completed. No prior feedback.

**Test Data:**

1. Rating: 5 stars
2. Comment: Excellent stay! Highly recommended.

**Test Scenario:** Verify that a guest can submit feedback and rating for a completed booking.

| Step # | Step Details | Expected Results | Actual Results | Pass / Fail / Not Executed / Suspended |
|:------:|:-------------|:-----------------|:---------------|:--------------------------------------:|
| 1 | Navigate to the booking detail page. | Feedback form is displayed (booking is completed). | As Expected | |
| 2 | Select 5 stars and enter the comment. | Fields accept input. | As Expected | |
| 3 | Click Submit Feedback. | Success toast: Thank you for your feedback! Feedback record created. | As Expected | |
| 4 | Check the unit listing page. | The average rating for the unit is updated to include this feedback. | As Expected | |

---

### 8.7.2 ## TC007_02: Feedback for Non-Completed Booking

| Field | Detail |
|:------|:-------|
| **Test Case ID** | TC007_02 |
| **Test Case Name** | Feedback for Non-Completed Booking |
| **Created By** | Aisyah Umairah |
| **Reviewed By** |  |
| **Version** | 1.0 |
| **QA Tester's Log** | Covers SD-FB-01 exception path. |
| **Tester's Name** | QA Tester |
| **Date Tested** |   |
| **Test Case (Pass/Fail/Not Executed)** |   |

**Prerequisites:**

1. Booking status is confirmed (not yet completed).

**Test Data:**

1. N/A

**Test Scenario:** Verify that the feedback form is not shown when the booking has not been completed.

| Step # | Step Details | Expected Results | Actual Results | Pass / Fail / Not Executed / Suspended |
|:------:|:-------------|:-----------------|:---------------|:--------------------------------------:|
| 1 | Navigate to the booking detail page. | Feedback form is NOT displayed. Message: Feedback can be submitted after your stay is completed. | As Expected | |

---

### 8.7.3 ## TC007_03: Attempt to Submit Duplicate Feedback

| Field | Detail |
|:------|:-------|
| **Test Case ID** | TC007_03 |
| **Test Case Name** | Attempt to Submit Duplicate Feedback |
| **Created By** | Aisyah Umairah |
| **Reviewed By** |  |
| **Version** | 1.0 |
| **QA Tester's Log** | Covers SD-FB-01 alternate path. |
| **Tester's Name** | QA Tester |
| **Date Tested** |   |
| **Test Case (Pass/Fail/Not Executed)** |   |

**Prerequisites:**

1. Feedback already exists for booking BK-20260701-001.

**Test Data:**

1. N/A

**Test Scenario:** Verify that a guest cannot submit feedback more than once for the same booking.

| Step # | Step Details | Expected Results | Actual Results | Pass / Fail / Not Executed / Suspended |
|:------:|:-------------|:-----------------|:---------------|:--------------------------------------:|
| 1 | Navigate to the booking detail page. | Submitted feedback displayed (read-only). Form replaced by: You have already submitted feedback. | As Expected | |

---

# Appendix A: Traceability Matrix

| Test Case ID | Use Case ID / Sequence Diagram ID | Package ID |
|:-------------|:----------------------------------|:-----------|
| TC001 for Authentication Subsystem |  | P001 |
| TC001_01 | UC-AUTH-01 / SD-AUTH-01 | P001 |
| TC001_02 | UC-AUTH-01 / SD-AUTH-01 (alternate) | P001 |
| TC001_03 | UC-AUTH-01 / SD-AUTH-01 (exception) | P001 |
| TC002 for Authentication Subsystem |  | P001 |
| TC002_01 | UC-AUTH-02 / SD-AUTH-02 | P001 |
| TC002_02 | UC-AUTH-02 / SD-AUTH-02 (alternate) | P001 |
| TC002_03 | UC-AUTH-02 / SD-AUTH-02 (exception) | P001 |
| TC003 for Homestay Management Subsystem |  | P002 |
| TC003_01 | UC-HS-03 / SD-HS-03 | P002 |
| TC003_02 | UC-HS-05 / SD-HS-05 (exception) | P002 |
| TC004 for Booking Subsystem |  | P003 |
| TC004_01 | UC-BK-02 / SD-BK-02 | P003 |
| TC004_02 | UC-BK-01 / SD-BK-01 (alternate) | P003 |
| TC004_03 | UC-BK-07 / SD-BK-07 | P003 |
| TC005 for Payment Subsystem |  | P004 |
| TC005_01 | UC-PAY-01 / SD-PAY-01 | P004 |
| TC005_02 | UC-PAY-01 / SD-PAY-01 (webhook) | P004 |
| TC005_03 | UC-PAY-01 / SD-PAY-01 (webhook alternate) | P004 |
| TC006 for QR Code & Access Subsystem |  | P010 |
| TC006_01 | UC-QR-01 / SD-QR-01 | P010 |
| TC006_02 | UC-QR-03 & UC-QR-04 / SD-QR-03 & SD-QR-04 | P010 |
| TC006_03 | UC-QR-05 / SD-QR-05 | P010 |
| TC007 for Guest Feedback Subsystem |  | P012 |
| TC007_01 | UC-FB-01 / SD-FB-01 | P012 |
| TC007_02 | UC-FB-01 / SD-FB-01 (exception) | P012 |
| TC007_03 | UC-FB-01 / SD-FB-01 (alternate) | P012 |

---
*— End of Chapter 8: Test Cases + Appendix A: Traceability Matrix —*