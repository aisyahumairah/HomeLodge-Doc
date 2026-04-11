# CHAPTER 2: LITERATURE REVIEW

## 2.1 Introduction

This chapter reviews existing literature and related systems relevant to the development of HomeLodge, a web-based homestay booking management system. Six areas are covered. The first two examine how small-scale homestay operators in Malaysia currently manage their properties and what tools they use. The third compares three existing booking platforms against the requirements of HomeLodge. The fourth reviews four software development methodologies and justifies the one selected for this project. The fifth examines the technologies used to build HomeLodge, with a reasoned comparison for each choice. The sixth reviews academic and industry literature that supports those technology selections.

---

## 2.2 Case Study

### 2.2.1 Background of the Organisation

This project is based on the operational situation of independent homestay operators in Malaysia who manage between two and five residential properties. The properties are typically terrace houses, apartments, or studio units, rented to guests for short stays ranging from one night to a few weeks. The operator is usually absent during the guest's stay, and the guest occupies the entire property rather than a shared room within a larger building.

The short-term rental market in Malaysia has grown over the past decade alongside domestic tourism. Many operators started with one property and added more over time. A single unit is manageable: bookings are infrequent enough to track without a formal system, payments follow a predictable pattern, and the whole operation fits within a single person's phone. The situation changes when a second or third unit is added. Five units running simultaneously requires five separate availability records to be maintained, five sets of payments to be monitored, and on a busy weekend, five different guests potentially checking in and out on the same day. That volume of coordination does not work when the only tool available is a mobile phone and a collection of chat threads.

HomeLodge is designed for this operational context. It is not intended for hotel chains or online travel agencies. The target user is an operator with a small number of properties who manages the business personally and requires a booking system that they own and configure independently.

### 2.2.2 Manual Operations

The booking process used by these operators is not documented anywhere. It is sustained by personal habit and familiarity rather than a written procedure, which means errors are corrected informally when noticed and go undetected when not.

When a guest enquires about availability, the operator consults a personal calendar or an Excel spreadsheet and checks the dates against booking records stored in a notes application or an earlier chat thread. If the requested dates are free, the operator confirms this and instructs the guest to pay by bank transfer. The guest sends a screenshot of the transfer, the operator verifies it manually, and the booking is recorded in the calendar with the guest's name and contact number.

Access to the property requires a physical handover. The operator, or a member of their family, meets the guest at the property to hand over the key. When two properties have guests arriving at similar times, the operator cannot be present at both simultaneously. At check-out, the guest returns the key or leaves it inside the property. The operator then visits to collect it, inspect the unit, and arrange cleaning before the next guest's arrival.

Reminders for upcoming check-ins, pending payments, and check-outs are sent manually through a messaging application, one at a time. When several units are active in the same week, it is straightforward to lose track of which reminders have been sent to which guests. A payment reminder that is not sent can result in a booking that lapses without either party becoming aware until after the date has passed.

Booking extensions are handled informally. The guest makes a request over chat, the operator checks whether the unit is available, quotes an additional charge verbally, and waits for a further bank transfer before confirming. No formal record is created. If a dispute arises later regarding whether the extension was agreed or whether payment was received, the only available evidence is the chat thread.

This process is adequate for a single unit with a low booking rate. It is not adequate for three or more units operating concurrently. The number of manual steps required per booking remains constant, but the total number of bookings increases, the probability of a missed step rises, and the consequences of a missed step become harder to recover from.

---

## 2.3 Analysis of Current Systems

The tools that operators use can be grouped into two categories: messaging applications used for communicating with guests, and productivity applications used for record keeping.

WhatsApp and Telegram are the primary tools for receiving booking requests, sending payment instructions, and corresponding with guests before and during a stay. Both support image sharing, which is why guests send payment screenshots through these channels. Neither was designed for booking management. Conversations with different guests sit in separate threads with no connection to any booking record, no payment status indicator, and no search function that returns all confirmed bookings for a given period. The operator must reconstruct that information from memory and scattered notes.

Google Calendar and Microsoft Excel fill some of the gaps left by the messaging tools. Google Calendar provides a visual overview of which dates are occupied, but it operates independently from the messaging applications, so any change to a booking requires a manual update in both places. Excel can hold structured records, but it depends entirely on correct manual entry. A date recorded in the wrong row, a row that is skipped during data entry, or a formula that stops working silently can leave the spreadsheet in an incorrect state that the operator may not discover until a guest arrives for a date that was never properly blocked.

