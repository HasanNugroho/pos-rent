---
HTML_ANALYSIS:
- Screens found: index.html (landing page), login.html, register.html, dashboard.html, bookings.html, booking-create.html, products.html, availability.html, customers.html, maintenance.html, staff.html, reports.html, settings.html, catalog.html (public), admin-dashboard.html, admin-outlets.html, admin-users.html, admin-subscriptions.html, admin-reports.html, admin-settings.html (4 tabs: System, Roles, Pricing Plans, Email Templates)
- Entities found: Outlet/Tenant, Users, Customers, Products (with categories, sizes, units), Bookings, Maintenance Records, Subscriptions, Invoices, Payments, Roles, Permissions, Pricing Plans, Email Templates
- Forms found: Login, Register (business + owner info), Customer, Product, Booking (3-step wizard), Staff, Maintenance, Pelunasan (payment settlement), System Config, Role Management, Pricing Plan Config, Email Template Editor
- Actions found: Booking Baru, Tambah Produk, Tambah Customer, Tambah Staff, Schedule Maintenance, Proses Pickup, Proses Return, Pelunasan, Generate Invoice, Edit, Hapus, Blokir/Buka Blokir Customer, Create Custom Role, Suspend User, Export CSV
- Statuses found: Booking (Dikonfirmasi/Aktif/Selesai/Overdue), Product (Tersedia/Disewa/Maintenance), Customer (Aktif/Diblokir), Maintenance (Scheduled/In Progress/Completed), Outlet (Active/Trial/Inactive), Staff (Active/Inactive)
- Permission system: 85 permissions dalam 7 groups (user, role, product, booking, payment, tenant, report)
- Subscription tiers: Trial (14 hari), Basic (Rp 99K/bulan), Pro (Rp 199K/bulan), Enterprise (Rp 499K/bulan)
- Unclear/ambiguous: none
---

# Product Documentation - POS Rental

## 1. Overview

### Nama Produk
**POS Rental** - Sistem Point of Sale untuk Bisnis Rental/Sewa

### Deskripsi Singkat
POS Rental adalah aplikasi SaaS multi-tenant untuk mengelola bisnis rental (khususnya sepatu dan perlengkapan olahraga). Aplikasi ini menyediakan manajemen produk, booking, customer, staff, maintenance, pembayaran dengan sistem DP (Down Payment) dan pelunasan, serta laporan bisnis lengkap.

### Target Pengguna

| Pengguna | Deskripsi |
|----------|-----------|
| **Super Admin** | Administrator sistem yang mengelola multi-tenant, outlet, subscription, dan semua users across outlets |
| **Owner** | Pemilik outlet/tenant yang memiliki akses penuh ke bisnis mereka |
| **Manager** | Manajer outlet yang mengelola operasional dan staff |
| **Kasir** | Staff yang menangani transaksi booking dan pembayaran di POS |
| **Inventory Staff** | Staff yang mengelola produk, stock, dan maintenance |
| **Customer** | Pelanggan yang melihat katalog produk publik (tanpa login) |

### Scope Fitur

**Fitur Outlet/Tenant (Internal):**
- Dashboard - Ringkasan bisnis dan metrics
- Booking - Manajemen transaksi rental
- Produk - Katalog produk dengan stock management
- Ketersediaan - Kalender availability per produk
- Customer - Database pelanggan dengan blacklist system
- Maintenance - Jadwal dan tracking perawatan produk
- Staff - Manajemen akun dan permissions
- Laporan - Analisis dan reporting bisnis
- Pengaturan - Profil, bisnis, pembayaran, notifikasi, outlet, langganan

**Fitur Super Admin:**
- Dashboard - System monitoring & analytics
- Outlets - CRUD outlets/tenants
- Users - Semua users across outlets
- Subscriptions - Billing & plan management
- Reports - Analytics & insights

**Fitur Public:**
- Katalog Produk Publik - Landing page untuk customer lihat produk & cek availability

---

## 2. User Roles & Permissions

### Roles yang Teridentifikasi di HTML

