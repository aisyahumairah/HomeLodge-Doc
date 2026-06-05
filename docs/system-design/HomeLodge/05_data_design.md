# Chapter 5: Data Design (SDD) — HomeLodge Booking Homestay System

This chapter describes how the system's information is stored and organised. All data is persisted in a single MySQL/MariaDB relational database.

---

## 5.1 Data Description

The major data or system entities are stored in a relational database named **`homelodge`**, processed and organised into 17 entities as listed in Table 5.1. The database schema is managed through Laravel migrations, and all data access goes through Eloquent ORM models.

**Table 5.1: Description of Entities in the Database**

| No. | Entity Name | Description |
| :--- | :--- | :--- |
| 1 | `activity_log` | Records every user action, admin change, and system event as a read-only audit trail (Spatie Activity Log). |
| 2 | `bills` | Payment request documents generated when a booking is created or an extension is approved. |
| 3 | `booking_extensions` | Tracks requests to extend a booking's check-out date/time, including the extension charge and payment status. |
| 4 | `bookings` | Reservations linking a user to a homestay unit for a specific date range, with status tracking. |
| 5 | `chat_conversations` | One conversation thread per guest with the admin. |
| 6 | `chat_messages` | Individual messages within a conversation, with timestamps and sender identification. |
| 7 | `default_house_policies` | System-level default policies that are copied to new homestay units upon creation. |
| 8 | `feedbacks` | Guest star ratings and written reviews for completed bookings. |
| 9 | `homestay_images` | Images associated with a homestay unit (multiple per unit). |
| 10 | `homestay_units` | Managed properties with profiles, pricing, check-in/check-out times, and status. |
| 11 | `house_policies` | Per-unit rules that guests must follow (e.g., no smoking, no pets). |
| 12 | `notifications` | In-app notifications stored using Laravel's database notification driver. |
| 13 | `payments` | Records of completed payment transactions from the payment gateway. |
| 14 | `qr_codes` | QR code tokens generated per booking for door access, with validity windows and status. |
| 15 | `settings` | Key-value store for all configurable operational parameters (SMTP, security, payment, etc.). |
| 16 | `users` | Registered system users (guests and admins) with authentication and profile data. |
| 17 | `roles` / `permissions` / pivot tables | RBAC tables managed by Spatie Laravel Permission (roles, permissions, model_has_roles, model_has_permissions, role_has_permissions). |

---

## 5.2 Data Dictionary

### 5.2.1 Entity: `activity_log`

| Attribute Name | Type | Description |
| :--- | :--- | :--- |
| id | BIGINT (PK) | Auto-incremented primary key. |
| log_name | VARCHAR(255) | Category of the log entry (e.g., "default"). |
| description | TEXT | Human-readable description of the action performed. |
| subject_type | VARCHAR(255) | The Eloquent model class of the affected record. |
| subject_id | BIGINT | The ID of the affected record. |
| causer_type | VARCHAR(255) | The Eloquent model class of the user who performed the action. |
| causer_id | BIGINT (FK → users.id) | The ID of the user who performed the action. NULL for system-triggered events. |
| properties | JSON | Before/after state of the affected record in JSON format. |
| event | VARCHAR(255) | The type of event (created, updated, deleted, login, logout, etc.). |
| created_at | TIMESTAMP | When the log entry was created. |
| updated_at | TIMESTAMP | When the log entry was last updated. |

---

### 5.2.2 Entity: `bills`

| Attribute Name | Type | Description |
| :--- | :--- | :--- |
| id | BIGINT (PK) | Auto-incremented primary key. |
| bill_number | VARCHAR(255) (UNIQUE) | Unique bill identifier (format: `BILL-YYYYMMDD-NNN`). |
| booking_id | BIGINT (FK → bookings.id) | The booking this bill belongs to. |
| amount | DECIMAL(10,2) | Total amount due. |
| status | ENUM('unpaid', 'paid', 'refunded') | Current bill status. |
| type | ENUM('booking', 'extension') | Whether this bill is for a new booking or an extension. |
| created_at | TIMESTAMP | When the bill was generated. |
| updated_at | TIMESTAMP | When the bill was last updated. |

---

### 5.2.3 Entity: `booking_extensions`

| Attribute Name | Type | Description |
| :--- | :--- | :--- |
| id | BIGINT (PK) | Auto-incremented primary key. |
| booking_id | BIGINT (FK → bookings.id) | The booking being extended. |
| original_check_out | DATETIME | Original check-out date and time before extension. |
| new_check_out | DATETIME | Requested new check-out date and time. |
| extension_type | ENUM('time', 'date') | Whether the extension is by hours or by nights. |
| charge_amount | DECIMAL(10,2) | Calculated extra fee for the extension. |
| status | ENUM('pending_payment', 'paid', 'cancelled', 'expired') | Current extension status. |
| payment_deadline | DATETIME | Deadline by which the guest must pay. |
| bill_id | BIGINT (FK → bills.id) | The bill generated for this extension. |
| created_at | TIMESTAMP | When the extension was requested. |
| updated_at | TIMESTAMP | When the record was last updated. |