These tools do not integrate with one another. The complete sequence of steps for processing a single booking typically involves confirming availability in the calendar, replying to the guest in WhatsApp, waiting for a payment screenshot, verifying the screenshot, updating the Excel record, updating the calendar, and remembering to send the check-in instructions at the right time. None of these steps is automated. If one is skipped or recorded incorrectly, the error propagates silently until it causes a visible problem — a double booking, a missing payment record, or a guest who arrives without receiving access instructions.

These tools were not designed for homestay management. They are general-purpose applications used for a specific operational purpose, and the reliability of that use depends entirely on the operator executing every manual step correctly, every time.

---

## 2.4 Comparative Study of Existing Systems

Three short-term rental platforms are reviewed in this section: Airbnb, Booking.com, and MySyok. The review covers the features most relevant to the problem described in Section 2.3 and the requirements gathered for HomeLodge.

### 2.4.1 Airbnb

Airbnb is an online accommodation marketplace founded in 2008. Hosts list properties on the platform, and guests search, compare, and book available units through the website or mobile application (Airbnb, 2024). The platform provides a centralised availability calendar, online payment processing, in-app messaging between host and guest, and a post-stay review system.

For an operator currently managing bookings through WhatsApp and a spreadsheet, Airbnb addresses several of the problems described in Section 2.2. Availability is centralised in one place, payment is processed and deposited automatically, and communication with guests happens within the platform rather than in a personal messaging application. The host dashboard shows upcoming bookings and cumulative earnings without requiring the operator to maintain a separate spreadsheet.

The limitation is structural. Airbnb governs the platform, not the operator. The rules for cancellations, dispute resolution, and service fees are set by Airbnb and cannot be changed by the host. Every confirmed booking incurs a platform commission. Properties must be listed publicly, which means the operator's guest records and pricing are held on a third-party platform. Operators who want to manage direct bookings without public marketplace exposure and without per-booking fees cannot achieve this through Airbnb. The platform has no QR code door access feature and no structured workflow for booking extensions.

### 2.4.2 Booking.com

Booking.com is a global online travel agency through which accommodation providers list properties and receive bookings from an international audience (Booking.com, 2024). The host management interface allows operators to update room availability, view incoming bookings, and communicate with guests before arrival. Payment arrangements vary by property: some operators collect payment directly from guests, while others use Booking.com's payment processing.

The platform is used by a wide range of accommodation types, from large hotels to small guesthouses. It provides analytics tools and manages a loyalty programme for returning guests.

The same structural constraint that applies to Airbnb applies to Booking.com. It is a marketplace. Operators who list there are bound by the platform's commission structure and content policies. The host interface does not support QR code door access, role-based access permissions, or booking extension management. Like Airbnb, the platform is built to give the marketplace visibility of transactions, not to give the operator a configurable private management tool.

### 2.4.3 MySyok

MySyok is a Malaysian short-term rental platform that connects property owners with domestic travellers (MySyok, 2024). Property owners list units, manage availability, and receive bookings through the platform. It supports Bahasa Malaysia and is oriented to the local rental market.

MySyok addresses localisation requirements that Airbnb and Booking.com do not meet, including local payment methods and language support. However, it operates on the same marketplace model. Operators list publicly and are subject to the platform's commission and policy structure. There is no in-platform messaging between guest and host, no QR code door access feature, no role and permission management system, and no booking extension workflow. The house policies that guests can see are limited to whatever the platform exposes, not what the operator configures independently.

### 2.4.4 Comparison Summary

Table 2.1 compares the three platforms against the key requirements of HomeLodge.

Table 2.1: Comparison of Existing Booking Systems

| Feature | Airbnb | Booking.com | MySyok | HomeLodge |
|---|---|---|---|---|
| Online availability calendar | Yes | Yes | Yes | Yes |
| Online payment processing | Yes | Yes | Yes | Yes |
| In-app guest to host messaging | Yes | Yes | No | Yes |
| QR code door access | No | No | No | Yes |
| Booking extension management | No | No | No | Yes |
| Role-based access control | No | No | No | Yes |
| Private, non-marketplace operation | No | No | No | Yes |
| Configurable house policies per unit | Limited | Limited | Limited | Yes |
| Immutable audit log | No | No | No | Yes |
| Configurable system settings | No | No | No | Yes |
| No per-booking marketplace commission | No | No | No | Yes |

