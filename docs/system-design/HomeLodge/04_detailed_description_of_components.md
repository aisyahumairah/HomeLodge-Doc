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

**Entity: User**

| Entity Name | User |
| :--- | :--- |
| **Method Name** | register |
| **Input** | name, email, password, password_confirmation |
| **Output** | User |
| **Algorithm** | 1. Start 2. Validate input fields (name, email, password, password_confirmation) 3. Create new User record. 5. Return User. 6. End |

| Entity Name | User |
| :--- | :--- |
| **Method Name** | login |
| **Input** | email, password |
| **Output** | Boolean |
| **Algorithm** | 1. Start 2. Validate input fields (email, password) 3. Verify credentials and create session. 5. Return Boolean. 6. End |

| Entity Name | User |
| :--- | :--- |
| **Method Name** | logout |
| **Input** |  |
| **Output** | void |
| **Algorithm** | 1. Start 2. Destroy user session. 4. Return success. 5. End |



#### 4.2.1.2 Sequence Diagrams

**a) SD001: Register Account (Email / Password)**

```
title SD001: Register Account (Email / Password)
actor Guest
participant RegisterView
participant RegisterController
participant User
database Database
participant EmailService

Guest->RegisterView: Open registration page
RegisterView-->Guest: Display Registration Form
Guest->RegisterView: Enter name, email, password, confirm password
RegisterView->RegisterController: POST /register
RegisterController->RegisterController: Validate input
RegisterController->User: validate & create(data)
User->Database: Check if email already exists
alt Email already in use
  Database-->User: Email taken
  User-->RegisterController: Validation error
  RegisterController-->RegisterView: Redirect back with errors
  RegisterView-->Guest: Highlight email field (suggest sign-in)
else Email is unique
  Database-->User: Email available
  User->User: Validate password strength
  User->Database: Hash & save password, create account
  Database-->User: Account created
  User-->RegisterController: Return User object
  RegisterController->EmailService: Send welcome email
  EmailService-->RegisterController: Email sent
  RegisterController-->RegisterView: Redirect to sign-in page
  RegisterView-->Guest: Show success message
end
```

**b) SD002: Login (Email/Password)**

```
title SD002: Login (Email/Password)
actor User
participant LoginView
participant AuthController
participant UserModel as User
database Database
participant Session

User->LoginView: Enter email and password
LoginView->AuthController: POST /login
AuthController->User: attemptLogin(credentials)
User->Database: Verify credentials
alt Invalid credentials
  Database-->User: Not matched
  User-->AuthController: Login failed
  AuthController-->LoginView: Redirect back with errors
  LoginView-->User: Show generic error
else Valid credentials
  Database-->User: Credentials OK
  User->Database: Check if password change is required
  alt Password change required
    Database-->User: True
    User-->AuthController: Requires password change
    AuthController-->LoginView: Redirect to forced password change
    LoginView-->User: Display forced change page
  else No change required
    Database-->User: False
    User->Database: Record sign-in time
    Database-->User: Updated
    User-->AuthController: Login success
    AuthController->Session: create session & tokens
    Session-->AuthController: Session active
    AuthController-->LoginView: Redirect to dashboard
    LoginView-->User: Display dashboard
  end
end
```

**c) SD003: Login via Google SSO**

```
title SD003: Login via Google SSO
actor User
participant LoginView
participant SocialiteController
participant GoogleOAuth
participant UserModel as User
database Database
participant Session

User->LoginView: Click "Continue with Google"
LoginView->SocialiteController: GET /auth/google
SocialiteController->GoogleOAuth: Redirect to Google sign-in
GoogleOAuth-->User: Display Google sign-in
User->GoogleOAuth: Authenticate & grant permission
GoogleOAuth->SocialiteController: Callback with profile
SocialiteController->User: findOrCreateByGoogle(profile)
User->Database: Check if account exists for email
alt Account exists
  Database-->User: Account found
  User->Database: Link Google identity
  Database-->User: Linked
else No account exists
  Database-->User: Not found
  User->Database: Create account with Guest role
  Database-->User: Account created
end
User-->SocialiteController: Return User
SocialiteController->Session: create session
Session-->SocialiteController: Session active
SocialiteController-->LoginView: Redirect to dashboard
LoginView-->User: Display dashboard
```

**d) SD004: Logout**

```
title SD004: Logout
actor User
participant DashboardView
participant AuthController
database Database
participant Session

User->DashboardView: Click Logout
DashboardView->AuthController: POST /logout
AuthController->Session: end session
Session->Database: Clear remember-me tokens
Database-->Session: Cleared
Session-->AuthController: Session ended
AuthController-->DashboardView: Redirect to sign-in page
DashboardView-->User: Display sign-in page
```

**e) SD005: Forgot Password (Reset via Email)**

```
title SD005: Forgot Password (Reset via Email)
actor User
participant ForgotPasswordView
participant PasswordResetController
participant UserModel as User
database Database
participant EmailService

User->ForgotPasswordView: Click "Forgot Password"
ForgotPasswordView-->User: Display Forgot Password form
User->ForgotPasswordView: Enter registered email
ForgotPasswordView->PasswordResetController: POST /forgot-password
PasswordResetController->User: checkEmailExists(email)
User->Database: Query email
alt Email found
  Database-->User: Email exists
  User->Database: Generate time-limited reset link
  Database-->User: Link created
  User-->PasswordResetController: Return reset link
  PasswordResetController->EmailService: sendResetLink(email, link)
  EmailService-->PasswordResetController: Email sent
else Email not found
  Database-->User: Not found
  User-->PasswordResetController: Null
end
PasswordResetController-->ForgotPasswordView: Return generic success message
ForgotPasswordView-->User: "If account exists, a link was sent"

User->EmailService: Open email & click reset link
EmailService->PasswordResetController: GET /reset-password/{token}
PasswordResetController->Database: Check link validity
alt Link valid
  Database-->PasswordResetController: Valid
  PasswordResetController-->ForgotPasswordView: Show reset form
  User->ForgotPasswordView: Enter new password
  ForgotPasswordView->PasswordResetController: POST /reset-password
  PasswordResetController->User: updatePassword(newPassword)
  User->Database: Save hashed password, mark link used
  Database-->User: Updated
  User-->PasswordResetController: Success
  PasswordResetController-->ForgotPasswordView: Redirect to sign-in
  ForgotPasswordView-->User: Show success message
else Link invalid
  Database-->PasswordResetController: Invalid
  PasswordResetController-->ForgotPasswordView: Redirect with error
  ForgotPasswordView-->User: "Link is invalid or expired"
end
```

**f) SD006: View / Update Profile**

```
title SD006: View / Update Profile
actor User
participant ProfileView
participant ProfileController
participant UserModel as User
database Database

User->ProfileView: Go to Profile page
ProfileView->ProfileController: GET /profile
ProfileController->User: getProfile()
User->Database: Retrieve profile data
Database-->User: Return profile
User-->ProfileController: Return User
ProfileController-->ProfileView: Render profile
ProfileView-->User: Display details

User->ProfileView: Edit fields / update photo
User->ProfileView: Click Save
ProfileView->ProfileController: PUT /profile
ProfileController->ProfileController: Validate input
alt Validation fails
  ProfileController-->ProfileView: Redirect back with errors
  ProfileView-->User: Highlight invalid fields
else Valid
  ProfileController->User: updateProfile(data)
  User->Database: Save updated profile
  Database-->User: Updated
  User-->ProfileController: Success
  ProfileController-->ProfileView: Redirect with success
  ProfileView-->User: Profile updated successfully
end
```

**g) SD007: Force Change Password**

