# 💰 Pelunasan WAJIB Saat Return

## 🚨 PERATURAN PENTING

**Saat proses Return, pelunasan pembayaran adalah WAJIB (tidak optional).**

Customer **HARUS** melunasi semua pembayaran yang outstanding sebelum return bisa dikonfirmasi:
- Sisa DP (jika booking dengan DP)
- Late Fee (jika terlambat)
- Damage Fee (jika ada kerusakan)

---

## 🎯 Kenapa WAJIB?

### ✅ **Alasan Bisnis:**

1. **Cash Flow** - Pastikan semua pembayaran lunas sebelum barang kembali
2. **Prevent Bad Debt** - Customer tidak bisa kabur sebelum bayar
3. **Clear Settlement** - Tidak ada piutang tersisa setelah return
4. **Jaminan Return** - Jaminan baru dikembalikan setelah semua lunas

### ✅ **Alasan Operasional:**

1. **Simple Process** - Satu kali proses, langsung selesai
2. **No Follow Up** - Tidak perlu kejar-kejar customer untuk bayar
3. **Clean Records** - Status booking langsung "Selesai + Lunas"

---

## 🔄 Return Flow dengan Pelunasan Wajib

```
Customer Return Barang
    ↓
Staff Buka Return Modal
    ↓
┌─────────────────────────────────────┐
│ 1. Upload Foto Kondisi Akhir        │
│ 2. Pilih Kondisi Barang             │
│ 3. Input Late Fee (jika ada)        │
│ 4. Input Damage Fee (jika ada)      │
│ 5. System Calculate Total Bayar     │
│ 6. WAJIB Pilih Metode Pembayaran    │ ← MANDATORY!
│ 7. Konfirmasi Return                │
└─────────────────────────────────────┘
    ↓
Validasi: Metode pembayaran harus dipilih
    ↓
    ├─ ✅ Sudah dipilih → Proses return
    └─ ❌ Belum dipilih → Alert error
    ↓
Customer Bayar Total
    ↓
Jaminan Dikembalikan
    ↓
Status: Aktif → Selesai ✅
Payment: LUNAS ✅
```

---

## 💰 Perhitungan Total yang Harus Dibayar

### Skenario 1: Hanya Sisa DP
```
Total Booking:     Rp 400,000
DP Dibayar:        Rp 200,000
Sisa:              Rp 200,000
Late Fee:          Rp 0
Damage Fee:        Rp 0
─────────────────────────────
TOTAL BAYAR:       Rp 200,000 ← WAJIB
```

### Skenario 2: Sisa DP + Late Fee
```
Total Booking:     Rp 400,000
DP Dibayar:        Rp 200,000
Sisa:              Rp 200,000
Late Fee:          Rp 100,000 (2 hari × Rp 50k)
Damage Fee:        Rp 0
─────────────────────────────
TOTAL BAYAR:       Rp 300,000 ← WAJIB
```

### Skenario 3: Sisa DP + Late + Damage
```
Total Booking:     Rp 400,000
DP Dibayar:        Rp 200,000
Sisa:              Rp 200,000
Late Fee:          Rp 100,000 (2 hari)
Damage Fee:        Rp 50,000
─────────────────────────────
TOTAL BAYAR:       Rp 350,000 ← WAJIB
```

### Skenario 4: Sudah Lunas + Ada Charges
```
Total Booking:     Rp 400,000
Paid:              Rp 400,000 (Lunas)
Sisa:              Rp 0
Late Fee:          Rp 50,000 (1 hari)
Damage Fee:        Rp 100,000
─────────────────────────────
TOTAL BAYAR:       Rp 150,000 ← WAJIB (charges saja)
```

### Skenario 5: Sudah Lunas + No Charges
```
Total Booking:     Rp 400,000
Paid:              Rp 400,000
Sisa:              Rp 0
Late Fee:          Rp 0
Damage Fee:        Rp 0
─────────────────────────────
TOTAL BAYAR:       Rp 0 ← Tidak ada section pelunasan
```

---

## 🎨 UI/UX - Return Modal

### Section Pelunasan (Red - MANDATORY)

