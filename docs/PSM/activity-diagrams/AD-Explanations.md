# Activity Diagram Explanations

## HomeLodge – Booking Homestay System

---

## Module 01 – Authentication

Figure 4.13 shows the activity diagram for the Register Account function (UC-AUTH-01). The diagram is split into two swimlanes, Guest and System, to show which party performs each step. The guest opens the registration page, fills in their name, email address, password, and password confirmation, then submits the form. The System validates all fields. If the email format is wrong, the password is too weak, or the confirmation does not match, the System highlights the failing fields and the guest corrects the entries. If the email is already registered, the System shows a separate message suggesting the Forgot Password function. When all inputs pass, the System hashes the password using bcrypt, creates the user record with the Guest role, and redirects to the login page. If the database write fails at any point, the System logs the error and shows a generic failure message.

---

Figure 4.14 shows the activity diagram for the Login and Login via Google SSO function (UC-AUTH-02). The diagram has two swimlanes, User and System. After the user opens the login page, the first decision point branches on the chosen login method. For email and password login, the user submits credentials and the System checks whether they are valid and whether the account is currently locked. An invalid or locked attempt increments the failed-login counter, may trigger a lockout, shows the appropriate error, and sends the user back to the credential entry step. For Google SSO, the user clicks Sign in with Google and is redirected to Google's OAuth consent screen. If the user denies permission, the flow ends with a cancellation message. If permission is granted, the System exchanges the authorisation code to obtain the user's name, email, and Google ID, then checks whether an account already exists with that email or Google ID. If no account exists, a new one is created with the Guest role and SSO-only access. If one exists, the System links the Google ID to it if not yet linked. Both login paths then converge at the active-account check. A deactivated account ends the flow with an access-denied message. For active accounts, the System checks the `must_change_password` flag. If the flag is set, the user is redirected to the Force Change Password page and cannot go elsewhere until that step is done. If the flag is clear, the System creates an authenticated session, records the login timestamp, resets the failed-attempt counter, and redirects the user to their role-appropriate dashboard.

---

Figure 4.15 shows the activity diagram for the Logout function (UC-AUTH-03). The diagram has two swimlanes, User and System. When the user clicks Logout from any authenticated page, the System checks whether the session is still active. If it has already expired, the System redirects to the login page directly. If the session is active, the System invalidates and destroys it, clears any remember-me tokens, and then redirects to the login page.

---

Figure 4.16 shows the activity diagram for the Forgot Password function (UC-AUTH-04). The diagram has two swimlanes, User and System, and covers the full reset flow from request to completion. The user clicks Forgot Password on the login page and enters their registered email address. The System checks whether the address exists in the database. If it does not, the System shows a deliberately vague message — that a link has been sent if an account exists — to avoid leaking account information to an attacker. If the email does exist, the System generates a unique, time-limited reset token and tries to send the link. If the email delivery fails, the System logs the error and shows a generic message. If it succeeds, the user opens the email, clicks the link, and the System validates the token. An expired or already-used token ends the flow with an error and a prompt to request a new link. A valid token opens the password reset form. The user enters a new password and confirmation. The System checks the password against the complexity rules; failing entries are highlighted and the user must try again. Once the password is accepted, the System hashes and saves it, invalidates the token, lifts any existing account lockout, and redirects to the login page.

---

Figure 4.17 shows the activity diagram for the View and Update Profile function (UC-AUTH-05). The diagram has two swimlanes, User and System. The user opens the Profile page and the System loads the current data: name, email, phone number, and profile photo. The user edits any of these fields and may upload a new photo, then submits. The System validates the phone number format and checks that any uploaded image has the right file type and size. Failing inputs are highlighted and the user revises them. When inputs are valid, the System saves the record. If the file storage operation fails, the System rolls back the change and notifies the user. If the save succeeds, a confirmation message is displayed.

---