```
title SD007: Force Change Password
actor User
participant ChangePasswordView
participant PasswordController
participant UserModel as User
database Database

User->ChangePasswordView: Login with temp password
ChangePasswordView->PasswordController: GET /force-change-password
PasswordController->User: checkForcedFlag()
User->Database: Query forced change flag
Database-->User: Flag active
User-->PasswordController: True
PasswordController-->ChangePasswordView: Render Change Your Password page
ChangePasswordView-->User: Display form (blocks other pages)

User->ChangePasswordView: Enter new password & confirm
ChangePasswordView->PasswordController: POST /force-change-password
PasswordController->PasswordController: Validate strength
alt Validation fails
  PasswordController-->ChangePasswordView: Redirect back with errors
  ChangePasswordView-->User: Show failing rules
else Valid
  PasswordController->User: updatePassword(newPassword)
  User->Database: Save password, clear forced-change flag
  Database-->User: Updated
  User-->PasswordController: Success
  PasswordController-->ChangePasswordView: Redirect to dashboard
  ChangePasswordView-->User: Display dashboard
end
```


---

### 4.2.2 P002: Homestay Management Subsystem

#### 4.2.2.1 Class Diagram



> **Note:** Include a UML class diagram showing: `HomestayUnit`, `HousePolicy`, `HomestayImage`, `HomestayController`, `HomestayService`.

**Entity: HomestayUnit**

| Entity Name | HomestayUnit |
| :--- | :--- |
| **Method Name** | createUnit |
| **Input** | name, description, location, base_price, deposit, check_in_time, check_out_time, images, policies |
| **Output** | HomestayUnit |
| **Algorithm** | 1. Start 2. Validate input fields (name, description, location, base_price, deposit, check_in_time, check_out_time, images, policies) 3. Create new HomestayUnit record. 5. Return HomestayUnit. 6. End |

| Entity Name | HomestayUnit |
| :--- | :--- |
| **Method Name** | updateUnit |
| **Input** | data |
| **Output** | HomestayUnit |
| **Algorithm** | 1. Start 2. Validate input fields (data) 3. Find HomestayUnit record and update with provided data. 5. Return HomestayUnit. 6. End |

| Entity Name | HomestayUnit |
| :--- | :--- |
| **Method Name** | deleteUnit |
| **Input** | id |
| **Output** | Boolean |
| **Algorithm** | 1. Start 2. Validate input fields (id) 3. Find HomestayUnit record and delete or deactivate it. 5. Return Boolean. 6. End |

**Entity: HousePolicy**

| Entity Name | HousePolicy |
| :--- | :--- |
| **Method Name** | addPolicy |
| **Input** | data |
| **Output** | HousePolicy |
| **Algorithm** | 1. Start 2. Validate input fields (data) 3. Create new HousePolicy record. 5. Return HousePolicy. 6. End |

| Entity Name | HousePolicy |
| :--- | :--- |
| **Method Name** | removePolicy |
| **Input** | id |
| **Output** | Boolean |
| **Algorithm** | 1. Start 2. Validate input fields (id) 3. Find HousePolicy record and delete or deactivate it. 5. Return Boolean. 6. End |

**Entity: HomestayImage**

| Entity Name | HomestayImage |
| :--- | :--- |
| **Method Name** | uploadImage |
| **Input** | file, unit_id |
| **Output** | HomestayImage |
| **Algorithm** | 1. Start 2. Validate input fields (file, unit_id) 3. Perform uploadImage business logic for HomestayImage. 5. Return HomestayImage. 6. End |

| Entity Name | HomestayImage |
| :--- | :--- |
| **Method Name** | deleteImage |
| **Input** | id |
| **Output** | Boolean |
| **Algorithm** | 1. Start 2. Validate input fields (id) 3. Find HomestayImage record and delete or deactivate it. 5. Return Boolean. 6. End |



#### 4.2.2.2 Sequence Diagrams

**a) SD008: Browse Homestay Units**

```
title SD008: Browse Homestay Units
actor Guest
participant LandingPageView
participant HomestayController
participant HomestayUnitModel as HomestayUnit
database Database

Guest->LandingPageView: Open homestay listing page
LandingPageView->HomestayController: GET /units
HomestayController->HomestayUnit: getAllActive()
HomestayUnit->Database: Retrieve all active units
Database-->HomestayUnit: Return unit list
HomestayUnit-->HomestayController: Return models
HomestayController-->LandingPageView: Render view with units
alt No active units
  LandingPageView-->Guest: "No homestay units are currently available"
else Units found
  LandingPageView-->Guest: Display unit cards
end
```

**b) SD009: View Unit Details & Availability**

```
title SD009: View Unit Details & Availability
actor Guest
participant UnitDetailView
participant HomestayController
participant HomestayUnitModel as HomestayUnit
participant BookingModel as Booking
database Database

Guest->UnitDetailView: Click on a unit card
UnitDetailView->HomestayController: GET /units/{id}
HomestayController->HomestayUnit: getDetails({id})
HomestayUnit->Database: Retrieve unit details
Database-->HomestayUnit: Return data
HomestayUnit-->HomestayController: Return unit details
HomestayController->Booking: getAvailabilityForUnit({id})
Booking->Database: Retrieve date states
Database-->Booking: Return states
Booking-->HomestayController: Return availability
HomestayController-->UnitDetailView: Render unit detail view
UnitDetailView-->Guest: Display full details & calendar
```

**c) SD010: Create Homestay Unit**

```
title SD010: Create Homestay Unit
actor Admin
participant HomestayView
participant HomestayController
participant HomestayService
participant HomestayUnitModel as HomestayUnit
database Database

Admin->HomestayView: Enter unit details & Upload photos -> Save
HomestayView->HomestayController: POST /admin/units
HomestayController->HomestayController: Validate fields & files
alt Validation fails
  HomestayController-->HomestayView: Redirect with errors
  HomestayView-->Admin: Highlight errors
else Valid
  HomestayController->HomestayService: createUnit(data)
  HomestayService->HomestayUnit: populate data & instantiate
  HomestayUnit->Database: Save unit record (active)
  Database-->HomestayUnit: Saved
  HomestayService->Database: Copy default house rules to unit
  Database-->HomestayService: Rules copied
  HomestayService->Database: Write audit log entry
  Database-->HomestayService: Logged
  HomestayService-->HomestayController: Return new unit
  HomestayController-->HomestayView: Redirect to listing
  HomestayView-->Admin: Show success message
end
```

**d) SD011: Edit Homestay Unit**

```
title SD011: Edit Homestay Unit
actor Admin
participant HomestayView
participant HomestayController
participant HomestayService
participant HomestayUnitModel as HomestayUnit
database Database

Admin->HomestayView: Select unit -> Edit
HomestayView->HomestayController: GET /admin/units/{id}/edit
HomestayController->HomestayUnit: find({id})
HomestayUnit->Database: Retrieve current unit
Database-->HomestayUnit: Return data
HomestayUnit-->HomestayController: Return model
HomestayController-->HomestayView: Render edit form
HomestayView-->Admin: Pre-filled form
Admin->HomestayView: Modify fields -> Save
HomestayView->HomestayController: PUT /admin/units/{id}
HomestayController->HomestayController: Validate changes
alt Invalid data
  HomestayController-->HomestayView: Redirect with errors
  HomestayView-->Admin: Highlight problem fields
else Valid
  HomestayController->HomestayService: updateUnit(id, data)
  HomestayService->HomestayUnit: update(data)
  HomestayUnit->Database: Save updated record
  Database-->HomestayUnit: Saved
  HomestayService->Database: Write audit log entry
  Database-->HomestayService: Logged
  HomestayService-->HomestayController: Success
  HomestayController-->HomestayView: Redirect to listing
  HomestayView-->Admin: Show success message
end
```

**e) SD012: Deactivate / Delete Unit**

