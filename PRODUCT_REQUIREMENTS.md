# Product Requirements Document - SaaS Persewaan Barang

## 📋 Executive Summary

Sistem SaaS untuk manajemen persewaan barang yang dirancang untuk menggantikan sistem manual berbasis Excel. Platform ini menggunakan model **multi-tenant** dengan sistem POS (Point of Sale) untuk mengelola proses persewaan barang secara efisien.

### Target User
- Bisnis persewaan (sepatu, kamera, alat outdoor, perlengkapan pesta, dll)
- UMKM yang menyewakan barang
- Toko yang memiliki layanan rental

---

## 🎯 Problem Statement

1. **Manual & Tidak Efisien**: Pengelolaan persewaan masih menggunakan Excel yang rawan error dan tidak real-time
2. **Sulit Tracking Availability**: Sulit mengetahui ketersediaan barang pada tanggal tertentu
3. **Tidak Ada Sistem Permission**: Semua orang punya akses yang sama, tidak ada pembatasan role
4. **Sulit Maintenance Management**: Tidak ada pencatatan jelas untuk maintenance/perbaikan barang
5. **Reporting Manual**: Laporan pendapatan dan statistik harus dibuat manual

---

## ✨ Solution

Platform SaaS multi-tenant dengan fitur:
- Manajemen persewaan terintegrasi
- Sistem availability real-time
- Role-based access control (RBAC)
- Tracking maintenance barang
- Dashboard analytics & reporting
- Manajemen karyawan per tenant

---

## 👥 User Roles & Permissions

### 1. Super Admin (Platform Level)
- Mengelola semua tenant
- Monitoring sistem
- Konfigurasi platform

### 2. Tenant Owner
- Full akses untuk tenant mereka
- Mengelola karyawan/staff
- Konfigurasi tenant
- Akses semua laporan
- Mengelola subscription/billing

### 3. Manager
- Mengelola produk
- Melihat semua laporan
- Mengelola maintenance
- Tidak bisa mengelola karyawan

### 4. Staff/Kasir
- Input booking baru
- Check-in/check-out
- Cek availability barang
- Terima pembayaran
- Laporan terbatas

### 5. Inventory Staff
- Mengelola barang
- Input maintenance
- Update kondisi barang
- Laporan inventory

---

## 🔐 Authentication & Authorization

### Authentication Features

#### 1. Register
- Email & password
- Nama bisnis (untuk tenant baru)
- Verifikasi email otomatis
- Auto-create tenant untuk user pertama

#### 2. Login
- Email & password
- Rate limiting (max 5 attempts)
- Remember me option
- Multi-device support

#### 3. Email Verification
- Send verification email saat register
- Resend verification email
- Link expiry 24 jam
- Auto-login setelah verify

#### 4. Forgot Password
- Request reset password via email
- Token expiry 1 jam
- Link sekali pakai

#### 5. Reset Password
- Validasi token
- Password strength requirements
- Konfirmasi password baru

#### 6. Refresh Token
- JWT access token (15 menit)
- Refresh token (7 hari)
- Auto-refresh sebelum expiry
- Revoke token saat logout

#### 7. Logout
- Clear session
- Revoke refresh token
- Multi-device logout option

### Permission System

Format: `resource:action`

**Contoh Permissions:**
```
user:create
user:read
user:update
user:delete
user:list

product:create
product:read
product:update
product:delete
product:list

booking:create
booking:read
booking:update
booking:delete
booking:cancel

payment:create
payment:read
payment:refund

maintenance:create
maintenance:read
maintenance:update
maintenance:complete

report:view-all
report:view-own
report:export

tenant:settings
tenant:billing

role:create
role:assign
```

---

## 🏢 Multi-Tenant Architecture

### Tenant Features
- Isolated data per tenant
- Custom subdomain (opsional): `{tenant-slug}.yoursaas.com`
- Branding: logo, warna tema
- Timezone & currency settings
- Business hours configuration

### Tenant Onboarding
1. Register → Create tenant
2. Setup profil bisnis
3. Add produk pertama
4. Invite karyawan (opsional)
5. Trial period 14 hari

---

## 📦 Core Features

### 1. Product Management

