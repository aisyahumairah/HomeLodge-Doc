# HomeLodge – Sequence Diagram Codes
> Paste each block into [sequencediagram.org](https://sequencediagram.org/) to render.

---

## Module 1 – Authentication

### UC-AUTH-01: Register Account (Email / Password)

```
title UC-AUTH-01: Register Account

actor Guest
participant System
database DB

Guest->System:Open registration page
System-->Guest:Display Registration Form
Guest->System:Enter name, email, password, confirm password
System->System:Validate email format
System->DB:Check if email already exists
alt Email already in use
  DB-->System:Email taken
  System-->Guest:Highlight email field – suggest Sign In or Forgot Password
else Email is unique
  DB-->System:Email available
  System->System:Validate password strength (8-12 chars, upper, lower, number, symbol)
  alt Password too weak
    System-->Guest:Show which rules are not met
  else Passwords do not match
    System-->Guest:Highlight confirm password field
  else All valid
    System->DB:Hash & save password, create account with "Guest" role
    DB-->System:Account created
    System-->Guest:Show success message & redirect to sign-in page
  end
end
```

---

### UC-AUTH-02: Login (Email/Password + Google SSO)

```
title UC-AUTH-02: Login / Login via Google SSO

actor User
participant System
participant Google
database DB

note over User,System:--- Email & Password Login ---
User->System:Enter email and password
System->DB:Verify credentials
alt Invalid credentials
  DB-->System:Not matched
  System-->User:Show generic error (Invalid email or password)
else Account deactivated
  DB-->System:Account inactive
  System-->User:Account deactivated – contact support
else Account locked
  DB-->System:Account locked
  System-->User:Show lockout message + unlock via reset password
else Valid credentials
  DB-->System:Credentials OK
  System->DB:Check if password change is required
  alt Password change required
    System-->User:Redirect to forced password change page (UC-AUTH-06)
  else No change required
    System->DB:Record sign-in time
    System-->User:Redirect to dashboard
  end
end

note over User,System:--- Google Sign-In ---
User->System:Click "Continue with Google"
System->Google:Redirect to Google sign-in
User->Google:Authenticate & grant permission
Google->System:Return name and email
System->DB:Check if account exists for this email
alt Account exists
  System->DB:Link Google identity if not already linked
  System-->User:Signed in – redirect to dashboard
else No account exists
  System->DB:Create account with "Guest" role using Google profile
  System-->User:Signed in – redirect to dashboard
end
```

---

### UC-AUTH-03: Logout

```
title UC-AUTH-03: Logout

actor User
participant System
database DB

User->System:Click Logout
System->DB:End session, clear remember-me tokens
DB-->System:Session cleared
System-->User:Redirect to sign-in page
note over User:Browser back button shows sign-in page, not protected content
```

---

### UC-AUTH-04: Forgot Password (Reset via Email)

```
title UC-AUTH-04: Forgot Password

actor User
participant System
participant EmailService
database DB

User->System:Click "Forgot Password"
System-->User:Display page Forgot Password
User->System:Enter registered email address
System->DB:Check if email exists
note over System:Always shows same generic message to protect privacy
System-->User:If an account exists, a reset link has been sent
alt Email found
  System->DB:Generate time-limited reset link (default 60 min)
  System->EmailService:Send reset link email
end
User->EmailService:Open email & click reset link
User->System:Visit reset link
System->DB:Check link is valid (not expired, not already used)
alt Link expired or already used
  System-->User:Link is invalid or expired – request a new one
else Link valid
  System-->User:Show password reset form
  User->System:Enter new password and confirm
  System->System:Validate password strength
  alt Password too weak
    System-->User:Highlight failing rules
  else Passwords match and strong
    System->DB:Save new hashed password, mark link as used
    System->DB:Remove lock & reset failed-attempt counter (if account was locked)
    System-->User:Redirect to sign-in page with success message
  end
end
```

---

### UC-AUTH-05: View / Update Profile

```
title UC-AUTH-05: View / Update Profile

actor User
participant System
database DB

User->System:Go to Profile page
System->DB:Retrieve profile data
DB-->System:Return profile
System-->User:Display name, email, phone, profile photo

User->System:Edit fields and/or upload new profile photo
User->System:Click Save
System->System:Validate phone format and photo file type/size
alt Validation fails
  System-->User:Highlight invalid fields
else Valid
  System->DB:Save updated profile
  System-->User:Profile updated successfully
end

note over User,System:--- Change Password ---
User->System:Click Change Password
System-->User:Show current + new password form
User->System:Enter current password and new password (x2)
System->DB:Verify current password
alt Current password wrong
  System-->User:Current password does not match
else New passwords do not match
  System-->User:Passwords do not match
else Password too weak
  System-->User:Show failing rules
else All valid
  System->DB:Save new password
  System-->User:Password changed successfully
end
```

---

### UC-AUTH-06: Force Change Password

```
title UC-AUTH-06: Force Change Password

actor User
participant System
database DB

User->System:Sign in with temporary password
System->DB:Check if forced password change is required
DB-->System:Flag active
System-->User:Redirect to Change Your Password page
note over System:All other pages are blocked until this is completed

User->System:Enter new password and confirm
User->System:Click Save
System->System:Validate password strength
alt Same as temporary password
  System-->User:Choose a different password
else Password too weak
  System-->User:Highlight failing rules
else Valid
  System->DB:Save new password, clear forced-change flag
  System-->User:Redirect to dashboard
end
```

---

## Module 2 – Homestay Management

### UC-HS-01: Browse Homestay Units

```
title UC-HS-01: Browse Homestay Units

actor Guest
participant System
database DB

Guest->System:Open homestay listing page
System->DB:Retrieve all active units
DB-->System:Return unit list
alt No active units
  System-->Guest:No homestay units are currently available
else Units found
  System-->Guest:Display unit cards (name, photo, price/night, location, avg rating)
  Guest->System:Click on a unit card
  System-->Guest:Navigate to unit detail page (UC-HS-02)
end
```

---

### UC-HS-02: View Unit Details & Availability

```
title UC-HS-02: View Unit Details & Availability

actor Guest
participant System
database DB

Guest->System:Click unit from listing page
System->DB:Retrieve unit details and booking records
DB-->System:Return unit data
System-->Guest:Display name, photos, description, location, price, deposit,\ncheck-in/out times, house rules, avg rating, guest reviews
System->DB:Retrieve availability data for calendar
DB-->System:Return date states
System-->Guest:Show colour-coded availability calendar\n(Available / Booked / Temporarily Held / Blocked)
Guest->System:Click "Book Now"
System-->Guest:Navigate to booking form (UC-BK-01)
```

---

### UC-HS-03: Create Homestay Unit

```
title UC-HS-03: Create Homestay Unit

actor Admin
participant System
database DB

Admin->System:Go to Homestay Management → Create New Unit
Admin->System:Enter name, description, location, price/night, deposit,\ncheck-in/out times, optional extension window
Admin->System:Upload one or more photos
Admin->System:Click Save
System->System:Validate required fields and photo file types
alt Required field missing
  System-->Admin:Highlight missing fields
else Invalid photo file
  System-->Admin:Reject file – ask for valid format
else All valid
  System->DB:Save unit record (status: active)
  System->DB:Copy all active default house rules to this unit
  System->DB:Write audit log entry
  System-->Admin:Success message – unit visible on listing page
end
```

---

### UC-HS-04: Edit Homestay Unit

```
title UC-HS-04: Edit Homestay Unit

actor Admin
participant System
database DB

Admin->System:Homestay Management → select unit → Edit
System->DB:Retrieve current unit details
DB-->System:Return data
System-->Admin:Pre-filled edit form
Admin->System:Modify desired fields (name, description, pricing,\ntimes, photos, house rules, extension window)
Admin->System:Click Save
System->System:Validate changes
alt Invalid data
  System-->Admin:Highlight problem fields (values preserved)
else Valid
  System->DB:Save updated record
  System->DB:Write audit log entry
  System-->Admin:Success message – changes live immediately
end
```

---

### UC-HS-05: Deactivate / Delete Unit

```
title UC-HS-05: Deactivate / Delete Unit

actor Admin
participant System
database DB

Admin->System:Open unit management page → click Deactivate / Delete
System->DB:Check for upcoming confirmed bookings on this unit
alt Upcoming confirmed bookings exist
  System-->Admin:Block action – list conflicting bookings
  note over Admin:Must cancel or reassign bookings first
else No conflicts
  System-->Admin:Show confirmation dialog
  alt Admin cancels
    System-->Admin:No changes made
  else Admin confirms
    System->DB:Set unit status to inactive
    System->DB:Write audit log entry
    System-->Admin:Success message – unit removed from guest listing
  end
end
```

---

### UC-HS-06: View All Units List

```
title UC-HS-06: View All Units List (Admin)

actor Admin
participant System
database DB

Admin->System:Go to Homestay Management section
System->DB:Retrieve all unit records (active and inactive)
DB-->System:Return list
System-->Admin:Display table: name, status, upcoming bookings count,\nbase price, action buttons (Edit / Deactivate / Manage Policies)
Admin->System:Search or filter by status
System->DB:Apply filter
DB-->System:Return filtered results
System-->Admin:Update displayed list
```

---

## Module 3 – Booking

### UC-BK-01: View Availability & Select Dates

```
title UC-BK-01: View Availability & Select Dates

actor Guest
participant System
database DB

Guest->System:View unit detail or booking form
System->DB:Retrieve availability data
DB-->System:Return date states
System-->Guest:Display colour-coded availability calendar
Guest->System:Click check-in date
System-->Guest:Highlight selected check-in date
Guest->System:Click check-out date
System->DB:Real-time availability check for selected range
alt Dates include unavailable date
  System-->Guest:Highlight conflict – Selected dates not available
else Check-out before check-in
  System-->Guest:Validation error – re-select dates
else Minimum stay not met
  System-->Guest:Show minimum stay requirement
else All dates available
  System-->Guest:Confirm availability – show nights count and estimated cost
  Guest->System:Optionally adjust check-in/out times
end
```

---

### UC-BK-02: Submit Booking

```
title UC-BK-02: Submit Booking

actor Guest
participant System
participant NotifService
database DB

Guest->System:Review booking summary (unit, dates, times, total cost)
Guest->System:Click Confirm Booking
System->DB:Double-check date availability (prevent race condition)
alt Dates became unavailable
  System-->Guest:Conflict – please re-select dates
else Available
  System->DB:Create booking record (status: awaiting payment, 1-day deadline)
  System->DB:Generate bill with unique bill number
  System->DB:Temporarily reserve selected dates
  System->NotifService:Send in-app + email notification (bill + payment deadline)
  System-->Guest:Redirect to payment page
end
```

---

### UC-BK-03: View Bookings

```
title UC-BK-03: View Bookings

actor User
participant System
database DB

note over User,System:--- Guest view ---
User->System:Go to My Bookings
System->DB:Retrieve all bookings for signed-in guest
DB-->System:Return bookings
System-->User:Display bookings in Current and History tabs\n(unit name, dates, status, total cost)
User->System:Click a booking
System-->User:Navigate to booking detail (UC-BK-04)

note over User,System:--- Admin view ---
User->System:Go to Booking Management
System->DB:Retrieve all bookings system-wide
DB-->System:Return all bookings
System-->User:Display booking list with filters (status, date, unit, reference)
User->System:Open booking calendar view
System-->User:All-unit calendar with all reservations
User->System:Click booking entry
System-->User:Navigate to booking detail or take action
```

---

### UC-BK-04: View Booking Details

```
title UC-BK-04: View Booking Details

actor User
participant System
database DB

User->System:Click booking from bookings list
System->DB:Retrieve full booking record (unit, billing, QR code, extensions)
DB-->System:Return data
System-->User:Display: unit name & photo, check-in/out date & time,\ntotal amount, payment status, booking status,\ncancellation policy & estimated refund,\nQR code (if confirmed), extension history
User->System:Download bill or receipt (optional)
```

---

### UC-BK-05: Cancel Booking

```
title UC-BK-05: Cancel Booking

actor User
participant System
participant PaymentGateway
participant NotifService
database DB

User->System:Open booking detail → click Cancel Booking
System->DB:Calculate refund based on cancellation policy tiers
DB-->System:Return refund amount
System-->User:Show confirmation: You will receive a refund of [amount].\nThis cannot be undone.
alt User cancels confirmation
  System-->User:No changes made
else User confirms
  System->DB:Set booking status to cancelled
  System->DB:Release reserved dates
  alt Payment was made
    System->PaymentGateway:Process refund
    alt Refund fails
      System->DB:Flag refund for manual processing by admin
    end
  end
  System->NotifService:Send cancellation notification (in-app + email) to guest and admin
  System->DB:Write audit log entry
end
```

---

### UC-BK-06: Manage Booking (Admin)

```
title UC-BK-06: Manage Booking (Admin)

actor Admin
participant System
participant NotifService
database DB

note over Admin,System:--- Create Booking on Behalf of Guest ---
Admin->System:Bookings → Create Booking
Admin->System:Select guest account, unit, check-in/out dates & times
System->DB:Check real-time availability
DB-->System:Available
System-->Admin:Show booking summary with total cost
Admin->System:Confirm booking
System->DB:Create booking (awaiting payment), generate bill
System->NotifService:Notify guest to pay

note over Admin,System:--- Edit Booking ---
Admin->System:Open booking detail → Edit
Admin->System:Modify fields (dates, times, etc.)
System->DB:Check availability for new dates if changed
System->DB:Save updated record
System->DB:Write audit log entry
System->NotifService:Notify guest of change

note over Admin,System:--- Delete Booking ---
Admin->System:Select booking → Delete
System-->Admin:Warning: this is permanent and cannot be undone
Admin->System:Confirm deletion
System->DB:Delete booking record, release dates
System->NotifService:Notify guest
System->DB:Write audit log entry

note over Admin,System:--- Block Dates ---
Admin->System:Select unit and date range → Block Dates
Admin->System:Enter optional internal note/reason
System->DB:Store blocked dates (hidden from guests)
System-->Admin:Dates show as unavailable on guest calendar
```

---

### UC-BK-07: Auto-Cancel Expired Booking

```
title UC-BK-07: Auto-Cancel Expired Booking

participant Scheduler
participant System
participant NotifService
database DB

Scheduler->System:Scheduled job runs (~hourly)
System->DB:Find bookings: status = awaiting payment AND deadline has passed
DB-->System:Return list of overdue bookings
loop For each overdue booking
  System->DB:Set status to cancelled
  System->DB:Release reserved dates
  System->NotifService:Send cancellation notification (in-app + email) to guest
  System->DB:Write audit log entry
end
```

---

## Module 4 – Payment

### UC-PAY-01: Make Payment

```
title UC-PAY-01: Make Payment

actor Guest
participant System
participant PaymentGateway
participant NotifService
database DB

Guest->System:Click Pay Now (from booking detail or bill notification)
System->PaymentGateway:Create payment request
PaymentGateway-->System:Return payment session URL
System-->Guest:Redirect to secure payment page
Guest->PaymentGateway:Complete payment
PaymentGateway->System:Send payment confirmation
System->System:Verify confirmation is genuine
alt Confirmation not genuine
  System->DB:Record security alert
  System-->Guest:Payment could not be verified
else Duplicate confirmation
  System->System:Detect duplicate – ignore without re-processing
else Valid confirmation
  System->DB:Record payment as successful
  System->DB:Update booking status to confirmed
  System->DB:Generate QR access code (valid check-in to check-out)
  System->DB:Generate payment receipt
  System->NotifService:Send booking confirmation (in-app + email) with receipt and QR code to guest
  System->NotifService:Notify admin of new confirmed booking
end

alt Payment declined or failed
  PaymentGateway->System:Payment failed notification
  System->DB:Record payment as failed
  System-->Guest:Error message + Try Again option
end
```

---

### UC-PAY-02: View Payment & Billing Records

```
title UC-PAY-02: View Payment & Billing Records

actor User
participant System
database DB

note over User,System:--- Guest view ---
User->System:Go to My Bookings → booking detail or Payment History
System->DB:Retrieve all payment records for this guest
DB-->System:Return records
System-->User:Display: payment number, booking reference, date, amount, status
User->System:Click bill – view or download itemised bill\n(nightly rate, nights, deposit, total, deadline)
User->System:Click receipt (if payment completed) – view or download

note over User,System:--- Admin view ---
User->System:Go to Payment Management
System->DB:Retrieve all billing and payment records
DB-->System:Return all records
System-->User:Display: reference, guest name, unit, amount, status, date
User->System:Apply filters (date range, reference, status)
System->DB:Apply filters
DB-->System:Filtered results
System-->User:Update displayed list
User->System:Click entry to view details or take action
```

---

### UC-PAY-03: Regenerate Bill / Receipt

```
title UC-PAY-03: Regenerate Bill / Receipt

actor Admin
participant System
participant EmailService
database DB

Admin->System:Go to billing or payment detail page
Admin->System:Click Regenerate Bill or Regenerate Receipt
System->DB:Retrieve latest booking and payment data
System->System:Generate fresh PDF document
System->DB:Store new document
System-->Admin:Document available for download
System->EmailService:Optionally resend document to guest via email
```

---

## Module 5 – Notification

### UC-NOTIF-01: Receive System Notification

```
title UC-NOTIF-01: Receive System Notification

participant TriggerEvent
participant System
participant EmailService
actor User

note over TriggerEvent,System:--- In-App Notification ---
TriggerEvent->System:Significant event occurs\n(booking confirmed, payment, cancellation, etc.)
System->System:Create notification record for relevant user
alt User is currently online
  System-->User:Bell icon badge updates in real time
end
User->System:Click bell icon
System-->User:Show notifications list (newest first, read + unread)
User->System:Click a notification
System->System:Mark notification as read
System-->User:Navigate to relevant page (e.g., booking detail)

note over TriggerEvent,System:--- Email Notification ---
TriggerEvent->System:Significant event occurs
System->System:Check if email notifications are globally enabled (UC-SET-01)
alt Email enabled
  System->EmailService:Send email to user's registered address
  EmailService-->User:Email arrives in inbox
end

note over TriggerEvent,System:--- Automated Reminders (Scheduled) ---
System->System:Daily scheduled check runs
System->System:Find awaiting-payment bookings with deadline approaching
System->EmailService:Send payment reminder (reference, amount, deadline, payment link)
System->System:Find confirmed bookings with check-in/out within reminder window
System->EmailService:Send check-in/out reminder to guest and admin
```

---

### UC-NOTIF-02: Google Calendar Integration

```
title UC-NOTIF-02: Google Calendar Integration

participant System
participant GoogleCalendar
database DB

System->System:Detect booking confirmed event
System->DB:Retrieve user's stored Google account connection
alt User has not connected Google Calendar
  System->System:Skip calendar step – no error shown
else Connection exists
  System->GoogleCalendar:Create or update calendar event\n(title: unit name + Stay, start: check-in, end: check-out,\ndescription: booking reference + unit address)
  alt Google Calendar error or connection expired
    System->DB:Record failure
    note over System:User may need to reconnect Google account
  else Success
    GoogleCalendar-->System:Event confirmed
    note over GoogleCalendar:Event appears in user's Google Calendar
  end
end
```

---

## Module 6 – Chat

### UC-CHAT-01: Send / Receive Messages

```
title UC-CHAT-01: Send / Receive Messages

actor Sender
participant System
actor Recipient
database DB

Sender->System:Open Chat page
Sender->System:Type message in input field
Sender->System:Click Send (or press Enter)
alt Message is empty
  System-->Sender:Send button disabled – empty message not allowed
else Message has content
  System->DB:Save message (sender, recipient, content, timestamp)
  alt Recipient is online
    System-->Recipient:Deliver message instantly to chat window
    System-->Recipient:Update unread count badge on chat icon
  else Recipient is offline
    note over System:Message stored – shown on next sign-in
  end
  System-->Sender:Show message as sent in chat window
end
```

---

### UC-CHAT-02: View Chat History

```
title UC-CHAT-02: View Chat History

actor User
participant System
database DB

User->System:Open Chat page
System->DB:Retrieve all messages in conversation (oldest to newest)
DB-->System:Return message list
System-->User:Display messages:\n- Sent messages on right\n- Received messages on left\n- Each with sender name and timestamp
System->DB:Mark all unread messages as read
System-->User:Auto-scroll to most recent message
```

---

## Module 7 – User & Access Management

### UC-USR-01: Create User Account

```
title UC-USR-01: Create User Account (Admin)

actor Admin
participant System
participant EmailService
database DB

Admin->System:User Management → Create User
Admin->System:Enter full name, email, assigned role
System->DB:Check if email already registered
alt Email already exists
  System-->Admin:Validation error – edit existing account instead
else Email is unique
  System->System:Generate temporary password
  System->DB:Create user record (forced password change flag set)
  System->EmailService:Send email with temporary password and sign-in link
  System->DB:Write audit log entry
  System-->Admin:Success message
end
```

---

### UC-USR-02: Edit / Activate / Deactivate User

```
title UC-USR-02: Edit / Activate / Deactivate User

actor Admin
participant System
database DB

note over Admin,System:--- Edit User ---
Admin->System:User Management → select user → Edit
System->DB:Retrieve user record
DB-->System:Return data
System-->Admin:Pre-filled edit form
Admin->System:Update fields → Save
System->DB:Validate (email unique) and save changes
System->DB:Write audit log entry
System-->Admin:Success message

note over Admin,System:--- Activate / Deactivate ---
Admin->System:Select user → click Deactivate or Activate
System-->Admin:Confirmation dialog
Admin->System:Confirm
alt Deactivating
  System->DB:Disable account
  System->System:Immediately end all active sessions for this user
else Activating
  System->DB:Restore account to active status
end
System->DB:Write audit log entry
System-->Admin:Success message
```

---

### UC-USR-03: Reset User Password

```
title UC-USR-03: Reset User Password (Admin)

actor Admin
participant System
participant EmailService
database DB

Admin->System:User Management → select user → Reset Password
Admin->System:Choose reset method
alt Option A: Send reset link
  System->EmailService:Send password reset email to user
else Option B: Set to default
  System->DB:Set password to default temporary password
end
System->DB:Set forced password change flag on account
System->DB:Remove lock, reset failed-attempts counter (if locked)
System->System:Notify user (in-app + email)
System->DB:Write audit log entry
```

---

### UC-USR-04: Manage Roles

```
title UC-USR-04: Manage Roles

actor Admin
participant System
database DB

note over Admin,System:--- Create Role ---
Admin->System:Role Management → Create Role
Admin->System:Enter role name and optional description
System->DB:Check name is unique
System->DB:Create role record
System-->Admin:Navigate to role detail page to assign permissions

note over Admin,System:--- Edit Role ---
Admin->System:Select role → Edit
Admin->System:Modify name or description
System->DB:Check name is unique → Save updated role

note over Admin,System:--- Assign Permissions ---
Admin->System:Role Management → select role → Manage Permissions
System->DB:Retrieve all permissions with current assignments
System-->Admin:Show checklist of permissions (ticked if currently assigned)
Admin->System:Tick / untick permissions → Save
System->DB:Update role permissions (takes effect immediately for all users)
System->DB:Write audit log entry

note over Admin,System:--- Delete Role ---
Admin->System:Select role → Delete
System->DB:Check if any users have this role
alt Users have this role
  System-->Admin:Block – role assigned to N users, reassign first
else No users
  System-->Admin:Confirmation prompt
  Admin->System:Confirm
  System->DB:Delete role
  System->DB:Write audit log entry
end
```

---

### UC-USR-05: Manage Permissions

```
title UC-USR-05: Manage Permissions

actor Admin
participant System
database DB

note over Admin,System:--- Create Permission ---
Admin->System:Permission Management → Create Permission
Admin->System:Enter permission name and optional description
System->DB:Check name is unique
System->DB:Create permission record
System-->Admin:Permission created

note over Admin,System:--- Edit Permission ---
Admin->System:Select permission → Edit
Admin->System:Modify name or description
System->DB:Save update

note over Admin,System:--- Delete Permission ---
Admin->System:Select permission → Delete
System->DB:Check if permission is attached to any roles
alt Attached to roles
  System-->Admin:Block – attached to N role(s), remove from roles first
else Not attached
  System-->Admin:Confirmation prompt
  Admin->System:Confirm
  System->DB:Delete permission
  System->DB:Write audit log entry
end
```

---

## Module 8 – System Settings

### UC-SET-01: Configure System Settings

```
title UC-SET-01: Configure System Settings

actor Admin
participant System
database DB

Admin->System:Go to System Settings
Admin->System:Navigate to relevant section and update values:
note over Admin:Email: mail server, port, username, password, encryption, sender\nSecurity: max failed attempts, lockout duration, session timeout\nCancellation Policy: tiered refund rules (days + refund %)\nPayment & Billing: payment credentials, bill number prefix, payment window\nExtension Charges: hourly rate, nightly rate\nExtension Payment Window: default minutes to pay extension\nEmail Notifications Toggle: enable/disable global emails
Admin->System:Optionally test email connection
alt Email test fails
  System-->Admin:Show connection error – correct and retry
end
Admin->System:Click Save
System->System:Validate settings (positive numbers, non-overlapping tiers, valid percentages)
alt Validation fails
  System-->Admin:Show specific validation errors
else Valid
  System->DB:Save all settings
  System-->Admin:Settings saved – all future operations use new values
end
```

---

### UC-SET-02: Manage Default Homestay Policies

```
title UC-SET-02: Manage Default Homestay Policies

actor Admin
participant System
database DB

Admin->System:System Settings → Default Policies
System->DB:Retrieve current default house rules
DB-->System:Return rules (e.g., No Pets, No Durians, No Smoking)
System-->Admin:Display list of default rules

alt Add new rule
  Admin->System:Enter new rule description → Save
  System->DB:Add rule to default list
else Edit existing rule
  Admin->System:Modify rule text → Save
  System->DB:Update rule text
else Remove rule
  Admin->System:Click Delete on a rule
  System-->Admin:Confirmation prompt
  Admin->System:Confirm
  System->DB:Remove rule from default list
end
note over System:Changes apply to NEW units only – existing units are not affected
```

---

## Module 9 – Audit Logs

### UC-AUDIT-01: View / Filter Audit Trail

```
title UC-AUDIT-01: View / Filter Audit Trail

actor Admin
participant System
database DB

Admin->System:Go to Audit Logs section
System->DB:Retrieve all log entries (reverse chronological order)
DB-->System:Return entries
System-->Admin:Display entries: timestamp, actor (user name or System),\nevent type, what was affected
Admin->System:Page through entries

note over Admin,System:--- Filtering ---
Admin->System:Select filter criteria (date range, event type, user name)
System->DB:Apply filters
DB-->System:Return matching entries
System-->Admin:Display filtered log
Admin->System:Clear filters
System-->Admin:Return to full log view
note over Admin:Log entries are read-only – cannot be edited or deleted
```

---

### UC-AUDIT-02: Automatic Event Logging

```
title UC-AUDIT-02: Automatic Event Logging

participant Actor
participant System
database DB

note over Actor,System:--- User / Admin Action ---
Actor->System:Performs significant action\n(sign-in, booking, payment, feedback, user management, settings change, etc.)
System->DB:Auto-create log entry: timestamp, actor name, event type, affected record
DB-->System:Entry saved (permanent and immutable)

note over Actor,System:--- Automated System Action ---
System->System:Scheduled process triggers\n(auto-cancel expired booking, expire QR code, process payment, etc.)
System->DB:Auto-create log entry: timestamp = System, event type, affected record
DB-->System:Entry saved (permanent and immutable)
```

---

## Module 10 – QR Code & Access

### UC-QR-01: Receive & Use QR Code

```
title UC-QR-01: Receive & Use QR Code

actor Guest
participant System
participant SmartLock
participant NotifService
database DB

System->System:Payment confirmed (UC-PAY-01)
System->DB:Generate unique QR code (valid: check-in time → check-out time, status: active)
System->NotifService:Send in-app notification to guest with QR code
System->NotifService:Send email to guest with QR code and booking details
Guest->System:View QR code on booking detail page (anytime)

note over Guest,SmartLock:--- Using the QR Code at the property ---
Guest->SmartLock:Present QR code to smart lock scanner
SmartLock->System:Validate QR code
System->DB:Check code status and current time within valid window
alt Code is expired (past check-out time)
  System-->SmartLock:Deny – QR Code Expired
else Code is inactive or revoked
  System-->SmartLock:Deny – access not granted
else Code is valid
  System-->SmartLock:Grant access
  SmartLock-->Guest:Door opens
end
```

---

### UC-QR-02: Manage Housekeeping Cycle

```
title UC-QR-02: Manage Housekeeping Cycle

participant Scheduler
actor Admin
participant System
participant NotifService
database DB

note over Scheduler,System:--- Automatic QR Expiry (Scheduled) ---
Scheduler->System:Scheduled job runs (around check-out times)
System->DB:Find active QR codes with valid-until time passed
loop For each expired code
  System->DB:Set QR code to expired
  System->DB:Update booking status to completed
end

note over Admin,System:--- Housekeeping Access ---
Admin->System:Booking / unit management → Generate Housekeeping QR
Admin->System:Set validity window (e.g., 4 hours)
System->DB:Generate temporary QR code (type: housekeeping)
System-->Admin:Display housekeeping QR code for sharing with cleaning staff

note over Admin,System:--- Mark Housekeeping Complete ---
Admin->System:Click Mark Housekeeping Complete
System->DB:Set housekeeping QR code to expired
System->DB:Check for next confirmed booking on this unit
alt Next confirmed booking exists
  System->DB:Generate new QR code for next guest
  System->NotifService:Send QR code to next guest (in-app + email)
else No next booking
  System->System:Skip QR generation – no action needed
end
```

---

### UC-QR-03: Initiate Booking Extension

```
title UC-QR-03: Initiate Booking Extension

actor Admin
participant System
participant NotifService
database DB

Admin->System:Open active booking → click Extend Stay
Admin->System:Select extension type:\n  Time extension: later check-out time (same day)\n  Date extension: extra nights
Admin->System:Enter new check-out date/time
System->DB:Check availability for extended period
alt Extended dates conflict with another booking
  System-->Admin:Show conflicting booking – select a different period
else Available
  System->System:Calculate additional charge:\n  Time: hours × hourly rate\n  Date: nights × nightly rate
  System->DB:Create extension record (status: awaiting payment)
  System->DB:Generate extension bill
  System->System:Set payment deadline (unit-specific window or system default)
  System->NotifService:Notify guest: extension charge, deadline, direct payment link
  System->DB:Write audit log entry
end
note over System:QR code validity is NOT extended yet – updated only after payment
```

---

### UC-QR-04: Pay Extension Charge

```
title UC-QR-04: Pay Extension Charge

actor Guest
participant System
participant PaymentGateway
participant NotifService
database DB

Guest->System:Open booking detail → find pending extension charge
Guest->System:Click Pay Extension Charge
System->PaymentGateway:Redirect to secure payment page with extension bill amount
Guest->PaymentGateway:Complete payment
PaymentGateway->System:Send payment confirmation
System->System:Verify confirmation is genuine
System->DB:Record payment as successful
System->DB:Update extension status to confirmed
System->DB:Update booking check-out date and time to extended values
System->DB:Extend QR code validity to new check-out date/time
System->NotifService:Notify guest: stay extended, QR code valid until [new check-out]
System->DB:Write audit log entry
```

---

### UC-QR-05: Auto-Cancel Extension

```
title UC-QR-05: Auto-Cancel Extension

participant Scheduler
participant System
participant NotifService
database DB

Scheduler->System:Scheduled job runs (every few minutes)
System->DB:Find extension records: status = awaiting payment AND deadline has passed
loop For each overdue extension
  System->DB:Set extension status to cancelled
  System->DB:Revert booking check-out date/time to original values
  note over System:QR code is NOT modified – already reflects original check-out time
  System->NotifService:Notify guest: extension cancelled – original check-out remains
  System->DB:Write audit log entry
end
```

---

## Module 11 – Reporting & Analytics

### UC-RPT-01: View Analytics Dashboard

```
title UC-RPT-01: View Analytics Dashboard

actor Admin
participant System
database DB

Admin->System:Go to Reporting & Analytics section
System->DB:Calculate KPIs:\n  Total bookings (month + YTD)\n  Total revenue (month + YTD)\n  Occupancy rate\n  Cancellation rate\n  Average guest rating (visible reviews only)
System->DB:Retrieve data for charts:\n  Booking trends (line chart – daily/weekly/monthly toggle)\n  Revenue summary\n  Per-unit booking breakdown (bar chart: bookings, occupancy, revenue)\n  Feedback & rating summary per unit
DB-->System:Return all data
System-->Admin:Display KPI cards and interactive charts
Admin->System:Interact with charts (filter by date range or unit)
System->DB:Apply filters and recalculate
DB-->System:Filtered data
System-->Admin:Update dashboard
```

---

### UC-RPT-02: View Revenue Report

```
title UC-RPT-02: View Revenue Report

actor Admin
participant System
database DB

Admin->System:Go to Reporting → Revenue Report
Admin->System:Apply filters: date range, unit (all or specific), payment status
System->DB:Retrieve matching payment and booking data
DB-->System:Return filtered records
System-->Admin:Display: total revenue, itemised breakdown by booking, summary by unit
Admin->System:Click Export PDF or Export CSV
System-->Admin:Trigger export (see UC-RPT-03)
```

---

### UC-RPT-03: Export Report

```
title UC-RPT-03: Export Report

actor Admin
participant System
database DB

Admin->System:Click Export PDF or Export CSV on a report page
System->DB:Collect currently filtered report data
DB-->System:Return data
System->System:Generate file in chosen format (PDF or CSV)
System-->Admin:File automatically downloaded to admin's device
```

---

## Module 12 – Guest Feedback

### UC-FB-01: Submit Rating & Feedback

```
title UC-FB-01: Submit Rating & Feedback

actor Guest
participant System
database DB

Guest->System:My Bookings → History → find completed booking → Leave a Review
System->DB:Verify booking is completed and no review exists yet
alt Review already submitted
  System-->Guest:Leave a Review button is hidden – show View Your Review
else Booking not completed (direct URL access)
  System-->Guest:Block action – validation error
else Eligible for review
  System-->Guest:Show feedback form (star rating 1–5, optional comment)
  Guest->System:Select star rating and optionally write comment
  Guest->System:Click Submit
  alt Star rating not selected
    System-->Guest:Highlight star rating as required – block submission
  else Rating selected
    System->DB:Save review (linked to booking, unit, guest; visibility: visible)
    System->DB:Recalculate unit's average rating (visible reviews only)
    System-->Guest:Thank-you confirmation message
    System-->Guest:Hide Leave a Review button for this booking
  end
end
```

---

### UC-FB-02: View Submitted Feedback

```
title UC-FB-02: View Submitted Feedback

actor Guest
participant System
database DB

Guest->System:Go to My Reviews or view completed booking in history
System->DB:Retrieve all review records for signed-in guest
DB-->System:Return review list
System-->Guest:Display each entry (read-only):\n  unit name, check-in/out dates, star rating,\n  comment, submission date, admin reply (if any)
note over Guest:Reviews cannot be edited after submission
```

---

### UC-FB-03: View / Manage All Feedback (Admin)

```
title UC-FB-03: View / Manage All Feedback (Admin)

actor Admin
participant System
database DB

note over Admin,System:--- View ---
Admin->System:Go to Guest Feedback management
System->DB:Retrieve all review records including hidden ones
DB-->System:Return records
System-->Admin:Display: guest name, unit, booking ref, rating, comment,\ndate, visibility status, admin reply status
Admin->System:Filter by unit or visibility status
System->DB:Apply filter
System-->Admin:Update list

note over Admin,System:--- Reply to Review ---
Admin->System:Select review → Reply
Admin->System:Type response → click Publish Reply
System->DB:Save reply with reply date
System-->Admin:Reply immediately visible on unit detail page
note over System:Guest also sees reply when viewing their own feedback (UC-FB-02)

note over Admin,System:--- Hide / Moderate ---
Admin->System:Select review with inappropriate content → Hide
System-->Admin:Confirmation: Hide this review from public view?
alt Admin cancels
  System-->Admin:No changes made
else Admin confirms
  System->DB:Set review visibility to hidden
  System->DB:Recalculate unit's average rating (excluding hidden review)
  note over System:Review removed from guest-facing unit detail page
end

note over Admin,System:--- Restore Hidden Review ---
Admin->System:Select hidden review → Restore
System->DB:Set visibility back to visible
System->DB:Recalculate unit's average rating
```

---

### UC-FB-04: Display Average Rating

```
title UC-FB-04: Display Average Rating

actor User
participant System
database DB

User->System:View homestay listing page or unit detail page
System->DB:Retrieve all visible (non-hidden) reviews for each unit
DB-->System:Return review data
alt No visible reviews exist for a unit
  System-->User:Display No reviews yet on unit card/detail page
else Visible reviews exist
  System->System:Calculate average rating:\n  sum of ratings ÷ number of visible reviews
  System-->User:Display X.X ★ (N reviews) on unit card and detail page
end
note over System:Rating updates automatically when:\n- New review submitted (UC-FB-01)\n- Review hidden or restored (UC-FB-03)
```