Table 2.1 shows that all three platforms provide online availability management and payment processing, which removes the most immediate failures of the manual process described in Section 2.2. What none of them provides is the ability to operate privately without marketplace exposure, manage physical door access through QR codes, process booking extensions through a formal payment workflow, or apply role-based access control to administrative functions. These are the specific requirements that HomeLodge addresses.

---

## 2.5 Review of Development Methodology

### 2.5.1 Waterfall Model

The Waterfall model is a sequential software development approach in which each phase — requirements gathering, system design, implementation, testing, and deployment — must be completed before the next begins (Royce, 1970). The model is founded on the assumption that requirements can be fully captured and agreed on before any code is written, and that they will not change substantively during development.

Each phase produces a documented output, which makes progress traceable and the scope of each stage well-defined. This property is useful for projects with formal milestone reviews and fixed deliverables. The limitation is inflexibility: if a requirement is found to be incomplete or incorrect during implementation, the team must return to an earlier phase, which can disrupt the schedule.

For HomeLodge, the system requirements were documented in full before development began and the scope does not change once implementation starts. In this context, the sequential structure of Waterfall is a practical match rather than a constraint.

### 2.5.2 Agile Model

The Agile model is an iterative and incremental approach to software development. Development is divided into short cycles, typically two to four weeks long, with a working version of the system delivered at the end of each cycle (Beck et al., 2001). Requirements are expected to evolve throughout the project, and the development team adjusts priorities based on feedback received at the end of each cycle.

Agile is appropriate when the final requirements cannot be fully defined at the start and when stakeholders are available to give feedback regularly. In an academic project setting, sustained stakeholder availability throughout development is difficult to guarantee, and the absence of formal phase-level documentation can make it harder to structure the written report around the development process.

### 2.5.3 Rapid Application Development (RAD)

Rapid Application Development prioritises working prototypes over formal upfront planning (Martin, 1991). Users review working prototypes early in the process, and the system is refined through repeated short cycles of building and incorporating feedback.

RAD is appropriate when the project scope is narrow and the developer can move quickly between versions. The risk specific to this project is that RAD treats documentation as secondary to delivery speed. In a final year project where the written documentation carries equal academic weight to the software itself, a methodology that minimises documentation effort creates a conflict with the project's assessment requirements.

### 2.5.4 Iterative Model

The Iterative model develops a system through a series of repeated cycles, each of which produces a more complete version of the system than the one before (Larman & Basili, 2003). Unlike Agile, it does not require short fixed-length sprints or continuous stakeholder review sessions. Each cycle covers requirements analysis, design, implementation, and testing, but only for a defined portion of the system's functionality. The output of each cycle informs the scope of the next.

The model is useful when a large system must be broken into independently deliverable pieces for verification, or when design decisions made in early cycles need to be adjusted before the next cycle begins. This reduces the risk of a significant late-stage design error requiring rework of the entire system.

The limitation for this project is similar to Agile: the iterative model works best when requirements are not fully defined at the start and when there is room to refine scope between cycles. For HomeLodge, the full requirements were documented and fixed before development began. The flexibility that the iterative model provides is not needed here, and the additional planning overhead of managing multiple iteration boundaries does not add value when the system scope is already known in full.

### 2.5.5 Methodology Comparison and Selection

Table 2.2 compares the four methodologies on criteria relevant to this project.

Table 2.2: Comparison of Software Development Methodologies

| Criteria | Waterfall | Agile | RAD | Iterative |
|---|---|---|---|---|
| Flexibility to changing requirements | Low | High | Medium | Medium |
| Documentation produced at each phase | Extensive | Minimal | Moderate | Moderate |
| Suitability for a fixed-scope academic project | High | Medium | Low | Medium |
| Speed of delivering working software | Low | High | High | Medium |
| Risk of technical debt | Low | Medium | High | Low |
| Stakeholder involvement required | Low | High | Medium | Low |