#### Product Master Data
```
- Nama produk
- Kategori
- SKU/Kode unik
- Deskripsi
- Foto (multiple)
- Harga sewa per hari
- Harga sewa per minggu
- Harga sewa per bulan
- Deposit (jaminan)
- Kondisi (Baru/Bekas Baik/Rusak)
- Status (Aktif/Tidak Aktif/Maintenance)
- Quantity total
- Quantity available
- Size/ukuran (untuk sepatu, pakaian)
- Spesifikasi teknis (JSON field)
- Min/max rental duration
- Late fee per hari
```

#### Product Categories
- Hierarchical categories
- Custom fields per kategori
- Icon/gambar kategori

#### Product Variants (Opsional)
- Untuk produk dengan varian (misal: sepatu size berbeda)
- Tracking availability per varian

### 2. Availability Management

#### Calendar View
- Calendar harian/mingguan/bulanan
- Color coding:
  - Hijau: Available
  - Kuning: Partially booked
  - Merah: Fully booked
  - Abu-abu: Maintenance
- Drag & drop untuk cek availability

#### Availability Rules
- Block dates (tanggal tidak bisa disewa)
- Recurring blocks (misal: setiap hari Minggu)
- Minimum notice period (misal: booking minimal H-1)
- Maximum advance booking (misal: max booking 3 bulan ke depan)

#### Real-time Stock
- Auto-update saat ada booking
- Reserved stock saat booking pending
- Release stock saat booking cancelled/expired

### 3. Booking/Rental Management

#### Booking Flow (POS Mode)
```
1. Staff pilih customer (existing/new)
2. Pilih produk & jumlah
3. Pilih tanggal mulai & selesai
4. System cek availability
5. Kalkulasi otomatis:
   - Biaya sewa
   - Deposit
   - Pajak (jika ada)
   - Total
6. Input payment
7. Print receipt/send via email/WhatsApp
```

#### Booking Status
- **Pending**: Belum bayar
- **Confirmed**: Sudah bayar, menunggu pickup
- **Active**: Sedang disewa (sudah pickup)
- **Overdue**: Melewati tanggal return
- **Returned**: Sudah dikembalikan
- **Completed**: Selesai (sudah settled)
- **Cancelled**: Dibatalkan

#### Booking Details
```
- Booking ID (auto-generated)
- Customer info
- Product(s) dengan quantity
- Tanggal & jam rental (start/end)
- Actual pickup date/time
- Actual return date/time
- Rental type: Standard / 1 Hari Fleksibel ⭐ NEW
- Kondisi barang saat pickup
- Kondisi barang saat return
- Total biaya
- Deposit
- Payment status
- Late fees (jika ada)
- Notes/catatan
- Photos (saat pickup & return)
```

#### Rental 1 Hari Fleksibel ⭐ NEW FEATURE
**Use Case**: Customer sewa produk untuk event 1 hari tapi butuh fleksibilitas pickup & return

**Fitur**:
- Toggle "Rental 1 Hari Fleksibel" saat create booking
- **Hanya untuk rental 1 hari** (tidak bisa multi-hari)
- Sistem auto-set:
  - Tanggal Sewa: Tanggal event/acara (contoh: 18 Apr)
  - Pickup Window: H-1 sore (17 Apr, 16:00-21:00)
  - Return Deadline: H+1 pagi (19 Apr, 07:00-12:00)
- **Harga tetap 1 hari** meskipun produk di customer ~36 jam
- Availability blocking: ~1.5 hari (sore H-1 + full H + pagi H+1)

**Business Benefits**:
- Meningkatkan kepuasan customer (fleksibilitas waktu)
- Tetap charge 1 hari (revenue tidak berkurang)
- Ideal untuk event: pernikahan, acara formal, pemotretan, dll
- Competitive advantage

**Calculation Example**:
```
Tanggal Sewa: 18 Apr 2026
├── Pickup: 17 Apr, 18:00 ✓
├── Event: 18 Apr (full day)
└── Return: 19 Apr, 10:00 ✓

Duration di customer: ~40 jam
Charge: 1 hari × Rp 50.000 = Rp 50.000 ✓
Deposit: Rp 200.000
Total: Rp 250.000

VS Standard Rental 3 hari:
17-19 Apr = 3 hari × Rp 50.000 = Rp 150.000 ❌
(Customer hanya pakai 1 hari tapi bayar 3 hari)
```

#### Check-in (Pickup)
- Scan/input booking ID
- Verifikasi customer identity
- Photo barang yang diserahkan
- Checklist kondisi barang
- Tanda tangan digital customer
- Print/send receipt

