# 🔄 Status Flow - Simplified (3 Status)

## 📋 Overview

Sistem booking menggunakan **3 status sederhana** untuk lifecycle booking:

1. **Dikonfirmasi** (Confirmed) - Booking dibuat, belum pickup
2. **Aktif** (Active) - Barang sudah diambil, sedang dirental
3. **Selesai** (Completed) - Barang sudah dikembalikan

---

## 🎯 Status Lifecycle

```
┌─────────────────────────────────────────────────────────┐
│                    BOOKING LIFECYCLE                     │
└─────────────────────────────────────────────────────────┘

1. Customer Booking
        ↓
   ┌────────────────┐
   │ DIKONFIRMASI   │ ← Status awal
   │   (Blue)       │
   └────────────────┘
        ↓
   Staff Proses PICKUP
   (Isi jaminan + foto kondisi awal + optional pelunasan)
        ↓
   ┌────────────────┐
   │    AKTIF       │ ← Barang sedang dirental
   │   (Green)      │
   └────────────────┘
        ↓
   Staff Proses RETURN
   (Foto kondisi akhir + late/damage fee + optional pelunasan)
        ↓
   ┌────────────────┐
   │   SELESAI      │ ← Rental completed
   │   (Gray)       │
   └────────────────┘
```

---

## 🎨 Status Details

### 1️⃣ **DIKONFIRMASI** (Confirmed)

**Badge:** Blue `bg-blue-100 text-blue-800`

**Artinya:**
- Booking sudah dibuat
- Payment sudah dibayar (full atau DP)
- Barang belum diambil customer
- Menunggu proses pickup

**Actions yang Muncul:**
```
✅ Detail (View) - Gray
✅ Edit - Blue
✅ Pickup - Purple
```

**Next Action:** Staff proses **Pickup**

---

### 2️⃣ **AKTIF** (Active)

**Badge:** Green `bg-green-100 text-green-800`

**Artinya:**
- Barang sudah diambil customer (picked up)
- Jaminan sudah diterima
- Foto kondisi awal sudah didokumentasikan
- Rental sedang berjalan
- Menunggu return

**Actions yang Muncul:**
```
✅ Detail (View) - Gray
✅ Return - Orange
```

**Next Action:** Staff proses **Return**

**Note:** Tidak ada tombol Edit karena barang sudah diambil

---

### 3️⃣ **SELESAI** (Completed)

**Badge:** Gray `bg-slate-100 text-slate-800`

**Artinya:**
- Barang sudah dikembalikan
- Foto kondisi akhir sudah didokumentasikan
- Late fee & damage fee (jika ada) sudah tercatat
- Payment sudah lunas (jika ada sisa)
- Jaminan sudah dikembalikan
- Booking selesai

**Actions yang Muncul:**
```
✅ Detail (View) - Gray
```

**Next Action:** Tidak ada, booking selesai

---

## 💰 Pelunasan Payment

**Pelunasan TIDAK ada tombol terpisah**, tapi **integrated di dalam Pickup/Return modal**.

### Opsi Pelunasan:

1. **Saat Booking** (booking-create.html)
   - Customer pilih "Pembayaran Penuh"
   - Bayar 100% langsung
   - paymentType = 'full'

2. **Saat Pickup** (di dalam Pickup Modal)
   - Jika booking dengan DP
   - Muncul section "Pelunasan Pembayaran"
   - Checkbox: "Lunasi sekarang"
   - Pilih metode pembayaran
   - Lunasi sisa DP

3. **Saat Return** (di dalam Return Modal)
   - Jika booking masih DP
   - Muncul section "Pelunasan Pembayaran"
   - Breakdown: Sisa DP + Late Fee + Damage Fee
   - Checkbox: "Lunasi sekarang"
   - Pilih metode pembayaran
   - Lunasi semua charges

---

## 📊 Action Buttons Matrix