```
title SD012: Deactivate / Delete Unit
actor Admin
participant HomestayView
participant HomestayController
participant BookingModel as Booking
participant HomestayUnitModel as HomestayUnit
database Database

Admin->HomestayView: Click Deactivate / Delete
HomestayView->HomestayController: DELETE /admin/units/{id}
HomestayController->Booking: checkUpcomingBookings({id})
Booking->Database: Query upcoming confirmed bookings
Database-->Booking: Return count
alt Upcoming bookings exist
  Booking-->HomestayController: Conflicting bookings
  HomestayController-->HomestayView: Block action, list conflicts
  HomestayView-->Admin: Display conflicts, require manual resolution
else No conflicts
  Booking-->HomestayController: Safe to delete
  HomestayController-->HomestayView: Show confirmation dialog
  Admin->HomestayView: Confirm
  HomestayView->HomestayController: POST /admin/units/{id}/confirm
  HomestayController->HomestayUnit: deactivate()
  HomestayUnit->Database: Set status = inactive
  Database-->HomestayUnit: Updated
  HomestayController->Database: Write audit log entry
  Database-->HomestayController: Logged
  HomestayController-->HomestayView: Redirect with success
  HomestayView-->Admin: Unit removed from guest listing
end
```

**f) SD013: View All Units List**

```
title SD013: View All Units List (Admin)
actor Admin
participant HomestayView
participant HomestayController
participant HomestayUnitModel as HomestayUnit
database Database

Admin->HomestayView: Go to Homestay Management
HomestayView->HomestayController: GET /admin/units
HomestayController->HomestayUnit: getAll()
HomestayUnit->Database: Retrieve all unit records
Database-->HomestayUnit: Return list
HomestayUnit-->HomestayController: Return models
HomestayController-->HomestayView: Render view
HomestayView-->Admin: Display table with units
Admin->HomestayView: Search or filter by status
HomestayView->HomestayController: GET /admin/units?filter
HomestayController->HomestayUnit: getFiltered(filter)
HomestayUnit->Database: Apply filter
Database-->HomestayUnit: Return filtered results
HomestayUnit-->HomestayController: Return models
HomestayController-->HomestayView: Update view
HomestayView-->Admin: Update displayed list
```


---

### 4.2.3 P003: Booking Subsystem

#### 4.2.3.1 Class Diagram



> **Note:** Include a UML class diagram showing: `Booking`, `BookingController`, `BookingService`, `HomestayUnit`.

**Entity: Booking**

| Entity Name | Booking |
| :--- | :--- |
| **Method Name** | createBooking |
| **Input** | user_id, unit_id, check_in_date, check_in_time, check_out_date, check_out_time |
| **Output** | Booking |
| **Algorithm** | 1. Start 2. Validate input fields (user_id, unit_id, check_in_date, check_in_time, check_out_date, check_out_time) 3. Create new Booking record. 5. Return Booking. 6. End |

| Entity Name | Booking |
| :--- | :--- |
| **Method Name** | cancelBooking |
| **Input** | booking_id, cancelled_by |
| **Output** | Boolean |
| **Algorithm** | 1. Start 2. Validate input fields (booking_id, cancelled_by) 3. Find Booking record and delete or deactivate it. 5. Return Boolean. 6. End |

| Entity Name | Booking |
| :--- | :--- |
| **Method Name** | isExpired |
| **Input** |  |
| **Output** | Boolean |
| **Algorithm** | 1. Start 2. Perform isExpired business logic for Booking. 4. Return Boolean. 5. End |



#### 4.2.3.2 Sequence Diagrams

**a) SD014: View Availability & Select Dates**

```
title SD014: View Availability & Select Dates
actor Guest
participant BookingFormView
participant BookingController
participant BookingModel as Booking
database Database

Guest->BookingFormView: View unit detail or booking form
BookingFormView->BookingController: GET /availability/{unit_id}
BookingController->Booking: getAvailability(unit_id)
Booking->Database: Retrieve availability data
Database-->Booking: Return date states
Booking-->BookingController: Return states
BookingController-->BookingFormView: Render colour-coded calendar
BookingFormView-->Guest: Display availability calendar
Guest->BookingFormView: Click check-in date
BookingFormView-->Guest: Highlight check-in date
Guest->BookingFormView: Click check-out date
BookingFormView->BookingController: GET /availability/check?dates
BookingController->Booking: checkRealTimeAvailability(dates)
Booking->Database: Query for conflicts
alt Dates unavailable
  Database-->Booking: Conflict found
  Booking-->BookingController: Unavailable
  BookingController-->BookingFormView: Validation error
  BookingFormView-->Guest: Highlight conflict
else Dates available
  Database-->Booking: Available
  Booking-->BookingController: Available
  BookingController-->BookingFormView: Confirm availability
  BookingFormView-->Guest: Show nights count & cost
end
```

**b) SD015: Submit Booking**

```
title SD015: Submit Booking
actor Guest
participant BookingFormView
participant BookingController
participant BookingService
participant BookingModel as Booking
participant BillModel as Bill
participant NotificationService
database Database

Guest->BookingFormView: Click Confirm Booking
BookingFormView->BookingController: POST /bookings
BookingController->BookingService: createBooking(data)
BookingService->Database: Double-check date availability
alt Dates unavailable
  Database-->BookingService: Conflict found
  BookingService-->BookingController: Throw conflict error
  BookingController-->BookingFormView: Redirect back with error
  BookingFormView-->Guest: Highlight conflict
else Available
  Database-->BookingService: Available
  BookingService->Booking: Instantiate with data (status: awaiting payment)
  Booking->Database: Create booking record
  Database-->Booking: Record created
  BookingService->Bill: generateBill(booking_id)
  Bill->Database: Create bill record
  Database-->Bill: Bill created
  BookingService->Database: Temporarily reserve selected dates
  Database-->BookingService: Dates reserved
  BookingService->NotificationService: sendPaymentNotification(guest, bill)
  NotificationService-->BookingService: Notification sent
  BookingService-->BookingController: Return new Booking
  BookingController-->BookingFormView: Redirect to payment page
  BookingFormView-->Guest: Show payment page
end
```

**c) SD016: View Bookings**

```
title SD016: View Bookings
actor User
participant BookingView
participant BookingController
participant BookingModel as Booking
database Database

User->BookingView: Go to My Bookings (or Booking Management)
BookingView->BookingController: GET /bookings
BookingController->Booking: getBookingsForUser(user_id)
Booking->Database: Retrieve bookings
Database-->Booking: Return bookings
Booking-->BookingController: Return list
BookingController-->BookingView: Render view
BookingView-->User: Display bookings in tabs (Current / History)
User->BookingView: Click a booking
BookingView->BookingController: GET /bookings/{id}
BookingController-->User: Navigate to booking detail
```

**d) SD017: View Booking Details**

```
title SD017: View Booking Details
actor User
participant BookingDetailView
participant BookingController
participant BookingModel as Booking
database Database

User->BookingDetailView: Click booking from list
BookingDetailView->BookingController: GET /bookings/{id}
BookingController->Booking: getFullDetails({id})
Booking->Database: Retrieve booking, bill, QR code, extensions
Database-->Booking: Return data
Booking-->BookingController: Return detailed record
BookingController-->BookingDetailView: Render view
BookingDetailView-->User: Display unit name, dates, amount, status, QR
User->BookingDetailView: Click Download Bill/Receipt
BookingDetailView->BookingController: GET /bookings/{id}/bill
BookingController-->User: Return PDF file
```

**e) SD018: Cancel Booking**

```
title SD018: Cancel Booking
actor Guest
participant BookingDetailView
participant BookingController
participant BookingService
participant BookingModel as Booking
participant PaymentService
participant NotificationService
database Database

Guest->BookingDetailView: Click Cancel Booking
BookingDetailView->BookingController: POST /bookings/{id}/cancel
BookingController->BookingService: getRefundAmount(booking_id)
BookingService->Booking: Calculate refund
Booking-->BookingService: Return amount
BookingService-->BookingController: Return details
BookingController-->BookingDetailView: Ask confirmation
BookingDetailView-->Guest: Show refund amount & confirm
Guest->BookingDetailView: Confirm cancellation
BookingDetailView->BookingController: POST /bookings/{id}/confirm-cancel
BookingController->BookingService: processCancellation(booking_id)
BookingService->Booking: Set status = cancelled
Booking->Database: Save status & Release dates
Database-->Booking: Saved
alt Payment was made
  BookingService->PaymentService: processRefund(booking_id, amount)
  PaymentService->Database: Record refund
  Database-->PaymentService: Refund recorded
  PaymentService-->BookingService: Success
end
BookingService->NotificationService: sendCancellationNotification(user)
NotificationService-->BookingService: Notified
BookingService->Database: Write audit log
Database-->BookingService: Logged
BookingService-->BookingController: Success
BookingController-->BookingDetailView: Redirect
BookingDetailView-->Guest: Display "Cancelled"
```

