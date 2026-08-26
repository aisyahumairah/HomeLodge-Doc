# Use Case Descriptions — Table Format
## HomeLodge – Homestay Booking System

---

## 1. Authentication Module

---

### UC-AUTH-01: Register Account (Email / Password)

| | |
|---|---|
| **Use Case ID:** | UC-AUTH-01 |
| **Use Case Name:** | Register Account (Email / Password) |
| **Created By:** | Aisyah Umairah | **Last Updated By:** | - |
| **Date Created:** | | **Last Revision Date:** | |
| **Actors:** | Guest |
| **Description:** | A new visitor creates a HomeLodge account by providing their name, email address, and a password of their choice. Once registered, the person is given the "Guest" role and can sign in to browse and book homestay units. |
| **Pre-conditions:** | 1. The email address is not already linked to an existing account. 2. The registration page is open to everyone. |
| **Normal Flow:** | 1. Guest opens the registration page. 2. Guest enters their full name, email address, password, and retypes the password to confirm it. 3. System checks that the email is in the correct format, is not already taken, the password meets the strength rules (8–12 characters with a mix of upper-case, lower-case, numbers, and symbols), and both password entries match. 4. System securely saves the password and creates the account. 5. System assigns the "Guest" role to the new account. 6. A success message is shown and the guest is taken to the sign-in page. |
| **Exception:** | **AF.1. Email already in use** — System highlights the email field and suggests the person either sign in or use "Forgot Password." Flow returns to step 2. **AF.2. Password does not meet strength rules** — System shows which rules are not yet met. Flow returns to step 2. **AF.3. Password entries do not match** — System highlights the confirmation field. Flow returns to step 2. **AF.4. Invalid email format** — System highlights the email field. Flow returns to step 2. **EF.1. Unexpected system error** — A general message is shown ("Registration failed. Please try again."). No account is created. |
| **Post-conditions:** | A new account is created with the "Guest" role. The person can now sign in. |

---

### UC-AUTH-02: Login / Login via Google SSO

| | |
|---|---|
| **Use Case ID:** | UC-AUTH-02 |
| **Use Case Name:** | Login / Login via Google SSO |
| **Created By:** | Aisyah Umairah | **Last Updated By:** | - |
| **Date Created:** | | **Last Revision Date:** | |
| **Actors:** | Guest, Registered User, Admin |
| **Description:** | A registered user signs in to HomeLodge using either their email and password, or their Google account. If a person signs in with Google for the first time and does not already have a HomeLodge account, the system creates one automatically with the "Guest" role. |
| **Pre-conditions:** | 1. The person has either a HomeLodge account or a valid Google account. 2. The account is not locked or deactivated. |
| **Normal Flow:** | **Email & Password:** 1. User goes to the sign-in page and enters their email and password. 2. System verifies the credentials are correct. 3. System checks that the account is active and not locked. 4. System signs the user in and records the sign-in time. 5. System checks whether a password change is required. If yes, user is redirected to forced password change page. If no, user is taken to their dashboard. **Google Sign-In:** 1. User clicks "Continue with Google." 2. User is taken to the Google sign-in screen and grants permission. 3. Google sends the person's name and email back to HomeLodge. 4. System checks whether an account with that email already exists. If yes, links the Google identity and signs in. If no, creates a new account with the "Guest" role. 5. User is signed in and taken to their dashboard. |
| **Exception:** | **EF.1. System is temporarily unavailable** — An error message is shown and no session is created. **EF.2. Google sign-in service is unreachable** — System shows an error and returns the person to the sign-in page. |
| **Post-conditions:** | The person is signed in and taken to their dashboard. If a new account was created through Google, it is set up with the "Guest" role. |

---

### UC-AUTH-03: Logout

| | |
|---|---|
| **Use Case ID:** | UC-AUTH-03 |
| **Use Case Name:** | Logout |
| **Created By:** | Aisyah Umairah | **Last Updated By:** | - |
| **Date Created:** | | **Last Revision Date:** | |
| **Actors:** | Registered User (Guest or Admin) |
| **Description:** | A signed-in user ends their session and signs out of HomeLodge. After signing out, the user must sign in again to access any protected areas of the system. |
| **Pre-conditions:** | 1. The user is currently signed in. |
| **Normal Flow:** | 1. User clicks the "Logout" button in the navigation menu. 2. System ends the current session and clears any "remember me" settings. 3. User is redirected to the sign-in page. 4. If the user tries to go back using the browser's back button, they will see the sign-in page instead of the previously protected content. |
| **Exception:** | **EF.1. Session had already expired before the user clicked Logout** — System still redirects to the sign-in page without showing an error. |
| **Post-conditions:** | The session is ended. The user is taken to the sign-in page and can no longer access protected pages without signing in again. |

---

### UC-AUTH-04: Forgot Password (Reset via Email)

| | |
|---|---|
| **Use Case ID:** | UC-AUTH-04 |
| **Use Case Name:** | Forgot Password (Reset via Email) |
| **Created By:** | Aisyah Umairah | **Last Updated By:** | - |
| **Date Created:** | | **Last Revision Date:** | |
| **Actors:** | Guest, Registered User, Admin |
| **Description:** | A person who has forgotten their password requests a reset link via email. The system sends a time-limited link; clicking it allows the person to set a new password. If the account was previously locked due to too many failed sign-in attempts, this process also unlocks it. |
| **Pre-conditions:** | 1. The person has a registered email account. 2. The system's email sending feature is configured and working. |
| **Normal Flow:** | 1. User clicks "Forgot Password" on the sign-in page. 2. User enters their registered email address. 3. System checks whether the email exists in the system. 4. System generates a time-limited password reset link (valid for 60 minutes by default). 5. System sends the reset link to the email address. 6. System displays: "If an account exists with this email, a reset link has been sent." 7. User opens their email and clicks the reset link. 8. System checks that the link is still valid. 9. A password reset form is shown. 10. User enters a new password and confirms it. 11. System securely saves the new password. 12. System marks the reset link as used. 13. If the account was locked, the system removes the lock and resets the failed sign-in counter. 14. User is redirected to the sign-in page with a success message. |
| **Exception:** | **EF.1. Email delivery failure** — The reset link is created but may not reach the person. The person can try again. |
| **Post-conditions:** | The person has a new password and can sign in with it. The reset link can no longer be reused. If the account was locked, the lock is removed. |

---

### UC-AUTH-05: View / Update Profile

| | |
|---|---|
| **Use Case ID:** | UC-AUTH-05 |
| **Use Case Name:** | View / Update Profile |
| **Created By:** | Aisyah Umairah | **Last Updated By:** | - |
| **Date Created:** | | **Last Revision Date:** | |
| **Actors:** | Registered User (Guest or Admin) |
| **Description:** | A signed-in user views and updates their own personal information, including name, phone number, and profile photo. |
| **Pre-conditions:** | 1. User is signed in. |
| **Normal Flow:** | 1. User goes to the Profile page. 2. System displays current profile information (name, email, phone number, profile photo). 3. User changes one or more fields. 4. User uploads a new profile photo (optional). 5. User clicks "Save." 6. System checks that the changes are valid. 7. System saves the updated information. 8. A confirmation message is shown: "Profile updated successfully." |
| **Exception:** | **EF.1. Photo upload failure** — System rolls back the change and notifies the user. |
| **Post-conditions:** | Updated profile information is saved and reflected across the system. |

---

### UC-AUTH-06: Force Change Password

