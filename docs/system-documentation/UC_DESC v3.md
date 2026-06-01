# Use Case Descriptions

## HomeLodge – Homestay Booking System

| Field | Detail |
|---|---|
| **Document Version** | 3.0 |
| **Status** | Draft |
| **Last Updated** | 2026-06-01 |
| **Based On** | Consolidated Use Case Model (47 use cases, 12 modules) |
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
| **Actor(s)** | Guest |
| **Description** | A new visitor creates a HomeLodge account by providing their name, email address, and a password of their choice. Once registered, the person is given the "Guest" role and can sign in to browse and book homestay units. |
| **Preconditions** | The email address is not already linked to an existing account. The registration page is open to everyone. |
| **Postconditions** | A new account is created with the "Guest" role. The person can now sign in. |
| **Priority** | Must Have |

**Normal Flow:**
1. Guest opens the registration page.
2. Guest enters their full name, email address, password, and retypes the password to confirm it.
3. System checks that the email is in the correct format, is not already taken, the password meets the strength rules (8–12 characters with a mix of upper-case, lower-case, numbers, and symbols), and both password entries match.
4. System securely saves the password and creates the account.
5. System assigns the "Guest" role to the new account.
6. A success message is shown and the guest is taken to the sign-in page.

**Alternative Flows:**
- **A1 – Email already in use:** System highlights the email field and suggests the person either sign in or use "Forgot Password." Flow returns to step 2.
- **A2 – Password does not meet the strength rules:** System shows which rules are not yet met so the person can fix them. Flow returns to step 2.
- **A3 – Password entries do not match:** System highlights the confirmation field. Flow returns to step 2.
- **A4 – Invalid email format:** System highlights the email field and asks for a corrected address. Flow returns to step 2.

**Exception Flows:**
- **E1 – Unexpected system error during account creation:** A general message is shown ("Registration failed. Please try again."). No account is created.

---

### UC-AUTH-02: Login / Login via Google SSO

| Field | Detail |
|---|---|
| **Use Case ID** | UC-AUTH-02 |
| **Use Case Name** | Login / Login via Google SSO |
| **Actor(s)** | Guest, Registered User, Admin |
| **Description** | A registered user signs in to HomeLodge using either their email and password, or their Google account. If a person signs in with Google for the first time and does not already have a HomeLodge account, the system creates one automatically with the "Guest" role. |
| **Preconditions** | The person has either a HomeLodge account or a valid Google account. The account is not locked or deactivated. |
| **Postconditions** | The person is signed in and taken to their dashboard. If a new account was created through Google, it is set up with the "Guest" role. |
| **Priority** | Must Have |

**Normal Flow (Email & Password):**
1. User goes to the sign-in page and enters their email and password.
2. System verifies the credentials are correct.
3. System checks that the account is active and not locked.
4. System signs the user in and records the sign-in time.
5. System checks whether the user is required to change their password (e.g., after an admin reset).
   - **5a – Password change required:** User is redirected to the forced password change page (see UC-AUTH-06). All other pages are blocked until this is completed.
   - **5b – No password change required:** User is taken to their dashboard.

**Normal Flow (Google Sign-In):**
1. User clicks "Continue with Google" on the sign-in or registration page.
2. User is taken to the Google sign-in screen and grants permission.
3. Google sends the person's name and email back to HomeLodge.
4. System checks whether an account with that email already exists.
   - **4a – Account exists:** System links the Google identity (if not already linked) and signs the user in.
   - **4b – No account exists:** System creates a new account with the "Guest" role using the Google profile information.
5. User is signed in and taken to their dashboard.

**Alternative Flows:**
- **A1 – Wrong email or password:** System shows a general error message ("Invalid email or password.") without revealing which part is wrong, for security purposes. Flow returns to step 1.
- **A2 – Account is deactivated:** System shows "Your account has been deactivated. Please contact support."
- **A3 – Account is locked (too many failed attempts):** System shows a lockout message with the estimated time until the account unlocks. The person can also reset their password to unlock the account immediately.
- **A4 – Google sign-in cancelled or denied:** System returns the person to the sign-in page with a message that Google sign-in was not completed.

**Exception Flows:**
- **E1 – System is temporarily unavailable:** An error message is shown and no session is created.
- **E2 – Google sign-in service is unreachable:** System shows an error and returns the person to the sign-in page.

---

### UC-AUTH-03: Logout

| Field | Detail |
|---|---|
| **Use Case ID** | UC-AUTH-03 |
| **Use Case Name** | Logout |
| **Actor(s)** | Registered User (Guest or Admin) |
| **Description** | A signed-in user ends their session and signs out of HomeLodge. After signing out, the user must sign in again to access any protected areas of the system. |
| **Preconditions** | The user is currently signed in. |
| **Postconditions** | The session is ended. The user is taken to the sign-in page and can no longer access protected pages without signing in again. |
| **Priority** | Must Have |

**Normal Flow:**
1. User clicks the "Logout" button in the navigation menu.
2. System ends the current session and clears any "remember me" settings.
3. User is redirected to the sign-in page.
4. If the user tries to go back using the browser's back button, they will see the sign-in page instead of the previously protected content.

**Exception Flows:**
- **E1 – Session had already expired before the user clicked Logout:** System still redirects to the sign-in page without showing an error.

---

### UC-AUTH-04: Forgot Password (Reset via Email)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-AUTH-04 |
| **Use Case Name** | Forgot Password (Reset via Email) |
| **Actor(s)** | Guest, Registered User, Admin |
| **Description** | A person who has forgotten their password requests a reset link via email. The system sends a time-limited link; clicking it allows the person to set a new password. If the account was previously locked due to too many failed sign-in attempts, this process also unlocks it. |
| **Preconditions** | The person has a registered email account. The system's email sending feature is configured and working. |
| **Postconditions** | The person has a new password and can sign in with it. The reset link can no longer be reused. If the account was locked, the lock is removed. |
| **Priority** | Must Have |

**Normal Flow:**
1. User clicks "Forgot Password" on the sign-in page.
2. User enters their registered email address.
3. System checks whether the email exists in the system.
4. System generates a time-limited password reset link (valid for a configurable period; default: 60 minutes).
5. System sends the reset link to the email address.
6. System displays: "If an account exists with this email, a reset link has been sent." (This message is the same regardless of whether the email is found, to protect privacy.)
7. User opens their email and clicks the reset link.
8. System checks that the link is still valid (not expired and not already used).
9. A password reset form is shown.
10. User enters a new password and confirms it, following the strength rules.
11. System securely saves the new password.
12. System marks the reset link as used so it cannot be reused.
13. If the account was locked, the system removes the lock and resets the failed sign-in counter.
14. User is redirected to the sign-in page with a success message.

**Alternative Flows:**
- **A1 – Email not found:** System still shows the same generic message from step 6 to prevent others from discovering whether a particular email is registered.
- **A2 – Reset link expired or already used:** System shows "This reset link is invalid or has expired" and suggests requesting a new one.
- **A3 – New password does not meet the strength rules:** System highlights the failing rules. Flow returns to step 10.

**Exception Flows:**
- **E1 – Email delivery failure:** The reset link is created but may not reach the person. The person can try again.

---

### UC-AUTH-05: View / Update Profile

| Field | Detail |
|---|---|
| **Use Case ID** | UC-AUTH-05 |
| **Use Case Name** | View / Update Profile |
| **Actor(s)** | Registered User (Guest or Admin) |
| **Description** | A signed-in user views and updates their own personal information, including name, phone number, and profile photo. |
| **Preconditions** | User is signed in. |
| **Postconditions** | Updated profile information is saved and reflected across the system. |
| **Priority** | Should Have |

**Normal Flow:**
1. User goes to the Profile page.
2. System displays current profile information (name, email, phone number, profile photo).
3. User changes one or more fields.
4. User uploads a new profile photo (optional).
5. User clicks "Save."
6. System checks that the changes are valid (e.g., phone number format is correct, photo file is an accepted image type and within the size limit).
7. System saves the updated information.
8. A confirmation message is shown: "Profile updated successfully."

**Alternative Flows:**
- **A1 – Invalid phone number format:** System highlights the field and asks for correction.
- **A2 – Uploaded file is not a valid image or exceeds the size limit:** System shows an appropriate error message.

**Exception Flows:**
- **E1 – Photo upload failure:** System rolls back the change and notifies the user.

---

### UC-AUTH-06: Force Change Password

| Field | Detail |
|---|---|
| **Use Case ID** | UC-AUTH-06 |
| **Use Case Name** | Force Change Password |
| **Actor(s)** | Registered User |
| **Description** | When an administrator resets a user's password, the system marks the account so that the user must choose a new personal password the next time they sign in. Until they do so, they cannot access any other part of the system. This ensures the temporary password is replaced with one that only the user knows. |
| **Preconditions** | An administrator has reset the user's password (see UC-USR-03). The user has successfully signed in with the temporary password. |
| **Postconditions** | The user has set a new personal password. The forced password change flag is cleared. The user can now use the system normally. |
| **Priority** | Must Have |

