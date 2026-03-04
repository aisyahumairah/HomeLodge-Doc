# HomeLodge — Homestay Booking System

> A modern, full-featured web application for managing homestay bookings, payments, user communication, and smart door access control.

---

## Table of Contents

- [Project Overview](#project-overview)
- [Key Features](#key-features)
- [Project Structure](#project-structure)
- [Getting Started](#getting-started)
- [Documentation Index](#documentation-index)
- [Contributing](#contributing)
- [License](#license)

---

## Project Overview

**HomeLodge** is a Homestay Booking Management System designed to streamline the entire booking lifecycle — from reservation and payment to guest check-in via QR-based smart door locks. It provides a self-service portal for guests and a comprehensive management dashboard for administrators.

The system is built to:
- Allow users to search availability, make bookings, and process payments online.
- Enable administrators to manage bookings, users, roles, permissions, and system settings.
- Facilitate real-time communication between guests and admins via an integrated chat module.
- Automate reminders and notifications for payments and upcoming bookings.
- Provide secure, time-limited QR code access for physical door locks.

---

## Key Features

| Feature | Users | Admin |
|---|:---:|:---:|
| Authentication (Login, Register, Forgot Password) | ✅ | ✅ |
| Booking Management | ✅ | ✅ |
| Payment Processing (Gateway Integration) | ✅ | ✅ |
| Real-time Chat (WebSocket) | ✅ | ✅ |
| Email & In-System Notifications | ✅ | ✅ |
| QR Code Smart Door Lock Access | ✅ | ✅ |
| User Management | ❌ | ✅ |
| Role & Permission Management | ❌ | ✅ |
| System Settings & Security Config | ❌ | ✅ |
| Audit Logs | ❌ | ✅ |

---

## Project Structure

```
HomeLodge/
├── docs/
│   ├── dev/             # Developer documentation
│   │   ├── README.md            ← You are here
│   │   ├── PRD.md               ← Product Requirements
│   │   ├── PRD-01-Overview.md
│   │   ├── PRD-02-User_Modules.md
│   │   ├── PRD-03-Admin_Modules.md
│   │   ├── URS.md               ← User Requirements Specification
│   │   ├── TASKS.md             ← Development Task Tracker
│   │   ├── TECH_STACK.md        ← Technology Stack
│   │   ├── UI_UX.md             ← UI/UX Guidelines
│   │   └── DB_SCHEMA.md         ← Database Schema
│   └── users/           # User guides and manuals (TBD)
├── app/                 # Application source code
├── ...
└── homelodge.md         # Initial project brief
```

---

## Getting Started

> **Note:** A detailed environment setup guide will be added once the technology stack and framework are finalised. Refer to [TECH_STACK.md](./TECH_STACK.md) for current decisions.

### Prerequisites

- PHP 8.2+ (or applicable runtime based on final stack)
- Composer / npm
- MySQL 8.0+ or PostgreSQL 15+
- Redis (for WebSocket / session / queue management)
- SMTP credentials for email notifications
- Payment gateway API credentials

### Installation (Placeholder)

```bash
# 1. Clone the repository
git clone https://github.com/your-org/homelodge.git
cd homelodge

# 2. Install dependencies
composer install
npm install

# 3. Set up environment variables
cp .env.example .env
# Fill in DB, SMTP, payment gateway, and app keys

# 4. Run migrations and seeders
php artisan migrate --seed

# 5. Start the development server
php artisan serve
npm run dev
```

---

## Documentation Index

| Document | Description |
|---|---|
| [PRD.md](./PRD.md) | Product Requirements Document — full feature overview index |
| [PRD-01-Overview.md](./PRD-01-Overview.md) | Product scope, goals, and stakeholders |
| [PRD-02-User_Modules.md](./PRD-02-User_Modules.md) | Detailed requirements for all user-facing modules |
| [PRD-03-Admin_Modules.md](./PRD-03-Admin_Modules.md) | Detailed requirements for all admin modules |
| [URS.md](./URS.md) | User Requirements Specification — formal functional requirements |
| [TASKS.md](./TASKS.md) | Development tasks and progress tracker |
| [TECH_STACK.md](./TECH_STACK.md) | Technology decisions and rationale |
| [UI_UX.md](./UI_UX.md) | UI/UX design guidelines and principles |
| [DB_SCHEMA.md](./DB_SCHEMA.md) | Database schema and entity relationships |

---

## Contributing

1. Read through the [PRD](./PRD.md) and [TASKS.md](./TASKS.md) before starting any work.
2. Follow the code style and conventions described in [UI_UX.md](./UI_UX.md) and [TECH_STACK.md](./TECH_STACK.md).
3. Create a feature branch from `develop`: `git checkout -b feature/your-feature-name`
4. Submit a pull request against `develop` with a clear description of changes.
5. All PRs require at least one reviewer approval before merging.

---

## License

This project is proprietary. All rights reserved. Unauthorised copying, distribution, or modification is prohibited.

---

*Last updated: 2026-03-03*
