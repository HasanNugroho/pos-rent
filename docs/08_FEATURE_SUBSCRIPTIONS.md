# Feature Spec — Subscription & Billing

> **Module**: Subscriptions | **UI Pages**: `settings.html` (Langganan tab), `admin-subscriptions.html`, `admin-settings.html` (Pricing Plans tab)
> **Priority**: P1 | **Status**: ✅ UI Complete, 🔧 Backend Needed

---

## 1. Overview

The subscription system manages tenant billing lifecycle — from free trial to paid plans — with add-on purchases, automated renewal, and invoice generation. It has two faces: the **tenant-side** (view own plan, buy add-ons, see invoices) and the **admin-side** (manage plans, view all subscriptions, MRR tracking).

---

## 2. Data Model (Prisma)

```prisma
model Subscription {
  id              String             @id @default(uuid())
  tenantId        String             @unique
  planId          String
  status          SubscriptionStatus @default(TRIAL)
  billingCycle    BillingCycle       @default(MONTHLY)
  currentPeriodStart DateTime
  currentPeriodEnd   DateTime
  trialEndsAt     DateTime?
  cancelledAt     DateTime?
  createdAt       DateTime           @default(now())
  updatedAt       DateTime           @updatedAt

  plan            Plan               @relation(fields: [planId], references: [id])
  tenant          Tenant             @relation(fields: [tenantId], references: [id])
  addons          SubscriptionAddon[]
  invoices        Invoice[]
}

model Plan {
  id              String    @id @default(uuid())
  name            String    @unique  // Free Trial, Basic, Pro, Enterprise
  priceMonthly    Int       // in Rupiah
  priceYearly     Int?
  maxOutlets      Int?      // null = unlimited
  maxUsers        Int?
  maxProducts     Int?
  features        Json      // ["multi_branch_sync","advanced_reporting",...]
  isActive        Boolean   @default(true)
  sortOrder       Int       @default(0)
  createdAt       DateTime  @default(now())

  subscriptions   Subscription[]
}

model SubscriptionAddon {
  id              String   @id @default(uuid())
  subscriptionId  String
  addonType       AddonType
  quantity        Int      @default(1)
  pricePerUnit    Int
  activatedAt     DateTime @default(now())

  subscription    Subscription @relation(fields: [subscriptionId], references: [id])
}

model Invoice {
  id              String        @id @default(uuid())
  tenantId        String
  subscriptionId  String
  invoiceNumber   String        @unique // INV-YYYYMM-NNN
  description     String
  amount          Int
  status          InvoiceStatus @default(PENDING)
  dueDate         DateTime
  paidAt          DateTime?
  paymentMethod   String?
  paymentRef      String?       // gateway transaction ID
  createdAt       DateTime      @default(now())

  subscription    Subscription  @relation(fields: [subscriptionId], references: [id])
  tenant          Tenant        @relation(fields: [tenantId], references: [id])

  @@index([tenantId])
}

enum SubscriptionStatus {
  TRIAL
  ACTIVE
  PAST_DUE
  CANCELLED
  EXPIRED
}

enum BillingCycle {
  MONTHLY
  YEARLY
}

enum AddonType {
  EXTRA_OUTLET
  EXTRA_USER
  EXTRA_PRODUCTS
}

enum InvoiceStatus {
  PENDING
  PAID
  FAILED
  EXPIRED
  REFUNDED
}
```

---

## 3. Pricing Plans (Admin-Configured)

**UI Reference**: `admin-settings.html` → Pricing Plans tab

| Plan | Monthly | Outlets | Users | Products |
|------|---------|---------|-------|----------|
| Free Trial | Rp 0 (14 days) | 1 | 5 | 50 |
| Basic | Rp 99,000 | 1 | 10 | 500 |
| Pro ⭐ | Rp 199,000 | 3 | Unlimited | 2,000 |
| Enterprise | Rp 499,000 | Unlimited | Unlimited | Unlimited |

### Accessible Features (toggles per plan)

- Multi-Branch Sync
- Inventory Tracking
- Advanced Reporting
- API Access & Webhooks
- Custom Domain