| | |
|---|---|
| **Use Case ID:** | UC-AUTH-06 |
| **Use Case Name:** | Force Change Password |
| **Created By:** | Aisyah Umairah | **Last Updated By:** | - |
| **Date Created:** | | **Last Revision Date:** | |
| **Actors:** | Registered User |
| **Description:** | When an administrator resets a user's password, the system marks the account so that the user must choose a new personal password the next time they sign in. Until they do so, they cannot access any other part of the system. |
| **Pre-conditions:** | 1. An administrator has reset the user's password. 2. The user has successfully signed in with the temporary password. |
| **Normal Flow:** | 1. User signs in with the temporary password provided by the administrator. 2. System detects that a password change is required. 3. System redirects the user to the "Change Your Password" page. 4. System blocks access to all other pages until this step is completed. 5. User enters a new password and confirms it. 6. User clicks "Save." 7. System securely saves the new password. 8. The forced password change requirement is cleared. 9. User is redirected to their dashboard. |
| **Exception:** | **EF.1. User tries to navigate to another page while the flag is active** — System redirects them back to the password change page. |
| **Post-conditions:** | The user has set a new personal password. The forced password change flag is cleared. The user can now use the system normally. |

---

## 2. Homestay Management Module

---

### UC-HS-01: Browse Homestay Units

| | |
|---|---|
| **Use Case ID:** | UC-HS-01 |
| **Use Case Name:** | Browse Homestay Units |
| **Created By:** | Aisyah Umairah | **Last Updated By:** | - |
| **Date Created:** | | **Last Revision Date:** | |
| **Actors:** | Guest |
| **Description:** | A guest views the list of all available homestay units, each displayed with a summary including name, thumbnail photo, base nightly price, location, and average guest rating. |
| **Pre-conditions:** | 1. At least one active homestay unit exists in the system. 2. The guest may or may not be signed in. |
| **Normal Flow:** | 1. Guest opens the homestay listing page. 2. System retrieves all active units. 3. System displays each unit as a card showing: name, main photo, price per night, location, and average rating. 4. Guest scrolls through the options and clicks on a unit they are interested in. 5. Guest is taken to the selected unit's detail page. |
| **Exception:** | **EF.1. No active units exist** — System shows: "No homestay units are currently available." |
| **Post-conditions:** | The guest can see all available units and can select one to view its full details. |

---

### UC-HS-02: View Unit Details & Availability

| | |
|---|---|
| **Use Case ID:** | UC-HS-02 |
| **Use Case Name:** | View Unit Details & Availability |
| **Created By:** | Aisyah Umairah | **Last Updated By:** | - |
| **Date Created:** | | **Last Revision Date:** | |
| **Actors:** | Guest |
| **Description:** | A guest views the full detail page for a selected homestay unit, including all photos, complete description, pricing, check-in/check-out times, house rules, guest reviews, and a real-time availability calendar showing which dates are free, booked, or blocked. |
| **Pre-conditions:** | 1. The guest has selected a unit from the listing page. 2. The unit is active. |
| **Normal Flow:** | 1. Guest clicks on a unit card from the listing page. 2. System loads the unit detail page showing: unit name, all photos, full description, location, base price per night, deposit amount, check-in and check-out times, house rules, and average rating with guest reviews. 3. System displays the availability calendar with colour-coded dates: Available, Booked, Temporarily held, Blocked. 4. Guest reviews the information and can click "Book Now" to start the booking process. |
| **Exception:** | - |
| **Post-conditions:** | The guest has all the information they need to decide whether to proceed with a booking. |

---

### UC-HS-03: Create Homestay Unit

| | |
|---|---|
| **Use Case ID:** | UC-HS-03 |
| **Use Case Name:** | Create Homestay Unit |
| **Created By:** | Aisyah Umairah | **Last Updated By:** | - |
| **Date Created:** | | **Last Revision Date:** | |
| **Actors:** | Admin |
| **Description:** | An administrator adds a new homestay unit to the system with all required details. Upon creation, the system automatically applies the standard default house rules. The unit becomes visible to guests immediately. |
| **Pre-conditions:** | 1. Administrator is signed in with permission to create homestay units. |
| **Normal Flow:** | 1. Admin goes to Homestay Management → Create New Unit. 2. Admin fills in: unit name, description, location/address, base price per night, and deposit amount. 3. Admin sets the default check-in and check-out times. 4. Admin uploads one or more photos. 5. Admin optionally sets a custom extension payment window. 6. Admin clicks "Save." 7. System checks that all required fields are filled in correctly. 8. System saves the unit record. 9. System automatically copies all active default house rules to the new unit's rules list. 10. System records an audit log entry. 11. A success message is shown. |
| **Exception:** | **EF.1. Photo upload failure** — System saves the unit record but shows a warning that the photo upload failed. Admin can upload photos later by editing the unit. |
| **Post-conditions:** | A new homestay unit record is created and set to "active." Default house rules are automatically attached. The unit appears on the guest-facing listing page. An audit log entry is recorded. |

---

### UC-HS-04: Edit Homestay Unit

| | |
|---|---|
| **Use Case ID:** | UC-HS-04 |
| **Use Case Name:** | Edit Homestay Unit |
| **Created By:** | Aisyah Umairah | **Last Updated By:** | - |
| **Date Created:** | | **Last Revision Date:** | |
| **Actors:** | Admin |
| **Description:** | An administrator updates the details of an existing homestay unit. All changes take effect immediately across the system, including on the guest-facing listing page. |
| **Pre-conditions:** | 1. The unit exists. 2. Admin is signed in with permission to edit homestay units. |
| **Normal Flow:** | 1. Admin goes to Homestay Management → selects the unit → clicks "Edit." 2. System shows a form pre-filled with the unit's current details. 3. Admin changes the desired fields. 4. Admin clicks "Save." 5. System checks the changes are valid. 6. System saves the updated record and records an audit log entry. 7. A success message is shown. |
| **Exception:** | - |
| **Post-conditions:** | The unit record is updated. Guest-facing pages reflect the new information. An audit log entry is recorded. |

---

### UC-HS-05: Deactivate / Delete Unit

| | |
|---|---|
| **Use Case ID:** | UC-HS-05 |
| **Use Case Name:** | Deactivate / Delete Unit |
| **Created By:** | Aisyah Umairah | **Last Updated By:** | - |
| **Date Created:** | | **Last Revision Date:** | |
| **Actors:** | Admin |
| **Description:** | An administrator takes a homestay unit off the market by deactivating or soft-deleting it. Once deactivated, the unit is hidden from the guest listing page and no new bookings can be made for it. Existing confirmed bookings are not affected. |
| **Pre-conditions:** | 1. Admin is signed in with permission to manage units. 2. The unit has no confirmed future bookings. |
| **Normal Flow:** | 1. Admin opens the unit's management page. 2. Admin clicks "Deactivate" or "Delete." 3. System checks for any upcoming confirmed bookings on this unit. 4. System asks for confirmation. 5. Admin confirms. 6. System marks the unit as inactive and records an audit log entry. 7. A success message is shown. The unit disappears from the guest listing page. |
| **Exception:** | **EF.1. Admin cancels the confirmation prompt** — No changes are made. |
| **Post-conditions:** | The unit is set to "inactive." It no longer appears on the guest listing page. Existing data is kept. An audit log entry is recorded. |

---

### UC-HS-06: View All Units List

| | |
|---|---|
| **Use Case ID:** | UC-HS-06 |
| **Use Case Name:** | View All Units List |
| **Created By:** | Aisyah Umairah | **Last Updated By:** | - |
| **Date Created:** | | **Last Revision Date:** | |
| **Actors:** | Admin |
| **Description:** | An administrator views a complete, searchable list of all homestay units in the system — both active and inactive — along with their status, upcoming booking count, and quick-action links. |
| **Pre-conditions:** | 1. Admin is signed in with homestay management access. |
| **Normal Flow:** | 1. Admin goes to the Homestay Management section. 2. System retrieves all unit records, including inactive ones. 3. System displays a list showing each unit's name, status (active / inactive), number of upcoming confirmed bookings, base price, and action buttons. 4. Admin can search or filter the list by status. |
| **Exception:** | - |
| **Post-conditions:** | Admin has a full operational view of all managed properties. |

---

## 3. Booking Module

---

### UC-BK-01: View Availability & Select Dates

