# 💰 Panduan Cepat Pelunasan - Quick Reference

## 🎯 Kapan Customer Bisa Lunasi?

### 1️⃣ **Saat Booking** (Langsung Lunas)
- Customer bayar **100%** langsung
- Tidak ada sisa pembayaran
- Status: **LUNAS** ✅

### 2️⃣ **Saat Pickup Barang** (Lunasi sambil ambil barang)
- Customer sudah DP sebelumnya
- **Lunasi sisa** saat pickup
- Praktis, sekalian isi jaminan

### 3️⃣ **Saat Return Barang** (Lunasi + charges di akhir)
- Customer sudah DP sebelumnya
- **Lunasi sisa + late/damage fee** saat return
- Total bayar di akhir

---

## 📋 Checklist untuk Staff

### ✅ Proses Pickup dengan Pelunasan

```
□ Klik tombol "Proses Pickup" (icon box-open purple)
□ Isi Tipe Jaminan (KTP/SIM/Passport/Cash/Other)
□ Isi Bukti Jaminan (Nomor atau deskripsi)
□ Upload Foto Jaminan (optional)
□ Upload Foto Kondisi Awal Barang (required, bisa multiple)
□ Isi Catatan Pickup (optional)

[ JIKA BOOKING STATUS = DP ]
□ Cek muncul section "Pelunasan Pembayaran" (yellow box)
□ Lihat breakdown:
   - Total Booking: Rp XXX
   - DP Dibayar: Rp XXX
   - Sisa Pembayaran: Rp XXX
□ Centang checkbox "Lunasi sekarang" (jika customer mau bayar)
□ Pilih Metode Pembayaran (Cash/Transfer/QRIS)

□ Klik "Konfirmasi Pickup"
□ Cek alert konfirmasi
□ Status booking update: Confirmed → Picked Up
□ Payment status update: DP → LUNAS (jika centang payNow)
```

### ✅ Proses Return dengan Pelunasan

```
□ Klik tombol "Proses Return" (icon undo orange)
□ Upload Foto Kondisi Akhir Barang (required, bisa multiple)
□ Pilih Kondisi Barang (Baik/Kerusakan Ringan/Kerusakan Berat)

[ CHARGE KETERLAMBATAN ]
□ Isi Hari Terlambat (0 jika tepat waktu)
□ Tarif/Hari (default Rp 50,000)
□ Lihat Total Late Fee (auto calculate)

[ CHARGE KERUSAKAN ]
□ Isi Charge Kerusakan (0 jika tidak ada)
□ Isi Deskripsi Kerusakan (jika ada damage fee)

□ Cek Ringkasan Charge:
   - Late Fee: Rp XXX
   - Damage Fee: Rp XXX
   - Total Charge: Rp XXX

[ JIKA BOOKING STATUS = DP ]
□ Cek muncul section "Pelunasan Pembayaran" (yellow box)
□ Lihat breakdown:
   - Total Booking: Rp XXX
   - DP Dibayar: Rp XXX
   - Sisa Pembayaran: Rp XXX
   - Additional Charges: Rp XXX
   - Total yang Harus Dibayar: Rp XXX
□ Centang checkbox "Lunasi sekarang" (jika customer mau bayar)
□ Pilih Metode Pembayaran (Cash/Transfer/QRIS)

□ Isi Catatan Return (optional)
□ Klik "Konfirmasi Return"
□ Cek alert konfirmasi
□ Status booking update: Picked Up → Completed
□ Payment status update: DP → LUNAS (jika centang payNow)
```

---

## 🚨 Perhatian Penting!

### ⚠️ **Validasi Wajib**

**Saat Pickup:**
1. Tipe Jaminan HARUS diisi
2. Bukti Jaminan HARUS diisi
3. Jika centang "Lunasi sekarang" → Metode Pembayaran HARUS dipilih

**Saat Return:**
1. Foto Kondisi Akhir HARUS diupload
2. Jika centang "Lunasi sekarang" → Metode Pembayaran HARUS dipilih

### 💡 **Tips untuk Staff**

**DO:**
✅ Tanya customer: "Apakah ingin lunasi sekarang?"
✅ Jelaskan total yang harus dibayar
✅ Double check perhitungan late fee & damage fee
✅ Upload foto yang jelas dan lengkap
✅ Catat kondisi barang dengan akurat