Figure 4.18 shows the activity diagram for the Force Change Password function (UC-AUTH-06). This flow starts immediately after an admin resets a user's password, which sets the `must_change_password` flag to true. The diagram has two swimlanes, User and System. The user logs in with the temporary password the admin provided. The System detects the flag, redirects the user to the Change Password page, and blocks every other route until the step is completed. The user enters a new password and a confirmation. The System validates against two conditions: the password must meet the complexity rules and must differ from the temporary password. A weak password gets the failing rules highlighted. Using the same temporary password triggers a specific warning. In both cases the user returns to the entry step. Once both conditions are met, the System hashes and saves the new password, sets `must_change_password` to false, and redirects the user to their dashboard.

---

## Module 02 – Homestay Management

Figure 4.19 shows the activity diagram for the Browse Homestay Units function (UC-HS-01). The diagram has two swimlanes, Guest and System. The guest opens the homestay listing page and the System queries for all units where `is_active` is true. If none are found, the System shows a message that no units are currently available. If active units exist, the System renders a card per unit showing the name, thumbnail, base price, location, and average guest rating. The guest selects a card and is taken to the unit detail page covered by UC-HS-02.

---

Figure 4.20 shows the activity diagram for the View Unit Details and Availability function (UC-HS-02). The diagram has two swimlanes, Guest and System. When the guest selects a unit, the System loads the full unit record: description, images, pricing, check-in and check-out times, house policies, and a real-time availability calendar. Available dates appear in green, booked or payment-pending dates in red, and admin-blocked dates in grey. The guest can move between months to check future availability. Clicking any available date starts the booking flow covered by UC-BK-01.

---

Figure 4.21 shows the activity diagram for the Create Homestay Unit function (UC-HS-03). The diagram has two swimlanes, Admin and System. The admin opens the Create Unit page and fills in the name, description, location, base price, deposit amount, check-in and check-out times, house policies, and one or more unit images. The System validates each uploaded image for file type and size. Any failing image is flagged and the admin corrects it before resubmitting. Once all fields and images are valid, the System creates the unit record, stores the images, copies the default policies from the System Settings module to the new unit, and confirms the creation. The unit becomes active and visible to guests immediately.

---

Figure 4.22 shows the activity diagram for the Edit Homestay Unit function (UC-HS-04). The diagram has two swimlanes, Admin and System. The admin opens a unit's edit page and the System pre-fills all existing data. The admin changes any combination of fields — name, description, location, pricing, check-in and check-out times, house policies, or images — and submits. The System validates the submission the same way as the create flow. Validation failures return the admin to the form with errors shown. When validation passes, the System saves the updated record and confirms.

---

Figure 4.23 shows the activity diagram for the Deactivate and Delete Unit function (UC-HS-05). The diagram has two swimlanes, Admin and System. The admin selects a unit and chooses to deactivate or delete it. Before acting, the System checks for confirmed bookings with future check-in dates on that unit. If any exist, the operation is blocked and the admin is told to cancel or reassign those bookings first. If none exist, the System carries out the requested action. Deactivation sets `is_active` to false, hiding the unit from guests while keeping all historical records. Deletion performs a soft delete, removing the unit from public views while preserving all booking and payment data in the database. Both outcomes are confirmed to the admin.

---

Figure 4.24 shows the activity diagram for the View All Units List function (UC-HS-06). The diagram has two swimlanes, Admin and System. The admin opens the Units Management page and the System retrieves all unit records, active and inactive, displaying each unit's name, status, and a count of upcoming confirmed bookings. The admin can click any unit to open its detail or edit page. This page is the main starting point for all unit management actions.

---

## Module 03 – Booking

Figure 4.25 shows the activity diagram for the View Availability and Select Dates function (UC-BK-01). The diagram has two swimlanes, Guest and System. The guest opens a unit's detail page and sees the availability calendar, which the System populates with live data from the database. Available dates are green, booked or payment-pending dates are red, and blocked dates are grey. The guest picks a check-in date and the System highlights valid check-out options based on the minimum stay. After the guest selects a check-out date, the System re-validates the full range against current data. If another user has taken any date in the range since the calendar first loaded, the System shows a conflict message and clears the selection. If the range is still free, the System shows a booking summary with the selected dates, number of nights, nightly rate, deposit, and total charge. The guest can then submit the booking or go back to choose different dates.

---