| Role | Menu Access | Actions |
|------|-------------|---------|
| **Super Admin** | Dashboard, Outlets, Users, Subscriptions, Reports, Settings, Logout | View outlet detail, Edit outlet, Add outlet, Export CSV, Suspend user, View system activity, Manage subscriptions |
| **Owner** | Dashboard, Booking, Produk, Ketersediaan, Customer, Maintenance, Staff, Laporan, Pengaturan | Semua actions: Booking Baru, Tambah Produk, Tambah Customer, Tambah Staff, Schedule Maintenance, Proses Pickup, Proses Return, Pelunasan, Generate Invoice, Edit, Blokir/Buka Blokir Customer |
| **Manager** | Dashboard, Booking, Produk, Ketersediaan, Customer, Maintenance, Staff, Laporan, Pengaturan | Booking Baru, Edit Booking, View Detail, Tambah Produk, Edit Produk, Tambah Customer, Schedule Maintenance, View Reports |
| **Kasir** | Dashboard, Booking, Produk (view), Customer (view/tambah), Ketersediaan (view) | Booking Baru, Proses Pickup, Proses Return, Pelunasan, Generate Invoice, Tambah Customer |
| **Inventory Staff** | Dashboard (view), Produk, Maintenance, Ketersediaan | Tambah Produk, Edit Produk, Schedule Maintenance, Update Progress, Complete Maintenance |
| **Customer (Public)** | Katalog Produk Publik | Cek Ketersediaan, Chat WhatsApp |

### Permission Details dari UI

**Permissions yang terlihat di Staff Management:**
- `booking:create` - Buat Booking
- `booking:read` - Lihat Booking
- `booking:update` - Edit Booking
- `product:create` - Tambah Produk
- `product:read` - Lihat Produk
- `product:update` - Edit Produk
- `customer:create` - Tambah Customer
- `customer:read` - Lihat Customer
- `payment:create` - Terima Pembayaran
- `report:read` - Lihat Laporan

---

## 3. Feature List

### Navigasi Utama (Sidebar)

| Fitur | Deskripsi | Sub-fitur |
|-------|-----------|-----------|
| **Dashboard** | Ringkasan bisnis hari ini dengan metrics dan quick actions | Stats cards (Pendapatan, Booking Aktif, Produk Tersedia, Terlambat), Revenue chart, Top Products, Recent Bookings table |
| **Booking** | Manajemen transaksi rental | Stats (Total, Dikonfirmasi, Aktif, Selesai), Filters (Search, Status), Table dengan Payment info, Actions (Detail, Edit, Pickup, Return) |
| **Booking Baru** | Form 3-step wizard untuk membuat booking | Step 1: Customer (Pilih/Add), Step 2: Produk & Periode Sewa (Custom Pickup/Return time), Step 3: Pembayaran (DP/Lunas) |
| **Produk** | Katalog produk dengan stock management | Stats (Total, Tersedia, Disewa, Maintenance), Filters (Search, Kategori, Status), Grid view, Actions (Tambah, Edit, View Detail) |
| **Ketersediaan** | Kalender availability per produk | Filters (Search, Kategori), Month navigation, Calendar grid dengan status colors, Day detail modal |
| **Customer** | Database pelanggan dengan blacklist | Stats (Total, Active, Baru, Transaksi, Diblokir), Filters (Search, Status), Grid view dengan blacklist info, Actions (Detail, Chat WA, Blokir/Buka Blokir) |
| **Maintenance** | Jadwal dan tracking perawatan produk | Stats (Total, In Progress, Scheduled, Completed), Filters (Status, Jenis), Product cards dengan maintenance list, Progress tracking |
| **Staff** | Manajemen akun dan permissions | Stats (Total, Active, Owner, Kasir), Filters (Search, Role, Status), Staff cards, Actions (Edit, Detail), Permission checkboxes |
| **Laporan** | Analisis dan reporting bisnis | Period selector (Bulan/Tahun/Custom), Export (PDF/Excel), Summary cards, Charts (Revenue, Category), Tables (Top Products, Top Customers), Maintenance report |
| **Pengaturan** | Konfigurasi sistem | Tabs: Profil, Bisnis, Pembayaran, Notifikasi, Outlet, Langganan |

### Navigasi Super Admin

