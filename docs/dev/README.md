# HomeLodge – Booking Homestay System

> A comprehensive homestay booking management platform that streamlines reservations, facilitates communication between administrators and guests, and provides full administrative control over operations.

---

## Table of Contents

1. [Project Overview](#project-overview)
2. [Key Features](#key-features)
3. [Documentation Index](#documentation-index)
4. [Getting Started](#getting-started)
5. [Project Structure](#project-structure)
6. [Contributing](#contributing)
7. [Contact](#contact)

---

## Project Overview

**HomeLodge** is a web-based homestay booking system designed to manage the complete lifecycle of a homestay reservation — from account registration and booking, through payment processing and QR-code door access, to post-stay feedback and analytics.

The system serves two primary roles:
- **Users (Guests):** Register, browse multiple homestay units with their individual policies, create bookings, make payments, receive notifications, communicate with admin via chat, and submit post-stay feedback.
- **Administrators:** Manage bookings, users, roles, permissions, payments, system settings, QR code access (including validity extension), reporting & analytics, guest feedback, and homestay house policies. All operational values are end-to-end configurable — nothing is hardcoded.

---

## Key Features

| Feature | Users | Admin |
|---|---|---|
| Authentication (Email / Google SSO) | ✅ | ✅ |
| Multi-Homestay Browsing & Booking | ✅ | ✅ |
| Homestay House Policies (per unit) | ✅ (view) | ✅ (manage) |
| Payment Processing (Gateway + Webhook) | ✅ | ✅ |
| QR Code Door Access + Validity Extension | — | ✅ |
| Real-time Chat (WebSocket) | ✅ | ✅ |
| Notifications (In-App & Email) | ✅ | ✅ |
| Google Calendar Integration | ✅ | ✅ |
| Guest Feedback & Ratings | ✅ (submit) | ✅ (manage) |
| Reporting & Analytics Dashboard | — | ✅ |
| Role & Permission Management | — | ✅ |
| User Management | — | ✅ |
| System Settings (fully configurable) | — | ✅ |
| Audit Logs | — | ✅ |

---

## Documentation Index

All documentation is stored in the `/docs` directory and is split into two audiences:

### `/docs/dev` — Developer Documentation

| Document | Description |
|---|---|
| [README.md](./README.md) | This file — project overview and developer guide |
| [PRD.md](./PRD.md) | Product Requirements Document |
| [URS.md](./URS.md) | User Requirements Specification |
| [TASKS.md](./TASKS.md) | Development tasks and progress tracking |
| [TECH_STACK.md](./TECH_STACK.md) | Technology stack and architectural decisions |
| [UI_UX.md](./UI_UX.md) | UI/UX guidelines, design system, and screen flows |
| [DB_SCHEMA.md](./DB_SCHEMA.md) | Database schema and entity-relationship documentation |

### `/docs/users` — User Documentation

> 🚧 User guides and manuals will be added here as the system matures.

---

## Getting Started

> ⚠️ Detailed setup instructions will be added once the tech stack and infrastructure are finalised. See [TECH_STACK.md](./TECH_STACK.md) for the current stack.

### Prerequisites

- PHP 8.x
- Composer
- Node.js & NPM
- MySQL / MariaDB
- A configured `.env` file (copy from `.env.example`)

### Installation (Draft)

```bash
# Clone the repository
git clone <repository-url>
cd HomeLodge

# Install PHP dependencies
composer install

# Install frontend dependencies
npm install

# Copy environment config and configure values
cp .env.example .env
php artisan key:generate

# Run database migrations and seeders
php artisan migrate --seed

# Start the development server
php artisan serve
npm run dev
```

---

## Project Structure

```
HomeLodge/
├── app/                  # Application logic (Controllers, Models, Services)
├── database/             # Migrations, seeders, factories
├── resources/            # Views, CSS, JS assets
├── routes/               # Web and API route definitions
├── docs/
│   ├── dev/              # Developer documentation (this folder)
│   └── users/            # End-user guides and manuals
└── tests/                # Feature and unit tests
```

---

## Contributing

1. Read the [PRD](./PRD.md) and [URS](./URS.md) to understand requirements.
2. Check [TASKS.md](./TASKS.md) for open tasks and pick one.
3. Follow the coding standards defined in [TECH_STACK.md](./TECH_STACK.md).
4. Ensure UI changes adhere to [UI_UX.md](./UI_UX.md).
5. Submit a pull request with a clear description of changes.

---

## Contact

For questions about this project, please contact the development team lead.