**f) SD019: Manage Booking (Admin)**

```
title SD019: Manage Booking (Admin)
actor Admin
participant BookingView
participant BookingController
participant BookingService
participant NotificationService
database Database

Admin->BookingView: Select booking -> Edit
BookingView->BookingController: PUT /admin/bookings/{id}
BookingController->BookingService: updateBooking(id, data)
BookingService->Database: Check availability for new dates (if changed)
Database-->BookingService: Available
BookingService->Database: Save updated record
Database-->BookingService: Saved
BookingService->Database: Write audit log
Database-->BookingService: Logged
BookingService->NotificationService: notifyGuestOfChange(booking_id)
NotificationService-->BookingService: Sent
BookingService-->BookingController: Success
BookingController-->BookingView: Redirect
BookingView-->Admin: Show success message

Admin->BookingView: Select unit and dates -> Block Dates
BookingView->BookingController: POST /admin/blocks
BookingController->Database: Store blocked dates
Database-->BookingController: Stored
BookingController-->BookingView: Update calendar
BookingView-->Admin: Dates show as unavailable
```

**g) SD020: Auto-Cancel Expired Booking**

```
title SD020: Auto-Cancel Expired Booking
actor Scheduler
participant BookingService
participant BookingModel as Booking
participant NotificationService
database Database

Scheduler->BookingService: execute() (Hourly Job)
BookingService->Booking: getExpiredUnpaidBookings()
Booking->Database: Query status=awaiting payment AND deadline < now
Database-->Booking: Return list of overdue bookings
Booking-->BookingService: Return list
loop For each overdue booking
  BookingService->Booking: cancelBooking(id)
  Booking->Database: Set status = cancelled
  Database-->Booking: Updated
  Booking->Database: Release reserved dates
  Database-->Booking: Released
  BookingService->NotificationService: sendCancellationNotification(guest)
  NotificationService-->BookingService: Sent
  BookingService->Database: Write audit log
  Database-->BookingService: Logged
end
BookingService-->Scheduler: Job complete
```


---

### 4.2.4 P004: Payment Subsystem

#### 4.2.4.1 Class Diagram



> **Note:** Include a UML class diagram showing: `Payment`, `Bill`, `PaymentController`, `PaymentService`, `WebhookController`.

**Entity: Payment**

| Entity Name | Payment |
| :--- | :--- |
| **Method Name** | processPayment |
| **Input** | booking_id |
| **Output** | String |
| **Algorithm** | 1. Start 2. Validate input fields (booking_id) 3. Perform processPayment business logic for Payment. 5. Return String. 6. End |

| Entity Name | Payment |
| :--- | :--- |
| **Method Name** | handleWebhook |
| **Input** | payload |
| **Output** | Boolean |
| **Algorithm** | 1. Start 2. Validate input fields (payload) 3. Perform handleWebhook business logic for Payment. 5. Return Boolean. 6. End |

**Entity: Bill**

| Entity Name | Bill |
| :--- | :--- |
| **Method Name** | generateBill |
| **Input** | booking |
| **Output** | Bill |
| **Algorithm** | 1. Start 2. Validate input fields (booking) 3. Create new Bill record. 5. Return Bill. 6. End |

| Entity Name | Bill |
| :--- | :--- |
| **Method Name** | regenerateBill |
| **Input** |  |
| **Output** | File |
| **Algorithm** | 1. Start 2. Create new Bill record. 4. Return File. 5. End |



#### 4.2.4.2 Sequence Diagrams

**a) SD021: Make Payment**

```
title SD021: Make Payment
actor Guest
participant PaymentView
participant PaymentController
participant PaymentService
participant PaymentGatewayAPI
database Database

Guest->PaymentView: Click Pay Now
PaymentView->PaymentController: POST /payments/initiate
PaymentController->PaymentService: createPaymentSession(booking_id)
PaymentService->PaymentGatewayAPI: Create payment request
PaymentGatewayAPI-->PaymentService: Return payment session URL
PaymentService-->PaymentController: Return URL
PaymentController-->PaymentView: Redirect to Gateway URL
PaymentView-->Guest: Render secure payment page
Guest->PaymentGatewayAPI: Complete payment form & submit
PaymentGatewayAPI->PaymentGatewayAPI: Process transaction
PaymentGatewayAPI-->Guest: Show payment processing
```

**b) SD022: View Payment & Billing Records**

```
title SD022: View Payment & Billing Records
actor User
participant PaymentView
participant PaymentController
participant PaymentModel as Payment
database Database

User->PaymentView: Go to Payment History
PaymentView->PaymentController: GET /payments
PaymentController->Payment: getPaymentsForUser(user_id)
Payment->Database: Retrieve payment records
Database-->Payment: Return records
Payment-->PaymentController: Return list
PaymentController-->PaymentView: Render view
PaymentView-->User: Display payment history table
User->PaymentView: Click bill
PaymentView->PaymentController: GET /bills/{id}/download
PaymentController-->User: Return itemised bill PDF
```

**c) SD023: Regenerate Bill / Receipt**

```
title SD023: Regenerate Bill / Receipt
actor Admin
participant PaymentView
participant PaymentController
participant BillModel as Bill
participant EmailService
database Database

Admin->PaymentView: Click Regenerate Bill / Receipt
PaymentView->PaymentController: POST /admin/bills/{id}/regenerate
PaymentController->Bill: regenerate(booking_id)
Bill->Database: Retrieve latest booking/payment data
Database-->Bill: Return data
Bill->Bill: Generate fresh PDF
Bill->Database: Store new document
Database-->Bill: Stored
Bill-->PaymentController: Return document URL
PaymentController->EmailService: resendDocument(email)
EmailService-->PaymentController: Sent
PaymentController-->PaymentView: Return file stream
PaymentView-->Admin: Document downloaded
```

**d) SD024: Payment Webhook Processing**

```
title SD024: Payment Webhook Processing
actor PaymentGateway
participant WebhookController
participant PaymentService
participant PaymentModel as Payment
participant BillModel as Bill
participant BookingService
participant QrCodeService
participant NotificationService
database Database

PaymentGateway->WebhookController: POST webhook (payment confirmation)
WebhookController->PaymentService: handleWebhook(payload)
PaymentService->PaymentService: Verify authenticity
alt Valid confirmation
  PaymentService->Payment: Record successful payment
  Payment->Database: Save payment record
  Database-->Payment: Saved
  PaymentService->BookingService: markAsConfirmed(booking_id)
  BookingService->Database: Update booking status
  Database-->BookingService: Updated
  PaymentService->QrCodeService: generateQrCode(booking_id)
  QrCodeService->Database: Generate & save QR
  Database-->QrCodeService: Saved
  PaymentService->Bill: generateReceipt(booking_id)
  Bill->Database: Save receipt
  Database-->Bill: Saved
  PaymentService->NotificationService: notifyGuestAndAdmin(booking_id)
  NotificationService-->PaymentService: Sent
  PaymentService-->WebhookController: Processed (200 OK)
end
WebhookController-->PaymentGateway: 200 OK
```


---

### 4.2.5 P005: Notification Subsystem

#### 4.2.5.1 Class Diagram



> **Note:** Include a UML class diagram showing: `Notification` (Laravel built-in), `NotificationService`, `NotificationController`, `GoogleCalendarService`.

**Entity: Notification**

| Entity Name | Notification |
| :--- | :--- |
| **Method Name** | markAsRead |
| **Input** |  |
| **Output** | void |
| **Algorithm** | 1. Start 2. Perform markAsRead business logic for Notification. 4. Return success. 5. End |