#### Check-out (Return)
- Scan/input booking ID
- Verifikasi kondisi barang
- Photo barang yang dikembalikan
- Kalkulasi late fee (jika overdue)
- Kalkulasi damage fee (jika rusak)
- Return deposit (full/partial)
- Final settlement
- Print/send receipt

### 4. Customer Management

#### Customer Data
```
- Nama lengkap
- Email
- No. telepon/WhatsApp
- Alamat
- ID Card/KTP (upload foto)
- Tanggal lahir
- Customer sejak (auto)
- Total bookings
- Total spent
- Customer status (Active/Blocked)
- Notes
```

#### Customer Features
- Riwayat booking
- Outstanding rentals
- Debt/piutang (jika ada)
- Customer tags/labels
- Blacklist feature

### 5. Maintenance Management

#### Maintenance Types
- **Cleaning**: Cuci/pembersihan
- **Repair**: Perbaikan
- **Inspection**: Pengecekan rutin
- **Others**: Lainnya

#### Maintenance Record
```
- Product
- Maintenance type
- Start date
- End date (estimated & actual)
- Status (Scheduled/In Progress/Completed)
- Cost
- Performed by (staff/external)
- Description/masalah
- Before photos
- After photos
- Notes
```

#### Maintenance Impact
- Produk tidak available untuk booking pada periode maintenance
- Auto-block calendar
- Notification jika ada booking request pada tanggal maintenance

#### Scheduled Maintenance
- Recurring maintenance (misal: cuci setiap 10x pemakaian)
- Reminder untuk maintenance
- Maintenance history per produk

### 6. Payment Management

#### Payment Methods
- Cash
- Transfer Bank
- E-wallet (GoPay, OVO, Dana)
- Credit/Debit Card
- QRIS

#### Payment Recording
```
- Payment date/time
- Amount
- Payment method
- Reference number
- Attachment (bukti transfer)
- Notes
```

#### Payment Features
- Partial payment support
- Payment installments
- Refund management
- Deposit tracking
- Payment history

### 7. Pricing & Billing

#### Pricing Models
- Harga per hari
- Harga per minggu (diskon otomatis)
- Harga per bulan (diskon otomatis)
- Custom pricing per customer (VIP/wholesale)

#### Dynamic Pricing (Future)
- Seasonal pricing
- Weekend/weekday pricing
- Holiday pricing

#### Auto-calculation
- Late fees otomatis
- Damage fees
- Overtime charges
- Tax calculation (jika ada)

### 8. Inventory Management

#### Stock Tracking
- Real-time stock availability
- Reserved stock (booking pending/confirmed)
- Stock on rent (sedang disewa)
- Stock in maintenance
- Stock history

#### Stock Alerts
- Low stock alert
- Overdue rentals alert
- Maintenance due alert

#### Stock Opname
- Physical count
- Stock reconciliation
- Adjustment notes
- Audit trail

### 9. Staff Management

#### Staff Data
```
- Personal info (nama, email, phone)
- Role assignment
- Permission customization
- Employment date
- Status (Active/Inactive)
- Avatar/photo
```

#### Staff Features
- Invite via email
- Set custom permissions
- Activity logs
- Performance tracking (jumlah booking, revenue generated)

### 10. Reporting & Analytics

#### Dashboard Metrics
- **Today's Overview**
  - Total booking hari ini
  - Revenue hari ini
  - Check-ins hari ini
  - Check-outs hari ini
  - Overdue rentals

- **This Month**
  - Total revenue
  - Total booking
  - Unique customers
  - Utilization rate (%)
  - Average rental duration

- **Charts**
  - Revenue trend (daily/weekly/monthly)
  - Top products (most rented)
  - Top customers
  - Booking status distribution
  - Revenue by payment method
  - Revenue by product category

#### Reports
1. **Rental Report**
   - Filter by date range, status, customer, product
   - Export to Excel/PDF

2. **Revenue Report**
   - Daily/weekly/monthly revenue
   - Revenue by product
   - Revenue by customer
   - Payment method breakdown
   - Tax report

3. **Product Performance**
   - Utilization rate per product
   - Revenue per product
   - Maintenance cost per product
   - ROI per product

4. **Customer Report**
   - Top customers by revenue
   - Top customers by frequency
   - New customers
   - Customer retention rate