```
┌─────────────────────────────────────────────┐
│ ⚠️  Pelunasan Pembayaran (WAJIB)             │
├─────────────────────────────────────────────┤
│ Total Booking:           Rp 400,000         │
│ DP Dibayar:              Rp 200,000         │
│ Sisa Pembayaran:         Rp 200,000         │
│ Additional Charges:      Rp 100,000         │
├═════════════════════════════════════════════┤
│ TOTAL YANG HARUS DIBAYAR: Rp 300,000       │
├─────────────────────────────────────────────┤
│ Metode Pembayaran *                         │
│ [Pilih Metode ▼]                            │
│   - Cash                                    │
│   - Transfer Bank                           │
│   - QRIS                                    │
└─────────────────────────────────────────────┘

[Batal]  [✓ Konfirmasi Return]
```

**Design Details:**
- **Background:** Red-50 `bg-red-50`
- **Border:** Red-300 `border-red-300`
- **Icon:** Warning Triangle (red)
- **Title:** "Pelunasan Pembayaran (WAJIB)" in bold red
- **Required Indicator:** Asterisk (*) merah
- **No Checkbox** - Tidak ada opsi "Lunasi sekarang"

---

## ⚠️ Validation Rules

### 1. **Check Total Bayar**
```javascript
const totalBayar = sisaPembayaran + lateFee + damageFee;
```

### 2. **Jika Total > 0:**
```javascript
if (totalBayar > 0 && !paymentMethod) {
    alert('Mohon pilih Metode Pembayaran!');
    return; // Block return process
}
```

### 3. **Required Fields:**
- ✅ Foto kondisi akhir (WAJIB)
- ✅ Metode pembayaran (WAJIB jika ada yang bayar)
- ⭕ Catatan return (Optional)

---

## 📱 Confirmation Message

### With Payment (Sisa DP + Charges):
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
   (Lecet ringan di ujung)

📍 Total Charge: Rp 150,000

💰 PELUNASAN (WAJIB):
Sisa Pembayaran Booking: Rp 200,000
Additional Charges: Rp 150,000
═══════════════════════════════
TOTAL DIBAYAR: Rp 350,000
Metode: CASH
Status Payment: DP → LUNAS ✅

Status: Aktif → Selesai ✅
```

### Without Payment (Sudah Lunas + No Charges):
```
✅ Return berhasil dikonfirmasi!

Booking ID: #BK-004
Customer: Maya Putri

📸 Foto kondisi akhir: Terdokumentasi
✅ Kondisi: good

✅ Tidak ada charge tambahan

Status: Aktif → Selesai ✅
```

---

## 🔐 Backend Logic

### Payment Update
```javascript
if (totalBayar > 0) {
    // Update payment status
    booking.paymentType = 'full';
    booking.dpPaid = booking.total;
    
    // Record payment transaction
    createPaymentTransaction({
        bookingId: booking.id,
        type: 'settlement',
        amount: totalBayar,
        method: paymentMethod,
        breakdown: {
            sisaDP: sisaPembayaran,
            lateFee: lateFee,
            damageFee: damageFee
        },
        timestamp: now()
    });
}
```

### Status Update
```javascript
booking.status = 'completed';
booking.returnDatetime = now();
booking.returnNotes = notes;
booking.finalCondition = itemCondition;
```

---

## 📊 Comparison: Before vs After

### ❌ **Before (Optional):**
```
Return Modal:
├─ Upload foto
├─ Input charges
├─ ☐ Lunasi sekarang (Optional checkbox)
└─ Jika tidak dicentang:
    ├─ Return bisa diproses
    ├─ Payment status masih DP
    └─ Perlu follow up payment nanti
```

**Problem:**
- Customer bisa return tanpa bayar
- Staff harus kejar-kejar pembayaran
- Bad debt risk

### ✅ **After (Mandatory):**
```
Return Modal:
├─ Upload foto
├─ Input charges
├─ System calculate total
├─ WAJIB pilih metode pembayaran
└─ Return TIDAK bisa proses jika:
    ├─ Ada yang harus dibayar (total > 0)
    └─ Tapi metode belum dipilih