| Entity Name | Notification |
| :--- | :--- |
| **Method Name** | unread |
| **Input** |  |
| **Output** | Boolean |
| **Algorithm** | 1. Start 2. Perform unread business logic for Notification. 4. Return Boolean. 5. End |



#### 4.2.5.2 Sequence Diagrams

**a) SD025: Receive System Notification**

```
title SD025: Receive System Notification
participant TriggerEvent
participant NotificationService
participant DatabaseNotification
participant EmailService
actor User

TriggerEvent->NotificationService: Significant event occurs
NotificationService->DatabaseNotification: Create notification record
DatabaseNotification->DatabaseNotification: Save to DB
alt User is online
  DatabaseNotification-->User: Bell icon badge updates
end
User->DatabaseNotification: Click bell icon -> View list
DatabaseNotification-->User: Show list
User->DatabaseNotification: Click a notification
DatabaseNotification->DatabaseNotification: Mark as read
DatabaseNotification-->User: Navigate to related page

NotificationService->NotificationService: Check global email settings
alt Email enabled
  NotificationService->EmailService: Send email notification
  EmailService-->User: Email arrives in inbox
end
```

**b) SD026: Google Calendar Integration**

```
title SD026: Google Calendar Integration
participant BookingService
participant NotificationService
participant GoogleCalendarAPI
database Database

BookingService->NotificationService: triggerCalendarSync(user, booking)
NotificationService->Database: Retrieve user's Google account connection
alt Connected
  Database-->NotificationService: Tokens found
  NotificationService->GoogleCalendarAPI: Create/update calendar event
  alt API Error
    GoogleCalendarAPI-->NotificationService: Error
    NotificationService->Database: Record failure
  else Success
    GoogleCalendarAPI-->NotificationService: Event confirmed
  end
else Not connected
  Database-->NotificationService: Null
  NotificationService->NotificationService: Skip calendar sync
end
```


---

### 4.2.6 P006: Chat Subsystem

#### 4.2.6.1 Class Diagram



> **Note:** Include a UML class diagram showing: `ChatConversation`, `ChatMessage`, `ChatController`, `ChatService`.

**Entity: ChatConversation**

| Entity Name | ChatConversation |
| :--- | :--- |
| **Method Name** | messages |
| **Input** |  |
| **Output** | Collection |
| **Algorithm** | 1. Start 2. Perform messages business logic for ChatConversation. 4. Return Collection. 5. End |

| Entity Name | ChatConversation |
| :--- | :--- |
| **Method Name** | unreadCount |
| **Input** | user_id |
| **Output** | Integer |
| **Algorithm** | 1. Start 2. Validate input fields (user_id) 3. Perform unreadCount business logic for ChatConversation. 5. Return Integer. 6. End |

**Entity: ChatMessage**

| Entity Name | ChatMessage |
| :--- | :--- |
| **Method Name** | conversation |
| **Input** |  |
| **Output** | ChatConversation |
| **Algorithm** | 1. Start 2. Perform conversation business logic for ChatMessage. 4. Return ChatConversation. 5. End |

| Entity Name | ChatMessage |
| :--- | :--- |
| **Method Name** | sender |
| **Input** |  |
| **Output** | User |
| **Algorithm** | 1. Start 2. Perform sender business logic for ChatMessage. 4. Return User. 5. End |



#### 4.2.6.2 Sequence Diagrams

**a) SD027: Send / Receive Messages**

```
title SD027: Send / Receive Messages
actor Sender
participant ChatView
participant ChatController
participant ChatMessageModel as ChatMessage
participant LaravelReverb
actor Recipient
database Database

Sender->ChatView: Type message & click Send
ChatView->ChatController: POST /chat/send
ChatController->ChatController: Validate message content
alt Valid message
  ChatController->ChatMessage: create(sender_id, recipient_id, content)
  ChatMessage->Database: Save message & timestamp
  Database-->ChatMessage: Message saved
  ChatMessage-->ChatController: Return new message
  ChatController->LaravelReverb: Broadcast MessageSent event
  LaravelReverb-->Recipient: Deliver message instantly to chat window
  LaravelReverb-->Sender: Confirm delivery
  ChatController-->ChatView: Success (200 OK)
  ChatView-->Sender: Show message as sent
end
```

**b) SD028: View Chat History**

```
title SD028: View Chat History
actor User
participant ChatView
participant ChatController
participant ChatMessageModel as ChatMessage
database Database

User->ChatView: Open Chat page
ChatView->ChatController: GET /chat/history/{conversation_id}
ChatController->ChatMessage: getMessagesForConversation(id)
ChatMessage->Database: Retrieve all messages (oldest to newest)
Database-->ChatMessage: Return list
ChatMessage-->ChatController: Return models
ChatController->Database: Mark all unread messages as read
Database-->ChatController: Updated
ChatController-->ChatView: Render message list
ChatView-->User: Display messages, auto-scroll to bottom
```


---

### 4.2.7 P007: User Management Subsystem

#### 4.2.7.1 Class Diagram



> **Note:** Include a UML class diagram showing: `User`, `UserController`, `UserService`.

**Entity: User**

| Entity Name | User |
| :--- | :--- |
| **Method Name** | assignRole |
| **Input** | role |
| **Output** | void |
| **Algorithm** | 1. Start 2. Validate input fields (role) 3. Find User record and update with provided data. 5. Return success. 6. End |

| Entity Name | User |
| :--- | :--- |
| **Method Name** | isActive |
| **Input** |  |
| **Output** | Boolean |
| **Algorithm** | 1. Start 2. Perform isActive business logic for User. 4. Return Boolean. 5. End |



#### 4.2.7.2 Sequence Diagrams

**a) SD029: Create User Account**

```
title SD029: Create User Account
actor Admin
participant UserView
participant UserController
participant UserService
participant UserModel as User
database Database
participant EmailService

Admin->UserView: Fill new user form (name, email, role)
UserView->UserController: POST /admin/users
UserController->UserService: createUser(data)
UserService->User: validate(email)
User->Database: Check email unique
alt Email unique
  Database-->User: Available
  UserService->UserService: Generate temp password
  UserService->User: create(data, forced_change=true)
  User->Database: Save user
  Database-->User: Saved
  UserService->EmailService: sendTempPassword(email)
  EmailService-->UserService: Sent
  UserService->Database: Write audit log
  Database-->UserService: Logged
  UserService-->UserController: Success
  UserController-->UserView: Redirect with success
  UserView-->Admin: Show success message
end
```

**b) SD030: Edit / Activate / Deactivate User**

```
title SD030: Edit / Activate / Deactivate User
actor Admin
participant UserView
participant UserController
participant UserModel as User
participant Session
database Database

Admin->UserView: Edit user details -> Save
UserView->UserController: PUT /admin/users/{id}
UserController->User: update(data)
User->Database: Save changes
Database-->User: Saved
UserController->Database: Write audit log
Database-->UserController: Logged
UserController-->UserView: Success

Admin->UserView: Click Deactivate User
UserView->UserController: POST /admin/users/{id}/deactivate
UserController->User: deactivate()
User->Database: Disable account
Database-->User: Updated
UserController->Session: endAllSessions(user_id)
Session-->UserController: Sessions cleared
UserController->Database: Write audit log
Database-->UserController: Logged
UserController-->UserView: Success
```

**c) SD031: Reset User Password**

```
title SD031: Reset User Password (Admin)
actor Admin
participant UserView
participant UserController
participant UserService
participant UserModel as User
database Database
participant EmailService

Admin->UserView: Click Reset Password
UserView->UserController: POST /admin/users/{id}/reset-password
UserController->UserService: resetPassword(user_id, method)
UserService->User: find(user_id)
alt Method = Send Link
  UserService->EmailService: sendResetLink(user.email)
  EmailService-->UserService: Sent
else Method = Default Password
  UserService->User: update(password=default, forced_change=true)
  User->Database: Save password & reset failed attempts
  Database-->User: Saved
end
UserService->Database: Write audit log
Database-->UserService: Logged
UserService-->UserController: Success
UserController-->UserView: Redirect with success
UserView-->Admin: Show success message
```


