# **Booking Homestay System (HomeLodge)**

This system streamlines booking management, facilitates communication between admin and users, and provides comprehensive administrative controls.

---

# **Users**

## **1. Authentication Module**

* Can login into the system.
* Can logout from the system.
* **Register Account:** User can choose to create an account using Gmail or not.
* **Forgot Password:** User enters registered email to receive a reset password token link.
* **Manage Profile:** View or update own profile.
* **Change Password:**

  * Enter old password, new password, and confirmation password.
  * System will show the password criteria and the function of show/hide password.
    * Password criteria:
      * 8–12 characters
      * At least one symbol
      * One uppercase letter
      * One lowercase letter
      * One number
  * User must change password if reset by admin.

  * Cannot access other pages by editing URL until password is changed.
* During lockout duration, user cannot login unless:

  * Password is reset via forgot password, OR
  * Admin resets password.

---

## **2. Booking Module**

* Choose date and time.
* System checks availability:

  * If available → submit button enabled.
  * If unavailable → message shown:

    > “The selected date has been booked! Please choose another date”
* Submit own booking.
* View own booking:

  * Booking list
  * Booking history
  * Booking details
* Cancel own booking.

### **Cancellation Policy**

* Cancel **3 days before booking** → No refund.
* Cancel **1 week before booking** → 25% refund.
* Cancel **2 weeks before booking** → 50% refund.
* Refund processed within **3–5 days**.

### **Payment & Booking Status**

* Date blocked temporarily for **1 day** if payment not made.
* If payment made → booking confirmed.
* If no payment after 1 day → booking auto cancelled and date reopened.

---

## **3. Payment Module**

* Make payment (gateway calculates total including deposit).
* View payment bill and receipt.
* Receipt available after successful payment.
* View payment history:

  * Filter by date range
  * Filter by payment status
* Receive notification for payment success or failure (webhook).

---

## **4. Notifications**

* Receive notifications in system and via email.
* Payment reminders for pending payments.
* Reminder for upcoming bookings.
* Integrate with Google Calendar. show the booking in the calendar.

---

## **5. Chat Module**

* Communicate with admin via chat (websocket).

---

# **Admin**

## **1. Authentication Module**

* Login and logout.
* Register account (with or without Gmail).
* Forgot password via email reset link.
* Manage profile (view/update).
* Change password with same password criteria as users.

---

## **2. Booking Module**

* View calendar showing:

  * Booked dates
  * Temporary blocked dates
  * Available dates
* Choose date and time.
* System checks availability before submission.
* Create booking:

  * Own booking
  * Booking on behalf of user
* Manage bookings:

  * Create
  * View
  * Edit
  * Delete
  * Cancel
* Filter booking list:

  * Status
  * Booking ID
  * Date range

* Admin are able to blocked certain date from booking. User just can the date is blocked only, no need to show the reason of blocking.

### **Cancellation Policy**

* 3 days → No refund
* 1 week → 25% refund
* 2 weeks → 50% refund
* Refund processed within 3–5 days.

### **QR Code Lock Door Access**

* System generates QR code for each booking.
* Check-in: **3:00 PM**
* Check-out: **12:00 PM**
* QR code regenerates after checkout time.

### **Admin**

* Admin can regenerate QR code for housekeeping purpose.
* After housekeeping is done, then the system will generate the new QR code.
* Each booking will get difference QR code for lock door access.

---

## **3. Payment Module**

* Auto generate:

  * Bill number
  * Payment number
* View billing list (filter by ID, date range).
* View payment list (filter by ID, date range, status).
* Regenerate bill.
* Regenerate receipt (for payment succeeded).

---

## **4. User Module**

* Manage users (create, edit, delete).
* Reset user password:

  * Send reset link via email, OR
  * Reset to default password `Abc@123`.
* Activate or deactivate users.
* Default role assigned: **User**.
* 

---

## **5. Role Module**

* Manage roles (add, edit, delete).
* Assign permissions to roles.
* Cannot delete roles assigned to users.

---

## **6. Permission Module**

* Manage permissions (add, edit, delete).
* Cannot delete permissions attached to roles.

---

## **7. System Settings Module**

### SMTP Configuration

* Manage email configuration.

### Security Settings

* Lockout duration.
* Session timeout.
* Login attempt limits:

  * Account temporarily blocked after exceeded attempts.
  * Automatically unblocked after lockout period.
* Reset user password:

  * Send reset link via email, OR
  * Reset to default password `Abc@123`.

### Other Settings

* Enable/disable email notifications.
* General system settings.
* Payment refund configuration.
* Payment and billing configuration.

---

## **8. Audit Logs**

* View audit trail of all system activities and user behavior.

---

## **9. Notifications**

* Receive system and email notifications.
* Reminder for incoming bookings.
* Payment reminders for pending payments.
* QR code reminders for check-in and check-out.
* Integrate with Google Calendar. show the all bookings in the calendar.

---

## **10. Chat Module**

* Communicate with users via chat (websocket).

---

## **11. QR Code Access for Door (Homestay)**

* Each booking generates a unique QR code.
* Check-in: 3:00 PM
* Check-out: 12:00 PM
* QR code regenerates after checkout.
* Admin can regenerate key for housekeeping.
* System generates new key after housekeeping completed.
* Each booking has a different card access key.