| Status | View | Edit | Pickup | Return | Payment |
|--------|------|------|--------|--------|---------|
| **Dikonfirmasi** | ✅ | ✅ | ✅ | ❌ | *Di dalam Pickup* |
| **Aktif** | ✅ | ❌ | ❌ | ✅ | *Di dalam Return* |
| **Selesai** | ✅ | ❌ | ❌ | ❌ | ❌ (Sudah lunas) |

---

## 🔄 Workflow Examples

### Example 1: Normal Flow (Lunas Saat Pickup)

```
Step 1: Customer Booking dengan DP
├─ Total: Rp 400,000
├─ DP Paid: Rp 200,000
├─ Sisa: Rp 200,000
└─ Status: DIKONFIRMASI (Blue)
    Actions: [View] [Edit] [Pickup]

Step 2: Staff Klik "Pickup"
├─ Isi jaminan: KTP 3273xxxx
├─ Upload foto kondisi awal
├─ ☑ Lunasi sekarang
├─ Bayar sisa: Rp 200,000
├─ Metode: Cash
└─ Status: DIKONFIRMASI → AKTIF (Green)
    Payment: DP → LUNAS
    Actions: [View] [Return]

Step 3: Customer pakai barang

Step 4: Staff Klik "Return"
├─ Upload foto kondisi akhir
├─ Kondisi: Baik
├─ Late days: 0
├─ Damage fee: 0
└─ Status: AKTIF → SELESAI (Gray)
    Actions: [View]
```

### Example 2: With Late Fee (Lunas Saat Return)

```
Step 1: Customer Booking dengan DP
├─ Total: Rp 400,000
├─ DP Paid: Rp 200,000
├─ Sisa: Rp 200,000
└─ Status: DIKONFIRMASI

Step 2: Staff Pickup (Tidak Lunasi)
├─ Isi jaminan
├─ Upload foto kondisi awal
├─ ☐ Lunasi sekarang (TIDAK dicentang)
└─ Status: AKTIF
    Payment: Masih DP

Step 3: Customer terlambat 2 hari

Step 4: Staff Return
├─ Upload foto kondisi akhir
├─ Kondisi: Baik
├─ Late days: 2
├─ Late fee: 2 × Rp 50,000 = Rp 100,000
├─ Damage fee: 0
├─ Breakdown:
│   • Sisa Pembayaran: Rp 200,000
│   • Late Fee: Rp 100,000
│   • TOTAL: Rp 300,000
├─ ☑ Lunasi sekarang
├─ Bayar: Rp 300,000
└─ Status: SELESAI
    Payment: LUNAS
```

### Example 3: Full Payment Upfront

```
Step 1: Customer Booking LUNAS
├─ Total: Rp 400,000
├─ Paid: Rp 400,000
└─ Status: DIKONFIRMASI
    Payment: LUNAS

Step 2: Staff Pickup
├─ Isi jaminan
├─ Upload foto kondisi awal
├─ Tidak ada section pelunasan (sudah lunas)
└─ Status: AKTIF
    Payment: LUNAS

Step 3: Staff Return
├─ Upload foto kondisi akhir
├─ Kondisi: Baik
├─ Late days: 0
├─ Tidak ada section pelunasan
└─ Status: SELESAI
    Payment: LUNAS
```

---

## 🎨 Visual Reference

### Table View
```
┌──────────┬──────────────┬────────┬──────────────┬─────────────┬──────────────────────┐
│ Booking  │ Customer     │ Total  │ Payment      │ Status      │ Actions              │
├──────────┼──────────────┼────────┼──────────────┼─────────────┼──────────────────────┤
│ #BK-001  │ Ahmad Rizki  │ 406k   │ DP: 200k     │ AKTIF 🟢    │ [👁️] [🔄 Return]     │
│ #BK-002  │ Siti N.      │ 164k   │ Lunas        │ DIKONFIRMASI│ [👁️] [✏️] [📦 Pickup]│
│ #BK-003  │ Budi S.      │ 128k   │ DP: 50k      │ AKTIF 🟢    │ [👁️] [🔄 Return]     │
│ #BK-004  │ Maya P.      │ 850k   │ Lunas        │ DIKONFIRMASI│ [👁️] [✏️] [📦 Pickup]│
│ #BK-005  │ Dewi L.      │ 320k   │ Lunas        │ SELESAI ⚫  │ [👁️]                 │
└──────────┴──────────────┴────────┴──────────────┴─────────────┴──────────────────────┘
```