| | |
|---|---|
| **Use Case ID:** | UC-BK-01 |
| **Use Case Name:** | View Availability & Select Dates |
| **Created By:** | Aisyah Umairah | **Last Updated By:** | - |
| **Date Created:** | | **Last Revision Date:** | |
| **Actors:** | Guest |
| **Description:** | A guest views a colour-coded availability calendar for a specific homestay unit and selects their preferred check-in and check-out dates. The system checks availability in real time upon selection. |
| **Pre-conditions:** | 1. The guest has selected a unit from the listing or unit detail page. |
| **Normal Flow:** | 1. Guest is on the unit detail page or booking form. 2. System displays the availability calendar with colour-coded date states. 3. Guest clicks a check-in date on the calendar. 4. System highlights the selected check-in date. 5. Guest clicks a check-out date. 6. System instantly checks whether all dates in the selected range are available. 7. System confirms availability and updates the booking summary with total nights and estimated cost. 8. Guest optionally adjusts check-in and check-out times. |
| **Exception:** | **EF.1. Selected dates include an unavailable date** — System highlights the conflict. Guest must re-select dates. **EF.2. Check-out date is before the check-in date** — System shows a validation message. **EF.3. Minimum stay requirement not met** — System shows the minimum stay requirement. |
| **Post-conditions:** | The selected dates are confirmed as available. The booking summary shows the number of nights and estimated cost. The guest can proceed to submit the booking. |

---

### UC-BK-02: Submit Booking

| | |
|---|---|
| **Use Case ID:** | UC-BK-02 |
| **Use Case Name:** | Submit Booking |
| **Created By:** | Aisyah Umairah | **Last Updated By:** | - |
| **Date Created:** | | **Last Revision Date:** | |
| **Actors:** | Guest |
| **Description:** | A guest submits a booking for a selected unit and date range. The system creates the booking in "awaiting payment" status and gives the guest a one-day window to complete payment. A bill is automatically generated. |
| **Pre-conditions:** | 1. Guest is signed in. 2. The selected date range has been confirmed as available. 3. Guest is on the booking summary page. |
| **Normal Flow:** | 1. Guest reviews the booking summary (unit name, dates, times, total cost including deposit). 2. Guest clicks "Confirm Booking." 3. System double-checks availability. 4. System creates the booking in "awaiting payment" status with a one-day payment deadline. 5. System automatically generates a bill with a unique bill number. 6. System sends an in-app and email notification to the guest with the bill and payment deadline. 7. Guest is directed to the payment page. |
| **Exception:** | **EF.1. System error during booking creation** — No record is created. System shows an error and allows the guest to try again. |
| **Post-conditions:** | A booking record is created in "awaiting payment" status. A bill is auto-generated. The selected dates are temporarily reserved. The guest receives a notification with the bill and payment deadline. |

---

### UC-BK-03: View Bookings

| | |
|---|---|
| **Use Case ID:** | UC-BK-03 |
| **Use Case Name:** | View Bookings |
| **Created By:** | Aisyah Umairah | **Last Updated By:** | - |
| **Date Created:** | | **Last Revision Date:** | |
| **Actors:** | Guest, Admin |
| **Description:** | A guest sees all their reservations — both current and past — in one place. Administrators can view all bookings system-wide. Bookings can be filtered by status, date range, or other criteria. |
| **Pre-conditions:** | 1. User is signed in. 2. At least one booking exists. |
| **Normal Flow:** | **Guest:** 1. Guest goes to "My Bookings." 2. System retrieves all bookings belonging to the guest. 3. System displays bookings in sections — "Current" (active and upcoming) and "History" (completed and cancelled). 4. Each booking shows: unit name, check-in/check-out dates, status, and total cost. 5. Guest clicks a booking to see its full details. **Admin:** 1. Admin goes to the Booking Management section. 2. System retrieves all bookings across all units. 3. System displays the bookings with filtering options. 4. Admin can use the booking calendar view to see all reservations at a glance. 5. Admin clicks a booking to view details or take action. |
| **Exception:** | - |
| **Post-conditions:** | The user can see a clear overview of all relevant bookings and can navigate to individual booking details. |

---

### UC-BK-04: View Booking Details

| | |
|---|---|
| **Use Case ID:** | UC-BK-04 |
| **Use Case Name:** | View Booking Details |
| **Created By:** | Aisyah Umairah | **Last Updated By:** | - |
| **Date Created:** | | **Last Revision Date:** | |
| **Actors:** | Guest, Admin |
| **Description:** | A user views the complete details of a specific booking, including unit information, dates and times, booking status, payment status, cancellation policy, QR access code (if confirmed), and any extension history. |
| **Pre-conditions:** | 1. The booking belongs to the signed-in guest, or the viewer is an administrator. |
| **Normal Flow:** | 1. User clicks on a booking from their bookings list. 2. System retrieves the full booking record and all related information. 3. System displays: unit name and photo, check-in/check-out date and time, total amount, payment status, booking status, cancellation policy and estimated refund, and QR code (if the booking is confirmed). 4. User can download the bill or receipt from this page. |
| **Exception:** | - |
| **Post-conditions:** | The user has full visibility of the reservation state and can take available actions (download bill, cancel booking, view receipt). |

---

### UC-BK-05: Cancel Booking

| | |
|---|---|
| **Use Case ID:** | UC-BK-05 |
| **Use Case Name:** | Cancel Booking |
| **Created By:** | Aisyah Umairah | **Last Updated By:** | - |
| **Date Created:** | | **Last Revision Date:** | |
| **Actors:** | Guest, Admin |
| **Description:** | A guest or administrator cancels a booking. If a payment was already made, the system calculates the refund amount based on the cancellation policy tiers. |
| **Pre-conditions:** | 1. The booking is in an active state ("awaiting payment" or "confirmed"). 2. The booking has not already been checked in. |
| **Normal Flow:** | 1. User opens the booking detail page. 2. User clicks "Cancel Booking." 3. System calculates the applicable refund amount based on the configured cancellation policy tiers. 4. System displays a confirmation dialog with the refund amount. 5. User clicks "Confirm Cancellation." 6. System sets the booking status to "cancelled" and releases the dates. 7. System records the refund amount and processes the refund (if applicable). 8. System sends cancellation notifications (in-app and email) to the guest and admin. 9. System records an audit log entry. |
| **Exception:** | **EF.1. Refund processing failure at the payment service** — System records the cancellation but flags the refund for the administrator to process manually. |
| **Post-conditions:** | Booking status is set to "cancelled." Reserved dates are released. Refund (if applicable) is initiated. Both the guest and the administrator are notified. An audit log entry is recorded. |

---

### UC-BK-06: Manage Booking (Admin)

| | |
|---|---|
| **Use Case ID:** | UC-BK-06 |
| **Use Case Name:** | Manage Booking (Admin) |
| **Created By:** | Aisyah Umairah | **Last Updated By:** | - |
| **Date Created:** | | **Last Revision Date:** | |
| **Actors:** | Admin |
| **Description:** | The administrator has full control over bookings — creating bookings on behalf of guests, editing existing booking details, deleting erroneous bookings, viewing the all-unit booking calendar, and blocking specific dates on a unit to prevent new bookings. |
| **Pre-conditions:** | 1. Admin is signed in with the appropriate booking management permissions. |
| **Normal Flow:** | **Create Booking on Behalf of Guest:** 1. Admin goes to Bookings → Create Booking. 2. Admin selects the target guest account. 3. Admin selects the unit and desired dates/times. 4. System checks availability and shows booking summary. 5. Admin confirms the booking. 6. System creates the booking in "awaiting payment" status, generates a bill, and notifies the guest. **Edit Booking:** 1. Admin opens the booking detail and clicks "Edit." 2. Admin changes the desired fields. 3. If dates are changed, system checks availability for the new dates. 4. Admin submits the changes. 5. System saves and records an audit log entry. 6. System notifies the guest. **Delete Booking:** 1. Admin selects a booking and clicks "Delete." 2. System shows a confirmation warning. 3. Admin confirms. 4. System deletes the booking, releases the dates, notifies the guest, and records an audit log entry. **Block Dates:** 1. Admin selects a unit and a date range. 2. Admin enters an internal note/reason. 3. Admin clicks "Block Dates." 4. The blocked dates immediately appear as "unavailable" on the guest-facing calendar. |
| **Exception:** | **EF.1. Blocked dates have confirmed bookings** — System warns admin. Admin must cancel existing bookings before blocking. |
| **Post-conditions:** | Booking records are updated as needed. Guests are notified of any changes affecting them. Audit log entries are recorded for all actions. |

