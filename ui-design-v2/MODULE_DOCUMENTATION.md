# Dokumentasi Modul POS Rental UI Design V2

## Overview Arsitektur Sistem

Sistem POS Rental memiliki **3 Level Akses**:
1. **Super Admin** - Multi-tenant management (SaaS)
2. **Outlet/Tenant Level** - Operasional single outlet
3. **Public** - Customer-facing (tanpa login)

---

## � TOTAL MODUL: 20

**Breakdown:**
- Super Admin Level: 6 modul
- Outlet/Tenant Level: 11 modul
- Public Level: 1 modul
- Auth: 2 modul

---

## � LEVEL 1: SUPER ADMIN MODULES (Multi-Tenant Management)

### 1. Admin Dashboard (`admin-dashboard.html`)

**Deskripsi Modul:**
Halaman monitoring sistem untuk super admin dengan overview seluruh tenant/outlet dalam platform.

**Fitur & Kegunaan:**
| Fitur | Kegunaan |
|-------|----------|
| System-wide Stats | Melihat total outlets, users, revenue, subscriptions |
| Revenue Trend Chart | Grafik pendapatan 6 bulan terakhir |
| Outlet Growth Chart | Pertumbuhan jumlah outlet per bulan |
| Recent Outlets | 4 outlet terbaru dengan status |
| Activity Log | Real-time tracking aktivitas sistem |
| Subscription Overview | Breakdown Active/Trial/Expiring/Expired |

**Field yang Digunakan:**
```javascript
{
  totalOutlets: number,
  totalUsers: number,
  monthlyRevenue: number,
  activeSubscriptions: number,
  recentOutlets: [
    { id, name, owner, status, joinDate }
  ],
  subscriptionStats: {
    active, trial, expiring, expired
  }
}
```

**Proses (CRUD):**
- **Read**: Mengambil statistik agregat dari semua outlet
- **Read**: Menampilkan daftar outlet terbaru
- **Read**: Melihat activity log sistem

**Relasi:**
- Terhubung ke: `admin-outlets.html`, `admin-users.html`, `admin-subscriptions.html`
- Data dari: Semua outlet dalam sistem

---

### 2. Manage Outlets (`admin-outlets.html`)

**Deskripsi Modul:**
CRUD outlets/tenants dalam sistem multi-tenant.

**Fitur & Kegunaan:**
| Fitur | Kegunaan |
|-------|----------|
| Outlet Cards | Grid view semua outlet dengan metrics |
| Stats Overview | Total, Active, Trial, Inactive counts |
| Search & Filter | Cari berdasarkan nama, filter status |
| Add Outlet Modal | Form lengkap pembuatan outlet baru |
| View/Edit Outlet | Detail dan edit outlet |

**Field yang Digunakan:**
```javascript
{
  id: "OUT-001",
  name: "Sepatu Premium Jakarta",
  owner: "Pak Budi",
  email: "budi@sepatu.com",
  phone: "08123456789",
  address: "Jl. Sudirman No. 123",
  joinDate: "Jan 2024",
  status: "active|trial|inactive",
  plan: "Trial|Basic|Pro|Enterprise",
  totalUsers: 5,
  totalProducts: 120,
  totalBookings: 456
}
```

**Proses (CRUD):**
- **Create**: Tambah outlet baru dengan form lengkap
  - Field: nama outlet, nama owner, email, phone, alamat, plan, status
  - Auto-generate outlet ID
  - Kirim email aktivasi ke owner
- **Read**: List semua outlet dengan search & filter
- **Update**: Edit data outlet (nama, owner, plan, status)
- **Delete**: Nonaktifkan outlet (soft delete)

**Relasi:**
- Berelasi dengan: Users (setiap outlet punya owner & staff)
- Berelasi dengan: Subscriptions (setiap outlet punya plan aktif)
- Berelasi dengan: Products, Bookings (aggregated count)

---

### 3. Manage Users (`admin-users.html`)

**Deskripsi Modul:**
Manajemen semua users di seluruh outlet (cross-tenant user management).

**Fitur & Kegunaan:**
| Fitur | Kegunaan |
|-------|----------|
| Users Table | List semua users dengan detail lengkap |
| Multi-filter | Filter by outlet, role, status |
| Stats Cards | Total Users, Owners, Staff, Active Today |
| Export CSV | Export data users |
| Suspend User | Nonaktifkan user |

**Field yang Digunakan:**
```javascript
{
  id: "USR-001",
  name: "Pak Budi",
  email: "budi@sepatu.com",
  phone: "08123456789",
  outletId: "OUT-001",
  outletName: "Sepatu Premium Jakarta",
  role: "Owner|Admin|Kasir|Manager",
  status: "Active|Inactive|Suspended",
  lastLogin: "2024-04-15 14:30",
  joinedDate: "Jan 2024"
}
```

**Proses (CRUD):**
- **Create**: Tambah user (via invite atau manual)
- **Read**: List users dengan filter multi-level
- **Update**: Edit data user & role
- **Delete**: Suspend/deactivate user

**Relasi:**
- Berelasi dengan: Outlet (setiap user ter-assign ke outlet)
- Berelasi dengan: Permissions (role-based access)

---

### 4. Subscriptions Management (`admin-subscriptions.html`)

**Deskripsi Modul:**
Billing dan subscription tracking untuk semua outlet.

