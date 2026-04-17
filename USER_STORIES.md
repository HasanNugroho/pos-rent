# User Stories & Use Cases

## 👥 User Personas

### 1. Owner (Pemilik Bisnis)
**Nama**: Pak Budi  
**Bisnis**: Rental Sepatu Futsal & Olahraga  
**Pain Points**:
- Sulit tracking sepatu mana yang masih available
- Sering lupa customer belum bayar
- Laporan pendapatan masih manual pakai Excel
- Susah koordinasi sama karyawan

**Goals**:
- Sistem yang otomatis dan mudah
- Bisa monitor bisnis dari HP
- Laporan real-time

### 2. Staff/Kasir
**Nama**: Siti  
**Role**: Staff kasir  
**Pain Points**:
- Proses booking lama karena harus cek Excel
- Sering salah hitung total bayar
- Susah cari data customer lama

**Goals**:
- Proses booking cepat
- Otomatis kalkulasi harga
- Mudah cari data customer

### 3. Inventory Staff
**Nama**: Ahmad  
**Role**: Maintenance & inventory  
**Pain Points**:
- Tidak ada sistem tracking maintenance
- Sepatu yang rusak kadang masih disewakan
- Sulit tahu mana sepatu yang perlu dicuci

**Goals**:
- Schedule maintenance teratur
- Auto-block sepatu yang maintenance
- History maintenance tiap sepatu

---

## 📝 User Stories

### Epic 1: Authentication & Onboarding

#### US-001: Register Tenant Baru
**As a** business owner  
**I want to** register dan create account  
**So that** saya bisa mulai pakai sistem  

**Acceptance Criteria**:
- ✅ Form register dengan nama bisnis, email, password
- ✅ Email verification otomatis terkirim
- ✅ Auto-create tenant dengan trial 14 hari
- ✅ Auto-assign role "Owner"
- ✅ Redirect ke onboarding wizard

**Story Points**: 5

---

#### US-002: Login Staff
**As a** staff  
**I want to** login dengan email yang diinvite  
**So that** saya bisa akses sistem sesuai role saya  

**Acceptance Criteria**:
- ✅ Login dengan email & password
- ✅ Remember me option
- ✅ Redirect ke dashboard sesuai role
- ✅ Show last login info

**Story Points**: 3

---

### Epic 2: Product Management

#### US-003: Add Product
**As an** owner/manager  
**I want to** add produk baru  
**So that** produk bisa disewakan  

**Acceptance Criteria**:
- ✅ Form input lengkap (nama, SKU, harga, foto, dll)
- ✅ Upload multiple photos
- ✅ Set pricing tier (harian/mingguan/bulanan)
- ✅ Set quantity & deposit
- ✅ Auto-generate SKU jika kosong
- ✅ Success message & redirect ke product detail

**Story Points**: 8

---

#### US-004: Check Product Availability
**As a** staff  
**I want to** cek availability produk untuk tanggal tertentu  
**So that** saya tahu apakah bisa dibooking  

**Acceptance Criteria**:
- ✅ Calendar view dengan color coding
- ✅ Show available quantity per hari
- ✅ Show booking yang sudah ada
- ✅ Show maintenance schedule
- ✅ Real-time update

**Story Points**: 13

---

### Epic 3: Booking Management

#### US-005: Create Booking (POS)
**As a** staff  
**I want to** create booking untuk customer  
**So that** customer bisa sewa barang  

**Flow**:
1. Staff login
2. Search/create customer
3. Add products ke cart
4. Select date range
5. System auto-check availability
6. System kalkulasi total
7. Input payment
8. Print/send receipt

**Acceptance Criteria**:
- ✅ Quick search customer (autocomplete)
- ✅ Quick add customer inline
- ✅ Add multiple products
- ✅ Real-time availability check
- ✅ Auto-calculate pricing based on duration
- ✅ Show breakdown: subtotal, deposit, tax, total
- ✅ Multiple payment methods
- ✅ Generate booking number otomatis
- ✅ Send confirmation via email/WhatsApp
- ✅ Print receipt

**Story Points**: 21