**DON'T:**
❌ Lupa centang "Lunasi sekarang" jika customer sudah bayar
❌ Salah pilih metode pembayaran
❌ Skip foto dokumentasi
❌ Lupa isi late fee jika terlambat
❌ Lupa charge damage fee jika ada kerusakan

---

## 📊 Contoh Perhitungan

### Example 1: Pickup + Pelunasan (Tidak Terlambat)
```
Total Booking: Rp 400,000
DP Dibayar:    Rp 200,000
Sisa:          Rp 200,000

Customer ambil barang, mau lunasi:
→ Centang "Lunasi sekarang"
→ Pilih "Cash"
→ Terima Rp 200,000
→ Status: LUNAS ✅
```

### Example 2: Return + Pelunasan + Late Fee
```
Total Booking: Rp 400,000
DP Dibayar:    Rp 200,000
Sisa:          Rp 200,000

Terlambat 2 hari:
→ Late Fee: 2 × Rp 50,000 = Rp 100,000

Customer return, mau lunasi semua:
→ Centang "Lunasi sekarang"
→ Total Bayar: Rp 200,000 + Rp 100,000 = Rp 300,000
→ Pilih "Transfer"
→ Terima Rp 300,000
→ Status: LUNAS ✅
```

### Example 3: Return + Pelunasan + Late + Damage
```
Total Booking: Rp 400,000
DP Dibayar:    Rp 200,000
Sisa:          Rp 200,000

Terlambat 1 hari + Rusak Ringan:
→ Late Fee: 1 × Rp 50,000 = Rp 50,000
→ Damage Fee: Rp 100,000

Customer return, mau lunasi semua:
→ Centang "Lunasi sekarang"
→ Breakdown:
   - Sisa Pembayaran: Rp 200,000
   - Late Fee: Rp 50,000
   - Damage Fee: Rp 100,000
→ Total Bayar: Rp 350,000
→ Pilih "Cash"
→ Terima Rp 350,000
→ Status: LUNAS ✅
```

---

## 🎬 Step-by-Step Screenshots (Simulasi)

### 📸 Pickup Modal dengan Pelunasan
```
┌─────────────────────────────────────────────┐
│ Proses Pickup Barang                        │
├─────────────────────────────────────────────┤
│ Booking ID: #BK-001                         │
│ Customer: Ahmad Rizki                       │
├─────────────────────────────────────────────┤
│ 📋 Tipe Jaminan: [KTP ▼]                   │
│ 📄 Bukti Jaminan: [3273xxxxxxxxxxxx]       │
│ 📷 Foto Jaminan: [Choose File]             │
│ 📸 Foto Kondisi Awal: [Choose Files]       │
├─────────────────────────────────────────────┤
│ ⚠️  PELUNASAN PEMBAYARAN                    │
│ ┌─────────────────────────────────────────┐ │
│ │ Total Booking:     Rp 400,000          │ │
│ │ DP Dibayar:        Rp 200,000          │ │
│ │ Sisa Pembayaran:   Rp 200,000          │ │
│ │                                         │ │
│ │ ☑ Lunasi sekarang                      │ │
│ │                                         │ │
│ │ Metode Pembayaran: [Cash ▼]           │ │
│ └─────────────────────────────────────────┘ │
├─────────────────────────────────────────────┤
│ 📝 Catatan: [...]                          │
├─────────────────────────────────────────────┤
│ [Batal]  [✓ Konfirmasi Pickup]             │
└─────────────────────────────────────────────┘
```

