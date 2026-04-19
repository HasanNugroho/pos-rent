# Feature Spec — Settings & Configuration

> **Module**: Settings | **UI Page**: `settings.html`
> **Priority**: P1 | **Status**: ✅ UI Complete, 🔧 Backend Needed

---

## 1. Overview

Tenant-level settings allow outlet owners and authorized staff to manage their profile, business info, payment methods, notification preferences, outlet configuration, and subscription. This is separated from the Super Admin settings which control platform-wide configuration.

---

## 2. Settings Tabs

### Tab 1: Profil

**Permission**: Any authenticated user (own profile)

| Field | Type |
|-------|------|
| Avatar/Photo | File upload (R2) |
| Full Name | Text |
| Email | Email (read-only or change with verification) |
| Phone | Text |
| Password | Change password form |

---

### Tab 2: Bisnis (Business)

**Permission**: `tenant:settings`

| Field | Type |
|-------|------|
| Business Name | Text |
| Business Address | Textarea |
| Phone | Text |
| Email | Email |
| Tax ID (NPWP) | Text |
| Logo | File upload (R2) |

---

### Tab 3: Pembayaran (Payment Methods)

**Permission**: `tenant:settings`

Configure which payment methods the outlet accepts:

| Method | Toggle | Fields |
|--------|--------|--------|
| Tunai (Cash) | ✅ | — |
| Transfer Bank | ✅ | Bank name, account number, account holder |
| QRIS | ✅ | QRIS image upload |
| E-Wallet | ✅ | Provider, number |

---

### Tab 4: Notifikasi (Notifications)

**Permission**: `tenant:settings`

Toggle per notification type:

| Notification | Email | Push | WhatsApp |
|-------------|-------|------|----------|
| New Booking | ✅ | ✅ | ❌ |
| Payment Received | ✅ | ✅ | ❌ |
| Booking Reminder | ✅ | ❌ | ✅ |
| Overdue Alert | ✅ | ✅ | ✅ |
| Maintenance Update | ❌ | ✅ | ❌ |

---

### Tab 5: Outlet Configuration

**Permission**: `tenant:settings`

#### Categories

- CRUD product categories
- Each category: name, icon (FontAwesome), color (hex)
- Used for product filtering

#### Custom Fields

Dynamic fields added to product forms:

| Config | Options |
|--------|---------|
| Field Name | Text |
| Field Type | Text / Number / Dropdown / Date / Checkbox |
| Options (if dropdown) | Comma-separated values |
| Required | Toggle |
| Active | Toggle |

#### Tax & Fees

| Setting | Type |
|---------|------|
| PPN (VAT) | Percentage (e.g., 11%) |
| Service Charge | Percentage or fixed |
| Apply tax to bookings | Toggle |

#### Booking Rules

| Rule | Type | Default |
|------|------|---------|
| Min rental duration | Number (days) | 1 |
| Max rental duration | Number (days) | 30 |
| Min DP percentage | Percentage | 50% |
| Late fee per day | Fixed (Rp) | 10,000 |
| Cancellation policy | Dropdown | Full refund / Partial / No refund |

#### Operating Hours

| Day | Open | Close | Closed? |
|-----|------|-------|---------|
| Senin | 08:00 | 21:00 | ❌ |
| Selasa | 08:00 | 21:00 | ❌ |
| ... | ... | ... | ... |
| Minggu | 09:00 | 18:00 | ❌ |

---

### Tab 6: Langganan (Subscription)

**Permission**: `tenant:billing`

See full spec in `08_FEATURE_SUBSCRIPTIONS.md`. Summary:

- **Current plan card**: Plan name, status, price, usage (outlets/users), renewal date
- **Upgrade Plan** button
- **Beli Add-on** → modal with add-on quantity controls and live total
- **Billing history table** with download invoice action per row

---

## 3. Outlet Management (Owner)

**Permission**: `tenant:settings` (Owner role)

Owners with multi-outlet plans (Pro/Enterprise) can manage their branches:

