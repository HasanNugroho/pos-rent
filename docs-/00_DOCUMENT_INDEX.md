# POS Sewa — Documentation Index

> **Product**: POS Sewa (Point of Sale for Rental Business)
> **Version**: 2.0
> **Last Updated**: April 19, 2026

---

## 📂 Document List

| #  | Document | Path | Description |
|----|----------|------|-------------|
| 00 | **Document Index** | `docs/00_DOCUMENT_INDEX.md` | This file. Master index of all project documents. |
| 01 | **Product Requirements (PRD)** | `docs/01_PRD.md` | High-level product vision, target users, goals, and feature scope. |
| 02 | **Feature Spec — Authentication & Multi-Tenancy** | `docs/02_FEATURE_AUTH.md` | Login, registration, JWT, roles, tenant isolation, permission system. |
| 03 | **Feature Spec — Product & Inventory** | `docs/03_FEATURE_PRODUCTS.md` | Product CRUD, categories, custom fields, stock/unit tracking, public catalog. |
| 04 | **Feature Spec — Booking & Payments** | `docs/04_FEATURE_BOOKINGS.md` | 3-step booking flow, DP/full payment, pelunasan, calendar availability. |
| 05 | **Feature Spec — Maintenance** | `docs/05_FEATURE_MAINTENANCE.md` | Unit-level maintenance scheduling, progress tracking, completion with receipts. |
| 06 | **Feature Spec — Customers & Staff** | `docs/06_FEATURE_CUSTOMERS_STAFF.md` | Customer CRM, staff CRUD, role-based permissions, invitation flow. |
| 07 | **Feature Spec — Reporting & Analytics** | `docs/07_FEATURE_REPORTS.md` | Tenant-level and system-level reports, charts, export (PDF/Excel). |
| 08 | **Feature Spec — Subscription & Billing** | `docs/08_FEATURE_SUBSCRIPTIONS.md` | Pricing plans, add-ons, tenant subscription lifecycle, invoice history. |
| 09 | **Feature Spec — Super Admin Panel** | `docs/09_FEATURE_SUPERADMIN.md` | Multi-tenant management, system settings, API integrations, communication templates. |
| 10 | **Feature Spec — Settings & Configuration** | `docs/10_FEATURE_SETTINGS.md` | Tenant settings (profile, business, payment, notifications, outlet config). |
| 11 | **Technical Architecture** | `docs/11_TECHNICAL_ARCHITECTURE.md` | System architecture, tech stack, database schema, API design, deployment. |

---

## 🗺️ Reading Order

1. Start with **01_PRD.md** to understand the product vision.
2. Read **11_TECHNICAL_ARCHITECTURE.md** for the system design overview.
3. Read individual feature specs (02–10) for detailed requirements per module.

---

## 🏗️ Status Legend

| Status | Meaning |
|--------|---------|
| ✅ UI Complete | Frontend mockup is fully built and interactive |
| 🔧 Backend Needed | Requires backend API implementation |
| 📐 Design Only | Concept exists in UI but needs refinement |
