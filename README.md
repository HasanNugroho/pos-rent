# 🏪 SaaS Persewaan Barang - POS Rental

Platform SaaS untuk mengelola bisnis persewaan barang dengan sistem multi-tenant dan POS (Point of Sale) untuk manajemen rental yang efisien.

## 📚 Dokumentasi

Dokumentasi lengkap produk ada di file-file berikut:

1. **[PRODUCT_REQUIREMENTS.md](./PRODUCT_REQUIREMENTS.md)** - Dokumen kebutuhan produk lengkap
   - Problem statement & solution
   - User roles & permissions
   - Core features detail
   - MVP scope & timeline

2. **[DATABASE_SCHEMA.md](./DATABASE_SCHEMA.md)** - Database schema & design
   - Entity relationship
   - Table definitions
   - Indexes & performance
   - Sample queries

3. **[API_SPECIFICATION.md](./API_SPECIFICATION.md)** - API endpoints
   - Authentication
   - CRUD operations
   - Request/response examples

4. **[TECH_STACK.md](./TECH_STACK.md)** - Technology recommendations
   - Backend & frontend stack
   - Dependencies
   - Architecture diagram

5. **[USER_STORIES.md](./USER_STORIES.md)** - User stories & use cases
   - User personas
   - Detailed user stories
   - Acceptance criteria
   - Edge cases

6. **[PERMISSIONS_LIST.md](./PERMISSIONS_LIST.md)** - Permissions & role mapping
   - All permissions list
   - Default role permissions
   - Permission matrix
   - Custom role examples

7. **[DEVELOPMENT_GUIDE.md](./DEVELOPMENT_GUIDE.md)** - Development guide
   - Setup environment
   - Project structure
   - Testing & deployment

8. **[ui-design/](./ui-design/)** - UI/UX Design mockups (HTML)
   - Complete HTML pages dengan dummy data
   - Login, Dashboard, Products, Bookings, Customers, Maintenance
   - Responsive design dengan TailwindCSS
   - [View Demo](./ui-design/index.html)

9. **[ui-design-v2/](./ui-design-v2/)** - UI/UX Design V2 (RECOMMENDED) ⭐
   - **Shadcn-style design** - Modern, clean, professional
   - **SEMUA FITUR BERFUNGSI** - Buttons, modals, forms
   - **DP & Pelunasan System** - Bayar DP dulu, pelunasan saat ambil
   - **Calendar Availability** - Kalender ketersediaan per produk
   - **Staff Management** - CRUD staff dengan permissions
   - **3-Step Booking Flow** - Customer → Produk → Pembayaran
   - Full Alpine.js interactivity
   - [View Demo](./ui-design-v2/index.html)

---

## ✨ Key Features

### 🔐 Authentication
- Login, Register, Verify Email
- Forgot & Reset Password
- JWT + Refresh Token
- Multi-device support

### 🏢 Multi-Tenant
- Isolated data per tenant
- Custom subdomain (optional)
- Tenant settings & branding

### 👥 User Management
- Role-based access control (RBAC)
- Custom permissions (user:create, product:read, dll)
- Staff invitation

### 📦 Product Management
- Product catalog dengan foto
- Categories & variants
- Real-time availability tracking
- Inventory management

### 📅 Booking & Rental
- POS-style booking flow
- Check-in & check-out
- Availability calendar
- Multiple status tracking

### 👤 Customer Management
- Customer database
- Booking history
- ID verification

### 🔧 Maintenance
- Schedule maintenance
- Auto-block availability
- Maintenance history

### 💰 Payment
- Multiple payment methods
- Deposit tracking
- Late fees calculation
- Payment history

### 📊 Dashboard & Reports
- Real-time metrics
- Revenue tracking
- Product performance
- Export to Excel/PDF

---

## 🎯 Target User

- Bisnis persewaan (sepatu, kamera, alat outdoor, perlengkapan pesta)
- UMKM rental
- Toko dengan layanan rental

---

## 🚀 Roadmap

### Phase 1 - MVP (8-12 minggu)
✅ Authentication lengkap  
✅ Multi-tenant  
✅ Product & Category management  
✅ Booking & Check-in/out  
✅ Customer management  
✅ Staff & Roles  
✅ Maintenance scheduling  
✅ Payment recording  
✅ Dashboard & Basic reports  

### Phase 2 - Enhancement (6-8 minggu)
- Advanced reporting
- WhatsApp integration
- Barcode/QR scanning
- Product variants
- Email automation
- Customer portal

### Phase 3 - Scale (8-10 minggu)
- Mobile app
- Multi-location
- Advanced integrations
- Loyalty program
- Public API

---

## 💡 Use Case Example

**Rental Sepatu Futsal**

1. Customer datang ingin sewa sepatu
2. Staff login ke sistem
3. Cari/tambah customer
4. Pilih sepatu & cek availability
5. Set tanggal rental (3 hari)
6. Sistem kalkulasi: 3 hari × Rp 50.000 = Rp 150.000 + Deposit Rp 200.000
7. Customer bayar → Record payment
8. Check-in: Foto sepatu, checklist kondisi
9. Print receipt
10. Setelah 3 hari customer return
11. Check-out: Cek kondisi sepatu
12. Return deposit
13. Done!

---

## 🎨 UI/UX Principles

- **Simple & Intuitive**: Mudah digunakan tanpa training lama
- **Fast**: Quick actions untuk POS
- **Mobile-friendly**: Responsive untuk tablet & mobile
- **Keyboard shortcuts**: Speed up workflow
- **Real-time updates**: Data selalu up-to-date

---

## 🔒 Security

- Row-level security (RLS) untuk multi-tenant
- Encrypted passwords
- HTTPS only
- Rate limiting
- Input validation
- Audit logs
- Regular backups

---

## 💰 Pricing (Rencana)

**Starter** - Rp 199.000/bulan
- 1 user, 50 produk, 100 booking/bulan

**Professional** - Rp 499.000/bulan
- 5 users, unlimited produk & booking

**Enterprise** - Custom
- Unlimited everything + custom integration

**Free Trial**: 14 hari semua fitur

---

## 📞 Support

**Email**: support@yoursaas.com  
**WhatsApp**: +62 812-3456-7890  
**Docs**: https://docs.yoursaas.com

---

## 📝 License

Proprietary - All rights reserved

---

## 👨‍💻 Development

Untuk memulai development, lihat:
- [TECH_STACK.md](./TECH_STACK.md) untuk setup environment
- [DATABASE_SCHEMA.md](./DATABASE_SCHEMA.md) untuk database setup
- [API_SPECIFICATION.md](./API_SPECIFICATION.md) untuk API contract

---

**Version**: 1.0  
**Last Updated**: April 15, 2026  
**Status**: Planning Phase ✅
