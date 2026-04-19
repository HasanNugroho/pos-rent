# Product Requirements Document (PRD)

## POS Sewa — Point of Sale for Rental Business

> **Version**: 2.0 | **Status**: UI Complete, Backend Pending
> **Author**: Product Team | **Date**: April 19, 2026

---

## 1. Product Vision

**POS Sewa** is a multi-tenant SaaS platform that enables rental businesses (e.g., shoe rental, equipment rental) to manage their entire operation — from inventory and bookings to payments and customer relationships — through a modern, intuitive web interface.

### Problem Statement

Small-to-medium rental businesses in Indonesia currently rely on manual processes (spreadsheets, WhatsApp, paper receipts) to manage their rental operations, leading to:

- Lost bookings and double-booking conflicts
- Inaccurate inventory tracking (items not returned, items in maintenance)
- No visibility into revenue, utilization, or customer behavior
- Difficulty scaling to multiple outlets/branches

### Solution

A cloud-based POS system purpose-built for rental businesses that provides:

- **Real-time inventory** with unit-level tracking and availability calendar
- **End-to-end booking** with DP (down payment) support and automated status management
- **Multi-tenant architecture** allowing a single platform to serve hundreds of independent businesses
- **Subscription-based pricing** with tiered plans and add-ons

---

## 2. Target Users

### Primary Users

| Persona | Role | Needs |
|---------|------|-------|
| **Outlet Owner** | Tenant owner / business owner | Full control over their outlet: products, bookings, staff, reports, billing |
| **Manager** | Outlet manager | Day-to-day operations without billing/user management access |
| **Kasir / Staff** | POS operator | Create bookings, process payments, manage customers |
| **Inventory Staff** | Warehouse/inventory | Product management, maintenance scheduling |

### Secondary Users

| Persona | Role | Needs |
|---------|------|-------|
| **Super Admin** | Platform operator | Manage all tenants, monitor system health, configure integrations |
| **End Customer** | Renter / consumer | Browse catalog, check availability, inquire via WhatsApp |

---

## 3. System Architecture (High-Level)

```
┌──────────────────────────────────────────────┐
│              SUPER ADMIN LAYER               │
│  (Multi-tenant management, billing, config)  │
├──────────────────────────────────────────────┤
│            TENANT / OUTLET LAYER             │
│  (Products, Bookings, Customers, Staff, etc) │
├──────────────────────────────────────────────┤
│             PUBLIC LAYER                     │
│  (Catalog, Availability Check, WhatsApp)     │
└──────────────────────────────────────────────┘
```

### Two-Level System:

1. **Super Admin Level** — Platform-wide management (outlets, users, subscriptions, system config)
2. **Outlet/Tenant Level** — Per-outlet operations (products, bookings, payments, staff)
3. **Public Level** — No-auth pages (product catalog, availability check)

---

## 4. Feature Scope

### 4.1 Core Features (Tenant Level)

| Module | Status | Priority | Description |
|--------|--------|----------|-------------|
| Authentication | ✅ UI | P0 | Login, Register, Role-based routing |
| Dashboard | ✅ UI | P0 | Revenue stats, charts, recent bookings, quick actions |
| Products | ✅ UI | P0 | CRUD products, categories, stock with unit-level tracking |
| Availability Calendar | ✅ UI | P0 | Visual per-product calendar with color-coded availability |
| Bookings | ✅ UI | P0 | 3-step booking flow, DP/full payment, pelunasan system |
| Customers | ✅ UI | P0 | CRM with customer profiles, booking history |
| Maintenance | ✅ UI | P1 | Unit-level maintenance: schedule, progress, completion with receipts |
| Staff Management | ✅ UI | P1 | CRUD staff, role assignment, granular permissions |
| Reports | ✅ UI | P1 | Revenue, rental, product, customer reports with charts & export |
| Settings | ✅ UI | P1 | Profile, business info, payment methods, notifications, outlet config |
| Outlet Management | ✅ UI | P1 | Owner multi-branch management: add/edit/switch outlets |
| Subscription (User) | ✅ UI | P1 | Current plan view, add-on purchase, billing history with invoice download |

### 4.2 Public Features

| Module | Status | Priority | Description |
|--------|--------|----------|-------------|
| Product Catalog | ✅ UI | P1 | Public browsable catalog with search, filter, WhatsApp inquiry |
| Availability Check | ✅ UI | P1 | Date picker modal to check product availability |

### 4.3 Super Admin Features