### Filter Options
```
Semua Status
├─ Dikonfirmasi (12 booking)
├─ Aktif (24 booking)
└─ Selesai (120 booking)
```

---

## 🔧 Technical Implementation

### Status Values
```javascript
status: 'confirmed'  // Dikonfirmasi - Blue badge
status: 'active'     // Aktif - Green badge
status: 'completed'  // Selesai - Gray badge
```

### Status Badge Component
```html
<span :class="{
    'bg-blue-100 text-blue-800': booking.status === 'confirmed',
    'bg-green-100 text-green-800': booking.status === 'active',
    'bg-slate-100 text-slate-800': booking.status === 'completed'
}"
x-text="
    booking.status === 'confirmed' ? 'Dikonfirmasi' : 
    booking.status === 'active' ? 'Aktif' : 
    'Selesai'
">
</span>
```

### Action Buttons Logic
```html
<!-- View - All statuses -->
<button @click="viewBooking(booking)">
    <i class="fas fa-eye"></i>
</button>

<!-- Edit - Only Dikonfirmasi -->
<template x-if="booking.status === 'confirmed'">
    <button @click="editBooking(booking)">
        <i class="fas fa-edit"></i>
    </button>
</template>

<!-- Pickup - Only Dikonfirmasi -->
<template x-if="booking.status === 'confirmed'">
    <button @click="processPickup(booking)">
        <i class="fas fa-box-open"></i>
    </button>
</template>

<!-- Return - Only Aktif -->
<template x-if="booking.status === 'active'">
    <button @click="processReturn(booking)">
        <i class="fas fa-undo"></i>
    </button>
</template>
```

### Status Updates
```javascript
// Pickup: confirmed → active
confirmPickup() {
    this.bookings[index].status = 'active';
}

// Return: active → completed
confirmReturn() {
    this.bookings[index].status = 'completed';
}
```

---

## ✅ Benefits

### 🎯 **Simplicity**
- Hanya 3 status, mudah dipahami
- Clear lifecycle: Dikonfirmasi → Aktif → Selesai
- Tidak ada status ambigu

### 👥 **User Friendly**
- Staff langsung tahu apa yang harus dilakukan
- Status badge dengan warna jelas
- Action buttons sesuai status

### 💰 **Integrated Payment**
- Pelunasan di dalam Pickup/Return flow
- Tidak perlu klik tombol terpisah
- Satu proses untuk pickup + bayar atau return + bayar

### 🔄 **Clear Workflow**
- Dikonfirmasi = Belum pickup
- Aktif = Sedang rental
- Selesai = Sudah return
- Tidak ada confusion

---

## 📱 Staff Quick Reference

**Booking Baru Masuk:**
- Status: Dikonfirmasi (Blue)
- Action: Tunggu customer datang untuk pickup

**Customer Datang Pickup:**
- Klik tombol "Pickup" (Purple)
- Isi jaminan + upload foto
- Optional: Lunasi jika masih DP
- Status berubah: Dikonfirmasi → Aktif

**Customer Return Barang:**
- Klik tombol "Return" (Orange)
- Upload foto kondisi akhir
- Input late/damage fee jika ada
- Optional: Lunasi jika masih DP + charges
- Status berubah: Aktif → Selesai

**Booking Selesai:**
- Status: Selesai (Gray)
- Hanya bisa view detail
- Tidak ada action lain

---

**Simple, Clear, Effective!** ✅🎯✨