**Fitur & Kegunaan:**
| Fitur | Kegunaan |
|-------|----------|
| Plans Overview | Statistik per plan (Trial/Basic/Pro/Enterprise) |
| Subscriptions Table | List subscriptions dengan status |
| MRR Calculation | Monthly Recurring Revenue |
| Upgrade/Downgrade | Ganti plan outlet |
| Export Billing | Export data ke CSV |

**Field yang Digunakan:**
```javascript
{
  subscriptionId: "SUB-001",
  outletId: "OUT-001",
  outletName: "Sepatu Premium",
  plan: "Pro",
  price: 199000,
  status: "Active|Trial|Expired|Cancelled",
  startDate: "2024-01-15",
  nextBillingDate: "2024-05-15",
  billingCycle: "monthly|yearly",
  paymentHistory: [
    { date, amount, method, invoiceNumber }
  ]
}
```

**Proses (CRUD):**
- **Create**: Assign plan ke outlet baru
- **Read**: List subscriptions dengan filter
- **Update**: Upgrade/downgrade plan, extend trial
- **Delete**: Cancel subscription

**Relasi:**
- Berelasi dengan: Outlet (1 outlet = 1 subscription aktif)
- Berelasi dengan: Payment History (riwayat pembayaran)

---

### 5. System Reports (`admin-reports.html`)

**Deskripsi Modul:**
Analytics dan insights untuk seluruh sistem.

**Fitur & Kegunaan:**
| Fitur | Kegunaan |
|-------|----------|
| Report Type Selector | Revenue, Outlets, Users, Subscriptions |
| Period Selector | Month/Quarter/Year/Custom |
| Interactive Charts | 4 chart types (Chart.js) |
| Top 10 Outlets | Ranking by revenue |
| Export | PDF & Excel |

**Field yang Digunakan:**
```javascript
{
  reportType: "revenue|outlets|users|subscriptions",
  period: { startDate, endDate },
  summary: {
    totalRevenue: 245000000,
    totalBookings: 12458,
    avgPerOutlet: 1930000,
    growthRate: 24
  },
  charts: {
    revenueByOutlet: [],
    userGrowthTrend: [],
    subscriptionDistribution: [],
    bookingTrends: []
  },
  topOutlets: [
    { rank, outletId, outletName, revenue, bookings }
  ]
}
```

**Proses (CRUD):**
- **Read**: Generate report berdasarkan filter
- **Read**: Export data ke PDF/Excel

---

### 6. Super Admin Settings (`admin-settings.html`)

**Deskripsi Modul:**
Konfigurasi global sistem dengan 4 tabs: System Configuration, Roles & Permissions, Pricing Plans, Email Templates.

**Fitur & Kegunaan:**

#### Tab 1: System Configuration
| Fitur | Kegunaan |
|-------|----------|
| General Settings | App name, support email, trial duration, timezone |
| Feature Toggles | Allow new registrations, Maintenance mode |

**Field:**
```javascript
{
  appName: "POS Rental System",
  supportEmail: "support@posrental.com",
  defaultTrialDays: 14,
  timezone: "Asia/Jakarta",
  allowNewRegistrations: true,
  maintenanceMode: false
}
```

#### Tab 2: Roles & Permissions
| Fitur | Kegunaan |
|-------|----------|
| Roles Table | List system & custom roles |
| Create Custom Role | Tambah role baru dengan permissions |
| Edit Permissions | Granular permission matrix |
| Delete Custom Role | Hapus role custom (system role locked) |

**Permission Groups:**
- **User Management**: create, read, update, delete, list, assign-role
- **Role Management**: create, read, update, delete, list
- **Product Management**: create, read, update, delete, list, import, export
- **Booking Management**: create, read, update, cancel, list, list-own, checkin, checkout
- **Payment Management**: create, read, refund, list
- **Tenant Management**: settings, billing, branding
- **Report Management**: view-dashboard, view-revenue, export

**Field:**
```javascript
{
  roleId: 1,
  name: "Owner",
  description: "Tenant Owner dengan full akses",
  isSystem: true, // System role tidak bisa diedit
  permissionCount: 85,
  permissions: [
    "user:create", "user:read", "user:update", "user:delete",
    "product:create", "product:read", "product:update",
    "booking:create", "booking:read", "booking:checkin",
    // ... total 85 permissions
  ]
}
```

**System Roles (Locked):**
- Owner (85 permissions)
- Manager (62 permissions)
- Staff/Kasir (30 permissions)
- Inventory Staff (35 permissions)

#### Tab 3: Pricing Plans
| Fitur | Kegunaan |
|-------|----------|
| Plans Grid | Display 4 plans (Trial/Basic/Pro/Enterprise) |
| Add New Plan | Form lengkap untuk create plan |
| Edit Plan | Update limits & features |
| Core Limits | Max outlets, users, products |
| Feature Toggles | Multi-branch sync, inventory, API access |
| Add-ons Config | Pricing untuk +Outlet, +User, +Products |

**Field:**
```javascript
{
  planId: "pro",
  name: "Pro Plan",
  price: 199000,
  interval: "monthly|yearly",
  limits: {
    maxOutlets: 3,
    maxUsers: null, // unlimited
    maxProducts: 2000
  },
  features: {
    inventoryTracking: true,
    advancedReporting: false,
    apiAccess: false,
    customDomain: false
  },
  addons: [
    { name: "+1 Outlet", price: 50000, enabled: true },
    { name: "+1 User", price: 15000, enabled: true },
    { name: "+500 Products", price: 25000, enabled: false }
  ]
}
```