---

### UC-BK-07: Auto-Cancel Expired Booking

| | |
|---|---|
| **Use Case ID:** | UC-BK-07 |
| **Use Case Name:** | Auto-Cancel Expired Booking |
| **Created By:** | Aisyah Umairah | **Last Updated By:** | - |
| **Date Created:** | | **Last Revision Date:** | |
| **Actors:** | System |
| **Description:** | The system runs a scheduled check to find all bookings that are still in "awaiting payment" status after the one-day payment deadline has passed. These bookings are automatically cancelled, the temporarily reserved dates are released, and the guest is notified. |
| **Pre-conditions:** | 1. A booking is in "awaiting payment" status. 2. The payment deadline has passed without payment being received. |
| **Normal Flow:** | 1. The scheduled job runs at regular intervals (approximately once per hour). 2. System finds all bookings where the status is "awaiting payment" and the payment deadline has passed. 3. For each such booking: system sets the status to "cancelled." 4. System releases the temporarily reserved dates. 5. System sends a cancellation notification (in-app and email) to the guest. 6. System records an audit log entry. |
| **Exception:** | - |
| **Post-conditions:** | Booking status is set to "cancelled." Dates are released and become available for new bookings. The guest receives a cancellation notification. An audit log entry is recorded. |

---

## 4. Payment Module

---

### UC-PAY-01: Make Payment

| | |
|---|---|
| **Use Case ID:** | UC-PAY-01 |
| **Use Case Name:** | Make Payment |
| **Created By:** | Aisyah Umairah | **Last Updated By:** | - |
| **Date Created:** | | **Last Revision Date:** | |
| **Actors:** | Guest, Payment Gateway |
| **Description:** | A guest pays the outstanding bill for their booking through the secure online payment service. Once payment is successfully confirmed, the booking status changes to "confirmed," a QR access code is generated and delivered to the guest, and a payment receipt is created. |
| **Pre-conditions:** | 1. Booking status is "awaiting payment." 2. A bill exists for the booking. 3. The payment deadline has not passed. 4. The payment service is configured. |
| **Normal Flow:** | 1. Guest clicks "Pay Now" from the booking detail page or from the bill notification. 2. System creates a payment request and redirects the guest to the secure payment page. 3. Guest completes the payment on the payment service's page. 4. The payment service processes the payment and sends a confirmation back to HomeLodge. 5. System verifies the confirmation is genuine. 6. System records the payment as successful. 7. System updates the booking status to "confirmed." 8. System generates a QR access code for the guest. 9. System generates a payment receipt. 10. System sends a booking confirmation notification (in-app and email) to the guest with the receipt and QR code. 11. System notifies the administrator of the new confirmed booking. |
| **Exception:** | **EF.1. Duplicate confirmation received** — System detects the duplication and ignores it without re-processing. **EF.2. Confirmation cannot be verified as genuine** — System rejects the confirmation and records a security alert. |
| **Post-conditions:** | Payment is recorded as successful. Booking status is updated to "confirmed." A QR access code is generated and delivered. A receipt is generated. The guest and admin are notified. |

---

### UC-PAY-02: View Payment & Billing Records

| | |
|---|---|
| **Use Case ID:** | UC-PAY-02 |
| **Use Case Name:** | View Payment & Billing Records |
| **Created By:** | Aisyah Umairah | **Last Updated By:** | - |
| **Date Created:** | | **Last Revision Date:** | |
| **Actors:** | Guest, Admin |
| **Description:** | Users can view their financial records. Guests see their own bills and payment history. Administrators see all billing and payment records system-wide. |
| **Pre-conditions:** | 1. User is signed in. 2. At least one billing or payment record exists. |
| **Normal Flow:** | **Guest:** 1. Guest goes to their booking detail or "Payment History" section. 2. System retrieves all payment records for the signed-in guest. 3. System displays each payment: payment number, booking reference, date, amount, and status. 4. Guest can view or download the itemised bill. 5. For completed payments, guest can view or download the receipt. **Admin:** 1. Admin goes to Payment Management. 2. System displays the billing list and the payment list. 3. Each entry shows: reference number, guest name, unit, amount, status, and date. 4. Admin can filter by date range, reference number, or status. 5. Admin clicks an entry to view details or take action. |
| **Exception:** | - |
| **Post-conditions:** | The user has a clear view of all relevant financial records. |

---

### UC-PAY-03: Regenerate Bill / Receipt

| | |
|---|---|
| **Use Case ID:** | UC-PAY-03 |
| **Use Case Name:** | Regenerate Bill / Receipt |
| **Created By:** | Aisyah Umairah | **Last Updated By:** | - |
| **Date Created:** | | **Last Revision Date:** | |
| **Actors:** | Admin |
| **Description:** | An administrator creates a fresh copy of a bill or receipt document on demand. Useful when the original document has been lost or when booking details have been updated. |
| **Pre-conditions:** | 1. The corresponding booking and billing/payment records exist. 2. Admin has the appropriate permission. |
| **Normal Flow:** | 1. Admin goes to the billing or payment detail page. 2. Admin clicks "Regenerate Bill" or "Regenerate Receipt." 3. System creates a fresh document using the latest booking and payment data. 4. System makes the new document available for download and optionally resends it to the guest via email. |
| **Exception:** | - |
| **Post-conditions:** | A new PDF document is generated and made available for download. |

---

## 5. Notification Module

---

### UC-NOTIF-01: Receive System Notification

| | |
|---|---|
| **Use Case ID:** | UC-NOTIF-01 |
| **Use Case Name:** | Receive System Notification |
| **Created By:** | Aisyah Umairah | **Last Updated By:** | - |
| **Date Created:** | | **Last Revision Date:** | |
| **Actors:** | Registered User (Guest or Admin), System |
| **Description:** | Users receive timely notifications whenever something important happens — both within the app (via a bell icon) and by email. Notifications cover all key system events. Email notifications can be turned on or off globally by the administrator. |
| **Pre-conditions:** | 1. User has an account. 2. A relevant system event has occurred. 3. For email notifications: the email feature must be configured and enabled. |
| **Normal Flow:** | **In-App:** 1. A triggering event occurs. 2. System creates a notification for the relevant user. 3. If the user is online, the bell icon badge updates in real time. 4. User clicks the bell icon to open the notifications panel. 5. System shows the list of notifications (newest first). 6. Clicking a notification marks it as read and takes the user to the relevant page. **Email:** 1. A triggering event occurs. 2. System checks whether email notifications are enabled. 3. If enabled: system sends an email to the user's registered address. **Automated Reminders:** 1. The system runs scheduled daily checks for upcoming events. 2. For payment reminders: system sends reminders to guests whose payment deadline is approaching. 3. For check-in/check-out reminders: system sends reminders to both the guest and the administrator. |
| **Exception:** | **EF.1. Email sending fails (mail server unreachable)** — System retries. After maximum retries, the failure is recorded. In-app notifications are unaffected. |
| **Post-conditions:** | A notification record is created. The user's bell icon shows the new notification count. An email is also sent (if email notifications are enabled). |

---

### UC-NOTIF-02: Google Calendar Integration

