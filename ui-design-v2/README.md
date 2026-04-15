# POS Rental - UI Design V2 (Shadcn Style)

Modern, clean UI design untuk sistem Point of Sale rental dengan shadcn design principles.

**🔐 Multi-Tenant System dengan Super Admin Panel**

---

## 🏗️ System Architecture

### Two-Level System:

1. **Super Admin Level** (Multi-Tenant Management)
   - Manage semua outlets/tenants
   - Monitor all users across outlets
   - System analytics & reporting
   - Subscription management

2. **Outlet/Tenant Level** (Single Outlet Operations)
   - Manage produk, booking, customer
   - Staff management per outlet
   - Outlet-specific reports
   - Daily operations

---

## 🎨 Design System

### Colors (Shadcn-inspired)
- **Primary**: `slate-900` (#0F172A)
- **Secondary**: `slate-50` - `slate-200`
- **Success**: `green-600`
- **Warning**: `yellow-600`
- **Error**: `red-600`
- **Info**: `blue-600`

### Typography
- **Font**: Inter (Google Fonts)
- **Weights**: 300-800

## 📱 Pages

---

## 🔐 SUPER ADMIN PAGES (Multi-Tenant Management)

### SA-1. **Admin Dashboard** (`admin-dashboard.html`) ⭐ NEW
- **Purple gradient sidebar** dengan crown icon
- **System-wide stats**:
  - Total Outlets: 127
  - Total Users: 2,456
  - Monthly Revenue: Rp 245M
  - Active Subscriptions: 98
- **Charts**:
  - Revenue trend (6 bulan)
  - Outlet growth per bulan
- **Recent Outlets**: 4 outlet terbaru dengan status
- **System Activity Log**: Real-time activity tracking
- **Subscription Overview**: Breakdown Active/Trial/Expiring/Expired

### SA-2. **Manage Outlets** (`admin-outlets.html`) ⭐ NEW
- **CRUD outlets/tenants**
- **Stats**: Total, Active, Trial, Inactive
- **Search & Filter** by status
- **Outlet Cards** dengan:
  - Avatar, nama, ID, owner
  - Status badge (green/yellow/red)
  - Metrics: users, products, bookings
  - Actions: View, Edit
- **Modal Add Outlet**:
  - Form lengkap (nama, owner, email, phone, alamat)
  - Plan selection (Trial/Basic/Pro/Enterprise)
  - Status management

### SA-3. **Manage Users** (`admin-users.html`) ⭐ NEW
- **All users across all outlets**
- **Stats**: Total Users, Owners, Staff, Active Today
- **Multi-filter**:
  - Search by nama/email
  - Filter by outlet
  - Filter by role (Owner/Admin/Kasir)
- **Users Table** dengan kolom:
  - User (foto, nama, email)
  - Outlet (nama & ID)
  - Role badge
  - Status (Active/Inactive)
  - Last Login
  - Actions (View, Suspend)
- **Export CSV** functionality

### SA-4. **Subscriptions Management** (`admin-subscriptions.html`) ⭐ NEW
- **Billing & subscription tracking**
- **Stats**: Active (98), Trial (15), Expiring (8), Expired (6), Total MRR (Rp 18.2M)
- **Plans Overview**:
  - Trial (Free) - 15 outlets
  - Basic (Rp 99K) - 42 outlets
  - Pro (Rp 199K) - 48 outlets ⭐ Popular
  - Enterprise (Rp 499K) - 8 outlets
- **Multi-filter**: By outlet, plan, status
- **Subscriptions Table**:
  - Outlet info & plan
  - Price & status badge
  - Next billing date
  - Actions: View Invoice, Upgrade, Cancel
- **Export Billing** to CSV

### SA-5. **System Reports** (`admin-reports.html`) ⭐ NEW
- **Analytics & insights** untuk seluruh sistem
- **Report Type**: Revenue, Outlets, Users, Subscriptions
- **Period Selector**: Month/Quarter/Year/Custom
- **Summary Cards**:
  - Total Revenue: Rp 245M (+18%)
  - Total Bookings: 12,458
  - Avg per Outlet: Rp 1.93M
  - Growth Rate: +24% YoY
- **4 Interactive Charts** (Chart.js):
  - Revenue by Outlet (Bar)
  - User Growth Trend (Line)
  - Subscription Distribution (Doughnut)
  - Booking Trends 12mo (Line)
- **Top 10 Outlets Table** by revenue
- **Key Metrics**: Users (2,456), Products (8,942), Utilization (73%), Satisfaction (4.7/5)
- **Export**: PDF & Excel

### 🎨 Super Admin Design
- **Sidebar**: Purple-Indigo gradient
- **Logo**: Yellow crown icon
- **Theme**: Professional & authoritative
- **Access Control**: Super admin only
- **Total Pages**: 5 halaman ✅

---

## � PUBLIC PAGES (Customer-Facing / No Login Required)

### PUB-1. **Katalog Produk** (`catalog.html`) ⭐ NEW
**Landing page publik untuk customer lihat produk & cek ketersediaan**

- **Hero Section** dengan gradient purple
- **Sticky Navigation** dengan:
  - Logo & branding
  - Contact phone
  - WhatsApp button
- **Search & Filter**:
  - Real-time search (nama/type/desc)
  - Filter by category (Sport/Casual/Formal/Hiking)
  - Sticky filter bar
- **Product Grid** (4 kolom desktop):
  - 12 produk sample dengan real images
  - Product card dengan:
    - Image berkualitas tinggi (280px)
    - Category badge
    - Stock warning badge (jika <= 3)
    - Nama & type produk
    - Description (2 lines)
    - Available sizes (badge list)
    - Price per hari (highlight)
    - Stock count (color coded)
    - "Cek Ketersediaan" button
  - Hover effect: Lift & shadow
- **Availability Modal**:
  - Product info summary
  - Date range picker (start/end)
  - Size selector
  - Quantity input
  - Stock information
  - WhatsApp contact button
- **Footer** dengan:
  - About & social media
  - Contact info (address, phone, email)
  - Operating hours
- **WhatsApp Integration**:
  - Pre-filled message dengan detail produk
  - Direct inquiry link

#### Features:
✅ No login required
✅ 12 produk dengan 4 kategori
✅ Real-time search & filter
✅ Responsive design (mobile-first)
✅ WhatsApp quick inquiry
✅ Stock availability check
✅ Beautiful product cards with hover effects

#### Data Sample (12 Products):
```javascript
- Nike Air Max 270 (Sport) - Rp 75K
- Adidas Ultraboost (Sport) - Rp 85K
- Converse Chuck Taylor (Casual) - Rp 45K
- Vans Old Skool (Casual) - Rp 50K
- Clarks Desert Boot (Formal) - Rp 65K
- Oxford Leather Shoes (Formal) - Rp 70K
- Salomon X Ultra 3 (Hiking) - Rp 90K
- Merrell Moab 2 (Hiking) - Rp 80K
+ 4 more...
```

---

## �🏢 OUTLET/TENANT PAGES (Single Outlet Operations)

### 1. **Login** (`login.html`)
- Login form dengan demo accounts
- Link ke register
- Responsive design

### 2. **Dashboard** (`dashboard.html`)
- 4 Stats cards (Pendapatan, Booking, Produk, Overdue)
- Revenue chart (Chart.js)
- Top products
- Recent bookings table
- Quick actions
- **Modal**: Booking baru (redirect ke form)

### 3. **Products** (`products.html`)
- Product grid view
- Search & filter (kategori, status)
- Stats overview
- **Modal Add Product**: Form lengkap dengan upload foto
- **Modal View Product**: Detail produk dengan gambar & info
- **Modal Edit Product**: Edit data produk

### 4. **Availability Calendar** (`availability.html`) ⭐ NEW
- **Kalender visual** per produk dengan tampilan yang jelas
- **Color-coded availability** (Bold & Clear):
  - 🟢 **Hijau Terang** (`bg-green-100 border-green-400`): Tersedia Banyak (>70% stock)
  - � **Orange** (`bg-orange-100 border-orange-400`): Stock Terbatas (1-70% stock)
  - 🔴 **Merah** (`bg-red-200 border-red-500`): **HABIS / DIBOOKING PENUH** ← Ini yang dimaksud!
  - 🟡 **Kuning** (`bg-yellow-200 border-yellow-500`): **MAINTENANCE** ← Ini yang dimaksud!
- **Blok calendar** dengan:
  - Border tebal (2px) untuk kontras jelas
  - Background color yang mencolok
  - Icon untuk maintenance (🔧) dan fully booked (🚫)
  - Hover effect (scale-105, shadow-lg)
- **Data Realistis**:
  - Tanggal 12-14, 18-19, 25-26: **MERAH** (Fully Booked)
  - Tanggal 10, 20, 27: **KUNING** (Maintenance)
  - Tanggal 16-17, 22-24: Orange (Stock terbatas)
  - Sisanya: Hijau (Tersedia)
- **Expandable per produk** (Nike Mercurial default expanded)
- **Modal Day Detail**:
  - Alert box MERAH untuk fully booked
  - Alert box KUNING untuk maintenance
  - List booking aktif pada tanggal tersebut
  - Stats card dengan warna dinamis
- Navigate ke booking form

### 5. **Bookings** (`bookings.html`)
- List semua booking dengan table
- Filter by status
- Stats (Total, Active, Confirmed, Overdue, Completed)
- **Kolom Pembayaran**:
  - DP: Tampilkan jumlah DP & sisa
  - Lunas: Badge hijau
- **Modal Pelunasan**: Proses pelunasan saat customer ambil barang
- **Modal View**: Detail booking

### 6. **Booking Create** (`booking-create.html`)
- **3-Step Process**:
  1. **Customer Info**: Pilih/tambah customer
  2. **Produk & Date**: Pilih tanggal & produk
  3. **Pembayaran**: Pilih DP/Lunas, metode bayar
- **Modal Add Customer**: Form customer baru
- **Modal Add Product**: Pilih produk dari grid
- Real-time calculation
- Sidebar summary
- **DP System**:
  - Input jumlah DP
  - Tampilkan sisa pembayaran
  - Alert konfirmasi

### 7. **Settings** (`settings.html`) - 5 TABS
- **Tab 1 - Profil**: Edit data personal, upload foto
- **Tab 2 - Bisnis**: Info bisnis (nama, alamat, kontak)
- **Tab 3 - Pembayaran**: Metode payment aktif (Tunai, Transfer, QRIS, E-Wallet)
- **Tab 4 - Notifikasi**: Toggle notifications (Booking, Payment, Reminder, Overdue)
- **Tab 5 - Outlet Configuration** ⭐ NEW - **FIELD DINAMIS**:
  - **Category Management**: CRUD kategori dengan icon & warna custom
  - **Custom Fields**: Tambah/edit field dinamis untuk produk (Text, Number, Dropdown, Date, dll)
  - **Tax & Fees**: Atur PPN, Service Charge
  - **Booking Rules**: Min/max durasi, min DP, denda keterlambatan
  - **Operating Hours**: Jam buka/tutup per hari
  - Toggle active/inactive untuk fields
  - Modal add/edit yang fully functional

### 8. **Staff Management** (`staff.html`) ⭐ NEW
- Grid view staff
- Filter by role & status
- Stats (Total, Active, Owner, Kasir)
- **Modal Add Staff**:
  - Form lengkap (nama, email, phone, role)
  - Password untuk staff baru
  - **Permission checkboxes** (product:read, booking:create, dll)
  - Send invite email option
- **Modal Edit Staff**: Update data & permissions
- Staff detail view

## 🔥 Key Features

### ✅ Fully Functional
- **Semua tombol bekerja** - buka page atau modal
- **Semua modal berfungsi** - form bisa diisi & submit
- **Filter & search aktif** - real-time filtering
- **Modals bisa ditutup** - click backdrop atau tombol X

### 💰 DP & Pelunasan System
1. **Saat Booking**: Pilih DP/Lunas
2. **Jika DP**: Input jumlah DP
3. **View di List**: Tampilkan DP dibayar & sisa
4. **Saat Pengambilan**: Button pelunasan
5. **Modal Pelunasan**: Konfirmasi bayar sisa

### 📅 Calendar Availability
- Kalender per produk
- View ketersediaan 1 bulan
- Klik tanggal → detail booking
- Color indicators
- Expandable/collapsible

### 👥 Staff Management
- CRUD operations
- Role-based (Owner, Manager, Kasir, Inventory)
- Permission granular
- Email invitation

## 🚀 Tech Stack

```
Frontend:
├── TailwindCSS 3.0 (CDN)
├── Alpine.js 3.x
├── Chart.js
├── Font Awesome 6.4
└── Inter Font (Google Fonts)
```

## 📝 Usage

1. **Open** `index.html` di browser
2. **Navigate** ke page yang diinginkan
3. **Interact** dengan buttons/modals

### Demo Flow:
```
Login → Dashboard → Booking Baru
├── Step 1: Pilih Customer (+ Add Customer modal)
├── Step 2: Pilih Produk & Tanggal (+ Add Product modal)
└── Step 3: DP/Lunas → Konfirmasi

View Bookings → Klik tombol 💵
└── Modal Pelunasan → Konfirmasi
```

## 🎯 Components Breakdown

### Modals (All Working)
1. ✅ Add/Edit Customer
2. ✅ Add/Edit Product
3. ✅ View Product Detail
4. ✅ Add/Edit Staff
5. ✅ Pelunasan Payment
6. ✅ View Booking Detail
7. ✅ Calendar Day Detail

### Forms (All Functional)
- Customer form
- Product form (dengan upload area)
- Staff form (dengan permissions)
- Booking 3-step form
- Payment form

### Tables (All Interactive)
- Bookings table (sortable, filterable)
- Products grid (searchable)
- Staff list (filterable)

## 🎨 Shadcn-Style Elements

### Buttons
```html
<!-- Primary -->
<button class="px-4 py-2.5 bg-slate-900 text-white rounded-lg hover:bg-slate-800">

<!-- Secondary -->
<button class="px-4 py-2.5 border-2 border-slate-200 rounded-lg hover:bg-slate-50">
```

### Cards
```html
<div class="bg-white rounded-xl border border-slate-200 p-6 shadow-sm">
```

### Inputs
```html
<input class="w-full px-4 py-2.5 border border-slate-200 rounded-lg 
               focus:outline-none focus:ring-2 focus:ring-slate-900">
```

### Badges
```html
<span class="px-2.5 py-1 rounded-full text-xs font-semibold bg-green-100 text-green-800">
```

## 📊 Data Flow

### Booking dengan DP:
```
Create Booking
├── Customer: Ahmad Rizki
├── Produk: Nike (2x) + Adidas (1x)
├── Periode: 15-17 Apr (3 hari)
├── Total: Rp 406.000
├── Tipe: DP
└── DP Dibayar: Rp 200.000

View di List
├── Pembayaran: DP Rp 200.000
└── Sisa: Rp 206.000

Saat Pengambilan
├── Klik tombol 💵
├── Modal Pelunasan
├── Input metode: Tunai
└── Konfirmasi → Status: Lunas
```

## 🔧 Customization

### Update Colors
Edit TailwindCSS classes:
- `bg-slate-900` → `bg-blue-900`
- `text-slate-900` → `text-blue-900`

### Add New Page
1. Copy existing HTML
2. Update sidebar active state
3. Update content
4. Add link di index.html

### Modify Data
Semua dummy data ada di Alpine.js `x-data`:
```javascript
products: [
    { id: 1, name: 'Nike...', price: 35000, ... }
]
```

## ✨ Highlights

### 🎯 Fully Functional
- ✅ Semua button bekerja
- ✅ Modals bisa dibuka/tutup
- ✅ Forms bisa diisi
- ✅ Filter/search aktif
- ✅ Navigation works

### 📅 Visual Calendar Availability
- ✅ **BLOK MERAH** untuk tanggal fully booked/dibooking
- ✅ **BLOK KUNING** untuk tanggal maintenance
- ✅ Border tebal 2px untuk kontras maksimal
- ✅ Hover effect dengan zoom & shadow
- ✅ Icon visual (🔧 maintenance, 🚫 booked)
- ✅ Modal detail dengan alert box berwarna
- ✅ Real-time data per produk

### 💎 Production Ready Features
- ✅ DP & Pelunasan
- ✅ Calendar Availability
- ✅ Staff Management
- ✅ Permission System
- ✅ 3-Step Booking Flow
- ✅ Real-time Calculation
- ✅ Responsive Design

### 🚀 Performance
- Minimal dependencies (CDN)
- Fast load time
- No build required
- Pure HTML/CSS/JS

## 📦 File Structure

```
ui-design-v2/
├── index.html              # Landing page
├── login.html             # Auth
├── dashboard.html         # Main dashboard
├── products.html          # Product management
├── availability.html      # Calendar ⭐ NEW
├── bookings.html          # Booking list with DP
├── booking-create.html    # 3-step booking form
├── staff.html             # Staff management ⭐ NEW
└── README.md              # This file
```

## 🎓 Learn From This

### Alpine.js Patterns
- `x-data` for component state
- `x-show` for conditional rendering
- `x-model` for two-way binding
- `x-for` for loops
- `@click` for event handlers

### Modal Pattern
```javascript
showModal: false,  // State

@click="showModal = true"    // Open
@click="showModal = false"   // Close
x-show="showModal"          // Conditional render
```

### Form Handling
```javascript
formData: { name: '', email: '' },  // State

x-model="formData.name"             // Bind input
@click="saveForm()"                 // Submit
```

## 🔗 Next Steps

### Convert to Production:
1. **Backend**: Node.js + Express/NestJS
2. **Database**: PostgreSQL
3. **Auth**: JWT
4. **State**: Zustand/Redux
5. **Framework**: Next.js/React

### Integration:
```javascript
// Replace Alpine.js data
const { data, loading } = useQuery('/api/products')

// Replace alerts
toast.success('Produk berhasil ditambahkan!')

// Replace window.location
router.push('/bookings')
```

---

**Version**: 2.0  
**Created**: April 15, 2026  
**Status**: Production-Ready Mockup ✅  
**All Features**: WORKING 🚀
