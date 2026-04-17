# Payment Settlement Flow - Pelunasan Pembayaran

## 📋 Overview

Sistem pelunasan pembayaran booking yang fleksibel dengan **3 pilihan waktu pelunasan**:

1. **Saat Booking** (sudah ada) - Full payment langsung
2. **Saat Pickup Barang** (NEW) - Lunasi saat pengambilan barang
3. **Saat Return Barang** (NEW) - Lunasi saat pengembalian barang + additional charges

---

## 🔄 Payment Flow Options

### Option 1: Full Payment (Saat Booking)
```
Customer Booking
    ↓
Pilih "Pembayaran Penuh"
    ↓
Bayar 100% langsung
    ↓
Status: LUNAS ✅
    ↓
Pickup Barang (tanpa pembayaran lagi)
    ↓
Return Barang (hanya charges jika ada)
```

**Use Case:**
- Customer punya budget cukup
- Ingin langsung selesai pembayaran
- Tidak ribet saat pickup

---

### Option 2: DP + Payment at Pickup (NEW)
```
Customer Booking
    ↓
Pilih "DP 50%"
    ↓
Bayar DP (misal Rp 200k dari Rp 400k)
    ↓
Status: DP (Sisa Rp 200k)
    ↓
PICKUP BARANG
    ↓
Staff centang "Lunasi sekarang"
    ↓
Bayar sisa Rp 200k
    ↓
Status: LUNAS ✅
    ↓
Return Barang (hanya charges jika ada)
```

**Use Case:**
- Customer mau lunasi saat ambil barang
- Lebih praktis, sambil isi jaminan
- Sekalian proses pickup

**Form Flow:**
1. Staff klik tombol "Proses Pickup"
2. Isi jaminan (KTP/SIM/etc)
3. Upload foto kondisi awal
4. Muncul section "Pelunasan Pembayaran" (jika paymentType = 'dp')
5. Centang checkbox "Lunasi sekarang"
6. Pilih metode pembayaran (Cash/Transfer/QRIS)
7. Konfirmasi pickup
8. Payment status updated: DP → LUNAS

---

### Option 3: DP + Payment at Return (NEW)
```
Customer Booking
    ↓
Pilih "DP 50%"
    ↓
Bayar DP (misal Rp 200k dari Rp 400k)
    ↓
Status: DP (Sisa Rp 200k)
    ↓
Pickup Barang (tanpa pelunasan)
    ↓
Return Barang
    ↓
Staff centang "Lunasi sekarang"
    ↓
Bayar: Sisa Rp 200k + Charges (late/damage)
    ↓
Status: LUNAS ✅
```

**Use Case:**
- Customer mau lunasi saat kembalikan barang
- Bisa sekalian dengan additional charges
- Total dibayar di akhir

**Form Flow:**
1. Staff klik tombol "Proses Return"
2. Upload foto kondisi akhir
3. Isi kondisi barang
4. Hitung late fee (jika ada)
5. Hitung damage fee (jika ada)
6. Muncul section "Pelunasan Pembayaran" (jika paymentType = 'dp')
7. Show breakdown:
   - Sisa Pembayaran Booking: Rp 200,000
   - Additional Charges (Late + Damage): Rp 50,000
   - **Total yang Harus Dibayar: Rp 250,000**
8. Centang checkbox "Lunasi sekarang"
9. Pilih metode pembayaran
10. Konfirmasi return
11. Payment status updated: DP → LUNAS

---

## 💡 Standalone Payment Option (Tetap Ada)

Staff masih bisa proses pelunasan terpisah (sebelum pickup/return) melalui tombol "Pelunasan" di action column.

**Use Case:**
- Customer mau bayar sebelum pickup, tapi tidak bersamaan
- Transfer di hari sebelumnya
- Pelunasan terpisah dari pickup/return flow

---

## 📊 Comparison Table

| Opsi | Timing | Sisa Pembayaran | Additional Charges | Total Dibayar |
|------|--------|-----------------|-------------------|---------------|
| **Full Payment** | Saat booking | Rp 0 | - | Total booking |
| **DP + Pay at Pickup** | Saat pickup | Sisa DP | - | Sisa DP |
| **DP + Pay at Return** | Saat return | Sisa DP | Late + Damage | Sisa DP + Charges |
| **Standalone Payment** | Kapan saja | Sisa DP | - | Sisa DP |

---

## 🎯 UI Components

### 1. Pickup Modal - Payment Section