**Normal Flow:**
1. User signs in with the temporary password provided by the administrator.
2. System detects that a password change is required.
3. System redirects the user to the "Change Your Password" page.
4. System blocks access to all other pages until this step is completed.
5. User enters a new password and confirms it, following the password strength rules.
6. User clicks "Save."
7. System securely saves the new password.
8. The forced password change requirement is cleared.
9. User is redirected to their dashboard.

**Alternative Flows:**
- **A1 – New password is the same as the temporary one:** System asks the user to choose a different password.
- **A2 – Password does not meet the strength rules:** System highlights the failing rules. Flow returns to step 5.

**Exception Flows:**
- **E1 – User tries to navigate to another page while the flag is active:** System redirects them back to the password change page.

---

## 2. Homestay Management Module

This module is about the properties themselves — the homestay units that guests can browse and book. Guests can view available units and their details, while administrators can create, edit, and manage the units behind the scenes.

---

### UC-HS-01: Browse Homestay Units

| Field | Detail |
|---|---|
| **Use Case ID** | UC-HS-01 |
| **Use Case Name** | Browse Homestay Units |
| **Actor(s)** | Guest |
| **Description** | A guest views the list of all available homestay units, each displayed with a summary including name, thumbnail photo, base nightly price, location, and average guest rating. |
| **Preconditions** | At least one active homestay unit exists in the system. The guest may or may not be signed in. |
| **Postconditions** | The guest can see all available units and can select one to view its full details. |
| **Priority** | Must Have |

**Normal Flow:**
1. Guest opens the homestay listing page.
2. System retrieves all active units.
3. System displays each unit as a card showing: name, main photo, price per night, location, and average rating from past guest reviews.
4. Guest scrolls through the options and clicks on a unit they are interested in.
5. Guest is taken to the selected unit's detail page (see UC-HS-02).

**Alternative Flows:**
- **A1 – No active units exist:** System shows an informational message: "No homestay units are currently available."

---

### UC-HS-02: View Unit Details & Availability

| Field | Detail |
|---|---|
| **Use Case ID** | UC-HS-02 |
| **Use Case Name** | View Unit Details & Availability |
| **Actor(s)** | Guest |
| **Description** | A guest views the full detail page for a selected homestay unit, including all photos, complete description, pricing, check-in/check-out times, house rules (policies), guest reviews, and a real-time availability calendar showing which dates are free, booked, or blocked. |
| **Preconditions** | The guest has selected a unit from the listing page (UC-HS-01). The unit is active. |
| **Postconditions** | The guest has all the information they need to decide whether to proceed with a booking. |
| **Priority** | Must Have |

**Normal Flow:**
1. Guest clicks on a unit card from the listing page.
2. System loads the unit detail page showing: unit name, all photos in a gallery, full description, location, base price per night, deposit amount, check-in and check-out times, house rules, and average rating with guest reviews.
3. System displays the availability calendar with colour-coded dates:
   - **Available:** The guest can book these dates.
   - **Booked:** Already reserved by another guest.
   - **Temporarily held:** Another guest is in the process of paying for these dates.
   - **Blocked:** The administrator has made these dates unavailable (e.g., for maintenance).
4. Guest reviews the information and can click "Book Now" to start the booking process.

---

### UC-HS-03: Create Homestay Unit

| Field | Detail |
|---|---|
| **Use Case ID** | UC-HS-03 |
| **Use Case Name** | Create Homestay Unit |
| **Actor(s)** | Admin |
| **Description** | An administrator adds a new homestay unit to the system with all required details — name, description, location, pricing, check-in/check-out times, and photos. Upon creation, the system automatically applies the standard default house rules (e.g., No Pets, No Smoking) so the administrator does not need to set them up manually each time. The unit becomes visible to guests immediately. |
| **Preconditions** | Administrator is signed in with permission to create homestay units. |
| **Postconditions** | A new homestay unit record is created and set to "active." Default house rules are automatically attached to the unit. The unit appears on the guest-facing listing page. An audit log entry is recorded. |
| **Priority** | Must Have |

**Normal Flow:**
1. Admin goes to Homestay Management → Create New Unit.
2. Admin fills in: unit name, description, location/address, base price per night, and deposit amount.
3. Admin sets the default check-in and check-out times.
4. Admin uploads one or more photos of the property (at least one is recommended).
5. Admin optionally sets a custom extension payment window (how long a guest has to pay an extension charge before it is automatically cancelled — this overrides the system-wide default for this unit only).
6. Admin clicks "Save."
7. System checks that all required fields are filled in correctly.
8. System saves the unit record.
9. System automatically copies all active default house rules (e.g., No Pets, No Durians, No Smoking) to the new unit's rules list.
10. System records an audit log entry.
11. A success message is shown. The unit is now visible on the guest listing page.

**Alternative Flows:**
- **A1 – Required field missing:** System highlights the missing fields. Flow returns to step 2.
- **A2 – Uploaded file is not a valid image:** System rejects the file and asks for a valid format.

**Exception Flows:**
- **E1 – Photo upload failure:** System saves the unit record but shows a warning that the photo upload failed. Admin can upload photos later by editing the unit.

---

### UC-HS-04: Edit Homestay Unit

| Field | Detail |
|---|---|
| **Use Case ID** | UC-HS-04 |
| **Use Case Name** | Edit Homestay Unit |
| **Actor(s)** | Admin |
| **Description** | An administrator updates the details of an existing homestay unit — such as name, description, pricing, photos, check-in/check-out times, house rules, or extension payment window. All changes take effect immediately across the system, including on the guest-facing listing page. |
| **Preconditions** | The unit exists. Admin is signed in with permission to edit homestay units. |
| **Postconditions** | The unit record is updated. Guest-facing pages reflect the new information. An audit log entry is recorded. |
| **Priority** | Must Have |

**Normal Flow:**
1. Admin goes to Homestay Management → selects the unit → clicks "Edit."
2. System shows a form pre-filled with the unit's current details.
3. Admin changes the desired fields (name, description, pricing, times, photos, house rules, extension window).
4. Admin clicks "Save."
5. System checks the changes are valid.
6. System saves the updated record and records an audit log entry.
7. A success message is shown.

**Alternative Flows:**
- **A1 – Invalid information entered:** System highlights the problem fields and keeps the entered values so nothing is lost.

---

### UC-HS-05: Deactivate / Delete Unit

| Field | Detail |
|---|---|
| **Use Case ID** | UC-HS-05 |
| **Use Case Name** | Deactivate / Delete Unit |
| **Actor(s)** | Admin |
| **Description** | An administrator takes a homestay unit off the market by deactivating or soft-deleting it. Once deactivated, the unit is hidden from the guest listing page and no new bookings can be made for it. Existing confirmed bookings are not affected. All data about the unit is retained in the system. |
| **Preconditions** | Admin is signed in with permission to manage units. The unit has no confirmed future bookings. |
| **Postconditions** | The unit is set to "inactive." It no longer appears on the guest listing page. Existing data is kept. An audit log entry is recorded. |
| **Priority** | Must Have |

**Normal Flow:**
1. Admin opens the unit's management page.
2. Admin clicks "Deactivate" or "Delete."
3. System checks for any upcoming confirmed bookings on this unit.
4. System asks for confirmation.
5. Admin confirms.
6. System marks the unit as inactive and records an audit log entry.
7. A success message is shown. The unit disappears from the guest listing page.

**Alternative Flows:**
- **A1 – Unit has upcoming confirmed bookings:** System blocks the action and shows a warning listing the conflicting bookings. Admin must cancel or reassign those bookings before deactivating.

**Exception Flows:**
- **E1 – Admin cancels the confirmation prompt:** No changes are made.

---

### UC-HS-06: View All Units List

| Field | Detail |
|---|---|
| **Use Case ID** | UC-HS-06 |
| **Use Case Name** | View All Units List |
| **Actor(s)** | Admin |
| **Description** | An administrator views a complete, searchable list of all homestay units in the system — both active and inactive — along with their status, upcoming booking count, and quick-action links. This serves as the main overview screen for property management. |
| **Preconditions** | Admin is signed in with homestay management access. |
| **Postconditions** | Admin has a full operational view of all managed properties. |
| **Priority** | Must Have |

**Normal Flow:**
1. Admin goes to the Homestay Management section.
2. System retrieves all unit records, including inactive ones.
3. System displays a list or table showing each unit's name, status (active / inactive), number of upcoming confirmed bookings, base price, and action buttons (Edit, Deactivate, Manage Policies).
4. Admin can search or filter the list by status.

---

## 3. Booking Module