Figure 4.26 shows the activity diagram for the Submit Booking function (UC-BK-02). The diagram has two swimlanes, Guest and System. After reviewing the booking summary, the guest confirms and clicks Submit. The System runs a final availability check on the dates before writing anything to the database. If the dates have been taken in the meantime, the guest is returned to the calendar with a conflict message. If they are still free, the System creates the booking record with status `pending_payment`, generates a bill with a unique bill number, and holds the dates for twenty-four hours to allow time for payment. The guest is then sent to the payment page to continue with UC-PAY-01.

---

Figure 4.27 shows the activity diagram for the View Bookings function (UC-BK-03). The diagram has two swimlanes, User and System, with both the Guest and the Admin represented in the User lane since both see the same page structure. The user opens the Bookings page and the System retrieves the relevant booking records: a guest sees only their own, while an admin sees all bookings across the system. Records are split into two tabs — Current for confirmed and pending-payment bookings, and Past for completed and cancelled ones. The admin view includes additional filter controls for date range, unit, status, and booking ID. Either actor can click a booking to open the detail view covered by UC-BK-04.

---

Figure 4.28 shows the activity diagram for the View Booking Details function (UC-BK-04). The diagram has two swimlanes, User and System. The user selects a booking and the System loads the full record onto a single page: booking number, unit name, check-in and check-out dates and times, status, total charge, bill and payment records, the applicable cancellation policy, and the estimated refund if the booking were cancelled now. If a QR code has been issued for the booking, it appears on this page as well. In the admin view, the page also shows the guest's name and contact details alongside booking management actions.

---

Figure 4.29 shows the activity diagram for the Cancel Booking function (UC-BK-05). The diagram has two swimlanes, User and System, where User is either the Guest or the Admin depending on who initiates the cancellation. The user opens the booking detail page and clicks Cancel Booking. The System fetches the applicable cancellation policy, calculates the refund amount based on days remaining before check-in, and shows a confirmation dialog with the policy terms and the exact refund figure. If the user dismisses the dialog, nothing changes and the flow ends. If the user confirms, the System sets the booking status to `cancelled`, releases the dates for new reservations, and creates a refund record for the calculated amount. A notification then goes to the guest with the cancellation confirmation and the expected refund timeline.

---

Figure 4.30 shows the activity diagram for the Manage Booking function, which is the admin-only flow (UC-BK-06). This is the most complex diagram in the Booking module because it covers five separate administrative actions in a single diagram. The diagram has two swimlanes, Admin and System. After the admin opens the Booking Management page, a decision point branches into one of five paths.

For View/Filter, the admin applies filters by status, date range, unit, or booking ID, and the System returns the matching list.

For Create, the admin selects a guest user, a unit, and the desired dates. The System checks availability. If the dates are taken, a conflict message is shown and the admin re-selects. If they are free, the System creates the booking with status `pending_payment`, auto-generates the bill, and notifies the guest.

For Edit, the admin modifies fields on an existing booking. If the check-in or check-out dates change, the System re-validates availability for the new range before saving. The record is updated and the guest is notified.

For Delete/Cancel, the admin confirms the action in a dialog. The System then cancels or deletes the booking, releases the dates, notifies the guest, and writes an audit log entry.

For Block Dates, the admin selects a unit and a date range. The System checks for confirmed bookings within that range. If any exist, the admin is warned and asked to cancel them first. If none exist, the System creates the blocked-date records and the dates are immediately removed from the guest-facing calendar.

---

Figure 4.31 shows the activity diagram for the Auto-Cancel Expired Booking function (UC-BK-07). The diagram has two swimlanes, Scheduler and System. No human actor is involved; the flow runs entirely as a background job. The Scheduler triggers the job on a regular interval. The System queries for all bookings with status `pending_payment` where the `payment_deadline` has passed. If none are found, the job ends without doing anything. For each expired booking found, the System sets the status to `cancelled`, releases the held dates, and sends a notification to the guest explaining that the booking was cancelled due to non-payment. The job repeats this for each record in the result before completing.

---

## Module 04 – Payment

