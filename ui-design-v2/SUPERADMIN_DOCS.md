# Super Admin Panel Documentation

## 🔐 Overview

Super Admin Panel adalah **sistem manajemen multi-tenant** yang memungkinkan administrator sistem untuk mengelola semua outlets/tenants, users, subscriptions, dan monitoring sistem secara terpusat.

## 📋 Halaman Super Admin

### 1. **Admin Dashboard** (`admin-dashboard.html`)

**Dashboard monitoring untuk semua sistem**

#### Features:
- **System Stats Cards**:
  - Total Outlets: 127
  - Total Users: 2,456
  - Monthly Revenue: Rp 245M
  - Active Subscriptions: 98

- **Charts**:
  - Revenue Trend (6 bulan terakhir)
  - Outlet Growth per bulan

- **Recent Outlets**: 4 outlet terbaru
- **System Activity Log**: Real-time activity
- **Subscription Overview**: Active, Trial, Expiring, Expired

#### Tech Stack:
- Alpine.js untuk reactive data
- Chart.js untuk visualisasi
- Gradient sidebar purple/indigo

---

### 2. **Manage Outlets** (`admin-outlets.html`)

**CRUD dan monitoring semua outlet/tenant**

#### Features:
- **Stats**: Total, Active, Trial, Inactive outlets
- **Search & Filter**:
  - Search by nama/owner
  - Filter by status (Active/Trial/Inactive)

- **Outlet Cards** dengan info:
  - Logo outlet (avatar)
  - Nama outlet & ID
  - Owner, email, join date
  - Status badge (green/yellow/red)
  - Metrics: Total users, products, bookings
  - Action buttons: View, Edit

- **Modal Add Outlet**:
  - Nama outlet
  - Nama owner
  - Email & No. HP
  - Alamat
  - Plan selection (Trial/Basic/Pro/Enterprise)
  - Status

#### Data Sample:
```javascript
outlets: [
  { id: 'OUT-001', name: 'Sepatu Premium Jakarta', owner: 'Pak Budi', status: 'active', totalUsers: 5, totalProducts: 120, totalBookings: 456 },
  { id: 'OUT-002', name: 'Sport Rental Bandung', owner: 'Ibu Siti', status: 'active', totalUsers: 3, totalProducts: 85, totalBookings: 234 },
  ...
]
```

---

### 3. **Manage Users** (`admin-users.html`)

**Manajemen semua users dari semua outlets**

#### Features:
- **Stats**: Total Users, Owners, Staff, Active Today
- **Filters**:
  - Search by nama/email
  - Filter by outlet
  - Filter by role (Owner/Admin/Kasir)

- **Users Table** dengan kolom:
  - User (foto, nama, email)
  - Outlet (nama & ID)
  - Role (badge warna)
  - Status (Active/Inactive)
  - Last Login
  - Actions (View, Suspend)

- **Export CSV** button

#### Data Sample:
```javascript
users: [
  { id: 1, name: 'Pak Budi', email: 'budi@sepatu.com', outlet: 'Sepatu Premium Jakarta', outletId: 'OUT-001', role: 'owner', status: 'active', lastLogin: '2 mins ago' },
  ...
]
```

### 4. **Subscriptions Management** (`admin-subscriptions.html`) ⭐ NEW

**Billing & subscription tracking untuk semua outlets**

#### Features:
- **Stats Cards**:
  - Active: 98 (Rp 18.2M MRR)
  - Trial: 15 
  - Expiring Soon: 8
  - Expired: 6
  - Total MRR: Rp 18.2M

- **Plans Overview**:
  - Trial (Free) - 15 outlets
  - Basic (Rp 99K/bulan) - 42 outlets
  - Pro (Rp 199K/bulan) - 48 outlets ⭐ Popular
  - Enterprise (Rp 499K/bulan) - 8 outlets

- **Filters**:
  - Search by outlet name/ID
  - Filter by plan (Trial/Basic/Pro/Enterprise)
  - Filter by status (Active/Expiring/Expired)

- **Subscriptions Table** dengan kolom:
  - Outlet (nama & ID)
  - Plan (badge warna)
  - Price
  - Status (Active/Expiring/Expired)
  - Next Billing date
  - Actions (View Invoice, Upgrade, Cancel)

- **Export Billing** to CSV/Excel

#### Data Sample:
```javascript
subscriptions: [
  { outlet: 'Sepatu Premium Jakarta', plan: 'pro', price: 'Rp 199K', status: 'active', nextBilling: '15 May 2024' },
  ...
]
```

---

### 5. **System Reports** (`admin-reports.html`) ⭐ NEW

**Comprehensive analytics & reporting untuk seluruh sistem**

#### Features:
- **Report Type Selector**:
  - Revenue Report
  - Outlets Performance
  - User Activity
  - Subscription Analytics

- **Period Selector**:
  - This Month
  - This Quarter
  - This Year
  - Custom Range

- **Summary Cards**:
  - Total Revenue: Rp 245M (+18%)
  - Total Bookings: 12,458
  - Avg per Outlet: Rp 1.93M
  - Growth Rate: +24% YoY

- **4 Interactive Charts**:
  - Revenue by Outlet (Top 10) - Bar Chart
  - User Growth Trend - Line Chart
  - Subscription Distribution - Doughnut Chart
  - Booking Trends (12 months) - Line Chart

- **Top Performers Table**:
  - Top 10 Outlets by Revenue
  - Growth percentage per outlet