```

**Benefits:**
- Semua lunas sebelum return
- No follow up needed
- Clean settlement

---

## 🎯 Staff Training Points

### **Prosedur Return:**

1. **Terima barang dari customer**
2. **Cek kondisi barang** vs foto kondisi awal
3. **Klik tombol "Return"** di booking aktif
4. **Upload foto kondisi akhir** (wajib)
5. **Input Late Fee** jika terlambat:
   - Hitung hari terlambat
   - Multiply dengan tarif per hari
6. **Input Damage Fee** jika rusak:
   - Assess kerusakan
   - Tentukan biaya perbaikan/penggantian
   - Isi deskripsi kerusakan
7. **System akan hitung total otomatis**
8. **WAJIB pilih metode pembayaran** jika ada yang bayar
9. **Terima pembayaran dari customer**
10. **Klik "Konfirmasi Return"**
11. **Kembalikan jaminan** (KTP/SIM/etc)

### **Jika Customer Tidak Bawa Uang:**

**❌ TIDAK BOLEH:**
- Skip metode pembayaran
- Konfirmasi return tanpa payment
- "Nanti bayar belakangan"

**✅ HARUS:**
```
Staff: "Pak/Bu, total yang harus dibayar: Rp XXX"
       "Silakan dibayar dulu sebelum jaminan dikembalikan"

Customer: "Saya tidak bawa uang cukup"

Staff: "Baik Pak/Bu, bisa transfer sekarang atau ambil uang dulu?"
       "Jaminan baru bisa dikembalikan setelah pembayaran lunas"

Wait for payment → Process return → Return jaminan
```

---

## ❓ FAQ

**Q: Bagaimana jika customer tidak setuju dengan late/damage fee?**
A: Tunjukkan evidence:
- Foto kondisi awal vs akhir
- Tanggal return yang dijadwalkan
- Diskusikan secara baik-baik
- Jika tidak ada kesepakatan, eskalasi ke manager

**Q: Customer sudah lunas tapi ada late fee, apakah wajib bayar sekarang?**
A: Ya! Semua charges WAJIB dibayar saat return.

**Q: Bagaimana jika customer tidak mau bayar late/damage fee?**
A: Jaminan tidak dikembalikan sampai lunas. Tahan jaminan (KTP/SIM) sampai semua lunas.

**Q: Bisa tidak proses return tanpa pembayaran?**
A: TIDAK BISA! System akan block jika metode pembayaran belum dipilih dan ada yang harus dibayar.

**Q: Bagaimana jika sudah lunas dan tidak ada charges sama sekali?**
A: Section pelunasan tidak akan muncul. Langsung proses return tanpa pembayaran.

---

## ✅ Checklist Return dengan Pelunasan

**Pre-Return:**
- [ ] Customer datang return barang
- [ ] Staff terima barang
- [ ] Cek kondisi fisik barang

**Process Return:**
- [ ] Klik tombol "Return"
- [ ] Upload foto kondisi akhir barang (multiple angles)
- [ ] Pilih kondisi barang (Baik/Rusak Ringan/Rusak Berat)
- [ ] Input hari terlambat (jika ada)
- [ ] Input damage fee (jika ada)
- [ ] Input deskripsi kerusakan (jika ada)
- [ ] Cek total yang harus dibayar
- [ ] **WAJIB pilih metode pembayaran** (jika ada yang bayar)
- [ ] Terima pembayaran dari customer
- [ ] Klik "Konfirmasi Return"
- [ ] Verify success message

**Post-Return:**
- [ ] Kembalikan jaminan customer (KTP/SIM/etc)
- [ ] Berikan struk/bukti pembayaran (jika ada)
- [ ] Thank customer
- [ ] Cek barang returned sudah dicatat

---

## 🔒 Security & Compliance

**Audit Trail:**
- Semua return dengan payment tercatat
- Timestamp payment
- Payment method recorded
- Staff who processed
- Amount breakdown

**Compliance:**
- Sesuai kebijakan cash management
- No outstanding debt
- Clear financial closure
- Customer jaminan management

---

## 📈 Benefits Summary

| Aspect | Benefit |
|--------|---------|
| **Cash Flow** | All payments settled immediately |
| **Bad Debt** | Zero risk - customer can't leave without paying |
| **Admin Work** | No follow-up needed for payment collection |
| **Customer** | Clear process - know exactly what to pay |
| **Records** | Clean closure - all bookings end with "Selesai + Lunas" |
| **Jaminan** | Clear policy - jaminan returned after full payment |

---

**REMEMBER: Pelunasan Saat Return = WAJIB, Bukan Optional!** 🚨💰✅