5. **Inventory Report**
   - Current stock status
   - Stock movement
   - Products in maintenance
   - Products overdue

6. **Staff Performance**
   - Booking processed per staff
   - Revenue generated per staff
   - Average processing time

#### Export Options
- Excel (.xlsx)
- PDF
- CSV

### 11. Notification System

#### Notification Types
- **Booking Reminders**
  - Reminder H-1 sebelum rental start (ke customer)
  - Reminder H-1 sebelum return (ke customer)
  - Overdue notification

- **Payment Reminders**
  - Pending payment reminder
  - Payment received confirmation

- **Maintenance Alerts**
  - Maintenance due
  - Maintenance completed

- **System Notifications**
  - New booking
  - Check-in completed
  - Check-out completed
  - Low stock alert

#### Notification Channels
- In-app notification
- Email
- WhatsApp (via API) - optional
- SMS (optional)

### 12. Settings & Configuration

#### Tenant Settings
- Business info (nama, alamat, contact)
- Logo & branding
- Business hours
- Timezone
- Currency
- Tax configuration
- Receipt/invoice customization
- Booking rules (min/max duration, notice period)
- Late fee rules
- Damage fee rules

#### Email Templates
- Welcome email
- Verification email
- Password reset
- Booking confirmation
- Reminder emails
- Receipt email
- Custom templates

#### Receipt/Invoice Settings
- Header (logo, business info)
- Footer (terms & conditions)
- Custom fields
- QR code for verification

---

## 🎨 User Interface

### POS Interface
- Quick search produk
- Shopping cart style untuk add multiple items
- Real-time price calculation
- Quick actions (check-in, check-out)
- Customer search/create inline
- Keyboard shortcuts untuk speed

### Mobile Responsive
- Fully responsive untuk tablet & mobile
- Touch-friendly untuk POS pada tablet
- Offline mode (basic features) - future

### Key Screens
1. Dashboard
2. Booking List & Create
3. Product Catalog
4. Calendar/Availability View
5. Customer Management
6. Check-in/Check-out Screen
7. Maintenance Schedule
8. Reports
9. Settings

---

## 🔔 Additional Features (Nice to Have)

### 1. WhatsApp Integration
- Send booking confirmation via WA
- Send reminders via WA
- Customer service via WA

### 2. Barcode/QR Code
- QR code pada produk
- Scan untuk quick check-in/out
- QR code pada receipt

### 3. Customer Portal (Future Phase)
- Customer bisa booking sendiri online
- Lihat riwayat booking
- Pembayaran online

### 4. Mobile App
- Native app untuk POS
- Offline support

### 5. Integration
- Accounting software (Jurnal, Accurate)
- Payment gateway
- Google Calendar sync
- WhatsApp Business API

### 6. Advanced Analytics
- Predictive analytics (demand forecasting)
- Customer lifetime value
- Churn prediction

### 7. Multi-location
- Support untuk tenant dengan multiple lokasi
- Inter-location transfer

### 8. Package/Bundle
- Rental package (misal: paket wedding lengkap)
- Bundle pricing

### 9. Loyalty Program
- Point system
- Membership tiers
- Discount vouchers

### 10. Insurance
- Optional insurance untuk rental
- Claim management

---

## 🚀 MVP (Minimum Viable Product) Scope

### Phase 1 - MVP
✅ **Core Authentication**
- Login, Register, Forgot Password
- Email Verification
- JWT + Refresh Token

✅ **Multi-tenant**
- Tenant isolation
- Basic tenant settings

✅ **Product Management**
- CRUD produk
- Categories
- Upload foto
- Availability status

✅ **Booking Management**
- Create booking (POS style)
- Booking list & detail
- Check-in/Check-out
- Basic status management

✅ **Customer Management**
- CRUD customer
- Basic customer data
- Booking history

✅ **Staff Management**
- Invite staff
- Role assignment (Owner, Manager, Staff)
- Basic permissions

✅ **Availability Calendar**
- Calendar view
- Real-time availability check
- Booking blocks

✅ **Maintenance**
- Create maintenance schedule
- Auto-block availability

✅ **Payment**
- Record payment
- Cash & transfer
- Deposit management

✅ **Dashboard**
- Basic metrics
- Today's overview
- Simple charts

✅ **Reports**
- Rental report
- Revenue report
- Export to Excel

