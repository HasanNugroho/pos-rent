# UI/UX Design - POS Rental

Desain UI/UX lengkap untuk sistem manajemen persewaan barang (POS Rental) dalam bentuk HTML dengan dummy data yang realistic.

## 🎨 Pages Overview

### Authentication
- **`login.html`** - Halaman login dengan demo accounts
- **`register.html`** - Form registrasi tenant baru (trial 14 hari)

### Main Application
- **`dashboard.html`** - Dashboard utama dengan metrics, charts, dan quick actions
- **`products.html`** - List produk dengan filter dan search
- **`bookings.html`** - Manajemen booking dengan status tracking
- **`booking-create.html`** - Form pembuatan booking baru (POS style)
- **`customers.html`** - Database customer dengan statistics
- **`maintenance.html`** - Schedule dan tracking maintenance produk

### Navigation
- **`index.html`** - Landing page showcase dengan navigasi ke semua pages

## 🚀 Quick Start

1. Buka `index.html` di browser
2. Pilih page yang ingin dilihat
3. Atau langsung buka file individual (e.g., `dashboard.html`)

## 💡 Features

### ✅ Technology Stack
- **TailwindCSS** - Styling framework
- **Font Awesome 6** - Icon library
- **Chart.js** - Data visualization
- **Alpine.js** - Lightweight interactivity (booking form)

### ✅ Design Highlights
- **Responsive Design** - Mobile, tablet, dan desktop
- **Modern UI** - Clean, minimal, professional
- **Consistent Colors** - Indigo primary, semantic status colors
- **Real Dummy Data** - Realistic example data untuk semua fitur

### ✅ Components
- Sidebar Navigation
- Stats Cards
- Data Tables dengan pagination
- Form Elements
- Modal-ready structure
- Charts & Analytics
- Status Badges
- Action Buttons

## 📊 Dummy Data Examples

### Products
- Sepatu Futsal Nike Mercurial (Rp 35.000/hari)
- Sepatu Futsal Adidas Predator (Rp 32.000/hari)
- Sepatu Basket Nike Jordan (Rp 45.000/hari)
- Dan lainnya...

### Customers
- Ahmad Rizki (#CUST-001) - 15 bookings, Rp 4.5M total
- Siti Nurhaliza (#CUST-002) - 8 bookings, Rp 2.1M total
- Budi Santoso (#CUST-003) - 5 bookings, Rp 1.2M total

### Bookings
- #BK-20240415-001 - Active
- #BK-20240415-002 - Confirmed
- #BK-20240414-099 - Overdue (terlambat 1 hari)

### Maintenance
- #MAINT-001 - Cuci & Perawatan Rutin (Scheduled)
- #MAINT-002 - Perbaikan Sol (In Progress - 60%)
- #MAINT-003 - Deep Cleaning (Completed)

## 🎯 User Roles & Demo Accounts

### Owner
- Email: `owner@sepatu-rental.com`
- Password: `password123`
- Full access ke semua fitur

### Manager
- Email: `manager@sepatu-rental.com`
- Password: `password123`
- Manage operations tanpa user management

### Staff/Kasir
- Email: `staff@sepatu-rental.com`
- Password: `password123`
- POS operations (booking, payment)

## 📱 Responsive Breakpoints

- **Mobile**: < 768px
- **Tablet**: 768px - 1024px
- **Desktop**: > 1024px

## 🎨 Color Palette

### Primary Colors
- **Indigo 600**: `#4F46E5` - Primary actions, navigation
- **Gray 900**: `#111827` - Sidebar, text headers
- **White**: `#FFFFFF` - Backgrounds

### Status Colors
- **Green**: Available, Active, Success
- **Blue**: Confirmed, Info
- **Yellow**: Maintenance, Warning
- **Red**: Overdue, Error, Delete
- **Purple**: Categories, Special

## 📐 Layout Structure

```
┌─────────────────────────────────────┐
│ Sidebar (Fixed, w-64)               │
│ ┌───────────────────────────────┐  │
│ │ Header (h-16)                 │  │
│ ├───────────────────────────────┤  │
│ │                               │  │
│ │ Main Content (p-6)            │  │
│ │                               │  │
│ │ - Stats Cards                 │  │
│ │ - Filters & Actions           │  │
│ │ - Data Grid/Table             │  │
│ │ - Pagination                  │  │
│ │                               │  │
│ └───────────────────────────────┘  │
└─────────────────────────────────────┘
```

## 🔧 Customization

### Update Colors
Edit TailwindCSS classes di setiap file:
- `bg-indigo-600` → ganti dengan warna lain
- `text-green-600` → untuk status colors

### Add New Pages
1. Copy template dari existing page
2. Update sidebar active state
3. Update content area
4. Add link di `index.html`

### Modify Data
Search & replace dummy data di masing-masing file:
- Customer names
- Product names
- Prices
- Dates

## 📝 Next Steps (Development)

1. **Convert to React/Next.js** - Component-based architecture
2. **Connect to API** - Replace dummy data dengan real data
3. **Add State Management** - Zustand atau Redux
4. **Implement Auth** - JWT authentication
5. **Add Real Charts** - Connect Chart.js ke real data
6. **Form Validation** - React Hook Form + Zod
7. **Add Modals** - Create/Edit dalam modal
8. **Implement Search** - Real-time search & filtering
9. **Add Pagination** - Server-side pagination
10. **Print/Export** - Receipt, reports PDF/Excel

## 🌟 Best Practices Used

- **Mobile-First Design**
- **Semantic HTML**
- **Accessible Components** (ARIA labels)
- **Consistent Spacing** (Tailwind spacing scale)
- **Hover States** pada interactive elements
- **Clear Visual Hierarchy**
- **Intuitive Navigation**
- **Status Indicators** yang jelas
- **Action Buttons** dengan icons

## 📄 License

Desain ini dibuat untuk dokumentasi produk POS Rental.

---

**Created**: April 15, 2026  
**Version**: 1.0  
**Framework**: TailwindCSS + HTML