**Plans Summary:**
| Plan | Price | Outlets | Users | Products |
|------|-------|---------|-------|----------|
| Trial | Rp 0 (14 days) | 1 | 5 | 50 |
| Basic | Rp 99K/month | 1 | 10 | 500 |
| Pro | Rp 199K/month | 3 | ∞ | 2000 |
| Enterprise | Rp 499K/month | ∞ | ∞ | ∞ |

#### Tab 4: Email Templates
| Fitur | Kegunaan |
|-------|----------|
| Event Triggers | List 6 event types |
| Channel Tabs | Email, WhatsApp, Push Notification |
| Template Editor | Subject + body dengan variables |
| Enable/Disable | Toggle per channel |
| Send Test | Test email/notif |

**Event Triggers:**
1. Verify Email Address
2. Forgot Password
3. Subscription Reminder
4. Payment Receipt
5. New Booking (Tenant)
6. Overdue Return

**Field:**
```javascript
{
  trigger: "auth_verify",
  channels: {
    email: {
      enabled: true,
      subject: "Welcome to POS Rental, {{user_name}}!",
      body: "Hi {{user_name}},\n\nPlease verify your email...\n{{verify_url}}"
    },
    whatsapp: {
      enabled: true,
      body: "Hi {{user_name}},\nYour OTP: {{otp_code}}"
    },
    push: {
      enabled: false,
      title: "Welcome!",
      body: "Your account has been created."
    }
  },
  variables: [
    "{{user_name}}", "{{outlet_name}}", "{{login_url}}", 
    "{{trial_end_date}}", "{{otp_code}}", "{{verify_url}}"
  ]
}
```

**Proses (CRUD):**
- **Read**: Load system config, roles, plans, templates
- **Create**: Create custom role, create new plan
- **Update**: Update system settings, edit role permissions, edit plan limits, edit templates
- **Delete**: Delete custom role (system roles protected)

**Relasi:**
- Berelasi dengan: All Outlets (global config)
- Berelasi dengan: Subscriptions (plan limits)
- Berelasi dengan: Staff (role permissions)

---

## 🏢 LEVEL 2: OUTLET/TENANT MODULES (Single Outlet Operations)

### 7. Dashboard (`dashboard.html`)

**Deskripsi Modul:**
Overview bisnis untuk outlet aktif.

**Fitur & Kegunaan:**
| Fitur | Kegunaan |
|-------|----------|
| Stats Cards | Pendapatan hari ini, Booking aktif, Produk tersedia, Overdue |
| Revenue Chart | Trend 7 hari terakhir (Chart.js) |
| Top Products | Produk terlaris |
| Recent Bookings | 5 booking terbaru |
| Quick Actions | Tombol booking baru |

**Field yang Digunakan:**
```javascript
{
  todayRevenue: 850000,
  revenueChange: 12, // percentage
  activeBookings: 24,
  newBookingsToday: 8,
  availableProducts: 45,
  totalProducts: 120,
  rentedProducts: 75,
  overdueBookings: 3,
  revenueChart: {
    labels: ["9 Apr", "10 Apr", ...],
    data: [650000, 720000, ...]
  },
  topProducts: [
    { name, rentalsCount, revenue }
  ],
  recentBookings: [
    { id, customer, phone, items, period, status }
  ]
}
```

**Proses (CRUD):**
- **Read**: Agregat data real-time dari outlet aktif

**Relasi:**
- Berelasi dengan: Bookings, Products, Customers

---

### 8. Products Management (`products.html`)

**Deskripsi Modul:**
Manajemen katalog produk rental.

**Fitur & Kegunaan:**
| Fitur | Kegunaan |
|-------|----------|
| Product Grid | Card view produk dengan gambar & status |
| Search & Filter | Cari nama, filter kategori & status |
| Stats Cards | Total, Tersedia, Disewa, Maintenance |
| Add/Edit Product Modal | Form lengkap dengan upload foto |
| View Product Detail | Detail produk dengan kalender link |

**Field yang Digunakan:**
```javascript
{
  id: 1,
  name: "Nike Mercurial",
  sku: "NK-001",
  category: "Sepatu Futsal|Sepatu Basket|Sepatu Running",
  size: "42",
  stock: 5, // total unit
  available: 3, // unit tersedia
  price: 35000, // per hari
  description: "Sepatu futsal premium...",
  images: ["url1", "url2"],
  status: "available|rented|maintenance",
  totalRentals: 42,
  createdAt: "2024-01-15",
  
  // Custom Fields (dinamis dari Settings)
  customFields: {
    // Contoh field custom yang bisa ditambahkan dari Settings:
    "brand": "Nike", // Type: Text
    "weight": 350, // Type: Number
    "condition": "Excellent", // Type: Dropdown
    "purchaseDate": "2024-01-10", // Type: Date
    "isWaterproof": true // Type: Checkbox
  }
}
```

**Proses (CRUD):**
- **Create**: Tambah produk baru
  - Field: nama, SKU, kategori, size, stock, harga, deskripsi, foto
- **Read**: List produk dengan filter & search
- **Update**: Edit data produk
- **Delete**: Soft delete (mark as inactive)

**Relasi:**
- Berelasi dengan: Bookings (produk yang dibooking)
- Berelasi dengan: Availability (ketersediaan per tanggal)
- Berelasi dengan: Maintenance (history perawatan)
- Berelasi dengan: Categories (kategori produk)

---

### 9. Availability Calendar (`availability.html`)

