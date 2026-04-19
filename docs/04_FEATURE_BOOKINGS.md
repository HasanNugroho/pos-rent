# Feature Spec — Booking & Payments

> **Module**: Bookings | **UI Pages**: `bookings.html`, `booking-create.html`
> **Priority**: P0 | **Status**: ✅ UI Complete, 🔧 Backend Needed

---

## 1. Overview

The booking system is the core transactional module. It supports a **3-step creation flow**, **down payment (DP) / full payment** options, **pelunasan (settlement)** at pickup, and full lifecycle management (confirmed → active → completed / overdue / cancelled).

---

## 2. Data Model (Prisma)

```prisma
model Booking {
  id              String        @id @default(uuid())
  tenantId        String
  bookingNumber   String        @unique  // e.g. BK-001
  customerId      String
  status          BookingStatus @default(CONFIRMED)
  startDate       DateTime
  endDate         DateTime
  totalAmount     Int
  paymentType     PaymentType   // DP or FULL
  dpAmount        Int?          // if DP
  amountPaid      Int           @default(0)
  remainingAmount Int           @default(0)
  notes           String?
  createdBy       String        // user ID
  createdAt       DateTime      @default(now())
  updatedAt       DateTime      @updatedAt

  customer        Customer      @relation(fields: [customerId], references: [id])
  items           BookingItem[]
  payments        Payment[]
  tenant          Tenant        @relation(fields: [tenantId], references: [id])

  @@index([tenantId])
  @@index([tenantId, status])
}

model BookingItem {
  id         String  @id @default(uuid())
  bookingId  String
  productId  String
  unitId     String  // specific unit assigned
  quantity   Int     @default(1)
  pricePerDay Int
  subtotal   Int

  booking    Booking @relation(fields: [bookingId], references: [id])
}

model Payment {
  id            String        @id @default(uuid())
  tenantId      String
  bookingId     String
  amount        Int
  method        PaymentMethod
  type          PaymentPurpose // DP, SETTLEMENT, FULL
  referenceNo   String?
  paidAt        DateTime      @default(now())
  createdBy     String

  booking       Booking       @relation(fields: [bookingId], references: [id])
  tenant        Tenant        @relation(fields: [tenantId], references: [id])

  @@index([tenantId])
}

enum BookingStatus {
  DRAFT
  CONFIRMED
  ACTIVE       // checked in
  COMPLETED    // returned
  OVERDUE      // past end date, not returned
  CANCELLED
}

enum PaymentType {
  DP
  FULL
}

enum PaymentMethod {
  CASH
  TRANSFER
  QRIS
  E_WALLET
}

enum PaymentPurpose {
  DOWN_PAYMENT
  SETTLEMENT
  FULL_PAYMENT
  REFUND
}
```

---

## 3. Booking Creation Flow (3 Steps)

**UI Reference**: `booking-create.html`

### Step 1: Customer Info
- Search existing customer or add new one
- Modal: add new customer (name, phone, email, ID, address)
- Selected customer displayed in summary sidebar

### Step 2: Product & Date Selection
- Select rental period (start date → end date)
- Browse / search products
- Add products to booking (modal grid with stock info)
- Auto-calculate: `quantity × pricePerDay × days = subtotal`
- Running total in sidebar

### Step 3: Payment
- Choose payment type:
  - **Full Payment (Lunas)** — pay total amount immediately
  - **Down Payment (DP)** — pay partial, settle remainder at pickup
- If DP: input DP amount, display remaining balance
- Select payment method (Cash / Transfer / QRIS / E-Wallet)
- Confirmation alert before submission

**Sidebar Summary** (persistent across steps):
- Customer name
- Date range & duration
- Product list with quantities
- Subtotals → Grand Total
- Payment breakdown

---

## 4. Booking Lifecycle

```
DRAFT → CONFIRMED → ACTIVE → COMPLETED
                  ↘ OVERDUE ↗
                  ↘ CANCELLED
```

| Transition | Trigger | Action |
|-----------|---------|--------|
| → CONFIRMED | Booking created with payment | Record DP or Full payment |
| → ACTIVE | Check-in at pickup | Verify identity, settle remaining if DP |
| → COMPLETED | Check-out / return | Mark items returned, update unit status |
| → OVERDUE | Cron job: end date passed + not returned | Send overdue notification, calculate late fees |
| → CANCELLED | Manual cancellation | Release booked units, process refund if applicable |

---

## 5. DP & Pelunasan System

### At Booking Creation:
```
Total: Rp 406,000
Type: DP
DP Paid: Rp 200,000
Remaining: Rp 206,000
```

### In Booking List (`bookings.html`):
- Payment column shows: "DP Rp 200.000" with remaining badge
- 💵 Button to trigger pelunasan modal

### Pelunasan Modal:
- Shows booking details & outstanding balance
- Select payment method
- Confirm settlement
- Status updates to ACTIVE + remaining = 0

---

## 6. Booking List & Filters

**UI Reference**: `bookings.html`

| Filter | Options |
|--------|---------|
| Status | All, Confirmed, Active, Overdue, Completed, Cancelled |
| Search | Booking number, customer name |
| Date Range | Custom date picker |

**Stats Cards**:
- Total Bookings
- Active
- Confirmed (pending pickup)
- Overdue (⚠️)
- Completed

**Table Columns**: Booking #, Customer, Products, Period, Total, Payment Status, Status, Actions

---

## 7. API Endpoints

> **Header**: All tenant-scoped endpoints require `X-MERCHANT-ID: <tenant_id>` header. See `02_FEATURE_AUTH.md` for details.

| Method | Endpoint | Permission | `X-MERCHANT-ID` | Description |
|--------|----------|-----------|:---:|-------------|
| GET | `/api/bookings` | `booking:list` | ✅ | List bookings (filtered, paginated) |
| POST | `/api/bookings` | `booking:create` | ✅ | Create booking + initial payment |
| GET | `/api/bookings/:id` | `booking:read` | ✅ | Booking detail |
| PATCH | `/api/bookings/:id` | `booking:update` | ✅ | Update booking |
| POST | `/api/bookings/:id/checkin` | `booking:checkin` | ✅ | Process check-in |
| POST | `/api/bookings/:id/checkout` | `booking:checkout` | ✅ | Process check-out / return |
| POST | `/api/bookings/:id/cancel` | `booking:cancel` | ✅ | Cancel booking |
| POST | `/api/bookings/:id/settle` | `payment:create` | ✅ | Process pelunasan |
| GET | `/api/payments` | `payment:list` | ✅ | List all payments |

---

## 8. Business Rules

| Rule | Value |
|------|-------|
| Min booking duration | Configurable per tenant (default: 1 day) |
| Max booking duration | Configurable per tenant (default: 30 days) |
| Min DP percentage | Configurable (default: 50%) |
| Late fee | Configurable per tenant (e.g., Rp 10,000/day) |
| Cancellation policy | Configurable (refund %, deadline) |
| Overdue detection | Cron every 1h: bookings past endDate with status ACTIVE |
