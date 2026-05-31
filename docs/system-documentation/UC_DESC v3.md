# Use Case Descriptions

## HomeLodge – Homestay Booking System

| Field | Detail |
|---|---|
| **Document Version** | 3.0 |
| **Status** | Draft |
| **Last Updated** | 2026-06-01 |
| **Based On** | Consolidated Use Case Model (42 use cases, 12 modules) |
| **Supersedes** | UC_DESC v2.md |

> **About This Document:** This document describes what the HomeLodge system does from the perspective of the people who use it — guests, administrators, and the system itself when it performs tasks automatically. Each section explains a specific action or task, written in plain language so that anyone can understand how the system works without needing a background in computing.

---

## Table of Contents

1. [Authentication Module](#1-authentication-module)
2. [Homestay Management Module](#2-homestay-management-module)
3. [Booking Module](#3-booking-module)
4. [Payment Module](#4-payment-module)
5. [Notification Module](#5-notification-module)
6. [Chat Module](#6-chat-module)
7. [User & Access Management Module](#7-user--access-management-module)
8. [System Settings Module](#8-system-settings-module)
9. [Audit Logs Module](#9-audit-logs-module)
10. [QR Code & Access Module](#10-qr-code--access-module)
11. [Reporting & Analytics Module](#11-reporting--analytics-module)
12. [Guest Feedback Module](#12-guest-feedback-module)

---

## 1. Authentication Module

This module handles everything related to signing in and out of HomeLodge, creating an account, recovering a forgotten password, and keeping personal profile details up to date. It is the front door of the system — every user must go through it before they can do anything else.

---

### UC-AUTH-01: Register Account (Email / Password)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-AUTH-01 |
| **Use Case Name** | Register Account (Email / Password) |
| **Who Is Involved** | Guest (a new visitor who does not yet have an account) |
| **What It Does** | Allows a new person to create a HomeLodge account by providing their name, email address, and a password of their choice. Once registration is complete, the person can sign in and start browsing or booking homestays. |
| **Before This Can Happen** | The person's email address must not already be linked to an existing account. The registration page is open to everyone. |
| **After This Is Done** | A new account exists in the system. The person is given the "Guest" role, meaning they can browse, book, and pay for homestay stays. |

**How It Works:**

1. The person opens the HomeLodge registration page.
2. They fill in their full name, email address, password, and retype the password to confirm it.
3. The system checks that:
   - The email address is in the correct format and is not already taken.
   - The password meets the strength requirements (between 8 and 12 characters, with a mix of upper-case letters, lower-case letters, numbers, and symbols).
   - Both password entries match.
4. If everything is in order, the system creates the account.
5. A success message is shown and the person is taken to the sign-in page, where they can log in for the first time.

**What If Something Goes Wrong?**

- **Email already in use:** The system highlights the email field and suggests that the person either sign in or use the "Forgot Password" option.
- **Weak password:** The system shows which password rules are not yet met so the person can fix them.
- **Passwords do not match:** The system asks the person to re-enter the confirmation password.
- **Invalid email format:** The system points out the issue and asks for a corrected address.
- **Unexpected system error:** A general message ("Registration failed. Please try again.") is shown. No account is created.

---

### UC-AUTH-02: Login / Login via Google SSO

| Field | Detail |
|---|---|
| **Use Case ID** | UC-AUTH-02 |
| **Use Case Name** | Login / Login via Google SSO |
| **Who Is Involved** | Guest, Registered User, Admin |
| **What It Does** | Lets a registered user sign in to HomeLodge using either their email and password or their Google account. If someone signs in with Google for the first time and does not already have a HomeLodge account, the system creates one for them automatically. |
| **Before This Can Happen** | The person must have either a HomeLodge account or a valid Google account. The account must not be locked or deactivated. |
| **After This Is Done** | The person is signed in and taken to their personal dashboard. If a new account was created through Google, it is set up with the "Guest" role. |

**How It Works — Email & Password:**

1. The person goes to the sign-in page and enters their email and password.
2. The system verifies the credentials.
3. If correct, the system checks whether the account is active and not locked.
4. The person is signed in and taken to their dashboard.
5. If an administrator previously reset this person's password, the system will ask them to set a new personal password before they can continue (see UC-AUTH-06).

**How It Works — Google Sign-In:**

1. The person clicks "Continue with Google" on the sign-in or registration page.
2. They are taken to Google's sign-in screen, where they grant permission.
3. Google sends the person's name and email back to HomeLodge.
4. If an account with that email already exists, the person is signed in directly.
5. If no account exists, the system creates a new Guest account using the Google profile information.
6. The person is signed in and taken to their dashboard.

**What If Something Goes Wrong?**

- **Wrong email or password:** The system shows a general message ("Invalid email or password.") without revealing which part is incorrect, to protect account security.
- **Account is deactivated:** The system informs the person that their account is deactivated and asks them to contact support.
- **Account is locked (too many failed attempts):** The system shows a lockout message with the estimated time until the account unlocks. The person can also reset their password to unlock it immediately.
- **Google sign-in cancelled or failed:** The system returns the person to the sign-in page with a message that Google sign-in was not completed.

---

### UC-AUTH-03: Logout

| Field | Detail |
|---|---|
| **Use Case ID** | UC-AUTH-03 |
| **Use Case Name** | Logout |
| **Who Is Involved** | Registered User (Guest or Admin who is currently signed in) |
| **What It Does** | Allows a signed-in user to end their session and sign out of HomeLodge. After signing out, the person must sign in again to access any protected areas of the system. |
| **Before This Can Happen** | The person is currently signed in. |
| **After This Is Done** | The session is ended. The person is taken back to the sign-in page and can no longer view protected pages without signing in again. |

**How It Works:**

1. The person clicks the "Logout" button in the navigation menu.
2. The system ends the current session.
3. The person is redirected to the sign-in page.
4. If they try to go back using the browser's back button, they will be shown the sign-in page instead of the protected content.

---

### UC-AUTH-04: Forgot Password (Reset via Email)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-AUTH-04 |
| **Use Case Name** | Forgot Password (Reset via Email) |
| **Who Is Involved** | Guest, Registered User, Admin |
| **What It Does** | Helps a person who has forgotten their password to set a new one. The system sends a special link to their registered email address. Clicking the link opens a page where they can choose a new password. If the account was previously locked due to too many failed sign-in attempts, this process also unlocks it. |
| **Before This Can Happen** | The person must have a registered email account. The system's email sending feature must be configured and working. |
| **After This Is Done** | The person has a new password and can sign in with it. If their account was locked, the lock is removed. The reset link can no longer be reused. |

**How It Works:**

1. The person clicks "Forgot Password" on the sign-in page.
2. They enter their registered email address.
3. The system displays a message saying: "If an account exists with this email, a reset link has been sent." (This message is the same regardless of whether the email is found, to protect privacy.)
4. If the email address is in the system, a time-limited reset link is sent.
5. The person opens their email and clicks the reset link.
6. The system confirms the link is still valid (not expired or already used).
7. A password reset form appears. The person enters a new password and confirms it.
8. The system saves the new password and, if the account was locked, removes the lock.
9. The person is taken back to the sign-in page with a success message.

**What If Something Goes Wrong?**

- **Email not found:** The same generic message is shown to prevent others from discovering whether an email is registered.
- **Link expired or already used:** The system tells the person the link is no longer valid and suggests requesting a new one.
- **New password does not meet the strength rules:** The system highlights which rules are not satisfied so the person can correct it.

---

### UC-AUTH-05: View / Update Profile

| Field | Detail |
|---|---|
| **Use Case ID** | UC-AUTH-05 |
| **Use Case Name** | View / Update Profile |
| **Who Is Involved** | Registered User (Guest or Admin) |
| **What It Does** | Lets a signed-in user view and update their personal details, such as their name, phone number, and profile photo. |
| **Before This Can Happen** | The person must be signed in. |
| **After This Is Done** | The updated information is saved and reflected across the system. |

**How It Works:**

1. The person goes to the Profile page from the navigation menu.
2. The system shows their current details — name, email, phone number, and profile photo.
3. The person changes whatever they need (for example, updating their phone number or uploading a new photo).
4. They click "Save."
5. The system checks that the information is valid (for example, the phone number format is correct and the photo file is an accepted image type within the size limit).
6. If everything is valid, the changes are saved and a confirmation message is shown.

**What If Something Goes Wrong?**

- **Invalid phone number:** The system highlights the field and asks for correction.
- **Photo file too large or wrong format:** The system shows an appropriate error message.

---

### UC-AUTH-06: Force Change Password

| Field | Detail |
|---|---|
| **Use Case ID** | UC-AUTH-06 |
| **Use Case Name** | Force Change Password |
| **Who Is Involved** | Registered User (whose password was reset by an administrator), Admin (who initiates the reset) |
| **What It Does** | When an administrator resets a user's password, the user is required to choose a new personal password the next time they sign in. Until they do so, they cannot access any other part of the system. This ensures the temporary password is replaced with one that only the user knows. |
| **Before This Can Happen** | An administrator has reset the user's password. The user has successfully signed in using the temporary password. |
| **After This Is Done** | The user has set a new personal password and can now use the system normally. |

**How It Works:**

1. The user signs in with the temporary password given by the administrator.
2. The system detects that a password change is required.
3. Instead of going to the dashboard, the user is taken to a "Change Your Password" page.
4. The user cannot navigate to any other page until this step is completed.
5. The user enters a new password and confirms it, following the password strength rules.
6. The system saves the new password.
7. The user is redirected to their dashboard and can now use the system as normal.

**What If Something Goes Wrong?**

- **New password is the same as the temporary one:** The system asks for a different password.
- **Password does not meet the strength rules:** The system highlights the failing rules.
- **User tries to navigate elsewhere:** The system redirects them back to the password change page.

---

## 2. Homestay Management Module

This module is about the properties themselves — the homestay units that guests can browse and book. Guests can view available units and their details, while administrators can create, edit, and manage the units behind the scenes.

---

### UC-HS-01: Browse Homestay Units

| Field | Detail |
|---|---|
| **Use Case ID** | UC-HS-01 |
| **Use Case Name** | Browse Homestay Units |
| **Who Is Involved** | Guest |
| **What It Does** | Displays a list of all available homestay units so that guests can explore what is on offer. Each unit is shown with a summary that includes its name, a photo, the nightly price, the location, and the average guest rating. |
| **Before This Can Happen** | At least one active homestay unit exists in the system. |
| **After This Is Done** | The guest can see all available units and choose one to view in more detail. |

**How It Works:**

1. The guest opens the homestay listing page (this page is accessible to everyone, even without signing in).
2. The system shows all active units, each displayed as a card with key information — name, main photo, price per night, location, and average rating from past guests.
3. The guest scrolls through the options and clicks on a unit they are interested in.
4. They are taken to that unit's detailed information page (see UC-HS-02).

**What If Something Goes Wrong?**

- **No units available:** The system shows a message saying "No homestay units are currently available."

---

### UC-HS-02: View Unit Details & Availability

| Field | Detail |
|---|---|
| **Use Case ID** | UC-HS-02 |
| **Use Case Name** | View Unit Details & Availability |
| **Who Is Involved** | Guest |
| **What It Does** | Shows the full details of a specific homestay unit, including all photos, a complete description, pricing, check-in and check-out times, house rules (policies), guest reviews, and a calendar showing which dates are available or already booked. This gives the guest everything they need to decide whether to book. |
| **Before This Can Happen** | The guest has selected a unit from the listing page. |
| **After This Is Done** | The guest has all the information they need and can proceed to book by clicking "Book Now." |

**How It Works:**

1. The guest clicks on a unit from the listing page.
2. The system shows the full detail page, which includes:
   - All uploaded photos in a gallery view.
   - A full description of the property.
   - Location/address.
   - Base price per night and deposit amount.
   - Default check-in and check-out times.
   - House rules and policies (e.g., No Pets, No Smoking).
   - Guest reviews and the average rating.
3. An availability calendar is displayed, colour-coded to show which dates are free, which are already booked, and which are temporarily held (awaiting payment).
4. If the guest wants to proceed, they click "Book Now" to start the booking process.

---

### UC-HS-03: Create Homestay Unit

| Field | Detail |
|---|---|
| **Use Case ID** | UC-HS-03 |
| **Use Case Name** | Create Homestay Unit |
| **Who Is Involved** | Admin |
| **What It Does** | Allows an administrator to add a new homestay unit to the system. The administrator fills in all the property details — name, description, location, pricing, check-in/check-out times, and photos. When the unit is created, the system automatically applies the standard house rules (e.g., No Pets, No Smoking) so the administrator does not need to set them up manually each time. |
| **Before This Can Happen** | The administrator must be signed in with permission to create homestay units. |
| **After This Is Done** | The new unit is live and visible to guests on the listing page. Standard house rules are already attached to it. |

**How It Works:**

1. The administrator goes to Homestay Management and clicks "Create New Unit."
2. They fill in the required details: unit name, description, location/address, base price per night, and deposit amount.
3. They set the default check-in and check-out times.
4. They upload one or more photos of the property.
5. Optionally, they can set a custom payment window for stay extensions (how long a guest has to pay an extension charge before it is cancelled).
6. They click "Save."
7. The system checks that all required fields are filled in correctly.
8. The system saves the unit and automatically attaches the system-wide default house rules to it.
9. The unit immediately appears on the guest-facing listing page.

**What If Something Goes Wrong?**

- **Required field missing:** The system highlights the missing fields.
- **Invalid photo file:** The system asks for a valid image format.
- **Photo upload fails:** The unit is saved but a warning is shown. The administrator can upload photos later by editing the unit.

---

### UC-HS-04: Edit Homestay Unit

| Field | Detail |
|---|---|
| **Use Case ID** | UC-HS-04 |
| **Use Case Name** | Edit Homestay Unit |
| **Who Is Involved** | Admin |
| **What It Does** | Allows an administrator to update any details of an existing homestay unit — such as its name, description, pricing, photos, check-in/check-out times, or house policies. All changes take effect immediately across the system. |
| **Before This Can Happen** | The unit must already exist. The administrator must be signed in with permission to edit homestay units. |
| **After This Is Done** | The updated information is saved and reflected on the guest-facing pages. |

**How It Works:**

1. The administrator goes to Homestay Management, selects the unit, and clicks "Edit."
2. The system shows a form pre-filled with the unit's current details.
3. The administrator changes whatever is needed (e.g., updates the price, adds new photos, changes the check-out time, or edits house rules).
4. They click "Save."
5. The system checks the changes are valid and saves them.
6. A confirmation message is shown. The changes are immediately visible to guests.

**What If Something Goes Wrong?**

- **Invalid information entered:** The system highlights the problem fields and keeps what was entered so nothing is lost.

---

### UC-HS-05: Deactivate / Delete Unit

| Field | Detail |
|---|---|
| **Use Case ID** | UC-HS-05 |
| **Use Case Name** | Deactivate / Delete Unit |
| **Who Is Involved** | Admin |
| **What It Does** | Allows an administrator to take a homestay unit off the market. Once deactivated, the unit is hidden from the guest listing page and no new bookings can be made. Existing confirmed bookings for the unit are not affected. All data about the unit is kept on record. |
| **Before This Can Happen** | The administrator must be signed in with permission to manage units. The unit should not have any upcoming confirmed bookings (if it does, the system will warn the administrator). |
| **After This Is Done** | The unit is no longer visible to guests and cannot accept new bookings. |

**How It Works:**

1. The administrator opens the unit's management page.
2. They click "Deactivate" or "Delete."
3. The system checks whether there are any upcoming confirmed bookings for the unit.
4. If there are no conflicts, the system asks for confirmation.
5. The administrator confirms.
6. The unit is hidden from the guest listing page. A confirmation message is shown.

**What If Something Goes Wrong?**

- **Unit has upcoming bookings:** The system blocks the action and lists the conflicting bookings. The administrator must cancel or rearrange those bookings first.
- **Administrator cancels the confirmation:** No changes are made.

---

### UC-HS-06: View All Units List

| Field | Detail |
|---|---|
| **Use Case ID** | UC-HS-06 |
| **Use Case Name** | View All Units List |
| **Who Is Involved** | Admin |
| **What It Does** | Shows the administrator a complete list of all homestay units in the system — both active and inactive — along with their status, number of upcoming bookings, and quick links to edit or manage each one. This is the administrator's main overview screen for property management. |
| **Before This Can Happen** | The administrator must be signed in with homestay management access. |
| **After This Is Done** | The administrator has a full picture of all managed properties and can take action from this page. |

**How It Works:**

1. The administrator goes to the Homestay Management section.
2. The system displays a list or table of all units, showing each unit's name, status (active or inactive), number of upcoming confirmed bookings, base price, and action buttons (Edit, Deactivate, Manage Policies).
3. The administrator can search or filter the list by status.

---

## 3. Booking Module

This module covers the entire booking journey — from a guest choosing their dates and submitting a reservation, all the way to viewing, managing, and cancelling bookings. It also handles the system's automatic cancellation of bookings when payment is not received in time.

---

### UC-BK-01: View Availability & Select Dates

| Field | Detail |
|---|---|
| **Use Case ID** | UC-BK-01 |
| **Use Case Name** | View Availability & Select Dates |
| **Who Is Involved** | Guest |
| **What It Does** | Shows the guest a calendar view of a specific homestay unit so they can see which dates are available and which are already booked. The guest can then select their preferred check-in and check-out dates. The system checks in real time whether the selected dates are still available. |
| **Before This Can Happen** | The guest has selected a unit from the listing or detail page. |
| **After This Is Done** | The guest has chosen valid, available dates and can proceed to submit their booking. |

**How It Works:**

1. The guest views the availability calendar on the unit detail page or booking form.
2. Dates are colour-coded:
   - **Available** — the guest can book these dates.
   - **Booked** — already reserved by another guest.
   - **Temporarily held** — another guest is in the process of paying for these dates.
   - **Blocked** — the administrator has blocked these dates (e.g., for maintenance).
3. The guest clicks a check-in date, then a check-out date.
4. The system instantly checks that the entire range is available.
5. If the dates are available, the system shows a summary with the number of nights and estimated cost.
6. The guest can adjust check-in and check-out times if the unit allows it.

**What If Something Goes Wrong?**

- **Dates overlap with an existing booking:** The system highlights the conflict and asks the guest to choose different dates.
- **Check-out date is before the check-in date:** The system shows a validation message.

---

### UC-BK-02: Submit Booking

| Field | Detail |
|---|---|
| **Use Case ID** | UC-BK-02 |
| **Use Case Name** | Submit Booking |
| **Who Is Involved** | Guest |
| **What It Does** | Allows the guest to confirm and submit a booking for their selected dates. Once submitted, the system reserves those dates for the guest and gives them a one-day window to complete payment. A bill is automatically created, and the guest is notified with payment instructions. If payment is not made within the window, the booking will be automatically cancelled (see UC-BK-07). |
| **Before This Can Happen** | The guest must be signed in. The selected dates must be available. |
| **After This Is Done** | A booking is created and the selected dates are reserved. A bill is generated. The guest receives a notification with the payment deadline and instructions. |

**How It Works:**

1. The guest reviews the booking summary — the unit name, dates, times, and total cost including any deposit.
2. They click "Confirm Booking."
3. The system double-checks that the dates are still available (to prevent two guests booking the same dates at the same moment).
4. The booking is created with a one-day payment deadline.
5. A bill is automatically generated with a unique bill number.
6. The guest receives both an in-app notification and an email with the bill and payment deadline.
7. The guest is directed to the payment page.

**What If Something Goes Wrong?**

- **Dates became unavailable between selection and submission:** The system informs the guest and asks them to choose different dates.
- **System error during booking creation:** No booking is created. The guest can try again.

---

### UC-BK-03: View Bookings

| Field | Detail |
|---|---|
| **Use Case ID** | UC-BK-03 |
| **Use Case Name** | View Bookings |
| **Who Is Involved** | Guest, Admin |
| **What It Does** | Lets a guest see all their reservations in one place — both current (active and upcoming) and past (completed and cancelled). The administrator can also view all bookings across the system. Bookings can be filtered by status, date, or other criteria. From here, users can navigate to individual booking details. |
| **Before This Can Happen** | The user must be signed in. For guests, they must have at least one booking. |
| **After This Is Done** | The user can see a clear overview of all relevant bookings. |

**How It Works (Guest):**

1. The guest goes to "My Bookings."
2. The system shows their bookings organised into sections or tabs — for example, "Current" (active and upcoming) and "History" (completed and cancelled).
3. Each booking is shown with key details: unit name, check-in/check-out dates, status, and amount.
4. The guest can click any booking to see its full details (see UC-BK-04).
5. For completed bookings, the guest can access their receipt or leave a review.

**How It Works (Admin):**

1. The administrator goes to the Booking Management section.
2. The system displays all bookings across all units with filtering options (by status, date range, unit, or booking reference).
3. The administrator can use the booking calendar view to see a visual overview of all reservations across properties.

---

### UC-BK-04: View Booking Details

| Field | Detail |
|---|---|
| **Use Case ID** | UC-BK-04 |
| **Use Case Name** | View Booking Details |
| **Who Is Involved** | Guest, Admin |
| **What It Does** | Shows the complete details of a specific booking, including the unit information, dates and times, booking status, payment status, the cancellation policy, QR access code (if the booking is confirmed), and any extension history. |
| **Before This Can Happen** | The booking exists and belongs to the signed-in guest, or the viewer is an administrator. |
| **After This Is Done** | The user has full visibility of the reservation and can take available actions (e.g., download bill, cancel, view receipt). |

**How It Works:**

1. The user clicks on a booking from their bookings list.
2. The system shows all booking information:
   - Unit name and photo.
   - Check-in and check-out dates and times.
   - Total amount and payment status.
   - Booking status (e.g., Pending Payment, Confirmed, Completed, Cancelled).
   - QR access code (shown only for confirmed bookings).
   - Cancellation policy and estimated refund if the booking were to be cancelled.
3. The user can download the bill or receipt from this page.

---

### UC-BK-05: Cancel Booking

| Field | Detail |
|---|---|
| **Use Case ID** | UC-BK-05 |
| **Use Case Name** | Cancel Booking |
| **Who Is Involved** | Guest, Admin |
| **What It Does** | Allows a guest or administrator to cancel a booking. If a payment has already been made, the system calculates the refund amount based on the cancellation policy (how far in advance the cancellation is made). The refund rules are set by the administrator in the system settings. |
| **Before This Can Happen** | The booking must be in an active state (either awaiting payment or confirmed). |
| **After This Is Done** | The booking is marked as cancelled. The previously reserved dates become available for other guests. If a refund applies, it is processed. Both the guest and the administrator are notified. |

**How It Works:**

1. The user opens the booking detail page and clicks "Cancel Booking."
2. The system calculates the refund based on the cancellation policy. For example:
   - More than 14 days before check-in: full refund (100%).
   - 7 to 14 days before check-in: half refund (50%).
   - Less than 7 days before check-in: no refund (0%).
3. The system displays a confirmation message showing the refund amount and a clear warning that this action cannot be undone.
4. The user confirms the cancellation.
5. The booking is cancelled and the dates are released.
6. If payment was already made, the refund is processed through the payment service.
7. Both the guest and the administrator receive a cancellation notification (in-app and by email).

**What If Something Goes Wrong?**

- **User cancels the confirmation prompt:** No changes are made.
- **Booking was still awaiting payment:** Since no payment was made, there is nothing to refund. The booking is simply cancelled.
- **Refund processing issue:** The cancellation still goes through, but the refund is flagged for the administrator to handle manually.

---

### UC-BK-06: Manage Booking (Admin)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-BK-06 |
| **Use Case Name** | Manage Booking (Admin) |
| **Who Is Involved** | Admin |
| **What It Does** | Gives the administrator full control over bookings. The administrator can create bookings on behalf of guests (e.g., for walk-in or phone reservations), edit existing booking details, delete erroneous bookings, view the all-unit booking calendar, filter the booking list, and block specific dates on a unit to prevent new bookings (e.g., for maintenance). |
| **Before This Can Happen** | The administrator must be signed in with the appropriate booking management permissions. |
| **After This Is Done** | The booking records are updated as needed. Guests are notified of any changes that affect them. |

**How It Works — Create Booking on Behalf of a Guest:**

1. The administrator goes to Bookings and clicks "Create Booking."
2. They select the guest's account, choose the unit, and pick the check-in and check-out dates.
3. The system checks availability and shows a booking summary with the total cost.
4. The administrator confirms. The booking is created and the guest is notified with payment instructions.

**How It Works — Edit a Booking:**

1. The administrator opens a booking and clicks "Edit."
2. They adjust the details (e.g., change dates or add notes).
3. If dates are changed, the system checks that the new dates are available.
4. The changes are saved and the guest is notified of the update.

**How It Works — Delete a Booking:**

1. The administrator selects a booking and clicks "Delete."
2. The system warns that this is permanent and cannot be undone.
3. The administrator confirms. The booking is removed and the dates are released.

**How It Works — Block Dates:**

1. The administrator selects a unit and a date range.
2. They enter an internal note (optional — not shown to guests).
3. They click "Block Dates."
4. The blocked dates immediately appear as unavailable on the guest-facing calendar.

---

### UC-BK-07: Auto-Cancel Expired Booking

| Field | Detail |
|---|---|
| **Use Case ID** | UC-BK-07 |
| **Use Case Name** | Auto-Cancel Expired Booking |
| **Who Is Involved** | System (automatic process) |
| **What It Does** | The system automatically checks for bookings that are still awaiting payment after the one-day payment deadline has passed. These bookings are automatically cancelled, and the reserved dates are released so that other guests can book them. The guest is notified that their booking has been cancelled due to non-payment. |
| **Before This Can Happen** | A booking must be in "awaiting payment" status and the payment deadline must have passed. |
| **After This Is Done** | The booking is cancelled. The dates are available again. The guest receives a cancellation notification. |

**How It Works:**

1. The system runs a regular check (approximately once per hour) to look for overdue bookings.
2. For each booking where the payment deadline has passed without payment:
   - The booking is cancelled.
   - The reserved dates are released and become available.
   - The guest is notified (both in the app and by email) that their booking was cancelled because payment was not received in time.

---

## 4. Payment Module

This module handles all financial transactions — from making a payment for a booking to viewing bills and receipts. It connects with the online payment service to process payments securely.

---

### UC-PAY-01: Make Payment

| Field | Detail |
|---|---|
| **Use Case ID** | UC-PAY-01 |
| **Use Case Name** | Make Payment |
| **Who Is Involved** | Guest, Payment Gateway (the external online payment service) |
| **What It Does** | Allows the guest to pay for their booking through a secure online payment service. Once payment is successfully received, the booking is confirmed, a QR access code is generated and sent to the guest, and a payment receipt is created. |
| **Before This Can Happen** | The guest has a booking that is awaiting payment. The payment deadline has not yet passed. |
| **After This Is Done** | The payment is recorded as successful. The booking status changes to "Confirmed." A QR access code is generated. A receipt is created. Both the guest and the administrator are notified. |

**How It Works:**

1. The guest clicks "Pay Now" from their booking detail page or from the bill notification.
2. The system directs them to the secure payment page provided by the payment service.
3. The guest completes the payment on that page.
4. The payment service notifies HomeLodge that the payment was successful.
5. The system records the payment and confirms the booking.
6. A QR access code is generated for the guest (so they can access the property during their stay).
7. A payment receipt is created.
8. The guest receives a confirmation notification with the receipt and QR code.
9. The administrator is also notified of the new confirmed booking.

**What If Something Goes Wrong?**

- **Payment is declined or fails:** The guest is brought back with an error message and can try again.
- **Guest leaves the payment page without completing:** The booking remains awaiting payment until the deadline passes.

---

### UC-PAY-02: View Payment & Billing Records

| Field | Detail |
|---|---|
| **Use Case ID** | UC-PAY-02 |
| **Use Case Name** | View Payment & Billing Records |
| **Who Is Involved** | Guest, Admin |
| **What It Does** | Allows users to view bills and payment records. Guests can see their own bills and payment history with details such as bill number, amount, status, and dates. They can view or download bills and receipts. Administrators can see all billing and payment records across the entire system and filter them by various criteria. |
| **Before This Can Happen** | The user must be signed in. At least one billing or payment record must exist. |
| **After This Is Done** | The user has a clear view of all relevant financial records. |

**How It Works (Guest):**

1. The guest navigates to their booking detail or "Payment History" section.
2. The system shows a list of their payment transactions — each with a payment reference, date, amount, and status.
3. The guest can view or download the itemised bill (showing the cost breakdown: nightly rate, number of nights, deposit, total amount, and payment deadline).
4. For completed payments, the guest can view or download the receipt.

**How It Works (Admin):**

1. The administrator goes to Payment Management.
2. The system shows two views: the billing list (all bills) and the payment list (all payment transactions).
3. Each entry shows the reference number, guest name, unit, amount, status, and date.
4. The administrator can filter by date range, reference number, or status.
5. The administrator can click any entry to see its full details.

---

### UC-PAY-03: Regenerate Bill / Receipt

| Field | Detail |
|---|---|
| **Use Case ID** | UC-PAY-03 |
| **Use Case Name** | Regenerate Bill / Receipt |
| **Who Is Involved** | Admin |
| **What It Does** | Allows the administrator to create a fresh copy of a bill or receipt document on demand. This is useful when the original document has been lost, or when booking details have been updated and a new document is needed to reflect the current information. |
| **Before This Can Happen** | The corresponding booking and payment records must exist. The administrator must be signed in with the appropriate permission. |
| **After This Is Done** | A new document is generated and available for download. The administrator can optionally resend it to the guest by email. |

**How It Works:**

1. The administrator goes to the billing or payment detail page.
2. They click "Regenerate Bill" or "Regenerate Receipt."
3. The system creates a fresh document using the latest booking and payment information.
4. The new document is available for download and can optionally be emailed to the guest.

---

## 5. Notification Module

This module ensures that everyone stays informed about important events — from booking confirmations to payment reminders and check-in alerts. Notifications are delivered both within the app (via a notification bell) and by email.

---

### UC-NOTIF-01: Receive System Notification

| Field | Detail |
|---|---|
| **Use Case ID** | UC-NOTIF-01 |
| **Use Case Name** | Receive System Notification |
| **Who Is Involved** | Registered User (Guest or Admin), System |
| **What It Does** | Delivers timely notifications to users whenever something important happens in the system. Notifications are sent both within the app (appearing under a bell icon in the navigation bar) and by email. Examples include: booking confirmed, payment received, booking cancelled, extension charge issued, upcoming check-in reminder, payment deadline approaching, and QR code reminders. |
| **Before This Can Happen** | The user must have an account. A relevant event must have occurred. |
| **After This Is Done** | The user is informed about the event and can click the notification to view the related details. |

**How It Works:**

1. An important event happens (e.g., a booking is confirmed, a payment is received, or a check-in date is approaching).
2. The system creates a notification for the relevant user(s).
3. **In-App Notification:** If the user is currently online, the bell icon updates immediately to show a new notification. When they click the bell, they see a list of all notifications, newest first. Clicking a notification marks it as read and takes them to the relevant page (e.g., the booking detail).
4. **Email Notification:** The system also sends an email to the user's registered email address with the same information. (The administrator can turn email notifications on or off globally from the system settings.)
5. **Automated Reminders:** The system also sends reminders for upcoming events, such as:
   - Payment reminders when the deadline is approaching.
   - Check-in and check-out reminders to help both guests and administrators prepare.

---

### UC-NOTIF-02: Google Calendar Integration

| Field | Detail |
|---|---|
| **Use Case ID** | UC-NOTIF-02 |
| **Use Case Name** | Google Calendar Integration |
| **Who Is Involved** | Registered User (Guest or Admin), Google Calendar |
| **What It Does** | Automatically adds confirmed bookings to the user's Google Calendar, so they can see their homestay reservations alongside their other personal events. The calendar event spans from the check-in date to the check-out date and includes the booking details. |
| **Before This Can Happen** | The user must have connected their Google account to HomeLodge. A booking must be confirmed. |
| **After This Is Done** | A calendar event appears in the user's Google Calendar showing the check-in and check-out dates, the unit name, and the booking reference. |

**How It Works:**

1. A booking is confirmed (after successful payment).
2. The system checks whether the user has connected their Google Calendar.
3. If they have, the system creates (or updates) a calendar event with the stay details — the unit name, check-in date/time, check-out date/time, and booking reference.
4. The event appears in the user's Google Calendar.

**What If Something Goes Wrong?**

- **Google Calendar not connected:** The system simply skips this step. No error is shown.
- **Connection issue with Google:** The system records the issue and moves on. The user may need to reconnect their Google account.

---

## 6. Chat Module

This module provides a built-in messaging system so that guests and administrators can communicate directly within HomeLodge. Messages are delivered instantly and all conversations are saved for future reference.

---

### UC-CHAT-01: Send / Receive Messages

| Field | Detail |
|---|---|
| **Use Case ID** | UC-CHAT-01 |
| **Use Case Name** | Send / Receive Messages |
| **Who Is Involved** | Guest, Admin |
| **What It Does** | Allows guests and administrators to exchange text messages in real time through the built-in chat feature. Messages are delivered instantly without the need to refresh the page. If the other person is not currently online, the message is saved and they will see it the next time they sign in. |
| **Before This Can Happen** | Both parties must have accounts. The sender must be signed in. |
| **After This Is Done** | The message is saved and delivered. The recipient sees the message immediately if they are online, or upon their next sign-in. |

**How It Works:**

1. The user opens the Chat page.
2. They type a message and click "Send" (or press Enter).
3. The message is saved and instantly appears in both parties' chat windows.
4. If the recipient is online, the message appears in real time. If they are not online, an unread message indicator appears when they next sign in.

**What If Something Goes Wrong?**

- **Empty message:** The system does not allow sending an empty message. The send button is disabled until text is entered.
- **Connection interruption:** The message is still saved. The user will see a "Reconnecting..." indicator, and the message will appear when the connection is restored.

---

### UC-CHAT-02: View Chat History

| Field | Detail |
|---|---|
| **Use Case ID** | UC-CHAT-02 |
| **Use Case Name** | View Chat History |
| **Who Is Involved** | Guest, Admin |
| **What It Does** | Shows the full conversation history between a guest and an administrator. All messages are displayed in the order they were sent, with timestamps and sender names. Unread messages are marked as read when viewed. |
| **Before This Can Happen** | At least one message has been exchanged. |
| **After This Is Done** | The user can read the entire conversation. Any previously unread messages are now marked as read. |

**How It Works:**

1. The user opens the Chat page.
2. The system displays all messages in the conversation, from oldest to newest.
3. Each message shows who sent it, what it says, and when it was sent.
4. The chat automatically scrolls to the most recent message.
5. All unread messages are marked as read.

---

## 7. User & Access Management Module

This module is for administrators only. It handles the management of user accounts, roles, and permissions. Roles define what a user can do in the system (e.g., "Admin" vs. "Guest"), and permissions are the individual capabilities assigned to each role.

---

### UC-USR-01: Create User Account

| Field | Detail |
|---|---|
| **Use Case ID** | UC-USR-01 |
| **Use Case Name** | Create User Account |
| **Who Is Involved** | Admin |
| **What It Does** | Allows an administrator to manually create a new user account and assign a role to it. The new user is given a temporary password and will be required to change it the first time they sign in. The new user is notified with their sign-in details. |
| **Before This Can Happen** | The email address must not already be registered. The administrator must be signed in with the appropriate permission. |
| **After This Is Done** | A new account is created with the assigned role. The user is notified by email with their temporary password and instructions to sign in. |

**How It Works:**

1. The administrator goes to User Management and clicks "Create User."
2. They enter the person's full name, email address, and assign a role (e.g., Guest or Admin).
3. The system generates a temporary password for the account.
4. The account is created and flagged so that the user must change their password on first sign-in.
5. The new user receives an email with their temporary password and a link to sign in.
6. The administrator sees a success message.

**What If Something Goes Wrong?**

- **Email already in use:** The system shows a message saying the email is already registered.

---

### UC-USR-02: Edit / Activate / Deactivate User

| Field | Detail |
|---|---|
| **Use Case ID** | UC-USR-02 |
| **Use Case Name** | Edit / Activate / Deactivate User |
| **Who Is Involved** | Admin |
| **What It Does** | Allows the administrator to update a user's details (name, email, phone, or role) and to activate or deactivate their account. Deactivating an account prevents the user from signing in. Re-activating restores their access. |
| **Before This Can Happen** | The user account must exist. The administrator must be signed in with the appropriate permission. |
| **After This Is Done** | The account is updated. If a role was changed, the new permissions take effect immediately. If the account was deactivated, the user can no longer sign in. |

**How It Works — Edit Details:**

1. The administrator selects a user and clicks "Edit."
2. They update the desired fields.
3. They click "Save."
4. The system validates the changes and saves them.

**How It Works — Activate / Deactivate:**

1. The administrator selects a user and clicks "Deactivate" (or "Activate" if the account is currently inactive).
2. The system asks for confirmation.
3. The administrator confirms.
4. If deactivating: the account is disabled and any active sessions for that user are immediately ended.
5. If activating: the account is restored and the user can sign in again.

---

### UC-USR-03: Reset User Password

| Field | Detail |
|---|---|
| **Use Case ID** | UC-USR-03 |
| **Use Case Name** | Reset User Password |
| **Who Is Involved** | Admin |
| **What It Does** | Allows the administrator to reset a user's password. This can be done by either sending a password reset link to the user's email, or by setting the password back to the default temporary password. Either way, the user will be required to choose a new personal password the next time they sign in. If the user's account was locked due to too many failed sign-in attempts, this also unlocks it. |
| **Before This Can Happen** | The user account must exist. The administrator must be signed in with the appropriate permission. |
| **After This Is Done** | The user's password is reset. The user will be required to change it on next sign-in. If the account was locked, it is now unlocked. The user is notified. |

**How It Works:**

1. The administrator selects a user and clicks "Reset Password."
2. They choose a reset method:
   - **Send reset link:** The system sends a password reset email to the user.
   - **Set to default:** The password is immediately set to the default temporary password.
3. The user's account is flagged so they must change their password on next sign-in.
4. If the account was locked, the lock is removed.
5. The user is notified (in-app and by email).

---

### UC-USR-04: Manage Roles

| Field | Detail |
|---|---|
| **Use Case ID** | UC-USR-04 |
| **Use Case Name** | Manage Roles |
| **Who Is Involved** | Admin |
| **What It Does** | Allows the administrator to create, edit, and delete roles. Roles are categories that group a set of permissions together — for example, an "Admin" role might have permission to manage bookings, while a "Guest" role only has permission to make bookings. The administrator can also assign specific permissions to each role. |
| **Before This Can Happen** | The administrator must be signed in with role management access. |
| **After This Is Done** | Roles are updated. Any changes to a role's permissions take effect immediately for all users with that role. |

**How It Works:**

1. The administrator goes to Role Management.
2. They can:
   - **Create a role:** Enter a name and optional description for the new role, then assign permissions to it.
   - **Edit a role:** Change the name, description, or assigned permissions of an existing role.
   - **Delete a role:** Remove a role that is no longer needed. However, a role cannot be deleted if it is currently assigned to any users — those users must be reassigned to a different role first.

**What If Something Goes Wrong?**

- **Trying to delete a role that is assigned to users:** The system blocks the deletion and lists the number of users who currently have this role. The administrator must reassign them first.

---

### UC-USR-05: Manage Permissions

| Field | Detail |
|---|---|
| **Use Case ID** | UC-USR-05 |
| **Use Case Name** | Manage Permissions |
| **Who Is Involved** | Admin |
| **What It Does** | Allows the administrator to create, edit, and delete individual permissions. Permissions are the building blocks that are assigned to roles — they define specific capabilities such as "can create bookings," "can manage users," or "can view reports." |
| **Before This Can Happen** | The administrator must be signed in with permission management access. |
| **After This Is Done** | Permissions are updated. They can now be assigned to roles. |

**How It Works:**

1. The administrator goes to Permission Management.
2. They can:
   - **Create a permission:** Enter a name and optional description for the new permission.
   - **Edit a permission:** Change the name or description.
   - **Delete a permission:** Remove a permission that is no longer needed. However, a permission cannot be deleted if it is currently attached to any role — it must be removed from all roles first.

**What If Something Goes Wrong?**

- **Trying to delete a permission that is attached to roles:** The system blocks the deletion and shows the number of roles using this permission. The administrator must remove the permission from those roles first.

---

## 8. System Settings Module

This module gives the administrator control over the system's global configuration — everything from email settings and security rules to cancellation policies, payment options, and default house rules. Changes made here affect the entire system.

---

### UC-SET-01: Configure System Settings

| Field | Detail |
|---|---|
| **Use Case ID** | UC-SET-01 |
| **Use Case Name** | Configure System Settings |
| **Who Is Involved** | Admin |
| **What It Does** | Provides a centralised settings page where the administrator can configure all system-wide options. This includes email settings (for sending notifications), security settings (login attempt limits and lockout rules), cancellation and refund policy, payment and billing options, extension charge rates, the default payment window for stay extensions, and whether email notifications are turned on or off. |
| **Before This Can Happen** | The administrator must be signed in with system settings access. |
| **After This Is Done** | All updated settings are saved and take effect immediately across the system. |

**What Can Be Configured:**

| Setting Area | What It Controls |
|---|---|
| **Email (Outgoing Mail)** | The email server details used to send notifications and password reset links to users. The administrator can test the connection before saving. |
| **Security** | How many failed sign-in attempts are allowed before an account is locked, how long the lockout lasts, and how long a user's session remains active before they are automatically signed out. |
| **Cancellation & Refund Policy** | The rules for how much of a refund a guest receives when they cancel a booking, based on how far in advance the cancellation is made. For example: more than 14 days = full refund; 7–14 days = half refund; less than 7 days = no refund. |
| **Payment & Billing** | Payment service credentials, the format for bill numbers, the initial booking payment window (how long a guest has to pay before the booking is auto-cancelled), and deposit rules. |
| **Extension Charges** | The extra charge rate per hour (for same-day time extensions) and per night (for overnight date extensions). |
| **Extension Payment Window** | The default amount of time (in minutes) a guest has to pay an extension charge before the extension is automatically cancelled. Individual units can override this with their own custom window. |
| **Email Notifications Toggle** | A switch to turn email notifications on or off system-wide. When turned off, all email notifications are suppressed, but in-app notifications continue to work normally. |

**How It Works:**

1. The administrator goes to System Settings.
2. They navigate to the relevant section (e.g., Security, Payment, Refund Policy).
3. They update the values and click "Save."
4. The system validates the settings and saves them. A confirmation message is shown.
5. All future operations in the system will use the new settings.

---

### UC-SET-02: Manage Default Homestay Policies

| Field | Detail |
|---|---|
| **Use Case ID** | UC-SET-02 |
| **Use Case Name** | Manage Default Homestay Policies |
| **Who Is Involved** | Admin |
| **What It Does** | Allows the administrator to manage the set of default house rules that are automatically applied to every new homestay unit when it is created. The system comes with built-in defaults such as "No Pets," "No Durians," and "No Smoking." The administrator can add new default rules, edit existing ones, or remove rules that are no longer needed. |
| **Before This Can Happen** | The administrator must be signed in with system settings access. |
| **After This Is Done** | The default policy list is updated. Any new homestay units created after this change will inherit the updated defaults. Existing units are not affected — their policies can be managed individually from the unit's edit page. |

**How It Works:**

1. The administrator goes to System Settings → Default Policies.
2. The system shows the current list of default house rules.
3. The administrator can:
   - **Add** a new default rule by entering a description and saving.
   - **Edit** an existing rule by changing its text.
   - **Remove** a rule (with a confirmation prompt).
4. Changes are saved. Any homestay units created in the future will start with this updated set of rules.

---

## 9. Audit Logs Module

This module keeps a permanent, tamper-proof record of everything that happens in the system. This provides accountability and helps the administrator track who did what and when.

---

### UC-AUDIT-01: View / Filter Audit Trail

| Field | Detail |
|---|---|
| **Use Case ID** | UC-AUDIT-01 |
| **Use Case Name** | View / Filter Audit Trail |
| **Who Is Involved** | Admin |
| **What It Does** | Allows the administrator to view a complete, chronological record of all significant actions taken in the system — by users, administrators, and the system itself. The log includes events such as sign-ins, bookings created, payments received, settings changed, accounts modified, and automatic actions (like auto-cancellation of expired bookings). The administrator can filter the log by date range, type of event, or specific user. Log entries cannot be edited or deleted. |
| **Before This Can Happen** | The administrator must be signed in with audit log access. |
| **After This Is Done** | The administrator has a clear picture of system activity and can investigate any event in detail. |

**How It Works:**

1. The administrator goes to the Audit Logs section.
2. The system displays all recorded events, newest first. Each entry shows:
   - When it happened (date and time).
   - Who did it (user name or "System" for automatic actions).
   - What happened (e.g., "Booking created," "Payment received," "User account deactivated").
   - What was affected (e.g., the specific booking, user, or setting).
3. The administrator can apply filters to narrow the list — for example, showing only events from a specific date range, only certain types of events (e.g., payment events), or only actions by a particular user.
4. The administrator can clear the filters to return to the full log.

---

### UC-AUDIT-02: Automatic Event Logging

| Field | Detail |
|---|---|
| **Use Case ID** | UC-AUDIT-02 |
| **Use Case Name** | Automatic Event Logging |
| **Who Is Involved** | System (automatic process) |
| **What It Does** | The system automatically records a log entry every time a significant action occurs — whether it is performed by a user, an administrator, or the system itself. This happens behind the scenes without anyone needing to do anything. The recorded events include all user actions (signing in, booking, paying, leaving feedback), all administrator actions (managing users, changing settings, editing bookings), and all automatic system actions (auto-cancelling expired bookings, expiring QR codes, processing payments). |
| **Before This Can Happen** | A recordable event must occur somewhere in the system. |
| **After This Is Done** | A permanent, unchangeable log entry is created. It can be viewed by administrators through the Audit Trail (UC-AUDIT-01) but cannot be modified or deleted by anyone. |

**How It Works:**

1. Any significant event occurs in the system (e.g., a guest submits a booking, an administrator changes a setting, or the system auto-cancels an expired booking).
2. The system automatically creates a log entry recording: the time, the person (or "System" if automatic), the type of event, and what was affected.
3. The entry is permanently saved and cannot be edited or removed.

---

## 10. QR Code & Access Module

This module manages the digital QR codes that allow guests to access their booked homestay units. QR codes are automatically generated when a booking is confirmed and automatically expire at check-out. The module also handles housekeeping access and booking extensions (when a guest wants to stay longer).

---

### UC-QR-01: Receive & Use QR Code

| Field | Detail |
|---|---|
| **Use Case ID** | UC-QR-01 |
| **Use Case Name** | Receive & Use QR Code |
| **Who Is Involved** | Guest |
| **What It Does** | After a guest's booking is confirmed (payment received), the system automatically creates a unique QR access code for them. The QR code is valid only during the guest's booked stay — from check-in time to check-out time. The guest receives the code both in the app and by email, and can use it to unlock the homestay door by showing it to the smart lock scanner. |
| **Before This Can Happen** | The booking must be confirmed (payment received). |
| **After This Is Done** | The guest has a QR code that grants them physical access to the property during their stay. |

**How It Works:**

1. The guest's payment is confirmed and the booking becomes active.
2. The system generates a unique QR code that is valid from the check-in date/time until the check-out date/time.
3. The guest receives the QR code via an in-app notification and an email.
4. The QR code is also visible at any time on the guest's booking detail page.
5. To enter the property, the guest holds the QR code up to the smart lock scanner at the door.
6. The lock verifies the code and grants access if the code is valid and the current time is within the allowed window.

**What If Something Goes Wrong?**

- **QR code has expired (past check-out time):** Access is denied. The lock displays a message indicating the code is expired.
- **QR code has been deactivated:** Access is denied.
- **Guest's phone is out of battery or has no signal:** The guest must contact the administrator for manual assistance.

---

### UC-QR-02: Manage Housekeeping Cycle

| Field | Detail |
|---|---|
| **Use Case ID** | UC-QR-02 |
| **Use Case Name** | Manage Housekeeping Cycle |
| **Who Is Involved** | Admin, System |
| **What It Does** | Handles the transition between guests. After a guest checks out, their QR code is automatically expired by the system. The administrator can then generate a temporary QR code for cleaning staff to access the property. Once housekeeping is marked as complete, the system automatically generates a new QR code for the next guest (if there is an upcoming confirmed booking). |
| **Before This Can Happen** | A guest has checked out and their QR code has been expired by the system. |
| **After This Is Done** | The property has been cleaned, and the next guest's QR code (if applicable) has been generated and sent to them. |

**How It Works:**

1. **Automatic QR expiry:** When a guest's check-out time passes, the system automatically expires their QR code so it can no longer be used for access. The booking is marked as completed.
2. **Housekeeping access:** The administrator generates a temporary QR code for the cleaning staff. This code is valid for a short window (e.g., 4 hours) and cannot be confused with a guest code.
3. **Mark housekeeping complete:** Once the property has been cleaned, the administrator marks housekeeping as complete.
4. **Next guest's QR code:** The system automatically checks if there is a next confirmed booking for the unit. If there is, it generates and sends a new QR code to the next guest.
5. If there is no upcoming booking, the system simply skips the QR code generation step.

---

### UC-QR-03: Initiate Booking Extension

| Field | Detail |
|---|---|
| **Use Case ID** | UC-QR-03 |
| **Use Case Name** | Initiate Booking Extension |
| **Who Is Involved** | Admin |
| **What It Does** | When a guest wants to extend their stay (either by checking out later on the same day or adding extra nights), the administrator initiates the extension from the system. The system checks whether the extended dates are available, calculates the additional charge based on the configured rates, and sends the guest a bill with a payment deadline. The guest's QR code is not extended until payment is received. |
| **Before This Can Happen** | The booking must be confirmed and currently active. The extended dates must be available (no conflicting bookings). |
| **After This Is Done** | An extension request is created with a bill and payment deadline. The guest is notified and must pay within the deadline to confirm the extension. |

**How It Works:**

1. The administrator opens the active booking and clicks "Extend Stay."
2. They select the type of extension:
   - **Time extension:** The check-out time is moved later on the same day (e.g., from 12 PM to 3 PM).
   - **Date extension:** Extra nights are added to the stay.
3. The administrator enters the new check-out date/time.
4. The system checks that the extended period is available (no conflicting bookings from other guests).
5. The system calculates the additional charge:
   - For time extensions: based on the configured hourly rate.
   - For date extensions: based on the configured nightly rate.
6. The system creates the extension request and generates a bill.
7. A payment deadline is set (based on the unit-specific or system-wide extension payment window).
8. The guest receives a notification with the extension charge, payment deadline, and a direct link to pay.

**Important:** The guest's QR access code is not extended at this point. It is only updated after the guest pays the extension charge (see UC-QR-04).

---

### UC-QR-04: Pay Extension Charge

| Field | Detail |
|---|---|
| **Use Case ID** | UC-QR-04 |
| **Use Case Name** | Pay Extension Charge |
| **Who Is Involved** | Guest |
| **What It Does** | Allows the guest to pay the additional charge for their booking extension through the online payment service. Once payment is confirmed, the booking dates are updated to reflect the extension, and the guest's QR access code validity is extended accordingly. |
| **Before This Can Happen** | An extension request must exist and be awaiting payment. The payment deadline must not have passed. |
| **After This Is Done** | The extension is confirmed. The booking's check-out date/time is updated. The QR code validity is extended to the new check-out time. The guest receives a confirmation. |

**How It Works:**

1. The guest opens their booking detail page and finds the pending extension charge.
2. They click "Pay Extension Charge."
3. The system directs them to the secure payment page.
4. The guest completes the payment.
5. The system confirms the payment and updates the extension status to "Confirmed."
6. The booking's check-out date and time are updated to the new extended values.
7. The guest's QR access code is extended to remain valid until the new check-out time.
8. The guest receives a confirmation notification: "Your stay has been extended. Your QR code is now valid until [new check-out date/time]."

---

### UC-QR-05: Auto-Cancel Extension

| Field | Detail |
|---|---|
| **Use Case ID** | UC-QR-05 |
| **Use Case Name** | Auto-Cancel Extension |
| **Who Is Involved** | System (automatic process) |
| **What It Does** | The system regularly checks for extension requests whose payment deadline has passed without payment. These extensions are automatically cancelled, and the booking reverts to its original check-out date and time. The guest's QR code is not modified (it stays at the original check-out time). |
| **Before This Can Happen** | An extension request must be awaiting payment and the payment deadline must have passed. |
| **After This Is Done** | The extension is cancelled. The booking returns to its original dates. The guest is notified that the extension was not confirmed due to non-payment. |

**How It Works:**

1. The system runs a regular check (every few minutes) to find extension requests with expired payment deadlines.
2. For each overdue extension:
   - The extension is cancelled.
   - The booking is reverted to its original check-out date and time.
   - The guest is notified: "Your extension request was not confirmed because payment was not received by the deadline. Your booking remains at the original check-out: [original date/time]."
   - The QR code is not modified — it continues to reflect the original check-out time.

---

## 11. Reporting & Analytics Module

This module gives administrators a bird's-eye view of business performance through dashboards, charts, and reports. It helps with decision-making by showing trends in bookings, revenue, and guest satisfaction.

---

### UC-RPT-01: View Analytics Dashboard

| Field | Detail |
|---|---|
| **Use Case ID** | UC-RPT-01 |
| **Use Case Name** | View Analytics Dashboard |
| **Who Is Involved** | Admin |
| **What It Does** | Displays a summary dashboard showing the key business numbers at a glance — total bookings, total revenue, occupancy rate, cancellation rate, and average guest rating. The dashboard also includes visual charts showing booking trends over time and revenue breakdowns. |
| **Before This Can Happen** | The administrator must be signed in with reporting access. |
| **After This Is Done** | The administrator has a clear, visual overview of business performance. |

**How It Works:**

1. The administrator goes to the Reporting & Analytics section.
2. The system calculates and displays key performance indicators:
   - Total bookings (for the current month and year).
   - Total revenue (for the current month and year).
   - Occupancy rate (the percentage of available dates that were booked).
   - Cancellation rate (the percentage of bookings that were cancelled).
   - Average guest rating (from all guest reviews).
3. Visual charts are displayed:
   - A booking trends chart showing booking volumes over time (daily, weekly, or monthly).
   - A revenue summary.
   - A guest feedback and rating summary per unit.
4. The administrator can interact with the charts to explore the data in more detail.

---

### UC-RPT-02: View Revenue Report

| Field | Detail |
|---|---|
| **Use Case ID** | UC-RPT-02 |
| **Use Case Name** | View Revenue Report |
| **Who Is Involved** | Admin |
| **What It Does** | Shows a detailed revenue report that the administrator can filter by date range, specific homestay unit, or payment status. The report shows total revenue, revenue broken down by booking, and revenue summarised by unit. |
| **Before This Can Happen** | Payment records must exist. The administrator must be signed in with reporting access. |
| **After This Is Done** | The administrator can see detailed revenue figures and can download the report for offline use. |

**How It Works:**

1. The administrator goes to Reporting → Revenue Report.
2. They apply filters: date range, unit (all or a specific one), and payment status.
3. The system shows the filtered results: total revenue, a breakdown by individual booking, and a summary by unit.
4. The administrator can download the report as a PDF or spreadsheet file (see UC-RPT-03).

---

### UC-RPT-03: Export Report

| Field | Detail |
|---|---|
| **Use Case ID** | UC-RPT-03 |
| **Use Case Name** | Export Report |
| **Who Is Involved** | Admin |
| **What It Does** | Allows the administrator to download any report (revenue, booking breakdown, feedback summary) as a PDF document or a spreadsheet file (CSV) for offline use, sharing with stakeholders, or archiving. |
| **Before This Can Happen** | The administrator must be viewing a report with data loaded. |
| **After This Is Done** | A file is downloaded to the administrator's device. |

**How It Works:**

1. The administrator is viewing a report and clicks "Export PDF" or "Export CSV."
2. The system collects the currently filtered report data.
3. The system generates the file in the chosen format.
4. The file is automatically downloaded to the administrator's device.

---

## 12. Guest Feedback Module

This module allows guests to share their experience after completing a stay, and gives administrators the tools to manage and respond to guest reviews. Feedback and ratings are visible on the listing page to help future guests make informed choices.

---

### UC-FB-01: Submit Rating & Feedback

| Field | Detail |
|---|---|
| **Use Case ID** | UC-FB-01 |
| **Use Case Name** | Submit Rating & Feedback |
| **Who Is Involved** | Guest |
| **What It Does** | After completing a stay, the guest can rate their experience (from 1 to 5 stars) and optionally write a comment. Each booking allows only one review. The rating and comment are displayed on the unit's listing page for other guests to see. |
| **Before This Can Happen** | The guest's booking must be completed (stay is over). The guest must not have already submitted a review for this booking. The guest must be signed in. |
| **After This Is Done** | The review is saved and immediately visible on the unit's detail page. The unit's average rating is recalculated to include the new review. |

**How It Works:**

1. The guest goes to "My Bookings" → "History" and finds a completed booking.
2. They click "Leave a Review."
3. The system shows a review form with a star rating selector (1 to 5 stars, required) and a text area for an optional comment.
4. The guest selects their rating and optionally writes a comment.
5. They click "Submit."
6. The review is saved and appears on the unit's detail page.
7. The unit's average rating is recalculated.
8. A thank-you message is displayed.
9. The "Leave a Review" button is no longer shown for this booking.

**What If Something Goes Wrong?**

- **No star rating selected:** The system reminds the guest that a rating is required.
- **Review already submitted:** The "Leave a Review" button is replaced with "View Your Review."

---

### UC-FB-02: View Submitted Feedback

| Field | Detail |
|---|---|
| **Use Case ID** | UC-FB-02 |
| **Use Case Name** | View Submitted Feedback |
| **Who Is Involved** | Guest |
| **What It Does** | Allows the guest to see all the reviews they have previously submitted across their completed bookings. Each entry shows the unit name, the dates of stay, the star rating, the comment, the date it was submitted, and any reply from the administrator. Reviews are read-only and cannot be edited after submission. |
| **Before This Can Happen** | The guest must have submitted at least one review. |
| **After This Is Done** | The guest can revisit their past reviews and see if the administrator has responded to any of them. |

**How It Works:**

1. The guest goes to "My Reviews" or views a specific completed booking.
2. The system shows all of the guest's submitted reviews.
3. Each entry displays: unit name, stay dates, star rating, comment text, submission date, and any administrator reply.
4. Reviews are read-only.

---

### UC-FB-03: View / Manage All Feedback (Admin)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-FB-03 |
| **Use Case Name** | View / Manage All Feedback (Admin) |
| **Who Is Involved** | Admin |
| **What It Does** | Gives the administrator a complete view of all guest reviews across all units, including those that have been hidden from public view. From here, the administrator can respond to reviews (to show engagement with guest feedback) and moderate reviews by hiding those that contain inappropriate content. Hidden reviews are removed from the public listing but kept on record. When a review is hidden or restored, the unit's average rating is automatically recalculated. |
| **Before This Can Happen** | The administrator must be signed in with feedback management access. |
| **After This Is Done** | The administrator has a full picture of all guest feedback and can take action on individual reviews. |

**How It Works — View All Feedback:**

1. The administrator goes to the Guest Feedback management section.
2. The system shows all reviews, including hidden ones. Each entry displays: guest name, unit, booking reference, rating, comment, date, visibility status (visible or hidden), and whether the administrator has replied.
3. The administrator can filter by unit or visibility status.

**How It Works — Respond to a Review:**

1. The administrator selects a review and clicks "Reply."
2. They type their response.
3. They click "Publish Reply."
4. The reply is immediately visible alongside the original review on the unit's detail page.
5. The guest can also see the reply when they view their submitted feedback.

**How It Works — Hide / Moderate a Review:**

1. The administrator selects a review with inappropriate content and clicks "Hide."
2. The system asks for confirmation.
3. The administrator confirms.
4. The review is removed from the public listing (but kept on record).
5. The unit's average rating is recalculated without the hidden review.
6. The administrator can restore the review at any time by clicking "Restore Visibility."

---

### UC-FB-04: Display Average Rating

| Field | Detail |
|---|---|
| **Use Case ID** | UC-FB-04 |
| **Use Case Name** | Display Average Rating |
| **Who Is Involved** | System (automatic process) |
| **What It Does** | The system automatically calculates and displays the average star rating for each homestay unit on both the listing page and the unit detail page. Only visible (non-hidden) reviews are included in the calculation. The display format is, for example: "4.2 ★ (18 reviews)." The rating updates automatically whenever a new review is submitted or an existing review is hidden or restored. |
| **Before This Can Happen** | At least one visible review must exist for the unit. |
| **After This Is Done** | Guests and administrators can see the unit's rating at a glance, helping guests make informed booking decisions. |

**How It Works:**

1. Whenever a guest or administrator views the homestay listing page or a unit detail page, the system calculates the average rating for each unit.
2. Only reviews that are currently visible are included in the calculation.
3. The result is displayed as a star rating with the total number of reviews (e.g., "4.2 ★ (18 reviews)").
4. If a unit has no visible reviews, the system displays "No reviews yet" instead.

---

*End of Use Case Descriptions — HomeLodge v3.0*