This module covers the entire booking journey — from a guest choosing their dates and submitting a reservation, all the way to viewing, managing, and cancelling bookings. It also handles the system's automatic cancellation of bookings when payment is not received in time.

---

### UC-BK-01: View Availability & Select Dates

| Field | Detail |
|---|---|
| **Use Case ID** | UC-BK-01 |
| **Use Case Name** | View Availability & Select Dates |
| **Actor(s)** | Guest |
| **Description** | A guest views a colour-coded availability calendar for a specific homestay unit and selects their preferred check-in and check-out dates. The system checks availability in real time upon selection. |
| **Preconditions** | The guest has selected a unit from the listing or unit detail page. |
| **Postconditions** | The selected dates are confirmed as available. The booking summary shows the number of nights and estimated cost. The guest can proceed to submit the booking. |
| **Priority** | Must Have |

**Normal Flow:**
1. Guest is on the unit detail page or booking form.
2. System displays the availability calendar with colour-coded date states (available, booked, temporarily held, and blocked).
3. Guest clicks a check-in date on the calendar.
4. System highlights the selected check-in date.
5. Guest clicks a check-out date.
6. System instantly checks whether all dates in the selected range are available.
7. System confirms availability and updates the booking summary with the total number of nights and estimated cost.
8. Guest optionally adjusts check-in and check-out times if the unit supports it.

**Alternative Flows:**
- **A1 – Selected dates include an unavailable date:** System highlights the conflict and shows "Selected dates are not available." Guest must re-select dates.
- **A2 – Check-out date is before the check-in date:** System shows a validation message and asks for re-selection.
- **A3 – Minimum stay requirement not met:** System shows the minimum stay requirement.

---

### UC-BK-02: Submit Booking

| Field | Detail |
|---|---|
| **Use Case ID** | UC-BK-02 |
| **Use Case Name** | Submit Booking |
| **Actor(s)** | Guest |
| **Description** | A guest submits a booking for a selected unit and date range. The system creates the booking in "awaiting payment" status and gives the guest a one-day window to complete payment. A bill is automatically generated. If payment is not received within the window, the booking is automatically cancelled (see UC-BK-07). |
| **Preconditions** | Guest is signed in. The selected date range has been confirmed as available. Guest is on the booking summary page. |
| **Postconditions** | A booking record is created in "awaiting payment" status. A bill is auto-generated. The selected dates are temporarily reserved. The guest receives a notification with the bill and payment deadline. |
| **Priority** | Must Have |

**Normal Flow:**
1. Guest reviews the booking summary (unit name, dates, times, total cost including deposit).
2. Guest clicks "Confirm Booking."
3. System double-checks availability (to prevent two guests booking the same dates at the same moment).
4. System creates the booking in "awaiting payment" status with a one-day payment deadline.
5. System automatically generates a bill with a unique bill number.
6. System sends an in-app and email notification to the guest with the bill and payment deadline.
7. Guest is directed to the payment page.

**Alternative Flows:**
- **A1 – Dates became unavailable between selection and submission:** System shows a conflict message. Guest must re-select dates.

**Exception Flows:**
- **E1 – System error during booking creation:** No record is created. System shows an error and allows the guest to try again.

---

### UC-BK-03: View Bookings

| Field | Detail |
|---|---|
| **Use Case ID** | UC-BK-03 |
| **Use Case Name** | View Bookings |
| **Actor(s)** | Guest, Admin |
| **Description** | A guest sees all their reservations — both current (active and upcoming) and past (completed and cancelled) — in one place. Administrators can view all bookings system-wide. Bookings can be filtered by status, date range, or other criteria. |
| **Preconditions** | User is signed in. At least one booking exists. |
| **Postconditions** | The user can see a clear overview of all relevant bookings and can navigate to individual booking details. |
| **Priority** | Must Have |

**Normal Flow (Guest):**
1. Guest goes to "My Bookings."
2. System retrieves all bookings belonging to the guest.
3. System displays bookings in sections or tabs — "Current" (active and upcoming) and "History" (completed and cancelled).
4. Each booking is shown as a card or list item with: unit name, check-in/check-out dates, status, and total cost.
5. Guest clicks a booking to see its full details (see UC-BK-04).
6. For completed bookings, "View Receipt" and "Leave a Review" (if not yet submitted) links are available.

**Normal Flow (Admin):**
1. Admin goes to the Booking Management section.
2. System retrieves all bookings across all units.
3. System displays the bookings with filtering options (by status, date range, unit, or booking reference).
4. Admin can use the booking calendar view to see all reservations across all properties at a glance.
5. Admin clicks a booking to view its full details or take action.

---

### UC-BK-04: View Booking Details

| Field | Detail |
|---|---|
| **Use Case ID** | UC-BK-04 |
| **Use Case Name** | View Booking Details |
| **Actor(s)** | Guest, Admin |
| **Description** | A user views the complete details of a specific booking, including unit information, dates and times, booking status, payment status, cancellation policy, QR access code (if confirmed), and any extension history. |
| **Preconditions** | The booking belongs to the signed-in guest, or the viewer is an administrator. |
| **Postconditions** | The user has full visibility of the reservation state and can take available actions (download bill, cancel booking, view receipt). |
| **Priority** | Must Have |

**Normal Flow:**
1. User clicks on a booking from their bookings list.
2. System retrieves the full booking record and all related information (unit details, billing, QR code, extension records if any).
3. System displays: unit name and photo, check-in/check-out date and time, total amount, payment status, booking status, cancellation policy and estimated refund, and QR code (if the booking is confirmed).
4. User can download the bill or receipt from this page.

---

### UC-BK-05: Cancel Booking

| Field | Detail |
|---|---|
| **Use Case ID** | UC-BK-05 |
| **Use Case Name** | Cancel Booking |
| **Actor(s)** | Guest, Admin |
| **Description** | A guest or administrator cancels a booking. If a payment was already made, the system calculates the refund amount based on the cancellation policy tiers (configured in system settings), which determine how much is refunded depending on how far in advance the cancellation is made. |
| **Preconditions** | The booking is in an active state ("awaiting payment" or "confirmed"). The booking has not already been checked in. |
| **Postconditions** | Booking status is set to "cancelled." Reserved dates are released and become available for other guests. Refund (if applicable) is initiated. Both the guest and the administrator are notified. An audit log entry is recorded. |
| **Priority** | Must Have |

**Normal Flow:**
1. User opens the booking detail page.
2. User clicks "Cancel Booking."
3. System calculates the applicable refund amount based on the configured cancellation policy tiers (e.g., more than 14 days before check-in: 100% refund; 7–14 days: 50% refund; less than 7 days: 0% refund).
4. System displays a confirmation dialog: "You will receive a refund of [amount]. This action cannot be undone."
5. User clicks "Confirm Cancellation."
6. System sets the booking status to "cancelled" and releases the dates.
7. System records the refund amount and processes the refund through the payment service (if applicable).
8. System sends cancellation notifications (in-app and email) to the guest and admin.
9. System records an audit log entry.

**Alternative Flows:**
- **A1 – User cancels the confirmation prompt:** No changes are made.
- **A2 – Booking is in "awaiting payment" status:** No payment was made, so there is nothing to refund. The booking is simply cancelled.

**Exception Flows:**
- **E1 – Refund processing failure at the payment service:** System records the cancellation but flags the refund for the administrator to process manually.

---

### UC-BK-06: Manage Booking (Admin)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-BK-06 |
| **Use Case Name** | Manage Booking (Admin) |
| **Actor(s)** | Admin |
| **Description** | The administrator has full control over bookings. This includes creating bookings on behalf of guests (e.g., for walk-in or phone reservations), editing existing booking details, deleting erroneous bookings, viewing the all-unit booking calendar, filtering the booking list, and blocking specific dates on a unit to prevent new bookings (e.g., for maintenance or personal use). |
| **Preconditions** | Admin is signed in with the appropriate booking management permissions. |
| **Postconditions** | Booking records are updated as needed. Guests are notified of any changes affecting them. Audit log entries are recorded for all actions. |
| **Priority** | Must Have |

**Normal Flow – Create Booking on Behalf of Guest:**
1. Admin goes to Bookings → Create Booking.
2. Admin selects the target guest account.
3. Admin selects the homestay unit and desired check-in/check-out dates and times.
4. System checks availability in real time and shows the booking summary with total cost.
5. Admin confirms the booking.
6. System creates the booking in "awaiting payment" status, generates a bill, and notifies the guest to pay.

**Normal Flow – Edit Booking:**
1. Admin opens the booking detail and clicks "Edit."
2. Admin changes the desired fields.
3. If dates are changed: system checks availability for the new dates.
4. Admin submits the changes.
5. System saves the updated record and records an audit log entry.
6. System notifies the guest of the change.