---

### 4.2.8 P008: Role and Permission Subsystem

#### 4.2.8.1 Class Diagram



> **Note:** Include a UML class diagram showing: `Role`, `Permission`, `RoleController`, `PermissionController` (Spatie models).

**Entity: Role**

| Entity Name | Role |
| :--- | :--- |
| **Method Name** | permissions |
| **Input** |  |
| **Output** | Collection |
| **Algorithm** | 1. Start 2. Perform permissions business logic for Role. 4. Return Collection. 5. End |

| Entity Name | Role |
| :--- | :--- |
| **Method Name** | givePermissionTo |
| **Input** | permission |
| **Output** | void |
| **Algorithm** | 1. Start 2. Validate input fields (permission) 3. Perform givePermissionTo business logic for Role. 5. Return success. 6. End |

| Entity Name | Role |
| :--- | :--- |
| **Method Name** | revokePermissionTo |
| **Input** | permission |
| **Output** | void |
| **Algorithm** | 1. Start 2. Validate input fields (permission) 3. Find Role record and delete or deactivate it. 5. Return success. 6. End |

| Entity Name | Role |
| :--- | :--- |
| **Method Name** | syncPermissions |
| **Input** | permissions |
| **Output** | void |
| **Algorithm** | 1. Start 2. Validate input fields (permissions) 3. Find Role record and update with provided data. 5. Return success. 6. End |

**Entity: Permission**

| Entity Name | Permission |
| :--- | :--- |
| **Method Name** | roles |
| **Input** |  |
| **Output** | Collection |
| **Algorithm** | 1. Start 2. Perform roles business logic for Permission. 4. Return Collection. 5. End |

| Entity Name | Permission |
| :--- | :--- |
| **Method Name** | assignRole |
| **Input** | role |
| **Output** | void |
| **Algorithm** | 1. Start 2. Validate input fields (role) 3. Find Permission record and update with provided data. 5. Return success. 6. End |

| Entity Name | Permission |
| :--- | :--- |
| **Method Name** | removeRole |
| **Input** | role |
| **Output** | void |
| **Algorithm** | 1. Start 2. Validate input fields (role) 3. Find Permission record and delete or deactivate it. 5. Return success. 6. End |



#### 4.2.8.2 Sequence Diagrams

**a) SD032: Manage Roles**

```
title SD032: Manage Roles
actor Admin
participant RoleView
participant RoleController
participant RoleModel as Role
database Database

Admin->RoleView: Enter role name & description -> Save
RoleView->RoleController: POST /admin/roles
RoleController->Role: create(name, description)
Role->Database: Insert role
Database-->Role: Created
Role-->RoleController: Success
RoleController->Database: Write audit log
Database-->RoleController: Logged
RoleController-->RoleView: Redirect to permissions page
RoleView-->Admin: Display role permissions
```

**b) SD033: Manage Permissions**

```
title SD033: Manage Permissions
actor Admin
participant RoleView
participant RoleController
participant RoleModel as Role
database Database

Admin->RoleView: Tick permissions & Save
RoleView->RoleController: PUT /admin/roles/{id}/permissions
RoleController->Role: syncPermissions(permissions_array)
Role->Database: Update role_has_permissions table
Database-->Role: Synced
Role-->RoleController: Success
RoleController->Database: Write audit log
Database-->RoleController: Logged
RoleController-->RoleView: Redirect with success
RoleView-->Admin: Show success message
```


---

### 4.2.9 P009: System Settings Subsystem

#### 4.2.9.1 Class Diagram



> **Note:** Include a UML class diagram showing: `Setting`, `DefaultHousePolicy`, `SettingsController`, `SettingsService`.

**Entity: Setting**

| Entity Name | Setting |
| :--- | :--- |
| **Method Name** | getValue |
| **Input** | key |
| **Output** | String |
| **Algorithm** | 1. Start 2. Validate input fields (key) 3. Retrieve Setting record from database. 5. Return String. 6. End |

| Entity Name | Setting |
| :--- | :--- |
| **Method Name** | setValue |
| **Input** | key, value |
| **Output** | void |
| **Algorithm** | 1. Start 2. Validate input fields (key, value) 3. Perform setValue business logic for Setting. 5. Return success. 6. End |

**Entity: DefaultHousePolicy**

| Entity Name | DefaultHousePolicy |
| :--- | :--- |
| **Method Name** | addPolicy |
| **Input** | data |
| **Output** | DefaultHousePolicy |
| **Algorithm** | 1. Start 2. Validate input fields (data) 3. Create new DefaultHousePolicy record. 5. Return DefaultHousePolicy. 6. End |

| Entity Name | DefaultHousePolicy |
| :--- | :--- |
| **Method Name** | removePolicy |
| **Input** | id |
| **Output** | Boolean |
| **Algorithm** | 1. Start 2. Validate input fields (id) 3. Find DefaultHousePolicy record and delete or deactivate it. 5. Return Boolean. 6. End |



#### 4.2.9.2 Sequence Diagrams

**a) SD034: Configure System Settings**

```
title SD034: Configure System Settings
actor Admin
participant SettingsView
participant SettingsController
participant SettingsService
participant SettingModel as Setting
database Database

Admin->SettingsView: Enter details (SMTP, Refunds, etc.) & Save
SettingsView->SettingsController: POST /admin/settings
SettingsController->SettingsController: Validate input
alt Valid
  SettingsController->SettingsService: updateSettings(data)
  SettingsService->Setting: setValue(key, value)
  Setting->Database: Upsert settings
  Database-->Setting: Saved
  Setting-->SettingsService: Success
  SettingsService->Database: Write audit log
  Database-->SettingsService: Logged
  SettingsService-->SettingsController: Success
  SettingsController-->SettingsView: Redirect with success
  SettingsView-->Admin: Show success message
end
```

**b) SD035: Manage Default Homestay Policies**

```
title SD035: Manage Default Homestay Policies
actor Admin
participant SettingsView
participant SettingsController
participant PolicyModel as Policy
database Database

Admin->SettingsView: System Settings -> Default Policies
SettingsView->SettingsController: GET /admin/settings/policies
SettingsController->Policy: getDefaultPolicies()
Policy->Database: Retrieve policies
Database-->Policy: Return list
Policy-->SettingsController: Return models
SettingsController-->SettingsView: Render policies
SettingsView-->Admin: Display list

Admin->SettingsView: Add/Edit Policy -> Save
SettingsView->SettingsController: POST /admin/settings/policies
SettingsController->Policy: save(data)
Policy->Database: Add/update policy in default list
Database-->Policy: Saved
Policy-->SettingsController: Success
SettingsController-->SettingsView: Redirect with success
```


---

### 4.2.10 P010: Audit Log Subsystem

#### 4.2.10.1 Class Diagram



> **Note:** Include a UML class diagram showing: `Activity` (Spatie Activity Log model), `AuditLogController`.

**Entity: Activity**

| Entity Name | Activity |
| :--- | :--- |
| **Method Name** | subject |
| **Input** |  |
| **Output** | Model |
| **Algorithm** | 1. Start 2. Perform subject business logic for Activity. 4. Return Model. 5. End |

| Entity Name | Activity |
| :--- | :--- |
| **Method Name** | causer |
| **Input** |  |
| **Output** | Model |
| **Algorithm** | 1. Start 2. Perform causer business logic for Activity. 4. Return Model. 5. End |



#### 4.2.10.2 Sequence Diagrams

**a) SD036: View / Filter Audit Trail**

```
title SD036: View / Filter Audit Trail
actor Admin
participant AuditLogView
participant AuditLogController
participant ActivityModel as Activity
database Database

Admin->AuditLogView: Open Audit Logs, apply filters
AuditLogView->AuditLogController: GET /admin/audit-logs?filters
AuditLogController->Activity: query(filters, sort)
Activity->Database: Apply conditions
Database-->Activity: Return filtered entries
Activity-->AuditLogController: Return paginated results
AuditLogController-->AuditLogView: Update view
AuditLogView-->Admin: Display filtered logs
```