- **Key System Metrics**:
  - Total Active Users: 2,456
  - Total Products: 8,942
  - Avg Utilization Rate: 73%
  - Customer Satisfaction: 4.7/5

- **Export Functions**:
  - Export to PDF
  - Export to Excel

#### Charts Library:
- Chart.js untuk semua visualisasi
- Responsive & interactive
- Color-coded data

---

## 🎨 Design System

### Sidebar Super Admin
```css
Background: gradient purple-900 to indigo-900
Logo: Yellow crown icon
Active menu: white bg with opacity-20
Hover: white bg with opacity-10
Text: purple-100 / white
```

### Color Scheme
- **Primary**: Purple 600
- **Secondary**: Indigo 600
- **Success**: Green 600
- **Warning**: Yellow 500
- **Danger**: Red 600
- **Crown**: Yellow 400

### Menu Structure
```
🔐 Super Admin
├── Dashboard
├── Outlets
├── Users
├── Subscriptions (planned)
├── Reports (planned)
├── ─────────────
├── Settings (planned)
└── Logout
```

---

## 🆚 Perbedaan dengan Outlet Pages

| Aspect | Super Admin | Outlet/Tenant |
|--------|-------------|---------------|
| **Scope** | Multi-tenant | Single tenant |
| **Users** | All users | Outlet users only |
| **Data** | All outlets | Own data only |
| **Sidebar** | Purple gradient | White/slate |
| **Access** | System admin | Outlet owner/staff |
| **Features** | Manage outlets, subscriptions | Manage products, bookings |

---

## 🔑 Authentication Flow

```
Login Page
    ↓
Check role
    ↓
    ├─ Super Admin → admin-dashboard.html
    └─ Outlet User → dashboard.html
```

---

## 📊 Use Cases

### 1. Tambah Outlet Baru
1. Super admin masuk ke **Manage Outlets**
2. Klik "Tambah Outlet"
3. Isi form (nama, owner, email, plan)
4. Klik "Buat Outlet"
5. System kirim email aktivasi ke owner

### 2. Monitor Revenue
1. Super admin masuk ke **Dashboard**
2. Lihat revenue trend chart
3. Lihat breakdown per outlet
4. Export report

### 3. Suspend User
1. Super admin masuk ke **Manage Users**
2. Search user
3. Klik suspend button
4. Konfirmasi
5. User tidak bisa login

---

## 🚀 Future Features (Planned)

### 4. **Subscriptions Management**
- List all subscriptions
- Billing history
- Payment tracking
- Plan upgrades/downgrades
- Trial expiration alerts

### 5. **Admin Reports**
- Revenue by outlet
- User growth metrics
- Subscription analytics
- System usage statistics
- Export to PDF/Excel

### 6. **Admin Settings** (`admin-settings.html`) ⭐ NEW
- System configuration & Feature Toggles
- Role & Permissions Management (Global)
- Pricing plans (UI Implemented)
- Communication Templates (Email, Push, WhatsApp) (UI Implemented)
- API keys & Integrations (UI Implemented)

---

## 🔒 Security Notes

- Super admin credentials **harus sangat aman**
- Implementasi 2FA recommended
- Audit log semua super admin actions
- Rate limiting untuk API calls
- IP whitelist untuk admin access

---

## 💾 Database Schema (Recommended)

```sql
-- Outlets/Tenants
CREATE TABLE outlets (
    id VARCHAR(50) PRIMARY KEY,
    name VARCHAR(255),
    owner_name VARCHAR(255),
    email VARCHAR(255),
    phone VARCHAR(50),
    address TEXT,
    plan VARCHAR(50),
    status VARCHAR(20),
    created_at TIMESTAMP,
    trial_ends_at TIMESTAMP
);

-- Multi-tenant Users
CREATE TABLE users (
    id INT PRIMARY KEY,
    outlet_id VARCHAR(50) REFERENCES outlets(id),
    name VARCHAR(255),
    email VARCHAR(255),
    role VARCHAR(50),
    status VARCHAR(20),
    last_login TIMESTAMP
);

-- Subscriptions
CREATE TABLE subscriptions (
    id INT PRIMARY KEY,
    outlet_id VARCHAR(50) REFERENCES outlets(id),
    plan VARCHAR(50),
    status VARCHAR(20),
    price DECIMAL(10,2),
    billing_cycle VARCHAR(20),
    next_billing_date DATE,
    created_at TIMESTAMP
);
```

---

## 📝 Notes

1. **Isolasi Data**: Setiap outlet punya database/schema terpisah atau tenant_id untuk multi-tenancy
2. **Scalability**: Design support ratusan/ribuan outlets
3. **Monitoring**: Real-time monitoring performa setiap outlet
4. **Backup**: Automatic backup per outlet
5. **Migration**: Easy tenant migration antar server

---

## 🎯 Summary

Super Admin Panel memberikan **kontrol penuh** atas sistem multi-tenant, memungkinkan:
- ✅ Manage ratusan outlets dari satu dashboard
- ✅ Monitor revenue & metrics real-time
- ✅ User management cross-tenant
- ✅ Subscription & billing tracking
- ✅ System analytics & reporting
- ✅ Global configuration & custom roles management

**Total Halaman Super Admin yang sudah dibuat: 6 halaman** 🎉
- admin-dashboard.html ✅
- admin-outlets.html ✅
- admin-users.html ✅
- admin-subscriptions.html ✅
- admin-reports.html ✅
- admin-settings.html ✅ NEW