Figure 4.32 shows the activity diagram for the Make Payment function (UC-PAY-01). The diagram has three swimlanes — Guest, Payment Gateway, and System — to show where control passes between parties. The guest clicks Pay Now on their pending bill page. The System redirects them to the payment gateway's hosted page, passing the bill amount and a reference number. The guest completes the payment on the gateway side. After the transaction, the Payment Gateway sends an HTTPS webhook to the System's webhook endpoint. The System verifies the payload's signature against the configured secret key. An invalid signature causes the request to be logged and discarded. If the signature is valid, the System checks the payment result. A failed payment updates the record to `failed` status and notifies the guest with the reason and a retry prompt. A successful payment updates the record to `succeeded`, moves the booking from `pending_payment` to `confirmed`, generates a QR code, and sends the guest a confirmation with the booking details and the QR code.

---

Figure 4.33 shows the activity diagram for the View Payment and Billing Records function (UC-PAY-02). The diagram has two swimlanes, User and System. The user opens the Payments section. The System loads the relevant records: a guest sees only their own bills and transactions, while an admin sees all records across all units and guests. The page uses a tabbed layout separating Bills from Payments. The admin can filter by date range, unit, or payment status. Both actors can open the full detail of any record. The admin can also download a PDF receipt for any completed payment.

---

Figure 4.34 shows the activity diagram for the Regenerate Bill and Receipt function (UC-PAY-03). The diagram has two swimlanes, Admin and System. This function is only available to the admin. The admin opens a booking or extension payment record and clicks Regenerate for either the bill or the receipt. The System fetches the current record data, renders the PDF using the stored values, and delivers it to the browser as a download. Regenerating a document does not change any underlying data.

---

## Module 05 – Notification

Figure 4.35 shows the activity diagram for the Receive System Notification function (UC-NOTIF-01). The diagram has two swimlanes, User and System. Notifications start on the System side — they are triggered by events, not by user action. When a notifiable event occurs, such as a booking confirmation, payment receipt, cancellation, extension billing, or payment reminder, the System creates a notification record in the database and pushes a WebSocket update to the user's bell icon so the badge count changes without a page refresh. At the same time, the System sends an email to the user's registered address. The user can then click the bell to open the notification panel and read the listed items. Clicking a specific notification opens the relevant page — such as the booking detail or the bill — and marks that notification as read.

---

Figure 4.36 shows the activity diagram for the Google Calendar Integration function (UC-NOTIF-02). The diagram has three swimlanes, System, Google Calendar API, and User. The flow starts automatically when a booking moves to `confirmed` status after payment. The System builds a calendar event payload with the booking reference number, unit name, check-in date and time, and check-out date and time, then sends it to the Google Calendar API using the OAuth access token stored at the time of the user's Google SSO login. The API creates the event and returns a success response. The System saves the returned event ID against the booking record so the event can be updated or removed if the booking changes later. The user then finds the booking in their Google Calendar without doing anything further.

---

## Module 06 – Chat

Figure 4.37 shows the activity diagram for the Send and Receive Messages function (UC-CHAT-01). The diagram has two swimlanes, User and System, where User covers both the Guest and the Admin since both use the same interface. When the user opens the Chat page, the System checks whether a conversation record already exists between them and the other party. If none exists, the System creates one before loading the chat view. The user types a message and clicks Send. The System saves the message to the database, ties it to the conversation and the sender, and immediately broadcasts it to the recipient over the WebSocket connection managed by Laravel Reverb. The recipient sees the message appear in real time. A typing indicator is shown on the recipient's screen while the other person is composing. Both parties can continue the conversation from there.

---

Figure 4.38 shows the activity diagram for the View Chat History function (UC-CHAT-02). The diagram has two swimlanes, User and System. The user opens the Chat page and the System retrieves the full message history for the conversation, ordered from oldest to newest. The current user's messages appear on the right, the other party's on the left. The System marks all unread messages as read on load. For long conversations, the System loads the most recent messages first and lets the user scroll up to fetch earlier ones through pagination.

---

## Module 07 – User & Access Management

