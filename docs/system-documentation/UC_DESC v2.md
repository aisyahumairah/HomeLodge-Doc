# Use Case Descriptions
## HomeLodge – Booking Homestay System

| Field | Detail |
|---|---|
| **Document Version** | 2.0 |
| **Status** | Draft |
| **Last Updated** | 2026-05-01 |
| **Reference** | USE_CASE_DIAGRAMS.md (v1.2), PRD.md, URS.md (v1.2), DB_SCHEMA.md |
| **Supersedes** | USE_CASE_DESCRIPTIONS.md v1.0 |

> **Note:** This document is generated from the simplified use case diagram (`homeLodge-UC_Diagram_v2.png`) and cross-referenced against all project documentation. Each use case entry includes full actor, description, preconditions, normal/alternative/exception flows, and postconditions.

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
10. [Audit Log Module](#10-audit-log-module)
11. [QR Code Door Access Module](#11-qr-code-door-access-module)
12. [Reporting & Analytics Module](#12-reporting--analytics-module)
13. [Guest Feedback Module](#13-guest-feedback-module)

---

## 1. Authentication Module

---

### UC-AUTH-01: Register Account (Email / Password)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-AUTH-01 |
| **Use Case Name** | Register Account (Email / Password) |
| **Actor(s)** | Guest |
| **Description** | A new user registers for a HomeLodge account using their email address and a self-chosen password. Upon success, the system assigns the Guest role and allows the user to log in. |
| **Preconditions** | The email address is not already registered in the system. The registration page is publicly accessible. |
| **Postconditions** | A new user account is created with the `Guest` role. `must_change_password` is `false`. The user may log in. |
| **Priority** | Must Have |
| **URS Refs** | URS-U-AUTH-01, URS-U-AUTH-06 |
| **PRD Refs** | AUTH-01, AUTH-05 |

**Normal Flow:**
1. Guest navigates to the registration page.
2. Guest enters full name, email address, password, and password confirmation.
3. System validates: email format, email uniqueness, password length (8–12 characters), password complexity (uppercase, lowercase, number, symbol), and password confirmation match.
4. System hashes the password (bcrypt) and creates the user record.
5. System assigns the `Guest` role to the new user.
6. System displays a success message and redirects the user to the login page.

**Alternative Flows:**
- **A1 – Email already registered:** After step 3, system highlights the email field with an error message. System suggests using "Forgot Password" or signing in. Flow returns to step 2.
- **A2 – Password does not meet complexity rules:** System highlights the failing rules in the password strength indicator. Flow returns to step 2.
- **A3 – Password confirmation mismatch:** System highlights the confirmation field. Flow returns to step 2.
- **A4 – Invalid email format:** System highlights the email field. Flow returns to step 2.

**Exception Flows:**
- **E1 – Database error during account creation:** System displays a generic error message ("Registration failed. Please try again.") and logs the error. No account is created.

---

### UC-AUTH-02: Register / Login via Google SSO

| Field | Detail |
|---|---|
| **Use Case ID** | UC-AUTH-02 |
| **Use Case Name** | Register / Login via Google SSO |
| **Actor(s)** | Guest, Admin |
| **Description** | A user authenticates using their Google account via OAuth 2.0 (Laravel Socialite). If no HomeLodge account is linked to the Google identity, a new account is created automatically. If one exists, the user is logged in. |
| **Preconditions** | The user has a valid, active Google account. Google OAuth credentials are configured in the system. |
| **Postconditions** | The user is authenticated and redirected to their role-appropriate dashboard. If a new account was created, the `Guest` role is assigned. |
| **Priority** | Should Have |
| **URS Refs** | URS-U-AUTH-02, URS-A-AUTH-01 |
| **PRD Refs** | AUTH-03 |

**Normal Flow:**
1. User clicks "Continue with Google" on the login or registration page.
2. System redirects user to the Google OAuth consent screen.
3. User grants permission and authorises HomeLodge.
4. Google returns an authorisation code; system exchanges it for profile data (name, email, `google_id`).
5. System checks whether a user account with the returned email or `google_id` already exists.
   - **5a – Account exists:** System links the `google_id` (if not yet linked) and logs the user in.
   - **5b – No account exists:** System creates a new account with `Guest` role using the Google profile data. Password is not set (SSO-only account).
6. System creates a session and redirects the user to their dashboard.

**Alternative Flows:**
- **A1 – User denies Google permission:** Google redirects back with an error. System displays a message: "Google sign-in was cancelled." User is returned to the login page.
- **A2 – Email already registered via email/password:** System links the Google identity to the existing account and logs the user in.

**Exception Flows:**
- **E1 – Google OAuth server unreachable:** System displays an error message and returns the user to the login page.
- **E2 – Google returns invalid or incomplete profile data:** System logs the error and shows a generic failure message.

---

### UC-AUTH-03: Login

| Field | Detail |
|---|---|
| **Use Case ID** | UC-AUTH-03 |
| **Use Case Name** | Login |
| **Actor(s)** | Guest, Admin |
| **Description** | A registered user logs in to HomeLodge using their email address and password. The system validates credentials, enforces lockout rules, and starts an authenticated session. |
| **Preconditions** | The user has a registered, active account. The account is not locked. |
| **Postconditions** | User is authenticated. Session is created. `last_login_at` is updated. `failed_login_attempts` is reset to 0. User is redirected to their dashboard. |
| **Priority** | Must Have |
| **URS Refs** | URS-U-AUTH-03, URS-A-AUTH-01 |
| **PRD Refs** | AUTH-01, AUTH-05, AUTH-06 |

**Normal Flow:**
1. User navigates to the login page.
2. User enters email address and password.
3. System validates credentials against the stored bcrypt hash.
4. System checks that the account is active and not locked.
5. System creates an authenticated session and records `last_login_at`.
6. System resets `failed_login_attempts` to 0.
7. System checks if `must_change_password = true`.
   - **7a – `must_change_password = true`:** System redirects to the Force Change Password page (extends UC-AUTH-08). All other pages are blocked until completed.
   - **7b – `must_change_password = false`:** System redirects to the role-appropriate dashboard.

**Alternative Flows:**
- **A1 – Invalid credentials:** System increments `failed_login_attempts`. Displays a generic error ("Invalid email or password."). Flow returns to step 2.
- **A2 – Account deactivated:** System displays "Your account has been deactivated. Please contact support."
- **A3 – Account locked:** System displays a lockout message with estimated unlock time. Extends to UC-AUTH-09 (Account Lockout) and UC-AUTH-10 (Auto Unlock).

**Exception Flows:**
- **E1 – Database unavailable:** System displays a service unavailability error and does not create a session.

---

### UC-AUTH-04: Logout

| Field | Detail |
|---|---|
| **Use Case ID** | UC-AUTH-04 |
| **Use Case Name** | Logout |
| **Actor(s)** | Guest, Admin |
| **Description** | An authenticated user intentionally ends their session. The system invalidates the session and redirects the user to the login page. |
| **Preconditions** | The user is currently authenticated (logged in). |
| **Postconditions** | The user's session is invalidated. Any session tokens are destroyed. The user is redirected to the login page and cannot access protected pages without re-authenticating. |
| **Priority** | Must Have |
| **URS Refs** | URS-U-AUTH-04 |
| **PRD Refs** | AUTH-04 |

**Normal Flow:**
1. User clicks the "Logout" button in the navigation.
2. System invalidates and destroys the current session.
3. System clears any remember-me tokens if present.
4. User is redirected to the login page.
5. Browser back-button navigation to a protected page returns the login page (session expired).

**Exception Flows:**
- **E1 – Session already expired before logout action:** System still redirects to the login page without error.

---

### UC-AUTH-05: Forgot Password (Reset via Email)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-AUTH-05 |
| **Use Case Name** | Forgot Password (Reset via Email) |
| **Actor(s)** | Guest, Admin |
| **Description** | A user who has forgotten their password requests a password reset link via email. The system sends a time-limited link; clicking it allows the user to set a new password and clears any active account lockout. |
| **Preconditions** | The user has a registered email account. SMTP is configured in system settings. |
| **Postconditions** | The user's password is updated. The reset token is invalidated. If the account was locked, the lockout is cleared. The user can log in with the new password. |
| **Priority** | Must Have |
| **URS Refs** | URS-U-AUTH-05, URS-U-AUTH-06 |
| **PRD Refs** | AUTH-07 |

**Normal Flow:**
1. User clicks "Forgot Password" on the login page.
2. User enters their registered email address.
3. System validates that the email exists in the database.
4. System generates a unique, time-limited password reset token (expiry configurable; default: 60 minutes).
5. System sends the reset link to the provided email address.
6. System displays: "If an account exists with this email, a reset link has been sent."
7. User opens the email and clicks the reset link.
8. System validates the token (not expired, not already used).
9. System displays the password reset form.
10. User enters a new password and confirmation, satisfying complexity rules.
11. System hashes and saves the new password.
12. System invalidates the reset token.
13. System clears `locked_until` and resets `failed_login_attempts` to 0 (early unlock on reset).
14. System redirects the user to the login page with a success message.

**Alternative Flows:**
- **A1 – Email not found:** System still displays the generic message from step 6 to prevent user enumeration.
- **A2 – Reset link already used or expired:** System displays "This reset link is invalid or has expired." and prompts the user to request a new link.
- **A3 – New password fails complexity rules:** System highlights the failing rules. Flow returns to step 10.

**Exception Flows:**
- **E1 – Email delivery failure:** System logs the error. Token is stored but may not reach the user. User can try again.

---

### UC-AUTH-06: View / Update Profile

| Field | Detail |
|---|---|
| **Use Case ID** | UC-AUTH-06 |
| **Use Case Name** | View / Update Profile |
| **Actor(s)** | Guest, Admin |
| **Description** | An authenticated user views and updates their own profile information, including name, phone number, and profile photo. Email address changes may require re-verification. |
| **Preconditions** | User is logged in. |
| **Postconditions** | Updated profile information is persisted in the `users` table. |
| **Priority** | Should Have |
| **URS Refs** | URS-U-AUTH-08, URS-A-AUTH-03 |
| **PRD Refs** | AUTH-08, AUTH-11 |

**Normal Flow:**
1. User navigates to the Profile page.
2. System displays current profile information (name, email, phone, profile photo).
3. User modifies one or more fields.
4. User uploads a new profile photo (optional).
5. User submits the form.
6. System validates input (e.g., phone number format, file type for photo).
7. System saves the updated record.
8. System displays a success notification: "Profile updated successfully."

**Alternative Flows:**
- **A1 – Invalid phone number format:** System highlights the field and prompts for correction.
- **A2 – Uploaded file is not a valid image type or exceeds size limit:** System displays an appropriate error.

**Exception Flows:**
- **E1 – File storage failure during photo upload:** System rolls back the change and notifies the user.

---

### UC-AUTH-07: Show / Hide Password Toggle

| Field | Detail |
|---|---|
| **Use Case ID** | UC-AUTH-07 |
| **Use Case Name** | Show / Hide Password Toggle |
| **Actor(s)** | Guest |
| **Description** | On any form containing a password field (login, registration, password reset, forced change), the user can toggle the visibility of their password input. |
| **Preconditions** | A password input field is rendered and active on the current page. |
| **Postconditions** | The password field toggles between `type="password"` (masked) and `type="text"` (visible). No data is sent to the server during the toggle. |
| **Priority** | Should Have |
| **URS Refs** | URS-U-AUTH-07 |
| **PRD Refs** | AUTH-09 |

**Normal Flow:**
1. User is on a form with a password field.
2. User clicks the eye icon adjacent to the password field.
3. System toggles the input `type` attribute between `password` and `text`.
4. Icon updates to reflect the current state (eye-open / eye-closed).
5. User clicks again to restore masked input.

---

### UC-AUTH-08: Force Change Password (After Admin Reset)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-AUTH-08 |
| **Use Case Name** | Force Change Password |
| **Actor(s)** | Guest |
| **Description** | When an admin resets a user's password, the `must_change_password` flag is set to `true`. On the user's next login, the system intercepts navigation and redirects them to a mandatory password change screen. The user cannot access any other page until the password is changed. |
| **Preconditions** | Admin has reset the user's password (UC-USR-04). `must_change_password = true` on the user's record. User has successfully authenticated with the temporary password. |
| **Postconditions** | User has set a new personal password. `must_change_password` is set to `false`. User proceeds to their dashboard. |
| **Priority** | Must Have |
| **URS Refs** | URS-U-AUTH-03, URS-A-USR-03 |
| **PRD Refs** | AUTH-07, AUTH-10, USR-04 |

**Normal Flow:**
1. User logs in with the admin-provided temporary password.
2. System detects `must_change_password = true`.
3. System redirects user to the "Change Your Password" page.
4. System blocks access to all other routes until this step is complete (middleware guard).
5. User enters a new password and confirmation that meets complexity rules.
6. User submits the form.
7. System hashes and saves the new password.
8. System sets `must_change_password = false`.
9. System redirects user to the dashboard.

**Alternative Flows:**
- **A1 – New password same as temporary password:** System displays a warning and requires a different password.
- **A2 – Password fails complexity rules:** System highlights failing rules. Flow returns to step 5.

**Exception Flows:**
- **E1 – User attempts to navigate to another page while flag is active:** Middleware intercepts and redirects back to the force-change screen.

---

### UC-AUTH-09: Account Lockout (Exceeded Failed Attempts)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-AUTH-09 |
| **Use Case Name** | Account Lockout |
| **Actor(s)** | System |
| **Description** | The system automatically locks a user account after a configurable number of consecutive failed login attempts. This prevents brute-force attacks. The lockout threshold and duration are configurable in System Settings. |
| **Preconditions** | `failed_login_attempts` reaches the configured `security.max_attempts` threshold (default: 5). |
| **Postconditions** | `is_locked = true` (or `locked_until` timestamp is set). User cannot log in during the lockout period. A lockout event is written to the audit log. |
| **Priority** | Must Have |
| **URS Refs** | URS-A-SET-02 |
| **PRD Refs** | AUTH-05, AUTH-06, SET-SEC-01, SET-SEC-03 |

**Normal Flow:**
1. User submits incorrect credentials (extends UC-AUTH-03 Alternative Flow A1).
2. System increments `failed_login_attempts`.
3. System checks whether the new count equals or exceeds `security.max_attempts`.
4. If threshold reached: System sets `locked_until = now() + security.lockout_duration_minutes`.
5. System displays: "Your account has been locked due to too many failed attempts. Try again after [time] or reset your password."
6. System writes an audit log entry for the lockout event.
7. Subsequent login attempts are blocked until `locked_until` has passed (extends to UC-AUTH-10).

**Alternative Flows:**
- **A1 – User resets password while locked:** Lockout is cleared immediately (UC-AUTH-05, step 13).

---

### UC-AUTH-10: Auto Unlock Account (After Lockout Duration)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-AUTH-10 |
| **Use Case Name** | Auto Unlock Account |
| **Actor(s)** | System |
| **Description** | The system automatically unlocks a locked account once the configured lockout duration has elapsed. No admin intervention is required. |
| **Preconditions** | A user account has `locked_until` set (UC-AUTH-09). Current time is equal to or later than `locked_until`. |
| **Postconditions** | `locked_until` is cleared (set to `null`). `failed_login_attempts` is reset to 0. The user may attempt to log in again. |
| **Priority** | Must Have |
| **URS Refs** | URS-A-SET-02 |
| **PRD Refs** | AUTH-05, AUTH-06, SET-SEC-01 |

**Normal Flow:**
1. User attempts to log in after the lockout duration has elapsed.
2. System checks `locked_until` at the point of login.
3. System determines that `now() >= locked_until`.
4. System clears `locked_until` and resets `failed_login_attempts` to 0.
5. System proceeds with normal credential validation (UC-AUTH-03).

---

## 2. Homestay Management Module

---

### UC-HS-01: Browse Homestay Units

| Field | Detail |
|---|---|
| **Use Case ID** | UC-HS-01 |
| **Use Case Name** | Browse Homestay Units |
| **Actor(s)** | Guest |
| **Description** | A guest views the publicly available list of all active homestay units, each showing summary information including name, thumbnail image, base price, location, and average rating. |
| **Preconditions** | At least one active (non-deactivated) homestay unit exists in the system. Guest may be authenticated or browsing as a visitor. |
| **Postconditions** | Guest can see all active units and select one to view further details. |
| **Priority** | Must Have |
| **URS Refs** | URS-U-BK-01 |
| **PRD Refs** | HS-05 |

**Normal Flow:**
1. Guest navigates to the homestay listing page.
2. System queries all units where `is_active = true`.
3. System displays each unit as a card with: name, primary thumbnail image, base price per night, location, and average rating (from visible feedbacks).
4. Guest scrolls through and selects a unit.
5. System navigates to the Unit Details page (UC-HS-02).

**Alternative Flows:**
- **A1 – No active units exist:** System displays an informational message ("No homestay units are currently available.").

---

### UC-HS-02: View Unit Details & Availability

| Field | Detail |
|---|---|
| **Use Case ID** | UC-HS-02 |
| **Use Case Name** | View Unit Details & Availability |
| **Actor(s)** | Guest |
| **Description** | A guest views the full detail page for a selected homestay unit, including description, all images, pricing, check-in/check-out times, house policies, and a real-time availability calendar. |
| **Preconditions** | Guest has selected a unit from the listing (UC-HS-01). The unit is active. |
| **Postconditions** | Guest has sufficient information to decide whether to proceed with a booking. |
| **Priority** | Must Have |
| **URS Refs** | URS-U-BK-01, URS-U-POL-01 |
| **PRD Refs** | HS-05, HS-11 |

**Normal Flow:**
1. Guest clicks on a unit card from the listing.
2. System loads the unit detail page with: name, all images (gallery), full description, location, base price, deposit amount, default check-in/check-out times, house policies, and average rating with reviews.
3. System renders the availability calendar showing: available dates (bookable), booked dates (confirmed), blocked dates (admin-blocked), and held/pending dates.
4. Guest reviews information and can proceed to booking via "Book Now" button.

---

### UC-HS-03: View House Policies

| Field | Detail |
|---|---|
| **Use Case ID** | UC-HS-03 |
| **Use Case Name** | View House Policies |
| **Actor(s)** | Guest |
| **Description** | A guest views the specific house rules and policies for a homestay unit before making a booking. Policies are displayed on the unit detail page. |
| **Preconditions** | The unit has at least one active policy configured. |
| **Postconditions** | Guest is informed of all house rules they must follow during their stay (e.g., No Smoking, No Pets, No Durians). |
| **Priority** | Must Have |
| **URS Refs** | URS-U-POL-01 |
| **PRD Refs** | HS-11 |

**Normal Flow:**
1. Guest is viewing the unit detail page (UC-HS-02).
2. System retrieves and displays all active policies associated with the unit from the `homestay_policies` table.
3. Policies are listed clearly in the House Rules section.
4. Guest reads the policies before proceeding to book.

---

### UC-HS-04: Create Homestay Unit

| Field | Detail |
|---|---|
| **Use Case ID** | UC-HS-04 |
| **Use Case Name** | Create Homestay Unit |
| **Actor(s)** | Admin |
| **Description** | Admin creates a new homestay unit with all required configuration details. On creation, the system automatically applies the system-level default house policies to the new unit. The unit becomes immediately visible to guests after creation. |
| **Preconditions** | Admin is logged in with the `homestay.create` permission. |
| **Postconditions** | A new `homestays` record is created with `is_active = true`. System-level default policies are copied to the unit's `homestay_policies` table. Unit appears on the guest listing page. |
| **Priority** | Must Have |
| **URS Refs** | URS-A-HS-01, URS-A-HS-05, URS-A-HS-06, URS-A-HS-08 |
| **PRD Refs** | HS-01, HS-04, HS-06, HS-07, HS-08, HS-10 |

**Normal Flow:**
1. Admin navigates to Homestay Management → Create New Unit.
2. Admin fills in: unit name, description, location/address, base price per night, deposit amount.
3. Admin sets default check-in time and check-out time.
4. Admin uploads one or more images (minimum one recommended).
5. Admin optionally sets a custom `extension_payment_window_minutes` (overrides system default for this unit).
6. Admin submits the form.
7. System validates all required fields.
8. System saves the unit record.
9. System automatically copies all active system-level default policies (e.g., No Pets, No Durians, No Smoking) to the unit's policy list (includes UC-HS-10).
10. System writes an audit log entry.
11. System displays a success message. Unit is visible on the guest listing.

**Alternative Flows:**
- **A1 – Required field missing:** System highlights missing fields. Flow returns to step 2.
- **A2 – Uploaded file is not a valid image:** System rejects the file and prompts for a valid format.

**Exception Flows:**
- **E1 – Image storage failure:** System saves the unit record but displays a warning that image upload failed. Admin can retry image upload via edit.

---

### UC-HS-05: Edit Homestay Unit

| Field | Detail |
|---|---|
| **Use Case ID** | UC-HS-05 |
| **Use Case Name** | Edit Homestay Unit |
| **Actor(s)** | Admin |
| **Description** | Admin updates the details of an existing homestay unit. All changes take effect immediately system-wide, including on the guest-facing listing. |
| **Preconditions** | The unit exists. Admin is logged in with the `homestay.edit` permission. |
| **Postconditions** | The `homestays` record is updated. Guest listing reflects the new information. An audit log entry is created. |
| **Priority** | Must Have |
| **URS Refs** | URS-A-HS-02, URS-A-HS-05, URS-A-HS-06 |
| **PRD Refs** | HS-02, HS-04, HS-07, HS-08 |

**Normal Flow:**
1. Admin navigates to Homestay Management → select unit → Edit.
2. System pre-populates the form with current unit values.
3. Admin modifies one or more fields (name, description, pricing, times, images, extension window).
4. Admin submits the form.
5. System validates changes.
6. System updates the record and writes an audit log entry.
7. System displays a success message.

**Alternative Flows:**
- **A1 – Validation failure:** System highlights invalid fields and retains entered values.

---

### UC-HS-06: Deactivate / Delete Homestay Unit

| Field | Detail |
|---|---|
| **Use Case ID** | UC-HS-06 |
| **Use Case Name** | Deactivate / Delete Homestay Unit |
| **Actor(s)** | Admin |
| **Description** | Admin deactivates or soft-deletes a homestay unit to prevent new bookings. The unit is hidden from the guest listing. Existing confirmed bookings are unaffected. |
| **Preconditions** | Admin is logged in with `homestay.delete` permission. The unit has no confirmed future bookings. |
| **Postconditions** | `is_active = false` (or soft-deleted). Unit no longer appears on the guest listing. Existing data is retained. |
| **Priority** | Must Have |
| **URS Refs** | URS-A-HS-03 |
| **PRD Refs** | HS-03 |

**Normal Flow:**
1. Admin navigates to the unit's management page.
2. Admin clicks "Deactivate" or "Delete."
3. System checks for any confirmed future bookings on this unit.
4. System prompts for confirmation.
5. Admin confirms.
6. System sets `is_active = false` and writes an audit log entry.
7. System displays a success message. Unit disappears from guest listing.

**Alternative Flows:**
- **A1 – Unit has confirmed future bookings:** System blocks deactivation and displays a warning listing conflicting bookings. Admin must cancel or reassign those bookings before deactivating.

**Exception Flows:**
- **E1 – Admin cancels confirmation prompt:** No changes are made.

---

### UC-HS-07: View All Homestay Units (Admin)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-HS-07 |
| **Use Case Name** | View All Homestay Units (Admin) |
| **Actor(s)** | Admin |
| **Description** | Admin views a paginated, searchable list of all homestay units (active and inactive), with their status, upcoming booking count, and quick-action links. |
| **Preconditions** | Admin is logged in with homestay management access. |
| **Postconditions** | Admin has a complete operational view of all managed properties. |
| **Priority** | Must Have |
| **URS Refs** | URS-A-HS-04 |
| **PRD Refs** | HS-05 |

**Normal Flow:**
1. Admin navigates to Homestay Management.
2. System retrieves all `homestays` records including inactive ones.
3. System displays a table/list with: unit name, status (active/inactive), number of upcoming confirmed bookings, base price, and action buttons (Edit, Deactivate, Manage Policies).
4. Admin can search or filter by status.

---

### UC-HS-08: Manage Unit House Policies

| Field | Detail |
|---|---|
| **Use Case ID** | UC-HS-08 |
| **Use Case Name** | Manage Unit House Policies |
| **Actor(s)** | Admin |
| **Description** | Admin adds, edits, or removes individual house policy entries for a specific homestay unit. Changes are reflected immediately for guests viewing the unit detail page. |
| **Preconditions** | The unit exists. Admin has `homestay.edit` permission. |
| **Postconditions** | The `homestay_policies` table is updated. Guest-facing policy list on the unit page reflects the new state. |
| **Priority** | Must Have |
| **URS Refs** | URS-A-HS-07 |
| **PRD Refs** | HS-09, HS-10 |

**Normal Flow:**
1. Admin navigates to the unit's detail → "Manage House Policies."
2. System displays the current list of policies for this unit.
3. Admin can:
   - **Add:** Enter a new policy description and save.
   - **Edit:** Modify an existing policy text and save.
   - **Remove:** Click delete on a policy; system confirms before removal.
4. System updates the `homestay_policies` records.
5. Changes are immediately visible to guests.

---

### UC-HS-09: Set Pricing & Check-in/out Times

| Field | Detail |
|---|---|
| **Use Case ID** | UC-HS-09 |
| **Use Case Name** | Set Pricing & Check-in/out Times |
| **Actor(s)** | Admin |
| **Description** | Admin configures the base nightly price, deposit amount, default check-in time, and default check-out time for a specific homestay unit. These values are used in booking fee calculations and availability scheduling. |
| **Preconditions** | Admin is on the unit create/edit form (UC-HS-04 or UC-HS-05). |
| **Postconditions** | Pricing and timing values are saved to the `homestays` record and applied to new bookings on this unit. |
| **Priority** | Must Have |
| **URS Refs** | URS-A-HS-05 |
| **PRD Refs** | HS-04, HS-07, HS-08 |

**Normal Flow:**
1. Admin enters or updates: base price per night, deposit amount, default check-in time (HH:MM), default check-out time (HH:MM).
2. System validates that prices are positive numeric values and times are valid.
3. System saves values as part of the unit record (UC-HS-04 or UC-HS-05).

**Alternative Flows:**
- **A1 – Invalid price (negative or zero):** System highlights the field with a validation error.
- **A2 – Check-out time is before check-in time:** System displays a time validation warning.

---

### UC-HS-10: Apply Default Policies on Unit Creation

| Field | Detail |
|---|---|
| **Use Case ID** | UC-HS-10 |
| **Use Case Name** | Apply Default Policies on Unit Creation |
| **Actor(s)** | System |
| **Description** | When a new homestay unit is created (UC-HS-04), the system automatically copies all active system-level default policies from the `settings` / `system_policies` configuration into the new unit's `homestay_policies` list. This ensures every unit starts with a consistent baseline of rules without manual admin effort. |
| **Preconditions** | System default policies are configured in System Settings (UC-SET-08). At least one default policy exists. |
| **Postconditions** | The new unit's `homestay_policies` records are pre-populated with copies of the current system defaults. Admin can subsequently add, edit, or remove these per-unit policies independently. |
| **Priority** | Must Have |
| **URS Refs** | URS-A-HS-08 |
| **PRD Refs** | HS-10, HS-12, SET-POL-01, SET-POL-02 |

**Normal Flow:**
1. Admin completes unit creation form and submits (UC-HS-04, step 8).
2. System retrieves all active system-level default policies.
3. System creates a copy of each default policy as a `homestay_policies` record linked to the new unit.
4. Admin can view and modify these inherited policies via UC-HS-08.

---

## 3. Booking Module

---

### UC-BK-01: View Availability Calendar

| Field | Detail |
|---|---|
| **Use Case ID** | UC-BK-01 |
| **Use Case Name** | View Availability Calendar |
| **Actor(s)** | Guest |
| **Description** | Guest views a monthly availability calendar for a specific homestay unit, with date cells colour-coded to indicate available, confirmed-booked, temporarily held (pending payment), and admin-blocked statuses. |
| **Preconditions** | Guest has selected a unit from the listing or unit detail page. |
| **Postconditions** | Guest has a visual overview of which dates can and cannot be selected for booking. |
| **Priority** | Must Have |
| **URS Refs** | URS-U-BK-02 |
| **PRD Refs** | BK-U-01, BK-U-02 |

**Normal Flow:**
1. Guest is on the unit detail page or booking form.
2. System queries `bookings`, `blocked_dates`, and any temporary holds for the unit.
3. System renders the calendar with colour-coded date states:
   - **Green / default:** Available to book.
   - **Red / strikethrough:** Confirmed booking or admin-blocked.
   - **Yellow / muted:** Temporary hold (pending payment window active).
4. Guest can navigate between months.

---

### UC-BK-02: Select Check-in / Check-out Date & Time

| Field | Detail |
|---|---|
| **Use Case ID** | UC-BK-02 |
| **Use Case Name** | Select Check-in / Check-out Date & Time |
| **Actor(s)** | Guest |
| **Description** | Guest selects their desired check-in and check-out dates from the availability calendar and adjusts times if the unit allows flexible check-in/out. The system verifies availability in real time upon selection. |
| **Preconditions** | Guest is on the booking form for an active unit. |
| **Postconditions** | Selected dates and times are validated and passed to the booking summary for review before submission. |
| **Priority** | Must Have |
| **URS Refs** | URS-U-BK-03, URS-U-BK-04 |
| **PRD Refs** | BK-U-02, BK-U-03 |

**Normal Flow:**
1. Guest clicks a check-in date on the availability calendar.
2. System highlights the selected check-in date.
3. Guest clicks a check-out date.
4. System triggers a real-time availability check for the selected range (includes UC-BK-03).
5. System confirms availability and updates the booking summary with total nights and estimated cost.
6. Guest optionally adjusts check-in/check-out times if the unit supports it.

**Alternative Flows:**
- **A1 – Selected date range includes an unavailable date:** System highlights the conflict and displays "Selected dates are not available." Guest must re-select. (UC-BK-03 exception surface.)
- **A2 – Check-out date before check-in date:** System shows a validation error and prompts re-selection.
- **A3 – Single night minimum not met:** System shows the minimum stay requirement.

---

### UC-BK-03: Check Date Availability (Real-time)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-BK-03 |
| **Use Case Name** | Check Date Availability (Real-time) |
| **Actor(s)** | System |
| **Description** | The system performs a real-time database check to confirm that no confirmed bookings, pending/held bookings, or admin-blocked dates overlap with the guest's selected date range for the target unit. This is an «include» of UC-BK-02 and UC-BK-11. |
| **Preconditions** | Guest has entered a check-in and check-out date and unit is identified. |
| **Postconditions** | System returns "available" (booking can proceed) or "unavailable" (conflict found, with details). |
| **Priority** | Must Have |
| **URS Refs** | URS-U-BK-04, URS-SYS-04 |
| **PRD Refs** | BK-U-03, BK-U-04 |

**Normal Flow:**
1. System receives the unit ID, check-in date/time, and check-out date/time.
2. System queries `bookings` for any records on the same unit with status `confirmed` or `pending_payment` that overlap the requested range.
3. System queries `blocked_dates` for any admin-blocked entries that fall within the range.
4. If no conflicts found: System returns availability confirmation.
5. If conflict found: System returns unavailable with the conflicting range.

**Exception Flows:**
- **E1 – Database timeout during check:** System returns an error state. Booking form displays "Unable to verify availability. Please try again."

---

### UC-BK-04: Submit Booking

| Field | Detail |
|---|---|
| **Use Case ID** | UC-BK-04 |
| **Use Case Name** | Submit Booking |
| **Actor(s)** | Guest |
| **Description** | Guest submits a booking request for a selected unit and date range. The system creates a booking in `pending_payment` status and applies a 1-day payment hold window. A bill is auto-generated. If payment is not received within the configured window, the booking is auto-cancelled. |
| **Preconditions** | Guest is logged in. Date range has been confirmed as available (UC-BK-03). Guest is on the booking summary/confirmation page. |
| **Postconditions** | A `bookings` record is created with status `pending_payment`. A `billing` record is auto-generated. A 1-day payment deadline is set. Guest receives a booking confirmation notification directing them to pay. |
| **Priority** | Must Have |
| **URS Refs** | URS-U-BK-05 |
| **PRD Refs** | BK-U-05, BK-U-06, BK-H-01 |

**Normal Flow:**
1. Guest reviews the booking summary (unit, dates, times, total cost including deposit).
2. Guest clicks "Confirm Booking."
3. System re-validates availability (to prevent race conditions).
4. System creates a `bookings` record: status `pending_payment`, `payment_deadline = now() + 1 day`.
5. System auto-generates a bill number and `billing` record (includes UC-PAY-06).
6. System sends an in-app and email notification to the guest with the bill and payment deadline.
7. System redirects the guest to the payment page.

**Alternative Flows:**
- **A1 – Dates became unavailable between selection and submission:** System displays a conflict message. Guest must re-select dates.

**Exception Flows:**
- **E1 – Database error during booking creation:** No record is created. System displays an error and allows retry.

---

### UC-BK-05: Temporary Hold (1-Day Payment Window)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-BK-05 |
| **Use Case Name** | Temporary Hold (1-Day Payment Window) |
| **Actor(s)** | System |
| **Description** | Upon booking submission, the system places a temporary hold on the selected dates by setting the booking status to `pending_payment` with a deadline. This prevents other guests from booking the same dates during the payment window. This is an «include» of UC-BK-04. |
| **Preconditions** | A booking record has been created with `pending_payment` status. |
| **Postconditions** | Dates are treated as unavailable on the availability calendar during the hold period. The booking's `payment_deadline` is set to `now() + 1 day`. |
| **Priority** | Must Have |
| **URS Refs** | URS-U-BK-04 |
| **PRD Refs** | BK-H-01, BK-H-02 |

**Normal Flow:**
1. Booking is created in `pending_payment` status (UC-BK-04, step 4).
2. System marks the dates as held in availability queries.
3. System schedules an auto-cancellation job if payment is not received before `payment_deadline` (extends to UC-BK-06).

---

### UC-BK-06: Auto-Cancel Booking (Payment Timeout)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-BK-06 |
| **Use Case Name** | Auto-Cancel Booking (Payment Timeout) |
| **Actor(s)** | System |
| **Description** | A scheduled job checks all `pending_payment` bookings. If the `payment_deadline` has passed without a confirmed payment, the booking is automatically cancelled, the held dates are released, and the guest is notified. |
| **Preconditions** | `bookings.status = pending_payment` and `payment_deadline < now()`. |
| **Postconditions** | Booking status set to `cancelled`. Dates are released and available for new bookings. Guest receives a cancellation notification. An audit log entry is written. |
| **Priority** | Must Have |
| **URS Refs** | URS-U-BK-04 |
| **PRD Refs** | BK-H-01, BK-H-02, BK-H-03 |

**Normal Flow:**
1. Scheduled job runs hourly (or at configured interval).
2. System queries all bookings where `status = pending_payment` AND `payment_deadline < now()`.
3. For each such booking: system sets `status = cancelled`.
4. System releases the held dates.
5. System sends a cancellation notification to the guest (in-app and email).
6. System writes an audit log entry.

---

### UC-BK-07: View Current Bookings (Guest)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-BK-07 |
| **Use Case Name** | View Current Bookings |
| **Actor(s)** | Guest |
| **Description** | Guest views a list of all their active and upcoming bookings (status: `confirmed` or `pending_payment`), with brief summary and quick access to booking details. |
| **Preconditions** | Guest is logged in and has at least one non-historical booking. |
| **Postconditions** | Guest can see all current reservation statuses and navigate to detailed views. |
| **Priority** | Must Have |
| **URS Refs** | URS-U-BK-06 |
| **PRD Refs** | BK-U-05 |

**Normal Flow:**
1. Guest navigates to "My Bookings."
2. System retrieves all bookings for the logged-in user where status is `confirmed` or `pending_payment`.
3. System displays each booking as a card or list item: unit name, check-in/check-out dates, status badge, total cost.
4. Guest clicks a booking to view full details (UC-BK-09).

---

### UC-BK-08: View Booking History (Guest)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-BK-08 |
| **Use Case Name** | View Booking History |
| **Actor(s)** | Guest |
| **Description** | Guest views all past bookings (status: `completed` or `cancelled`). From completed bookings, the guest can access receipts and submit feedback. |
| **Preconditions** | Guest is logged in. |
| **Postconditions** | Guest can review all historical stays and take follow-up actions (download receipt, leave review). |
| **Priority** | Should Have |
| **URS Refs** | URS-U-BK-07 |
| **PRD Refs** | BK-U-06 |

**Normal Flow:**
1. Guest navigates to "My Bookings" → "History" tab.
2. System retrieves bookings where status is `completed` or `cancelled`.
3. System displays each booking with: unit name, dates, status, amount paid.
4. For `completed` bookings: "View Receipt" and "Leave a Review" (if not yet submitted) action links are displayed.

---

### UC-BK-09: View Booking Details (Guest)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-BK-09 |
| **Use Case Name** | View Booking Details |
| **Actor(s)** | Guest |
| **Description** | Guest views the full detail record of a specific booking, including unit information, dates/times, booking status, payment status, QR code (if confirmed), and any applicable extension history. |
| **Preconditions** | The booking belongs to the logged-in guest. |
| **Postconditions** | Guest has full visibility of their reservation state. |
| **Priority** | Must Have |
| **URS Refs** | URS-U-BK-08 |
| **PRD Refs** | BK-U-06 |

**Normal Flow:**
1. Guest clicks on a booking from their list.
2. System retrieves the full booking record and related data (unit, billing, QR code, extension records if any).
3. System displays: unit name & thumbnail, check-in/check-out date and time, total amount, payment status, booking status, and QR code (if status is `confirmed`).
4. Guest can download bill or receipt from this page.

---

### UC-BK-10: Cancel Booking (Guest)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-BK-10 |
| **Use Case Name** | Cancel Booking |
| **Actor(s)** | Guest |
| **Description** | Guest cancels a confirmed or pending booking. The system applies the configured refund policy tiers based on how far in advance the cancellation is made and triggers a refund if applicable. |
| **Preconditions** | Guest has an active booking (status `confirmed` or `pending_payment`). The booking is cancellable (not already checked in). |
| **Postconditions** | Booking status set to `cancelled`. Dates are released. Refund (if applicable) is initiated and recorded. Guest and admin are notified. |
| **Priority** | Must Have |
| **URS Refs** | URS-U-BK-09, URS-U-BK-10 |
| **PRD Refs** | BK-U-07, BK-U-08 |

**Normal Flow:**
1. Guest navigates to the booking detail page.
2. Guest clicks "Cancel Booking."
3. System calculates the applicable refund amount based on the configured cancellation policy tiers (e.g., >2 weeks: 100%; 1–2 weeks: 50%; <1 week: 0%).
4. System displays the cancellation confirmation dialog: "You will receive a refund of [amount]. This action cannot be undone."
5. Guest clicks "Confirm Cancellation."
6. System sets booking status to `cancelled` and releases the dates.
7. System records the refund amount and triggers the refund process via the payment gateway (if applicable).
8. System sends cancellation notifications (in-app and email) to guest and admin.
9. System writes an audit log entry.

**Alternative Flows:**
- **A1 – Guest cancels the confirmation dialog:** No changes are made.
- **A2 – Booking in `pending_payment` status:** No payment was made; no refund is issued. Booking is simply cancelled.

**Exception Flows:**
- **E1 – Refund processing failure at payment gateway:** System records the cancellation but flags the refund as `pending_manual`. Admin is notified to process manually.

---

### UC-BK-11: View Cancellation Policy & Refund Info

| Field | Detail |
|---|---|
| **Use Case ID** | UC-BK-11 |
| **Use Case Name** | View Cancellation Policy & Refund Info |
| **Actor(s)** | Guest |
| **Description** | Before confirming a cancellation, the guest is shown the applicable cancellation policy and the exact refund amount they will receive. This is an «include» of UC-BK-10. |
| **Preconditions** | Guest has initiated the cancellation process (UC-BK-10, step 2). Cancellation policy is configured in System Settings. |
| **Postconditions** | Guest makes an informed decision whether to proceed with the cancellation. |
| **Priority** | Must Have |
| **URS Refs** | URS-U-BK-10 |
| **PRD Refs** | BK-U-08 |

**Normal Flow:**
1. System retrieves current cancellation policy tiers from `settings`.
2. System calculates the number of days until check-in.
3. System applies the matching tier and computes the refund amount.
4. System displays: policy summary, days until check-in, refund percentage, and exact refund amount.

---

### UC-BK-12: View Booking Calendar (Admin)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-BK-12 |
| **Use Case Name** | View Booking Calendar (Admin) |
| **Actor(s)** | Admin |
| **Description** | Admin views an all-unit booking calendar showing all bookings across all homestay units. The calendar displays confirmed, pending, completed, cancelled, and blocked date entries to provide a complete operational picture. |
| **Preconditions** | Admin is logged in with booking management access. |
| **Postconditions** | Admin has a comprehensive visual overview of all reservations across all units. |
| **Priority** | Must Have |
| **URS Refs** | URS-A-BK-01 |
| **PRD Refs** | BK-A-01 |

**Normal Flow:**
1. Admin navigates to the Booking Calendar.
2. System retrieves all bookings and blocked dates across all units.
3. System renders a calendar (monthly or weekly view) with colour-coded entries per unit per status.
4. Admin can filter by unit or status.
5. Admin clicks an entry to view booking details or quick-edit.

---

### UC-BK-13: Create Booking on Behalf of User (Admin)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-BK-13 |
| **Use Case Name** | Create Booking on Behalf of User |
| **Actor(s)** | Admin |
| **Description** | Admin creates a new booking for a specified user and homestay unit directly from the admin panel, bypassing the online payment flow (e.g., for walk-in guests or phone bookings). The system applies the same availability and hold rules. |
| **Preconditions** | Admin has `booking.create` permission. The target user account exists. The selected dates are available. |
| **Postconditions** | A `bookings` record is created. A bill is generated. The booking follows the normal payment and confirmation lifecycle. Guest is notified. |
| **Priority** | Must Have |
| **URS Refs** | URS-A-BK-02 |
| **PRD Refs** | BK-A-02 |

**Normal Flow:**
1. Admin navigates to Bookings → Create Booking.
2. Admin selects the target user account.
3. Admin selects the homestay unit and desired check-in/check-out dates and times.
4. System performs a real-time availability check (UC-BK-03).
5. System displays the booking summary and total cost.
6. Admin confirms the booking.
7. System creates the booking record (`pending_payment` status), auto-generates the bill, and notifies the guest to pay.

**Alternative Flows:**
- **A1 – Date range unavailable:** System displays conflict and prompts admin to select different dates.

---

### UC-BK-14: Edit Booking (Admin)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-BK-14 |
| **Use Case Name** | Edit Booking |
| **Actor(s)** | Admin |
| **Description** | Admin modifies the details of an existing booking (e.g., adjusting dates, updating notes). Changes to dates trigger a re-availability check. |
| **Preconditions** | Booking exists and is in a modifiable state (`pending_payment` or `confirmed`). Admin has `booking.edit` permission. |
| **Postconditions** | Booking record is updated. If dates changed, the old dates are released and new dates are held. An audit log entry is created. |
| **Priority** | Must Have |
| **URS Refs** | URS-A-BK-03 |
| **PRD Refs** | BK-A-04 |

**Normal Flow:**
1. Admin navigates to the booking detail page and clicks "Edit."
2. Admin modifies desired fields.
3. If dates are changed: system checks availability for new dates (UC-BK-03).
4. Admin submits the changes.
5. System updates the record and writes an audit log entry.
6. System notifies the guest of the change.

**Alternative Flows:**
- **A1 – New dates are unavailable:** System blocks the change and displays the conflict.

---

### UC-BK-15: Delete Booking (Admin)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-BK-15 |
| **Use Case Name** | Delete Booking |
| **Actor(s)** | Admin |
| **Description** | Admin permanently removes a booking record from the system. This is a destructive action and should only be used for erroneous bookings. Confirmed bookings with payments should use cancellation instead. |
| **Preconditions** | Admin has `booking.delete` permission. |
| **Postconditions** | Booking record is permanently deleted. Dates are released. Guest is notified. Audit log entry is created. |
| **Priority** | Must Have |
| **URS Refs** | URS-A-BK-03 |
| **PRD Refs** | BK-A-04 |

**Normal Flow:**
1. Admin navigates to the booking detail and clicks "Delete."
2. System displays a confirmation prompt warning that this is irreversible.
3. Admin confirms.
4. System deletes the booking record, releases the dates, notifies the guest, and writes an audit log entry.

**Alternative Flows:**
- **A1 – Admin cancels the confirmation:** No changes are made.

---

### UC-BK-16: Cancel Booking on Behalf of User (Admin)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-BK-16 |
| **Use Case Name** | Cancel Booking on Behalf of User |
| **Actor(s)** | Admin |
| **Description** | Admin cancels a guest's booking from the admin panel and applies the appropriate refund policy. This mirrors the guest cancellation flow (UC-BK-10) but is initiated by admin. |
| **Preconditions** | Booking exists and is in a cancellable state. Admin has `booking.cancel` permission. |
| **Postconditions** | Booking status set to `cancelled`. Refund (if applicable) is initiated. Both guest and admin are notified. Audit log entry created. |
| **Priority** | Must Have |
| **URS Refs** | URS-A-BK-04 |
| **PRD Refs** | BK-A-05 |

**Normal Flow:**
1. Admin opens the booking detail page and clicks "Cancel Booking."
2. System calculates and displays the refund amount per configured policy.
3. Admin confirms the cancellation.
4. System cancels the booking, releases dates, triggers refund, and notifies the guest.

---

### UC-BK-17: Filter Booking List (Admin)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-BK-17 |
| **Use Case Name** | Filter Booking List |
| **Actor(s)** | Admin |
| **Description** | Admin filters the system-wide booking list using one or more criteria: booking status, booking ID, check-in date range, and homestay unit. |
| **Preconditions** | Admin is on the Bookings management page. |
| **Postconditions** | The list is filtered to show only matching booking records. |
| **Priority** | Should Have |
| **URS Refs** | URS-A-BK-05 |
| **PRD Refs** | BK-A-06 |

**Normal Flow:**
1. Admin selects one or more filter criteria (status, date range, unit, booking ID).
2. System applies the filters to the bookings query.
3. System re-renders the booking list with matching results.
4. Admin can clear filters to return to the full list.

---

### UC-BK-18: Block Dates (Admin)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-BK-18 |
| **Use Case Name** | Block Dates |
| **Actor(s)** | Admin |
| **Description** | Admin manually blocks one or more specific dates on a homestay unit to prevent any new bookings from being created for those dates (e.g., for maintenance, owner personal use). The reason for blocking is stored internally but hidden from guests. |
| **Preconditions** | Admin has `booking.block` permission. The target dates do not have confirmed bookings. |
| **Postconditions** | Selected dates appear as "unavailable" on the guest-facing availability calendar. `blocked_dates` records are created. Blocked reason is stored but not exposed to guests. |
| **Priority** | Should Have |
| **URS Refs** | URS-A-BK-06, URS-A-BK-07 |
| **PRD Refs** | BK-A-07 |

**Normal Flow:**
1. Admin navigates to the Booking Calendar or unit management page.
2. Admin selects a date range and the target unit.
3. Admin enters an internal reason/note (optional; not shown to guests).
4. Admin clicks "Block Dates."
5. System creates `blocked_dates` records for the selected range and unit.
6. Dates immediately appear unavailable on the guest-facing calendar.

**Alternative Flows:**
- **A1 – Selected dates have confirmed bookings:** System warns admin of the conflict. Admin must cancel existing bookings before blocking.

---

## 4. Payment Module

---

### UC-PAY-01: Make Payment (Online Gateway)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-PAY-01 |
| **Use Case Name** | Make Payment (Online Gateway) |
| **Actor(s)** | Guest, Payment Gateway |
| **Description** | Guest pays the outstanding bill for their booking via the integrated online payment gateway. Payment confirmation is received by the system via webhook, which triggers booking confirmation, QR code generation, and receipt issuance. |
| **Preconditions** | Booking status is `pending_payment`. A `billing` record exists for the booking. The `payment_deadline` has not passed. Payment gateway is configured. |
| **Postconditions** | `payments` record status updated to `succeeded`. Booking status updated to `confirmed`. QR code generated and delivered. Receipt generated. Guest and admin notified. |
| **Priority** | Must Have |
| **URS Refs** | URS-U-PAY-01, URS-U-PAY-02, URS-U-PAY-05 |
| **PRD Refs** | PAY-U-01, PAY-U-02 |

**Normal Flow:**
1. Guest clicks "Pay Now" from the booking detail or bill view.
2. System creates a payment request with the gateway and redirects the guest to the payment page.
3. Guest completes payment on the gateway's hosted page.
4. Gateway processes payment and sends an HTTP webhook to the system (UC-PAY-04).
5. System verifies the webhook signature.
6. System updates the `payments` record to `succeeded`.
7. System updates the booking status to `confirmed`.
8. System generates the guest QR code (UC-QR-01).
9. System generates the payment receipt.
10. System sends booking confirmation notification (in-app and email) to the guest with receipt and QR code.
11. System notifies admin of the new confirmed booking.

**Alternative Flows:**
- **A1 – Payment declined or failed:** Gateway notifies system; `payments` record updated to `failed`. Guest is redirected back with an error message and a "Try Again" option.
- **A2 – Guest abandons the gateway page:** No webhook is received. Booking remains `pending_payment` until deadline.

**Exception Flows:**
- **E1 – Duplicate webhook received:** System detects idempotency key collision and discards the duplicate without re-processing.
- **E2 – Webhook signature verification fails:** System rejects the webhook and logs a security alert.

---

### UC-PAY-02: View Payment Bill

| Field | Detail |
|---|---|
| **Use Case ID** | UC-PAY-02 |
| **Use Case Name** | View Payment Bill |
| **Actor(s)** | Guest |
| **Description** | Guest views the itemised payment bill generated for their booking, showing the breakdown of base cost, deposit, total amount due, payment deadline, and bill number. |
| **Preconditions** | A `billing` record exists for the booking (auto-generated at booking submission). |
| **Postconditions** | Guest can review the full bill before making payment. Guest may optionally download the bill as PDF. |
| **Priority** | Must Have |
| **URS Refs** | URS-U-PAY-02 |
| **PRD Refs** | PAY-U-01 |

**Normal Flow:**
1. Guest navigates to booking detail or clicks the bill link in the notification.
2. System retrieves the associated `billing` record.
3. System renders the bill: bill number, booking reference, unit name, dates, nightly rate, number of nights, deposit, total amount, and payment deadline.
4. Guest can click "Download PDF" to save the bill.

---

### UC-PAY-03: View / Download Payment Receipt

| Field | Detail |
|---|---|
| **Use Case ID** | UC-PAY-03 |
| **Use Case Name** | View / Download Payment Receipt |
| **Actor(s)** | Guest |
| **Description** | Guest views or downloads a PDF receipt for a successfully completed payment. The receipt serves as proof of transaction. |
| **Preconditions** | Payment status is `succeeded`. A receipt has been generated. |
| **Postconditions** | Guest has a downloadable receipt for their records. |
| **Priority** | Must Have |
| **URS Refs** | URS-U-PAY-03 |
| **PRD Refs** | PAY-U-03 |

**Normal Flow:**
1. Guest navigates to their booking detail or payment history.
2. Guest clicks "View Receipt" or "Download Receipt."
3. System retrieves or regenerates the receipt PDF using `barryvdh/laravel-dompdf`.
4. System delivers the PDF to the guest's browser for viewing or download.

---

### UC-PAY-04: View Payment History (Guest)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-PAY-04 |
| **Use Case Name** | View Payment History |
| **Actor(s)** | Guest |
| **Description** | Guest views a chronological list of all their payments with the ability to filter by date range and payment status. From each entry, the guest can access the associated bill or receipt. |
| **Preconditions** | Guest is logged in and has at least one payment record. |
| **Postconditions** | Guest can see all payment transactions. Applied filters narrow the view. |
| **Priority** | Should Have |
| **URS Refs** | URS-U-PAY-04 |
| **PRD Refs** | PAY-U-04 |

**Normal Flow:**
1. Guest navigates to "Payment History."
2. System retrieves all `payments` records for the logged-in user.
3. System displays each payment: payment number, booking reference, date, amount, status.
4. Guest applies optional filters (date range, status).
5. System re-queries and updates the list.
6. Guest clicks an entry to view the corresponding bill or receipt.

---

### UC-PAY-05: Process Payment Webhook

| Field | Detail |
|---|---|
| **Use Case ID** | UC-PAY-05 |
| **Use Case Name** | Process Payment Webhook |
| **Actor(s)** | Payment Gateway, System |
| **Description** | The payment gateway sends an HTTP POST callback to the system's webhook endpoint notifying it of a payment outcome (success, failure, or pending). The system verifies the request signature, identifies the related payment record, and updates statuses accordingly. Processing is idempotent to prevent duplicate state changes. |
| **Preconditions** | A payment was initiated. The gateway has processed it and is sending the notification. |
| **Postconditions** | Payment and booking records are updated. QR code and receipt generation are triggered on success. Audit log entry is created. |
| **Priority** | Must Have |
| **URS Refs** | URS-U-PAY-01 |
| **PRD Refs** | PAY-U-05, PAY-A-04 |

**Normal Flow:**
1. Gateway sends POST request to `/webhook/payment` with payload and signature header.
2. System validates the HMAC/signature against the configured gateway secret.
3. System extracts the payment reference and looks up the corresponding `payments` record.
4. System checks the idempotency key; if already processed, discards without re-processing.
5. System updates the payment status (succeeded / failed / pending).
6. If succeeded: triggers booking confirmation, QR generation, and receipt creation (UC-PAY-01 steps 7–11).
7. System returns HTTP 200 to the gateway.
8. System writes an audit log entry.

**Exception Flows:**
- **E1 – Signature verification fails:** System returns HTTP 400 and logs a security warning. No state is changed.
- **E2 – Payment record not found:** System returns HTTP 404 and logs the event.

---

### UC-PAY-06: Auto-Generate Bill & Payment Number

| Field | Detail |
|---|---|
| **Use Case ID** | UC-PAY-06 |
| **Use Case Name** | Auto-Generate Bill & Payment Number |
| **Actor(s)** | System |
| **Description** | The system automatically generates a unique, sequentially formatted bill number when a booking is submitted, and a payment number when a payment record is created. Admin does not need to assign numbers manually. |
| **Preconditions** | A new booking is being submitted (UC-BK-04) or a payment record is being created (UC-PAY-01). |
| **Postconditions** | `billing.bill_number` and `payments.payment_number` are populated with unique, formatted identifiers. |
| **Priority** | Must Have |
| **URS Refs** | URS-A-PAY-01 |
| **PRD Refs** | PAY-A-01 |

**Normal Flow:**
1. System detects a new `billing` or `payments` record is being inserted.
2. System generates a formatted reference number (e.g., `BILL-2026-0001`, `PAY-2026-0001`) using an auto-increment or sequential scheme.
3. System assigns the number to the record before saving.

---

### UC-PAY-07: View Billing List (Admin)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-PAY-07 |
| **Use Case Name** | View Billing List |
| **Actor(s)** | Admin |
| **Description** | Admin views all billing records system-wide, with the ability to filter by bill number and date range. From this view, admin can access individual bills and regenerate them. |
| **Preconditions** | Admin has billing management access. |
| **Postconditions** | Admin can see all bills with status, amounts, and related booking references. |
| **Priority** | Should Have |
| **URS Refs** | URS-A-PAY-02 |
| **PRD Refs** | PAY-A-02 |

**Normal Flow:**
1. Admin navigates to Payment Management → Billing List.
2. System retrieves all `billing` records.
3. System displays: bill number, booking reference, guest name, unit, amount, status, date generated.
4. Admin applies filters (bill number search, date range).
5. Admin clicks a bill to view details or regenerate (UC-PAY-09).

---

### UC-PAY-08: View Payment List (Admin)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-PAY-08 |
| **Use Case Name** | View Payment List |
| **Actor(s)** | Admin |
| **Description** | Admin views all payment records system-wide with filtering by payment number, date range, and payment status. |
| **Preconditions** | Admin has payment management access. |
| **Postconditions** | Admin can review all payment transactions and their current statuses. |
| **Priority** | Should Have |
| **URS Refs** | URS-A-PAY-03 |
| **PRD Refs** | PAY-A-03 |

**Normal Flow:**
1. Admin navigates to Payment Management → Payment List.
2. System retrieves all `payments` records.
3. System displays: payment number, related bill, guest, amount, status (succeeded/failed/pending), date.
4. Admin applies filters.
5. Admin clicks a payment to view receipt or details.

---

### UC-PAY-09: Regenerate Bill / Receipt (Admin)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-PAY-09 |
| **Use Case Name** | Regenerate Bill / Receipt |
| **Actor(s)** | Admin |
| **Description** | Admin regenerates a bill or payment receipt PDF on demand. This is useful when the original document is lost, or when changes require a fresh document. |
| **Preconditions** | The booking and billing/payment records exist. Admin has appropriate permission. |
| **Postconditions** | A new PDF is generated and made available for download or resending to the guest. |
| **Priority** | Should Have |
| **URS Refs** | URS-A-PAY-04 |
| **PRD Refs** | PAY-A-04, PAY-A-05 |

**Normal Flow:**
1. Admin navigates to the billing or payment detail page.
2. Admin clicks "Regenerate Bill" or "Regenerate Receipt."
3. System re-renders the document using the latest booking and payment data via `barryvdh/laravel-dompdf`.
4. System makes the new PDF available for download and optionally resends it to the guest via email.

---

## 5. Notification Module

---

### UC-NOTIF-01: Receive In-App Notification

| Field | Detail |
|---|---|
| **Use Case ID** | UC-NOTIF-01 |
| **Use Case Name** | Receive In-App Notification |
| **Actor(s)** | Guest, Admin |
| **Description** | Users receive real-time in-app notifications via the bell icon in the navigation bar for all relevant system events (e.g., booking confirmed, payment received, extension bill issued, QR code reminder). Unread count badge updates in real time. |
| **Preconditions** | User is logged in. A notifiable system event has occurred. |
| **Postconditions** | A new notification record is created in the `notifications` table. The user's bell icon badge count increments. The notification is listed in the notifications panel. |
| **Priority** | Must Have |
| **URS Refs** | URS-U-NOTIF-01, URS-A-NOTIF-01 |
| **PRD Refs** | NOTIF-01, NOTIF-02 |

**Normal Flow:**
1. A triggering event occurs (e.g., booking confirmed, payment received).
2. System creates a `notifications` record for the target user.
3. System broadcasts a notification event via Laravel Reverb (WebSocket).
4. If the user is online: the bell icon badge increments in real time without a page refresh.
5. User clicks the bell icon to open the notifications panel.
6. System displays the list of notifications (read and unread) in reverse chronological order.
7. Clicking a notification marks it as read and navigates to the relevant entity (e.g., booking detail).

---

### UC-NOTIF-02: Receive Email Notification

| Field | Detail |
|---|---|
| **Use Case ID** | UC-NOTIF-02 |
| **Use Case Name** | Receive Email Notification |
| **Actor(s)** | Guest, Admin |
| **Description** | The system sends transactional emails for key events to users' registered email addresses. Email delivery is enabled/disabled by the `notification.email_enabled` system setting. SMTP configuration must be set in System Settings. |
| **Preconditions** | SMTP is configured (UC-SET-01). `notification.email_enabled = true`. A notifiable event has occurred. |
| **Postconditions** | An email is dispatched via the configured SMTP server and delivered to the user's inbox. |
| **Priority** | Must Have |
| **URS Refs** | URS-U-NOTIF-01, URS-A-NOTIF-01 |
| **PRD Refs** | NOTIF-02, SET-GEN-04 |

**Normal Flow:**
1. A triggering event occurs (booking confirmation, payment receipt, cancellation, extension bill, etc.).
2. System checks `notification.email_enabled`.
3. System queues an email notification job via Laravel Queues.
4. Queue worker dispatches the email via the configured SMTP server.
5. Email arrives in the recipient's inbox.

**Alternative Flows:**
- **A1 – Email notifications globally disabled:** System skips email sending; in-app notification is still created.

**Exception Flows:**
- **E1 – SMTP server unreachable or authentication failure:** Email job fails. System retries per queue configuration. After max retries, logs the failure. No in-app notification impact.

---

### UC-NOTIF-03: Receive Payment Reminder

| Field | Detail |
|---|---|
| **Use Case ID** | UC-NOTIF-03 |
| **Use Case Name** | Receive Payment Reminder |
| **Actor(s)** | Guest |
| **Description** | The system automatically sends a reminder to guests who have a booking in `pending_payment` status and whose payment deadline is approaching, prompting them to complete payment before auto-cancellation. |
| **Preconditions** | Booking status is `pending_payment`. The payment deadline has not yet passed. The reminder schedule threshold has been reached. |
| **Postconditions** | Guest receives both an in-app and email reminder notification linking to the payment page. |
| **Priority** | Must Have |
| **URS Refs** | URS-U-PAY-06 |
| **PRD Refs** | NOTIF-04 |

**Normal Flow:**
1. Scheduled job runs daily (or at configured interval).
2. System queries bookings where `status = pending_payment` and `payment_deadline` is within the next N hours (configurable).
3. For each matching booking: system sends an in-app notification and email to the guest.
4. Notification includes: booking reference, amount due, payment deadline, and direct payment link.

---

### UC-NOTIF-04: Receive Check-in / Check-out Reminder

| Field | Detail |
|---|---|
| **Use Case ID** | UC-NOTIF-04 |
| **Use Case Name** | Receive Check-in / Check-out Reminder |
| **Actor(s)** | Guest, Admin |
| **Description** | The system sends upcoming booking reminders to guests (check-in date approaching) and admin (both check-in and check-out upcoming) to help all parties prepare for the stay. |
| **Preconditions** | Booking status is `confirmed`. The check-in or check-out date is within the configured reminder window (e.g., 1 day before). |
| **Postconditions** | Guest and admin each receive timely reminders via in-app and email notifications. |
| **Priority** | Should Have |
| **URS Refs** | URS-U-NOTIF-02, URS-A-NOTIF-02 |
| **PRD Refs** | NOTIF-03 |

**Normal Flow:**
1. Scheduled daily job runs.
2. System queries confirmed bookings where check-in or check-out is within the reminder window.
3. System sends check-in reminder to guest: unit name, check-in date/time, QR code reminder.
4. System sends check-in and check-out reminders to admin for operational readiness (includes QR code reminder — UC-NOTIF-05).

---

### UC-NOTIF-05: Receive QR Code Reminder (Admin)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-NOTIF-05 |
| **Use Case Name** | Receive QR Code Reminder |
| **Actor(s)** | Admin |
| **Description** | Admin receives a specific reminder notification to ensure the QR code for an upcoming booking is valid and has been issued to the guest, especially for bookings approaching check-in or check-out. |
| **Preconditions** | A confirmed booking has a check-in or check-out approaching within the configured reminder window. |
| **Postconditions** | Admin is alerted to verify QR code status and take action if needed (e.g., manual regeneration). |
| **Priority** | Must Have |
| **URS Refs** | URS-A-NOTIF-02 |
| **PRD Refs** | NOTIF-05 |

**Normal Flow:**
1. Scheduled daily job runs (same job as UC-NOTIF-04).
2. System identifies confirmed bookings with approaching check-in/check-out.
3. System sends admin an in-app and email notification: "Upcoming check-in for [Guest] at [Unit] on [Date]. Please verify QR code status."

---

### UC-NOTIF-06: View Booking in Google Calendar

| Field | Detail |
|---|---|
| **Use Case ID** | UC-NOTIF-06 |
| **Use Case Name** | View Booking in Google Calendar |
| **Actor(s)** | Guest, Admin |
| **Description** | Confirmed bookings are synchronised to the user's connected Google Calendar as events spanning the check-in to check-out dates. Users who have connected their Google account via OAuth can see their homestay bookings alongside their other calendar events. |
| **Preconditions** | User has connected their Google account (OAuth granted with `calendar.events` scope). Booking status is `confirmed`. |
| **Postconditions** | A Google Calendar event is created or updated for the booking via the Google Calendar API. |
| **Priority** | Should Have |
| **URS Refs** | URS-U-NOTIF-03, URS-A-NOTIF-03 |
| **PRD Refs** | NOTIF-06, NOTIF-07 |

**Normal Flow:**
1. System detects a booking status change to `confirmed`.
2. System retrieves the user's stored Google OAuth token.
3. System calls the Google Calendar API to create an event: title (unit name + "Stay"), start (check-in datetime), end (check-out datetime), description (booking reference, unit address).
4. If the event already exists (e.g., from a previous sync): system updates it.
5. Event appears in the user's Google Calendar.

**Alternative Flows:**
- **A1 – User has not connected Google Calendar:** System skips calendar sync. No error shown to user.

**Exception Flows:**
- **E1 – Google API error or token expired:** System logs the failure. User may need to reconnect their Google account.

---

### UC-NOTIF-07: Toggle Email Notifications (Admin)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-NOTIF-07 |
| **Use Case Name** | Toggle Email Notifications |
| **Actor(s)** | Admin |
| **Description** | Admin globally enables or disables the sending of email notifications system-wide. When disabled, all transactional emails are suppressed; in-app notifications continue to be delivered. |
| **Preconditions** | Admin is logged in with system settings access. |
| **Postconditions** | `settings['notification.email_enabled']` is updated. All subsequent email notification attempts respect the new setting. |
| **Priority** | Should Have |
| **URS Refs** | URS-A-SET-04 |
| **PRD Refs** | SET-GEN-04 |

**Normal Flow:**
1. Admin navigates to System Settings → Notification Settings.
2. Admin toggles the "Email Notifications" switch.
3. System saves `notification.email_enabled = true/false`.
4. System displays a success message. Future email jobs check this flag before sending.

---

## 6. Chat Module

---

### UC-CHAT-01: Send Message

| Field | Detail |
|---|---|
| **Use Case ID** | UC-CHAT-01 |
| **Use Case Name** | Send Message |
| **Actor(s)** | Guest, Admin |
| **Description** | A user sends a text message to the other party (Guest ↔ Admin) via the built-in real-time chat system. Messages are persisted in the database and delivered instantly via WebSocket (Laravel Reverb). |
| **Preconditions** | Both parties have accounts. The sender is logged in and on the chat interface. |
| **Postconditions** | The message is stored in the `chat_messages` table. The message is delivered to the recipient via WebSocket broadcast. |
| **Priority** | Should Have |
| **URS Refs** | URS-U-CHAT-01, URS-A-CHAT-01 |
| **PRD Refs** | CHAT-01, CHAT-02 |

**Normal Flow:**
1. User navigates to the Chat page.
2. User types a message in the text input field.
3. User clicks "Send" or presses Enter.
4. System saves the message to `chat_messages` with sender ID, recipient ID, content, and timestamp.
5. System broadcasts the message via Laravel Reverb on the appropriate chat channel.
6. Recipient receives the message in real time (UC-CHAT-02).
7. Message appears in the sender's chat window as "sent."

**Alternative Flows:**
- **A1 – Empty message submitted:** System does not send an empty string; submit button is disabled when input is blank.

**Exception Flows:**
- **E1 – WebSocket connection lost:** System falls back to showing a "Reconnecting..." indicator. Message is saved to the database and will be visible on next load.

---

### UC-CHAT-02: Receive Message (Real-time)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-CHAT-02 |
| **Use Case Name** | Receive Message (Real-time) |
| **Actor(s)** | Guest, Admin |
| **Description** | The recipient receives a new chat message instantly via WebSocket without requiring a page refresh. An unread message badge updates on the chat icon. |
| **Preconditions** | Recipient is logged in. Laravel Echo is active on the client. |
| **Postconditions** | The message appears in the chat window in real time. Unread badge count increments if the chat panel is not currently in focus. |
| **Priority** | Should Have |
| **URS Refs** | URS-U-CHAT-01, URS-A-CHAT-01 |
| **PRD Refs** | CHAT-02 |

**Normal Flow:**
1. Sender sends a message (UC-CHAT-01).
2. Laravel Reverb broadcasts the event on the recipient's private channel.
3. Laravel Echo on the recipient's browser receives the event.
4. System appends the new message to the chat window.
5. If the chat window is not focused: unread badge on the chat icon increments.

**Alternative Flows:**
- **A1 – Recipient is not currently online:** WebSocket is not connected. Message is stored in the database. Recipient sees it on next login (UC-CHAT-03).

---

### UC-CHAT-03: View Chat History

| Field | Detail |
|---|---|
| **Use Case ID** | UC-CHAT-03 |
| **Use Case Name** | View Chat History |
| **Actor(s)** | Guest, Admin |
| **Description** | User views the full persisted conversation history between the guest and admin. Messages are displayed in chronological order with sender identification and timestamps. |
| **Preconditions** | At least one message exists in the `chat_messages` table for this conversation. |
| **Postconditions** | All messages are displayed. Unread messages are marked as read. |
| **Priority** | Should Have |
| **URS Refs** | URS-U-CHAT-01, URS-A-CHAT-01 |
| **PRD Refs** | CHAT-03 |

**Normal Flow:**
1. User navigates to the Chat page.
2. System retrieves all `chat_messages` records for the conversation (ordered by `created_at` ascending).
3. System renders the message thread with: sender name, message content, and timestamp.
4. Messages sent by the current user are aligned to the right; received messages to the left.
5. System marks all unread messages as read.
6. Chat panel auto-scrolls to the latest message.

---

## 7. User Management Module

---

### UC-USR-01: Create User Account (Admin)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-USR-01 |
| **Use Case Name** | Create User Account |
| **Actor(s)** | Admin |
| **Description** | Admin manually creates a new user account and assigns it a role. The system sets a default temporary password and flags the account to require a password change on first login. The new user is notified with credentials or instructions. |
| **Preconditions** | The email address is not already registered. Admin has `user.create` permission. |
| **Postconditions** | A new `users` record is created with the assigned role. `must_change_password = true`. User is notified with login instructions and temporary credentials. Audit log entry created. |
| **Priority** | Must Have |
| **URS Refs** | URS-A-USR-01 |
| **PRD Refs** | USR-01 |

**Normal Flow:**
1. Admin navigates to User Management → Create User.
2. Admin enters: full name, email address, and assigns a role.
3. System generates a default temporary password (`Abc@123` or configurable default).
4. System creates the user record with `must_change_password = true`.
5. System sends the user an email with their temporary password and a link to log in.
6. System writes an audit log entry.
7. Admin sees a success message.

**Alternative Flows:**
- **A1 – Email already registered:** System displays a validation error. Admin can edit the existing account instead.

---

### UC-USR-02: Edit User Account (Admin)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-USR-02 |
| **Use Case Name** | Edit User Account |
| **Actor(s)** | Admin |
| **Description** | Admin updates an existing user's name, email address, phone number, or role assignment. |
| **Preconditions** | The user account exists. Admin has `user.edit` permission. |
| **Postconditions** | User record is updated. If role was changed, new permissions take effect immediately. Audit log entry created. |
| **Priority** | Must Have |
| **URS Refs** | URS-A-USR-01 |
| **PRD Refs** | USR-02 |

**Normal Flow:**
1. Admin navigates to User Management → select user → Edit.
2. System pre-populates the edit form with current values.
3. Admin updates desired fields.
4. Admin submits the form.
5. System validates changes (e.g., email uniqueness).
6. System saves the updated record and writes an audit log entry.
7. System displays a success message.

---

### UC-USR-03: Delete User Account (Admin)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-USR-03 |
| **Use Case Name** | Delete User Account |
| **Actor(s)** | Admin |
| **Description** | Admin deletes a user account from the system. If the user has active or upcoming bookings, the system warns the admin before proceeding. Deletion is a destructive action and should be used with care. |
| **Preconditions** | Admin has `user.delete` permission. The target user exists. |
| **Postconditions** | User record is soft-deleted or permanently removed. Associated data (bookings, payments) may be retained for record-keeping. Audit log entry created. |
| **Priority** | Must Have |
| **URS Refs** | URS-A-USR-01 |
| **PRD Refs** | USR-03 |

**Normal Flow:**
1. Admin navigates to User Management → select user → Delete.
2. System checks for active or upcoming bookings linked to this user.
3. If active bookings exist: system shows a warning ("This user has [N] active/upcoming bookings. Deleting may affect ongoing reservations.").
4. Admin confirms deletion (a secondary confirmation prompt is displayed).
5. System soft-deletes or removes the user record.
6. System writes an audit log entry.

**Alternative Flows:**
- **A1 – Admin cancels confirmation prompt:** No changes are made.

---

### UC-USR-04: Reset User Password (Admin)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-USR-04 |
| **Use Case Name** | Reset User Password |
| **Actor(s)** | Admin |
| **Description** | Admin resets a user's password, either by sending a password reset link to the user's email or by resetting to the system default temporary password (`Abc@123`). The system forces a password change on next login. |
| **Preconditions** | User account exists. Admin has `user.reset_password` permission. |
| **Postconditions** | User's password is reset. `must_change_password = true` is set. User is notified. `locked_until` is cleared if the account was locked. Audit log entry created. |
| **Priority** | Must Have |
| **URS Refs** | URS-A-USR-02, URS-A-USR-03 |
| **PRD Refs** | USR-04, USR-05 |

**Normal Flow:**
1. Admin navigates to User Management → select user → "Reset Password."
2. Admin chooses reset method:
   - **Option A – Send reset link:** System sends a password reset email to the user's registered address.
   - **Option B – Set to default:** System sets the password to `Abc@123` immediately.
3. System sets `must_change_password = true` on the user record.
4. System clears `locked_until` and resets `failed_login_attempts` to 0 (in case of lockout).
5. System notifies the user (in-app and email).
6. System writes an audit log entry.

---

### UC-USR-05: Activate / Deactivate User Account (Admin)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-USR-05 |
| **Use Case Name** | Activate / Deactivate User Account |
| **Actor(s)** | Admin |
| **Description** | Admin toggles a user account between active and inactive states. Deactivated users cannot log in and are blocked from accessing any protected pages. Re-activating restores full access. |
| **Preconditions** | User account exists. Admin has `user.manage` permission. |
| **Postconditions** | User's `is_active` flag is updated. If deactivated, any active sessions for that user are invalidated. Audit log entry created. |
| **Priority** | Must Have |
| **URS Refs** | URS-A-USR-04 |
| **PRD Refs** | USR-06 |

**Normal Flow:**
1. Admin navigates to User Management → select user → "Deactivate" or "Activate."
2. System prompts for confirmation.
3. Admin confirms.
4. System updates `is_active` flag.
5. If deactivating: system invalidates any existing sessions for this user.
6. System displays a success message and writes an audit log entry.

---

### UC-USR-06: Force Password Change (Triggered by Admin Reset)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-USR-06 |
| **Use Case Name** | Force Password Change (After Admin Reset) |
| **Actor(s)** | Admin (initiates), System (enforces) |
| **Description** | This use case is triggered as part of UC-USR-04. After an admin reset, the system sets a flag that forces the affected user to set a new personal password before they can access any other part of the system. This is the admin-side trigger; enforcement is handled by UC-AUTH-08. |
| **Preconditions** | Admin has reset the user's password (UC-USR-04). |
| **Postconditions** | `must_change_password = true` on the user record. On next login, the user is redirected to the forced change screen (UC-AUTH-08). |
| **Priority** | Must Have |
| **URS Refs** | URS-A-USR-03 |
| **PRD Refs** | USR-04, AUTH-10 |

**Normal Flow:**
1. Admin completes the password reset (UC-USR-04, step 3).
2. System automatically sets `must_change_password = true` on the affected user's record.
3. On the user's next login, system enforces the change (UC-AUTH-08).

---

## 8. Role & Permission Module

---

### UC-ROLE-01: Create Role

| Field | Detail |
|---|---|
| **Use Case ID** | UC-ROLE-01 |
| **Use Case Name** | Create Role |
| **Actor(s)** | Admin |
| **Description** | Admin creates a new named role in the system. Roles group permissions together and can be assigned to user accounts. |
| **Preconditions** | Admin has `role.create` permission. The role name is unique. |
| **Postconditions** | A new role is saved via `spatie/laravel-permission`. It can be assigned permissions (UC-ROLE-04) and assigned to users. Audit log entry created. |
| **Priority** | Must Have |
| **URS Refs** | URS-A-ROLE-01 |
| **PRD Refs** | ROLE-01 |

**Normal Flow:**
1. Admin navigates to Role Management → Create Role.
2. Admin enters the role name and optional description.
3. System validates the name is unique.
4. System creates the role record.
5. Admin is taken to the role detail page to assign permissions (UC-ROLE-04).

---

### UC-ROLE-02: Edit Role

| Field | Detail |
|---|---|
| **Use Case ID** | UC-ROLE-02 |
| **Use Case Name** | Edit Role |
| **Actor(s)** | Admin |
| **Description** | Admin edits the name or description of an existing role. |
| **Preconditions** | Role exists. Admin has `role.edit` permission. |
| **Postconditions** | Role record is updated. Audit log entry created. |
| **Priority** | Must Have |
| **URS Refs** | URS-A-ROLE-01 |
| **PRD Refs** | ROLE-01 |

**Normal Flow:**
1. Admin selects a role and clicks "Edit."
2. Admin modifies name or description.
3. System validates uniqueness of new name.
4. System saves the updated role.

---

### UC-ROLE-03: Delete Role

| Field | Detail |
|---|---|
| **Use Case ID** | UC-ROLE-03 |
| **Use Case Name** | Delete Role |
| **Actor(s)** | Admin |
| **Description** | Admin deletes a role. The system prevents deletion if the role is currently assigned to any user accounts, to avoid breaking access control. |
| **Preconditions** | Admin has `role.delete` permission. |
| **Postconditions** | Role is deleted if not assigned to any user. Audit log entry created. |
| **Priority** | Must Have |
| **URS Refs** | URS-A-ROLE-01, URS-A-ROLE-03 |
| **PRD Refs** | ROLE-01, ROLE-02 |

**Normal Flow:**
1. Admin selects a role and clicks "Delete."
2. System checks if any users are currently assigned this role.
3. If no users: system prompts for confirmation and proceeds with deletion.
4. System writes an audit log entry.

**Alternative Flows:**
- **A1 – Role is assigned to users (extends: Prevent Delete Role):** System blocks deletion and displays: "This role is assigned to [N] user(s). Reassign users before deleting." Admin must reassign or remove the role from all affected users first.

---

### UC-ROLE-04: Assign Permissions to Role

| Field | Detail |
|---|---|
| **Use Case ID** | UC-ROLE-04 |
| **Use Case Name** | Assign Permissions to Role |
| **Actor(s)** | Admin |
| **Description** | Admin assigns or revokes specific permission keys for a role, controlling what actions users bearing that role can perform. Changes take effect immediately for all users with that role. |
| **Preconditions** | The role and the target permissions exist. Admin has `role.assign_permissions` permission. |
| **Postconditions** | The role's permission set is updated via `spatie/laravel-permission`. All users with this role immediately gain or lose the affected capabilities. Audit log entry created. |
| **Priority** | Must Have |
| **URS Refs** | URS-A-ROLE-02 |
| **PRD Refs** | ROLE-03 |

**Normal Flow:**
1. Admin navigates to Role Management → select role → "Manage Permissions."
2. System displays a checklist of all available permissions, with currently assigned ones checked.
3. Admin checks or unchecks permissions as required.
4. Admin saves the changes.
5. System syncs the role's permissions via Spatie's `syncPermissions()` method.
6. System writes an audit log entry.

---

### UC-ROLE-05: Create Permission

| Field | Detail |
|---|---|
| **Use Case ID** | UC-ROLE-05 |
| **Use Case Name** | Create Permission |
| **Actor(s)** | Admin |
| **Description** | Admin creates a new individual permission key (e.g., `bookings.cancel`, `qr.extend`). Permissions are building blocks that can be assigned to roles. |
| **Preconditions** | Admin has `permission.create` access. The permission name/key is unique. |
| **Postconditions** | A new permission is created via Spatie Permission. It can be assigned to roles (UC-ROLE-04). Audit log entry created. |
| **Priority** | Must Have |
| **URS Refs** | URS-A-PERM-01 |
| **PRD Refs** | PERM-01 |

**Normal Flow:**
1. Admin navigates to Permission Management → Create Permission.
2. Admin enters the permission name (e.g., `booking.create`) and an optional description.
3. System validates uniqueness.
4. System creates the permission record.

---

### UC-ROLE-06: Edit Permission

| Field | Detail |
|---|---|
| **Use Case ID** | UC-ROLE-06 |
| **Use Case Name** | Edit Permission |
| **Actor(s)** | Admin |
| **Description** | Admin edits the name or description of an existing permission. |
| **Preconditions** | Permission exists. Admin has edit access. |
| **Postconditions** | Permission record is updated. Audit log entry created. |
| **Priority** | Must Have |
| **URS Refs** | URS-A-PERM-01 |
| **PRD Refs** | PERM-01 |

**Normal Flow:**
1. Admin selects a permission and clicks "Edit."
2. Admin modifies the name or description.
3. System saves the update.

---

### UC-ROLE-07: Delete Permission

| Field | Detail |
|---|---|
| **Use Case ID** | UC-ROLE-07 |
| **Use Case Name** | Delete Permission |
| **Actor(s)** | Admin |
| **Description** | Admin deletes a permission. The system prevents deletion if the permission is currently attached to any role, to prevent orphaned role configurations. |
| **Preconditions** | Admin has `permission.delete` access. |
| **Postconditions** | Permission is deleted if not attached to any role. Audit log entry created. |
| **Priority** | Must Have |
| **URS Refs** | URS-A-PERM-01, URS-A-PERM-02 |
| **PRD Refs** | PERM-01, PERM-02 |

**Normal Flow:**
1. Admin selects a permission and clicks "Delete."
2. System checks if the permission is attached to any roles.
3. If not attached: system prompts for confirmation and deletes.
4. System writes an audit log entry.

**Alternative Flows:**
- **A1 – Permission is attached to one or more roles (extends: Prevent Delete Permission):** System blocks deletion and displays: "This permission is attached to [N] role(s). Remove from roles before deleting."

---

## 9. System Settings Module

---

### UC-SET-01: Configure SMTP Settings

| Field | Detail |
|---|---|
| **Use Case ID** | UC-SET-01 |
| **Use Case Name** | Configure SMTP Settings |
| **Actor(s)** | Admin |
| **Description** | Admin configures the outgoing SMTP email server settings used for all system transactional emails: host, port, username, password, encryption type, and sender name/address. |
| **Preconditions** | Admin is logged in with system settings access. |
| **Postconditions** | SMTP credentials are saved to the `settings` table. All subsequent email dispatches use the new configuration. |
| **Priority** | Must Have |
| **URS Refs** | URS-A-SET-01 |
| **PRD Refs** | SET-SMTP-01 |

**Normal Flow:**
1. Admin navigates to System Settings → Email (SMTP).
2. Admin enters: SMTP host, port, username, password, encryption (TLS/SSL), sender name, sender email.
3. Admin optionally clicks "Test Connection" to verify credentials.
4. Admin saves the settings.
5. System stores values in the `settings` table under appropriate keys.

**Alternative Flows:**
- **A1 – Test connection fails:** System displays the SMTP error message. Admin can correct the credentials and retry.

---

### UC-SET-02: Configure Security Settings

| Field | Detail |
|---|---|
| **Use Case ID** | UC-SET-02 |
| **Use Case Name** | Configure Security Settings |
| **Actor(s)** | Admin |
| **Description** | Admin configures the three core security parameters: maximum failed login attempts before lockout, lockout duration (in minutes), and session timeout (in minutes). All values are stored dynamically in the `settings` table and applied immediately. |
| **Preconditions** | Admin is logged in with system settings access. |
| **Postconditions** | Settings saved. Login attempt tracking, lockout enforcement, and session management use the new values going forward. |
| **Priority** | Must Have |
| **URS Refs** | URS-A-SET-02 |
| **PRD Refs** | SET-SEC-01, SET-SEC-02, SET-SEC-03 |

**Normal Flow:**
1. Admin navigates to System Settings → Security.
2. Admin sets: max login attempts (e.g., 5), lockout duration in minutes (e.g., 30), session timeout in minutes (e.g., 120).
3. System validates that values are positive integers.
4. System saves values to the `settings` table.
5. System displays a success confirmation.

---

### UC-SET-03: Configure Refund Policy Parameters

| Field | Detail |
|---|---|
| **Use Case ID** | UC-SET-03 |
| **Use Case Name** | Configure Refund Policy Parameters |
| **Actor(s)** | Admin |
| **Description** | Admin configures the tiered cancellation refund policy. Each tier specifies a days-before-check-in threshold and the corresponding refund percentage. These values are used by the cancellation flow (UC-BK-10, UC-BK-11, UC-BK-16) to calculate refunds. |
| **Preconditions** | Admin is logged in with system settings access. |
| **Postconditions** | Refund policy tiers are saved to the `settings` table. Future cancellations use the new tiers. |
| **Priority** | Must Have |
| **URS Refs** | URS-A-SET-03 |
| **PRD Refs** | SET-GEN-02 |

**Normal Flow:**
1. Admin navigates to System Settings → Refund Policy.
2. Admin views the current tiered configuration (e.g., >14 days: 100%, 7–14 days: 50%, <7 days: 0%).
3. Admin modifies tier thresholds and/or percentages.
4. Admin saves.
5. System validates that tiers do not overlap and percentages are between 0 and 100.
6. System saves the tiers to `settings`.

---

### UC-SET-04: Configure Payment & Billing Options

| Field | Detail |
|---|---|
| **Use Case ID** | UC-SET-04 |
| **Use Case Name** | Configure Payment & Billing Options |
| **Actor(s)** | Admin |
| **Description** | Admin configures payment gateway credentials, bill number prefix/format, deposit rules, and the initial booking payment window (how long a guest has to pay before auto-cancellation). |
| **Preconditions** | Admin is logged in with system settings access. |
| **Postconditions** | Payment and billing configuration saved to `settings`. Applied to all subsequent payment operations. |
| **Priority** | Must Have |
| **URS Refs** | URS-A-PAY-01 |
| **PRD Refs** | SET-GEN-03 |

**Normal Flow:**
1. Admin navigates to System Settings → Payment & Billing.
2. Admin configures: gateway API key/secret, bill number prefix, payment window duration (default: 1 day), and any deposit configuration.
3. Admin saves the settings.
4. System validates the configuration and saves to `settings`.

---

### UC-SET-05: Toggle Email Notifications Globally

> See UC-NOTIF-07 for full description. This setting is accessible from System Settings → Notification Settings.

---

### UC-SET-06: Configure Extension Charge Rates

| Field | Detail |
|---|---|
| **Use Case ID** | UC-SET-06 |
| **Use Case Name** | Configure Extension Charge Rates |
| **Actor(s)** | Admin |
| **Description** | Admin sets the system-wide extra charge rates for booking extensions: one rate per hour (for same-day time extensions) and one rate per additional night (for date extensions). These rates are used by the extension billing logic when admin initiates an extension (UC-QR-06). |
| **Preconditions** | Admin is logged in with system settings access. |
| **Postconditions** | Extension charge rates saved to `settings` table under `extension.rate_per_hour` and `extension.rate_per_night`. Applied to all subsequent extension billing calculations. |
| **Priority** | Must Have |
| **URS Refs** | URS-A-SET-05, URS-A-SET-06 |
| **PRD Refs** | SET-EXT-01, SET-EXT-02 |

**Normal Flow:**
1. Admin navigates to System Settings → Extension Settings.
2. Admin enters the charge rate per hour (for time extensions) and the charge rate per night (for date extensions).
3. System validates that values are positive numeric amounts.
4. System saves values to `settings`.
5. Confirmation message is displayed.

---

### UC-SET-07: Configure System-Wide Default Extension Payment Window

| Field | Detail |
|---|---|
| **Use Case ID** | UC-SET-07 |
| **Use Case Name** | Configure System-Wide Default Extension Payment Window |
| **Actor(s)** | Admin |
| **Description** | Admin sets the system-wide default extension payment window in minutes. This value defines how long a guest has to pay the extension charge before the extension is auto-cancelled. Individual homestay units can override this value (UC-SET-09). Default value is 60 minutes. |
| **Preconditions** | Admin is logged in with system settings access. |
| **Postconditions** | `settings['extension.payment_window_minutes']` updated. Used as the fallback for units without a custom window. |
| **Priority** | Must Have |
| **URS Refs** | URS-A-SET-08 |
| **PRD Refs** | SET-EXT-04 |

**Normal Flow:**
1. Admin navigates to System Settings → Extension Settings.
2. Admin sets the payment window duration in minutes (default: 60).
3. System validates the value is a positive integer.
4. System saves to `settings`.

---

### UC-SET-08: Manage Default Homestay Policies

| Field | Detail |
|---|---|
| **Use Case ID** | UC-SET-08 |
| **Use Case Name** | Manage Default Homestay Policies |
| **Actor(s)** | Admin |
| **Description** | Admin manages the system-level default house policies that are automatically applied to all newly created homestay units (UC-HS-10). Default policies include the seeded defaults: No Pets, No Durians, No Smoking. Admin can add, edit, or remove these defaults. |
| **Preconditions** | Admin is logged in with system settings access. |
| **Postconditions** | Default policies are updated in the system. New units created after this change will inherit the updated defaults. Existing units are not retroactively changed. |
| **Priority** | Must Have |
| **URS Refs** | URS-A-SET-07 |
| **PRD Refs** | SET-POL-01, SET-POL-02 |

**Normal Flow:**
1. Admin navigates to System Settings → Default Policies.
2. System displays the current list of default policies (seeded: No Pets, No Durians, No Smoking).
3. Admin can:
   - **Add:** Enter a new default policy description.
   - **Edit:** Modify an existing default policy text.
   - **Remove:** Delete a default policy (with confirmation).
4. System saves changes. Future unit creations will apply the updated defaults.

---

### UC-SET-09: Configure Per-Unit Extension Payment Window

| Field | Detail |
|---|---|
| **Use Case ID** | UC-SET-09 |
| **Use Case Name** | Configure Per-Unit Extension Payment Window |
| **Actor(s)** | Admin |
| **Description** | Admin sets a custom extension payment window (in minutes) for a specific homestay unit from the unit's edit page. When set, this value overrides the system-wide default (UC-SET-07) for extension billing on this unit only. |
| **Preconditions** | Admin is on the unit create or edit form (UC-HS-04 or UC-HS-05). |
| **Postconditions** | `homestays.extension_payment_window_minutes` updated. Future extensions on this unit use the unit-specific window instead of the system default. |
| **Priority** | Must Have |
| **URS Refs** | URS-A-HS-09, URS-A-QR-07 |
| **PRD Refs** | HS-13, SET-EXT-04 |

**Normal Flow:**
1. Admin is on the homestay unit create/edit page.
2. Admin enters a value (in minutes) in the "Extension Payment Window" field. Leaving it blank means the system default is used.
3. System saves the value to `homestays.extension_payment_window_minutes`.
4. Extension billing for this unit will use this value as the payment deadline window.

---

## 10. Audit Log Module

---

### UC-AUDIT-01: View Audit Trail

| Field | Detail |
|---|---|
| **Use Case ID** | UC-AUDIT-01 |
| **Use Case Name** | View Audit Trail |
| **Actor(s)** | Admin |
| **Description** | Admin views the full chronological audit trail of all logged system events, including user actions (login, logout, booking operations), admin changes (user management, settings), and automated system events (auto-cancellations, QR operations). Logs are read-only and immutable. |
| **Preconditions** | Admin is logged in with audit log access. |
| **Postconditions** | Admin can view all audit entries. No modifications to log entries are possible. |
| **Priority** | Must Have |
| **URS Refs** | URS-A-AUDIT-01 |
| **PRD Refs** | AUDIT-01, AUDIT-03 |

**Normal Flow:**
1. Admin navigates to Audit Logs.
2. System retrieves all `activity_log` records (via `spatie/laravel-activitylog`) in reverse chronological order.
3. System displays each entry: timestamp, actor (user or system), action/event type, affected entity, IP address, and relevant metadata.
4. Admin can paginate through entries.

---

### UC-AUDIT-02: Filter Audit Logs

| Field | Detail |
|---|---|
| **Use Case ID** | UC-AUDIT-02 |
| **Use Case Name** | Filter Audit Logs |
| **Actor(s)** | Admin |
| **Description** | Admin applies one or more filters to the audit log view to locate specific entries: date range, event type/category, and actor (specific user or "System"). |
| **Preconditions** | Admin is on the Audit Logs page (UC-AUDIT-01). |
| **Postconditions** | The displayed log is filtered to show only matching entries. |
| **Priority** | Must Have |
| **URS Refs** | URS-A-AUDIT-01 |
| **PRD Refs** | AUDIT-01 |

**Normal Flow:**
1. Admin selects filter criteria: date range, event type, or actor name.
2. System applies the filters to the `activity_log` query.
3. System re-renders the log view with matching results.
4. Admin can clear filters to return to the full log.

---

### UC-AUDIT-03: Log User Actions

| Field | Detail |
|---|---|
| **Use Case ID** | UC-AUDIT-03 |
| **Use Case Name** | Log User Actions |
| **Actor(s)** | System |
| **Description** | The system automatically logs all significant guest actions to the audit trail, including: login, logout, booking submission, booking cancellation, payment initiation, and feedback submission. |
| **Preconditions** | A loggable guest event occurs. |
| **Postconditions** | An immutable audit record is created with: actor, event type, affected entity, and timestamp. |
| **Priority** | Must Have |
| **URS Refs** | URS-A-AUDIT-01 |
| **PRD Refs** | AUDIT-02, AUDIT-03 |

**Normal Flow:**
1. Guest performs a loggable action (e.g., submits a booking).
2. System invokes `spatie/laravel-activitylog` to record: `causer` (user), `event` (e.g., "booking.created"), `subject` (booking record), and `created_at`.
3. Record is inserted into `activity_log` table.
4. No edit or delete operations are permitted on this table (immutability enforced at DB and application level).

---

### UC-AUDIT-04: Log System Events

| Field | Detail |
|---|---|
| **Use Case ID** | UC-AUDIT-04 |
| **Use Case Name** | Log System Events |
| **Actor(s)** | System |
| **Description** | The system logs automated events to the audit trail, including: auto-cancellation of overdue bookings, QR code expiry and regeneration, extension auto-cancellations, and scheduled job executions. System events are attributed to the "System" actor rather than a user. |
| **Preconditions** | An automated system event occurs (scheduled job, webhook, triggered automation). |
| **Postconditions** | An immutable audit record is created attributed to the system with event details. |
| **Priority** | Must Have |
| **URS Refs** | URS-A-AUDIT-01 |
| **PRD Refs** | AUDIT-02, AUDIT-03 |

**Normal Flow:**
1. A system automation triggers (e.g., auto-cancel job finds an overdue booking).
2. System invokes `spatie/laravel-activitylog` with `causer = null` (system-initiated) and appropriate event descriptor.
3. Record is inserted into `activity_log` table.

---

## 11. QR Code Door Access Module

---

### UC-QR-01: Receive QR Code (Upon Booking Confirmation)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-QR-01 |
| **Use Case Name** | Receive QR Code Upon Booking Confirmation |
| **Actor(s)** | Guest |
| **Description** | After a booking is confirmed (payment successfully received), the system automatically generates a unique, time-limited QR code for the guest. The code is delivered via in-app notification and email, and is accessible from the booking detail page. |
| **Preconditions** | Booking status has just changed to `confirmed` (triggered by UC-PAY-05 webhook processing). |
| **Postconditions** | A `qr_codes` record is created with `status = active`, `valid_from = check_in_datetime`, `valid_until = check_out_datetime`. Guest receives the QR code via in-app and email notifications. |
| **Priority** | Must Have |
| **URS Refs** | URS-A-QR-01 |
| **PRD Refs** | QR-01, QR-06 |

**Normal Flow:**
1. System confirms payment (UC-PAY-05, step 6).
2. System generates a unique cryptographic token for the QR code.
3. System creates a `qr_codes` record linked to the booking: `token`, `status = active`, `valid_from = check_in_datetime`, `valid_until = check_out_datetime`.
4. System renders the QR code image using `simplesoftwareio/simple-qrcode`.
5. System sends an in-app notification to the guest with the QR code.
6. System sends a confirmation email to the guest containing the QR code image and booking details.
7. Guest can view the QR code at any time on their booking detail page.

---

### UC-QR-02: Use QR Code for Door Access

| Field | Detail |
|---|---|
| **Use Case ID** | UC-QR-02 |
| **Use Case Name** | Use QR Code for Door Access |
| **Actor(s)** | Guest |
| **Description** | Guest presents their QR code to the smart lock reader at the homestay entrance to gain physical access to the property. Access is granted only if the QR code is active and the current time falls within the `valid_from` and `valid_until` window. |
| **Preconditions** | QR code `status = active`. Current time is between `valid_from` and `valid_until`. Smart lock hardware is operational. |
| **Postconditions** | Guest gains physical access to the property. |
| **Priority** | Must Have |
| **URS Refs** | URS-A-QR-01 |
| **PRD Refs** | QR-02 |

**Normal Flow:**
1. Guest opens the booking detail on their device and displays the QR code.
2. Guest holds the QR code in front of the smart lock scanner.
3. Smart lock validates the QR token against the system (or locally cached valid tokens).
4. System/lock confirms the token is active and within the valid window.
5. Lock grants access and door opens.

**Alternative Flows:**
- **A1 – QR code is expired (`valid_until` passed):** Access is denied. Guest sees "QR Code Expired" on the lock interface.
- **A2 – QR code is `inactive` or `revoked`:** Access is denied.
- **A3 – Device cannot display QR code (dead battery, no signal):** Guest must contact admin for manual assistance.

---

### UC-QR-03: Auto-Invalidate QR Code (After Check-out)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-QR-03 |
| **Use Case Name** | Auto-Invalidate QR Code After Check-out |
| **Actor(s)** | System |
| **Description** | A scheduled job runs to detect QR codes whose `valid_until` timestamp has passed. It automatically sets those codes to `expired`, preventing further access to the property after the booked check-out time. This triggers the housekeeping QR code step (UC-QR-04). |
| **Preconditions** | `qr_codes.status = active` and `qr_codes.valid_until < now()`. |
| **Postconditions** | `qr_codes.status = expired`. Guest can no longer use the code for access. Housekeeping QR code generation is triggered. |
| **Priority** | Must Have |
| **URS Refs** | URS-A-QR-01 |
| **PRD Refs** | QR-03 |

**Normal Flow:**
1. Scheduled job runs at regular intervals (triggered at/after checkout times).
2. System queries all QR codes where `status = active` and `valid_until < now()`.
3. For each: system sets `status = expired`.
4. System triggers the housekeeping QR code step (admin notified to regenerate — UC-QR-04).
5. Booking status is updated to `completed`.

---

### UC-QR-04: Regenerate QR Code (For Housekeeping)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-QR-04 |
| **Use Case Name** | Regenerate QR Code for Housekeeping |
| **Actor(s)** | Admin |
| **Description** | Admin manually generates a temporary, short-validity QR code for housekeeping staff to access the property between guest stays (after check-out, before next guest check-in). The housekeeping code has a limited validity window and cannot be confused with a guest QR code. |
| **Preconditions** | The previous booking's QR code has been `expired` or `invalidated`. Admin is logged in with QR management permission. |
| **Postconditions** | A new `qr_codes` record is created with type `housekeeping` and a short `valid_until` window. Admin or housekeeping staff can use it for access. |
| **Priority** | Must Have |
| **URS Refs** | URS-A-QR-02 |
| **PRD Refs** | QR-04 |

**Normal Flow:**
1. Admin navigates to the booking or unit management and clicks "Generate Housekeeping QR."
2. Admin sets the validity window (e.g., valid for 4 hours).
3. System generates a new QR code record with `type = housekeeping` and the configured `valid_until`.
4. System displays the code for admin to share with cleaning staff.

---

### UC-QR-05: Mark Housekeeping Complete

| Field | Detail |
|---|---|
| **Use Case ID** | UC-QR-05 |
| **Use Case Name** | Mark Housekeeping Complete |
| **Actor(s)** | Admin |
| **Description** | Admin marks housekeeping as complete for a unit. This action expires the housekeeping QR code and triggers auto-generation of the next guest's QR code (UC-QR-06). |
| **Preconditions** | A housekeeping QR code exists for the unit. The next confirmed booking exists. Admin has appropriate access. |
| **Postconditions** | Housekeeping QR code status set to `expired`. Next guest's QR code generated (UC-QR-06). |
| **Priority** | Must Have |
| **URS Refs** | URS-A-QR-03 |
| **PRD Refs** | QR-05 |

**Normal Flow:**
1. Admin navigates to the unit or booking and clicks "Mark Housekeeping Complete."
2. System sets the housekeeping QR code status to `expired`.
3. System checks for the next confirmed booking on this unit.
4. System auto-generates a QR code for the next guest (UC-QR-06).

---

### UC-QR-06: Auto-Generate QR Code (For Next Guest)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-QR-06 |
| **Use Case Name** | Auto-Generate QR Code for Next Guest |
| **Actor(s)** | System |
| **Description** | After admin marks housekeeping complete (UC-QR-05), the system automatically generates a new QR code for the next confirmed booking on that unit. This ensures the next guest receives their access code without admin manually creating it. |
| **Preconditions** | Housekeeping is marked complete. A confirmed booking exists for the unit with a future check-in. |
| **Postconditions** | A new `qr_codes` record is created for the next booking guest. Guest is notified with the new QR code. |
| **Priority** | Must Have |
| **URS Refs** | URS-A-QR-03 |
| **PRD Refs** | QR-05 |

**Normal Flow:**
1. Admin completes housekeeping (UC-QR-05).
2. System identifies the next confirmed booking on the unit.
3. System generates a new unique QR code with `valid_from = check_in_datetime`, `valid_until = check_out_datetime`.
4. System sends the QR code to the next guest via in-app and email notification.

**Alternative Flows:**
- **A1 – No next confirmed booking exists:** System skips QR generation. Admin does not need to act.

---

### UC-QR-07: Initiate Booking Extension (Time or Date)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-QR-07 |
| **Use Case Name** | Initiate Booking Extension |
| **Actor(s)** | Admin |
| **Description** | Admin initiates a booking extension on behalf of a guest who has requested to stay longer. Admin selects the extension type (time extension: later check-out on the same day; or date extension: additional nights). The system checks availability, calculates the charge, creates an extension record, sets a payment deadline, and notifies the guest. The QR code is NOT extended at this point. |
| **Preconditions** | Booking status is `confirmed`. Admin has `qr.extend` permission. No conflicting bookings exist for the extended period. |
| **Postconditions** | A `booking_extensions` record created with `status = pending_payment`. Extension bill generated. Guest notified with payment deadline. QR code `valid_until` NOT yet updated. |
| **Priority** | Must Have |
| **URS Refs** | URS-A-QR-04, URS-A-QR-05, URS-A-QR-06 |
| **PRD Refs** | QR-07, QR-08, QR-09, QR-10 |

**Normal Flow:**
1. Admin opens the active booking and clicks "Extend Stay."
2. Admin selects extension type: "Time Extension" (adjust check-out time on same day) or "Date Extension" (add nights).
3. Admin inputs the new check-out date/time.
4. System performs an availability check for the extended period (UC-BK-03).
5. System calculates the additional charge:
   - Time extension: `(hours extended) × extension.rate_per_hour`
   - Date extension: `(nights added) × extension.rate_per_night`
6. System creates a `booking_extensions` record with `status = pending_payment`.
7. System generates an extension bill (includes UC-QR-08).
8. System sets `payment_deadline = now() + extension_payment_window_minutes` (unit-specific or system default).
9. System sends guest notification of the extension bill and deadline (UC-QR-09).
10. System writes an audit log entry.

**Alternative Flows:**
- **A1 – Availability conflict for extension period:** System displays conflicting booking details. Admin selects a different new check-out date/time.

---

### UC-QR-08: Generate Extension Bill & Set Payment Deadline

| Field | Detail |
|---|---|
| **Use Case ID** | UC-QR-08 |
| **Use Case Name** | Generate Extension Bill & Set Payment Deadline |
| **Actor(s)** | System |
| **Description** | Upon extension initiation by admin (UC-QR-07), the system calculates the total additional charge, generates a bill record, and sets the payment deadline using the unit's `extension_payment_window_minutes` (fallback: system-wide default of 60 minutes). This is an «include» of UC-QR-07. |
| **Preconditions** | Extension type, new check-out details, and applicable rates are known. `booking_extensions` record is being created. |
| **Postconditions** | A `billing` record is created for the extension charge. `booking_extensions.payment_deadline` is set. |
| **Priority** | Must Have |
| **URS Refs** | URS-A-QR-06 |
| **PRD Refs** | QR-09, QR-10, QR-11, HS-13, SET-EXT-04 |

**Normal Flow:**
1. System retrieves the applicable extension rate from `settings`.
2. System retrieves `extension_payment_window_minutes` from `homestays` for the unit; if null, falls back to `settings['extension.payment_window_minutes']` (default: 60).
3. System calculates the extension charge.
4. System creates a `billing` record linked to the extension.
5. System sets `booking_extensions.payment_deadline = now() + window_minutes`.

---

### UC-QR-09: Notify Guest of Extension Bill & Deadline

| Field | Detail |
|---|---|
| **Use Case ID** | UC-QR-09 |
| **Use Case Name** | Notify Guest of Extension Bill & Deadline |
| **Actor(s)** | System |
| **Description** | After the extension bill is generated (UC-QR-08), the system sends the guest an in-app and email notification containing the extension charge amount, the payment deadline, and a direct link to pay. This is an «include» of UC-QR-08. |
| **Preconditions** | `booking_extensions.status = pending_payment`. Extension bill has been generated. |
| **Postconditions** | Guest is informed of the extension charge and deadline. Guest can proceed to payment (UC-QR-10). |
| **Priority** | Must Have |
| **URS Refs** | URS-U-EXT-01, URS-U-EXT-02, URS-U-EXT-03 |
| **PRD Refs** | QR-11 |

**Normal Flow:**
1. System sends in-app notification: "Your extension request has been approved. An additional charge of [amount] is due by [deadline]. Pay now to confirm your extension and update your QR code validity."
2. System sends corresponding email notification with the extension bill PDF attached.
3. Notification includes a clear statement: "If you do not pay by [deadline], your booking will revert to the original check-out time and your QR code will not be extended."

---

### UC-QR-10: Guest Pays Extension Charge

| Field | Detail |
|---|---|
| **Use Case ID** | UC-QR-10 |
| **Use Case Name** | Guest Pays Extension Charge |
| **Actor(s)** | Guest |
| **Description** | Guest pays the additional extension charge via the online payment gateway within the configured payment deadline to confirm the booking extension. |
| **Preconditions** | `booking_extensions.status = pending_payment`. Payment deadline has not passed. |
| **Postconditions** | Payment is recorded. Extension status moves to `confirmed`. Booking dates updated. QR code `valid_until` extended (UC-QR-11). Guest receives confirmation. |
| **Priority** | Must Have |
| **URS Refs** | URS-U-EXT-01, URS-U-EXT-02 |
| **PRD Refs** | QR-11, QR-14 |

**Normal Flow:**
1. Guest navigates to their booking detail and finds the pending extension charge.
2. Guest clicks "Pay Extension Charge."
3. System redirects to the payment gateway with the extension bill amount.
4. Guest completes payment.
5. Gateway sends webhook to system.
6. System verifies webhook and updates `payments` record to `succeeded`.
7. System updates `booking_extensions.status = confirmed`.
8. System triggers UC-QR-11 (Confirm Extension: Update Booking + QR).

---

### UC-QR-11: Confirm Extension (Update Booking + QR)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-QR-11 |
| **Use Case Name** | Confirm Extension (Update Booking + QR) |
| **Actor(s)** | System |
| **Description** | After the extension payment is confirmed via webhook, the system updates the booking's check-out date/time to the extended values and updates the QR code's `valid_until` timestamp. The QR code is only extended at this point — not before. |
| **Preconditions** | Extension payment webhook received and verified. `booking_extensions.status` is being set to `confirmed`. |
| **Postconditions** | `bookings.check_out_date` and `bookings.check_out_time` updated to the new extended values. `qr_codes.valid_until` updated to the new check-out datetime. `booking_extensions.status = confirmed`. Guest receives a success notification. |
| **Priority** | Must Have |
| **URS Refs** | URS-A-QR-04 |
| **PRD Refs** | QR-12, QR-14 |

**Normal Flow:**
1. Payment webhook confirms extension payment (UC-QR-10, step 6–7).
2. System retrieves the `booking_extensions` record with new check-out values.
3. System updates `bookings.check_out_date` and `bookings.check_out_time`.
4. System updates `qr_codes.valid_until` to the new check-out datetime.
5. System sends guest a success notification: "Your stay has been extended. Your QR code is now valid until [new checkout datetime]."
6. System writes an audit log entry.

---

### UC-QR-12: Auto-Cancel Extension (Deadline Expired)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-QR-12 |
| **Use Case Name** | Auto-Cancel Extension (Deadline Expired) |
| **Actor(s)** | System |
| **Description** | A scheduled job runs every 1–5 minutes to detect extension records whose payment deadline has passed without confirmed payment. Such extensions are automatically cancelled and the booking is reverted to its original check-out date/time. The QR code is not updated. Guest is notified. |
| **Preconditions** | `booking_extensions.status = pending_payment` and `payment_deadline < now()`. |
| **Postconditions** | Extension status set to `cancelled`. Booking reverted to original dates (includes UC-QR-13). QR code NOT updated. Guest notified. Audit log entry created. |
| **Priority** | Must Have |
| **URS Refs** | URS-U-EXT-03, URS-A-QR-08 |
| **PRD Refs** | QR-13 |

**Normal Flow:**
1. Scheduled job runs every 1–5 minutes.
2. System queries all `booking_extensions` where `status = pending_payment` AND `payment_deadline < now()`.
3. For each: system sets `booking_extensions.status = cancelled`.
4. System reverts the booking to original dates (includes UC-QR-13).
5. System sends guest a notification: "Your extension request was not confirmed because payment was not received by the deadline. Your booking reverts to original check-out: [original datetime]."
6. System writes an audit log entry.

---

### UC-QR-13: Revert Booking to Original Dates / Times

| Field | Detail |
|---|---|
| **Use Case ID** | UC-QR-13 |
| **Use Case Name** | Revert Booking to Original Dates/Times |
| **Actor(s)** | System |
| **Description** | When an extension is cancelled due to deadline expiry (UC-QR-12), the system reverts the booking's check-out date and time to the original values that were stored in the `booking_extensions` record when the extension was created. The QR code's `valid_until` remains at the original check-out time and is not modified. |
| **Preconditions** | Extension has been cancelled (UC-QR-12). `booking_extensions.original_check_out_date` and `original_check_out_time` are stored. |
| **Postconditions** | `bookings.check_out_date` and `bookings.check_out_time` match original values. `qr_codes.valid_until` remains unchanged (at original checkout). |
| **Priority** | Must Have |
| **URS Refs** | URS-U-EXT-03 |
| **PRD Refs** | QR-13 |

**Normal Flow:**
1. Extension is cancelled (UC-QR-12, step 3).
2. System reads `booking_extensions.original_check_out_date` and `original_check_out_time`.
3. System restores `bookings.check_out_date` and `bookings.check_out_time` to the original values.
4. QR code `valid_until` is not modified (it already reflects the original checkout time).

---

## 12. Reporting & Analytics Module

---

### UC-RPT-01: View Analytics Dashboard

| Field | Detail |
|---|---|
| **Use Case ID** | UC-RPT-01 |
| **Use Case Name** | View Analytics Dashboard |
| **Actor(s)** | Admin |
| **Description** | Admin views the main reporting and analytics dashboard displaying a summary of key operational metrics: total bookings, total revenue, occupancy rate, cancellation rate, and guest feedback rating summary. The dashboard includes embedded charts (Chart.js) for booking trends and revenue. |
| **Preconditions** | Admin is logged in with reporting access. |
| **Postconditions** | Dashboard loads with real-time aggregated data from the database. KPI widgets and charts are rendered. |
| **Priority** | Must Have |
| **URS Refs** | URS-A-RPT-01 |
| **PRD Refs** | RPT-01, RPT-02 |

**Normal Flow:**
1. Admin navigates to Reporting & Analytics.
2. System aggregates data from `bookings`, `payments`, and `feedbacks` tables.
3. System renders KPI cards: total bookings (this month/year), total revenue (this month/year), occupancy rate (%), cancellation rate (%), average guest rating.
4. System renders embedded charts: booking trends chart (UC-RPT-02), revenue summary.
5. Admin can interact with charts to drill down.

---

### UC-RPT-02: View Booking Trends Chart

| Field | Detail |
|---|---|
| **Use Case ID** | UC-RPT-02 |
| **Use Case Name** | View Booking Trends Chart |
| **Actor(s)** | Admin |
| **Description** | Admin views a Chart.js line chart showing the number of bookings over a time period. Admin can toggle between daily, weekly, and monthly granularity to identify booking patterns. |
| **Preconditions** | Admin is on the analytics dashboard (UC-RPT-01). |
| **Postconditions** | Chart is rendered based on aggregated data from the `bookings` table at the selected time granularity. |
| **Priority** | Must Have |
| **URS Refs** | URS-A-RPT-02 |
| **PRD Refs** | RPT-03 |

**Normal Flow:**
1. Admin views the booking trends chart on the dashboard.
2. Admin selects granularity: Daily / Weekly / Monthly.
3. System re-queries `bookings` grouped by the selected period.
4. Chart.js re-renders the line chart with updated data points.

---

### UC-RPT-03: View Revenue Report

| Field | Detail |
|---|---|
| **Use Case ID** | UC-RPT-03 |
| **Use Case Name** | View Revenue Report |
| **Actor(s)** | Admin |
| **Description** | Admin views a detailed revenue report with configurable filters. The report shows total revenue, revenue by unit, and revenue by payment status for the selected period. Admin can export the report as PDF or CSV. |
| **Preconditions** | Payment records exist. Admin has reporting access. |
| **Postconditions** | Filtered revenue totals are displayed. Admin can download the report. |
| **Priority** | Must Have |
| **URS Refs** | URS-A-RPT-03 |
| **PRD Refs** | RPT-04 |

**Normal Flow:**
1. Admin navigates to Reporting → Revenue Report.
2. Admin applies filters: date range, homestay unit (all or specific), payment status.
3. System queries `payments` and `bookings` with the applied filters.
4. System displays: total revenue, itemised by booking, and summary by unit.
5. Admin clicks "Export PDF" or "Export CSV" to download (includes UC-RPT-06).

---

### UC-RPT-04: View Per-Unit Booking Breakdown

| Field | Detail |
|---|---|
| **Use Case ID** | UC-RPT-04 |
| **Use Case Name** | View Per-Unit Booking Breakdown |
| **Actor(s)** | Admin |
| **Description** | Admin views a breakdown of bookings segmented by homestay unit, comparing performance across properties with booking counts, occupancy rates, and revenue totals per unit. |
| **Preconditions** | Multiple homestay units have booking records. Admin has reporting access. |
| **Postconditions** | Bar chart and data table show booking distribution and revenue comparison across units. |
| **Priority** | Should Have |
| **URS Refs** | URS-A-RPT-04 |
| **PRD Refs** | RPT-05 |

**Normal Flow:**
1. Admin navigates to Reporting → Unit Breakdown.
2. System queries `bookings` grouped by `homestay_id`.
3. System renders a bar chart (Chart.js) and a data table with: unit name, total bookings, occupancy rate, total revenue.
4. Admin can filter by date range.

---

### UC-RPT-05: View Feedback & Rating Summary

| Field | Detail |
|---|---|
| **Use Case ID** | UC-RPT-05 |
| **Use Case Name** | View Feedback & Rating Summary |
| **Actor(s)** | Admin |
| **Description** | Admin views an aggregated summary of guest feedback ratings per unit, including average score, total review count, and rating distribution (star breakdown). This is a sub-component of the main analytics dashboard (UC-RPT-01). |
| **Preconditions** | Visible feedback records exist. Admin has reporting access. |
| **Postconditions** | Rating summary is rendered on the dashboard per unit. |
| **Priority** | Should Have |
| **URS Refs** | URS-A-RPT-06 |
| **PRD Refs** | RPT-06 |

**Normal Flow:**
1. Admin views the analytics dashboard.
2. System queries `feedbacks` where `is_visible = true`, grouped by `homestay_id`.
3. System calculates per unit: average rating, total review count, and rating distribution (1★ to 5★ counts).
4. System renders the summary widget on the dashboard.

---

### UC-RPT-06: Filter Reports

| Field | Detail |
|---|---|
| **Use Case ID** | UC-RPT-06 |
| **Use Case Name** | Filter Reports |
| **Actor(s)** | Admin |
| **Description** | Admin applies filters to any report view (revenue, bookings, feedback) to narrow the data. Available filters include date range, homestay unit, and payment/booking status. This is an «include» of UC-RPT-03 and other report use cases. |
| **Preconditions** | Admin is on a report page. |
| **Postconditions** | Report data is re-queried and displayed according to the selected filter criteria. |
| **Priority** | Must Have |
| **URS Refs** | URS-A-RPT-03 |
| **PRD Refs** | RPT-04 |

**Normal Flow:**
1. Admin selects one or more filter criteria from the filter panel.
2. System applies the filters to the underlying database query.
3. System re-renders the report view with filtered results.
4. Admin can clear all filters to reset to the default view.

---

### UC-RPT-07: Export Report (PDF / CSV)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-RPT-07 |
| **Use Case Name** | Export Report |
| **Actor(s)** | Admin |
| **Description** | Admin exports any report view (revenue, booking breakdown, feedback summary) as a downloadable PDF or CSV file for offline use, sharing, or archiving. |
| **Preconditions** | Admin is on a report page with data loaded. |
| **Postconditions** | A PDF (via `barryvdh/laravel-dompdf`) or CSV (via `maatwebsite/excel`) file is generated and downloaded to the admin's device. |
| **Priority** | Should Have |
| **URS Refs** | URS-A-RPT-05 |
| **PRD Refs** | RPT-07 |

**Normal Flow:**
1. Admin clicks "Export PDF" or "Export CSV" on a report page.
2. System collects the currently filtered report data.
3. For PDF: system renders the data through a Blade/DomPDF template.
4. For CSV: system uses `maatwebsite/excel` to generate a spreadsheet.
5. File is returned to the admin's browser as a download.

---

## 13. Guest Feedback Module

---

### UC-FB-01: Submit Rating & Feedback (After Stay Completed)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-FB-01 |
| **Use Case Name** | Submit Rating & Feedback |
| **Actor(s)** | Guest |
| **Description** | After a guest's stay is completed (booking status = `completed`), they can submit a star rating (1–5, required) and an optional written comment for the homestay unit. Each booking allows exactly one feedback submission. |
| **Preconditions** | Booking `status = completed`. No feedback has been submitted yet for this booking. Guest is logged in. |
| **Postconditions** | A `feedbacks` record is created with `is_visible = true`. The unit's average rating is recalculated. Guest sees a thank-you confirmation. |
| **Priority** | Must Have |
| **URS Refs** | URS-U-FB-01 |
| **PRD Refs** | FB-U-01, FB-U-02, FB-U-03 |

**Normal Flow:**
1. Guest navigates to "My Bookings" → "History."
2. Guest finds a completed booking and clicks "Leave a Review."
3. System verifies booking `status = completed` and no existing feedback for this booking.
4. System displays the feedback form: star rating widget (1–5 stars, required) and text area (optional comment).
5. Guest selects a star rating and optionally writes a comment.
6. Guest submits the form.
7. System saves the `feedbacks` record: `booking_id`, `homestay_id`, `user_id`, `rating`, `comment`, `is_visible = true`.
8. System recalculates the unit's average rating.
9. System displays a thank-you confirmation message.
10. The "Leave a Review" button is hidden/disabled for this booking going forward.

**Alternative Flows:**
- **A1 – Star rating not selected:** System highlights the star rating as required and blocks submission.
- **A2 – Feedback already submitted:** "Leave a Review" button is hidden and replaced with "View Your Review" for this booking.

**Exception Flows:**
- **E1 – Booking status is not `completed` (e.g., URL manipulation):** System returns a 403 or validation error. Feedback cannot be submitted.

---

### UC-FB-02: View Submitted Feedback (Guest)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-FB-02 |
| **Use Case Name** | View Submitted Feedback |
| **Actor(s)** | Guest |
| **Description** | Guest views all feedback they have previously submitted across their completed bookings, including the star rating, written comment, submission date, and any admin reply. All entries are read-only. |
| **Preconditions** | Guest has submitted at least one feedback entry. Guest is logged in. |
| **Postconditions** | Guest's own feedback entries are displayed in read-only format. |
| **Priority** | Should Have |
| **URS Refs** | URS-U-FB-02 |
| **PRD Refs** | FB-U-04 |

**Normal Flow:**
1. Guest navigates to "My Reviews" or accesses the review from a completed booking in history.
2. System retrieves all `feedbacks` records for the logged-in user.
3. System displays each entry: unit name, check-in/check-out dates, star rating, comment, submission date, and admin reply (if any).
4. Entries are read-only; guests cannot edit submitted feedback.

---

### UC-FB-03: View All Unit Feedback (Admin)

| Field | Detail |
|---|---|
| **Use Case ID** | UC-FB-03 |
| **Use Case Name** | View All Unit Feedback |
| **Actor(s)** | Admin |
| **Description** | Admin views all feedback and ratings submitted for homestay units. Admin can filter by unit and see all entries including hidden ones (with visibility status indicated). |
| **Preconditions** | Admin is logged in with feedback management access. |
| **Postconditions** | Admin can see all reviews, their ratings, visibility status, and any existing admin replies. |
| **Priority** | Must Have |
| **URS Refs** | URS-A-FB-01 |
| **PRD Refs** | FB-A-01 |

**Normal Flow:**
1. Admin navigates to Guest Feedback management.
2. System retrieves all `feedbacks` records (including hidden ones).
3. System displays: guest name, unit, booking reference, rating, comment, date, visibility status (`visible`/`hidden`), and admin reply status.
4. Admin can filter by unit or visibility status.
5. Admin can click an entry to respond (UC-FB-04) or moderate (UC-FB-05).

---

### UC-FB-04: Respond to Feedback

| Field | Detail |
|---|---|
| **Use Case ID** | UC-FB-04 |
| **Use Case Name** | Respond to Feedback |
| **Actor(s)** | Admin |
| **Description** | Admin writes and publishes a reply to a guest's feedback. The reply is displayed alongside the original review on the unit detail page, demonstrating responsiveness to guest experience. |
| **Preconditions** | Feedback exists with `is_visible = true`. Admin has `feedback.reply` permission. |
| **Postconditions** | `feedbacks.admin_reply` and `feedbacks.replied_at` are populated. Reply is visible to the guest on the unit detail page and in the guest's feedback history. |
| **Priority** | Should Have |
| **URS Refs** | URS-A-FB-02 |
| **PRD Refs** | FB-A-02 |

**Normal Flow:**
1. Admin navigates to Guest Feedback → selects a review → "Reply."
2. Admin types their response in the reply text area.
3. Admin clicks "Publish Reply."
4. System saves `admin_reply` and sets `replied_at = now()` on the `feedbacks` record.
5. Reply is immediately visible on the unit detail page alongside the original review.
6. If the guest checks their submitted feedback (UC-FB-02), they also see the admin's reply.

**Alternative Flows:**
- **A1 – Admin edits an existing reply:** Admin clicks "Edit Reply," modifies the text, and saves. `replied_at` is updated to the edit time.

---

### UC-FB-05: Moderate / Hide Feedback

| Field | Detail |
|---|---|
| **Use Case ID** | UC-FB-05 |
| **Use Case Name** | Moderate / Hide Feedback |
| **Actor(s)** | Admin |
| **Description** | Admin hides a feedback entry that violates content policies (e.g., offensive language, irrelevant content). The feedback is no longer visible to guests on the listing page but is retained in the database for record-keeping. The unit's average rating is recalculated excluding hidden reviews. |
| **Preconditions** | Feedback exists. Admin has `feedback.moderate` permission. |
| **Postconditions** | `feedbacks.is_visible = false`. Feedback is removed from public unit listing. Average rating recalculated excluding this entry. |
| **Priority** | Should Have |
| **URS Refs** | URS-A-FB-03 |
| **PRD Refs** | FB-A-03 |

**Normal Flow:**
1. Admin views a feedback entry and clicks "Hide / Moderate."
2. System prompts for a confirmation ("Hide this review from public view?").
3. Admin confirms.
4. System sets `feedbacks.is_visible = false`.
5. System recalculates the unit's average rating (now excluding this review).
6. Review is no longer shown on the guest-facing unit detail page.
7. Admin can reverse this action by clicking "Restore Visibility."

**Alternative Flows:**
- **A1 – Admin cancels the confirmation:** No changes are made.
- **A2 – Admin restores a hidden review:** System sets `is_visible = true` and recalculates the rating again.

---

### UC-FB-06: Display Average Rating on Listing Page

| Field | Detail |
|---|---|
| **Use Case ID** | UC-FB-06 |
| **Use Case Name** | Display Average Rating on Listing Page |
| **Actor(s)** | System |
| **Description** | The system calculates and displays the average star rating for each homestay unit on the listing page and unit detail page. Only visible (`is_visible = true`) feedback records are included in the calculation. The display format is: "4.2 ★ (18 reviews)". |
| **Preconditions** | At least one visible feedback record exists for the unit. |
| **Postconditions** | The calculated average rating and review count are shown on both the unit listing card and the unit detail page. The value updates dynamically whenever feedback is submitted, hidden, or restored. |
| **Priority** | Must Have |
| **URS Refs** | URS-U-FB-01 |
| **PRD Refs** | FB-A-04 |

**Normal Flow:**
1. Guest or admin views the homestay listing page or a unit detail page.
2. System queries `feedbacks` where `homestay_id = [unit]` AND `is_visible = true`.
3. System calculates the average rating (sum of ratings / count of visible reviews).
4. System displays "X.X ★ (N reviews)" on the unit card and detail page.

**Alternative Flows:**
- **A1 – No visible reviews exist for a unit:** System displays "No reviews yet" instead of a rating.

---

*End of Use Case Descriptions — HomeLodge v2.0*

---

## Appendix: Traceability Matrix

| Use Case ID | Use Case Name | Actor(s) | URS Refs | PRD Refs |
|---|---|---|---|---|
| UC-AUTH-01 | Register Account (Email/Password) | Guest | URS-U-AUTH-01, 06 | AUTH-01, 05 |
| UC-AUTH-02 | Register / Login via Google SSO | Guest, Admin | URS-U-AUTH-02, URS-A-AUTH-01 | AUTH-03 |
| UC-AUTH-03 | Login | Guest, Admin | URS-U-AUTH-03, URS-A-AUTH-01 | AUTH-01, 05, 06 |
| UC-AUTH-04 | Logout | Guest, Admin | URS-U-AUTH-04 | AUTH-04 |
| UC-AUTH-05 | Forgot Password | Guest, Admin | URS-U-AUTH-05, 06 | AUTH-07 |
| UC-AUTH-06 | View / Update Profile | Guest, Admin | URS-U-AUTH-08, URS-A-AUTH-03 | AUTH-08, 11 |
| UC-AUTH-07 | Show / Hide Password Toggle | Guest | URS-U-AUTH-07 | AUTH-09 |
| UC-AUTH-08 | Force Change Password | Guest | URS-U-AUTH-03, URS-A-USR-03 | AUTH-07, 10, USR-04 |
| UC-AUTH-09 | Account Lockout | System | URS-A-SET-02 | AUTH-05, 06, SET-SEC-01, 03 |
| UC-AUTH-10 | Auto Unlock Account | System | URS-A-SET-02 | AUTH-05, 06, SET-SEC-01 |
| UC-HS-01 | Browse Homestay Units | Guest | URS-U-BK-01 | HS-05 |
| UC-HS-02 | View Unit Details & Availability | Guest | URS-U-BK-01, URS-U-POL-01 | HS-05, 11 |
| UC-HS-03 | View House Policies | Guest | URS-U-POL-01 | HS-11 |
| UC-HS-04 | Create Homestay Unit | Admin | URS-A-HS-01, 05, 06, 08 | HS-01, 04, 06, 07, 08, 10 |
| UC-HS-05 | Edit Homestay Unit | Admin | URS-A-HS-02, 05, 06 | HS-02, 04, 07, 08 |
| UC-HS-06 | Deactivate / Delete Unit | Admin | URS-A-HS-03 | HS-03 |
| UC-HS-07 | View All Homestay Units (Admin) | Admin | URS-A-HS-04 | HS-05 |
| UC-HS-08 | Manage Unit House Policies | Admin | URS-A-HS-07 | HS-09, 10 |
| UC-HS-09 | Set Pricing & Check-in/out Times | Admin | URS-A-HS-05 | HS-04, 07, 08 |
| UC-HS-10 | Apply Default Policies on Creation | System | URS-A-HS-08 | HS-10, 12, SET-POL-01, 02 |
| UC-BK-01 | View Availability Calendar | Guest | URS-U-BK-02 | BK-U-01, 02 |
| UC-BK-02 | Select Check-in / Check-out Date & Time | Guest | URS-U-BK-03, 04 | BK-U-02, 03 |
| UC-BK-03 | Check Date Availability (Real-time) | System | URS-U-BK-04, URS-SYS-04 | BK-U-03, 04 |
| UC-BK-04 | Submit Booking | Guest | URS-U-BK-05 | BK-U-05, 06, BK-H-01 |
| UC-BK-05 | Temporary Hold (1-Day Payment Window) | System | URS-U-BK-04 | BK-H-01, 02 |
| UC-BK-06 | Auto-Cancel Booking (Payment Timeout) | System | URS-U-BK-04 | BK-H-01, 02, 03 |
| UC-BK-07 | View Current Bookings | Guest | URS-U-BK-06 | BK-U-05 |
| UC-BK-08 | View Booking History | Guest | URS-U-BK-07 | BK-U-06 |
| UC-BK-09 | View Booking Details | Guest | URS-U-BK-08 | BK-U-06 |
| UC-BK-10 | Cancel Booking (Guest) | Guest | URS-U-BK-09, 10 | BK-U-07, 08 |
| UC-BK-11 | View Cancellation Policy & Refund Info | Guest | URS-U-BK-10 | BK-U-08 |
| UC-BK-12 | View Booking Calendar (Admin) | Admin | URS-A-BK-01 | BK-A-01 |
| UC-BK-13 | Create Booking on Behalf of User | Admin | URS-A-BK-02 | BK-A-02 |
| UC-BK-14 | Edit Booking (Admin) | Admin | URS-A-BK-03 | BK-A-04 |
| UC-BK-15 | Delete Booking (Admin) | Admin | URS-A-BK-03 | BK-A-04 |
| UC-BK-16 | Cancel Booking on Behalf of User | Admin | URS-A-BK-04 | BK-A-05 |
| UC-BK-17 | Filter Booking List | Admin | URS-A-BK-05 | BK-A-06 |
| UC-BK-18 | Block Dates | Admin | URS-A-BK-06, 07 | BK-A-07 |
| UC-PAY-01 | Make Payment (Online Gateway) | Guest, Payment Gateway | URS-U-PAY-01, 02, 05 | PAY-U-01, 02 |
| UC-PAY-02 | View Payment Bill | Guest | URS-U-PAY-02 | PAY-U-01 |
| UC-PAY-03 | View / Download Receipt | Guest | URS-U-PAY-03 | PAY-U-03 |
| UC-PAY-04 | View Payment History | Guest | URS-U-PAY-04 | PAY-U-04 |
| UC-PAY-05 | Process Payment Webhook | Payment Gateway, System | URS-U-PAY-01 | PAY-U-05, PAY-A-04 |
| UC-PAY-06 | Auto-Generate Bill & Payment Number | System | URS-A-PAY-01 | PAY-A-01 |
| UC-PAY-07 | View Billing List (Admin) | Admin | URS-A-PAY-02 | PAY-A-02 |
| UC-PAY-08 | View Payment List (Admin) | Admin | URS-A-PAY-03 | PAY-A-03 |
| UC-PAY-09 | Regenerate Bill / Receipt (Admin) | Admin | URS-A-PAY-04 | PAY-A-04, 05 |
| UC-NOTIF-01 | Receive In-App Notification | Guest, Admin | URS-U-NOTIF-01, URS-A-NOTIF-01 | NOTIF-01, 02 |
| UC-NOTIF-02 | Receive Email Notification | Guest, Admin | URS-U-NOTIF-01, URS-A-NOTIF-01 | NOTIF-02, SET-GEN-04 |
| UC-NOTIF-03 | Receive Payment Reminder | Guest | URS-U-PAY-06 | NOTIF-04 |
| UC-NOTIF-04 | Receive Check-in / Check-out Reminder | Guest, Admin | URS-U-NOTIF-02, URS-A-NOTIF-02 | NOTIF-03 |
| UC-NOTIF-05 | Receive QR Code Reminder (Admin) | Admin | URS-A-NOTIF-02 | NOTIF-05 |
| UC-NOTIF-06 | View Booking in Google Calendar | Guest, Admin | URS-U-NOTIF-03, URS-A-NOTIF-03 | NOTIF-06, 07 |
| UC-NOTIF-07 | Toggle Email Notifications | Admin | URS-A-SET-04 | SET-GEN-04 |
| UC-CHAT-01 | Send Message | Guest, Admin | URS-U-CHAT-01, URS-A-CHAT-01 | CHAT-01, 02 |
| UC-CHAT-02 | Receive Message (Real-time) | Guest, Admin | URS-U-CHAT-01, URS-A-CHAT-01 | CHAT-02 |
| UC-CHAT-03 | View Chat History | Guest, Admin | URS-U-CHAT-01, URS-A-CHAT-01 | CHAT-03 |
| UC-USR-01 | Create User Account | Admin | URS-A-USR-01 | USR-01 |
| UC-USR-02 | Edit User Account | Admin | URS-A-USR-01 | USR-02 |
| UC-USR-03 | Delete User Account | Admin | URS-A-USR-01 | USR-03 |
| UC-USR-04 | Reset User Password | Admin | URS-A-USR-02, 03 | USR-04, 05 |
| UC-USR-05 | Activate / Deactivate User Account | Admin | URS-A-USR-04 | USR-06 |
| UC-USR-06 | Force Password Change (Triggered by Admin Reset) | Admin, System | URS-A-USR-03 | USR-04, AUTH-10 |
| UC-ROLE-01 | Create Role | Admin | URS-A-ROLE-01 | ROLE-01 |
| UC-ROLE-02 | Edit Role | Admin | URS-A-ROLE-01 | ROLE-01 |
| UC-ROLE-03 | Delete Role | Admin | URS-A-ROLE-01, 03 | ROLE-01, 02 |
| UC-ROLE-04 | Assign Permissions to Role | Admin | URS-A-ROLE-02 | ROLE-03 |
| UC-ROLE-05 | Create Permission | Admin | URS-A-PERM-01 | PERM-01 |
| UC-ROLE-06 | Edit Permission | Admin | URS-A-PERM-01 | PERM-01 |
| UC-ROLE-07 | Delete Permission | Admin | URS-A-PERM-01, 02 | PERM-01, 02 |
| UC-SET-01 | Configure SMTP Settings | Admin | URS-A-SET-01 | SET-SMTP-01 |
| UC-SET-02 | Configure Security Settings | Admin | URS-A-SET-02 | SET-SEC-01, 02, 03 |
| UC-SET-03 | Configure Refund Policy Parameters | Admin | URS-A-SET-03 | SET-GEN-02 |
| UC-SET-04 | Configure Payment & Billing Options | Admin | URS-A-PAY-01 | SET-GEN-03 |
| UC-SET-06 | Configure Extension Charge Rates | Admin | URS-A-SET-05, 06 | SET-EXT-01, 02 |
| UC-SET-07 | Configure System-Wide Default Extension Payment Window | Admin | URS-A-SET-08 | SET-EXT-04 |
| UC-SET-08 | Manage Default Homestay Policies | Admin | URS-A-SET-07 | SET-POL-01, 02 |
| UC-SET-09 | Configure Per-Unit Extension Payment Window | Admin | URS-A-HS-09, URS-A-QR-07 | HS-13, SET-EXT-04 |
| UC-AUDIT-01 | View Audit Trail | Admin | URS-A-AUDIT-01 | AUDIT-01, 03 |
| UC-AUDIT-02 | Filter Audit Logs | Admin | URS-A-AUDIT-01 | AUDIT-01 |
| UC-AUDIT-03 | Log User Actions | System | URS-A-AUDIT-01 | AUDIT-02, 03 |
| UC-AUDIT-04 | Log System Events | System | URS-A-AUDIT-01 | AUDIT-02, 03 |
| UC-QR-01 | Receive QR Code Upon Booking Confirmation | Guest | URS-A-QR-01 | QR-01, 06 |
| UC-QR-02 | Use QR Code for Door Access | Guest | URS-A-QR-01 | QR-02 |
| UC-QR-03 | Auto-Invalidate QR Code (After Check-out) | System | URS-A-QR-01 | QR-03 |
| UC-QR-04 | Regenerate QR Code (For Housekeeping) | Admin | URS-A-QR-02 | QR-04 |
| UC-QR-05 | Mark Housekeeping Complete | Admin | URS-A-QR-03 | QR-05 |
| UC-QR-06 | Auto-Generate QR Code (For Next Guest) | System | URS-A-QR-03 | QR-05 |
| UC-QR-07 | Initiate Booking Extension (Time or Date) | Admin | URS-A-QR-04, 05, 06 | QR-07, 08, 09, 10 |
| UC-QR-08 | Generate Extension Bill & Set Payment Deadline | System | URS-A-QR-06 | QR-09, 10, 11, HS-13, SET-EXT-04 |
| UC-QR-09 | Notify Guest of Extension Bill & Deadline | System | URS-U-EXT-01, 02, 03 | QR-11 |
| UC-QR-10 | Guest Pays Extension Charge | Guest | URS-U-EXT-01, 02 | QR-11, 14 |
| UC-QR-11 | Confirm Extension (Update Booking + QR) | System | URS-A-QR-04 | QR-12, 14 |
| UC-QR-12 | Auto-Cancel Extension (Deadline Expired) | System | URS-U-EXT-03, URS-A-QR-08 | QR-13 |
| UC-QR-13 | Revert Booking to Original Dates/Times | System | URS-U-EXT-03 | QR-13 |
| UC-RPT-01 | View Analytics Dashboard | Admin | URS-A-RPT-01 | RPT-01, 02 |
| UC-RPT-02 | View Booking Trends Chart | Admin | URS-A-RPT-02 | RPT-03 |
| UC-RPT-03 | View Revenue Report | Admin | URS-A-RPT-03 | RPT-04 |
| UC-RPT-04 | View Per-Unit Booking Breakdown | Admin | URS-A-RPT-04 | RPT-05 |
| UC-RPT-05 | View Feedback & Rating Summary | Admin | URS-A-RPT-06 | RPT-06 |
| UC-RPT-06 | Filter Reports | Admin | URS-A-RPT-03 | RPT-04 |
| UC-RPT-07 | Export Report (PDF / CSV) | Admin | URS-A-RPT-05 | RPT-07 |
| UC-FB-01 | Submit Rating & Feedback | Guest | URS-U-FB-01 | FB-U-01, 02, 03 |
| UC-FB-02 | View Submitted Feedback (Guest) | Guest | URS-U-FB-02 | FB-U-04 |
| UC-FB-03 | View All Unit Feedback (Admin) | Admin | URS-A-FB-01 | FB-A-01 |
| UC-FB-04 | Respond to Feedback | Admin | URS-A-FB-02 | FB-A-02 |
| UC-FB-05 | Moderate / Hide Feedback | Admin | URS-A-FB-03 | FB-A-03 |
| UC-FB-06 | Display Average Rating on Listing Page | System | URS-U-FB-01 | FB-A-04 |