### 3.1 Outlet List

| Column | Description |
|--------|-------------|
| Outlet Name | Branch name |
| Address | Location |
| Status | Active / Inactive |
| Created | Date |
| Staff Count | Number of assigned users |
| Actions | Edit, Deactivate, Switch |

### 3.2 Add Outlet

| Field | Type | Required |
|-------|------|----------|
| Outlet Name | Text | ✅ |
| Address | Textarea | ✅ |
| Phone | Text | ❌ |
| Email | Email | ❌ |
| Assign Manager | User dropdown | ❌ |

**Validation**: Check against plan outlet limit (e.g., Pro = max 3 outlets + add-ons).

### 3.3 Switch Outlet

Owners can switch active outlet context from a dropdown in the header/sidebar. Switching changes the `tenantId` scope for all data views.

### 3.4 Data Model (Prisma)

```prisma
model Tenant {
  id          String   @id @default(uuid())
  name        String
  slug        String   @unique
  ownerUserId String
  parentId    String?  // null for primary outlet
  address     String?
  phone       String?
  email       String?
  logo        String?  // R2 key
  status      TenantStatus @default(ACTIVE)
  settings    Json?    // outlet-specific config
  createdAt   DateTime @default(now())
  updatedAt   DateTime @updatedAt

  owner       User     @relation("TenantOwner", fields: [ownerUserId], references: [id])
  parent      Tenant?  @relation("TenantBranches", fields: [parentId], references: [id])
  branches    Tenant[] @relation("TenantBranches")

  subscription Subscription?
  users       User[]
  products    Product[]
  bookings    Booking[]
  customers   Customer[]

  @@index([ownerUserId])
}

enum TenantStatus {
  ACTIVE
  INACTIVE
  SUSPENDED
}
```

### 3.5 Branch vs Primary

- **Primary outlet** (`parentId = null`): The original outlet created at registration
- **Branch outlets** (`parentId = primary.id`): Additional outlets under the same subscription
- Subscription is linked to the primary outlet
- Owner can share products/catalog across branches or keep them separate (configurable)

---

## 4. API Endpoints

> **Header**: All tenant-scoped endpoints require `X-MERCHANT-ID: <tenant_id>` header. Profile endpoints (`/api/settings/profile*`) are user-centric and do not require it. See `02_FEATURE_AUTH.md` for details.

| Method | Endpoint | Permission | `X-MERCHANT-ID` | Description |
|--------|----------|-----------|:---:|-------------|
| GET | `/api/settings/profile` | authenticated | ❌ | Get own profile |
| PATCH | `/api/settings/profile` | authenticated | ❌ | Update profile |
| POST | `/api/settings/profile/avatar` | authenticated | ❌ | Upload avatar |
| GET | `/api/settings/business` | `tenant:settings` | ✅ | Get business info |
| PATCH | `/api/settings/business` | `tenant:settings` | ✅ | Update business |
| GET | `/api/settings/payments` | `tenant:settings` | ✅ | Get payment config |
| PATCH | `/api/settings/payments` | `tenant:settings` | ✅ | Update payment config |
| GET | `/api/settings/notifications` | `tenant:settings` | ✅ | Get notification prefs |
| PATCH | `/api/settings/notifications` | `tenant:settings` | ✅ | Update prefs |
| GET | `/api/settings/outlet` | `tenant:settings` | ✅ | Get outlet config |
| PATCH | `/api/settings/outlet` | `tenant:settings` | ✅ | Update config |
| GET | `/api/outlets` | `tenant:settings` | ✅ | List owner's outlets (branches) |
| POST | `/api/outlets` | `tenant:settings` | ✅ | Create new outlet/branch |
| PATCH | `/api/outlets/:id` | `tenant:settings` | ✅ | Update outlet |
| POST | `/api/outlets/:id/deactivate` | `tenant:settings` | ✅ | Deactivate branch |
| POST | `/api/outlets/switch/:id` | `tenant:settings` | ✅ | Switch active outlet context |
