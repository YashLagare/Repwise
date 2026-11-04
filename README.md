**Project Name**: Repwise  
**Project Description**: Full-Stack Personal Training Marketplace and Coaching Platform  
**Business Problem Solved**: Eliminates fragmented communication, manual payment reconciliation, and scheduling overhead for fitness trainers and clients by providing a unified portal for program sales, video lessons, 1:1 scheduling, chat, and HD video consultations.  
**Target Users**: Fitness Coaches/Personal Trainers and Trainees/Clients  
**Business Value**: Accelerates client monetization with direct-to-consumer digital workout programs and automated 1:1 session booking with real-time analytics.  
**Current Version**: 0.1.0  
**Last Updated**: November 4, 2026  
**Repository Structure Summary**: Monolithic Next.js 16 full-stack application structured with App Router, Drizzle ORM, Neon PostgreSQL, Neon Auth, Neon Realtime, Stream Chat/Video, and ImageKit.  

[INSERT_PROJECT_COVER_SCREENSHOT]

---

# Table of Contents

- [Cover Page](#cover-page)
- [Table of Contents](#table-of-contents)
- [1 Executive Summary](#1-executive-summary)
- [2 Project Overview](#2-project-overview)
- [3 Technology Stack](#3-technology-stack)
- [4 System Architecture](#4-system-architecture)
- [5 Repository Structure](#5-repository-structure)
- [6 Features](#6-features)
- [7 UI Screenshots](#7-ui-screenshots)
- [8 Database Design](#8-database-design)
- [9 Entity Relationship Diagram](#9-entity-relationship-diagram)
- [10 Security](#10-security)
- [11 Authentication](#11-authentication)
- [12 API Documentation](#12-api-documentation)
- [13 Third-party Services](#13-third-party-services)
- [14 Environment Variables](#14-environment-variables)
- [15 Major Dependencies](#15-major-dependencies)
- [16 Installation Guide](#16-installation-guide)
- [17 Deployment](#17-deployment)
- [18 Request Lifecycle](#18-request-lifecycle)
- [19 Performance](#19-performance)
- [20 Security Review](#20-security-review)
- [21 Challenges & Engineering Decisions](#21-challenges--engineering-decisions)
- [22 Future Improvements](#22-future-improvements)
- [23 Developer Notes](#23-developer-notes)

---

# 1 Executive Summary

### Purpose
Repwise is a dedicated coaching marketplace that connects independent personal trainers with clients seeking structured fitness coaching, digital workout programs, and scheduled 1:1 live video training.

### Key Capabilities
- **Trainer Marketplace**: Public trainer profiles displaying specialties, ratings, program offerings, and available booking slots.
- **Digital Programs & Lessons**: Multi-lesson video programs featuring a free first-lesson preview followed by private authenticated streaming for purchasers.
- **Atomic 1:1 Scheduling**: Slot hold and booking engine preventing double-booking through 30-minute reservation leases.
- **Live Communication**: In-app 1:1 messaging (replies, threads, emoji reactions) and WebRTC-based video rooms powered by Stream.
- **Real-Time Trainer Dashboard**: Live analytics feed broadcasting incoming sales and booking activity through Neon Realtime sealed queries.

### Technology Summary
The platform leverages Next.js 16.4.3 (App Router with Turbopack and React 19.3), Neon Postgres managed with Drizzle ORM, Neon Auth for identity and session management, Stream SDKs for communications, ImageKit for media optimization and signed private video streaming, and Sentry for observability.

### Architecture Summary
Repwise implements a unified full-stack architecture running as a long-lived Node.js server. Data mutations are processed through validated Server Actions, dynamic rendering uses React Server Components inside Suspense boundaries, and live client updates utilize reactive sealed queries over WebSocket/SSE.

### Business Value
Provides personal trainers with an end-to-end digital business operating system without external software subscriptions, while offering clients a friction-free experience to learn, communicate, and train.

---

# 2 Project Overview

### Objective
Deliver a production-ready personal training marketplace that consolidates client onboarding, digital content sales, real-time communication, and video consultations into a single platform.

### Scope
- **Client Experience**: Browse coaches by specialty, purchase digital workout curriculums, stream private training videos, reserve 1:1 consultation sessions, direct-message coaches, join live video sessions, and publish verified reviews.
- **Trainer Experience**: Maintain professional profiles, author multi-part video programs, publish 60-minute booking slots, inspect active clients, and monitor revenue in real time.
- **Out of Scope (v1)**: Stripe Connect multi-party payouts, subscription memberships, automated refund workflows, group video sessions, and localization.

### Primary Modules
1. **Discovery & Marketing (`app/(site)`)**: Landing page, featured coaches, specialty filtering, coach profiles, and public curriculum overviews.
2. **Onboarding & Role Gate (`app/(app)/onboarding`)**: Role selection (Client vs. Trainer) and trainer business profile configuration.
3. **Training & Library (`app/(site)/library`, `app/(site)/programs`)**: Purchased program inventory, video player with signed HLS streaming, and booked session management.
4. **Interactive Communications (`app/(app)/messages`, `app/call`)**: 1:1 Stream Chat messaging threads and 1:1 WebRTC video call rooms.
5. **Trainer Management Hub (`app/dashboard`)**: Earnings metrics, live sales feed, program management with private video uploads, calendar slot administration, and client rosters.

### Target Audience
- Independent personal trainers, strength coaches, nutritionists, and physical therapists.
- Fitness enthusiasts, athletes, and remote training clients.

### Real-world Use Cases
- A trainer publishes a 4-week "Strength Foundations" video program; a prospective client watches Lesson 1 for free, purchases the program, and unlocks the remaining 7 private lessons.
- A client selects an open Friday 10:00 AM slot on a trainer's calendar; the slot is held atomically and booked. At session time, both parties open the in-app call room to conduct the 1:1 workout.

---

# 3 Technology Stack

### Frontend
| Technology | Version | Purpose |
|---|---|---|
| Next.js | 16.4.3 | React full-stack framework with App Router, Turbopack, and Server Actions |
| React | 19.3.0 | Core UI library |
| React DOM | 19.3.0 | DOM rendering engine |
| Stream Chat React | 14.14.1 | In-app messaging UI components and thread management |
| Stream Video React SDK | 1.44.1 | WebRTC video calling interface and room controls |
| ImageKit Next | 2.1.6 | Optimized image delivery and responsive image components |
| ImageKit Video Player | 1.0.0-beta.6 | Video player supporting signed URLs and HLS streaming |
| Lucide React | 1.63.0 | Iconography system |

### Backend
| Technology | Version | Purpose |
|---|---|---|
| Node.js | >= 20 (Target 22 LTS) | Runtime execution environment |
| Next.js Server Actions | 16.4.3 | RPC-style backend mutations and data processing |
| Stream Node SDK | 0.8.15 | Server-side user upserts, channel generation, and video room token minting |
| Node Postgres (`pg`) | 8.23.1 | TCP connection pool for long-lived server execution |
| Zod | 4.6.7 | Strict schema validation for inputs and mutations |

### Database
| Technology | Version | Purpose |
|---|---|---|
| Neon Postgres | Serverless PostgreSQL | Relational data persistence |
| Drizzle ORM | 0.45.4 | TypeScript-first ORM and SQL query builder |
| Drizzle Kit | 0.31.11 | Database migrations and schema push utility |
| Neon Realtime | 0.1.2 | Real-time database query sealing and reactive updates |
| Neon Realtime Drizzle | 0.1.0 | Realtime Drizzle adapter integration |

### Authentication
| Technology | Version | Purpose |
|---|---|---|
| Neon Auth (`@neondatabase/auth`) | 0.7.0-beta | Managed authentication system with prebuilt UI and session management |

### Styling
| Technology | Version | Purpose |
|---|---|---|
| Tailwind CSS | 4.x | Utility-first CSS engine with `@tailwindcss/turbopack` |
| Class Variance Authority | 0.7.1 | Declarative component variant management |
| Tailwind Animate (`tw-animate-css`) | 1.4.0 | Animation utilities |

### Build Tools & Linters
| Technology | Version | Purpose |
|---|---|---|
| Turbopack | Next.js Built-in | Incremental bundling engine |
| TypeScript | 5.x | Static type checking and compiler |
| ESLint | 9.x | Static code quality and convention analysis |
| TSX | 4.23.15 | TypeScript script execution engine for database seeding |

### Cloud Services & Observability
| Technology | Purpose |
|---|---|
| Neon | Serverless Postgres database, Managed Auth, and Realtime query engine |
| ImageKit.io | Media storage, image transformations, and private signed video CDN |
| Stream.io | Chat messaging infrastructure and WebRTC video call infrastructure |
| Sentry (`@sentry/nextjs` 11.10.0) | Crash reporting, session replay, performance tracing, and structured logging |

### Testing
| Technology | Purpose |
|---|---|
| Node Test Runner (`node:test`) | Native unit test execution with `--experimental-strip-types` |
| Node Assert (`node:assert/strict`) | Type-safe runtime assertions for business rule validation |

---

# 4 System Architecture

Repwise operates as a monolithic full-stack application deployed as a persistent Node.js server. The frontend utilizes Server Components for dynamic data rendering inside React Suspense boundaries, delegating mutations to Server Actions. Real-time updates bypass polling by utilizing Neon Realtime subscriptions over WebSocket/SSE.

```text
+------------------------------------------------------------------------------------+
|                                    Client Browser                                  |
|                                                                                    |
|  +--------------------+  +----------------------+  +----------------------------+  |
|  | Server Components  |  | Client Interactions  |  |   Stream Video & Chat      |  |
|  | (HTML / Suspense)  |  |   (Forms / Actions)  |  | (WebRTC / WebSockets)      |  |
|  +---------+----------+  +----------+-----------+  +-------------+--------------+  |
+------------|------------------------|----------------------------|-----------------+
             |                        |                            |
      HTTPS Page Load           Server Action RPC             Signaling / WebRTC
             |                        |                            |
+------------v------------------------v----------------------------|-----------------+
|                                 Next.js Server                   |                 |
|                                                                  |                 |
|  +------------------------------------------------------------+  |                 |
|  |               Optimistic Proxy (`proxy.ts`)                |  |                 |
|  +-----------------------------+------------------------------+  |                 |
|                                |                                 |                 |
|  +-----------------------------v------------------------------+  |                 |
|  |          App Router (Pages, Layouts & Route Handlers)      |  |                 |
|  +-----------------------------+------------------------------+  |                 |
|                                |                                 |                 |
|  +-----------------------------v------------------------------+  |                 |
|  |         Server Actions Layer (`app/actions/*.ts`)          |  |                 |
|  |     (Zod Validation, Session Check, Authorization)         |  |                 |
|  +-----------------------------+------------------------------+  |                 |
|                                |                                 |                 |
|  +-----------------------------v------------------------------+  |                 |
|  |           Data Layer (`lib/queries.ts`, Drizzle ORM)       |  |                 |
|  +-----------------------------+------------------------------+  |                 |
+--------------------------------|---------------------------------|-----------------+
                                 |                                 |
         +-----------------------+-----------------------+         |
         |                       |                       |         |
         v                       v                       v         v
+-----------------+     +-----------------+     +--------------------+
|  Neon Postgres  |     |    ImageKit     |     |     Stream.io      |
|  - App Database |     |  - Media CDN    |     |  - Chat Engine     |
|  - Neon Auth    |     |  - HMAC Signer  |     |  - Video SFU       |
|  - Realtime Hub |     |  - Video Assets |     +--------------------+
+-----------------+     +-----------------+
```

---

# 5 Repository Structure

```text
repwise/
├── app/
│   ├── (app)/
│   │   ├── layout.tsx                     # Protected shell layout
│   │   ├── messages/
│   │   │   ├── chat-inbox.tsx             # Stream Chat UI wrapper
│   │   │   ├── page.tsx                   # Inbox page
│   │   │   └── stream-chat.css            # Dark athletic Stream styling
│   │   └── onboarding/
│   │       ├── onboarding-form.tsx        # Client/Trainer role onboarding form
│   │       └── page.tsx                   # Onboarding page entry
│   ├── (site)/
│   │   ├── layout.tsx                     # Public site navigation shell
│   │   ├── page.tsx                       # Landing page with hero & featured trainers
│   │   ├── library/
│   │   │   └── page.tsx                   # Client purchased programs & booked slots
│   │   ├── programs/
│   │   │   └── [id]/
│   │   │       ├── lesson-player.tsx      # ImageKit video player integration
│   │   │       └── page.tsx               # Program details & lesson selector
│   │   └── trainers/
│   │       ├── page.tsx                   # Coach directory with specialty filters
│   │       └── [id]/
│   │           └── page.tsx               # Coach profile, programs, slots & reviews
│   ├── actions/
│   │   ├── checkout.ts                    # Program purchase and slot booking actions
│   │   ├── media.ts                       # ImageKit upload auth and lesson URL signer
│   │   ├── profile.ts                     # Profile creation and onboarding mutations
│   │   ├── programs.ts                    # Program CRUD and lesson management
│   │   ├── reviews.ts                     # Client review submission and updates
│   │   ├── slots.ts                       # Availability slot management actions
│   │   └── stream.ts                      # Stream token generator action
│   ├── api/
│   │   ├── auth/
│   │   │   └── [...path]/route.ts         # Neon Auth route handlers
│   │   └── live/
│   │       ├── sales/route.ts             # Sealed query renewal for sales feed
│   │       └── slots/route.ts             # Sealed query renewal for slot availability
│   ├── auth/
│   │   └── [path]/page.tsx                # Neon Auth prebuilt authentication views
│   ├── call/
│   │   └── [slotId]/
│   │       ├── call-room.tsx              # WebRTC video room component
│   │       ├── page.tsx                   # Server authorization & call gate
│   │       └── stream-video.css           # Stream Video player overrides
│   ├── dashboard/
│   │   ├── layout.tsx                     # Trainer dashboard navigation and gate
│   │   ├── page.tsx                       # Trainer revenue overview & live sales feed
│   │   ├── clients/
│   │   │   └── page.tsx                   # Trainer client roster & purchase history
│   │   ├── programs/
│   │   │   ├── page.tsx                   # Trainer program list
│   │   │   └── [id]/page.tsx              # Program editor, cover upload & lesson builder
│   │   └── slots/
│   │       ├── page.tsx                   # Slot management calendar list
│   │       └── slot-form.tsx              # DateTime input for adding 60-min slots
│   ├── favicon.ico                        # Application favicon
│   ├── global-error.tsx                   # Root error boundary with Sentry capture
│   ├── globals.css                        # Tailwind v4 styles and color tokens
│   ├── layout.tsx                         # Root HTML shell with theme initialization
│   └── not-found.tsx                      # 404 error page
├── components/
│   ├── ui/                                # Headless UI components (button, card, dialog, etc.)
│   ├── action-form.tsx                    # Form wrapper binding Server Actions with status states
│   ├── auth-provider.tsx                  # Client-side Neon Auth session context provider
│   ├── cards.tsx                          # Shared trainer and program presentation cards
│   ├── dashboard-nav.tsx                  # Trainer dashboard navigation links
│   ├── empty-state.tsx                    # Standardized empty list state component
│   ├── live-sales.tsx                     # Neon Realtime client listener for trainer sales
│   ├── live-slots.tsx                     # Neon Realtime client listener for booking calendar
│   ├── local-time.tsx                     # Client component rendering timezone-aware timestamps
│   ├── logo.tsx                           # Repwise SVG logo
│   ├── page-header.tsx                    # Reusable page title and description header
│   ├── rating.tsx                         # Star rating presentation component
│   ├── realtime.tsx                       # Generic Neon Realtime provider wrapper
│   ├── share-profile-button.tsx           # Social/clipboard profile sharing utility
│   ├── site-footer.tsx                    # Footer component
│   ├── site-header.tsx                    # Top navigation header with role-based routing
│   ├── theme-toggle.tsx                   # Light/dark mode toggle button
│   ├── uploader.tsx                       # ImageKit browser file upload component
│   └── user-avatar.tsx                    # Responsive user avatar component
├── lib/
│   ├── db/
│   │   ├── index.ts                       # Pooled PostgreSQL connection and Drizzle client
│   │   └── schema.ts                      # Database table definitions, enums, and constraints
│   ├── access.test.ts                     # Unit tests for domain access rules
│   ├── access.ts                          # Pure access verification functions (lessons, reviews)
│   ├── auth-client.ts                     # Neon Auth client-side SDK initialization
│   ├── auth.ts                            # Server-side auth helper (getUser, requireTrainer)
│   ├── imagekit.ts                        # ImageKit server client and HMAC-SHA1 URL signer
│   ├── mock-data.ts                       # Seed fixtures for trainers, programs, and lessons
│   ├── queries.ts                         # Server-side Drizzle SQL queries
│   ├── realtime.ts                        # Server-side Neon Realtime query sealing
│   ├── specialties.ts                     # Fixed fitness specialty list and metadata
│   ├── stream.ts                          # Server-side Stream client, tokens, and channel creators
│   └── utils.ts                           # Utility helpers (formatting, UUID validation)
├── scripts/
│   ├── seed.ts                            # Database seed script for trainers and programs
│   └── upload-images.ts                   # Batch upload script for seed images to ImageKit
├── drizzle.config.ts                      # Drizzle Kit CLI configuration
├── eslint.config.mjs                      # ESLint configuration
├── instrumentation.ts                     # Sentry server-side instrumentation hook
├── instrumentation-client.ts              # Sentry client-side instrumentation hook
├── next.config.ts                         # Next.js configuration with Turbopack and Sentry
├── package.json                           # Project manifest and script declarations
├── proxy.ts                               # Optimistic edge middleware proxy
├── sentry.server.config.ts                # Sentry server-side runtime initialization
└── tsconfig.json                          # TypeScript configuration
```

---

# 6 Features

### 1. Trainer Marketplace & Discovery
- **Purpose**: Allow prospective clients to discover certified personal trainers and filter by coaching disciplines.
- **Business Value**: Drives top-of-funnel customer acquisition and coach visibility.
- **Main Components**: `app/(site)/trainers/page.tsx`, `components/cards.tsx` (`TrainerCard`).
- **Related APIs**: `listTrainers()` in `lib/queries.ts`.
- **Dependencies**: Drizzle ORM, PostgreSQL array filters.

### 2. Trainer Profile & Public Portfolio
- **Purpose**: Present coach credentials, biographies, programs, live availability, and verified client testimonials.
- **Business Value**: Increases visitor conversion to paid programs and booked sessions.
- **Main Components**: `app/(site)/trainers/[id]/page.tsx`, `components/cards.tsx`, `components/live-slots.tsx`.
- **Related APIs**: `getTrainer()`, `listTrainerPrograms()`, `listOpenSlots()`, `listReviews()` in `lib/queries.ts`.
- **Dependencies**: Drizzle ORM, Neon Realtime.

### 3. Workout Programs & Curriculum Player
- **Purpose**: Host digital training curriculums consisting of ordered video lessons. Lesson 1 is unrestricted as a preview; subsequent lessons require verified purchase.
- **Business Value**: Delivers scalable, asynchronous revenue for coaches through digital content sales.
- **Main Components**: `app/(site)/programs/[id]/page.tsx`, `app/(site)/programs/[id]/lesson-player.tsx`.
- **Related APIs**: `signLessonUrl` in `app/actions/media.ts`, `buyProgram` in `app/actions/checkout.ts`.
- **Dependencies**: ImageKit Video Player SDK, ImageKit Server SDK.

### 4. Atomic 1:1 Consultation Booking
- **Purpose**: Provide real-time reservation of 60-minute coaching slots with an atomic hold mechanism that prevents race conditions.
- **Business Value**: Facilitates frictionless scheduling of high-ticket 1:1 coaching calls.
- **Main Components**: `components/live-slots.tsx`, `app/dashboard/slots/slot-form.tsx`.
- **Related APIs**: `bookSlot` in `app/actions/checkout.ts`, `addSlot`, `deleteSlot` in `app/actions/slots.ts`.
- **Dependencies**: Drizzle ORM PostgreSQL transactions with SQL intervals.

### 5. In-App Direct Messaging
- **Purpose**: Enable rich 1:1 direct chat between clients and coaches with threading and reactions.
- **Business Value**: Enables direct client-trainer feedback and relationship building within the platform.
- **Main Components**: `app/(app)/messages/page.tsx`, `app/(app)/messages/chat-inbox.tsx`.
- **Related APIs**: `getStreamToken` in `app/actions/stream.ts`, `openDm` in `lib/stream.ts`.
- **Dependencies**: `stream-chat`, `stream-chat-react`, `@stream-io/node-sdk`.

### 6. Live 1:1 Video Consultations
- **Purpose**: Host secure, browser-based WebRTC video consultations tied directly to booked calendar slots.
- **Business Value**: Replaces external video conference tools with an integrated branded experience.
- **Main Components**: `app/call/[slotId]/page.tsx`, `app/call/[slotId]/call-room.tsx`.
- **Related APIs**: `getStreamToken` in `app/actions/stream.ts`, `ensureSessionCall` in `lib/stream.ts`.
- **Dependencies**: `@stream-io/video-react-sdk`, `@stream-io/node-sdk`.

### 7. Client Reviews & Rating System
- **Purpose**: Permit verified clients who have completed a purchase to submit 1–5 star ratings and reviews.
- **Business Value**: Builds social proof and platform integrity through verified feedback.
- **Main Components**: `app/(site)/trainers/[id]/page.tsx`, `components/rating.tsx`.
- **Related APIs**: `submitReview` in `app/actions/reviews.ts`.
- **Dependencies**: Drizzle ORM with `onConflictDoUpdate`.

### 8. Trainer Analytics & Live Dashboard
- **Purpose**: Display gross revenue metrics, total sales count, upcoming appointments, client rosters, and a real-time sales feed.
- **Business Value**: Provides trainers with actionable business intelligence without page refreshes.
- **Main Components**: `app/dashboard/page.tsx`, `components/live-sales.tsx`, `app/dashboard/clients/page.tsx`.
- **Related APIs**: `loadSales` in `lib/realtime.ts`, `POST /api/live/sales`.
- **Dependencies**: `@neon/realtime`, `@neon/realtime-react`.

### 9. User Onboarding & Role Assignment
- **Purpose**: Direct newly registered users to specify their role (Client or Trainer) and collect trainer profiles (bio, specialties, hourly rate).
- **Business Value**: Ensures proper authorization gates and populates directory listings.
- **Main Components**: `app/(app)/onboarding/page.tsx`, `app/(app)/onboarding/onboarding-form.tsx`.
- **Related APIs**: `createProfile` in `app/actions/profile.ts`.
- **Dependencies**: Zod schema validation, Drizzle ORM.

---

# 7 UI Screenshots

[INSERT_HOME_PAGE_SCREENSHOT]

[INSERT_TRAINERS_DIRECTORY_PAGE_SCREENSHOT]

[INSERT_TRAINER_PROFILE_PAGE_SCREENSHOT]

[INSERT_PROGRAM_DETAILS_PLAYER_PAGE_SCREENSHOT]

[INSERT_CLIENT_LIBRARY_PAGE_SCREENSHOT]

[INSERT_ONBOARDING_PAGE_SCREENSHOT]

[INSERT_MESSAGES_INBOX_PAGE_SCREENSHOT]

[INSERT_VIDEO_CALL_ROOM_PAGE_SCREENSHOT]

[INSERT_TRAINER_DASHBOARD_OVERVIEW_PAGE_SCREENSHOT]

[INSERT_TRAINER_DASHBOARD_PROGRAMS_PAGE_SCREENSHOT]

[INSERT_TRAINER_DASHBOARD_PROGRAM_EDITOR_PAGE_SCREENSHOT]

[INSERT_TRAINER_DASHBOARD_SLOTS_PAGE_SCREENSHOT]

[INSERT_TRAINER_DASHBOARD_CLIENTS_PAGE_SCREENSHOT]

[INSERT_AUTH_PAGE_SCREENSHOT]

---

# 8 Database Design

The database is built on PostgreSQL via Neon, defined in `lib/db/schema.ts` and managed with Drizzle ORM.

### Custom PostgreSQL Enums
- **`role`**: `['client', 'trainer']`
- **`slot_status`**: `['open', 'held', 'booked']`
- **`purchase_kind`**: `['program', 'session']`

---

### Entity: `profiles`
- **Purpose**: Stores role-specific metadata and profile attributes linked to the Neon Auth user account.
- **Primary Key**: `user_id` (UUID)
- **Fields**:
  - `user_id` (`uuid`): Primary key matching `neon_auth.user.id`.
  - `role` (`role` enum): User role (`client` or `trainer`). NOT NULL.
  - `name` (`text`): Full name. NOT NULL.
  - `avatar_url` (`text`): ImageKit file path or external image URL.
  - `headline` (`text`): Trainer headline (nullable for clients).
  - `bio` (`text`): Extended biography (nullable for clients).
  - `specialties` (`text[]`): Array of discipline slugs (nullable for clients).
  - `session_price_cents` (`integer`): Hourly session cost in integer cents (nullable for clients).
  - `created_at` (`timestamp with time zone`): Record creation timestamp. Default `now()`. NOT NULL.

---

### Entity: `programs`
- **Purpose**: Represents digital workout programs published by trainers.
- **Primary Key**: `id` (UUID, default random)
- **Foreign Keys**:
  - `trainer_id` references `profiles.user_id` with `ON DELETE CASCADE`.
- **Fields**:
  - `id` (`uuid`): Primary key.
  - `trainer_id` (`uuid`): Trainer owner ID. NOT NULL.
  - `title` (`text`): Program title. NOT NULL.
  - `description` (`text`): Program description. NOT NULL.
  - `cover_url` (`text`): ImageKit cover asset path.
  - `price_cents` (`integer`): Price in integer cents. NOT NULL.
  - `published` (`boolean`): Visibility flag. Default `false`. NOT NULL.
  - `created_at` (`timestamp with time zone`): Default `now()`. NOT NULL.

---

### Entity: `lessons`
- **Purpose**: Individual ordered video chapters belonging to a workout program. Position 1 serves as the free preview.
- **Primary Key**: `id` (UUID, default random)
- **Foreign Keys**:
  - `program_id` references `programs.id` with `ON DELETE CASCADE`.
- **Constraints**:
  - `UNIQUE (program_id, position)`: Ensures deterministic sequential ordering without collisions.
- **Fields**:
  - `id` (`uuid`): Primary key.
  - `program_id` (`uuid`): Parent program reference. NOT NULL.
  - `title` (`text`): Lesson title. NOT NULL.
  - `position` (`integer`): Display order sequence index. NOT NULL.
  - `video_path` (`text`): Private ImageKit storage path.

---

### Entity: `slots`
- **Purpose**: 60-minute trainer availability blocks and confirmed client bookings.
- **Primary Key**: `id` (UUID, default random)
- **Foreign Keys**:
  - `trainer_id` references `profiles.user_id` with `ON DELETE CASCADE`.
  - `client_id` references `profiles.user_id` with `ON DELETE SET NULL`.
- **Fields**:
  - `id` (`uuid`): Primary key.
  - `trainer_id` (`uuid`): Offering trainer ID. NOT NULL.
  - `starts_at` (`timestamp with time zone`): Slot start time. NOT NULL.
  - `status` (`slot_status` enum): Status (`open`, `held`, `booked`). Default `'open'`. NOT NULL.
  - `client_id` (`uuid`): Booking client ID (null while open).
  - `held_until` (`timestamp with time zone`): Temporary reservation expiration timestamp.

---

### Entity: `purchases`
- **Purpose**: Immutable ledger recording fulfilled program purchases and session bookings.
- **Primary Key**: `id` (UUID, default random)
- **Foreign Keys**:
  - `client_id` references `profiles.user_id` with `ON DELETE CASCADE`.
  - `trainer_id` references `profiles.user_id` with `ON DELETE CASCADE`.
  - `program_id` references `programs.id` (nullable for sessions).
  - `slot_id` references `slots.id` (nullable for programs).
- **Constraints & Indexes**:
  - `UNIQUE (stripe_session_id)`: Enforces idempotent checkout fulfillment.
  - Partial Unique Index `one_purchase_per_program`: `UNIQUE (client_id, program_id) WHERE kind = 'program'`.
- **Fields**:
  - `id` (`uuid`): Primary key.
  - `client_id` (`uuid`): Purchasing user ID. NOT NULL.
  - `trainer_id` (`uuid`): Recipient trainer ID. NOT NULL.
  - `kind` (`purchase_kind` enum): Type (`program` or `session`). NOT NULL.
  - `program_id` (`uuid`): Purchased program reference.
  - `slot_id` (`uuid`): Booked slot reference.
  - `amount_cents` (`integer`): Gross transaction amount in cents. NOT NULL.
  - `stripe_session_id` (`text`): Unique checkout session identifier. NOT NULL.
  - `created_at` (`timestamp with time zone`): Transaction timestamp. Default `now()`. NOT NULL.

---

### Entity: `reviews`
- **Purpose**: Client reviews and star ratings for coaches.
- **Primary Key**: `id` (UUID, default random)
- **Foreign Keys**:
  - `trainer_id` references `profiles.user_id` with `ON DELETE CASCADE`.
  - `client_id` references `profiles.user_id` with `ON DELETE CASCADE`.
- **Constraints**:
  - `UNIQUE (trainer_id, client_id)`: Restricts clients to one editable review per trainer.
  - `CHECK (rating BETWEEN 1 AND 5)`: Validates integer rating scale.
- **Fields**:
  - `id` (`uuid`): Primary key.
  - `trainer_id` (`uuid`): Reviewed coach ID. NOT NULL.
  - `client_id` (`uuid`): Review author ID. NOT NULL.
  - `rating` (`integer`): Rating value from 1 to 5. NOT NULL.
  - `comment` (`text`): Optional text feedback.
  - `created_at` (`timestamp with time zone`): Default `now()`. NOT NULL.

---

# 9 Entity Relationship Diagram

```text
+------------------------------------+
|             profiles               |
+------------------------------------+
| PK  user_id             uuid       |
|     role                role       |
|     name                text       |
|     avatar_url          text       |
|     headline            text       |
|     bio                 text       |
|     specialties         text[]     |
|     session_price_cents integer    |
|     created_at          timestamptz|
+-----------------+------------------+
                  |
         +--------+--------------------------+
         | 1                                 | 1
         |                                   |
         | N                                 | N
+--------v---------------------------+  +----v-------------------------------+
|             programs               |  |              slots                 |
+------------------------------------+  +------------------------------------+
| PK  id                  uuid       |  | PK  id                  uuid       |
| FK  trainer_id          uuid       |  | FK  trainer_id          uuid       |
|     title               text       |  |     starts_at           timestamptz|
|     description         text       |  |     status              slot_status|
|     cover_url           text       |  | FK  client_id           uuid       |
|     price_cents         integer    |  |     held_until          timestamptz|
|     published           boolean    |  +-----------------+------------------+
|     created_at          timestamptz|                    |
+-----------------+------------------+                    |
                  |                                       |
                  | 1                                     |
                  |                                       |
                  | N                                     |
+-----------------v------------------+                    |
|             lessons                |                    |
+------------------------------------+                    |
| PK  id                  uuid       |                    |
| FK  program_id          uuid       |                    |
|     title               text       |                    |
|     position            integer    |                    |
|     video_path          text       |                    |
+------------------------------------+                    |
                                                          |
         +------------------------------------------------+
         |
         | N
+--------v---------------------------+  +------------------------------------+
|            purchases               |  |             reviews                |
+------------------------------------+  +------------------------------------+
| PK  id                  uuid       |  | PK  id                  uuid       |
| FK  client_id           uuid       |  | FK  trainer_id          uuid       |
| FK  trainer_id          uuid       |  | FK  client_id           uuid       |
|     kind                purch_kind |  |     rating              integer    |
| FK  program_id          uuid       |  |     comment             text       |
| FK  slot_id             uuid       |  |     created_at          timestamptz|
|     amount_cents        integer    |  +------------------------------------+
|     stripe_session_id   text (UQ)  |
|     created_at          timestamptz|
+------------------------------------+
```

---

# 10 Security

### Authentication & Authorization Gates
- **Optimistic Edge Proxy (`proxy.ts`)**: Next.js middleware verifies authentication cookies for protected routes (`/onboarding`, `/library`, `/messages`, `/call/:path*`, `/dashboard/:path*`), redirecting unauthenticated requests to `/auth/sign-in`.
- **Layout & Action Role Checks**: Server Actions and layouts strictly re-validate session state via `getUser()`, `requireUser()`, `requireProfile()`, and `requireTrainer()` in `lib/auth.ts`. A client attempting to access `/dashboard` routes is redirected to `/library`.

### Domain Logic & Access Verification (`lib/access.ts`)
- **Video Lesson Access (`canWatchLesson`)**: Pure logic verifying that a lesson is either the first preview lesson (`position === 1`), owned by the viewing trainer (`isOwner`), or paid for by the viewing client (`hasPurchase`).
- **Review Permissions (`canReview`)**: Strictly enforces that self-reviews are forbidden (`!isSelf`) and that the reviewer has a confirmed purchase record with that trainer.
- **Messaging Permissions (`canMessage`)**: Ensures users cannot DM themselves and enforces that at least one conversation participant holds the `trainer` role.

### Media & Storage Security (`lib/imagekit.ts`)
- **HMAC-SHA1 URL Signatures**: Private lesson videos are never served publicly. The server action `signLessonUrl` generates time-limited (1-hour) HMAC-SHA1 signatures.
- **Path Escape Prevention (`isFileIn`, `isDerivedUrl`)**: Path traversal attacks (`../`, `%2e%2e/`, absolute URLs) are blocked when uploading or generating asset URLs.

### Input Validation
- All Server Actions parse `FormData` using Zod schemas (`app/actions/*.ts`), ensuring type safety, bounds checking (e.g. session price between $10 and $1,000, review ratings between 1 and 5), and string sanitization.

### Missing Security Mechanisms
- **Rate Limiting**: Application-level IP or user rate limiting is not implemented in source code; relies on upstream proxy/WAF.
- **CSRF Tokens**: Relies on Next.js Server Action internal boundary protection and SameSite cookie policies.
- **Stripe Webhook Signature Verification**: Stripe webhook endpoint is not yet connected.

---

# 11 Authentication

### Authentication Architecture
Identity, credential storage, and password encryption are managed through Neon Auth (`@neondatabase/auth`), an embedded Better Auth service integrated with Neon Postgres.

### Authentication Flow

```text
+------------------+         +--------------------+         +--------------------+
|   User Browser   |         |   Next.js Server   |         |     Neon Auth      |
+--------+---------+         +---------+----------+         +---------+----------+
         |                             |                              |
         |  1. GET /auth/sign-in       |                              |
         +---------------------------->|                              |
         |  2. Render AuthView UI      |                              |
         |<----------------------------+                              |
         |                                                            |
         |  3. Submit Email/Password or OAuth                         |
         +----------------------------------------------------------->|
         |  4. Validate Credentials & Set Encrypted Session Cookie    |
         |<-----------------------------------------------------------+
         |                                                            |
         |  5. Navigate to Protected Route (/dashboard)               |
         +---------------------------->|                              |
         |                             |  6. Verify Session Cookie    |
         |                             +----------------------------->|
         |                             |  7. Return Session Data      |
         |                             |<-----------------------------+
         |                             |                              |
         |                             |  8. Check Profile in DB      |
         |                             |     (Redirect if incomplete) |
         |  9. Render Dashboard Page   |                              |
         |<----------------------------+                              |
```

### Session & Token Strategy
- **Session Strategy**: Encrypted cookie-based sessions managed by Neon Auth using `NEON_AUTH_COOKIE_SECRET`.
- **Stream Tokens**: Ephemeral user tokens generated server-side via `@stream-io/node-sdk` using `STREAM_API_SECRET` with 1-hour validity (`validity_in_seconds: 3600`).
- **Logout Flow**: Handled by Neon Auth client SDK, clearing the session cookie and redirecting to the landing page.

---

# 12 API Documentation

### Route Handlers Summary Table
| Method | Endpoint | Auth Required | Description |
|---|---|---|---|
| `GET, POST, ...` | `/api/auth/[...path]` | Varies | Catch-all handler dispatching to Neon Auth engine |
| `POST` | `/api/live/sales` | Yes (Trainer) | Renews sealed Neon Realtime query for trainer sales feed |
| `POST` | `/api/live/slots` | No (Public) | Renews sealed Neon Realtime query for trainer open slots |

---

### Detailed Endpoint Specifications

#### 1. Live Sales Query Renewal
- **Endpoint**: `POST /api/live/sales`
- **Purpose**: Re-seals the trainer's sales feed query before the 60-second Neon Realtime query signature expires.
- **Authentication**: Required. Session user must match payload `userId` and hold the `trainer` role.
- **Request Body**:
  ```json
  {
    "userId": "d3b07384-d113-4621-9e8c-85194d3c34a2"
  }
  ```
- **Responses**:
  - `200 OK`:
    ```json
    {
      "sealedQuery": "ey..."
    }
    ```
  - `401 Unauthorized`:
    ```json
    {
      "error": "UNAUTHENTICATED"
    }
    ```

#### 2. Live Slots Query Renewal
- **Endpoint**: `POST /api/live/slots`
- **Purpose**: Re-seals the public slot query for a coach's booking grid.
- **Authentication**: None.
- **Request Body**:
  ```json
  {
    "trainerId": "d3b07384-d113-4621-9e8c-85194d3c34a2",
    "since": "2026-11-04T10:00:00.000Z"
  }
  ```
- **Responses**:
  - `200 OK`:
    ```json
    {
      "sealedQuery": "ey..."
    }
    ```
  - `400 Bad Request`:
    ```json
    {
      "error": "BAD_REQUEST"
    }
    ```

---

### Server Actions (RPC Endpoints)

#### Checkout Actions (`app/actions/checkout.ts`)
- **`buyProgram(programId: string)`**: Validates program existence and publication state; verifies buyer is not the program owner; creates a purchase record with an idempotent session ID (`dev_${randomUUID()}`).
- **`bookSlot(slotId: string)`**: Executes atomic PostgreSQL `UPDATE` setting `status = 'held'` and `held_until = now() + 30 minutes`; fulfills purchase and transitions slot to `booked`.

#### Media Actions (`app/actions/media.ts`)
- **`getUploadAuth()`**: Authenticates user and returns ImageKit upload credentials (token, expiry, signature).
- **`signLessonUrl(lessonId: string, url: string)`**: Verifies user access rights (`canWatchLesson`) and generates signed ImageKit URL valid for 1 hour.

#### Program Actions (`app/actions/programs.ts`)
- **`createProgram(_state, formData)`**: Creates draft program for authenticated trainer.
- **`updateProgram(programId, _state, formData)`**: Updates program metadata and publication state (requires >= 1 lesson to publish).
- **`deleteProgram(programId)`**: Deletes unpublished programs without purchase records.
- **`setCover(programId, path)`**: Updates cover image path.
- **`addLesson(programId, _state, formData)`**: Adds sequential lesson to program.

#### Slot Actions (`app/actions/slots.ts`)
- **`addSlot(_state, formData)`**: Adds future 60-minute calendar slot ensuring no 1-hour overlap.
- **`deleteSlot(slotId)`**: Deletes unbooked slot.

#### Profile & Review Actions
- **`createProfile(_state, formData)`**: Completes onboarding role setup.
- **`submitReview(trainerId, _state, formData)`**: Inserts or updates verified client review.
- **`getStreamToken()`**: Generates authenticated Stream user token.

---

# 13 Third-party Services

| Service | Integration Scope | Configuration / Authentication |
|---|---|---|
| **Neon Postgres** | Database storage, pooling, and serverless compute | `DATABASE_URL`, `DATABASE_URL_UNPOOLED` |
| **Neon Auth** | User authentication, identity management, and sessions | `NEON_AUTH_BASE_URL`, `NEON_AUTH_COOKIE_SECRET` |
| **Neon Realtime** | Sealed live query engine for sales and calendar feeds | `NEON_REALTIME_URL`, `NEON_REALTIME_SECRET` |
| **Stream Chat** | 1:1 direct messaging threads and channel state | `NEXT_PUBLIC_STREAM_API_KEY`, `STREAM_API_SECRET` |
| **Stream Video** | WebRTC video consultation rooms and media controls | `NEXT_PUBLIC_STREAM_API_KEY`, `STREAM_API_SECRET` |
| **ImageKit** | Media storage, image transformations, and signed video delivery | `NEXT_PUBLIC_IMAGEKIT_URL_ENDPOINT`, `IMAGEKIT_PRIVATE_KEY`, `NEXT_PUBLIC_IMAGEKIT_PUBLIC_KEY` |
| **Sentry** | Observability, error monitoring, session replay, and tracing | `NEXT_PUBLIC_SENTRY_DSN`, `SENTRY_AUTH_TOKEN`, `SENTRY_ORG`, `SENTRY_PROJECT` |
| **Stripe** | Planned for live payment processing (currently fulfilled via mock transactions) | `STRIPE_SECRET_KEY`, `STRIPE_WEBHOOK_SECRET` |

---

# 14 Environment Variables

| Variable | Required | Purpose |
|---|---|---|
| `DATABASE_URL` | Yes | Pooled PostgreSQL connection string for application runtime |
| `DATABASE_URL_UNPOOLED` | Yes | Direct PostgreSQL connection string for Drizzle Kit schema migrations |
| `NEON_AUTH_BASE_URL` | Yes | Base URL endpoint for Neon Auth service |
| `NEON_AUTH_COOKIE_SECRET` | Yes | Secret encryption key for session cookies (minimum 32 characters) |
| `NEON_REALTIME_URL` | Yes | Neon Realtime WebSocket connection endpoint |
| `NEON_REALTIME_SECRET` | Yes | Secret key used to cryptographically seal realtime database queries |
| `NEXT_PUBLIC_APP_URL` | Yes | Base application origin URL (e.g. `http://localhost:3000`) |
| `NEXT_PUBLIC_STREAM_API_KEY` | Yes | Public Stream API key for client-side chat and video SDKs |
| `STREAM_API_SECRET` | Yes | Server-side Stream API secret for generating user tokens |
| `NEXT_PUBLIC_IMAGEKIT_URL_ENDPOINT` | Yes | Base CDN URL for ImageKit asset delivery |
| `NEXT_PUBLIC_IMAGEKIT_PUBLIC_KEY` | Yes | Public ImageKit identifier used during browser file uploads |
| `IMAGEKIT_PRIVATE_KEY` | Yes | Private ImageKit key used for HMAC-SHA1 signature generation |
| `STRIPE_SECRET_KEY` | Optional | Stripe API secret key for payment processing |
| `STRIPE_WEBHOOK_SECRET` | Optional | Stripe webhook signature secret |
| `NEXT_PUBLIC_SENTRY_DSN` | Optional | Client/Server Sentry Data Source Name for error monitoring |
| `SENTRY_AUTH_TOKEN` | Optional | Build-time authentication token for uploading source maps to Sentry |
| `SENTRY_ORG` | Optional | Sentry organization identifier |
| `SENTRY_PROJECT` | Optional | Sentry project identifier |

---

# 15 Major Dependencies

### Runtime Dependencies
- **`next` (16.4.3)**: Application foundation providing App Router, Turbopack, and Server Actions.
- **`react` & `react-dom` (19.3.0)**: Modern component rendering with support for React Server Components.
- **`drizzle-orm` (0.45.4)**: Type-safe SQL query construction and schema management.
- **`pg` (8.23.1)**: Persistent PostgreSQL TCP client pool.
- **`@neondatabase/auth` (0.7.0-beta)**: Complete managed identity and authentication client/server integration.
- **`@neon/realtime` & `@neon/realtime-react` (0.1.2 / 0.1.1)**: Ephemeral reactive database query subscriptions.
- **`@stream-io/node-sdk` (0.8.15)**: Server SDK managing Stream users, channels, and video call tokens.
- **`stream-chat` & `stream-chat-react` (9.54.3 / 14.14.1)**: Client chat client and prebuilt messaging interface.
- **`@stream-io/video-react-sdk` (1.44.1)**: WebRTC video calling components and participant state management.
- **`@imagekit/next` & `@imagekit/video-player` (2.1.6 / 1.0.0-beta.6)**: Media optimization and signed HLS video streaming.
- **`@sentry/nextjs` (11.10.0)**: Distributed tracing, logging, and error telemetry.
- **`zod` (4.6.7)**: Runtime data validation across Server Actions and API boundaries.

### Development Dependencies
- **`drizzle-kit` (0.31.11)**: Schema migration generator and database inspection CLI.
- **`tsx` (4.23.15)**: Execution environment for TypeScript database seeding scripts.
- **`typescript` (5.x)**: Static type safety.
- **`tailwindcss` (4.x)**: Utility styling engine.

---

# 16 Installation Guide

### Prerequisites
- Node.js version 20.x or 22.x LTS installed.
- PostgreSQL database instance (Neon account recommended).
- Accounts configured for ImageKit and Stream.

### Step 1: Clone Repository
```bash
git clone <repository-url>
cd repwise-master
```

### Step 2: Install Dependencies
```bash
npm install
```

### Step 3: Configure Environment Variables
Create a `.env.local` file in the project root:
```bash
cp .env.example .env.local
```
Fill in the database connection strings, auth secrets, and API credentials.

### Step 4: Synchronize Database Schema
Push the Drizzle schema directly to your database instance:
```bash
npx drizzle-kit push
```

### Step 5: Seed Demo Fixtures (Optional)
Populate sample trainers, workout programs, and calendar slots:
```bash
npm run db:seed
```

### Step 6: Launch Development Server
```bash
npm run dev
```
*(On Windows environments where inline environment flags fail, run:)*
```bash
npx next dev
```
Open [http://localhost:3000](http://localhost:3000) in your browser.

---

# 17 Deployment

### Deployment Architecture
- **Runtime Target**: Persistent Node.js long-lived server (e.g. Cloudways Velocity or standard containerized Node host) using `next start`.
- **Database**: Cloud-hosted Neon Serverless PostgreSQL with connection pooling.
- **Static & Media Assets**: Static bundles served via Next.js; user avatars and private curriculum videos stored and delivered globally via ImageKit CDN.

```text
+-------------------+        +--------------------+        +---------------------+
|   Source Repo     |        |   Build Pipeline   |        |   Production Host   |
|   (GitHub)        +------->|   `next build`     +------->| (Node 22 LTS)       |
+-------------------+        |   Upload Sentry    |        | `next start`        |
                             |   Source Maps      |        +----------+----------+
                             +--------------------+                   |
                                                                      |
                             +--------------------+                   |
                             |   Neon Postgres    |<------------------+
                             |   (Pooled DB)      |                   |
                             +--------------------+                   |
                                                                      |
                             +--------------------+                   |
                             |  ImageKit / Stream |<------------------+
                             |  Cloud Services    |
                             +--------------------+
```

### Production Build & Execution
```bash
npm run build
npm run start
```

---

# 18 Request Lifecycle

```text
+------------------+
|   Client Device  |
+--------+---------+
         |
         | 1. HTTP Request (e.g. GET /dashboard)
         v
+------------------------------------+
|     Next.js Edge Proxy Layer       |
|            (proxy.ts)              |
|  - Validates Neon Auth Cookie      |
|  - Redirects unauthenticated users |
+--------+---------------------------+
         |
         | 2. Passes Optimistic Check
         v
+------------------------------------+
|        Next.js App Router          |
|    Layouts & Server Components     |
|  - layout.tsx enforces role gates  |
|  - Page initiates async queries    |
+--------+---------------------------+
         |
         | 3. Direct SQL Read
         v
+------------------------------------+
|           Data Access              |
|     (lib/queries.ts via Drizzle)   |
|  - Reads from PostgreSQL Pool      |
+--------+---------------------------+
         |
         | 4. Database Records
         v
+------------------------------------+
|        HTML Rendering Engine       |
|  - Renders UI inside <Suspense>    |
|  - Streams response with skeletons |
+--------+---------------------------+
         |
         | 5. Interactive Client Hydration
         v
+------------------------------------+
|     Client-Side Realtime Feed      |
|  - Subscribes to Neon Realtime WS  |
|  - Receives live updates           |
+------------------------------------+
```

---

# 19 Performance

### Implemented Optimizations
- **Persistent Connection Pooling**: Uses `node-postgres` (`pg`) pool with `sslmode=verify-full` rather than cold-start HTTP connection overhead, optimizing throughput on long-lived server instances.
- **Component Caching & Suspense**: Enabled `cacheComponents: true` in `next.config.ts`. All dynamic data reads are wrapped in `<Suspense>` boundaries with skeleton fallbacks, preventing blocking page transitions.
- **Sealed Realtime Queries**: Subscriptions to sales and slot calendars use server-sealed SQL queries, executing reactive state reconciliation without continuous polling.
- **On-Demand URL Signing**: Video signing uses local HMAC-SHA1 computations without external network roundtrips to ImageKit.

### Potential Bottlenecks
- Single-instance Node server memory utilization under high concurrency of WebSockets and live query subscriptions.
- Database connection limits if compute scaling disconnects idle clients.

### Scalability Considerations
- Heavy media assets (images, video streams) are offloaded to ImageKit CDN edge caches.
- Realtime signaling and WebRTC media streams are handled by Stream infrastructure rather than the core application server.

---

# 20 Security Review

### Current Security Strengths
- **Least-Privilege Database Queries**: All trainer program and lesson mutations strictly append `WHERE trainer_id = me.userId`, preventing horizontal privilege escalation.
- **Isolated File Namespaces**: ImageKit upload and signing logic checks that file paths strictly match `/avatars`, `/covers/{programId}`, or `/lessons/{programId}`.
- **Atomic Slot Leases**: Prevents double-booking via database-level atomic conditional updates.

### Current Risks
- **ImageKit Upload Signatures**: ImageKit v1 upload signatures do not strictly pin target folder paths on the server; client could upload arbitrary files if credentials are intercepted.
- **Stream Call Admission**: Call types must enforce member-only join rules in Stream dashboard to prevent unauthorized joiners.

### Recommended Improvements
- Implement Upload API v2 using server-generated JWTs with enforced target folder paths and file size constraints.
- Integrate application-level IP rate limiting on public search and authentication endpoints.

---

# 21 Challenges & Engineering Decisions

### 1. Pure Functional Access Control (`lib/access.ts`)
- **Decision**: Decoupled access authorization rules (`canWatchLesson`, `canReview`, `canMessage`, `isFileIn`, `isDerivedUrl`) from database queries and HTTP context into pure functions.
- **Rationale**: Enables deterministic, sub-millisecond unit testing via `node:test` without spinning up mock databases or auth servers.

### 2. Dual-Layer Route Protection
- **Decision**: Implemented an optimistic edge middleware gate (`proxy.ts`) paired with strict layout and Server Action authorization checks.
- **Rationale**: Minimizes redirect latency for unauthorized visitors while guaranteeing defense-in-depth against direct Server Action RPC attacks.

### 3. Atomic Database Slot Holds
- **Decision**: Implemented slot reservations via a single conditional SQL update (`UPDATE slots SET status='held', held_until=now() + '30 min' WHERE ...`).
- **Rationale**: Eliminates distributed locking overhead (e.g. Redis locks) while preventing race conditions when multiple clients attempt to reserve the same time slot.

---

# 22 Future Improvements

- **Live Stripe Webhook Integration**: Implement `app/api/webhooks/stripe/route.ts` to replace development checkout fulfillment with live card payments and webhook reconciliation.
- **Calendar Integration**: Export booked sessions to `.ics` formats and synchronize with Google Calendar / Apple Calendar.
- **Client Workout Progression**: Track completed program lessons and log personal records per workout.
- **Direct Trainer Payouts**: Implement Stripe Connect to disburse split payments and trainer earnings automatically.

---

# 23 Developer Notes

### Maintainability
- Keep data-fetching logic centralized in `lib/queries.ts` and mutations in `app/actions/*.ts`.
- Avoid mixing client state with server data fetching; let Server Actions revalidate paths via `refresh()`.

### Technical Debt
- In `app/actions/checkout.ts`, program and slot checkout fulfillment currently uses mock transaction IDs (`dev_${randomUUID()}`). This must be wired to Stripe Checkout sessions and verified webhooks prior to production launch.

### Code Consistency
- All monetary amounts must be stored and computed as integer cents (`amountCents`, `sessionPriceCents`, `priceCents`).
- All timestamps must be handled in UTC with timezone awareness (`timestamptz`) and converted to viewer local time only on the client via `components/local-time.tsx`.

---
Written by Yash Lagare

