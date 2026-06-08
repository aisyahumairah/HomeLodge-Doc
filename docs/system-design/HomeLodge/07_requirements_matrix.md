# Chapter 7: Requirements Matrix — HomeLodge Booking Homestay System

This chapter provides a cross-reference that traces components and data structures back to the requirements in Chapter 2. Each row identifies a Package ID, Use Case ID, and Sequence Diagram ID combination, and an `X` marks each entity class involved in that scenario.

---

**Table 7.1: Requirements Matrix (Use Case / Sequence Diagram vs. Classes)**

|   | User | HomestayUnit | HomestayImage | HousePolicy | Booking | Bill | Payment | QrCode | BookingExtension | ChatConversation | ChatMessage | Feedback | Setting | Activity |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| **P001 — Authentication** | | | | | | | | | | | | | | |
| P001, UC-AUTH-01, SD-AUTH-01 (Register Account) | X | | | | | | | | | | | | | X |
| P001, UC-AUTH-02, SD-AUTH-02 (Login / Google SSO) | X | | | | | | | | | | | | X | X |
| P001, UC-AUTH-03, SD-AUTH-03 (Logout) | X | | | | | | | | | | | | | X |
| P001, UC-AUTH-04, SD-AUTH-04 (Forgot Password) | X | | | | | | | | | | | | | X |
| P001, UC-AUTH-05, SD-AUTH-05 (View / Update Profile) | X | | | | | | | | | | | | | X |
| P001, UC-AUTH-06, SD-AUTH-06 (Force Change Password) | X | | | | | | | | | | | | | X |
| **P002 — Homestay Management** | | | | | | | | | | | | | | |
| P002, UC-HS-01, SD-HS-01 (Browse Homestay Units) | | X | X | | | | | | | | | X | | |
| P002, UC-HS-02, SD-HS-02 (View Unit Details & Availability) | | X | X | X | X | | | | | | | X | | |
| P002, UC-HS-03, SD-HS-03 (Create Homestay Unit) | | X | X | X | | | | | | | | | X | X |
| P002, UC-HS-04, SD-HS-04 (Edit Homestay Unit) | | X | X | X | | | | | | | | | | X |
| P002, UC-HS-05, SD-HS-05 (Deactivate / Delete Unit) | | X | | | X | | | | | | | | | X |
| P002, UC-HS-06, SD-HS-06 (View All Units List) | | X | | | | | | | | | | | | |
| **P003 — Booking** | | | | | | | | | | | | | | |
| P003, UC-BK-01, SD-BK-01 (View Availability & Select Dates) | | X | | | X | | | | | | | | | |
| P003, UC-BK-02, SD-BK-02 (Submit Booking) | X | X | | | X | X | | | | | | | | X |
| P003, UC-BK-03, SD-BK-03 (View Bookings) | X | X | | | X | | | | | | | | | |
| P003, UC-BK-04, SD-BK-04 (View Booking Details) | X | X | | | X | X | X | X | | | | | | |
| P003, UC-BK-05, SD-BK-05 (Cancel Booking) | X | | | | X | X | X | | | | | | X | X |
| P003, UC-BK-06, SD-BK-06 (Manage Booking (Admin)) | X | X | | | X | X | | | | | | | | X |
| P003, UC-BK-07, SD-BK-07 (Auto-Cancel Expired Booking) | | | | | X | X | | | | | | | X | X |
| **P004 — Payment** | | | | | | | | | | | | | | |
| P004, UC-PAY-01, SD-PAY-01 (Make Payment) | X | | | | X | X | X | X | | | | | | X |
| P004, UC-PAY-02, SD-PAY-02 (View Payment & Billing Records) | X | | | | X | X | X | | | | | | | |
| P004, UC-PAY-03, SD-PAY-03 (Regenerate Bill / Receipt) | X | | | | X | X | X | | | | | | | X |
| **P005 — Notification** | | | | | | | | | | | | | | |
| P005, UC-NOTIF-01, SD-NOTIF-01 (Receive System Notification) | X | | | | X | | | | | | | | | |
| P005, UC-NOTIF-02, SD-NOTIF-02 (Google Calendar Integration) | X | | | | X | | | | | | | | | |
| **P006 — Chat** | | | | | | | | | | | | | | |
| P006, UC-CHAT-01, SD-CHAT-01 (Send / Receive Messages) | X | | | | | | | | | X | X | | | |
| P006, UC-CHAT-02, SD-CHAT-02 (View Chat History) | X | | | | | | | | | X | X | | | |
| **P007 — User & Access Management** | | | | | | | | | | | | | | |
| P007, UC-USR-01, SD-USR-01 (Create User Account) | X | | | | | | | | | | | | | X |
| P007, UC-USR-02, SD-USR-02 (Edit / Activate / Deactivate User) | X | | | | | | | | | | | | | X |
| P007, UC-USR-03, SD-USR-03 (Reset User Password) | X | | | | | | | | | | | | | X |
| P007, UC-USR-04, SD-USR-04 (Manage Roles) | X | | | | | | | | | | | | | X |
| P007, UC-USR-05, SD-USR-05 (Manage Permissions) | X | | | | | | | | | | | | | X |
| **P008 — System Settings** | | | | | | | | | | | | | | |
| P008, UC-SET-01, SD-SET-01 (Configure System Settings) | | | | | | | | | | | | | X | X |
| P008, UC-SET-02, SD-SET-02 (Manage Default Homestay Policies) | | | | X | | | | | | | | | X | X |
| **P009 — Audit Logs** | | | | | | | | | | | | | | |
| P009, UC-AUDIT-01, SD-AUDIT-01 (View / Filter Audit Trail) | X | | | | | | | | | | | | | X |
| P009, UC-AUDIT-02, SD-AUDIT-02 (Automatic Event Logging) | | | | | | | | | | | | | | X |
| **P010 — QR Code & Access** | | | | | | | | | | | | | | |
| P010, UC-QR-01, SD-QR-01 (Receive & Use QR Code) | X | | | | X | | | X | | | | | | |
| P010, UC-QR-02, SD-QR-02 (Manage Housekeeping Cycle) | X | | | | X | | | X | | | | | | X |
| P010, UC-QR-03, SD-QR-03 (Initiate Booking Extension) | X | | | | X | X | | | X | | | | | X |
| P010, UC-QR-04, SD-QR-04 (Pay Extension Charge) | X | | | | X | X | X | | X | | | | | X |
| P010, UC-QR-05, SD-QR-05 (Auto-Cancel Extension) | | | | | X | X | | | X | | | | | X |
| **P011 — Reporting & Analytics** | | | | | | | | | | | | | | |
| P011, UC-RPT-01, SD-RPT-01 (View Analytics Dashboard) | | X | | | X | X | X | | | | | X | | |
| P011, UC-RPT-02, SD-RPT-02 (View Revenue Report) | | X | | | X | X | X | | | | | | | |
| P011, UC-RPT-03, SD-RPT-03 (Export Report) | | X | | | X | X | X | | | | | | | |
| **P012 — Guest Feedback** | | | | | | | | | | | | | | |
| P012, UC-FB-01, SD-FB-01 (Submit Rating & Feedback) | X | X | | | X | | | | | | | X | | X |
| P012, UC-FB-02, SD-FB-02 (View Submitted Feedback) | X | X | | | | | | | | | | X | | |
| P012, UC-FB-03, SD-FB-03 (View / Manage All Feedback (Admin)) | X | X | | | | | | | | | | X | | X |
| P012, UC-FB-04, SD-FB-04 (Display Average Rating) | | X | | | | | | | | | | X | | |

> **Note:** The Role and Permission entities are managed by Spatie Laravel Permission and are not listed as separate columns. They are used implicitly through middleware guards in the Auth and User Management subsystems.

---

*— End of Chapter 7: Requirements Matrix —*