**Normal Flow – Delete Booking:**
1. Admin selects a booking and clicks "Delete."
2. System shows a confirmation warning that this action is permanent and cannot be undone.
3. Admin confirms.
4. System deletes the booking record, releases the dates, notifies the guest, and records an audit log entry.

**Normal Flow – Block Dates:**
1. Admin selects a unit and a date range on the booking calendar or unit management page.
2. Admin enters an internal note/reason (optional — not shown to guests).
3. Admin clicks "Block Dates."
4. The blocked dates immediately appear as "unavailable" on the guest-facing availability calendar. The internal reason is stored but hidden from guests.

**Alternative Flows:**
- **A1 – Date range unavailable (create/edit):** System displays the conflict and asks admin to select different dates.
- **A2 – Admin cancels confirmation (delete):** No changes are made.
- **A3 – Blocked dates have confirmed bookings:** System warns admin of the conflict. Admin must cancel existing bookings before blocking.

---

### UC-BK-07: Auto-Cancel Expired Booking

| Field | Detail |
|---|---|
| **Use Case ID** | UC-BK-07 |
| **Use Case Name** | Auto-Cancel Expired Booking |
| **Actor(s)** | System |
| **Description** | The system runs a scheduled check to find all bookings that are still in "awaiting payment" status after the one-day payment deadline has passed. These bookings are automatically cancelled, the temporarily reserved dates are released, and the guest is notified. |
| **Preconditions** | A booking is in "awaiting payment" status and the payment deadline has passed without payment being received. |
| **Postconditions** | Booking status is set to "cancelled." Dates are released and become available for new bookings. The guest receives a cancellation notification. An audit log entry is recorded. |
| **Priority** | Must Have |

**Normal Flow:**
1. The scheduled job runs at regular intervals (approximately once per hour).
2. System finds all bookings where the status is "awaiting payment" and the payment deadline has passed.
3. For each such booking: system sets the status to "cancelled."
4. System releases the temporarily reserved dates.
5. System sends a cancellation notification (in-app and email) to the guest.
6. System records an audit log entry.

---

## 4. Payment Module

This module handles all financial transactions — from making a payment for a booking to viewing bills and receipts. It connects with the online payment service to process payments securely.

---

### UC-PAY-01: Make Payment

| Field | Detail |
|---|---|
| **Use Case ID** | UC-PAY-01 |
| **Use Case Name** | Make Payment |
| **Actor(s)** | Guest, Payment Gateway |
| **Description** | A guest pays the outstanding bill for their booking through the secure online payment service. Once payment is successfully confirmed, the booking status changes to "confirmed," a QR access code is generated and delivered to the guest, and a payment receipt is created. |
| **Preconditions** | Booking status is "awaiting payment." A bill exists for the booking. The payment deadline has not passed. The payment service is configured. |
| **Postconditions** | Payment is recorded as successful. Booking status is updated to "confirmed." A QR access code is generated and delivered. A receipt is generated. The guest and admin are notified. |
| **Priority** | Must Have |

**Normal Flow:**
1. Guest clicks "Pay Now" from the booking detail page or from the bill notification.
2. System creates a payment request with the payment service and redirects the guest to the secure payment page.
3. Guest completes the payment on the payment service's page.
4. The payment service processes the payment and sends a confirmation back to HomeLodge.
5. System verifies the confirmation is genuine.
6. System records the payment as successful.
7. System updates the booking status to "confirmed."
8. System generates a QR access code for the guest (see UC-QR-01).
9. System generates a payment receipt.
10. System sends a booking confirmation notification (in-app and email) to the guest with the receipt and QR code.
11. System notifies the administrator of the new confirmed booking.

**Alternative Flows:**
- **A1 – Payment declined or failed:** The payment service notifies the system; the payment is recorded as failed. Guest is returned to HomeLodge with an error message and a "Try Again" option.
- **A2 – Guest leaves the payment page without completing:** No confirmation is received. The booking remains in "awaiting payment" status until the deadline passes.

**Exception Flows:**
- **E1 – Duplicate confirmation received:** System detects the duplication and ignores it without re-processing.
- **E2 – Confirmation cannot be verified as genuine:** System rejects the confirmation and records a security alert.

---

### UC-PAY-02: View Payment & Billing Records

| Field | Detail |
|---|---|
| **Use Case ID** | UC-PAY-02 |
| **Use Case Name** | View Payment & Billing Records |
| **Actor(s)** | Guest, Admin |
| **Description** | Users can view their financial records. Guests see their own bills and payment history with details such as bill number, booking reference, amount, status, and dates, and can download bills and receipts. Administrators see all billing and payment records system-wide and can filter them by various criteria. |
| **Preconditions** | User is signed in. At least one billing or payment record exists. |
| **Postconditions** | The user has a clear view of all relevant financial records. |
| **Priority** | Must Have |

**Normal Flow (Guest):**
1. Guest goes to their booking detail or "Payment History" section.
2. System retrieves all payment records for the signed-in guest.
3. System displays each payment: payment number, booking reference, date, amount, and status.
4. Guest can view or download the itemised bill (showing cost breakdown: nightly rate, number of nights, deposit, total amount, and payment deadline).
5. For completed payments, guest can view or download the receipt.

**Normal Flow (Admin):**
1. Admin goes to Payment Management.
2. System displays the billing list (all bills) and the payment list (all payment transactions).
3. Each entry shows: reference number, guest name, unit, amount, status, and date.
4. Admin can filter by date range, reference number, or status.
5. Admin clicks an entry to view details or take action.

---

### UC-PAY-03: Regenerate Bill / Receipt

| Field | Detail |
|---|---|
| **Use Case ID** | UC-PAY-03 |
| **Use Case Name** | Regenerate Bill / Receipt |
| **Actor(s)** | Admin |
| **Description** | An administrator creates a fresh copy of a bill or receipt document on demand. This is useful when the original document has been lost, or when booking details have been updated and a new document is needed to reflect the latest information. |
| **Preconditions** | The corresponding booking and billing/payment records exist. Admin has the appropriate permission. |
| **Postconditions** | A new PDF document is generated and made available for download. Optionally, it can be resent to the guest via email. |
| **Priority** | Should Have |

**Normal Flow:**
1. Admin goes to the billing or payment detail page.
2. Admin clicks "Regenerate Bill" or "Regenerate Receipt."
3. System creates a fresh document using the latest booking and payment data.
4. System makes the new document available for download and optionally resends it to the guest via email.

---

## 5. Notification Module

This module ensures that everyone stays informed about important events — from booking confirmations to payment reminders and check-in alerts. Notifications are delivered both within the app (via a notification bell) and by email.

---

### UC-NOTIF-01: Receive System Notification

| Field | Detail |
|---|---|
| **Use Case ID** | UC-NOTIF-01 |
| **Use Case Name** | Receive System Notification |
| **Actor(s)** | Registered User (Guest or Admin), System |
| **Description** | Users receive timely notifications whenever something important happens — both within the app (via a bell icon in the navigation bar with an unread count) and by email. Notifications cover all key system events: booking confirmed, payment received, booking cancelled, extension charge issued, upcoming check-in/check-out reminder, payment deadline approaching, and QR code reminders. Email notifications can be turned on or off globally by the administrator. |
| **Preconditions** | User has an account. A relevant system event has occurred. For email notifications: the email feature must be configured and enabled. |
| **Postconditions** | A notification record is created. The user's bell icon shows the new notification count. The notification is listed in the notifications panel. An email is also sent (if email notifications are enabled). |
| **Priority** | Must Have |

**Normal Flow (In-App):**
1. A triggering event occurs (e.g., booking confirmed, payment received, cancellation processed).
2. System creates a notification for the relevant user.
3. If the user is currently online: the bell icon badge updates in real time without needing to refresh the page.
4. User clicks the bell icon to open the notifications panel.
5. System shows the list of notifications (read and unread) with the newest first.
6. Clicking a notification marks it as read and takes the user to the relevant page (e.g., booking detail).

**Normal Flow (Email):**
1. A triggering event occurs.
2. System checks whether email notifications are enabled (see UC-SET-01).
3. If enabled: system sends an email to the user's registered email address.
4. The email arrives in the user's inbox.

**Normal Flow (Automated Reminders):**
1. The system runs scheduled daily checks for upcoming events.
2. For payment reminders: system finds bookings in "awaiting payment" status where the payment deadline is approaching. System sends reminders to the guest with the booking reference, amount due, deadline, and a direct payment link.
3. For check-in/check-out reminders: system finds confirmed bookings with check-in or check-out dates within the reminder window (e.g., 1 day before). System sends reminders to both the guest and the administrator.

**Alternative Flows:**
- **A1 – Email notifications globally disabled:** System skips email sending; in-app notifications are still delivered.

**Exception Flows:**
- **E1 – Email sending fails (mail server unreachable):** System retries. After maximum retries, the failure is recorded. In-app notifications are unaffected.

---

### UC-NOTIF-02: Google Calendar Integration