---

### 5.2.4 Entity: `bookings`

| Attribute Name | Type | Description |
| :--- | :--- | :--- |
| id | BIGINT (PK) | Auto-incremented primary key. |
| booking_number | VARCHAR(255) (UNIQUE) | Unique booking identifier (format: `BK-YYYYMMDD-NNN`). |
| user_id | BIGINT (FK → users.id) | The guest who made the booking. |
| homestay_unit_id | BIGINT (FK → homestay_units.id) | The unit being booked. |
| check_in_date | DATE | Check-in date. |
| check_in_time | TIME | Check-in time (default: 15:00). |
| check_out_date | DATE | Check-out date. |
| check_out_time | TIME | Check-out time (default: 12:00). |
| status | ENUM('pending_payment', 'confirmed', 'cancelled', 'completed', 'blocked', 'extended') | Current booking status. |
| total_amount | DECIMAL(10,2) | Total price for the stay. |
| payment_deadline | DATETIME | Deadline for the guest to pay before auto-cancellation. |
| cancelled_at | DATETIME | When the booking was cancelled (if applicable). |
| refund_amount | DECIMAL(10,2) | Refund amount if cancelled (NULL if not cancelled). |
| created_by | BIGINT (FK → users.id) | Who created the booking (guest themselves, or admin on behalf). |
| created_at | TIMESTAMP | When the booking was created. |
| updated_at | TIMESTAMP | When the record was last updated. |

---

### 5.2.5 Entity: `chat_conversations`

| Attribute Name | Type | Description |
| :--- | :--- | :--- |
| id | BIGINT (PK) | Auto-incremented primary key. |
| user_id | BIGINT (FK → users.id) | The guest in this conversation. |
| last_message_at | DATETIME | Timestamp of the most recent message. |
| created_at | TIMESTAMP | When the conversation was started. |
| updated_at | TIMESTAMP | When the record was last updated. |

---

### 5.2.6 Entity: `chat_messages`

| Attribute Name | Type | Description |
| :--- | :--- | :--- |
| id | BIGINT (PK) | Auto-incremented primary key. |
| conversation_id | BIGINT (FK → chat_conversations.id) | The conversation this message belongs to. |
| sender_id | BIGINT (FK → users.id) | The user who sent the message. |
| message | TEXT | The message content. |
| is_read | BOOLEAN | Whether the recipient has read this message. |
| created_at | TIMESTAMP | When the message was sent. |
| updated_at | TIMESTAMP | When the record was last updated. |

---

### 5.2.7 Entity: `default_house_policies`

| Attribute Name | Type | Description |
| :--- | :--- | :--- |
| id | BIGINT (PK) | Auto-incremented primary key. |
| policy_name | VARCHAR(255) | Name of the default policy (e.g., "No Pets Allowed"). |
| description | TEXT | Detailed description of the policy. |
| is_active | BOOLEAN | Whether this policy is currently copied to new units. |
| created_at | TIMESTAMP | When the policy was created. |
| updated_at | TIMESTAMP | When the record was last updated. |

---

### 5.2.8 Entity: `feedbacks`

| Attribute Name | Type | Description |
| :--- | :--- | :--- |
| id | BIGINT (PK) | Auto-incremented primary key. |
| booking_id | BIGINT (FK → bookings.id, UNIQUE) | The completed booking this feedback is for (one per booking). |
| user_id | BIGINT (FK → users.id) | The guest who submitted the feedback. |
| homestay_unit_id | BIGINT (FK → homestay_units.id) | The unit being reviewed. |
| rating | TINYINT | Star rating from 1 to 5. |
| comment | TEXT | Written review (optional). |
| admin_reply | TEXT | Admin's response to the review (optional). |
| is_visible | BOOLEAN | Whether the feedback is visible on the public listing (default: true). |
| created_at | TIMESTAMP | When the feedback was submitted. |
| updated_at | TIMESTAMP | When the record was last updated. |

---

### 5.2.9 Entity: `homestay_images`

| Attribute Name | Type | Description |
| :--- | :--- | :--- |
| id | BIGINT (PK) | Auto-incremented primary key. |
| homestay_unit_id | BIGINT (FK → homestay_units.id) | The unit this image belongs to. |
| image_path | VARCHAR(255) | File path to the stored image. |
| sort_order | INT | Display order of the image in the gallery. |
| created_at | TIMESTAMP | When the image was uploaded. |
| updated_at | TIMESTAMP | When the record was last updated. |

---

### 5.2.10 Entity: `homestay_units`