**b) SD037: Automatic Event Logging**

```
title SD037: Automatic Event Logging
participant SystemActor as "User/Admin/System"
participant SpatieLogger
database Database

SystemActor->SpatieLogger: trigger event (created/updated/deleted)
SpatieLogger->SpatieLogger: Capture actor, action, and changes
SpatieLogger->Database: Insert into activity_log table
Database-->SpatieLogger: Saved (Immutable)
SpatieLogger-->SystemActor: Continue process
```


---

### 4.2.11 P011: QR Code Door Access Subsystem

#### 4.2.11.1 Class Diagram



> **Note:** Include a UML class diagram showing: `QrCode`, `BookingExtension`, `QrCodeController`, `QrCodeService`, `ExtensionService`.

**Entity: QrCode**

| Entity Name | QrCode |
| :--- | :--- |
| **Method Name** | generateQrCode |
| **Input** | booking_id |
| **Output** | QrCode |
| **Algorithm** | 1. Start 2. Validate input fields (booking_id) 3. Create new QrCode record. 5. Return QrCode. 6. End |

| Entity Name | QrCode |
| :--- | :--- |
| **Method Name** | isValid |
| **Input** |  |
| **Output** | Boolean |
| **Algorithm** | 1. Start 2. Perform isValid business logic for QrCode. 4. Return Boolean. 5. End |

**Entity: BookingExtension**

| Entity Name | BookingExtension |
| :--- | :--- |
| **Method Name** | requestExtension |
| **Input** | booking_id, new_out_date, new_out_time |
| **Output** | BookingExtension |
| **Algorithm** | 1. Start 2. Validate input fields (booking_id, new_out_date, new_out_time) 3. Perform requestExtension business logic for BookingExtension. 5. Return BookingExtension. 6. End |

| Entity Name | BookingExtension |
| :--- | :--- |
| **Method Name** | isExpired |
| **Input** |  |
| **Output** | Boolean |
| **Algorithm** | 1. Start 2. Perform isExpired business logic for BookingExtension. 4. Return Boolean. 5. End |



#### 4.2.11.2 Sequence Diagrams

**a) SD038: Receive & Use QR Code**

```
title SD038: Receive & Use QR Code
actor Guest
participant System
participant QrCodeModel as QrCode
participant SmartLock
participant NotificationService
database Database

System->System: Payment confirmed
System->QrCode: generateQrCode(booking_id)
QrCode->Database: Generate unique QR code (valid window)
Database-->QrCode: Saved
System->NotificationService: sendQrCodeNotification(guest)
NotificationService-->System: Sent

Guest->SmartLock: Present QR code to scanner
SmartLock->System: Validate QR code
System->Database: Check code status and validity window
alt Expired or Inactive
  Database-->System: Invalid
  System-->SmartLock: Deny - access not granted
else Valid
  Database-->System: Valid
  System-->SmartLock: Grant access
  SmartLock-->Guest: Door opens
end
```

**b) SD039: Manage Housekeeping Cycle**

```
title SD039: Manage Housekeeping Cycle
actor Admin
participant BookingView
participant QrCodeController
participant QrCodeModel as QrCode
participant NotificationService
database Database

Admin->BookingView: Click Generate Housekeeping QR
BookingView->QrCodeController: POST /admin/qr/housekeeping
QrCodeController->QrCode: generate(type: housekeeping)
QrCode->Database: Save temporary QR code
Database-->QrCode: Saved
QrCode-->QrCodeController: Success
QrCodeController-->BookingView: Display housekeeping QR code
BookingView-->Admin: Share with cleaning staff

Admin->BookingView: Click Mark Housekeeping Complete
BookingView->QrCodeController: POST /admin/qr/housekeeping/complete
QrCodeController->QrCode: expire()
QrCode->Database: Set housekeeping QR to expired
Database-->QrCode: Updated
QrCodeController->Database: Check for next confirmed booking
alt Next booking exists
  Database-->QrCodeController: Booking found
  QrCodeController->QrCode: generateQrCode(next_booking_id)
  QrCode->Database: Generate & save QR
  Database-->QrCode: Saved
  QrCodeController->NotificationService: sendQrCodeNotification(next_guest)
  NotificationService-->QrCodeController: Sent
end
QrCodeController-->BookingView: Success
```

**c) SD040: Initiate Booking Extension**

```
title SD040: Initiate Booking Extension
actor Admin
participant QrCodeView
participant ExtensionController
participant ExtensionService
participant BookingExtension
participant BillModel as Bill
participant NotificationService
database Database

Admin->QrCodeView: Enter new check-out time & click Extend
QrCodeView->ExtensionController: POST /admin/extensions
ExtensionController->ExtensionService: requestExtension(booking_id, new_out)
ExtensionService->Database: Check availability for extended period
alt Conflict
  Database-->ExtensionService: Unavailable
  ExtensionService-->ExtensionController: Throw error
  ExtensionController-->QrCodeView: Redirect with error
  QrCodeView-->Admin: Show conflicting booking
else Available
  Database-->ExtensionService: Available
  ExtensionService->ExtensionService: Calculate extension charge
  ExtensionService->BookingExtension: create(data, status: awaiting payment)
  BookingExtension->Database: Save record
  Database-->BookingExtension: Saved
  ExtensionService->Bill: generateExtensionBill()
  Bill->Database: Save bill
  Database-->Bill: Saved
  ExtensionService->NotificationService: sendExtensionNotification(guest)
  NotificationService-->ExtensionService: Sent
  ExtensionService->Database: Write audit log
  Database-->ExtensionService: Logged
  ExtensionService-->ExtensionController: Success
  ExtensionController-->QrCodeView: Show pending extension
  QrCodeView-->Admin: Extension requested, awaiting payment
end
```

**d) SD041: Pay Extension Charge**

```
title SD041: Pay Extension Charge
actor Guest
participant PaymentView
participant PaymentController
participant PaymentGateway
participant ExtensionService
participant NotificationService
database Database

Guest->PaymentView: Click Pay Extension Charge
PaymentView->PaymentController: POST /payments/extension/initiate
PaymentController->PaymentGateway: Redirect to secure payment
Guest->PaymentGateway: Complete payment
PaymentGateway->PaymentController: Payment webhook / confirmation
PaymentController->ExtensionService: confirmExtensionPayment(extension_id)
ExtensionService->Database: Record payment as successful
Database-->ExtensionService: Saved
ExtensionService->Database: Update extension status to confirmed
Database-->ExtensionService: Updated
ExtensionService->Database: Update booking check-out time
Database-->ExtensionService: Updated
ExtensionService->Database: Extend QR code validity
Database-->ExtensionService: Updated
ExtensionService->NotificationService: notifyGuest(stay_extended)
NotificationService-->ExtensionService: Sent
ExtensionService->Database: Write audit log
Database-->ExtensionService: Logged
```

**e) SD042: Auto-Cancel Extension**

```
title SD042: Auto-Cancel Extension
actor Scheduler
participant ExtensionService
participant BookingExtension
participant BookingModel as Booking
participant QrCodeModel as QrCode
participant NotificationService
database Database

Scheduler->ExtensionService: execute() (Frequent Job)
ExtensionService->BookingExtension: getOverdueExtensions()
BookingExtension->Database: Query awaiting payment & expired
Database-->BookingExtension: Return list
BookingExtension-->ExtensionService: List of overdue
loop For each overdue
  ExtensionService->BookingExtension: cancel()
  BookingExtension->Database: Update status = cancelled
  Database-->BookingExtension: Updated
  ExtensionService->Booking: Revert to original check-out
  Booking->Database: Update booking
  Database-->Booking: Updated
  ExtensionService->QrCode: Revert validity
  QrCode->Database: Update QR code
  Database-->QrCode: Updated
  ExtensionService->NotificationService: sendExtensionCancelledNotif(guest)
  NotificationService-->ExtensionService: Sent
  ExtensionService->Database: Write audit log
  Database-->ExtensionService: Logged
end
ExtensionService-->Scheduler: Job complete
```