**Example Scenario**:
```
Customer: "Saya mau sewa sepatu futsal untuk 3 hari"

Staff actions:
1. Click "New Booking"
2. Search customer "Ahmad" → Found
3. Search product "Sepatu Futsal Nike Size 42"
4. Set quantity: 1
5. Set date: 16 Apr - 18 Apr (3 hari)
6. System calculate:
   - Rental: 3 × Rp 50.000 = Rp 150.000
   - Deposit: Rp 200.000
   - Total: Rp 350.000
7. Customer bayar cash Rp 350.000
8. Click "Complete Booking"
9. Print receipt
```

---

#### US-006: Check-in (Pickup)
**As a** staff  
**I want to** process check-in saat customer pickup barang  
**So that** ada record kondisi barang saat diserahkan  

**Acceptance Criteria**:
- ✅ Scan/input booking number
- ✅ Show booking details
- ✅ Photo barang yang diserahkan
- ✅ Checklist kondisi (select: Baik/Rusak Ringan/Rusak Berat)
- ✅ Input notes
- ✅ Digital signature customer (optional)
- ✅ Update booking status → "Active"
- ✅ Reduce available stock
- ✅ Print/send receipt

**Story Points**: 13

---

#### US-007: Check-out (Return)
**As a** staff  
**I want to** process check-out saat customer return barang  
**So that** settlement bisa diselesaikan  

**Acceptance Criteria**:
- ✅ Scan/input booking number
- ✅ Show booking details & pickup condition
- ✅ Photo barang yang dikembalikan
- ✅ Compare kondisi (pickup vs return)
- ✅ Auto-calculate late fee jika overdue
- ✅ Input damage fee jika rusak
- ✅ Calculate deposit return (full/partial)
- ✅ Final settlement
- ✅ Update booking status → "Completed"
- ✅ Release stock
- ✅ Print/send receipt

**Story Points**: 13

**Example Scenario**:
```
Customer return sepatu after 3 days (on time)

Staff actions:
1. Scan booking "BK20260416001"
2. System show:
   - Pickup: 16 Apr, 09:00, Condition: Baik
   - Expected return: 18 Apr
   - Today: 18 Apr ✅ (on time)
3. Take photo sepatu yang dikembalikan
4. Check condition: Baik ✅
5. System calculate:
   - Late fee: Rp 0 (on time)
   - Damage fee: Rp 0 (kondisi baik)
   - Deposit return: Rp 200.000 (full)
6. Return deposit Rp 200.000 ke customer
7. Click "Complete Return"
8. Print receipt
9. Done!
```

---

#### US-007B: Flexible Pickup & Return untuk Semua Rental ⭐ NEW
**As a** customer  
**I want to** tentukan sendiri kapan ambil & kapan kembalikan barang  
**So that** saya punya fleksibilitas waktu sesuai kebutuhan  

**Business Context**:
Customer butuh fleksibilitas waktu pickup & return:
- Rental 2-3 Apr → Bisa pickup 1 Apr sore, return 4 Apr pagi
- Rental 2-10 Apr → Bisa pickup 1 Apr, return 11 Apr
- Event pagi → Perlu pickup malam sebelumnya
- Event selesai sore → Return paginya tidak sempat

**Acceptance Criteria**:
- ✅ Semua booking (1 hari, 2 hari, multi-hari) bisa punya custom pickup/return
- ✅ Form input di booking:
  - Date picker untuk tanggal pickup
  - Time picker untuk jam pickup
  - Date picker untuk tanggal return
  - Time picker untuk jam return
- ✅ Default behavior (jika tidak diisi):
  - Pickup datetime = Rental start date, 09:00
  - Return datetime = Rental end date, 17:00
- ✅ Validation rules:
  - Pickup datetime <= Rental start datetime
  - Return datetime >= Rental end datetime
  - Pickup datetime < Return datetime
- ✅ Show info jelas di booking detail:
  - "Sewa: 2-10 Apr 2026 (9 hari)"
  - "Pickup: 1 Apr 2026, 18:00"
  - "Return: 11 Apr 2026, 10:00"
- ✅ Availability blocking:
  - Block PENUH dari tanggal pickup sampai tanggal return
  - Contoh: Pickup 1 Apr → Rental 2-10 Apr → Return 11 Apr = Block 1-11 Apr (11 hari)
  - Charge: 9 hari (2-10 Apr)
