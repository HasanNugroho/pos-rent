# Feature Spec — Product & Inventory Management

> **Module**: Products | **UI Pages**: `products.html`, `catalog.html`, `availability.html`
> **Priority**: P0 | **Status**: ✅ UI Complete, 🔧 Backend Needed

---

## 1. Overview

Product management covers the full lifecycle of rental items — from creation and categorization to unit-level stock tracking and public catalog browsing. Each product can have multiple physical units, each independently tracked for availability, rental status, and maintenance.

---

## 2. Data Model (Prisma)

```prisma
model Product {
  id          String   @id @default(uuid())
  tenantId    String
  name        String
  description String?
  category    Category @relation(fields: [categoryId], references: [id])
  categoryId  String
  pricePerDay Int      // in Rupiah
  images      String[] // R2 object keys
  status      ProductStatus @default(ACTIVE)
  createdAt   DateTime @default(now())
  updatedAt   DateTime @updatedAt

  units       ProductUnit[]
  customFields Json?  // dynamic fields defined by tenant

  tenant      Tenant  @relation(fields: [tenantId], references: [id])

  @@index([tenantId])
}

model ProductUnit {
  id         String     @id @default(uuid())
  productId  String
  unitNumber Int
  size       String?
  status     UnitStatus @default(AVAILABLE)

  product    Product    @relation(fields: [productId], references: [id])

  @@unique([productId, unitNumber])
}

model Category {
  id       String    @id @default(uuid())
  tenantId String
  name     String
  icon     String?   // FontAwesome class
  color    String?   // hex color
  products Product[]
  tenant   Tenant    @relation(fields: [tenantId], references: [id])

  @@unique([tenantId, name])
}

enum ProductStatus {
  ACTIVE
  INACTIVE
  DISCONTINUED
}

enum UnitStatus {
  AVAILABLE
  RENTED
  MAINTENANCE
  RETIRED
}
```

---

## 3. Features

### 3.1 Product CRUD (Tenant)

**UI Reference**: `products.html`

| Action | Permission | Description |
|--------|-----------|-------------|
| List   | `product:list` | Grid view with search, filter by category/status |
| Create | `product:create` | Modal form: name, category, price, description, images, sizes |
| View   | `product:read` | Modal with full details, images, unit list |
| Update | `product:update` | Edit product data |
| Delete | `product:delete` | Soft-delete (set INACTIVE) |
| Import | `product:import` | Bulk import from Excel |
| Export | `product:export` | Export to Excel |

**Image Upload**: Images stored in Cloudflare R2 via presigned URLs.

### 3.2 Category Management

**UI Reference**: `settings.html` → Outlet tab

- CRUD categories with custom icon & color per tenant
- Categories are tenant-scoped
- Used for filtering on product list and public catalog

### 3.3 Custom Fields

**UI Reference**: `settings.html` → Outlet tab → Custom Fields section

Tenants can define additional fields per product:

| Field Type | Example |
|-----------|---------|
| Text | Brand, Material |
| Number | Weight (kg) |
| Dropdown | Condition (New/Good/Fair) |
| Date | Purchase Date |
| Checkbox | Waterproof |

Stored as JSON in `Product.customFields`.

### 3.4 Unit-Level Tracking

Each product has N physical units tracked independently:

```
Product: Nike Mercurial (Size 42)
├── Unit #1: Available
├── Unit #2: Rented (Booking #BK-042)
├── Unit #3: Maintenance (MAINT-001)
├── Unit #4: Available
└── Unit #5: Available
```

### 3.5 Availability Calendar

**UI Reference**: `availability.html`

Visual per-product calendar showing daily availability:

| Color | Meaning | Condition |
|-------|---------|-----------|
| 🟢 Green | Plenty available | > 70% units free |
| 🟠 Orange | Limited stock | 1–70% units free |
| 🔴 Red | Fully booked | 0 units free |
| 🟡 Yellow | Maintenance | Units in maintenance |

- Click a date → modal showing booking details for that day
- Navigate between months
- Expandable per product

### 3.6 Public Catalog

**UI Reference**: `catalog.html`

No-auth page for end customers:

- Hero section with outlet branding
- Search by name/type/description
- Filter by category
- Product cards with: image, price/day, stock count, sizes
- "Cek Ketersediaan" → availability modal (date picker, size, quantity)
- WhatsApp inquiry button with pre-filled message

---

## 4. API Endpoints

> **Header**: All tenant-scoped endpoints require `X-MERCHANT-ID: <tenant_id>` header. See `02_FEATURE_AUTH.md` for details.

| Method | Endpoint | Permission | `X-MERCHANT-ID` | Description |
|--------|----------|-----------|:---:|-------------|
| GET | `/api/products` | `product:list` | ✅ | List products (paginated, filterable) |
| POST | `/api/products` | `product:create` | ✅ | Create product |
| GET | `/api/products/:id` | `product:read` | ✅ | Get product detail |
| PATCH | `/api/products/:id` | `product:update` | ✅ | Update product |
| DELETE | `/api/products/:id` | `product:delete` | ✅ | Soft-delete product |
| GET | `/api/products/:id/availability` | public | ❌ | Get availability for date range |
| POST | `/api/products/import` | `product:import` | ✅ | Bulk import |
| GET | `/api/products/export` | `product:export` | ✅ | Export CSV/Excel |
| GET | `/api/categories` | `category:list` | ✅ | List categories |
| POST | `/api/categories` | `category:create` | ✅ | Create category |
| PATCH | `/api/categories/:id` | `category:update` | ✅ | Update category |
| DELETE | `/api/categories/:id` | `category:delete` | ✅ | Delete category |
| GET | `/api/public/catalog/:tenantSlug` | public | ❌ | Public catalog |

---

## 5. Caching (Redis)

| Key Pattern | TTL | Purpose |
|-------------|-----|---------|
| `product:list:{tenantId}:{hash}` | 5 min | Product list cache |
| `product:availability:{productId}:{month}` | 1 min | Availability calendar data |
| `catalog:{tenantSlug}` | 10 min | Public catalog cache |

Invalidate on product/booking/maintenance mutations.