**Deskripsi Modul:**
Kalender visual ketersediaan produk per tanggal.

**Fitur & Kegunaan:**
| Fitur | Kegunaan |
|-------|----------|
| Calendar Grid | View 1 bulan per produk |
| Color-coded Status | Hijau/Orange/Merah/Kuning |
| Expandable Products | Collapse/expand per produk |
| Day Detail Modal | Detail booking per tanggal |
| Month Navigation | Prev/Next month |

**Color Legend:**
- 🟢 Hijau: Tersedia Banyak (>70% stock)
- 🟠 Orange: Stock Terbatas (1-70% stock)
- 🔴 Merah: Habis / Fully Booked
- 🟡 Kuning: Semua unit Maintenance

**Field yang Digunakan:**
```javascript
{
  product: {
    id, name, sku, stock, available
  },
  calendarDays: [
    {
      date: "2024-04-15",
      day: 15,
      available: 3,
      booked: 2,
      maintenance: 0,
      allMaintenance: false,
      currentMonth: true,
      bookings: [
        { id, customer, qty }
      ]
    }
  ]
}
```

**Proses (CRUD):**
- **Read**: Generate kalender berdasarkan booking & maintenance data

**Relasi:**
- Berelasi dengan: Products (data stock)
- Berelasi dengan: Bookings (booking aktif)
- Berelasi dengan: Maintenance (unit dalam perawatan)

---

### 10. Bookings Management (`bookings.html`)

**Deskripsi Modul:**
Manajemen transaksi rental lengkap dengan DP & pelunasan.

**Fitur & Kegunaan:**
| Fitur | Kegunaan |
|-------|----------|
| Bookings Table | List semua booking dengan detail pembayaran |
| Status Filter | Confirmed, Active, Completed |
| Stats Cards | Total, Confirmed, Active, Completed |
| Pelunasan Modal | Proses pelunasan saat pickup |
| View Detail Modal | Detail lengkap booking |
| Invoice Modal | Generate & print invoice |
| Custom Schedule Badge | Indikator jadwal custom pickup/return |

**Status Flow:**
```
Confirmed → Active (pickup) → Completed (return)
     ↓           ↓                ↓
   Edit    Pelunasan DP      Invoice
```

**Field yang Digunakan:**
```javascript
{
  id: "BK-001",
  customer: "Ahmad Rizki",
  phone: "08123456789",
  email: "ahmad@email.com",
  items: 2,
  products: [
    { id, name, sku, qty, price }
  ],
  period: "15-17 Apr",
  startDate: "2024-04-15",
  endDate: "2024-04-17",
  duration: 3,
  pickupDatetime: "2024-04-15 09:00",
  returnDatetime: "2024-04-17 17:00",
  blockedDays: "15,16,17", // hari yang diblock
  hasCustomSchedule: true,
  total: 406000,
  paymentType: "dp|full",
  dpPaid: 200000,
  remainingPayment: 206000,
  paymentMethod: "cash|transfer|qris|ewallet",
  status: "confirmed|active|completed|cancelled",
  createdAt: "2024-04-10",
  pickedUpAt: "2024-04-15 09:30",
  returnedAt: null,
  notes: "..."
}
```

**Proses (CRUD):**
- **Create**: Booking dibuat di `booking-create.html`
- **Read**: List bookings dengan filter & search
- **Update**: 
  - Edit booking (hanya status Confirmed)
  - Process pickup: ubah status ke Active + pelunasan DP
  - Process return: ubah status ke Completed
- **Delete**: Cancel booking (soft delete)

**Relasi:**
- Berelasi dengan: Customers
- Berelasi dengan: Products (multiple)
- Berelasi dengan: Payments (multiple records)
- Berelasi dengan: Invoices

---

### 11. Create Booking (`booking-create.html`)

**Deskripsi Modul:**
Form 3-step untuk membuat booking baru.

**Fitur & Kegunaan:**
| Fitur | Kegunaan |
|-------|----------|
| Step Indicator | Visual progress 3 step |
| Step 1: Customer | Pilih customer atau tambah baru |
| Step 2: Product & Date | Pilih produk & periode sewa |
| Custom Pickup/Return | Set custom datetime (opsional) |
| Step 3: Payment | Pilih DP/Lunas & metode bayar |
| Real-time Calculation | Kalkulasi otomatis |
| Sidebar Summary | Ringkasan booking |

**3-Step Flow:**
```
Step 1: Customer Info
  → Pilih existing / Tambah customer baru
  
Step 2: Produk & Tanggal
  → Pilih tanggal mulai & selesai
  → Set custom pickup/return (opsional)
  → Pilih produk dari modal
  
Step 3: Pembayaran
  → Pilih DP atau Lunas
  → Input jumlah DP (jika DP)
  → Pilih metode pembayaran
  → Konfirmasi booking
```

**Field yang Digunakan:**
```javascript
// Step 1
{
  selectedCustomer: "CUST-001",
  customerName: "Ahmad Rizki",
  phone: "08123456789",
  email: "ahmad@email.com"
}

// Step 2
{
  startDate: "2024-04-15",
  endDate: "2024-04-17",
  pickupDate: "2024-04-15",
  pickupTime: "09:00",
  returnDate: "2024-04-17",
  returnTime: "17:00",
  selectedProducts: [
    { id, name, sku, price, qty }
  ],
  duration: 3,
  subtotal: 306000,
  deposit: 100000,
  total: 406000
}

// Step 3
{
  paymentType: "dp", // or "full"
  dpAmount: 200000,
  remaining: 206000,
  paymentMethod: "cash|transfer|qris|ewallet",
  notes: "..."
}
```