| Module | Status | Priority | Description |
|--------|--------|----------|-------------|
| Admin Dashboard | ✅ UI | P0 | System-wide KPIs + charts (revenue, outlets, subscriptions) |
| Manage Outlets | ✅ UI | P0 | CRUD tenants, status management, plan assignment |
| Manage Users | ✅ UI | P0 | Cross-tenant user management, suspension |
| Subscriptions Mgmt | ✅ UI | P1 | All tenant subscriptions, billing tracking, MRR |
| System Reports | ✅ UI | P1 | Revenue by outlet, user growth, subscription analytics |
| System Settings | ✅ UI | P1 | Global config, roles & permissions, pricing plans, communication templates |
| API & Integrations | ✅ UI | P1 | Payment gateway (Midtrans/Xendit), Storage (R2/S3), SMTP, Firebase, WhatsApp |

---

## 5. Subscription Model

### Pricing Tiers

| Plan | Price | Outlets | Users | Products |
|------|-------|---------|-------|----------|
| Free Trial | Rp 0 / 14 days | 1 | 5 | 50 |
| Basic | Rp 99.000/mo | 1 | 10 | 500 |
| Pro ⭐ | Rp 199.000/mo | 3 | Unlimited | 2,000 |
| Enterprise | Rp 499.000/mo | Unlimited | Unlimited | Unlimited |

### Available Add-ons

| Add-on | Price |
|--------|-------|
| +1 Additional Outlet | Rp 50.000/mo |
| +1 Additional User | Rp 15.000/mo |
| +500 Products | Rp 25.000/mo |

### Accessible Features (per plan)

- Multi-Branch Sync
- Inventory Tracking
- Advanced Reporting
- API Access & Webhooks
- Custom Domain

---

## 6. Technology Stack

| Layer | Technology |
|-------|------------|
| Runtime | **Bun** |
| Backend Framework | **NestJS** (modular structure) + TypeScript |
| ORM | **Prisma** (schema-first, type-safe) |
| Database | **PostgreSQL** |
| Cache / Session | **Redis** |
| Object Storage | **Cloudflare R2** |
| Authentication | JWT (access + refresh tokens) |
| Payment Gateway | Midtrans / Xendit |
| Push Notifications | Firebase Cloud Messaging |
| Email | SMTP (Mailgun / SES) |
| WhatsApp | Wablas / Fonnte |
| Frontend (Mockup) | HTML, TailwindCSS 3, Alpine.js 3.x, Chart.js |
| Frontend (Production) | Next.js / React |

---

## 7. Non-Functional Requirements

| Requirement | Target |
|-------------|--------|
| Response Time | < 200ms for API calls |
| Uptime | 99.9% |
| Data Isolation | Full tenant isolation via `tenant_id` or schema-per-tenant |
| Security | HTTPS, password hashing (bcrypt), input sanitization, 2FA for Super Admin |
| Scalability | Support 1,000+ tenants |
| Backup | Daily automated backups per tenant |
| Audit | Audit log for all admin and sensitive actions |

---

## 8. Success Metrics

| Metric | Target (6 months) |
|--------|-------------------|
| Registered Tenants | 100+ |
| Monthly Active Tenants | 50+ |
| MRR (Monthly Recurring Revenue) | Rp 15M+ |
| Avg Bookings per Tenant | 50+/month |
| Customer Satisfaction (NPS) | > 4.5/5 |

---

## 9. Risks & Mitigations

| Risk | Impact | Mitigation |
|------|--------|------------|
| Payment gateway downtime | Bookings blocked | Support multiple gateways (Midtrans + Xendit fallback) |
| Data breach | Trust loss | Encrypt sensitive data, regular security audits |
| Tenant data isolation failure | Cross-tenant data leak | Enforce tenant_id at ORM/query level + separate schemas |
| Low adoption | Revenue miss | Free trial, onboarding flow, WhatsApp support |

---

## 10. Document References

| Document | Purpose |
|----------|---------|
| `docs/02_FEATURE_AUTH.md` | Authentication & Multi-tenancy spec |
| `docs/03_FEATURE_PRODUCTS.md` | Product & inventory spec |
| `docs/04_FEATURE_BOOKINGS.md` | Booking & payment spec |
| `docs/05_FEATURE_MAINTENANCE.md` | Maintenance module spec |
| `docs/06_FEATURE_CUSTOMERS_STAFF.md` | Customer & staff spec |
| `docs/07_FEATURE_REPORTS.md` | Reporting & analytics spec |
| `docs/08_FEATURE_SUBSCRIPTIONS.md` | Subscription & billing spec |
| `docs/09_FEATURE_SUPERADMIN.md` | Super admin panel spec |
| `docs/10_FEATURE_SETTINGS.md` | Settings & configuration spec |
| `docs/11_TECHNICAL_ARCHITECTURE.md` | Technical architecture doc |
| `PERMISSIONS_LIST.md` | Full permission list & role mapping |
