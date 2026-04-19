# Feature Spec — Maintenance Management

> **Module**: Maintenance | **UI Page**: `maintenance.html`
> **Priority**: P1 | **Status**: ✅ UI Complete, 🔧 Backend Needed

---

## 1. Overview

The maintenance module enables unit-level tracking of cleaning, repairs, and inspections. When a unit enters maintenance, it becomes unavailable for booking until completed. The system supports scheduling, progress tracking, actual cost recording, and receipt upload.

---

## 2. Data Model (Prisma)

```prisma
model Maintenance {
  id                  String             @id @default(uuid())
  tenantId            String
  productId           String
  unitId              String
  unitNumber          Int
  type                MaintenanceType
  status              MaintenanceStatus  @default(SCHEDULED)
  scheduledDate       DateTime
  estimatedCompletion DateTime?
  completedAt         DateTime?
  estimatedCost       Int                @default(0)
  actualCost          Int?
  progress            Int                @default(0) // 0-100
  notes               String?
  completionNotes     String?
  receiptUrl          String?            // R2 object key
  createdBy           String
  createdAt           DateTime           @default(now())
  updatedAt           DateTime           @updatedAt

  tenant              Tenant             @relation(fields: [tenantId], references: [id])

  @@index([tenantId])
  @@index([tenantId, status])
}

enum MaintenanceType {
  CLEANING
  REPAIR
  INSPECTION
}

enum MaintenanceStatus {
  SCHEDULED
  IN_PROGRESS
  COMPLETED
}
```

---

## 3. Features

### 3.1 Maintenance List View

**UI**: Products grouped by name, each showing:
- Total units, available units, units in maintenance
- Expandable maintenance records per product
- Color-coded rows: yellow (in-progress), blue (scheduled), green (completed)

### 3.2 Schedule Maintenance (Modal)

| Field | Type | Required |
|-------|------|----------|
| Product | Pre-selected | ✅ |
| Unit selection | Grid of available units | ✅ |
| Maintenance type | Dropdown (Cuci/Perbaikan/Inspeksi) | ✅ |
| Start date | Date picker | ✅ |
| Estimated completion | Date picker | ❌ |
| Estimated cost | Number (Rp) | ❌ |
| Notes / damage description | Textarea | ❌ |

**On submit**: Unit status → `MAINTENANCE`, maintenance record created.

### 3.3 Progress Tracking

- Progress bar (0–100%) per maintenance task
- Update via button (prompt or inline edit)
- Auto-transition: scheduled → in_progress when progress > 0

### 3.4 Complete Maintenance (Modal)

| Field | Type | Required |
|-------|------|----------|
| Actual cost | Number (Rp) | ✅ |
| Receipt upload | File (JPG/PNG/PDF, max 5MB) | ❌ |
| Completion notes | Textarea | ❌ |

**On submit**: 
- Status → `COMPLETED`, progress → 100%
- Unit status → `AVAILABLE`
- Receipt file → Cloudflare R2

### 3.5 Filters

| Filter | Options |
|--------|---------|
| Status | All, Scheduled, In Progress, Completed |
| Type | All, Cuci/Cleaning, Perbaikan/Repair, Inspeksi |

---

## 4. Side Effects

| Event | Side Effect |
|-------|-------------|
| Schedule maintenance | Unit status = MAINTENANCE; availability calendar updated |
| Complete maintenance | Unit status = AVAILABLE; availability calendar updated |
| Delete scheduled | Unit status reverts to AVAILABLE |

---

## 5. API Endpoints

> **Header**: All tenant-scoped endpoints require `X-MERCHANT-ID: <tenant_id>` header. See `02_FEATURE_AUTH.md` for details.

| Method | Endpoint | Permission | `X-MERCHANT-ID` | Description |
|--------|----------|-----------|:---:|-------------|
| GET | `/api/maintenances` | `maintenance:list` | ✅ | List all (filtered) |
| POST | `/api/maintenances` | `maintenance:create` | ✅ | Schedule maintenance |
| GET | `/api/maintenances/:id` | `maintenance:read` | ✅ | Detail |
| PATCH | `/api/maintenances/:id` | `maintenance:update` | ✅ | Update progress/notes |
| POST | `/api/maintenances/:id/complete` | `maintenance:complete` | ✅ | Complete with cost+receipt |
| DELETE | `/api/maintenances/:id` | `maintenance:delete` | ✅ | Delete scheduled only |