**Proses (CRUD):**
- **Create**: Create booking baru dengan status "confirmed"
  - Block availability untuk pickup sampai return
  - Charge hanya untuk rental period (start-end)
  - Record payment (DP atau Lunas)

**Relasi:**
- Berelasi dengan: Customers
- Berelasi dengan: Products
- Berelasi dengan: Availability (block dates)

---

### 12. Customers Management (`customers.html`)

**Deskripsi Modul:**
Database pelanggan dengan blacklist system.

**Fitur & Kegunaan:**
| Fitur | Kegunaan |
|-------|----------|
| Customer Grid | Card view dengan statistik |
| Search & Filter | Cari nama/phone, filter status |
| Stats Cards | Total, Active Bulan Ini, Baru, Total Transaksi, Blacklist |
| Blacklist System | Blokir customer dengan alasan |
| Blacklist Reason | Tampilkan alasan pemblokiran |
| WhatsApp Chat | Direct link ke WhatsApp |
| View Detail Modal | History booking customer |

**Field yang Digunakan:**
```javascript
{
  id: "CUST-001",
  name: "Ahmad Rizki",
  phone: "08123456789",
  email: "ahmad@email.com",
  address: "Jl. Mawar No. 5",
  memberSince: "Jan 2024",
  totalBookings: 15,
  totalSpend: 4500000,
  status: "active|blacklisted",
  blacklistReason: "Tidak mengembalikan barang tepat waktu...",
  blacklistDate: "15 Mar 2024",
  bookingHistory: [
    { id, date, total, status }
  ],
  avgRating: 4.8
}
```

**Proses (CRUD):**
- **Create**: Tambah customer baru
  - Field: nama, phone, email, alamat
- **Read**: List customers dengan search & filter
- **Update**: Edit data customer
- **Update**: Blacklist customer (dengan alasan wajib)
- **Update**: Unblock customer (hapus dari blacklist)
- **Delete**: Hard delete (jika tidak ada riwayat)

**Relasi:**
- Berelasi dengan: Bookings (semua booking customer)
- Berelasi dengan: Create Booking (data customer)

---

### 13. Staff Management (`staff.html`)

**Deskripsi Modul:**
Manajemen akun staff dengan permission system.

**Fitur & Kegunaan:**
| Fitur | Kegunaan |
|-------|----------|
| Staff Grid | Card view staff dengan role badge |
| Role Filter | Owner, Manager, Kasir, Inventory |
| Stats Cards | Total, Active, Owner, Kasir |
| Add/Edit Modal | Form lengkap dengan permissions |
| Permission Checkboxes | Granular permissions |
| Email Invite | Kirim undangan email |

**Role & Permissions:**
```javascript
{
  role: "owner|manager|kasir|inventory",
  permissions: [
    "booking:create", "booking:read", "booking:update",
    "product:create", "product:read", "product:update",
    "customer:create", "customer:read",
    "payment:create",
    "report:read"
  ]
}
```

**Field yang Digunakan:**
```javascript
{
  id: 1,
  name: "Pak Budi",
  email: "budi@sepatu.com",
  phone: "08123456789",
  role: "owner",
  status: "active|inactive",
  joinedDate: "Jan 2024",
  password: "***",
  permissions: ["booking:create", "product:update", ...],
  lastLogin: "2024-04-15 09:00"
}
```

**Proses (CRUD):**
- **Create**: Tambah staff baru
  - Field: nama, email, phone, role, password
  - Set permissions (checkboxes)
  - Kirim email invite (opsional)
- **Read**: List staff dengan filter
- **Update**: Edit data & permissions
- **Delete**: Deactivate staff (soft delete)

**Relasi:**
- Berelasi dengan: Outlet (staff ter-assign ke outlet)
- Berelasi dengan: Permissions (RBAC system)

---

### 14. Maintenance Management (`maintenance.html`)

**Deskripsi Modul:**
Tracking perawatan dan perbaikan produk.

**Fitur & Kegunaan:**
| Fitur | Kegunaan |
|-------|----------|
| Product-based View | List per produk dengan unit breakdown |
| Maintenance List | Detail setiap maintenance |
| Progress Bar | Visual progress in-progress |
| Schedule Modal | Jadwalkan maintenance baru |
| Complete Modal | Selesaikan dengan biaya aktual |
| Receipt Upload | Upload nota/bukti pembayaran |
| Stats Cards | Total, In Progress, Scheduled, Completed |

**Maintenance Types:**
- `cleaning` - Cuci/Cleaning
- `repair` - Perbaikan
- `inspection` - Inspeksi

**Status:**
- `scheduled` - Dijadwalkan
- `in_progress` - Sedang dikerjakan
- `completed` - Selesai

**Field yang Digunakan:**
```javascript
{
  maintenanceId: "MAINT-001",
  productId: "PROD-001",
  productName: "Nike Mercurial",
  unitNumber: 3,
  type: "cleaning|repair|inspection",
  status: "scheduled|in_progress|completed",
  date: "2024-04-20",
  estimatedCompletion: "2024-04-21",
  actualCompletion: "2024-04-21",
  duration: "1 hari",
  estimatedCost: 50000,
  actualCost: 55000,
  progress: 60, // percentage for in_progress
  notes: "Cuci dan perawatan rutin...",
  completionNotes: "Sepatu sudah bersih dan kering...",
  receiptImage: "url_to_receipt",
  scheduledBy: "Pak Budi",
  completedBy: "Ani Wijaya"
}
```

