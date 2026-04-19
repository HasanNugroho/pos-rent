# Feature Spec — Reporting & Analytics

> **Module**: Reports | **UI Pages**: `reports.html` (tenant), `admin-reports.html` (super admin)
> **Priority**: P1 | **Status**: ✅ UI Complete, 🔧 Backend Needed

---

## 1. Overview

Two reporting layers exist: **Tenant Reports** (per-outlet analytics visible to outlet staff) and **System Reports** (cross-tenant analytics visible to super admins).

---

## 2. Tenant-Level Reports

**UI Reference**: `reports.html`

### 2.1 Report Types

| Report | Permission | Description |
|--------|-----------|-------------|
| Revenue | `report:view-revenue` | Income breakdown by period, product, payment method |
| Rental | `report:view-rental` | Booking counts, duration averages, occupancy rate |
| Product | `report:view-product` | Utilization per product, top earners, maintenance cost |
| Customer | `report:view-customer` | Top customers, repeat rate, lifetime value |
| Staff | `report:view-staff` | Bookings created per staff, revenue per staff |

### 2.2 Dashboard Summary

**UI Reference**: `dashboard.html`

- Revenue this month (vs last month %)
- Active bookings count
- Total products
- Overdue count (⚠️ warning)
- Revenue chart (Chart.js line — last 6 months)
- Top products (bar)
- Recent bookings table

### 2.3 Period Selection

| Period | Description |
|--------|-------------|
| Today | Current day |
| This Week | Mon–Sun |
| This Month | Calendar month |
| This Quarter | Q1/Q2/Q3/Q4 |
| Custom | Date range picker |

### 2.4 Export

- **PDF**: Formatted report with charts (server-rendered)
- **Excel**: Raw data tables
- Permission required: `report:export`

---

## 3. System-Level Reports (Super Admin)

**UI Reference**: `admin-reports.html`

### 3.1 Summary Cards

| Metric | Example |
|--------|---------|
| Total Revenue (all tenants) | Rp 245M (+18%) |
| Total Bookings | 12,458 |
| Avg Revenue per Outlet | Rp 1.93M |
| Growth Rate | +24% YoY |

### 3.2 Charts (Chart.js)

| Chart | Type | Data |
|-------|------|------|
| Revenue by Outlet (Top 10) | Bar | Monthly revenue breakdown |
| User Growth Trend | Line | New users over time |
| Subscription Distribution | Doughnut | Free / Basic / Pro / Enterprise |
| Booking Trends | Line | 12-month booking volume |

### 3.3 Top Performers Table

- Top 10 outlets by revenue
- Columns: Rank, Outlet, Revenue, Bookings, Growth %

### 3.4 Key System Metrics

| Metric | Value |
|--------|-------|
| Total Active Users | 2,456 |
| Total Products | 8,942 |
| Avg Utilization Rate | 73% |
| Customer Satisfaction | 4.7/5 |

---

## 4. API Endpoints

> **Header**: All tenant-scoped endpoints require `X-MERCHANT-ID: <tenant_id>` header. Super Admin (`/api/admin/*`) endpoints do NOT require this header. See `02_FEATURE_AUTH.md` for details.

| Method | Endpoint | Scope | `X-MERCHANT-ID` | Description |
|--------|----------|-------|:---:|-------------|
| GET | `/api/reports/dashboard` | Tenant | ✅ | Dashboard summary stats |
| GET | `/api/reports/revenue` | Tenant | ✅ | Revenue report data |
| GET | `/api/reports/bookings` | Tenant | ✅ | Booking analytics |
| GET | `/api/reports/products` | Tenant | ✅ | Product analytics |
| GET | `/api/reports/customers` | Tenant | ✅ | Customer analytics |
| GET | `/api/reports/export` | Tenant | ✅ | Export PDF/Excel |
| GET | `/api/admin/reports/overview` | Admin | ❌ | System-wide summary |
| GET | `/api/admin/reports/revenue` | Admin | ❌ | Revenue by outlet |
| GET | `/api/admin/reports/growth` | Admin | ❌ | User/outlet growth |
| GET | `/api/admin/reports/export` | Admin | ❌ | System export |

---

## 5. Caching Strategy (Redis)

| Key | TTL | Notes |
|-----|-----|-------|
| `report:dashboard:{tenantId}` | 5 min | Invalidated on booking/payment mutations |
| `report:revenue:{tenantId}:{period}` | 15 min | Per-period cache |
| `admin:report:overview` | 10 min | System-wide aggregate |

Reports are computationally expensive — use materialized views or pre-aggregation for large datasets.