| Field | Detail |
|---|---|
| **Use Case ID** | UC-NOTIF-02 |
| **Use Case Name** | Google Calendar Integration |
| **Actor(s)** | Registered User (Guest or Admin), Google Calendar |
| **Description** | Confirmed bookings are automatically added to the user's connected Google Calendar as events spanning from check-in to check-out. Users who have connected their Google account can see their homestay reservations alongside their other calendar events. |
| **Preconditions** | The user has connected their Google account to HomeLodge. A booking has been confirmed. |
| **Postconditions** | A Google Calendar event is created or updated for the booking, showing the stay details. |
| **Priority** | Should Have |

**Normal Flow:**
1. System detects a booking has been confirmed.
2. System retrieves the user's stored Google account connection.
3. System creates a calendar event: title (unit name + "Stay"), start (check-in date/time), end (check-out date/time), description (booking reference, unit address).
4. If the event already exists (e.g., from a previous update): system updates it.
5. The event appears in the user's Google Calendar.

**Alternative Flows:**
- **A1 – User has not connected Google Calendar:** System skips the calendar step. No error is shown to the user.

**Exception Flows:**
- **E1 – Google Calendar service error or connection expired:** System records the failure. The user may need to reconnect their Google account.

---

## 6. Chat Module

This module provides a built-in messaging system so that guests and administrators can communicate directly within HomeLodge. Messages are delivered instantly and all conversations are saved for future reference.

---

### UC-CHAT-01: Send / Receive Messages

| Field | Detail |
|---|---|
| **Use Case ID** | UC-CHAT-01 |
| **Use Case Name** | Send / Receive Messages |
| **Actor(s)** | Guest, Admin |
| **Description** | A user sends a text message to the other party (Guest ↔ Admin) via the built-in real-time chat system. Messages are saved in the system and delivered instantly. If the recipient is not currently online, the message is stored and they will see it the next time they sign in. |
| **Preconditions** | Both parties have accounts. The sender is signed in and on the chat page. |
| **Postconditions** | The message is saved. The message is delivered to the recipient instantly if they are online. An unread count badge updates on the chat icon. |
| **Priority** | Should Have |

**Normal Flow:**
1. User opens the Chat page.
2. User types a message in the text input field.
3. User clicks "Send" or presses Enter.
4. System saves the message with the sender's identity, recipient's identity, message content, and timestamp.
5. System delivers the message instantly to the recipient's chat window (if they are online).
6. The message appears in the sender's chat window as "sent."

**Alternative Flows:**
- **A1 – Empty message submitted:** System does not allow sending an empty message. The send button is disabled when the input is blank.
- **A2 – Recipient is not currently online:** The message is saved in the system. The recipient sees it upon their next sign-in (see UC-CHAT-02).

**Exception Flows:**
- **E1 – Real-time connection lost:** System shows a "Reconnecting..." indicator. The message is still saved and will be visible on next page load.

---

### UC-CHAT-02: View Chat History

| Field | Detail |
|---|---|
| **Use Case ID** | UC-CHAT-02 |
| **Use Case Name** | View Chat History |
| **Actor(s)** | Guest, Admin |
| **Description** | A user views the full saved conversation between the guest and the administrator. Messages are displayed in the order they were sent, with sender names and timestamps. Unread messages are marked as read when viewed. |
| **Preconditions** | At least one message exists in the conversation. |
| **Postconditions** | All messages are displayed. Unread messages are marked as read. |
| **Priority** | Should Have |

**Normal Flow:**
1. User opens the Chat page.
2. System retrieves all messages in the conversation, ordered from oldest to newest.
3. System displays each message with: sender name, message content, and timestamp.
4. Messages sent by the current user are shown on the right side; received messages on the left side.
5. System marks all unread messages as read.
6. The chat automatically scrolls to the most recent message.

---

## 7. User & Access Management Module

This module is for administrators only. It handles the management of user accounts, roles, and permissions — controlling who can do what within the system.

---

### UC-USR-01: Create User Account

| Field | Detail |
|---|---|
| **Use Case ID** | UC-USR-01 |
| **Use Case Name** | Create User Account |
| **Actor(s)** | Admin |
| **Description** | An administrator manually creates a new user account and assigns it a role. The system sets a temporary password, and the new user will be required to change it on their first sign-in. The new user is notified by email with their login details. |
| **Preconditions** | The email address is not already registered. Admin has the appropriate permission. |
| **Postconditions** | A new account is created with the assigned role. The account is flagged so the user must change the temporary password on first sign-in. The user is notified with login instructions. An audit log entry is recorded. |
| **Priority** | Must Have |

**Normal Flow:**
1. Admin goes to User Management → Create User.
2. Admin enters: full name, email address, and assigns a role.
3. System generates a temporary password for the account.
4. System creates the user record and flags it to require a password change on first sign-in.
5. System sends the user an email with the temporary password and a link to sign in.
6. System records an audit log entry.
7. Admin sees a success message.

**Alternative Flows:**
- **A1 – Email already registered:** System shows a validation error. Admin can edit the existing account instead.

---

### UC-USR-02: Edit / Activate / Deactivate User

| Field | Detail |
|---|---|
| **Use Case ID** | UC-USR-02 |
| **Use Case Name** | Edit / Activate / Deactivate User |
| **Actor(s)** | Admin |
| **Description** | An administrator updates a user's details (name, email, phone, or role assignment) and can toggle the account between active and inactive states. Deactivating prevents the user from signing in and immediately ends any active sessions. Re-activating restores full access. |
| **Preconditions** | The user account exists. Admin has the appropriate permission. |
| **Postconditions** | The account is updated. If the role was changed, new permissions take effect immediately. If deactivated, the user's active sessions are ended. An audit log entry is recorded. |
| **Priority** | Must Have |

**Normal Flow (Edit):**
1. Admin goes to User Management → selects user → clicks "Edit."
2. System shows the edit form pre-filled with the user's current information.
3. Admin updates the desired fields.
4. Admin clicks "Save."
5. System validates the changes (e.g., email is unique) and saves the updated record.
6. System records an audit log entry.
7. A success message is shown.

**Normal Flow (Activate / Deactivate):**
1. Admin goes to User Management → selects user → clicks "Deactivate" or "Activate."
2. System asks for confirmation.
3. Admin confirms.
4. If deactivating: system disables the account and immediately ends any active sessions for that user.
5. If activating: system restores the account so the user can sign in again.
6. System records an audit log entry.
7. A success message is shown.

---

### UC-USR-03: Reset User Password

| Field | Detail |
|---|---|
| **Use Case ID** | UC-USR-03 |
| **Use Case Name** | Reset User Password |
| **Actor(s)** | Admin |
| **Description** | An administrator resets a user's password — either by sending a password reset link to the user's email or by resetting it to the system default temporary password. The user will be required to set a new personal password on their next sign-in. If the account was locked due to too many failed attempts, this also unlocks it. |
| **Preconditions** | The user account exists. Admin has the appropriate permission. |
| **Postconditions** | The user's password is reset. The account is flagged to require a password change on next sign-in. If locked, the account is unlocked and the failed attempts counter is reset. The user is notified. An audit log entry is recorded. |
| **Priority** | Must Have |

**Normal Flow:**
1. Admin goes to User Management → selects user → clicks "Reset Password."
2. Admin chooses the reset method:
   - **Option A – Send reset link:** System sends a password reset email to the user's registered address.
   - **Option B – Set to default:** System immediately sets the password to the default temporary password.
3. System flags the account to require a password change on next sign-in.
4. If the account was locked, system removes the lock and resets the failed sign-in counter.
5. System notifies the user (in-app and email).
6. System records an audit log entry.

---

### UC-USR-04: Manage Roles

| Field | Detail |
|---|---|
| **Use Case ID** | UC-USR-04 |
| **Use Case Name** | Manage Roles |
| **Actor(s)** | Admin |
| **Description** | An administrator creates, edits, and deletes roles. Roles are categories that group permissions together — for example, an "Admin" role might have permission to manage bookings, while a "Guest" role only allows making bookings. The administrator can also assign specific permissions to each role. Changes take effect immediately for all users with that role. |
| **Preconditions** | Admin has role management access. |
| **Postconditions** | Roles and their assigned permissions are updated. All users with a modified role immediately gain or lose the affected capabilities. An audit log entry is recorded. |
| **Priority** | Must Have |

**Normal Flow (Create Role):**
1. Admin goes to Role Management → Create Role.
2. Admin enters the role name and an optional description.
3. System checks that the name is unique.
4. System creates the role.
5. Admin is taken to the role detail page to assign permissions.

**Normal Flow (Edit Role):**
1. Admin selects a role and clicks "Edit."
2. Admin modifies the name or description.
3. System checks that the new name is unique.
4. System saves the updated role.