---

### 4.2.12 P012: Reporting and Analytics Subsystem

#### 4.2.12.1 Class Diagram



> **Note:** Include a UML class diagram showing: `ReportController`, `ReportService`, `BookingExport`, `RevenueExport`.


*(No specific entity methods to list for this package)*


#### 4.2.12.2 Sequence Diagrams

**a) SD043: View Analytics Dashboard**

```
title SD043: View Analytics Dashboard
actor Admin
participant DashboardView
participant ReportController
participant ReportService
participant BookingModel
participant PaymentModel
database Database

Admin->DashboardView: Go to Dashboard
DashboardView->ReportController: GET /admin/dashboard
ReportController->ReportService: getDashboardStats()
ReportService->BookingModel: countBookings(), calcOccupancy()
BookingModel->Database: Query bookings
Database-->BookingModel: Return aggregates
BookingModel-->ReportService: Booking KPIs
ReportService->PaymentModel: sumRevenue()
PaymentModel->Database: Query payments
Database-->PaymentModel: Return aggregates
PaymentModel-->ReportService: Revenue KPIs
ReportService-->ReportController: Aggregated Stats & Chart Data
ReportController-->DashboardView: Render with Chart.js
DashboardView-->Admin: Display KPIs and Charts
```

**b) SD044: View Revenue Report**

```
title SD044: View Revenue Report
actor Admin
participant ReportView
participant ReportController
participant ReportService
database Database

Admin->ReportView: Go to Revenue Report, Apply filters
ReportView->ReportController: GET /admin/reports/revenue?filters
ReportController->ReportService: getRevenueReport(filters)
ReportService->Database: Retrieve matching payment & booking data
Database-->ReportService: Return filtered records
ReportService-->ReportController: Return itemised breakdown
ReportController-->ReportView: Render view
ReportView-->Admin: Display total revenue & summary
```

**c) SD045: Export Report**

```
title SD045: Export Report
actor Admin
participant ReportView
participant ReportController
participant ReportService
participant Exporter
database Database

Admin->ReportView: Apply filters & click Export
ReportView->ReportController: GET /admin/reports/export?filters
ReportController->ReportService: getFilteredData(filters)
ReportService->Database: Query records
Database-->ReportService: Return dataset
ReportService-->ReportController: Return data
ReportController->Exporter: generate(data, format)
Exporter->Exporter: Format to PDF/CSV
Exporter-->ReportController: Return file stream
ReportController-->ReportView: Download Response
ReportView-->Admin: File downloads
```


---

### 4.2.13 P013: Guest Feedback Subsystem

#### 4.2.13.1 Class Diagram



> **Note:** Include a UML class diagram showing: `Feedback`, `FeedbackController`, `FeedbackService`.

**Entity: Feedback**

| Entity Name | Feedback |
| :--- | :--- |
| **Method Name** | submitFeedback |
| **Input** | booking_id, rating, comment |
| **Output** | Feedback |
| **Algorithm** | 1. Start 2. Validate input fields (booking_id, rating, comment) 3. Perform submitFeedback business logic for Feedback. 5. Return Feedback. 6. End |

| Entity Name | Feedback |
| :--- | :--- |
| **Method Name** | unit |
| **Input** |  |
| **Output** | HomestayUnit |
| **Algorithm** | 1. Start 2. Perform unit business logic for Feedback. 4. Return HomestayUnit. 5. End |

| Entity Name | Feedback |
| :--- | :--- |
| **Method Name** | user |
| **Input** |  |
| **Output** | User |
| **Algorithm** | 1. Start 2. Perform user business logic for Feedback. 4. Return User. 5. End |

| Entity Name | Feedback |
| :--- | :--- |
| **Method Name** | booking |
| **Input** |  |
| **Output** | Booking |
| **Algorithm** | 1. Start 2. Perform booking business logic for Feedback. 4. Return Booking. 5. End |



#### 4.2.13.2 Sequence Diagrams

**a) SD046: Submit Rating & Feedback**

```
title SD046: Submit Rating & Feedback
actor Guest
participant BookingDetailView
participant FeedbackController
participant FeedbackService
participant FeedbackModel as Feedback
participant HomestayUnitModel as HomestayUnit
database Database

Guest->BookingDetailView: Select rating & submit review
BookingDetailView->FeedbackController: POST /feedback
FeedbackController->FeedbackController: Validate rating
FeedbackController->FeedbackService: submitFeedback(booking_id, data)
FeedbackService->Database: Check if review already exists
alt Review exists or ineligible
  Database-->FeedbackService: Exists
  FeedbackService-->FeedbackController: Error
  FeedbackController-->BookingDetailView: Redirect with error
  BookingDetailView-->Guest: Error message
else Eligible
  FeedbackService->Feedback: create(data)
  Feedback->Database: Save review (visible)
  Database-->Feedback: Saved
  FeedbackService->HomestayUnit: recalculateAvgRating()
  HomestayUnit->Database: Update unit average
  Database-->HomestayUnit: Updated
  FeedbackService-->FeedbackController: Success
  FeedbackController-->BookingDetailView: Redirect with success
  BookingDetailView-->Guest: Show "Thank You" message
end
```

**b) SD047: View Submitted Feedback**

```
title SD047: View Submitted Feedback
actor Guest
participant ReviewView
participant FeedbackController
participant FeedbackModel as Feedback
database Database

Guest->ReviewView: Go to My Reviews
ReviewView->FeedbackController: GET /reviews
FeedbackController->Feedback: getForUser(user_id)
Feedback->Database: Retrieve all review records for guest
Database-->Feedback: Return review list
Feedback-->FeedbackController: Return list
FeedbackController-->ReviewView: Render view
ReviewView-->Guest: Display each entry (read-only)
```

**c) SD048: View / Manage All Feedback (Admin)**

```
title SD048: View / Manage All Feedback (Admin)
actor Admin
participant FeedbackView
participant FeedbackController
participant FeedbackModel as Feedback
database Database

Admin->FeedbackView: Go to Guest Feedback
FeedbackView->FeedbackController: GET /admin/feedback
FeedbackController->Feedback: getAll()
Feedback->Database: Retrieve all review records
Database-->Feedback: Return records
Feedback-->FeedbackController: Return list
FeedbackController-->FeedbackView: Render view
FeedbackView-->Admin: Display reviews

Admin->FeedbackView: Type reply -> Submit
FeedbackView->FeedbackController: POST /admin/feedback/{id}/reply
FeedbackController->Feedback: update(admin_reply)
Feedback->Database: Save reply
Database-->Feedback: Saved
FeedbackController-->FeedbackView: Redirect back

Admin->FeedbackView: Select review -> Hide
FeedbackView->FeedbackController: POST /admin/feedback/{id}/hide
FeedbackController->Feedback: setVisibility(hidden)
Feedback->Database: Update status
Database-->Feedback: Updated
FeedbackController->Feedback: recalculateUnitRating()
Feedback->Database: Update average
Database-->Feedback: Updated
FeedbackController-->FeedbackView: Success
```

**d) SD049: Display Average Rating**

```
title SD049: Display Average Rating
actor User
participant UnitView
participant HomestayController
participant HomestayUnitModel as HomestayUnit
database Database

User->UnitView: View unit detail page
UnitView->HomestayController: GET /units/{id}
HomestayController->HomestayUnit: find({id})
HomestayUnit->Database: Retrieve unit & visible reviews
Database-->HomestayUnit: Return data
HomestayUnit-->HomestayController: Return unit with avg rating
HomestayController-->UnitView: Render view
alt No visible reviews
  UnitView-->User: Display "No reviews yet"
else Visible reviews exist
  UnitView-->User: Display X.X ★ (N reviews)
end
```


---

*— End of Chapter 4: Detailed Description of Components —*