**Proses (CRUD):**
- **Create**: Schedule maintenance
  - Pilih unit yang tersedia
  - Set type, tanggal, estimasi biaya
  - Catat detail kerusakan/perawatan
- **Read**: List maintenance dengan filter status & type
- **Update**: Update progress (in_progress)
- **Update**: Complete maintenance
  - Input biaya aktual
  - Upload receipt
  - Catat completion notes
  - Unit kembali tersedia
- **Delete**: Cancel scheduled maintenance

**Relasi:**
- Berelasi dengan: Products (maintenance per unit)
- Berelasi dengan: Availability (block unit yang di-maintenance)

---

### 15. Outlet Management (`outlets.html`)

**Deskripsi Modul:**
Multi-branch/outlet management untuk Owner.

**Fitur & Kegunaan:**
| Fitur | Kegunaan |
|-------|----------|
| Plan Limit Banner | Kuota outlet dengan progress bar |
| Stats Cards | Total, Aktif, Total Staff, Total Produk (aggregated) |
| Outlet Switcher | Dropdown di header untuk pindah outlet |
| Outlet Cards | Detail per outlet dengan metrics |
| Add Outlet Modal | Form tambah outlet baru |
| Deactivate | Nonaktifkan outlet (selain utama) |
| Outlet Metrics | Staff count, Product count, Booking count, Revenue |

**Field yang Digunakan:**
```javascript
{
  id: "OUT-001",
  name: "Cabang Utama",
  address: "Jl. Sudirman No. 123",
  phone: "021-1234567",
  email: "cabang1@sepatu.com",
  manager: "Pak Budi",
  status: "active|inactive",
  isPrimary: true,
  staffCount: 5,
  productCount: 120,
  bookingCount: 456,
  monthlyRevenue: 12500000,
  catalogSync: true, // sinkronisasi katalog
  operatingHours: {
    senin: { open: "09:00", close: "17:00" },
    // ...
  }
}
```

**Proses (CRUD):**
- **Create**: Tambah outlet baru (jika kuota masih ada)
  - Field: nama, alamat, phone, email, manager, jam operasional
  - Toggle: Sinkronisasi katalog dari outlet utama
- **Read**: List outlet dengan aggregated metrics
- **Update**: Edit data outlet
- **Update**: Switch active outlet (context switching)
- **Delete**: Deactivate outlet (bisa reactivate)

**Relasi:**
- Berelasi dengan: Subscription Plan (kuota outlet)
- Berelasi dengan: Staff (count per outlet)
- Berelasi dengan: Products (count per outlet)
- Berelasi dengan: Bookings (count & revenue per outlet)

---

### 16. Reports (`reports.html`)

**Deskripsi Modul:**
Laporan lengkap per outlet.

**Fitur & Kegunaan:**
| Fitur | Kegunaan |
|-------|----------|
| Period Selector | Bulan/Tahun/Custom range |
| Summary Cards | Total pendapatan, booking, avg per booking, utilization |
| Revenue Chart | Trend pendapatan (Chart.js) |
| Category Chart | Booking per kategori produk |
| Top Products Table | 10 produk terlaris |
| Top Customers Table | 10 customer terbaik |
| Maintenance Report | Summary maintenance dengan biaya |
| Export | PDF & Excel |

**Field yang Digunakan:**
```javascript
{
  period: "month",
  selectedMonth: 3, // April
  selectedYear: 2024,
  summary: {
    totalRevenue: 23500000,
    totalBookings: 156,
    avgPerBooking: 151000,
    utilizationRate: 73
  },
  charts: {
    revenueTrend: { labels, data },
    categoryDistribution: { labels, data }
  },
  topProducts: [
    { rank, name, bookings, revenue }
  ],
  topCustomers: [
    { rank, name, bookings, spend }
  ],
  maintenanceSummary: {
    total: 28,
    completed: 15,
    inProgress: 5,
    totalCost: 1200000
  }
}
```

**Proses (CRUD):**
- **Read**: Generate report berdasarkan periode
- **Read**: Export ke PDF/Excel

**Relasi:**
- Berelasi dengan: Bookings, Products, Customers, Maintenance (aggregated data)

---

### 17. Settings (`settings.html`)

**Deskripsi Modul:**
Konfigurasi outlet dengan 6 tabs.

**Tabs:**

#### Tab 1: Profile
- Edit data personal
- Upload foto profil
- Field: nama, email, phone, role

#### Tab 2: Business Info
- Data bisnis outlet
- Field: nama bisnis, alamat, phone, email

#### Tab 3: Payment Methods
- Aktif/nonaktif metode pembayaran
- Field: Tunai (always on), Transfer Bank, QRIS, E-Wallet
- Edit detail rekening bank

#### Tab 4: Notifications
- Toggle notifikasi
- Field:
  - Booking Baru
  - Pembayaran Masuk
  - Reminder Return (H-1)
  - Overdue Alert

#### Tab 5: Outlet Configuration
- Category Management: CRUD kategori dengan icon & warna
- Custom Fields: Field dinamis untuk produk (Text, Number, Dropdown, Date, Checkbox), mandatory?, description
- Tax & Fees: PPN, Service Charge
- Booking Rules: Min/max durasi, min DP percentage, late fee
- Operating Hours: Jam buka/tutup per hari