| Fitur | Deskripsi | Sub-fitur |
|-------|-----------|-----------|
| **Super Admin Dashboard** | System monitoring | Stats (Total Outlets, Users, Monthly Revenue, Active Subscriptions), Charts (Revenue Trend, Outlet Growth), Recent Outlets, System Activity, Subscription Overview |
| **Manage Outlets** | CRUD outlets/tenants | Stats (Total, Active, Trial, Inactive), Filters (Search, Status), Outlet cards dengan metrics, Actions (View, Edit, Add) |
| **Manage Users** | Semua users across outlets | Stats (Total, Owners, Staff, Active Today), Filters (Search, Outlet, Role), User table, Actions (View, Suspend), Export CSV |
| **Subscriptions** | Billing & plan management | Plan cards, Status tracking |
| **System Reports** | Analytics & insights | - |
| **Settings** | System configuration dengan 4 tabs | **System Configuration** (App Name, Support Email, Trial Duration, Timezone, Feature Toggles), **Roles & Permissions** (85 permissions dalam 7 groups, System & Custom roles), **Pricing Plans** (4 tiers dengan limits dan add-ons), **Email Templates** (6 event triggers, 3 channels: Email/WhatsApp/Push) |

### Fitur Publik

| Fitur | Deskripsi |
|-------|-----------|
| **Katalog Produk Publik** | Landing page untuk customer lihat produk & cek availability dengan WhatsApp integration, no login required |

---

## 4. User Journey per Role

### Super Admin Journey

1. Login sebagai Super Admin
2. Akses Dashboard untuk melihat system overview
3. Navigasi ke "Outlets" untuk melihat semua tenant
4. Bisa tambah outlet baru dengan mengisi form (Nama Outlet, Nama Owner, Email, No. HP, Alamat, Plan, Status)
5. Navigasi ke "Users" untuk melihat semua users
6. Bisa filter by outlet atau role
7. Bisa suspend user jika diperlukan
8. Navigasi ke "Subscriptions" untuk melihat billing status
9. Export data ke CSV jika diperlukan

### Owner Journey

1. Register dengan mengisi informasi bisnis (Nama Bisnis, Jenis Bisnis, No. Telepon) dan informasi pemilik (Nama, No. HP, Email, Password)
2. Login ke dashboard outlet
3. Lihat ringkasan bisnis hari ini (Pendapatan, Booking Aktif, Produk Tersedia, Terlambat)
4. Quick action: Klik "Booking Baru" untuk membuat transaksi
5. Di form Booking (3-step):
   - Step 1: Pilih customer existing atau tambah customer baru
   - Step 2: Pilih produk, set periode sewa (Tanggal Mulai & Selesai), custom waktu pickup & return (opsional)
   - Step 3: Pilih jenis pembayaran (DP atau Lunas), isi jumlah DP jika DP, pilih metode pembayaran
6. Simpan booking
7. Saat customer pickup: Buka booking, klik "Proses Pickup" (pelunasan DP)
8. Saat customer return: Buka booking, klik "Proses Return"
9. Generate invoice untuk customer
10. Kelola produk di menu "Produk" - tambah, edit, lihat detail
11. Cek ketersediaan di "Ketersediaan" - kalender interaktif
12. Kelola customer di "Customer" - tambah, blokir jika bermasalah
13. Schedule maintenance di "Maintenance"
14. Kelola staff dan permissions di "Staff"
15. Lihat laporan bisnis di "Laporan"
16. Konfigurasi outlet di "Pengaturan"

### Manager Journey

1. Login ke dashboard
2. Lihat ringkasan bisnis
3. Akses semua menu kecuali beberapa settings sensitif
4. Buat dan edit booking
5. Kelola produk dan maintenance
6. Lihat laporan untuk analisis
7. Tidak bisa mengubah subscription/billing settings

### Kasir Journey

1. Login ke dashboard
2. Fokus pada menu "Booking" dan "Customer"
3. Buat booking baru untuk customer
4. Proses pickup (pelunasan DP saat customer ambil barang)
5. Proses return saat customer mengembalikan
6. Generate invoice
7. Tambah customer baru jika belum terdaftar
8. View only untuk produk dan ketersediaan

### Inventory Staff Journey