**Normal Flow (Assign Permissions to Role):**
1. Admin goes to Role Management → selects role → "Manage Permissions."
2. System shows a checklist of all available permissions, with currently assigned ones ticked.
3. Admin ticks or unticks permissions as required.
4. Admin saves the changes.
5. System updates the role's permissions. All users with this role are immediately affected.
6. System records an audit log entry.

**Normal Flow (Delete Role):**
1. Admin selects a role and clicks "Delete."
2. System checks if any users currently have this role.
3. If no users: system asks for confirmation and deletes the role.
4. System records an audit log entry.

**Alternative Flows:**
- **A1 – Role is assigned to users (Delete):** System blocks the deletion and shows: "This role is assigned to [N] user(s). Reassign users before deleting." Admin must remove the role from all affected users first.

---

### UC-USR-05: Manage Permissions

| Field | Detail |
|---|---|
| **Use Case ID** | UC-USR-05 |
| **Use Case Name** | Manage Permissions |
| **Actor(s)** | Admin |
| **Description** | An administrator creates, edits, and deletes individual permissions. Permissions are the building blocks assigned to roles — they define specific capabilities such as "can create bookings," "can manage users," or "can view reports." |
| **Preconditions** | Admin has permission management access. |
| **Postconditions** | Permissions are updated. They can be assigned to roles. An audit log entry is recorded. |
| **Priority** | Must Have |

**Normal Flow (Create Permission):**
1. Admin goes to Permission Management → Create Permission.
2. Admin enters the permission name and an optional description.
3. System checks that the name is unique.
4. System creates the permission.

**Normal Flow (Edit Permission):**
1. Admin selects a permission and clicks "Edit."
2. Admin modifies the name or description.
3. System saves the update.

**Normal Flow (Delete Permission):**
1. Admin selects a permission and clicks "Delete."
2. System checks if the permission is currently attached to any roles.
3. If not attached: system asks for confirmation and deletes it.
4. System records an audit log entry.

**Alternative Flows:**
- **A1 – Permission is attached to roles (Delete):** System blocks the deletion and shows: "This permission is attached to [N] role(s). Remove from roles before deleting."

---

## 8. System Settings Module

This module gives the administrator control over the system's global configuration — everything from email settings and security rules to cancellation policies, payment options, and default house rules. Changes made here affect the entire system.

---

### UC-SET-01: Configure System Settings

| Field | Detail |
|---|---|
| **Use Case ID** | UC-SET-01 |
| **Use Case Name** | Configure System Settings |
| **Actor(s)** | Admin |
| **Description** | The administrator configures all system-wide options from a centralised settings page. This includes outgoing email settings, security parameters (sign-in attempt limits, lockout duration, session timeout), cancellation and refund policy tiers, payment and billing options, extension charge rates, the default extension payment window, and the global email notification toggle. |
| **Preconditions** | Admin is signed in with system settings access. |
| **Postconditions** | All updated settings are saved and take effect immediately across the system. |
| **Priority** | Must Have |

**Normal Flow:**
1. Admin goes to System Settings.
2. Admin navigates to the relevant section and updates the values:
   - **Email (Outgoing Mail):** Mail server address, port, username, password, encryption type, and sender name/address. Admin can optionally test the connection before saving.
   - **Security:** Maximum failed sign-in attempts before lockout (default: 5), lockout duration in minutes (default: 30), and session timeout in minutes (default: 120).
   - **Cancellation & Refund Policy:** Tiered refund rules — each tier specifies a days-before-check-in threshold and the corresponding refund percentage (e.g., >14 days: 100%; 7–14 days: 50%; <7 days: 0%).
   - **Payment & Billing:** Payment service credentials, bill number format/prefix, and the initial booking payment window (default: 1 day).
   - **Extension Charges:** Extra charge rate per hour (for same-day time extensions) and per night (for overnight date extensions).
   - **Extension Payment Window:** The default time in minutes a guest has to pay an extension charge before it is automatically cancelled (default: 60 minutes). Individual units can override this.
   - **Email Notifications Toggle:** A switch to globally enable or disable email notifications. When disabled, all emails are suppressed; in-app notifications continue normally.
3. Admin clicks "Save."
4. System validates the settings (e.g., values are positive numbers, refund tiers do not overlap, percentages are between 0 and 100).
5. System saves the settings. A confirmation message is shown.
6. All future operations use the new settings.

**Alternative Flows:**
- **A1 – Email connection test fails:** System shows the error message. Admin can correct the settings and retry.

---

### UC-SET-02: Manage Default Homestay Policies

| Field | Detail |
|---|---|
| **Use Case ID** | UC-SET-02 |
| **Use Case Name** | Manage Default Homestay Policies |
| **Actor(s)** | Admin |
| **Description** | The administrator manages the set of default house rules that are automatically applied to every new homestay unit when it is created. The system comes pre-loaded with standard defaults: No Pets, No Durians, No Smoking. The administrator can add new default rules, edit existing ones, or remove rules. Changes only affect units created after the update — existing units keep their current rules. |
| **Preconditions** | Admin is signed in with system settings access. |
| **Postconditions** | The default policy list is updated. New homestay units created after this change will inherit the updated defaults. Existing units are not retroactively changed. |
| **Priority** | Must Have |

**Normal Flow:**
1. Admin goes to System Settings → Default Policies.
2. System shows the current list of default house rules (e.g., No Pets, No Durians, No Smoking).
3. Admin can:
   - **Add:** Enter a new default rule description and save.
   - **Edit:** Change the text of an existing default rule and save.
   - **Remove:** Click delete on a rule; system asks for confirmation before removing.
4. System saves the changes. Any new homestay units created in the future will start with the updated set of default rules.

---

## 9. Audit Logs Module

This module keeps a permanent, tamper-proof record of everything that happens in the system. This provides accountability and transparency, and helps the administrator track who did what and when.

---

### UC-AUDIT-01: View / Filter Audit Trail

| Field | Detail |
|---|---|
| **Use Case ID** | UC-AUDIT-01 |
| **Use Case Name** | View / Filter Audit Trail |
| **Actor(s)** | Admin |
| **Description** | The administrator views a complete, chronological record of all significant actions in the system — performed by users, administrators, and the system itself. Log entries include events such as sign-ins, bookings created or cancelled, payments received, settings changed, accounts modified, and automatic actions (e.g., auto-cancellation of expired bookings). The administrator can filter the log by date range, event type, or specific user. Log entries are read-only and cannot be edited or deleted. |
| **Preconditions** | Admin is signed in with audit log access. |
| **Postconditions** | Admin can view all audit entries. No modifications to log entries are possible. |
| **Priority** | Must Have |

**Normal Flow:**
1. Admin goes to the Audit Logs section.
2. System retrieves all log entries in reverse chronological order (newest first).
3. System displays each entry showing: timestamp, who performed the action (user name or "System" for automatic actions), what happened (event type), what was affected (e.g., specific booking, user, or setting).
4. Admin can page through the entries.

**Normal Flow (Filtering):**
1. Admin selects one or more filter criteria (date range, event type, or user name).
2. System applies the filters and re-renders the log with matching results only.
3. Admin can clear filters to return to the full log.

---

### UC-AUDIT-02: Automatic Event Logging

| Field | Detail |
|---|---|
| **Use Case ID** | UC-AUDIT-02 |
| **Use Case Name** | Automatic Event Logging |
| **Actor(s)** | System |
| **Description** | The system automatically records a log entry every time a significant action occurs — whether performed by a user, an administrator, or the system itself. This includes all user actions (sign-in, sign-out, booking, payment, feedback), all administrator actions (managing users, changing settings, editing bookings), and all automatic system actions (auto-cancelling expired bookings, expiring QR codes, processing payments). Log entries are permanent and cannot be modified or deleted by anyone. |
| **Preconditions** | A significant event occurs somewhere in the system. |
| **Postconditions** | A permanent, unchangeable log entry is created. It can be viewed by administrators through the Audit Trail (UC-AUDIT-01) but cannot be modified or deleted. |
| **Priority** | Must Have |

**Normal Flow (User Actions):**
1. A user performs a significant action (e.g., submits a booking, makes a payment, leaves a review).
2. System automatically creates a log entry recording: the time, the person, the type of event, and what was affected.
3. The entry is permanently saved.

**Normal Flow (System Actions):**
1. An automated process triggers (e.g., a scheduled job auto-cancels an overdue booking, or a QR code is automatically expired).
2. System creates a log entry attributed to "System" (since no person initiated it) with the appropriate event description.
3. The entry is permanently saved.

---

## 10. QR Code & Access Module

This module manages the digital QR codes that allow guests to access their booked homestay units. QR codes are automatically generated when a booking is confirmed and automatically expire at check-out. The module also handles housekeeping access between guests and booking extensions (when a guest wants to stay longer).

---

### UC-QR-01: Receive & Use QR Code