```html
<!-- Muncul jika paymentType === 'dp' -->
<div class="p-4 bg-yellow-50 border-2 border-yellow-300 rounded-lg">
    <!-- Header dengan Checkbox -->
    <div class="flex items-center justify-between">
        <p class="font-semibold">Pelunasan Pembayaran</p>
        <label>
            <input type="checkbox" x-model="pickupForm.payNow">
            <span>Lunasi sekarang</span>
        </label>
    </div>
    
    <!-- Breakdown -->
    <div class="space-y-2">
        <div>Total Booking: Rp 400,000</div>
        <div>DP Dibayar: Rp 200,000</div>
        <div class="font-bold">Sisa Pembayaran: Rp 200,000</div>
    </div>
    
    <!-- Payment Method (conditional) -->
    <template x-if="pickupForm.payNow">
        <select x-model="pickupForm.paymentMethod">
            <option>Cash</option>
            <option>Transfer Bank</option>
            <option>QRIS</option>
        </select>
    </template>
</div>
```

### 2. Return Modal - Payment Section

```html
<!-- Muncul jika paymentType === 'dp' -->
<div class="p-4 bg-yellow-50 border-2 border-yellow-300 rounded-lg">
    <!-- Header dengan Checkbox -->
    <div class="flex items-center justify-between">
        <p class="font-semibold">Pelunasan Pembayaran</p>
        <label>
            <input type="checkbox" x-model="returnForm.payNow">
            <span>Lunasi sekarang</span>
        </label>
    </div>
    
    <!-- Breakdown -->
    <div class="space-y-2">
        <div>Total Booking: Rp 400,000</div>
        <div>DP Dibayar: Rp 200,000</div>
        <div>Sisa Pembayaran: Rp 200,000</div>
        <div>Additional Charges: Rp 50,000</div>
        <div class="font-bold">Total yang Harus Dibayar: Rp 250,000</div>
    </div>
    
    <!-- Payment Method (conditional) -->
    <template x-if="returnForm.payNow">
        <select x-model="returnForm.paymentMethod">
            <option>Cash</option>
            <option>Transfer Bank</option>
            <option>QRIS</option>
        </select>
    </template>
</div>
```

---

## 🔧 JavaScript Variables

```javascript
pickupForm: {
    collateralType: '',
    collateralProof: '',
    notes: '',
    payNow: false,           // NEW: Checkbox untuk pelunasan
    paymentMethod: ''        // NEW: Metode pembayaran
}

returnForm: {
    itemCondition: 'good',
    lateDays: 0,
    lateRatePerDay: 50000,
    damageFee: 0,
    damageDescription: '',
    notes: '',
    payNow: false,           // NEW: Checkbox untuk pelunasan
    paymentMethod: ''        // NEW: Metode pembayaran
}
```

---

## ✅ Validation Rules

### Pickup Payment
```javascript
if (pickupForm.payNow && !pickupForm.paymentMethod) {
    alert('Mohon pilih Metode Pembayaran untuk pelunasan!');
    return;
}
```

### Return Payment
```javascript
if (returnForm.payNow && !returnForm.paymentMethod) {
    alert('Mohon pilih Metode Pembayaran untuk pelunasan!');
    return;
}
```

---

## 📱 Confirmation Messages

### Pickup dengan Pelunasan
```
✅ Pickup berhasil dikonfirmasi!

Booking ID: #BK-001
Customer: Ahmad Rizki

📋 Jaminan:
Tipe: KTP
Bukti: 3273xxxxxxxxxxxx

📸 Foto kondisi awal barang telah didokumentasikan

💰 PELUNASAN:
Sisa Pembayaran: Rp 200,000
Metode: CASH
Status Payment: DP → LUNAS ✅

Status: Confirmed → Picked Up
```

### Return dengan Pelunasan + Charges
```
✅ Return berhasil dikonfirmasi!

Booking ID: #BK-001
Customer: Ahmad Rizki

📸 Foto kondisi akhir: Terdokumentasi
✅ Kondisi: minor_damage

💰 Additional Charges:
⏰ Late Fee: 2 hari × Rp 50,000
   = Rp 100,000
🔧 Damage Fee: Rp 50,000
   (Sedikit lecet di bagian ujung)

📍 Total Charge: Rp 150,000

💰 PELUNASAN:
Sisa Pembayaran Booking: Rp 200,000
Additional Charges: Rp 150,000
Total Dibayar: Rp 350,000
Metode: TRANSFER
Status Payment: DP → LUNAS ✅

Status: Picked Up → Completed
```

---

## 🎨 Visual Indicators

**Payment Status Badge:**
- **DP** → Yellow badge "DP: Rp 200,000" + "Sisa: Rp 200,000"
- **Lunas** → Green badge "Lunas"