1. Login ke dashboard
2. Akses "Produk" untuk mengelola katalog
3. Tambah produk baru dengan detail (Nama, SKU, Kategori, Size, Stock, Harga, Deskripsi, Foto)
4. Edit produk existing
5. Akses "Maintenance" untuk schedule perawatan
6. Pilih produk dan unit yang perlu maintenance
7. Pilih jenis maintenance (Cuci/Cleaning, Perbaikan, Inspeksi)
8. Set tanggal dan estimasi selesai
9. Update progress maintenance
10. Mark as complete saat selesai
11. Cek ketersediaan produk di "Ketersediaan"

### Customer (Public) Journey

1. Buka halaman Katalog Produk Publik (tanpa login)
2. Lihat daftar produk yang tersedia
3. Filter by kategori (Sport, Casual, Formal, Hiking)
4. Search produk by nama
5. Lihat detail produk: gambar, deskripsi, ukuran tersedia, harga/hari, stock
6. Klik "Cek Ketersediaan" untuk melihat kalender availability
7. Klik "Chat WhatsApp" untuk inquiry/booking via WhatsApp

---

## 5. Glossary

| Term | Definition |
|------|------------|
| **Booking** | Transaksi rental/reservasi produk oleh customer |
| **DP (Down Payment)** | Pembayaran di muka sebagian dari total harga rental |
| **Pelunasan** | Pembayaran sisa tagihan saat pickup atau return |
| **Pickup** | Proses customer mengambil barang rental (dengan pelunasan jika DP) |
| **Return** | Proses customer mengembalikan barang rental |
| **Outlet** | Tenant/bisnis individual dalam sistem multi-tenant |
| **Unit** | Satuan individu dari sebuah produk (misal: Nike Mercurial Size 42 Unit #1) |
| **SKU** | Stock Keeping Unit - kode unik produk |
| **Availability** | Status ketersediaan produk pada tanggal tertentu |
| **Blacklist** | Status customer yang diblokir karena pelanggaran (dengan alasan pemblokiran) |
| **Maintenance** | Perawatan/proses perbaikan produk (Cuci, Perbaikan, Inspeksi) |
| **Dikonfirmasi** | Status booking yang sudah dipesan tapi belum diambil |
| **Aktif** | Status booking yang sedang berjalan (sudah pickup) |
| **Selesai** | Status booking yang sudah selesai (sudah return dan lunas) |
| **Terlambat** | Status booking yang melewati tanggal return |
| **Tersedia** | Status produk yang bisa disewa |
| **Disewa** | Status produk yang sedang dalam booking aktif |
| **Trial** | Status outlet dalam masa percobaan (14 hari) |
| **Custom Pickup/Return** | Waktu pickup/return yang diatur custom (bukan default 09:00/17:00) |
| **Invoice** | Dokumen bukti pembayaran dengan riwayat pembayaran (DP + Pelunasan) |
| **QRIS** | Metode pembayaran QR code Indonesia Standard |
| **E-Wallet** | Metode pembayaran dompet digital (GoPay, OVO, Dana, dll) |
| **Utilization Rate** | Persentase produk yang aktif disewa |
| **Subscription** | Langganan berbayar untuk menggunakan sistem (Basic, Pro, Enterprise) |
| **Permission** | Hak akses spesifik untuk melakukan aksi (e.g., `booking:create`, `product:delete`) |
| **Role** | Kumpulan permissions yang diberikan ke user (Owner, Manager, Kasir, Inventory, Custom) |
| **System Role** | Role bawaan yang tidak bisa dihapus/diedit (Owner, Manager, Kasir, Inventory) |
| **Custom Role** | Role yang dibuat oleh Super Admin dengan permissions yang dipilih |
| **Maintenance Mode** | Mode sistem dimana semua tenant tidak bisa akses aplikasi (hanya Super Admin) |
| **Add-on** | Pembelian tambahan kuota diluar plan (e.g., +1 Outlet @ Rp 50K, +1 User @ Rp 15K) |
| **Template Variable** | Placeholder di email template seperti `{{user_name}}`, `{{outlet_name}}` |
| **Event Trigger** | Event yang memicu pengiriman notifikasi otomatis (e.g., booking_created, payment_success) |
| **Multi-tenancy** | Arsitektur dimana satu aplikasi melayani multiple tenant/outlet yang terisolasi |