| | |
|---|---|
| **Use Case ID:** | UC-NOTIF-02 |
| **Use Case Name:** | Google Calendar Integration |
| **Created By:** | Aisyah Umairah | **Last Updated By:** | - |
| **Date Created:** | | **Last Revision Date:** | |
| **Actors:** | Registered User (Guest or Admin), Google Calendar |
| **Description:** | Confirmed bookings are automatically added to the user's connected Google Calendar as events spanning from check-in to check-out. |
| **Pre-conditions:** | 1. The user has connected their Google account to HomeLodge. 2. A booking has been confirmed. |
| **Normal Flow:** | 1. System detects a booking has been confirmed. 2. System retrieves the user's stored Google account connection. 3. System creates a calendar event with: title (unit name + "Stay"), start (check-in date/time), end (check-out date/time), description (booking reference, unit address). 4. If the event already exists: system updates it. 5. The event appears in the user's Google Calendar. |
| **Exception:** | **EF.1. Google Calendar service error or connection expired** — System records the failure. The user may need to reconnect their Google account. |
| **Post-conditions:** | A Google Calendar event is created or updated for the booking, showing the stay details. |

---

## 6. Chat Module

---

### UC-CHAT-01: Send / Receive Messages

| | |
|---|---|
| **Use Case ID:** | UC-CHAT-01 |
| **Use Case Name:** | Send / Receive Messages |
| **Created By:** | Aisyah Umairah | **Last Updated By:** | - |
| **Date Created:** | | **Last Revision Date:** | |
| **Actors:** | Guest, Admin |
| **Description:** | A user sends a text message to the other party (Guest ↔ Admin) via the built-in real-time chat system. Messages are saved in the system and delivered instantly. If the recipient is not currently online, the message is stored and they will see it the next time they sign in. |
| **Pre-conditions:** | 1. Both parties have accounts. 2. The sender is signed in and on the chat page. |
| **Normal Flow:** | 1. User opens the Chat page. 2. User types a message in the text input field. 3. User clicks "Send" or presses Enter. 4. System saves the message with the sender's identity, recipient's identity, message content, and timestamp. 5. System delivers the message instantly to the recipient's chat window (if they are online). 6. The message appears in the sender's chat window as "sent." |
| **Exception:** | **EF.1. Real-time connection lost** — System shows a "Reconnecting..." indicator. The message is still saved and will be visible on next page load. |
| **Post-conditions:** | The message is saved. The message is delivered to the recipient instantly if they are online. An unread count badge updates on the chat icon. |

---

### UC-CHAT-02: View Chat History

| | |
|---|---|
| **Use Case ID:** | UC-CHAT-02 |
| **Use Case Name:** | View Chat History |
| **Created By:** | Aisyah Umairah | **Last Updated By:** | - |
| **Date Created:** | | **Last Revision Date:** | |
| **Actors:** | Guest, Admin |
| **Description:** | A user views the full saved conversation between the guest and the administrator. Messages are displayed in the order they were sent, with sender names and timestamps. Unread messages are marked as read when viewed. |
| **Pre-conditions:** | 1. At least one message exists in the conversation. |
| **Normal Flow:** | 1. User opens the Chat page. 2. System retrieves all messages in the conversation, ordered from oldest to newest. 3. System displays each message with: sender name, message content, and timestamp. 4. Messages sent by the current user are shown on the right side; received messages on the left side. 5. System marks all unread messages as read. 6. The chat automatically scrolls to the most recent message. |
| **Exception:** | - |
| **Post-conditions:** | All messages are displayed. Unread messages are marked as read. |

---

## 7. User & Access Management Module

---

### UC-USR-01: Create User Account

| | |
|---|---|
| **Use Case ID:** | UC-USR-01 |
| **Use Case Name:** | Create User Account |
| **Created By:** | Aisyah Umairah | **Last Updated By:** | - |
| **Date Created:** | | **Last Revision Date:** | |
| **Actors:** | Admin |
| **Description:** | An administrator manually creates a new user account and assigns it a role. The system sets a temporary password, and the new user will be required to change it on their first sign-in. |
| **Pre-conditions:** | 1. The email address is not already registered. 2. Admin has the appropriate permission. |
| **Normal Flow:** | 1. Admin goes to User Management → Create User. 2. Admin enters: full name, email address, and assigns a role. 3. System generates a temporary password for the account. 4. System creates the user record and flags it to require a password change on first sign-in. 5. System sends the user an email with the temporary password and a link to sign in. 6. System records an audit log entry. 7. Admin sees a success message. |
| **Exception:** | - |
| **Post-conditions:** | A new account is created with the assigned role. The account is flagged so the user must change the temporary password on first sign-in. The user is notified with login instructions. An audit log entry is recorded. |

---

### UC-USR-02: Edit / Activate / Deactivate User

| | |
|---|---|
| **Use Case ID:** | UC-USR-02 |
| **Use Case Name:** | Edit / Activate / Deactivate User |
| **Created By:** | Aisyah Umairah | **Last Updated By:** | - |
| **Date Created:** | | **Last Revision Date:** | |
| **Actors:** | Admin |
| **Description:** | An administrator updates a user's details and can toggle the account between active and inactive states. Deactivating prevents the user from signing in and immediately ends any active sessions. |
| **Pre-conditions:** | 1. The user account exists. 2. Admin has the appropriate permission. |
| **Normal Flow:** | **Edit:** 1. Admin goes to User Management → selects user → clicks "Edit." 2. System shows the edit form pre-filled with the user's current information. 3. Admin updates the desired fields. 4. Admin clicks "Save." 5. System validates the changes and saves the updated record. 6. System records an audit log entry. 7. A success message is shown. **Activate / Deactivate:** 1. Admin selects user → clicks "Deactivate" or "Activate." 2. System asks for confirmation. 3. Admin confirms. 4. If deactivating: system disables the account and immediately ends any active sessions. 5. If activating: system restores the account so the user can sign in again. 6. System records an audit log entry. 7. A success message is shown. |
| **Exception:** | - |
| **Post-conditions:** | The account is updated. If the role was changed, new permissions take effect immediately. If deactivated, the user's active sessions are ended. An audit log entry is recorded. |

---

### UC-USR-03: Reset User Password

| | |
|---|---|
| **Use Case ID:** | UC-USR-03 |
| **Use Case Name:** | Reset User Password |
| **Created By:** | Aisyah Umairah | **Last Updated By:** | - |
| **Date Created:** | | **Last Revision Date:** | |
| **Actors:** | Admin |
| **Description:** | An administrator resets a user's password — either by sending a password reset link to the user's email or by resetting it to the system default temporary password. The user will be required to set a new personal password on their next sign-in. |
| **Pre-conditions:** | 1. The user account exists. 2. Admin has the appropriate permission. |
| **Normal Flow:** | 1. Admin goes to User Management → selects user → clicks "Reset Password." 2. Admin chooses the reset method: Option A – Send reset link to the user's registered address, or Option B – Set to default temporary password immediately. 3. System flags the account to require a password change on next sign-in. 4. If the account was locked, system removes the lock and resets the failed sign-in counter. 5. System notifies the user (in-app and email). 6. System records an audit log entry. |
| **Exception:** | - |
| **Post-conditions:** | The user's password is reset. The account is flagged to require a password change on next sign-in. If locked, the account is unlocked. The user is notified. An audit log entry is recorded. |

---

### UC-USR-04: Manage Roles

| | |
|---|---|
| **Use Case ID:** | UC-USR-04 |
| **Use Case Name:** | Manage Roles |
| **Created By:** | Aisyah Umairah | **Last Updated By:** | - |
| **Date Created:** | | **Last Revision Date:** | |
| **Actors:** | Admin |
| **Description:** | An administrator creates, edits, and deletes roles. Roles are categories that group permissions together. The administrator can also assign specific permissions to each role. Changes take effect immediately for all users with that role. |
| **Pre-conditions:** | 1. Admin has role management access. |
| **Normal Flow:** | **Create Role:** 1. Admin goes to Role Management → Create Role. 2. Admin enters the role name and optional description. 3. System checks the name is unique. 4. System creates the role. 5. Admin is taken to the role detail page to assign permissions. **Edit Role:** 1. Admin selects a role and clicks "Edit." 2. Admin modifies the name or description. 3. System checks the new name is unique and saves. **Assign Permissions to Role:** 1. Admin selects role → "Manage Permissions." 2. System shows a checklist of all available permissions. 3. Admin ticks or unticks permissions as required. 4. Admin saves the changes. 5. System updates the role's permissions. All users with this role are immediately affected. 6. System records an audit log entry. **Delete Role:** 1. Admin selects a role and clicks "Delete." 2. System checks if any users currently have this role. 3. If no users: system asks for confirmation and deletes the role. |
| **Exception:** | **EF.1. Role is assigned to users (Delete)** — System blocks the deletion and shows how many users are affected. Admin must reassign users before deleting. |
| **Post-conditions:** | Roles and their assigned permissions are updated. All users with a modified role immediately gain or lose the affected capabilities. An audit log entry is recorded. |