Figure 4.39 shows the activity diagram for the Create User Account function (UC-USR-01). The diagram has two swimlanes, Admin and System. The admin opens the User Management page, clicks Create User, and fills in the new user's name, email, and assigned role. The System validates that the email is correctly formatted and is not already in use. Validation failures return the admin to the form with errors shown. When the inputs are valid, the System creates the account with a generated temporary password, assigns the selected role, sets `must_change_password` to true, and emails the new user their temporary credentials. The user must change the password on their first login before accessing anything else.

---

Figure 4.40 shows the activity diagram for the Edit, Activate, and Deactivate User function (UC-USR-02). The diagram has two swimlanes, Admin and System. The admin selects a user from the list and opens their edit page. The System loads the current user data. The admin can update the name, email, or role assignment. Separately, the admin can toggle the active status. If the admin deactivates a user, the System invalidates all that user's active sessions immediately, preventing further access. Re-activating the account makes it accessible again on the next login attempt. Every saved change is written to the audit log.

---

Figure 4.41 shows the activity diagram for the Reset User Password function (UC-USR-03). The diagram has two swimlanes, Admin and System. The admin selects a user from the list and clicks Reset Password. A confirmation dialog appears to prevent accidental resets. If the admin cancels, the flow ends without a change. If confirmed, the System generates a temporary password, hashes and saves it, sets `must_change_password` to true, clears any existing lockout on the account, and emails the new temporary credentials to the user with instructions to change the password on next login. The reset is recorded in the audit log with the admin's identity and timestamp.

---

Figure 4.42 shows the activity diagram for the Manage Roles function (UC-USR-04). The diagram has two swimlanes, Admin and System. The admin opens the Roles management page and the System lists all defined roles with their assigned permissions. The admin can take three kinds of action. Creating a new role means providing a name and selecting permissions; the System saves the definition. Editing an existing role means updating the name or changing which permissions are attached; the System saves the update and the new permissions apply to all users holding that role straight away. Deleting a role first checks whether any users are assigned to it. If so, the deletion is blocked with a warning. If no users hold the role, the System removes it.

---

Figure 4.43 shows the activity diagram for the Manage Permissions function (UC-USR-05). The diagram has two swimlanes, Admin and System. The admin opens the Permissions page and the System lists all defined permission keys by category. The admin can create a new key by entering a name; the System saves it and makes it available for role assignment. The admin can also edit an existing key's name, which the System updates immediately. For deletion, the System first checks whether the key is attached to any role. If it is, deletion is blocked with a warning, since removing a live permission would break the access control for every role using it. If the key is not attached to any role, the System deletes it.

---

## Module 08 – System Settings

Figure 4.44 shows the activity diagram for the Configure System Settings function (UC-SET-01). The diagram has two swimlanes, Admin and System. The admin opens the System Settings page and the System loads all current configuration values, grouped by category. The categories cover SMTP credentials, security parameters (maximum failed login attempts, lockout duration, session timeout), refund policy thresholds, extension charge rates per type, and the extension payment window duration. The admin changes the desired values and submits. The System validates all inputs — numeric fields must contain valid positive integers and SMTP credentials must match the expected format. Failing inputs are highlighted and the admin corrects them. When everything is valid, the System saves the values to the `settings` key-value table. The new settings take effect immediately for all subsequent operations; no server restart is needed.

---

Figure 4.45 shows the activity diagram for the Manage Default Homestay Policies function (UC-SET-02). The diagram has two swimlanes, Admin and System. The admin opens the Default Policies section within System Settings. The System loads the current list of default policies. The admin can add a new policy by entering the text and saving, edit an existing policy's text, or delete a policy from the list. These changes only affect units created after the change is saved. Units that already exist keep their own copy of the policies as they were at the time of creation and are not updated retroactively.

---

## Module 09 – Audit Logs

Figure 4.46 shows the activity diagram for the View and Filter Audit Trail function (UC-AUDIT-01). The diagram has two swimlanes, Admin and System. The admin opens the Audit Logs page and the System retrieves the most recent entries, displayed newest first. Each entry shows the timestamp, the actor who performed the action, the event type, and a description of what changed. The admin can filter by date range, event type such as authentication events, booking changes, or configuration changes, or by a specific user actor. The System re-queries and refreshes the results with each filter change. The audit log is strictly read-only; no entry can be modified or deleted by anyone. The admin can also export the filtered view as a downloadable file.