### Phase 2 - Enhancement
- Advanced reporting & analytics
- Custom permissions
- WhatsApp integration
- Barcode/QR integration
- Product variants
- Advanced pricing (seasonal, tiered)
- Email automation
- Customer portal

### Phase 3 - Scale
- Mobile app
- Multi-location
- Advanced integrations
- Loyalty program
- API for third-party

---

## 📊 Success Metrics

1. **User Adoption**
   - Number of tenants
   - Active users per tenant
   - Daily active usage

2. **Business Impact**
   - Reduction in booking errors
   - Time saved vs Excel
   - Customer satisfaction score

3. **System Performance**
   - Page load time < 2s
   - 99.9% uptime
   - Real-time availability updates

4. **Revenue**
   - Monthly recurring revenue (MRR)
   - Customer acquisition cost (CAC)
   - Lifetime value (LTV)
   - Churn rate < 5%

---

## 🛠 Technical Considerations

### Data Model (High-Level)
```
Tenants
├── Users (staff/employees)
├── Roles & Permissions
├── Products
│   ├── Categories
│   ├── Variants (optional)
│   └── Availability
├── Customers
├── Bookings
│   ├── Booking Items
│   └── Payments
├── Maintenance
└── Settings
```

### Security
- Row-level security untuk multi-tenant
- Encrypted passwords (bcrypt)
- HTTPS only
- XSS & CSRF protection
- Rate limiting
- Input validation & sanitization
- File upload validation
- Audit logs

### Performance
- Database indexing
- Caching (Redis)
- CDN untuk static assets
- Image optimization
- Lazy loading
- Pagination

### Scalability
- Horizontal scaling support
- Database sharding by tenant
- Queue system untuk async tasks
- Load balancing

---

## 📅 Timeline Estimation

**Phase 1 - MVP**: 8-12 minggu
- Week 1-2: Setup & Authentication
- Week 3-4: Multi-tenant & Product Management
- Week 5-6: Booking & Availability
- Week 7-8: Payment & Customer
- Week 9-10: Staff, Roles, Maintenance
- Week 11-12: Dashboard, Reports, Testing

**Phase 2**: 6-8 minggu
**Phase 3**: 8-10 minggu

---

## 💰 Monetization Strategy

### Pricing Tiers
1. **Starter** - Rp 199.000/bulan
   - 1 user
   - 50 produk
   - 100 booking/bulan
   - Basic reports

2. **Professional** - Rp 499.000/bulan
   - 5 users
   - Unlimited produk
   - Unlimited booking
   - Advanced reports
   - WhatsApp integration
   - Priority support

3. **Enterprise** - Custom
   - Unlimited users
   - Multi-location
   - Custom integration
   - Dedicated support
   - SLA guarantee

### Free Trial
- 14 hari trial semua fitur
- No credit card required
- Auto-downgrade to free plan (view-only) setelah trial

---

## 🎯 Competitive Advantages

1. **Specialized untuk Rental**: Fokus pada use case rental, bukan general inventory
2. **Easy to Use**: Interface intuitif, tidak perlu training lama
3. **Affordable**: Harga terjangkau untuk UMKM
4. **Local Support**: Support dalam bahasa Indonesia
5. **WhatsApp Integration**: Sesuai kebiasaan bisnis lokal
6. **Offline Capability**: Bisa tetap operasi saat internet bermasalah (future)

---

## 📝 Assumptions & Constraints

### Assumptions
- Target market: Indonesia
- Bahasa: Indonesia (default), English (optional)
- Payment: Rupiah (default)
- Internet connection available (untuk MVP)

### Constraints
- POS mode only untuk MVP (customer portal di phase 2)
- Single location per tenant untuk MVP
- Basic reporting untuk MVP

---

## ✅ Next Steps

1. **Technical Design**
   - Database schema design
   - API specification
   - Architecture diagram
   - Tech stack selection

2. **UI/UX Design**
   - Wireframes
   - User flow
   - Design system
   - Prototype

3. **Development Setup**
   - Repository setup
   - CI/CD pipeline
   - Development environment
   - Testing framework

4. **Sprint Planning**
   - Break down MVP into sprints
   - Assign tasks
   - Set milestones

---

**Document Version**: 1.0  
**Last Updated**: April 15, 2026  
**Status**: Draft - Ready for Technical Design Phase