- ✅ Late fee dihitung dari return datetime yang dipilih user
- ✅ Optional: Show badge "Custom Pickup/Return" jika berbeda dari default

**Story Points**: 8

**Example Scenario 1 - Short Rental**:
```
Customer: "Saya mau sewa sepatu 2-3 Apr (2 hari),
           tapi bisa ambil tanggal 1 Apr sore jam 18:00?
           Dan returnnya tanggal 4 Apr pagi jam 10:00?"

Staff: "Bisa! Anda bisa tentukan sendiri waktu pickup & return"

Staff actions:
1. Create booking baru
2. Select customer & product
3. Set periode sewa: 2-3 Apr 2026 (2 hari)
4. Input custom schedule:
   - Pickup: 1 Apr 2026, jam 18:00
   - Return: 4 Apr 2026, jam 10:00
5. System calculate:
   - Rental: 2 hari × Rp 50.000 = Rp 100.000 ✓
   - Deposit: Rp 200.000
   - Total: Rp 300.000
   - Availability blocked: 1-4 Apr (4 hari penuh)
6. Customer bayar Rp 300.000

Timeline:
- 1 Apr jam 18:00: Customer pickup ✓
- 2-3 Apr: Customer pakai sepatu ← Periode sewa (2 hari)
- 4 Apr jam 10:00: Customer return ✓
- Blocked: 1, 2, 3, 4 Apr (4 hari)
- Charge: Rp 100.000 (2 hari) ✓
```

**Example Scenario 2 - Long Rental**:
```
Customer: "Saya mau sewa 2-10 Apr (9 hari),
           bisa diambil 1 Apr dan dikembalikan 11 Apr?"

Staff actions:
1. Set periode sewa: 2-10 Apr 2026 (9 hari)
2. Input custom schedule:
   - Pickup: 1 Apr 2026, jam 16:00
   - Return: 11 Apr 2026, jam 09:00
3. System calculate:
   - Rental: 9 hari × Rp 50.000 = Rp 450.000 ✓
   - Blocked: 1-11 Apr (11 hari)
   - Charge: 9 hari
```

**Technical Notes**:
- Add fields `pickup_datetime: timestamp` & `return_datetime: timestamp`
- Default values:
  - `pickup_datetime` = rental_start_date + 09:00
  - `return_datetime` = rental_end_date + 17:00
- Validation: 
  - Pickup datetime <= Rental start datetime
  - Return datetime >= Rental end datetime
  - Pickup datetime < Return datetime
- Availability blocking:
  - Block PENUH dari pickup_date sampai return_date
  - Contoh: Pickup 1 Apr, Rental 2-10 Apr, Return 11 Apr = Block 11 hari (1-11 Apr)

---

### Epic 4: Customer Management

#### US-008: Quick Add Customer
**As a** staff  
**I want to** add customer dengan cepat saat booking  
**So that** proses booking tidak terhambat  

**Acceptance Criteria**:
- ✅ Inline form (modal/drawer)
- ✅ Minimal fields: nama, phone
- ✅ Optional: email, alamat, KTP
- ✅ Auto-save saat submit
- ✅ Auto-select customer yang baru dibuat

**Story Points**: 5

---

### Epic 5: Maintenance Management

#### US-009: Schedule Maintenance
**As an** inventory staff  
**I want to** schedule maintenance untuk produk  
**So that** produk tidak bisa dibooking saat maintenance  

**Acceptance Criteria**:
- ✅ Select product
- ✅ Select type (Cleaning/Repair/Inspection)
- ✅ Set start & end date
- ✅ Input estimated cost
- ✅ Upload photos (before)
- ✅ Input notes
- ✅ Auto-block availability di calendar
- ✅ Notification jika ada booking conflict

**Story Points**: 8

---

#### US-010: Complete Maintenance
**As an** inventory staff  
**I want to** mark maintenance sebagai complete  
**So that** produk available lagi untuk booking  

**Acceptance Criteria**:
- ✅ Set actual end date
- ✅ Input actual cost
- ✅ Upload photos (after)
- ✅ Input completion notes
- ✅ Update status → "Completed"
- ✅ Release product availability
- ✅ Send notification