---

Figure 4.47 shows the activity diagram for the Automatic Event Logging function (UC-AUDIT-02). The diagram has two swimlanes, System Action and Audit Logger, representing the component that performs an action and the one that records it. No human actor triggers this flow; it fires automatically whenever an auditable event occurs. Auditable events include user logins and logouts, failed login attempts and account lockouts, account creation and deactivation, password resets, booking creation and modification and cancellation, payment processing, admin configuration changes, role and permission changes, QR code generation and invalidation, and scheduled job executions. When an event completes, the System creates an immutable log entry through the `spatie/laravel-activitylog` package. Each entry captures the event type, actor identity, affected model, timestamp, and the before-and-after state of any changed fields. Entries go into the `activity_log` table and no part of the application can alter or remove them.

---

## Module 10 – QR Code & Access

Figure 4.48 shows the activity diagram for the Receive and Use QR Code function (UC-QR-01). The diagram has two swimlanes, Guest and System. The flow starts as soon as a booking payment is confirmed. The System generates a unique, cryptographically secure QR code token and stores it in the `qr_codes` table with a validity window that matches the booking's check-in and check-out timestamps. The System then delivers the code to the guest through two channels at once: an in-app notification and an email. During the stay, the guest presents the code at the door access point. The System validates the token — checking that it is genuine, not revoked, and that the current time falls within the valid window. The door opens on a pass; it stays shut on a fail. At check-out time, the System invalidates the code automatically with no action needed from the guest or admin.

---

Figure 4.49 shows the activity diagram for the Manage Housekeeping Cycle function (UC-QR-02). The diagram has two swimlanes, Admin and System. The flow picks up after the previous guest's QR code has been invalidated at checkout. The admin opens the QR Code management page for the unit and generates a temporary housekeeping code. The System creates a short-lived token with a validity window sized to the expected housekeeping duration. Housekeeping staff use this code to enter the unit and finish their work. Once housekeeping is done, the admin marks the task as complete. The System then checks whether a confirmed booking exists for the unit with a future check-in date. If one exists, the System generates a new guest QR code for that booking and delivers it to the next guest through the standard channels. If no future booking exists, the unit sits in an available state with no active code until the next booking is confirmed.

---

Figure 4.50 shows the activity diagram for the Initiate Booking Extension function (UC-QR-03). The diagram has two swimlanes, Admin and System. The admin opens a confirmed booking and clicks Initiate Extension. The admin then selects a new check-out date or time. The System checks whether the requested extension period is available by looking for any confirmed booking or blocked date that falls between the current check-out and the requested new check-out. If a conflict exists, the System shows the conflicting booking details and the flow ends without creating any extension record. If the period is free, the System calculates the extra charge based on the configured extension rate for that extension type, generates an extension bill with a unique bill number, sets a payment deadline using the unit's configured payment window, and notifies the guest with the extension details, the bill, and the deadline.

---

Figure 4.51 shows the activity diagram for the Pay Extension Charge function (UC-QR-04). The diagram has three swimlanes, Guest, Payment Gateway, and System. The flow starts when the guest receives the extension notification, opens the extension bill, and clicks Pay. The System redirects them to the payment gateway using the same mechanism as the standard payment flow in UC-PAY-01. The gateway processes the transaction and sends a webhook callback to the System. The System verifies the signature and checks the result. If payment succeeds, the System sets the extension status to `confirmed`, updates the booking's check-out date and time to the extended values, extends the validity window of the guest's existing QR code to match the new check-out, and notifies the guest. If payment fails, the System notifies the guest and they can retry within the remaining payment window.

---

Figure 4.52 shows the activity diagram for the Auto-Cancel Extension function (UC-QR-05). The diagram has two swimlanes, Scheduler and System. Like the booking auto-cancel flow, this runs entirely as a background job with no human actor. The Scheduler fires the job at regular intervals. The System queries for all extension records with status `pending_payment` where the `payment_deadline` has passed. If none are found, the job ends. For each expired record found, the System sets the extension status to `cancelled`, reverts the booking's check-out date and time to the original values stored in the `original_check_out_date` field of the extension record, and notifies the guest that the extension has been cancelled due to non-payment.