| Field | Detail |
|---|---|
| **Use Case ID** | UC-QR-01 |
| **Use Case Name** | Receive & Use QR Code |
| **Actor(s)** | Guest |
| **Description** | After a booking is confirmed (payment received), the system automatically generates a unique, time-limited QR access code for the guest. The code is valid only during the booked stay — from check-in time to check-out time. The guest receives it via in-app notification and email, and can use it to unlock the homestay door by presenting it to the smart lock scanner. |
| **Preconditions** | Booking status has changed to "confirmed" (payment successfully received). |
| **Postconditions** | A QR code record is created with "active" status, valid from check-in time until check-out time. Guest receives the QR code via in-app and email notifications. The QR code is accessible from the booking detail page. |
| **Priority** | Must Have |

**Normal Flow:**
1. System confirms payment (see UC-PAY-01).
2. System generates a unique QR code linked to the booking, valid from the check-in date/time until the check-out date/time.
3. System sends an in-app notification to the guest with the QR code.
4. System sends an email to the guest containing the QR code and booking details.
5. Guest can view the QR code at any time on their booking detail page.
6. To enter the property, guest holds the QR code up to the smart lock scanner.
7. The lock verifies that the code is active and the current time is within the valid window.
8. If valid: access is granted and the door opens.

**Alternative Flows:**
- **A1 – QR code is expired (past check-out time):** Access is denied. The lock displays "QR Code Expired."
- **A2 – QR code is inactive or has been revoked:** Access is denied.
- **A3 – Guest's device is unavailable (dead battery, no signal):** Guest must contact the administrator for manual assistance.

---

### UC-QR-02: Manage Housekeeping Cycle

| Field | Detail |
|---|---|
| **Use Case ID** | UC-QR-02 |
| **Use Case Name** | Manage Housekeeping Cycle |
| **Actor(s)** | Admin, System |
| **Description** | Handles the transition between guests. After a guest's check-out time passes, the system automatically expires their QR code. The administrator can then generate a temporary housekeeping QR code for cleaning staff. Once housekeeping is marked as complete, the system automatically generates a new QR code for the next guest (if there is an upcoming confirmed booking). |
| **Preconditions** | A guest has checked out and their QR code has been expired by the system. |
| **Postconditions** | The previous guest's code is expired. Housekeeping access is managed. The next guest's QR code is generated and delivered (if applicable). The booking is marked as completed. |
| **Priority** | Must Have |

**Normal Flow (Automatic QR Expiry):**
1. A scheduled job runs at regular intervals (around the time of check-outs).
2. System finds all active QR codes whose valid-until time has passed.
3. For each: system sets the QR code to "expired."
4. System updates the corresponding booking status to "completed."

**Normal Flow (Housekeeping Access):**
1. Admin goes to the booking or unit management and clicks "Generate Housekeeping QR."
2. Admin sets the validity window (e.g., valid for 4 hours).
3. System generates a temporary QR code marked as "housekeeping" type with the configured validity period.
4. System displays the code for admin to share with the cleaning staff.

**Normal Flow (Mark Housekeeping Complete):**
1. Admin clicks "Mark Housekeeping Complete" for the unit.
2. System sets the housekeeping QR code to "expired."
3. System checks for the next confirmed booking on this unit.
4. If a next booking exists: system automatically generates a new QR code for the next guest and sends it via in-app and email notifications.

**Alternative Flows:**
- **A1 – No next confirmed booking exists:** System skips QR code generation. No action needed from admin.

---

### UC-QR-03: Initiate Booking Extension

| Field | Detail |
|---|---|
| **Use Case ID** | UC-QR-03 |
| **Use Case Name** | Initiate Booking Extension |
| **Actor(s)** | Admin |
| **Description** | When a guest requests to stay longer, the administrator initiates a booking extension. The system checks whether the extended dates are available, calculates the additional charge based on configured rates, creates an extension record, generates a bill, and notifies the guest with a payment deadline. The guest's QR code is NOT extended at this point — it is only updated after payment is received. |
| **Preconditions** | Booking status is "confirmed." Admin has the appropriate permission. No conflicting bookings exist for the extended period. |
| **Postconditions** | An extension record is created in "awaiting payment" status. An extension bill is generated. The guest is notified with the charge amount and payment deadline. The QR code validity is NOT yet updated. An audit log entry is recorded. |
| **Priority** | Must Have |

**Normal Flow:**
1. Admin opens the active booking and clicks "Extend Stay."
2. Admin selects the extension type:
   - **Time extension:** Adjust the check-out time later on the same day (e.g., from 12 PM to 3 PM).
   - **Date extension:** Add extra nights to the stay.
3. Admin enters the new check-out date/time.
4. System checks availability for the extended period.
5. System calculates the additional charge:
   - Time extension: (hours extended) × configured hourly rate.
   - Date extension: (nights added) × configured nightly rate.
6. System creates the extension record in "awaiting payment" status.
7. System generates an extension bill.
8. System sets the payment deadline based on the unit-specific extension payment window, or the system-wide default if the unit does not have a custom setting.
9. System sends the guest a notification with the extension charge, payment deadline, and a direct link to pay.
10. System records an audit log entry.

**Alternative Flows:**
- **A1 – Extended dates conflict with another booking:** System shows the conflicting booking details. Admin must select a different extension period.

---

### UC-QR-04: Pay Extension Charge

| Field | Detail |
|---|---|
| **Use Case ID** | UC-QR-04 |
| **Use Case Name** | Pay Extension Charge |
| **Actor(s)** | Guest |
| **Description** | A guest pays the additional extension charge through the online payment service within the configured payment deadline. Upon confirmed payment, the booking dates are updated to reflect the extension and the guest's QR access code validity is extended to the new check-out time. |
| **Preconditions** | An extension record exists in "awaiting payment" status. The payment deadline has not passed. |
| **Postconditions** | Payment is recorded as successful. Extension status is updated to "confirmed." Booking check-out date/time is updated to the new extended values. QR code validity is extended to the new check-out time. Guest receives a confirmation notification. |
| **Priority** | Must Have |

**Normal Flow:**
1. Guest opens their booking detail page and finds the pending extension charge.
2. Guest clicks "Pay Extension Charge."
3. System redirects to the secure payment page with the extension bill amount.
4. Guest completes the payment.
5. The payment service sends a confirmation back to HomeLodge.
6. System verifies the confirmation and records the payment as successful.
7. System updates the extension status to "confirmed."
8. System updates the booking's check-out date and time to the new extended values.
9. System extends the QR code validity to match the new check-out date/time.
10. System sends the guest a confirmation notification: "Your stay has been extended. Your QR code is now valid until [new check-out date/time]."
11. System records an audit log entry.

---

### UC-QR-05: Auto-Cancel Extension

| Field | Detail |
|---|---|
| **Use Case ID** | UC-QR-05 |
| **Use Case Name** | Auto-Cancel Extension |
| **Actor(s)** | System |
| **Description** | A scheduled job runs regularly to find extension records whose payment deadline has passed without payment. These extensions are automatically cancelled and the booking reverts to its original check-out date/time. The QR code is not modified (it stays at the original check-out time). The guest is notified. |
| **Preconditions** | An extension record is in "awaiting payment" status and the payment deadline has passed. |
| **Postconditions** | Extension status is set to "cancelled." The booking is reverted to the original check-out date/time. The QR code is not modified. The guest is notified. An audit log entry is recorded. |
| **Priority** | Must Have |

**Normal Flow:**
1. Scheduled job runs every few minutes.
2. System finds all extension records where the status is "awaiting payment" and the payment deadline has passed.
3. For each overdue extension:
   - System sets the extension status to "cancelled."
   - System reverts the booking's check-out date and time to the original values (stored when the extension was created).
   - The QR code is not modified — it already reflects the original check-out time.
4. System sends the guest a notification: "Your extension request was not confirmed because payment was not received by the deadline. Your booking remains at the original check-out: [original date/time]."
5. System records an audit log entry.

---

## 11. Reporting & Analytics Module

This module gives administrators a bird's-eye view of business performance through dashboards, charts, and reports. It helps with decision-making by showing trends in bookings, revenue, and guest satisfaction.

---

### UC-RPT-01: View Analytics Dashboard

| Field | Detail |
|---|---|
| **Use Case ID** | UC-RPT-01 |
| **Use Case Name** | View Analytics Dashboard |
| **Actor(s)** | Admin |
| **Description** | The administrator views the main reporting and analytics dashboard, which displays a summary of key business numbers — total bookings, total revenue, occupancy rate, cancellation rate, and average guest rating — along with visual charts showing booking trends over time, revenue breakdowns, per-unit booking comparisons, and a guest feedback and rating summary. |
| **Preconditions** | Admin is signed in with reporting access. |
| **Postconditions** | The dashboard loads with real-time data from the system. Key performance indicators and charts are displayed. |
| **Priority** | Must Have |