#### Tab 6: Subscription
- Current plan info
- Usage stats (outlet used/total, users, products)
- Add-on purchase: +Outlet, +User, +Products
- Payment history
- Download invoice

**Proses (CRUD):**
- **Read**: Load semua konfigurasi
- **Update**: Edit per tab
- **Create**: Add category, custom field, add-on
- **Delete**: Remove category, custom field

---

## 🌐 LEVEL 3: PUBLIC MODULES (Customer-Facing)

### 18. Public Catalog (`catalog.html`)

**Deskripsi Modul:**
Landing page publik untuk customer melihat produk & cek ketersediaan. **No login required.**

**Fitur & Kegunaan:**
| Fitur | Kegunaan |
|-------|----------|
| Hero Section | Gradient banner dengan value proposition |
| Sticky Navigation | Logo, contact, WhatsApp button |
| Search & Filter | Real-time search + filter kategori |
| Product Grid | 4 kolom desktop, card dengan gambar |
| Stock Warning Badge | Tampil jika stock <= 3 |
| Availability Modal | Cek ketersediaan per tanggal |
| WhatsApp Integration | Pre-filled message dengan detail produk |
| Footer | About, contact info, social media |

**Product Card Fields:**
```javascript
{
  id: 1,
  name: "Nike Air Max 270",
  type: "Running Shoes",
  category: "Sport|Casual|Formal|Hiking",
  description: "Sepatu running dengan teknologi Air Max...",
  sizes: [40, 41, 42, 43, 44],
  price: 75000,
  stock: 5,
  image: "url_to_image"
}
```

**Availability Check Fields:**
```javascript
{
  productId: 1,
  startDate: "2024-04-20",
  endDate: "2024-04-22",
  selectedSize: 42,
  quantity: 1,
  available: true|false,
  whatsappMessage: "Halo, saya ingin cek ketersediaan Nike Air Max..."
}
```

**Proses (CRUD):**
- **Read**: List produk publik (hanya yang tersedia)
- **Read**: Check availability per tanggal & size

**Relasi:**
- Berelasi dengan: Products (read-only subset)
- Berelasi dengan: Availability (read-only)

---

## 🔐 AUTH MODULES

### 19. Login (`login.html`)

**Fitur:**
- Login form (email/username + password)
- Demo accounts display
- Remember me
- Forgot password link
- Link ke register

**Field:**
```javascript
{
  email: "budi@sepatu.com",
  password: "***",
  rememberMe: true
}
```

**Flow:**
```
Login → Validate → Check Role
         → Outlet Staff → dashboard.html
         → Super Admin → admin-dashboard.html
```

---

### 20. Register (`register.html`)

**Fitur:**
- Form registrasi outlet baru
- Pilih plan (Trial/Basic/Pro/Enterprise)
- Validasi email & phone
- Terms & conditions

**Field:**
```javascript
{
  outletName: "Sepatu Premium",
  ownerName: "Pak Budi",
  email: "budi@sepatu.com",
  phone: "08123456789",
  address: "Jl. Sudirman No. 123",
  selectedPlan: "Trial",
  password: "***",
  confirmPassword: "***"
}
```

**Flow:**
```
Register → Create Outlet → Create Owner Account
         → Create Subscription (Trial)
         → Send Activation Email
         → Redirect to Dashboard
```

---

## 📊 DATABASE RELATIONSHIP DIAGRAM

```
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│   SUPER_ADMIN   │     │     OUTLET      │     │  SUBSCRIPTION   │
├─────────────────┤     ├─────────────────┤     ├─────────────────┤
│ id              │────▶│ id              │◄────│ id              │
│ name            │     │ name            │     │ outlet_id       │
│ email           │     │ owner_id        │     │ plan_type       │
│ role            │     │ address         │     │ price           │
└─────────────────┘     │ phone           │     │ status          │
                        │ status          │     │ start_date      │
                        │ catalog_sync    │     │ end_date        │
                        └─────────────────┘     └─────────────────┘
                                │
        ┌───────────────────────┼───────────────────────┐
        ▼                       ▼                       ▼
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│     STAFF       │     │    PRODUCTS     │     │   CUSTOMERS     │
├─────────────────┤     ├─────────────────┤     ├─────────────────┤
│ id              │     │ id              │     │ id              │
│ outlet_id       │◄────│ outlet_id       │     │ outlet_id       │
│ name            │     │ name            │     │ name            │
│ email           │     │ sku             │     │ phone           │
│ role            │     │ category_id   │◄────│ email           │
│ permissions[]   │     │ size            │     │ status          │
│ status          │     │ stock           │     │ blacklist_reason│
└─────────────────┘     │ price           │     └─────────────────┘
                        │ status          │            │
                        └─────────────────┘            │
                                │                        │
        ┌───────────────────────┼───────────────────────┘
        ▼                       ▼
┌─────────────────┐     ┌─────────────────┐
│  MAINTENANCE    │     │    BOOKINGS     │
├─────────────────┤     ├─────────────────┤
│ id              │     │ id              │
│ product_id      │◄────│ outlet_id       │
│ unit_number     │     │ customer_id     │◄────
│ type            │     │ product_ids[]   │
│ status          │     │ start_date      │
│ cost            │     │ end_date        │
│ receipt_url     │     │ pickup_datetime │
└─────────────────┘     │ return_datetime │
                        │ total           │
                        │ payment_type    │
                        │ dp_paid         │
                        │ status          │
                        └─────────────────┘
                                │
                                ▼
                        ┌─────────────────┐
                        │    PAYMENTS     │
                        ├─────────────────┤
                        │ id              │
                        │ booking_id      │
                        │ amount          │
                        │ type            │
                        │ method          │
                        │ timestamp       │
                        └─────────────────┘
```