---

### UC-USR-05: Manage Permissions

| | |
|---|---|
| **Use Case ID:** | UC-USR-05 |
| **Use Case Name:** | Manage Permissions |
| **Created By:** | Aisyah Umairah | **Last Updated By:** | - |
| **Date Created:** | | **Last Revision Date:** | |
| **Actors:** | Admin |
| **Description:** | An administrator creates, edits, and deletes individual permissions. Permissions are the building blocks assigned to roles — they define specific capabilities such as "can create bookings," "can manage users," or "can view reports." |
| **Pre-conditions:** | 1. Admin has permission management access. |
| **Normal Flow:** | **Create Permission:** 1. Admin goes to Permission Management → Create Permission. 2. Admin enters the permission name and optional description. 3. System checks the name is unique. 4. System creates the permission. **Edit Permission:** 1. Admin selects a permission and clicks "Edit." 2. Admin modifies the name or description. 3. System saves the update. **Delete Permission:** 1. Admin selects a permission and clicks "Delete." 2. System checks if the permission is currently attached to any roles. 3. If not attached: system asks for confirmation and deletes it. 4. System records an audit log entry. |
| **Exception:** | **EF.1. Permission is attached to roles (Delete)** — System blocks the deletion and shows how many roles are affected. |
| **Post-conditions:** | Permissions are updated. They can be assigned to roles. An audit log entry is recorded. |

---

## 8. System Settings Module

---

### UC-SET-01: Configure System Settings

| | |
|---|---|
| **Use Case ID:** | UC-SET-01 |
| **Use Case Name:** | Configure System Settings |
| **Created By:** | Aisyah Umairah | **Last Updated By:** | - |
| **Date Created:** | | **Last Revision Date:** | |
| **Actors:** | Admin |
| **Description:** | The administrator configures all system-wide options from a centralised settings page. This includes outgoing email settings, security parameters, cancellation and refund policy tiers, payment and billing options, extension charge rates, and the global email notification toggle. |
| **Pre-conditions:** | 1. Admin is signed in with system settings access. |
| **Normal Flow:** | 1. Admin goes to System Settings. 2. Admin navigates to the relevant section and updates the values (Email, Security, Cancellation & Refund Policy, Payment & Billing, Extension Charges, Extension Payment Window, Email Notifications Toggle). 3. Admin clicks "Save." 4. System validates the settings. 5. System saves the settings and shows a confirmation message. 6. All future operations use the new settings. |
| **Exception:** | **EF.1. Email connection test fails** — System shows the error message. Admin can correct the settings and retry. |
| **Post-conditions:** | All updated settings are saved and take effect immediately across the system. |

---

### UC-SET-02: Manage Default Homestay Policies

| | |
|---|---|
| **Use Case ID:** | UC-SET-02 |
| **Use Case Name:** | Manage Default Homestay Policies |
| **Created By:** | Aisyah Umairah | **Last Updated By:** | - |
| **Date Created:** | | **Last Revision Date:** | |
| **Actors:** | Admin |
| **Description:** | The administrator manages the set of default house rules that are automatically applied to every new homestay unit when it is created. The system comes pre-loaded with standard defaults: No Pets, No Durians, No Smoking. Changes only affect units created after the update — existing units keep their current rules. |
| **Pre-conditions:** | 1. Admin is signed in with system settings access. |
| **Normal Flow:** | 1. Admin goes to System Settings → Default Policies. 2. System shows the current list of default house rules. 3. Admin can: Add a new default rule, Edit the text of an existing rule, or Remove a rule (with confirmation). 4. System saves the changes. New homestay units created in the future will start with the updated set of default rules. |
| **Exception:** | - |
| **Post-conditions:** | The default policy list is updated. New homestay units created after this change will inherit the updated defaults. Existing units are not retroactively changed. |

---

## 9. Audit Logs Module

---

### UC-AUDIT-01: View / Filter Audit Trail

| | |
|---|---|
| **Use Case ID:** | UC-AUDIT-01 |
| **Use Case Name:** | View / Filter Audit Trail |
| **Created By:** | Aisyah Umairah | **Last Updated By:** | - |
| **Date Created:** | | **Last Revision Date:** | |
| **Actors:** | Admin |
| **Description:** | The administrator views a complete, chronological record of all significant actions in the system — performed by users, administrators, and the system itself. Log entries are read-only and cannot be edited or deleted. |
| **Pre-conditions:** | 1. Admin is signed in with audit log access. |
| **Normal Flow:** | 1. Admin goes to the Audit Logs section. 2. System retrieves all log entries in reverse chronological order (newest first). 3. System displays each entry showing: timestamp, who performed the action, what happened, and what was affected. 4. Admin can page through the entries. **Filtering:** 1. Admin selects one or more filter criteria (date range, event type, or user name). 2. System applies the filters and re-renders the log with matching results only. 3. Admin can clear filters to return to the full log. |
| **Exception:** | - |
| **Post-conditions:** | Admin can view all audit entries. No modifications to log entries are possible. |

---

### UC-AUDIT-02: Automatic Event Logging

| | |
|---|---|
| **Use Case ID:** | UC-AUDIT-02 |
| **Use Case Name:** | Automatic Event Logging |
| **Created By:** | Aisyah Umairah | **Last Updated By:** | - |
| **Date Created:** | | **Last Revision Date:** | |
| **Actors:** | System |
| **Description:** | The system automatically records a log entry every time a significant action occurs — whether performed by a user, an administrator, or the system itself. Log entries are permanent and cannot be modified or deleted by anyone. |
| **Pre-conditions:** | 1. A significant event occurs somewhere in the system. |
| **Normal Flow:** | **User Actions:** 1. A user performs a significant action (e.g., submits a booking, makes a payment, leaves a review). 2. System automatically creates a log entry recording: the time, the person, the type of event, and what was affected. 3. The entry is permanently saved. **System Actions:** 1. An automated process triggers (e.g., auto-cancels an overdue booking, or a QR code is automatically expired). 2. System creates a log entry attributed to "System" with the appropriate event description. 3. The entry is permanently saved. |
| **Exception:** | - |
| **Post-conditions:** | A permanent, unchangeable log entry is created. It can be viewed by administrators through the Audit Trail but cannot be modified or deleted. |

---

## 10. QR Code & Access Module

---

### UC-QR-01: Receive & Use QR Code

| | |
|---|---|
| **Use Case ID:** | UC-QR-01 |
| **Use Case Name:** | Receive & Use QR Code |
| **Created By:** | Aisyah Umairah | **Last Updated By:** | - |
| **Date Created:** | | **Last Revision Date:** | |
| **Actors:** | Guest |
| **Description:** | After a booking is confirmed (payment received), the system automatically generates a unique, time-limited QR access code for the guest. The code is valid only during the booked stay — from check-in time to check-out time. The guest receives it via in-app notification and email, and can use it to unlock the homestay door. |
| **Pre-conditions:** | 1. Booking status has changed to "confirmed" (payment successfully received). |
| **Normal Flow:** | 1. System confirms payment. 2. System generates a unique QR code linked to the booking, valid from check-in until check-out date/time. 3. System sends an in-app notification to the guest with the QR code. 4. System sends an email to the guest containing the QR code and booking details. 5. Guest can view the QR code at any time on their booking detail page. 6. To enter the property, guest holds the QR code up to the smart lock scanner. 7. The lock verifies that the code is active and the current time is within the valid window. 8. If valid: access is granted and the door opens. |
| **Exception:** | **EF.1. QR code is expired (past check-out time)** — Access is denied. The lock displays "QR Code Expired." **EF.2. QR code is inactive or has been revoked** — Access is denied. **EF.3. Guest's device is unavailable** — Guest must contact the administrator for manual assistance. |
| **Post-conditions:** | A QR code record is created with "active" status, valid from check-in time until check-out time. Guest receives the QR code via in-app and email notifications. |