| Attribute Name | Type | Description |
| :--- | :--- | :--- |
| id | BIGINT (PK) | Auto-incremented primary key. |
| name | VARCHAR(255) | Name of the homestay unit. |
| description | TEXT | Full description of the unit. |
| location | VARCHAR(255) | Physical location or address. |
| base_price | DECIMAL(10,2) | Base price per night. |
| deposit | DECIMAL(10,2) | Deposit amount required. |
| check_in_time | TIME | Default check-in time (e.g., 15:00). |
| check_out_time | TIME | Default check-out time (e.g., 12:00). |
| extension_payment_window | INT | Per-unit extension payment window in minutes (overrides system default). NULL = use system default. |
| status | ENUM('active', 'inactive') | Whether the unit is listed publicly. |
| average_rating | DECIMAL(3,2) | Cached average star rating from visible feedback. |
| created_at | TIMESTAMP | When the unit was created. |
| updated_at | TIMESTAMP | When the record was last updated. |

---

### 5.2.11 Entity: `house_policies`

| Attribute Name | Type | Description |
| :--- | :--- | :--- |
| id | BIGINT (PK) | Auto-incremented primary key. |
| homestay_unit_id | BIGINT (FK → homestay_units.id) | The unit this policy belongs to. |
| policy_name | VARCHAR(255) | Name of the house policy. |
| description | TEXT | Detailed description. |
| created_at | TIMESTAMP | When the policy was added. |
| updated_at | TIMESTAMP | When the record was last updated. |

---

### 5.2.12 Entity: `payments`

| Attribute Name | Type | Description |
| :--- | :--- | :--- |
| id | BIGINT (PK) | Auto-incremented primary key. |
| payment_number | VARCHAR(255) (UNIQUE) | Unique payment identifier (format: `PAY-YYYYMMDD-NNN`). |
| bill_id | BIGINT (FK → bills.id) | The bill this payment satisfies. |
| booking_id | BIGINT (FK → bookings.id) | The associated booking. |
| amount | DECIMAL(10,2) | Amount paid. |
| gateway_reference | VARCHAR(255) (UNIQUE) | Unique reference from the payment gateway (for idempotency). |
| status | ENUM('success', 'failed', 'refunded') | Payment status. |
| paid_at | DATETIME | When the payment was completed. |
| created_at | TIMESTAMP | When the payment record was created. |
| updated_at | TIMESTAMP | When the record was last updated. |

---

### 5.2.13 Entity: `qr_codes`

| Attribute Name | Type | Description |
| :--- | :--- | :--- |
| id | BIGINT (PK) | Auto-incremented primary key. |
| booking_id | BIGINT (FK → bookings.id) | The booking this QR code belongs to. |
| token | VARCHAR(255) (UNIQUE) | Cryptographically secure random token encoded in the QR code. |
| valid_from | DATETIME | Start of the QR code's validity window (check-in date/time). |
| valid_until | DATETIME | End of the QR code's validity window (check-out date/time). |
| purpose | ENUM('guest', 'housekeeping') | Whether the code is for a guest or for housekeeping access. |
| status | ENUM('active', 'expired', 'regenerated') | Current QR code status. |
| created_at | TIMESTAMP | When the QR code was generated. |
| updated_at | TIMESTAMP | When the record was last updated. |

---

### 5.2.14 Entity: `settings`

| Attribute Name | Type | Description |
| :--- | :--- | :--- |
| id | BIGINT (PK) | Auto-incremented primary key. |
| group | VARCHAR(255) | Settings group (e.g., "smtp", "security", "payment", "extension", "general"). |
| key | VARCHAR(255) (UNIQUE) | Setting key (e.g., "smtp_host", "lockout_duration", "refund_2_weeks"). |
| value | TEXT | Setting value (stored as string, cast to appropriate type at runtime). |
| created_at | TIMESTAMP | When the setting was created. |
| updated_at | TIMESTAMP | When the setting was last changed. |

---

### 5.2.15 Entity: `users`

| Attribute Name | Type | Description |
| :--- | :--- | :--- |
| id | BIGINT (PK) | Auto-incremented primary key. |
| name | VARCHAR(255) | Full name of the user. |
| email | VARCHAR(255) (UNIQUE) | Email address (used for login). |
| email_verified_at | TIMESTAMP | When the email was verified (NULL if unverified). |
| password | VARCHAR(255) | Bcrypt-hashed password. NULL for Google SSO-only users. |
| phone | VARCHAR(255) | Phone number (optional). |
| avatar | VARCHAR(255) | Path to the user's profile image. |
| google_id | VARCHAR(255) | Google account ID for SSO users. |
| status | ENUM('active', 'inactive', 'locked') | Account status. |
| failed_login_count | INT | Number of consecutive failed login attempts (reset on success). |
| locked_until | DATETIME | When the lockout expires (NULL if not locked). |
| must_change_password | BOOLEAN | Whether the user must change their password at next login. |
| remember_token | VARCHAR(100) | Token for "remember me" functionality. |
| created_at | TIMESTAMP | When the account was created. |
| updated_at | TIMESTAMP | When the record was last updated. |
| deleted_at | TIMESTAMP | Soft-delete timestamp (NULL if not deleted). |

---

*— End of Chapter 5: Data Design —*