---

## 🔑 PERMISSION MATRIX

| Fitur | Owner | Manager | Kasir | Inventory |
|-------|-------|---------|-------|-----------|
| **Dashboard** | ✅ | ✅ | ✅ | ✅ |
| **Booking Create** | ✅ | ✅ | ✅ | ❌ |
| **Booking Edit** | ✅ | ✅ | ❌ | ❌ |
| **Booking View** | ✅ | ✅ | ✅ | ❌ |
| **Product Create** | ✅ | ✅ | ❌ | ✅ |
| **Product Edit** | ✅ | ✅ | ❌ | ✅ |
| **Product Delete** | ✅ | ❌ | ❌ | ❌ |
| **Customer Create** | ✅ | ✅ | ✅ | ❌ |
| **Customer Blacklist** | ✅ | ✅ | ❌ | ❌ |
| **Staff Management** | ✅ | ❌ | ❌ | ❌ |
| **Reports View** | ✅ | ✅ | ❌ | ❌ |
| **Settings** | ✅ | ❌ | ❌ | ❌ |
| **Maintenance** | ✅ | ✅ | ❌ | ✅ |
| **Outlet Management** | ✅ | ❌ | ❌ | ❌ |

---

## 📱 STATUS FLOW SUMMARY

### Booking Status Flow:
```
[Draft] → [Confirmed] → [Active] → [Completed]
            ↓              ↓
         [Cancelled]   [Overdue]
                         ↓
                      [Completed with late fee]
```

### Maintenance Status Flow:
```
[Scheduled] → [In Progress] → [Completed]
      ↓
[Cancelled]
```

### Product Status:
```
available ↔ rented
     ↕
maintenance (temporary)
```

### Customer Status:
```
active ↔ blacklisted (with reason)
```

---

## 🎯 KEY BUSINESS RULES

1. **Availability Blocking**:
   - Barang di-block dari pickup date sampai return date
   - Charge dihitung dari start_date ke end_date (rental period)
   - Custom pickup/return tidak mengubah perhitungan charge

2. **DP System**:
   - Min DP: 50% dari total (configurable)
   - Sisa dibayar saat pickup
   - Invoice final generated setelah pelunasan

3. **Maintenance Impact**:
   - Unit dalam maintenance tidak tersedia untuk booking
   - Maintenance muncul di availability calendar (kuning)

4. **Multi-Outlet**:
   - Setiap outlet punya data terpisah (isolated)
   - Owner bisa switch outlet context
   - Plan limit: jumlah outlet maksimum

5. **Blacklist**:
   - Customer blacklist tidak bisa membuat booking
   - Wajib isi alasan pemblokiran
   - Bisa di-unblock oleh Owner/Manager

---

## 📝 FILE MAPPING

| File | Modul | Level |
|------|-------|-------|
| `admin-dashboard.html` | Admin Dashboard | Super Admin |
| `admin-outlets.html` | Manage Outlets | Super Admin |
| `admin-users.html` | Manage Users | Super Admin |
| `admin-subscriptions.html` | Subscriptions | Super Admin |
| `admin-reports.html` | System Reports | Super Admin |
| `admin-settings.html` | Super Admin Settings | Super Admin |
| `dashboard.html` | Dashboard | Outlet |
| `products.html` | Products | Outlet |
| `availability.html` | Availability | Outlet |
| `bookings.html` | Bookings List | Outlet |
| `booking-create.html` | Create Booking | Outlet |
| `customers.html` | Customers | Outlet |
| `staff.html` | Staff Management | Outlet |
| `maintenance.html` | Maintenance | Outlet |
| `outlets.html` | Outlet Management | Outlet (Owner) |
| `reports.html` | Reports | Outlet |
| `settings.html` | Settings | Outlet |
| `catalog.html` | Public Catalog | Public |
| `login.html` | Login | Auth |
| `register.html` | Register | Auth |

---

## 📌 CATATAN PENTING

### Custom Fields di Products
Products dapat memiliki **custom fields dinamis** yang dikonfigurasi dari `Settings > Tab 5: Outlet Configuration > Custom Fields`. Tipe field yang didukung:
- **Text**: Input teks bebas
- **Number**: Input angka
- **Dropdown**: Pilihan dari list
- **Date**: Tanggal
- **Checkbox**: Boolean (true/false)

Contoh penggunaan:
```javascript
customFields: {
  "brand": "Nike",           // Text
  "weight": 350,             // Number
  "condition": "Excellent",  // Dropdown
  "purchaseDate": "2024-01-10", // Date
  "isWaterproof": true       // Checkbox
}
```

### Permission System
Super Admin dapat membuat **custom roles** dengan granular permissions di `admin-settings.html > Roles & Permissions`. Total 85+ permissions tersedia, dikelompokkan dalam 7 kategori:
1. User Management (6 permissions)
2. Role Management (5 permissions)
3. Product Management (7 permissions)
4. Booking Management (8 permissions)
5. Payment Management (4 permissions)
6. Tenant Management (3 permissions)
7. Report Management (3 permissions)

---

*Dokumen ini mencakup semua modul dari **20 file HTML** di ui-design-v2 dengan detail fitur, field, alur, proses CRUD, dan relasi antar modul.*