**Story Points**: 5

---

### Epic 6: Dashboard & Reporting

#### US-011: View Dashboard
**As an** owner  
**I want to** see dashboard dengan metrics penting  
**So that** saya tahu performa bisnis  

**Acceptance Criteria**:
- ✅ Today's metrics:
  - Total bookings hari ini
  - Revenue hari ini
  - Check-ins hari ini
  - Check-outs hari ini
- ✅ This month metrics:
  - Total revenue
  - Total bookings
  - Unique customers
  - Utilization rate
- ✅ Charts:
  - Revenue trend (7/30 days)
  - Top 5 products
  - Booking status distribution
- ✅ Quick alerts:
  - Overdue rentals
  - Pending payments
  - Low stock
  - Maintenance due

**Story Points**: 13

---

#### US-012: Generate Revenue Report
**As an** owner  
**I want to** generate revenue report  
**So that** saya bisa analisa pendapatan  

**Acceptance Criteria**:
- ✅ Filter by date range
- ✅ Group by: daily/weekly/monthly
- ✅ Show:
  - Total revenue
  - Revenue by product
  - Revenue by payment method
  - Revenue by customer
- ✅ Charts/graphs
- ✅ Export to Excel/PDF

**Story Points**: 13

---

### Epic 7: Staff & Permissions

#### US-013: Invite Staff
**As an** owner  
**I want to** invite staff baru  
**So that** mereka bisa akses sistem  

**Acceptance Criteria**:
- ✅ Input email staff
- ✅ Select role
- ✅ Send invitation email
- ✅ Staff register dengan link
- ✅ Auto-assign ke tenant
- ✅ Auto-assign role

**Story Points**: 8

---

#### US-014: Custom Role & Permissions
**As an** owner  
**I want to** create custom role dengan permission spesifik  
**So that** saya bisa control akses staff  

**Acceptance Criteria**:
- ✅ Create role dengan nama custom
- ✅ Select permissions (checkboxes)
- ✅ Preview permission list
- ✅ Assign role ke user
- ✅ Edit/delete custom role (non-system role)

**Story Points**: 13

---

## 🎭 Use Case Scenarios

### Scenario 1: Customer Walk-in untuk Sewa
**Actors**: Customer, Staff

**Precondition**: Staff sudah login

**Main Flow**:
1. Customer datang ke toko
2. Customer: "Saya mau sewa sepatu futsal size 42 untuk besok 3 hari"
3. Staff: Buka "New Booking"
4. Staff: Search customer by phone → Not found
5. Staff: Click "Add Customer" inline
6. Staff: Input nama "Rizki" & phone "0812xxx"
7. Staff: Search product "Sepatu Futsal" → Found multiple
8. Staff: Filter size 42 → Show 3 items
9. Staff: Check availability 17-19 Apr → Available
10. Staff: Add to cart
11. System: Calculate total (3 days × Rp 50k = Rp 150k + deposit Rp 200k = Rp 350k)
12. Customer: Bayar cash Rp 350k
13. Staff: Input payment cash Rp 350k
14. Staff: Click "Complete Booking"
15. System: Generate booking number "BK20260416001"
16. Staff: Print receipt
17. Customer: Terima receipt & info pickup besok

**Postcondition**: Booking created, stock reserved

---

### Scenario 2: Customer Return Terlambat
**Actors**: Customer, Staff

**Precondition**: Customer punya active booking yang overdue

**Main Flow**:
1. Customer datang return sepatu (H+1 dari jadwal)
2. Staff: Scan booking "BK20260416001"
3. System: Show alert "⚠️ Overdue 1 day"
4. System: Calculate late fee: 1 × Rp 10.000 = Rp 10.000
5. Staff: Check kondisi sepatu → Baik
6. System: Calculate settlement:
   - Deposit paid: Rp 200.000
   - Late fee: Rp 10.000
   - Deposit return: Rp 190.000
7. Staff: Explain ke customer tentang late fee
8. Staff: Return cash Rp 190.000
9. Staff: Complete check-out
10. System: Send receipt via email

**Postcondition**: Booking completed, stock released, late fee recorded