---

### UC-QR-02: Manage Housekeeping Cycle

| | |
|---|---|
| **Use Case ID:** | UC-QR-02 |
| **Use Case Name:** | Manage Housekeeping Cycle |
| **Created By:** | Aisyah Umairah | **Last Updated By:** | - |
| **Date Created:** | | **Last Revision Date:** | |
| **Actors:** | Admin, System |
| **Description:** | Handles the transition between guests. After a guest's check-out time passes, the system automatically expires their QR code. The administrator can then generate a temporary housekeeping QR code for cleaning staff. Once housekeeping is marked as complete, the system automatically generates a new QR code for the next guest (if there is an upcoming confirmed booking). |
| **Pre-conditions:** | 1. A guest has checked out and their QR code has been expired by the system. |
| **Normal Flow:** | **Automatic QR Expiry:** 1. A scheduled job runs at regular intervals. 2. System finds all active QR codes whose valid-until time has passed. 3. For each: system sets the QR code to "expired." 4. System updates the corresponding booking status to "completed." **Housekeeping Access:** 1. Admin clicks "Generate Housekeeping QR." 2. Admin sets the validity window (e.g., valid for 4 hours). 3. System generates a temporary QR code marked as "housekeeping" type. 4. System displays the code for admin to share with the cleaning staff. **Mark Housekeeping Complete:** 1. Admin clicks "Mark Housekeeping Complete." 2. System sets the housekeeping QR code to "expired." 3. System checks for the next confirmed booking on this unit. 4. If a next booking exists: system automatically generates a new QR code for the next guest and sends it via in-app and email notifications. |
| **Exception:** | - |
| **Post-conditions:** | The previous guest's code is expired. Housekeeping access is managed. The next guest's QR code is generated and delivered (if applicable). The booking is marked as completed. |

---

### UC-QR-03: Initiate Booking Extension

| | |
|---|---|
| **Use Case ID:** | UC-QR-03 |
| **Use Case Name:** | Initiate Booking Extension |
| **Created By:** | Aisyah Umairah | **Last Updated By:** | - |
| **Date Created:** | | **Last Revision Date:** | |
| **Actors:** | Admin |
| **Description:** | When a guest requests to stay longer, the administrator initiates a booking extension. The system checks whether the extended dates are available, calculates the additional charge, creates an extension record, generates a bill, and notifies the guest with a payment deadline. The guest's QR code is NOT extended at this point. |
| **Pre-conditions:** | 1. Booking status is "confirmed." 2. Admin has the appropriate permission. 3. No conflicting bookings exist for the extended period. |
| **Normal Flow:** | 1. Admin opens the active booking and clicks "Extend Stay." 2. Admin selects the extension type: Time extension (adjust check-out time on same day) or Date extension (add extra nights). 3. Admin enters the new check-out date/time. 4. System checks availability for the extended period. 5. System calculates the additional charge based on configured rates. 6. System creates the extension record in "awaiting payment" status. 7. System generates an extension bill. 8. System sets the payment deadline based on the unit-specific or system-wide extension payment window. 9. System sends the guest a notification with the extension charge, payment deadline, and a direct payment link. 10. System records an audit log entry. |
| **Exception:** | **EF.1. Extended dates conflict with another booking** — System shows the conflicting booking details. Admin must select a different extension period. |
| **Post-conditions:** | An extension record is created in "awaiting payment" status. An extension bill is generated. The guest is notified with the charge amount and payment deadline. The QR code validity is NOT yet updated. An audit log entry is recorded. |

---

### UC-QR-04: Pay Extension Charge

| | |
|---|---|
| **Use Case ID:** | UC-QR-04 |
| **Use Case Name:** | Pay Extension Charge |
| **Created By:** | Aisyah Umairah | **Last Updated By:** | - |
| **Date Created:** | | **Last Revision Date:** | |
| **Actors:** | Guest |
| **Description:** | A guest pays the additional extension charge through the online payment service within the configured payment deadline. Upon confirmed payment, the booking dates are updated and the guest's QR access code validity is extended to the new check-out time. |
| **Pre-conditions:** | 1. An extension record exists in "awaiting payment" status. 2. The payment deadline has not passed. |
| **Normal Flow:** | 1. Guest opens their booking detail page and finds the pending extension charge. 2. Guest clicks "Pay Extension Charge." 3. System redirects to the secure payment page with the extension bill amount. 4. Guest completes the payment. 5. The payment service sends a confirmation back to HomeLodge. 6. System verifies the confirmation and records the payment as successful. 7. System updates the extension status to "confirmed." 8. System updates the booking's check-out date and time to the new extended values. 9. System extends the QR code validity to match the new check-out date/time. 10. System sends the guest a confirmation notification. 11. System records an audit log entry. |
| **Exception:** | - |
| **Post-conditions:** | Payment is recorded as successful. Extension status is updated to "confirmed." Booking check-out date/time is updated. QR code validity is extended to the new check-out time. Guest receives a confirmation notification. |

---

### UC-QR-05: Auto-Cancel Extension

| | |
|---|---|
| **Use Case ID:** | UC-QR-05 |
| **Use Case Name:** | Auto-Cancel Extension |
| **Created By:** | Aisyah Umairah | **Last Updated By:** | - |
| **Date Created:** | | **Last Revision Date:** | |
| **Actors:** | System |
| **Description:** | A scheduled job runs regularly to find extension records whose payment deadline has passed without payment. These extensions are automatically cancelled and the booking reverts to its original check-out date/time. The QR code is not modified. The guest is notified. |
| **Pre-conditions:** | 1. An extension record is in "awaiting payment" status. 2. The payment deadline has passed. |
| **Normal Flow:** | 1. Scheduled job runs every few minutes. 2. System finds all extension records where the status is "awaiting payment" and the payment deadline has passed. 3. For each overdue extension: System sets the extension status to "cancelled." System reverts the booking's check-out date and time to the original values. The QR code is not modified. 4. System sends the guest a notification that the extension was cancelled due to non-payment. 5. System records an audit log entry. |
| **Exception:** | - |
| **Post-conditions:** | Extension status is set to "cancelled." The booking is reverted to the original check-out date/time. The QR code is not modified. The guest is notified. An audit log entry is recorded. |

---

## 11. Reporting & Analytics Module

---

### UC-RPT-01: View Analytics Dashboard

| | |
|---|---|
| **Use Case ID:** | UC-RPT-01 |
| **Use Case Name:** | View Analytics Dashboard |
| **Created By:** | Aisyah Umairah | **Last Updated By:** | - |
| **Date Created:** | | **Last Revision Date:** | |
| **Actors:** | Admin |
| **Description:** | The administrator views the main reporting and analytics dashboard, which displays a summary of key business numbers — total bookings, total revenue, occupancy rate, cancellation rate, and average guest rating — along with visual charts. |
| **Pre-conditions:** | 1. Admin is signed in with reporting access. |
| **Normal Flow:** | 1. Admin goes to the Reporting & Analytics section. 2. System calculates and displays key performance indicators: Total bookings, Total revenue, Occupancy rate, Cancellation rate, Average guest rating. 3. System displays visual charts: Booking trends (line chart), Revenue summary, Per-unit booking breakdown (bar chart), Guest feedback and rating summary per unit. 4. Admin can interact with the charts to explore the data in more detail. 5. Admin can filter data by date range or specific unit. |
| **Exception:** | - |
| **Post-conditions:** | The dashboard loads with real-time data from the system. Key performance indicators and charts are displayed. |