**Yellow Section:**
- Background: `bg-yellow-50`
- Border: `border-2 border-yellow-300`
- Icon: `fas fa-exclamation-circle text-yellow-600`

**Checkbox:**
- Color: Green (`text-green-600`)
- Label: "Lunasi sekarang"

---

## 💾 Data Update Logic

### After Pickup with Payment
```javascript
if (pickupForm.payNow) {
    bookings[index].paymentType = 'full';
    bookings[index].dpPaid = bookings[index].total;
}
```

### After Return with Payment
```javascript
if (returnForm.payNow) {
    bookings[index].paymentType = 'full';
    bookings[index].dpPaid = bookings[index].total;
}
```

---

## 🚀 Benefits

✅ **Fleksibilitas Tinggi**
- Customer bisa pilih kapan mau lunasi
- Staff bisa sesuaikan dengan kondisi

✅ **Integrated Flow**
- Pickup + Payment dalam 1 form
- Return + Payment + Charges dalam 1 form
- Tidak perlu buka modal terpisah

✅ **Clear Breakdown**
- Transparan semua biaya
- Customer tahu berapa harus bayar
- Staff tidak bingung hitung

✅ **Efficient**
- Kurangi step proses
- Satu kali klik untuk pickup + bayar
- Atau return + bayar semua charges

---

## 📊 Use Case Examples

### Example 1: Pay at Pickup
**Scenario:** Customer Ahmad booking dengan DP, mau lunasi saat ambil barang

1. Booking: Total Rp 400k, DP Rp 200k (Sisa Rp 200k)
2. Pickup Day:
   - Staff klik "Proses Pickup"
   - Isi jaminan: KTP 3273xxxx
   - Upload foto kondisi awal
   - **Centang "Lunasi sekarang"**
   - Pilih metode: Cash
   - Konfirmasi
3. Result: Status → Picked Up, Payment → LUNAS

### Example 2: Pay at Return with Late Fee
**Scenario:** Customer Budi terlambat 2 hari, mau lunasi saat return

1. Booking: Total Rp 400k, DP Rp 200k (Sisa Rp 200k)
2. Pickup: Tidak lunasi (masih DP)
3. Return Day (2 hari terlambat):
   - Staff klik "Proses Return"
   - Upload foto kondisi akhir
   - Input Late Days: 2
   - Late Fee: 2 × Rp 50k = Rp 100k
   - **Centang "Lunasi sekarang"**
   - Breakdown:
     - Sisa Pembayaran: Rp 200k
     - Late Fee: Rp 100k
     - **Total Bayar: Rp 300k**
   - Pilih metode: Transfer
   - Konfirmasi
4. Result: Status → Completed, Payment → LUNAS

### Example 3: Pay at Return with Damage + Late
**Scenario:** Customer rusak barang + terlambat

1. Booking: Total Rp 400k, DP Rp 200k
2. Pickup: Tidak lunasi
3. Return (terlambat 1 hari, rusak ringan):
   - Late Days: 1 → Rp 50k
   - Damage Fee: Rp 100k
   - **Centang "Lunasi sekarang"**
   - Breakdown:
     - Sisa Pembayaran: Rp 200k
     - Late Fee: Rp 50k
     - Damage Fee: Rp 100k
     - **Total Bayar: Rp 350k**
   - Metode: Cash
4. Result: Completed + Lunas

---

## 🔐 Security & Validation

✅ **Required Validations:**
1. Jika `payNow = true`, `paymentMethod` harus dipilih
2. Payment method harus salah satu: Cash/Transfer/QRIS
3. Total calculation harus akurat

✅ **Data Integrity:**
- Status payment update atomic dengan status booking
- DP amount update ke total jika lunas
- paymentType update: 'dp' → 'full'

---

## 📈 Future Enhancements

🔮 **Possible Improvements:**
1. **Partial Payment** - Bayar sebagian saat pickup, sisanya saat return
2. **Payment History** - Track semua transaksi payment
3. **Receipt Generation** - Auto generate receipt PDF
4. **Payment Gateway Integration** - Online payment support
5. **Reminder System** - Auto reminder untuk pelunasan
6. **Multi-Currency** - Support USD/EUR

---

## ✅ Summary

**3 Opsi Pelunasan:**
1. ✅ Saat Booking (Full payment)
2. ✅ Saat Pickup (NEW - DP + settle at pickup)
3. ✅ Saat Return (NEW - DP + settle at return + charges)

**Key Features:**
- Integrated dalam Pickup/Return flow
- Clear breakdown semua biaya
- Flexible payment timing
- Automatic status update
- Comprehensive confirmation messages

**Ready for Production!** 🚀💰✨
