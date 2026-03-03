# UI/UX Design Guidelines
## HomeLodge – Booking Homestay System

| Field | Detail |
|---|---|
| **Document Version** | 1.0 |
| **Status** | Draft |
| **Last Updated** | 2026-03-03 |

---

## Table of Contents

1. [Design Principles](#1-design-principles)
2. [Design System](#2-design-system)
3. [Layout & Navigation](#3-layout--navigation)
4. [Screen Flows](#4-screen-flows)
5. [Component Guidelines](#5-component-guidelines)
6. [Responsive Design](#6-responsive-design)
7. [Accessibility](#7-accessibility)

---

## 1. Design Principles

The HomeLodge UI should feel **clean, modern, and trustworthy**. Guests should be able to complete a booking with minimal friction, and admins should be able to find any information at a glance.

| Principle | Description |
|---|---|
| **Clarity** | Every screen has a single, obvious primary action. Labels and messages use simple, plain language. |
| **Efficiency** | Reduce the number of steps to complete common tasks (booking, payment). Use smart defaults. |
| **Feedback** | Every user action receives immediate feedback — loading states, success messages, and error states. |
| **Consistency** | Use the same patterns, colours, and components throughout the application. |
| **Trust** | Use professional typography, consistent branding, and clear security cues (e.g., payment icons). |

---

## 2. Design System

### 2.1 Colour Palette

| Token | Hex | Usage |
|---|---|---|
| `--color-primary` | `#2563EB` | Primary buttons, links, active states |
| `--color-primary-dark` | `#1D4ED8` | Primary button hover |
| `--color-secondary` | `#0F766E` | Secondary actions, badges |
| `--color-success` | `#16A34A` | Success alerts, confirmed status |
| `--color-warning` | `#D97706` | Payment pending, warnings |
| `--color-danger` | `#DC2626` | Errors, cancel actions, blocked dates |
| `--color-neutral-50` | `#F9FAFB` | Page background |
| `--color-neutral-100` | `#F3F4F6` | Card/panel background |
| `--color-neutral-700` | `#374151` | Body text |
| `--color-neutral-900` | `#111827` | Headings |
| `--color-white` | `#FFFFFF` | Surface / overlays |

### 2.2 Typography

| Style | Font | Size | Weight |
|---|---|---|---|
| Heading 1 | Inter | 2rem (32px) | 700 Bold |
| Heading 2 | Inter | 1.5rem (24px) | 600 Semibold |
| Heading 3 | Inter | 1.25rem (20px) | 600 Semibold |
| Body | Inter | 1rem (16px) | 400 Regular |
| Small / Caption | Inter | 0.875rem (14px) | 400 Regular |
| Button Label | Inter | 0.95rem (15px) | 500 Medium |

> Use **Inter** from Google Fonts as the primary typeface.

### 2.3 Spacing

Use an **8px base grid**. Common spacing values:

- `4px` (0.25rem) – tight padding (badge, chip)
- `8px` (0.5rem) – small padding (button inner padding)
- `16px` (1rem) – default padding (card inner, form field gap)
- `24px` (1.5rem) – section gap
- `32px` (2rem) – large section gap
- `48px` (3rem) – page section breaks

### 2.4 Border Radius

| Context | Value |
|---|---|
| Cards, panels | `12px` |
| Buttons, inputs | `8px` |
| Badges, chips | `999px` (pill shape) |
| Avatars | `50%` |

### 2.5 Status Badges / Colours

| Status | Colour | Badge Style |
|---|---|---|
| Confirmed | `--color-success` (green) | Filled |
| Pending Payment | `--color-warning` (amber) | Filled |
| Cancelled | `--color-danger` (red) | Muted |
| Blocked | `--color-neutral-700` (grey) | Muted |
| Completed | `--color-primary` (blue) | Muted |

---

## 3. Layout & Navigation

### 3.1 Guest (User) Layout

```
┌─────────────────────────────────────────────────────┐
│                      Top Navbar                       │
│  [Logo]   Home  My Bookings  Notifications  [Avatar] │
└─────────────────────────────────────────────────────┘
│                                                       │
│                   Page Content Area                   │
│                                                       │
└─────────────────────────────────────────────────────┘
│                       Footer                          │
└─────────────────────────────────────────────────────┘
```

- Top navigation bar with logo, main links, notification bell, and user avatar/dropdown.
- Full-width content area with max-width container (`1200px`).
- Sticky top navbar on scroll.

### 3.2 Admin Layout

```
┌──────────┬──────────────────────────────────────────┐
│          │              Top Header Bar               │
│          │  [Page Title]           [Notifications]  │
│ Side-    ├──────────────────────────────────────────┤
│ bar      │                                           │
│          │              Page Content Area             │
│ (Fixed)  │                                           │
│          │                                           │
└──────────┴──────────────────────────────────────────┘
```

- Fixed left sidebar with grouped navigation items.
- Top header bar showing current page title and admin quick actions.
- Collapsible sidebar on tablet screens.

### 3.3 Admin Sidebar Navigation Groups

| Group | Items |
|---|---|
| **Dashboard** | Dashboard Overview |
| **Bookings** | Booking Calendar, All Bookings |
| **Payments** | Bills, Payments |
| **Users** | Manage Users |
| **Access Control** | Roles, Permissions |
| **Communication** | Chat |
| **System** | Settings, Audit Logs |

---

## 4. Screen Flows

### 4.1 Guest: Booking Flow

```
[Home / Landing Page]
        │
        ▼
[Select Dates on Calendar]
        │
        ├── Date Unavailable ──► [Error Message] ──► [Re-select Date]
        │
        ├── Date Available ──► [Booking Form]
        │                             │
        │                    [Review Booking Details]
        │                             │
        │                    [Submit Booking]
        │                             │
        │                    [Payment Page]
        │                             │
        │              ┌──────────────┴──────────────┐
        │              │                             │
        │      [Payment Success]             [Payment Failed]
        │              │                             │
        │      [Receipt + Confirmation]       [Retry or Cancel]
        │              │
        ▼
[My Bookings – Confirmed]
```

### 4.2 Guest: Cancellation Flow

```
[My Bookings]
      │
      ▼
[Booking Detail]
      │
      ▼
[Cancel Button]
      │
      ▼
[Cancellation Policy Disclosed]
  (Refund amount shown clearly)
      │
      ├── [Confirm Cancel] ──► [Cancellation Confirmed + Refund Timeline]
      │
      └── [Back / Dismiss]
```

### 4.3 Admin: Booking Management Flow

```
[Booking Calendar]
      │
      ├── Click on Existing Booking ──► [Booking Detail] ──► [Edit / Cancel / Delete]
      │
      └── Click on Available Date ──► [Create Booking Form]
                                              │
                                    [Select User or Own Booking]
                                              │
                                    [Submit] ──► [Booking Created]
```

### 4.4 Admin: Housekeeping & QR Code Flow

```
[Booking Calendar / Booking List]
      │
      ▼
[Booking nearing checkout]
      │
      ▼
[Admin: Mark housekeeping in progress]
(Regenerate QR → Interim QR for housekeeping)
      │
      ▼
[Admin: Mark housekeeping done]
      │
      ▼
[System: Auto-generate new QR for next booking]
```

---

## 5. Component Guidelines

### 5.1 Buttons

| Type | Use Case | Style |
|---|---|---|
| Primary | Main CTA (Submit, Confirm, Book Now) | `--color-primary` background, white text |
| Secondary | Alternative actions (View Details, Edit) | Outlined border, `--color-primary` text |
| Danger | Destructive actions (Delete, Cancel Booking) | `--color-danger` background, white text |
| Ghost | Low-emphasis (Back, Dismiss) | No background, `--color-neutral-700` text |

- All buttons have a hover state with slight darkening or shadow.
- Disabled buttons use `opacity: 0.5` and `cursor: not-allowed`.
- Loading state shows a spinner icon inside the button.

### 5.2 Forms

- Labels are above the input field, never inside.
- All inputs have a visible border and a clear `:focus` ring (e.g., `2px solid --color-primary`).
- Validation errors appear directly below the relevant input in `--color-danger`.
- Required fields are marked with an asterisk (`*`).
- Password fields include a show/hide toggle icon on the right side.

### 5.3 Tables

- Use striped rows (`nth-child even`) for readability.
- Include column header sorting indicators where applicable.
- Pagination controls appear below the table.
- Row actions (Edit, View, Delete) use icon buttons with tooltips.
- Empty state shows an icon + message, e.g., *"No bookings found."*

### 5.4 Calendar

- Use a full-month view for availability.
- **Green dot / highlight:** Available date.
- **Red highlight:** Booked or unavailable.
- **Grey highlight:** Admin-blocked date.
- **Amber highlight:** Temporarily held (pending payment).
- Clicking a date opens a detail panel or booking form.

### 5.5 Notifications & Alerts

- **Toast notifications:** Appear top-right, auto-dismiss after 5 seconds.
- **Inline alerts:** Appear within the page content (e.g., payment failure banner).
- **Notification bell:** Icon with a badge count; clicking opens a dropdown list.
- Alert types follow the colour system: success (green), warning (amber), error (red), info (blue).

### 5.6 Modals / Dialogs

- Use for confirmations (delete, cancel), not for complex forms.
- Always include a clearly labelled **Cancel** / **Close** button.
- Destructive action modals use a `--color-danger` confirm button.
- Backdrop click or `Esc` key closes non-critical modals.

### 5.7 Chat Interface

- Split-panel layout: conversation list on the left, chat window on the right.
- Messages aligned: User messages (right, primary colour bubble), Admin messages (left, grey bubble).
- Timestamps shown beneath each message.
- Typing indicator shown when the other party is typing.
- Unread message badge on the conversation list.

---

## 6. Responsive Design

| Breakpoint | Screen Width | Layout Behaviour |
|---|---|---|
| Mobile | < 640px | Single column; sidebar hidden (hamburger menu) |
| Tablet | 640px – 1024px | Sidebar collapses to icons only |
| Desktop | > 1024px | Full sidebar + content layout |

Key considerations:
- Touch targets must be at least **44x44px** on mobile.
- Tables become horizontally scrollable or card-based on mobile.
- The booking calendar adapts to a single-week or list view on small screens.
- All modals are full-screen on mobile.

---

## 7. Accessibility

| Guideline | Requirement |
|---|---|
| **WCAG Level** | Aim for WCAG 2.1 Level AA compliance |
| **Colour Contrast** | Minimum 4.5:1 ratio for body text; 3:1 for large text |
| **Keyboard Navigation** | All interactive elements reachable and operable via keyboard |
| **Focus Indicators** | Visible focus rings on all interactive elements (do not remove default outlines without replacement) |
| **ARIA Labels** | Use `aria-label` on icon-only buttons and non-obvious interactive elements |
| **Form Labels** | All form inputs must have an associated `<label>` element |
| **Error Messages** | Error messages must be programmatically associated with the relevant input (`aria-describedby`) |
| **Alt Text** | All meaningful images have descriptive `alt` attributes; decorative images use `alt=""` |