As Table 2.2 shows, the Waterfall model scores highest on documentation output and suitability for a fixed-scope academic project. The system requirements were fully defined before development began, the scope does not change once implementation is underway, and the phase-based structure produces the specific outputs that a PSM report requires at each stage: a requirements specification, a system design, implementation records, and test results. These outputs correspond to Chapters 3, 4, and 5 of this report. Agile, RAD, and the Iterative model each provide flexibility that is well-suited to projects where requirements are expected to change, but that flexibility is not applicable here. Waterfall was selected as the development methodology for HomeLodge.

---

## 2.6 Review of Technologies

### 2.6.1 Backend Framework: PHP and Laravel

PHP is a server-side scripting language used extensively in web application development. Laravel is a PHP framework built on a Model-View-Controller (MVC) architecture, with a built-in object-relational mapper (ORM), authentication scaffolding, queue runner, task scheduler, and a maintained package ecosystem (Otwell, 2024).

Node.js with Express was assessed as an alternative. Node.js manages concurrent connections efficiently, which is an advantage for real-time applications, but it does not include the same breadth of built-in tooling. Building HomeLodge on Node.js would require selecting and integrating separate packages for authentication, database access, database migrations, queue processing, PDF generation, and QR code generation. For a single-developer project with a broad feature scope, that volume of external dependency management carries real risk: each package requires separate maintenance, and compatibility issues between packages are not always apparent until they cause a problem in production.

Laravel has first-party packages that address most of HomeLodge's requirements without additional integration: Reverb for WebSocket communication, Socialite for Google OAuth, Sanctum for session authentication, and built-in queue and scheduler systems for background jobs. The gap between what the framework provides and what the system requires is small, which allows development effort to focus on business logic rather than infrastructure setup.

Laravel 11 with PHP 8.2 was selected as the backend framework.

### 2.6.2 Frontend: Blade, Alpine.js, and Bootstrap

Laravel Blade is Laravel's server-side templating engine. It processes view files on the server, inserts data into the template, and sends complete HTML to the browser on each request (Laravel Documentation, 2024). All rendering logic runs on the server, and the browser receives a finished page without needing to execute a client-side framework startup sequence.

Alpine.js is a JavaScript library for adding client-side interactivity to server-rendered pages. It operates through declarative attributes written directly in the HTML markup and does not require a build pipeline or a component registration system (Alpine.js, 2024). In HomeLodge, it manages the booking calendar, the real-time notification counter, and the chat interface — situations where some state must be held in the browser between user interactions without routing every action through the server.

Bootstrap 5 provides a grid layout system and a library of pre-built interface components that keep the interface consistent across pages without requiring custom CSS for every element (Bootstrap, 2024).

The alternative considered was a Single Page Application (SPA) built with Vue.js or React, backed by a separate Laravel REST API. An SPA produces a more fluid browser experience but requires the API to be built and secured as a separate layer, client-side routing to be managed independently of the server, and state management to be handled within the browser for every page. Most operations in HomeLodge involve reading data from and writing data to the server. The additional complexity of an SPA architecture does not produce a proportionate improvement in user experience for this feature set.

Blade with Alpine.js and Bootstrap 5 was selected for the frontend.

### 2.6.3 Database: MySQL with Eloquent ORM

MySQL is a relational database management system that stores data in structured tables, enforces referential integrity through foreign key constraints, and supports atomic transactions (MySQL Documentation, 2024). A transaction is a group of database operations that must all succeed or all be reversed together. Transactions are relevant to HomeLodge because operations such as booking confirmation involve writing to several tables simultaneously — the booking record, the billing record, and the QR code record — and all of those writes must either complete together or be rolled back together if one fails.

Eloquent ORM is Laravel's built-in mapping layer between PHP model classes and MySQL tables. It represents relationships between tables as method calls on model objects, which keeps query logic consistent across the codebase and reduces the amount of raw SQL written directly. In HomeLodge, a booking record relates to a user, a homestay unit, one or more payment records, a QR code record, and potentially one or more extension records; Eloquent manages these relationships without requiring custom join logic in every query.

MariaDB was considered as an alternative. It is binary-compatible with MySQL and performs comparably on typical web application workloads. MySQL was chosen because it is the default database in the majority of Laravel hosting environments and has broader coverage in Laravel-specific documentation and community resources.

MySQL 8 with Eloquent ORM was selected for all relational data storage in HomeLodge.