---

## Module 11 – Reporting & Analytics

Figure 4.53 shows the activity diagram for the View Analytics Dashboard function (UC-RPT-01). The diagram has two swimlanes, Admin and System. The admin opens the Dashboard page and the System retrieves aggregated data and renders the summary components: total bookings for the selected period, total revenue, occupancy rate per unit, cancellation rate, a booking trend chart over time, a per-unit breakdown of bookings and revenue, and a guest feedback rating summary. The admin can change the date range filter between the current week, current month, or a custom range, and the System re-queries and refreshes all components accordingly. This page is the entry point for all reporting functions.

---

Figure 4.54 shows the activity diagram for the View Revenue Report function (UC-RPT-02). The diagram has two swimlanes, Admin and System. The admin opens the Revenue Report page and the System loads all payment records within the default date range, showing a detailed table with each payment, the linked booking reference, unit name, guest name, payment date, payment method, and amount. The admin can filter by date range, unit, or payment status (succeeded, failed, or refunded). The System re-queries with the filter parameters and updates the table. A summary row at the top shows the total revenue and total transaction count for the current filter.

---

Figure 4.55 shows the activity diagram for the Export Report function (UC-RPT-03). The diagram has two swimlanes, Admin and System. From any report page — the dashboard, the revenue report, or the bookings breakdown — the admin clicks Export and selects an output format, either PDF or CSV. The System uses the current filtered dataset and any active filter parameters to generate the file. PDF exports are rendered with HomeLodge branding. CSV exports are plain text with column headers matching the report view. Once the file is ready, the System triggers a browser download. No underlying data is changed by the export.

---

## Module 12 – Guest Feedback

Figure 4.56 shows the activity diagram for the Submit Rating and Feedback function (UC-FB-01). The diagram has two swimlanes, Guest and System. The review option only appears after a booking's status reaches `completed`. The guest opens the completed booking and clicks Leave a Review. The System immediately checks whether a feedback record already exists for that booking. If one does, the System shows a message that feedback has already been submitted, and the flow ends. If none exists, the System shows the feedback form. The guest selects a star rating from one to five and optionally types a comment, then submits. The rating is required; the comment is not. If no rating is selected, the System flags the field and returns the guest to the form. Once the submission passes, the System saves the feedback record linked to both the booking and the homestay unit, recalculates the unit's average rating, and shows the guest a confirmation.

---

Figure 4.57 shows the activity diagram for the View Submitted Feedback function (UC-FB-02). The diagram has two swimlanes, Guest and System. The guest opens the Feedback section of their account and the System retrieves all feedback records they have submitted, newest first. Each entry shows the star rating, written comment, unit name, submission date, and any reply from the admin. If no feedback has been submitted yet, the System shows an empty state message. The guest can read their submissions but cannot edit or delete them.

---

Figure 4.58 shows the activity diagram for the View and Manage All Feedback function, which is the admin-facing flow (UC-FB-03). The diagram has two swimlanes, Admin and System. The admin opens the Feedback management page and the System retrieves all feedback records across all units, showing the guest name, unit name, rating, comment, submission date, and visibility status per entry. The admin can filter by unit, date range, or visibility. From any entry, the admin can post or edit a reply, which the System saves and attaches to the record so it is visible to the guest. The admin can also toggle an entry's visibility. Hiding a record removes it from the public listing on the unit page but keeps it in the database and in the admin view. The visibility toggle is used to moderate entries that breach the platform's content policies.

---

Figure 4.59 shows the activity diagram for the Display Average Rating function (UC-FB-04). The diagram has two swimlanes, System Trigger and System. No human actor initiates this flow. It fires automatically whenever a new feedback record is submitted or whenever the admin changes the visibility of an existing one. On trigger, the System queries the `feedbacks` table for all records where `is_visible` is true for the relevant unit, calculates the arithmetic mean of the visible star ratings, rounds to one decimal place, and saves the updated average. The listing card, unit detail page, and analytics dashboard all read this stored value directly rather than recalculating it on each page load.