---

### Scenario 3: Schedule Maintenance Rutin
**Actors**: Inventory Staff

**Precondition**: Staff login dengan role "Inventory Staff"

**Main Flow**:
1. Staff: Buka "Maintenance" menu
2. Staff: Click "Schedule Maintenance"
3. Staff: Select product "Sepatu Futsal Nike #001"
4. Staff: Select type "Cleaning"
5. Staff: Set date 20-20 Apr (1 hari)
6. Staff: Input notes "Cuci rutin setelah 10x pemakaian"
7. Staff: Upload photo kondisi sebelum cuci
8. Staff: Submit
9. System: Check booking conflicts → No conflict
10. System: Block availability 20 Apr untuk produk tersebut
11. System: Create maintenance record
12. System: Send notification ke staff terkait

**Postcondition**: Maintenance scheduled, product blocked

---

### Scenario 4: Owner Cek Laporan Bulanan
**Actors**: Owner

**Main Flow**:
1. Owner: Login dari HP
2. Owner: Buka Dashboard
3. Dashboard show:
   - Bulan ini revenue: Rp 15.000.000
   - Total booking: 85
   - Customer baru: 15
   - Top product: Sepatu Futsal Nike (30x disewa)
4. Owner: Click "Reports" → "Revenue Report"
5. Owner: Filter: Apr 2026
6. Owner: View chart revenue per hari
7. Owner: View revenue by product category
8. Owner: Click "Export to Excel"
9. System: Generate Excel file
10. Owner: Download & share ke akuntan

**Postcondition**: Report downloaded

---

## 🚨 Edge Cases

### Edge Case 1: Double Booking
**Problem**: 2 staff booking produk yang sama di waktu bersamaan

**Solution**:
- Real-time stock locking
- Optimistic locking di database
- Show error "Produk sudah dibooking oleh staff lain"
- Suggest alternative produk atau tanggal

### Edge Case 2: Customer Cancel Mendadak
**Problem**: Customer cancel booking yang sudah dibayar

**Solution**:
- Cancellation policy di settings
- Partial refund berdasarkan H-berapa cancel
- Record cancellation reason
- Release stock immediately
- Send cancellation email

### Edge Case 3: Produk Rusak Saat di Customer
**Problem**: Customer report produk rusak saat sedang disewa

**Solution**:
- Staff create maintenance record
- Set start date dari sekarang
- Update booking notes
- Damage fee bisa dicharge saat return
- Alternative: tawari produk pengganti

### Edge Case 4: Booking Overlap dengan Maintenance
**Problem**: Staff lupa ada schedule maintenance dan booking produk

**Solution**:
- System auto-check maintenance schedule
- Block booking jika conflict
- Show alert "Produk dalam maintenance 20-21 Apr"
- Suggest available dates

---

## ✅ Acceptance Testing Checklist

### Authentication
- [ ] Register dengan email baru success
- [ ] Register dengan email yang sudah ada error
- [ ] Login dengan credential benar success
- [ ] Login dengan credential salah error (3x blocked)
- [ ] Email verification link work & expire setelah 24 jam
- [ ] Forgot password kirim email
- [ ] Reset password dengan token valid work
- [ ] Refresh token work & auto-refresh
- [ ] Logout clear session

### Product
- [ ] Create product dengan data lengkap success
- [ ] Create product tanpa harga error
- [ ] Upload photo success (max 5MB)
- [ ] Availability calendar show correct data
- [ ] Update stock otomatis saat booking

### Booking
- [ ] Create booking dengan produk available success
- [ ] Create booking dengan produk unavailable error
- [ ] Auto-calculate pricing correct
- [ ] Check-in update status & reduce stock
- [ ] Check-out calculate late fee correct
- [ ] Check-out dengan damage add damage fee
- [ ] Receipt print/send success

### Maintenance
- [ ] Schedule maintenance block availability
- [ ] Complete maintenance release availability
- [ ] Conflict detection work

### Reports
- [ ] Dashboard load < 2 seconds
- [ ] Revenue report calculate correct
- [ ] Export Excel work & downloadable

---

**Document Version**: 1.0  
**Last Updated**: April 15, 2026