### 2.6.4 Real-Time Communication: Laravel Reverb and Laravel Echo

The chat module in HomeLodge requires that a message appears on the recipient's screen as soon as it is sent, without the recipient manually refreshing the page. Standard HTTP connections cannot deliver this: HTTP requests are initiated by the client and closed after each response, so the server has no means of pushing data to the browser unprompted. WebSocket connections remain open after the initial handshake, allowing the server to send data to the browser at any time without a new client request.

Laravel Reverb is a WebSocket server built and maintained by the Laravel team (Laravel Reverb, 2024). It runs as a separate process alongside the Laravel application and handles broadcast channel management, connection lifecycle, and event routing. Laravel Echo is the JavaScript library that establishes a WebSocket connection in the browser, subscribes to the relevant broadcast channels, and receives events from Reverb as they are emitted.

Pusher is a managed WebSocket service that Laravel also supports natively. The configuration interface for Pusher and Reverb is identical in Laravel, so switching between them requires only an environment variable change and no code modification. Pusher charges a recurring subscription fee. Reverb is self-hosted and has no per-connection cost. For a project where minimising external service dependencies is a stated requirement, Reverb is the appropriate choice.

Laravel Reverb with Laravel Echo was selected for real-time communication.

### 2.6.5 Authentication and Role-Based Access Control

HomeLodge supports two authentication mechanisms. Email and password login is handled through Laravel's built-in session authentication system. Google Single Sign-On (SSO) is implemented via the OAuth 2.0 protocol using the `laravel/socialite` package, which manages the authorisation redirect, token exchange, and user account lookup.

Role-based access control (RBAC) governs what each authenticated user is permitted to do within the system. HomeLodge requires fine-grained RBAC: an administrator can create custom roles, assign specific permissions to each role, and assign roles to individual user accounts. The `spatie/laravel-permission` package implements this model through database-backed role and permission tables, with helper methods that integrate with Laravel's built-in gate and policy system.

A custom RBAC implementation was assessed as an alternative. The `spatie/laravel-permission` package covers the full requirements without modification and is actively maintained. Building an equivalent system from scratch would require time that is better directed toward the system's business logic.

Both `laravel/socialite` for SSO and `spatie/laravel-permission` for RBAC were selected.

### 2.6.6 QR Code Generation

Each confirmed booking in HomeLodge generates a unique QR code that encodes a time-limited access token. The token is valid from the booked check-in time to the booked check-out time. The QR code must be generated at the moment the booking is confirmed, without a manual step and without relying on an external service.

The `simplesoftwareio/simple-qrcode` package generates QR codes as SVG or PNG images directly within the Laravel application and requires no outbound API call (SimpleSoftwareIO, 2024). The alternative approach — sending a request to a third-party QR code generation API — places a network dependency in the booking confirmation path. If the external service is unavailable at the moment the booking is confirmed, the system cannot issue the QR code. Local generation removes this dependency.

`simplesoftwareio/simple-qrcode` was selected for QR code generation.

### 2.6.7 Payment Gateway

HomeLodge processes payments through an online gateway using a server-side API integration and webhook callbacks. A webhook is an HTTP notification that the payment gateway sends to HomeLodge when a payment event occurs, such as a successful transaction. This allows the system to update the booking status and generate the payment receipt automatically, without requiring the guest to complete a redirect back to the application.

Three gateways compatible with the Malaysian market were assessed: Billplz, Toyyibpay, and Stripe. Billplz and Toyyibpay are Malaysian payment processors that support FPX (Financial Process Exchange), which is the inter-bank payment method used in the majority of Malaysian online transactions. FPX allows a customer to pay directly from a bank account without a credit card. Stripe has broad international payment method coverage but limited FPX support for Malaysian banks.

For a system serving Malaysian operators and their guests, FPX support is a practical requirement. The final gateway selection depends on fee structure and account approval at deployment time. The system architecture uses a webhook-driven integration model that is compatible with all three options, so the payment integration layer does not need to be rebuilt if the selected gateway changes.

The gateway selection is deferred to deployment, with Billplz, Toyyibpay, and Stripe as the shortlisted options.

### 2.6.8 Notification System

HomeLodge sends notifications to users and the administrator through two channels: email and in-app notifications.