### Add-ons

| Add-on | Price/mo |
|--------|----------|
| +1 Outlet | Rp 50,000 |
| +1 User | Rp 15,000 |
| +500 Products | Rp 25,000 |

---

## 4. Tenant-Side (User View)

**UI Reference**: `settings.html` → Langganan tab

### 4.1 Current Subscription Card

Dark gradient card showing:
- Current plan name + status badge (Aktif)
- Monthly cost
- Usage: Outlets (2/3), Users (8/∞)
- Next renewal date
- **Upgrade Plan** button
- **Beli Add-on** button → opens Add-on modal

### 4.2 Add-on Purchase Modal

- Lists available add-ons with icon, name, price/mo
- Quantity controls (−/+) per add-on
- Live total calculation
- **Bayar Sekarang** button (disabled when total = 0)
- Redirects to payment gateway (Midtrans/Xendit)

### 4.3 Billing History (Riwayat Pembayaran)

Table columns: Invoice #, Description, Amount, Date, Status, **Download Invoice**

Invoice statuses: Lunas (green), Gagal (red), Pending (yellow)

---

## 5. Admin-Side (Super Admin)

**UI Reference**: `admin-subscriptions.html`

### 5.1 Stats Cards

- Active subscriptions count
- Trial count
- Expiring soon count
- Expired count
- Total MRR

### 5.2 Subscriptions Table

- Outlet name + ID
- Plan tier (badge)
- Price
- Status
- Next billing date
- Actions: View Invoice, Upgrade, Cancel

### 5.3 Plan Management

**UI Reference**: `admin-settings.html` → Pricing Plans tab

- Edit plan details: name, price, limits
- Toggle feature access per plan
- Configure add-on pricing and availability
- Save subscription plan

---

## 6. Subscription Lifecycle

```
Registration → TRIAL (14 days)
                  ↓ (payment)
              ACTIVE
                  ↓ (renewal failed)
              PAST_DUE (grace period: 7 days)
                  ↓ (still no payment)
              EXPIRED (features locked)
                  ↓ (payment received)
              ACTIVE (restored)

              ACTIVE → CANCELLED (by user/admin)
```

### Automated Jobs (Cron)

| Job | Schedule | Action |
|-----|----------|--------|
| Trial expiry check | Daily | Notify 3d/1d before, expire on day 14 |
| Renewal billing | Daily | Charge for subscriptions due today |
| Past due check | Daily | Move ACTIVE → PAST_DUE after failed charge |
| Expiry enforcement | Daily | Move PAST_DUE → EXPIRED after grace period |

---

## 7. Payment Gateway Integration

Process subscription/addon payments via configured gateway:

1. Create invoice record (PENDING)
2. Redirect to Midtrans/Xendit payment page
3. Webhook callback → update invoice status (PAID/FAILED)
4. If PAID → activate subscription/addon

---

## 8. API Endpoints

> **Header**: All tenant-scoped endpoints require `X-MERCHANT-ID: <tenant_id>` header. Super Admin (`/api/admin/*`) endpoints do NOT require this header. See `02_FEATURE_AUTH.md` for details.

| Method | Endpoint | Scope | `X-MERCHANT-ID` | Description |
|--------|----------|-------|:---:|-------------|
| GET | `/api/subscription` | Tenant | ✅ | Current subscription |
| POST | `/api/subscription/upgrade` | Tenant | ✅ | Upgrade plan |
| POST | `/api/subscription/addons` | Tenant | ✅ | Purchase add-ons |
| GET | `/api/subscription/invoices` | Tenant | ✅ | Invoice history |
| GET | `/api/subscription/invoices/:id/download` | Tenant | ✅ | Download PDF invoice |
| GET | `/api/admin/subscriptions` | Admin | ❌ | All subscriptions |
| GET | `/api/admin/plans` | Admin | ❌ | List plans |
| POST | `/api/admin/plans` | Admin | ❌ | Create/update plan |
| PATCH | `/api/admin/subscriptions/:id` | Admin | ❌ | Modify subscription |
