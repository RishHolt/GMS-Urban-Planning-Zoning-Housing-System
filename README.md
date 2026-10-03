# Urban Planning System

A government service management system for **Urban Planning, Zoning & Housing**. It lets citizens submit and track applications, and gives department staff and administrators a place to review, process, and manage them.

> **Project status:** This is an **unfinished capstone project** and is no longer in active development. Some features may be incomplete.

**Live site:** [urbanplanning.goserveph.com](https://urbanplanning.goserveph.com/)

## Overview

The system is a multi-department platform. Each department runs its own module with its own database:

| Module | Department |
|--------|------------|
| **ZCS** | Zoning Clearance Section |
| **HBR** | Housing Beneficiary Registry |
| **SBR** | Subdivision & Building Review |
| **OMT** | Operations & Maintenance |
| **IPC** | Infrastructure Projects |

Citizens use the user-facing side to apply for services (such as zoning clearances, housing, and building/subdivision permits). Staff and admins use the admin side to review applications, run eligibility checks, assess fees, and manage records.

## Key Features

- **Role-based access:** `user`, `staff`, `admin`, and `superadmin` roles, with department assignment for staff and admins. Users are redirected to the right home (`/admin` or `/user`) after login.
- **Module-based permissions:** fine-grained control over which modules each role can access, on top of Laravel Policies.
- **Authentication:** email/password with OTP verification, plus Google sign-in (Laravel Socialite).
- **Interactive maps:** Leaflet-based mapping with drawing tools and geospatial operations (Turf.js) for zoning and land-related workflows.
- **Business-logic services:** eligibility checks, fee assessment, allocation, and notifications.
- **Real-time notifications:** Laravel Echo with Pusher.
- **PDF export:** generate documents with DomPDF.
- **Dashboards and charts:** built with Recharts.
- **In-browser AI:** TensorFlow.js, lazy-loaded as a separate chunk.

## Tech Stack

**Backend:** PHP 8.2+, Laravel 12, Inertia.js (Laravel adapter), Laravel Wayfinder (typed route helpers), MySQL (one database per department), Pest for testing

**Frontend:** React 19, TypeScript, Vite 7, Tailwind CSS 4, Leaflet / React-Leaflet, Turf.js, Recharts, TensorFlow.js

**Tooling:** ESLint, Prettier, Laravel Pint, GitHub Actions

## Architecture

The system uses **six separate MySQL databases**, one per concern:

| Connection | Purpose |
|------------|---------|
| `user_db` | Users, roles, authentication |
| `zcs_db` | Zoning Clearance Section |
| `hbr_db` | Housing Beneficiary Registry |
| `sbr_db` | Subdivision & Building Review |
| `omt_db` | Operations & Maintenance |
| `ipc_db` | Infrastructure Projects |

Migrations live in `database/migrations/{db_name}/`, and each model declares its own connection.

```
app/
  Http/Controllers/        # User-facing controllers
  Http/Controllers/Admin/  # Admin-facing controllers
  Services/                # Business logic (eligibility, fees, allocation, notifications)
  DataTransferObjects/     # Typed result objects
  Http/Resources/          # API response shaping
  Policies/                # Authorization
resources/js/
  pages/                   # Inertia pages (Admin, Housing, Applications, User)
  components/              # Shared UI (layouts, sidebar, map, status badges)
  hooks/ , types/          # Custom hooks and shared TypeScript types
```

## Getting Started

**Requirements:** PHP 8.2+, Composer, Node.js and npm, MySQL

```bash
# 1. Install dependencies, create .env, generate key, run migrations, build assets
composer setup

# 2. Start the PHP server, queue worker, and Vite dev server together
composer dev
```

Before running migrations, update `.env` with your MySQL credentials. The default `.env.example` uses SQLite, but the app expects the six department databases listed above.

### Useful Commands

```bash
composer test                       # Run the test suite
php artisan test --filter TestName  # Run a single test
./vendor/bin/pint                   # Fix PHP code style
npm run lint                        # ESLint (auto-fix)
npm run format                      # Prettier
npm run types                       # TypeScript type check
npm run build                       # Production build
```

> **Note:** The test suite is configured with in-memory SQLite for `user_db` and `zcs_db` only. Tests touching the other databases need manual setup.