Email notifications are sent through Laravel's Mail system using SMTP credentials that the administrator configures through the admin settings panel. The operator can switch between SMTP providers — for example, from a development mail service to a production email account — by updating the admin settings, without modifying any application code.

In-app notifications use Laravel's built-in database notification driver. Each notification is stored as a record in the database notifications table and read by the application when the user loads a page. No third-party push service is required, and the user is not asked to grant browser push permission. This delivery method is sufficient for the notification types HomeLodge generates: booking confirmations, payment outcomes, payment reminders, check-in and check-out reminders, and extension payment requests.

### 2.6.9 Technology Stack Summary

Table 2.3 summarises the technology components selected for HomeLodge and the primary justification for each selection.

Table 2.3: Summary of Technology Selections

| Component | Technology Selected | Justification |
|---|---|---|
| Backend framework | Laravel 11, PHP 8.2 | First-party packages cover most feature requirements without additional integration |
| Frontend rendering | Laravel Blade, Alpine.js | Server-side rendering with selective client-side interactivity |
| CSS framework | Bootstrap 5 | Pre-built responsive components, reduces custom CSS overhead |
| Database | MySQL 8, Eloquent ORM | Relational integrity, atomic transaction support, Laravel integration |
| Real-time communication | Laravel Reverb, Laravel Echo | Self-hosted WebSocket server, no subscription cost |
| Authentication | Laravel Session, laravel/socialite | Email and password login, Google SSO via OAuth 2.0 |
| Role-based access control | spatie/laravel-permission | Database-backed, fine-grained RBAC |
| QR code generation | simplesoftwareio/simple-qrcode | Local generation, no external API dependency |
| Payment gateway | TBD: Billplz, Toyyibpay, or Stripe | FPX support for Malaysian market |
| Email notifications | Laravel Mail, configurable SMTP | Admin-configurable SMTP, no fixed provider dependency |
| In-app notifications | Laravel database notifications | No browser permission or external push service required |

---

## 2.6.10 Literature Review of Technology Used

This section reviews academic and industry literature relevant to the major technology components selected for HomeLodge. Each subsection states a finding from the reviewed literature that contextualises the selection made in Sections 2.6.1 to 2.6.8.

### 2.6.10.1 Server-Side Web Application Frameworks

Web application frameworks provide pre-built abstractions for routing, data access, session management, and input handling. The JetBrains Developer Ecosystem Survey (2023), which collected responses from over 26,000 software developers globally, recorded Laravel as the most widely used PHP framework among professional developers. W3Techs (2024) reports that PHP is used as the server-side language by approximately 77% of all websites whose server-side technology is identifiable, which reflects the scale and activity of the PHP ecosystem.

Waheed, Ahmad, and Iqbal (2021) compared PHP, Python, and Node.js as backend options for web-based information management systems. Their evaluation found that PHP frameworks combined with an MVC architecture produce maintainable codebases comparable to Node.js for server-rendered applications, with less initial configuration overhead for small development teams. The finding supports the selection of Laravel for a single-developer project where setup cost and integrated tooling coverage are more relevant considerations than raw concurrency performance.

### 2.6.10.2 Relational Database Management Systems

Coronel and Morris (2019) define a relational database management system as one that organises data into tables with defined relationships, uses SQL for data manipulation, and enforces data integrity through key constraints and transaction support. Their definition establishes that the distinguishing characteristic of a relational system is its capacity to guarantee consistency across related data through atomic transactions.

Cattell (2011) compared relational and non-relational (NoSQL) database systems for web application use and concluded that relational systems are the appropriate choice when data consistency and referential integrity are required and when the entity relationships are well-defined before implementation. For HomeLodge, these conditions hold: the database schema was designed in full before development began, bookings have mandatory relationships to users, homestay units, payment records, and QR code records, and operations such as booking confirmation must write to several tables as a unit. A document or key-value store does not enforce these relationships at the database level; enforcing them in the application layer instead would increase the risk of inconsistency. MySQL, as a relational system, manages this at the database level.

### 2.6.10.3 Real-Time Web Communication

Pimentel and Nickerson (2012) evaluated four approaches to server-push communication: periodic HTTP polling, long polling, Server-Sent Events, and WebSockets. Their analysis measured message latency, bandwidth consumption, and server resource usage. WebSocket connections produced the lowest message latency and the lowest per-message overhead among the four options for applications that require bidirectional real-time data exchange. The WebSocket protocol is formally specified in IETF RFC 6455 (Fette & Melnikov, 2011), which defines the handshake procedure, message framing, and connection lifecycle that server implementations must follow.