---

### UC-RPT-02: View Revenue Report

| | |
|---|---|
| **Use Case ID:** | UC-RPT-02 |
| **Use Case Name:** | View Revenue Report |
| **Created By:** | Aisyah Umairah | **Last Updated By:** | - |
| **Date Created:** | | **Last Revision Date:** | |
| **Actors:** | Admin |
| **Description:** | The administrator views a detailed revenue report with configurable filters. The report shows total revenue, revenue broken down by individual bookings, and a summary by homestay unit. The administrator can export the report for offline use. |
| **Pre-conditions:** | 1. Payment records exist. 2. Admin has reporting access. |
| **Normal Flow:** | 1. Admin goes to Reporting → Revenue Report. 2. Admin applies filters: date range, homestay unit (all or specific), and payment status. 3. System retrieves the payment and booking data matching the filters. 4. System displays: total revenue, itemised breakdown by booking, and summary by unit. 5. Admin can click "Export PDF" or "Export CSV" to download the report. |
| **Exception:** | - |
| **Post-conditions:** | Filtered revenue figures are displayed. The admin can download the report. |

---

### UC-RPT-03: Export Report

| | |
|---|---|
| **Use Case ID:** | UC-RPT-03 |
| **Use Case Name:** | Export Report |
| **Created By:** | Aisyah Umairah | **Last Updated By:** | - |
| **Date Created:** | | **Last Revision Date:** | |
| **Actors:** | Admin |
| **Description:** | The administrator downloads any report view as a PDF document or a spreadsheet file (CSV) for offline use, sharing with stakeholders, or archiving. |
| **Pre-conditions:** | 1. Admin is viewing a report page with data loaded. |
| **Normal Flow:** | 1. Admin clicks "Export PDF" or "Export CSV" on a report page. 2. System collects the currently filtered report data. 3. System generates the file in the chosen format (PDF document or CSV spreadsheet). 4. The file is automatically downloaded to the admin's device. |
| **Exception:** | - |
| **Post-conditions:** | A PDF or CSV file is generated and downloaded to the admin's device. |

---

## 12. Guest Feedback Module

---

### UC-FB-01: Submit Rating & Feedback

| | |
|---|---|
| **Use Case ID:** | UC-FB-01 |
| **Use Case Name:** | Submit Rating & Feedback |
| **Created By:** | Aisyah Umairah | **Last Updated By:** | - |
| **Date Created:** | | **Last Revision Date:** | |
| **Actors:** | Guest |
| **Description:** | After completing a stay, the guest can rate their experience (1 to 5 stars, required) and optionally write a comment about their stay. Each booking allows only one review. The review is immediately visible on the unit's listing and detail page, and the unit's average rating is recalculated. |
| **Pre-conditions:** | 1. The booking status is "completed" (the stay is over). 2. No review has been submitted yet for this booking. 3. The guest is signed in. |
| **Normal Flow:** | 1. Guest goes to "My Bookings" → "History." 2. Guest finds a completed booking and clicks "Leave a Review." 3. System verifies the booking is completed and no review exists for it yet. 4. System shows the feedback form: star rating selector (1–5 stars, required) and optional comment text area. 5. Guest selects a star rating and optionally writes a comment. 6. Guest clicks "Submit." 7. System saves the review (linked to the booking, unit, and guest, with visibility set to "visible"). 8. System recalculates the unit's average rating. 9. A thank-you confirmation message is shown. 10. The "Leave a Review" button is hidden for this booking going forward. |
| **Exception:** | **EF.1. Booking is not "completed"** — System blocks the action and shows a validation error. No review can be submitted. |
| **Post-conditions:** | A feedback record is created and set to "visible." The unit's average rating is recalculated to include the new review. |

---

### UC-FB-02: View Submitted Feedback

| | |
|---|---|
| **Use Case ID:** | UC-FB-02 |
| **Use Case Name:** | View Submitted Feedback |
| **Created By:** | Aisyah Umairah | **Last Updated By:** | - |
| **Date Created:** | | **Last Revision Date:** | |
| **Actors:** | Guest |
| **Description:** | A guest views all reviews they have previously submitted across their completed bookings. Each entry shows the unit name, stay dates, star rating, comment text, submission date, and any reply from the administrator. Reviews are read-only and cannot be edited after submission. |
| **Pre-conditions:** | 1. Guest has submitted at least one review. 2. Guest is signed in. |
| **Normal Flow:** | 1. Guest goes to "My Reviews" or views a specific completed booking in their history. 2. System retrieves all review records for the signed-in guest. 3. System displays each entry: unit name, check-in/check-out dates, star rating, comment, submission date, and admin reply (if any). 4. Entries are read-only; guests cannot edit submitted reviews. |
| **Exception:** | - |
| **Post-conditions:** | The guest's own review entries are displayed in read-only format. |

---

### UC-FB-03: View / Manage All Feedback (Admin)

| | |
|---|---|
| **Use Case ID:** | UC-FB-03 |
| **Use Case Name:** | View / Manage All Feedback (Admin) |
| **Created By:** | Aisyah Umairah | **Last Updated By:** | - |
| **Date Created:** | | **Last Revision Date:** | |
| **Actors:** | Admin |
| **Description:** | The administrator views all guest reviews across all units — including hidden ones — and can respond to reviews or moderate them by hiding those with inappropriate content. Hidden reviews are removed from the public listing but kept on record. |
| **Pre-conditions:** | 1. Admin is signed in with feedback management access. |
| **Normal Flow:** | **View:** 1. Admin goes to Guest Feedback management. 2. System retrieves all review records (including hidden ones). 3. System displays: guest name, unit, booking reference, rating, comment, date, visibility status, and admin reply status. 4. Admin can filter by unit or visibility status. **Reply:** 1. Admin selects a review and clicks "Reply." 2. Admin types their response. 3. Admin clicks "Publish Reply." 4. System saves the reply and records the reply date. 5. The reply is immediately visible alongside the original review. **Hide / Moderate:** 1. Admin selects a review with inappropriate content and clicks "Hide." 2. System asks for confirmation. 3. Admin confirms. 4. System sets the review's visibility to "hidden." 5. System recalculates the unit's average rating (excluding the hidden review). 6. The review is no longer shown on the guest-facing unit detail page. |
| **Exception:** | - |
| **Post-conditions:** | Admin can see all reviews, their ratings, visibility status, and any existing admin replies. Actions taken (reply, hide, restore) are reflected immediately on the guest-facing pages. |

---

### UC-FB-04: Display Average Rating

| | |
|---|---|
| **Use Case ID:** | UC-FB-04 |
| **Use Case Name:** | Display Average Rating |
| **Created By:** | Aisyah Umairah | **Last Updated By:** | - |
| **Date Created:** | | **Last Revision Date:** | |
| **Actors:** | System |
| **Description:** | The system automatically calculates and displays the average star rating for each homestay unit on both the listing page and the unit detail page. Only visible (non-hidden) reviews are included. The display format is: "4.2 ★ (18 reviews)." The rating updates automatically whenever a review is submitted, hidden, or restored. |
| **Pre-conditions:** | 1. At least one visible review exists for the unit. |
| **Normal Flow:** | 1. Guest or admin views the homestay listing page or a unit detail page. 2. System retrieves all visible reviews for each unit. 3. System calculates the average rating (sum of ratings divided by the number of visible reviews). 4. System displays "X.X ★ (N reviews)" on the unit card and detail page. |
| **Exception:** | **EF.1. No visible reviews exist for a unit** — System displays "No reviews yet" instead of a rating. |
| **Post-conditions:** | The calculated average rating and review count are displayed on the unit listing card and the unit detail page. The values update dynamically with any feedback changes. |

---

*End of Use Case Table Format — HomeLodge v3.0*