### 📸 Return Modal dengan Pelunasan
```
┌─────────────────────────────────────────────┐
│ Proses Return Barang                        │
├─────────────────────────────────────────────┤
│ Booking ID: #BK-001                         │
│ Customer: Ahmad Rizki                       │
├─────────────────────────────────────────────┤
│ 📸 Foto Kondisi Akhir: [Choose Files]      │
│ ✅ Kondisi: [Baik ▼]                       │
├─────────────────────────────────────────────┤
│ ⏰ CHARGE KETERLAMBATAN                     │
│ Hari: [2] × Tarif: [50,000] = Rp 100,000  │
├─────────────────────────────────────────────┤
│ 🔧 CHARGE KERUSAKAN                         │
│ Damage Fee: [50,000]                        │
│ Deskripsi: [Lecet ringan di ujung...]      │
├─────────────────────────────────────────────┤
│ 📊 RINGKASAN CHARGE                         │
│ Late Fee:         Rp 100,000               │
│ Damage Fee:       Rp  50,000               │
│ Total Charge:     Rp 150,000               │
├─────────────────────────────────────────────┤
│ ⚠️  PELUNASAN PEMBAYARAN                    │
│ ┌─────────────────────────────────────────┐ │
│ │ Total Booking:        Rp 400,000       │ │
│ │ DP Dibayar:           Rp 200,000       │ │
│ │ Sisa Pembayaran:      Rp 200,000       │ │
│ │ Additional Charges:   Rp 150,000       │ │
│ │ ═══════════════════════════════════════ │ │
│ │ TOTAL HARUS DIBAYAR:  Rp 350,000       │ │
│ │                                         │ │
│ │ ☑ Lunasi sekarang                      │ │
│ │                                         │ │
│ │ Metode Pembayaran: [Transfer ▼]       │ │
│ └─────────────────────────────────────────┘ │
├─────────────────────────────────────────────┤
│ 📝 Catatan: [...]                          │
├─────────────────────────────────────────────┤
│ [Batal]  [✓ Konfirmasi Return]             │
└─────────────────────────────────────────────┘
```

---

## 📞 FAQ Staff

**Q: Bagaimana jika customer sudah DP tapi tidak mau lunasi saat pickup?**
A: Tidak masalah! Jangan centang "Lunasi sekarang", lanjutkan proses pickup seperti biasa. Customer bisa lunasi nanti (standalone payment atau saat return).

**Q: Bisa lunasi sebagian saja tidak?**
A: Untuk saat ini belum bisa partial payment. Harus lunas semua sisa pembayaran.

**Q: Jika customer mau lunasi tapi lupa bawa uang?**
A: Tidak perlu centang "Lunasi sekarang". Setelah pickup, bisa proses pelunasan terpisah via tombol "Pelunasan" di list booking.

**Q: Late fee dihitung dari tanggal apa?**
A: Dari tanggal return yang dijadwalkan. Misal: Booking 15-17 Apr, return date = 17 Apr. Jika return tanggal 19 Apr → 2 hari terlambat.

**Q: Damage fee berapa maksimalnya?**
A: Tidak ada limit. Sesuai kesepakatan dengan customer dan biaya perbaikan/penggantian.

**Q: Apakah HARUS lunasi saat return?**
A: Tidak. Additional charges (late/damage) bisa dicatat tapi payment bisa dilakukan terpisah.

**Q: Bagaimana jika customer tidak setuju dengan damage fee?**
A: Tunjukkan foto kondisi awal vs akhir. Diskusikan dengan customer. Jika tidak ada kesepakatan, eskalasi ke manager.

---

## ✅ Success Criteria

**Pickup Berhasil:**
✓ Status booking: Confirmed → Picked Up
✓ Jaminan tercatat
✓ Foto kondisi awal tersimpan
✓ Payment lunas (jika centang payNow)

**Return Berhasil:**
✓ Status booking: Picked Up → Completed
✓ Foto kondisi akhir tersimpan
✓ Charges tercatat (jika ada)
✓ Payment lunas (jika centang payNow)
✓ Jaminan dikembalikan

---

## 🎯 Quick Commands

**Untuk Customer yang Mau Lunasi Saat Pickup:**
1. "Pak/Bu, booking bapak/ibu masih ada sisa pembayaran Rp XXX. Mau lunasi sekarang?"
2. Jika iya → Centang "Lunasi sekarang"
3. "Mau bayar pakai apa? Cash, Transfer, atau QRIS?"
4. Proses pembayaran
5. "Terima kasih, pembayaran sudah lunas!"

**Untuk Customer yang Terlambat:**
1. "Pak/Bu, periode rental sudah lewat X hari"
2. "Ada biaya keterlambatan Rp XXX per hari"
3. "Total late fee: Rp XXX"
4. "Mau lunasi sekarang atau nanti?"

**Untuk Customer dengan Damage:**
1. "Pak/Bu, ada kerusakan di barang"
2. Show foto kondisi awal vs akhir
3. "Biaya perbaikan/penggantian: Rp XXX"
4. "Apakah bapak/ibu setuju?"
5. "Total yang harus dibayar: Sisa Rp XXX + Late Rp XXX + Damage Rp XXX = Rp XXX"

---

**Print & Tempel di Counter!** 📌✨