For the HomeLodge chat module, HTTP-based alternatives introduce delays that are proportional to the polling interval or the server's long-poll timeout window. Pimentel and Nickerson (2012) found these delays to be inappropriate for applications where messages must arrive within one second of being sent. The literature supports WebSocket as the correct protocol for this requirement.

### 2.6.10.4 QR Code-Based Access Control

Quick Response (QR) codes were developed by Denso Wave Incorporated in 1994 as a high-density machine-readable label for industrial tracking (Denso Wave, 2024). Their use as an access control mechanism expanded with the adoption of mobile devices capable of scanning QR codes without dedicated hardware readers.

Shin, Jung, Chang, and Lee (2012) examined the security properties of QR codes used in mobile authentication and access control. They found that a static QR code presents an unauthorised-copy risk: any person who obtains an image of a valid code can use it to gain access at any time. Their recommended mitigation is to encode a time-limited token, so that a copied code becomes invalid once the original session expires.

HomeLodge applies this approach. The QR code for each booking encodes a unique token valid from the check-in time to the check-out time. The token is invalidated automatically at check-out and regenerated for the next confirmed booking only after housekeeping is marked complete. A copy of the guest's QR code cannot be used after the stay ends. This design directly addresses the vulnerability identified by Shin et al. (2012).

### 2.6.10.5 Role-Based Access Control

Sandhu, Coyne, Feinstein, and Youman (1996) formalised the role-based access control model in their paper "Role-Based Access Control Models," published in IEEE Computer. The model assigns permissions to roles rather than directly to users. Users are then assigned to roles. Access decisions are resolved by checking whether the user's current role carries the permission required for the operation being requested.

Ferraiolo, Sandhu, Gavrila, Kuhn, and Chandramouli (2001) developed this into the NIST RBAC reference model, which defines four levels: flat RBAC, hierarchical RBAC, constrained RBAC, and symmetric RBAC. HomeLodge uses flat RBAC: permissions are assigned to roles, and roles are assigned to users. There is no role hierarchy and no separation-of-duty constraint. This level is sufficient for the system's access control requirements, where an administrator creates roles, assigns permissions to each role, and assigns roles to user accounts. The `spatie/laravel-permission` package implements this flat model as described by Sandhu et al. (1996).

---

## 2.7 Chapter Summary

This chapter reviewed five areas that shape the development of HomeLodge.

The case study showed that small-scale multi-unit homestay operators in Malaysia manage their operations manually through messaging applications and disconnected productivity tools. The process is held together by personal habit rather than any formal system. It works for one unit. With three or more properties running at the same time, the number of manual steps per booking stays the same but the number of concurrent bookings grows, and errors accumulate.

The current systems analysis found that the specific tools used — WhatsApp, Telegram, Google Calendar, and Excel — are not designed for this purpose. They lack integration with each other, they provide no automatic availability validation, and there is no consolidated view of all active bookings across multiple units.

The comparison of Airbnb, Booking.com, and MySyok showed that each platform handles availability and payment adequately, but all three operate as public marketplaces. None supports QR code door access, booking extensions with a payment workflow, fine-grained role-based access control, or private operation without public listing and per-booking commission. These are the gaps HomeLodge is built to fill.

The methodology review compared four approaches: Waterfall, Agile, Rapid Application Development, and the Iterative model. All four are established methodologies for software development projects, but they differ in how well they suit a project with fixed requirements and formal documentation deliverables. Waterfall was selected because the requirements for HomeLodge are fully defined before development begins, the scope does not change once implementation starts, and the phase outputs that Waterfall requires — requirements specification, system design, implementation, and test report — map directly to the chapters of this PSM report.

The technology review selected Laravel 11 as the backend framework, Blade with Alpine.js and Bootstrap 5 for the frontend, MySQL 8 with Eloquent ORM for the database, Laravel Reverb and Echo for real-time communication, and supporting packages for authentication, RBAC, QR code generation, payment, and notifications. Each choice was made to reduce external dependencies, keep the integration surface small, and focus development effort on the system's business logic rather than its infrastructure.
