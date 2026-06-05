# Chapter 7: Requirements Matrix — HomeLodge Booking Homestay System

This chapter provides a cross-reference that traces components and data structures back to the requirements in Chapter 2. Each row identifies a Package ID, Use Case ID, and Sequence Diagram ID combination, and an `X` marks each entity class involved in that scenario.

---

**Table 7.1: Requirements Matrix (Use Case / Sequence Diagram vs. Classes)**

|   | User | HomestayUnit | HomestayImage | HousePolicy | Booking | Bill | Payment | QrCode | BookingExtension | ChatConversation | ChatMessage | Feedback | Setting | Activity |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| **P001 — Authentication** | | | | | | | | | | | | | | |
| P001, UC001, SD001 (Register) | X | | | | | | | | | | | | | X |
| P001, UC002, SD002 (Login) | X | | | | | | | | | | | | X | X |
| P001, UC003, SD003 (Google SSO) | X | | | | | | | | | | | | | X |
| P001, UC004, SD004 (Forgot Password) | X | | | | | | | | | | | | | X |
| **P002 — Homestay Management** | | | | | | | | | | | | | | |
| P002, UC005, SD005 (Create Unit) | | X | X | X | | | | | | | | | X | X |
| P002, UC006, SD006 (Browse Listings) | | X | X | | | | | | | | | X | | |
| **P003 — Booking** | | | | | | | | | | | | | | |
| P003, UC007, SD007 (Create Booking) | X | X | | | X | X | | | | | | | | X |
| P003, UC008, SD008 (Cancel Booking) | X | | | | X | X | X | | | | | | X | X |
| P003, UC009, SD009 (Auto-Cancel) | | | | | X | X | | | | | | | X | X |
| **P004 — Payment** | | | | | | | | | | | | | | |
| P004, UC010, SD010 (Make Payment) | X | | | | X | X | X | | | | | | | X |
| P004, UC011, SD011 (Webhook) | | | | | X | X | X | X | | | | | | X |
| **P005 — Notification** | | | | | | | | | | | | | | |
| P005, UC012, SD012 (Send Notification) | X | | | | X | | | | | | | | X | |
| **P006 — Chat** | | | | | | | | | | | | | | |
| P006, UC013, SD013 (Send Message) | X | | | | | | | | | X | X | | | |
| **P007 — User Management** | | | | | | | | | | | | | | |
| P007, UC014, SD014 (Create User) | X | | | | | | | | | | | | | X |
| P007, UC015, SD015 (Reset Password) | X | | | | | | | | | | | | | X |
| **P008 — Role & Permission** | | | | | | | | | | | | | | |
| P008, UC016, SD016 (Create Role) | X | | | | | | | | | | | | | X |
| P008, UC017, SD017 (Assign Permission) | X | | | | | | | | | | | | | X |
| **P009 — System Settings** | | | | | | | | | | | | | | |
| P009, UC018, SD018 (Update SMTP) | | | | | | | | | | | | | X | X |
| P009, UC019, SD019 (Update Refund) | | | | | | | | | | | | | X | X |
| **P010 — Audit Log** | | | | | | | | | | | | | | |
| P010, UC020, SD020 (Record Entry) | | | | | | | | | | | | | | X |
| P010, UC021, SD021 (View Audit Log) | | | | | | | | | | | | | | X |
| **P011 — QR Code Door Access** | | | | | | | | | | | | | | |
| P011, UC022, SD022 (Generate QR) | | | | | X | | | X | | | | | | X |
| P011, UC023, SD023 (Request Extension) | X | X | | | X | X | | X | X | | | | X | X |
| P011, UC024, SD024 (Auto-Cancel Ext.) | | | | | X | X | | X | X | | | | | X |
| **P012 — Reporting & Analytics** | | | | | | | | | | | | | | |
| P012, UC025, SD025 (View Dashboard) | | X | | | X | X | X | | | | | X | | |
| P012, UC026, SD026 (Export Report) | | X | | | X | X | X | | | | | X | | |
| **P013 — Guest Feedback** | | | | | | | | | | | | | | |
| P013, UC027, SD027 (Submit Feedback) | X | X | | | X | | | | | | | X | | X |
| P013, UC028, SD028 (Reply to Feedback) | X | | | | | | | | | | | X | | X |

> **Note:** The Role and Permission entities are managed by Spatie Laravel Permission and are not listed as separate columns. They are used implicitly through middleware guards in the Auth, User Management, and Role & Permission subsystems.

---

*— End of Chapter 7: Requirements Matrix —*