**Normal Flow:**
1. Admin goes to the Reporting & Analytics section.
2. System calculates and displays key performance indicators:
   - Total bookings (current month and year-to-date).
   - Total revenue (current month and year-to-date).
   - Occupancy rate (percentage of available dates that were booked).
   - Cancellation rate (percentage of bookings that were cancelled).
   - Average guest rating (from all visible reviews).
3. System displays visual charts:
   - Booking trends chart (line chart showing booking volumes over time; admin can toggle between daily, weekly, and monthly views).
   - Revenue summary.
   - Per-unit booking breakdown (bar chart comparing booking counts, occupancy, and revenue across different units).
   - Guest feedback and rating summary per unit (average score, review count, rating distribution).
4. Admin can interact with the charts to explore the data in more detail.
5. Admin can filter data by date range or specific unit.

---

### UC-RPT-02: View Revenue Report

| Field | Detail |
|---|---|
| **Use Case ID** | UC-RPT-02 |
| **Use Case Name** | View Revenue Report |
| **Actor(s)** | Admin |
| **Description** | The administrator views a detailed revenue report with configurable filters. The report shows total revenue, revenue broken down by individual bookings, and a summary by homestay unit. The administrator can filter by date range, unit, and payment status, and can export the report for offline use. |
| **Preconditions** | Payment records exist. Admin has reporting access. |
| **Postconditions** | Filtered revenue figures are displayed. The admin can download the report. |
| **Priority** | Must Have |

**Normal Flow:**
1. Admin goes to Reporting → Revenue Report.
2. Admin applies filters: date range, homestay unit (all or specific), and payment status.
3. System retrieves the payment and booking data matching the filters.
4. System displays: total revenue, itemised breakdown by booking, and summary by unit.
5. Admin can click "Export PDF" or "Export CSV" to download the report (see UC-RPT-03).

---

### UC-RPT-03: Export Report

| Field | Detail |
|---|---|
| **Use Case ID** | UC-RPT-03 |
| **Use Case Name** | Export Report |
| **Actor(s)** | Admin |
| **Description** | The administrator downloads any report view (revenue, booking breakdown, feedback summary) as a PDF document or a spreadsheet file (CSV) for offline use, sharing with stakeholders, or archiving. |
| **Preconditions** | Admin is viewing a report page with data loaded. |
| **Postconditions** | A PDF or CSV file is generated and downloaded to the admin's device. |
| **Priority** | Should Have |

**Normal Flow:**
1. Admin clicks "Export PDF" or "Export CSV" on a report page.
2. System collects the currently filtered report data.
3. System generates the file in the chosen format (PDF document or CSV spreadsheet).
4. The file is automatically downloaded to the admin's device.

---

## 12. Guest Feedback Module

This module allows guests to share their experience after completing a stay, and gives administrators the tools to manage and respond to guest reviews. Feedback and ratings are visible on the listing page to help future guests make informed choices.

---

### UC-FB-01: Submit Rating & Feedback

| Field | Detail |
|---|---|
| **Use Case ID** | UC-FB-01 |
| **Use Case Name** | Submit Rating & Feedback |
| **Actor(s)** | Guest |
| **Description** | After completing a stay, the guest can rate their experience (1 to 5 stars, required) and optionally write a comment about their stay. Each booking allows only one review. The review is immediately visible on the unit's listing and detail page, and the unit's average rating is recalculated. |
| **Preconditions** | The booking status is "completed" (the stay is over). No review has been submitted yet for this booking. The guest is signed in. |
| **Postconditions** | A feedback record is created and set to "visible." The unit's average rating is recalculated to include the new review. |
| **Priority** | Must Have |

**Normal Flow:**
1. Guest goes to "My Bookings" → "History."
2. Guest finds a completed booking and clicks "Leave a Review."
3. System verifies the booking is completed and no review exists for it yet.
4. System shows the feedback form: star rating selector (1–5 stars, required) and a text area for an optional comment.
5. Guest selects a star rating and optionally writes a comment.
6. Guest clicks "Submit."
7. System saves the review (linked to the booking, unit, and guest, with visibility set to "visible").
8. System recalculates the unit's average rating.
9. A thank-you confirmation message is shown.
10. The "Leave a Review" button is hidden for this booking going forward.

**Alternative Flows:**
- **A1 – Star rating not selected:** System highlights the star rating as required and blocks submission.
- **A2 – Review already submitted for this booking:** The "Leave a Review" button is hidden and replaced with "View Your Review."

**Exception Flows:**
- **E1 – Booking is not "completed" (e.g., someone tries to access the form directly):** System blocks the action and shows a validation error. No review can be submitted.

---

### UC-FB-02: View Submitted Feedback

| Field | Detail |
|---|---|
| **Use Case ID** | UC-FB-02 |
| **Use Case Name** | View Submitted Feedback |
| **Actor(s)** | Guest |
| **Description** | A guest views all reviews they have previously submitted across their completed bookings. Each entry shows the unit name, stay dates, star rating, comment text, submission date, and any reply from the administrator. Reviews are read-only and cannot be edited after submission. |
| **Preconditions** | Guest has submitted at least one review. Guest is signed in. |
| **Postconditions** | The guest's own review entries are displayed in read-only format. |
| **Priority** | Should Have |

**Normal Flow:**
1. Guest goes to "My Reviews" or views a specific completed booking in their history.
2. System retrieves all review records for the signed-in guest.
3. System displays each entry: unit name, check-in/check-out dates, star rating, comment, submission date, and admin reply (if any).
4. Entries are read-only; guests cannot edit submitted reviews.

---

### UC-FB-03: View / Manage All Feedback (Admin)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-FB-03 |
| **Use Case Name** | View / Manage All Feedback (Admin) |
| **Actor(s)** | Admin |
| **Description** | The administrator views all guest reviews across all units — including hidden ones — and can respond to reviews (to show engagement with guests) or moderate them by hiding those with inappropriate content. Hidden reviews are removed from the public listing but kept on record. When a review is hidden or restored, the unit's average rating is automatically recalculated. |
| **Preconditions** | Admin is signed in with feedback management access. |
| **Postconditions** | Admin can see all reviews, their ratings, visibility status, and any existing admin replies. Actions taken (reply, hide, restore) are reflected immediately on the guest-facing pages. |
| **Priority** | Must Have |

**Normal Flow (View):**
1. Admin goes to Guest Feedback management.
2. System retrieves all review records (including hidden ones).
3. System displays: guest name, unit, booking reference, rating, comment, date, visibility status ("visible" / "hidden"), and admin reply status.
4. Admin can filter by unit or visibility status.

**Normal Flow (Reply):**
1. Admin selects a review and clicks "Reply."
2. Admin types their response in the reply text area.
3. Admin clicks "Publish Reply."
4. System saves the reply and records the reply date.
5. The reply is immediately visible alongside the original review on the unit detail page.
6. If the guest views their submitted feedback (UC-FB-02), they also see the admin's reply.

**Normal Flow (Hide / Moderate):**
1. Admin selects a review with inappropriate content and clicks "Hide."
2. System asks for confirmation: "Hide this review from public view?"
3. Admin confirms.
4. System sets the review's visibility to "hidden."
5. System recalculates the unit's average rating (now excluding the hidden review).
6. The review is no longer shown on the guest-facing unit detail page.

**Alternative Flows:**
- **A1 – Admin cancels the hide confirmation:** No changes are made.
- **A2 – Admin restores a hidden review:** System sets the review back to "visible" and recalculates the rating again.
- **A3 – Admin edits an existing reply:** Admin modifies the text and saves. The reply date is updated to the edit time.

---

### UC-FB-04: Display Average Rating

| Field | Detail |
|---|---|
| **Use Case ID** | UC-FB-04 |
| **Use Case Name** | Display Average Rating |
| **Actor(s)** | System |
| **Description** | The system automatically calculates and displays the average star rating for each homestay unit on both the listing page and the unit detail page. Only visible (non-hidden) reviews are included in the calculation. The display format is, for example, "4.2 ★ (18 reviews)." The rating updates automatically whenever a new review is submitted, or an existing review is hidden or restored by the administrator. |
| **Preconditions** | At least one visible review exists for the unit. |
| **Postconditions** | The calculated average rating and review count are displayed on the unit listing card and the unit detail page. The values update dynamically with any feedback changes. |
| **Priority** | Must Have |

**Normal Flow:**
1. Guest or admin views the homestay listing page or a unit detail page.
2. System retrieves all visible reviews for each unit.
3. System calculates the average rating (sum of ratings divided by the number of visible reviews).
4. System displays "X.X ★ (N reviews)" on the unit card and detail page.

**Alternative Flows:**
- **A1 – No visible reviews exist for a unit:** System displays "No reviews yet" instead of a rating.

---

*End of Use Case Descriptions — HomeLodge v3.0*
