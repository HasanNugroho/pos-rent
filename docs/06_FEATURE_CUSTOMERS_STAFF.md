# Feature Spec — Customers & Staff Management

> **Module**: Customers + Staff | **UI Pages**: `customers.html`, `staff.html`
> **Priority**: P0 (Customers), P1 (Staff) | **Status**: ✅ UI Complete

---

## 1. Customer Management

### 1.1 Overview

Customer records are tenant-scoped. Each customer has a profile and linked booking history. Customers are created during the booking flow or managed independently.

### 1.2 Data Model (Prisma)

```prisma
model Customer {
  id          String    @id @default(uuid())
  tenantId    String
  name        String
  phone       String
  email       String?
  idNumber    String?   // KTP / SIM
  address     String?
  notes       String?
  totalBookings Int     @default(0)
  totalSpent    Int     @default(0)
  createdAt   DateTime  @default(now())
  updatedAt   DateTime  @updatedAt

  bookings    Booking[]
  tenant      Tenant    @relation(fields: [tenantId], references: [id])

  @@index([tenantId])
  @@unique([tenantId, phone])
}
```

### 1.3 Features

**UI Reference**: `customers.html`

| Feature | Description |
|---------|-------------|
| List | Table with search by name/phone, filterable |
| Add Customer | Modal: name, phone, email, KTP, address |
| View Detail | Profile card with booking history |
| Edit | Update customer info |
| Delete | Soft delete (if no active bookings) |
| Export | Export to CSV |
| Quick Add | Inline during booking creation (Step 1) |

### 1.4 API Endpoints

> **Header**: All tenant-scoped endpoints require `X-MERCHANT-ID: <tenant_id>` header. See `02_FEATURE_AUTH.md` for details.

| Method | Endpoint | Permission | `X-MERCHANT-ID` |
|--------|----------|-----------|:---:|
| GET | `/api/customers` | `customer:list` | ✅ |
| POST | `/api/customers` | `customer:create` | ✅ |
| GET | `/api/customers/:id` | `customer:read` | ✅ |
| PATCH | `/api/customers/:id` | `customer:update` | ✅ |
| DELETE | `/api/customers/:id` | `customer:delete` | ✅ |
| GET | `/api/customers/export` | `customer:export` | ✅ |

---

## 2. Staff Management

### 2.1 Overview

Staff management allows outlet owners to invite team members, assign roles with granular permissions, and manage access. Staff accounts are scoped to the tenant.

### 2.2 Data Model (Prisma)

```prisma
model User {
  id          String    @id @default(uuid())
  tenantId    String?   // null for super admin
  name        String
  email       String    @unique
  phone       String?
  password    String    // bcrypt hashed
  avatar      String?   // R2 key
  status      UserStatus @default(ACTIVE)
  lastLoginAt DateTime?
  createdAt   DateTime  @default(now())
  updatedAt   DateTime  @updatedAt

  roles       UserRole[]
  tenant      Tenant?   @relation(fields: [tenantId], references: [id])

  @@index([tenantId])
}

model Role {
  id          String    @id @default(uuid())
  name        String
  description String?
  type        RoleType  // SYSTEM or CUSTOM
  permissions Json      // string array e.g. ["product:create","booking:read"]
  isSystem    Boolean   @default(false)
  isDefault   Boolean   @default(false)
  createdAt   DateTime  @default(now())

  users       UserRole[]

  @@unique([name])
}

model UserRole {
  id      String @id @default(uuid())
  userId  String
  roleId  String

  user    User   @relation(fields: [userId], references: [id])
  role    Role   @relation(fields: [roleId], references: [id])

  @@unique([userId, roleId])
}

enum UserStatus {
  ACTIVE
  INACTIVE
  SUSPENDED
}

enum RoleType {
  SYSTEM
  CUSTOM
}
```

### 2.3 Features

**UI Reference**: `staff.html`

| Feature | Description |
|---------|-------------|
| Grid View | Staff cards with avatar, name, role badge, status |
| Stats | Total staff, Active, by role breakdown |
| Filter | By role (Owner/Manager/Kasir/Inventory), by status |
| Add Staff | Modal: name, email, phone, role, password, permissions |
| Edit Staff | Update data & permissions |
| Invite | Send invitation email to new staff |
| Suspend | Disable staff account without deleting |

### 2.4 Permission Assignment

In Add/Edit Staff modal:
- **Role selection** → loads default permissions for that role
- **Fine-tune permissions** via checkboxes grouped by module:
  - Products: list, create, update, delete
  - Bookings: list, create, update, checkin, checkout
  - Payments: list, create, refund
  - etc.

See full permission matrix in `PERMISSIONS_LIST.md`.

### 2.5 Staff API Endpoints

> **Header**: All tenant-scoped endpoints require `X-MERCHANT-ID: <tenant_id>` header. See `02_FEATURE_AUTH.md` for details.

| Method | Endpoint | Permission | `X-MERCHANT-ID` |
|--------|----------|-----------|:---:|
| GET | `/api/users` | `user:list` | ✅ |
| POST | `/api/users/invite` | `user:create` | ✅ |
| GET | `/api/users/:id` | `user:read` | ✅ |
| PATCH | `/api/users/:id` | `user:update` | ✅ |
| POST | `/api/users/:id/suspend` | `user:delete` | ✅ |
| POST | `/api/users/:id/assign-role` | `user:assign-role` | ✅ |
| GET | `/api/roles` | `role:list` | ✅ |
| POST | `/api/roles` | `role:create` | ✅ |
| PATCH | `/api/roles/:id` | `role:update` | ✅ |
| DELETE | `/api/roles/:id` | `role:delete` | ✅ |
