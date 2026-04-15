# Permissions List & Role Mapping

## 📋 Format Permission

Format: `resource:action`

Contoh: `product:create`, `booking:read`, `report:export`

## 💾 Storage

Permissions disimpan sebagai **JSON array** di field `permissions` pada table `roles`:

```json
{
  "permissions": [
    "product:create",
    "product:read",
    "booking:create",
    "customer:list"
  ]
}
```

**Keuntungan**:
- ✅ Lebih simple, tidak perlu join table
- ✅ Faster query performance
- ✅ Mudah update permissions per role
- ✅ Flexible untuk custom roles

---

## 🔑 All Permissions

### User Management
```
user:create         - Create/invite user baru
user:read           - View user detail
user:update         - Update user data
user:delete         - Delete/deactivate user
user:list           - List semua user
user:assign-role    - Assign role ke user
```

### Role Management
```
role:create         - Create custom role
role:read           - View role detail
role:update         - Update role & permissions
role:delete         - Delete custom role
role:list           - List all roles
```

### Product Management
```
product:create      - Create produk baru
product:read        - View produk detail
product:update      - Update produk
product:delete      - Delete produk
product:list        - List semua produk
product:import      - Import produk dari Excel
product:export      - Export produk ke Excel
```

### Category Management
```
category:create     - Create kategori
category:read       - View kategori
category:update     - Update kategori
category:delete     - Delete kategori
category:list       - List kategori
```

### Customer Management
```
customer:create     - Create customer baru
customer:read       - View customer detail
customer:update     - Update customer data
customer:delete     - Delete customer
customer:list       - List semua customer
customer:export     - Export customer data
```

### Booking Management
```
booking:create      - Create booking baru
booking:read        - View booking detail
booking:update      - Update booking
booking:delete      - Delete booking (draft only)
booking:cancel      - Cancel booking
booking:list        - List semua booking
booking:list-own    - List booking yang dibuat sendiri
booking:checkin     - Process check-in
booking:checkout    - Process check-out
```

### Payment Management
```
payment:create      - Record payment
payment:read        - View payment detail
payment:update      - Update payment (limited)
payment:delete      - Delete payment (limited)
payment:refund      - Process refund
payment:list        - List semua payment
```

### Maintenance Management
```
maintenance:create      - Schedule maintenance
maintenance:read        - View maintenance detail
maintenance:update      - Update maintenance
maintenance:delete      - Delete maintenance
maintenance:complete    - Mark maintenance complete
maintenance:list        - List semua maintenance
```

### Report & Analytics
```
report:view-dashboard   - View dashboard
report:view-revenue     - View revenue report
report:view-rental      - View rental report
report:view-product     - View product report
report:view-customer    - View customer report
report:view-staff       - View staff performance
report:export           - Export reports
```

### Tenant Settings
```
tenant:settings         - Manage tenant settings
tenant:billing          - Manage billing & subscription
tenant:branding         - Manage logo & branding
```

### Notification
```
notification:send       - Send manual notification
notification:read       - View notifications
notification:list       - List all notifications
```

---

## 👥 Default Role Permissions

### 1. Owner (Tenant Owner)
**Full Access** - Semua permissions

```javascript
const ownerPermissions = [
  // User Management
  'user:create', 'user:read', 'user:update', 'user:delete', 
  'user:list', 'user:assign-role',
  
  // Role Management
  'role:create', 'role:read', 'role:update', 'role:delete', 'role:list',
  
  // Product Management
  'product:create', 'product:read', 'product:update', 'product:delete',
  'product:list', 'product:import', 'product:export',
  
  // Category
  'category:create', 'category:read', 'category:update', 
  'category:delete', 'category:list',
  
  // Customer
  'customer:create', 'customer:read', 'customer:update', 
  'customer:delete', 'customer:list', 'customer:export',
  
  // Booking
  'booking:create', 'booking:read', 'booking:update', 
  'booking:delete', 'booking:cancel',
  'booking:list', 'booking:checkin', 'booking:checkout',
  
  // Payment
  'payment:create', 'payment:read', 'payment:update', 
  'payment:delete', 'payment:refund', 'payment:list',
  
  // Maintenance
  'maintenance:create', 'maintenance:read', 'maintenance:update',
  'maintenance:delete', 'maintenance:complete', 'maintenance:list',
  
  // Reports
  'report:view-dashboard', 'report:view-revenue', 'report:view-rental',
  'report:view-product', 'report:view-customer', 'report:view-staff',
  'report:export',
  
  // Tenant
  'tenant:settings', 'tenant:billing', 'tenant:branding',
  
  // Notification
  'notification:send', 'notification:read', 'notification:list'
];
```

---

### 2. Manager
**Manage Operations** - Semua operasional, tidak bisa manage user & billing

```javascript
const managerPermissions = [
  // User (read only)
  'user:read', 'user:list',
  
  // Product (full)
  'product:create', 'product:read', 'product:update', 'product:delete',
  'product:list', 'product:import', 'product:export',
  
  // Category (full)
  'category:create', 'category:read', 'category:update', 
  'category:delete', 'category:list',
  
  // Customer (full)
  'customer:create', 'customer:read', 'customer:update', 
  'customer:delete', 'customer:list', 'customer:export',
  
  // Booking (full)
  'booking:create', 'booking:read', 'booking:update', 
  'booking:delete', 'booking:cancel',
  'booking:list', 'booking:checkin', 'booking:checkout',
  
  // Payment (full)
  'payment:create', 'payment:read', 'payment:refund', 'payment:list',
  
  // Maintenance (full)
  'maintenance:create', 'maintenance:read', 'maintenance:update',
  'maintenance:delete', 'maintenance:complete', 'maintenance:list',
  
  // Reports (all)
  'report:view-dashboard', 'report:view-revenue', 'report:view-rental',
  'report:view-product', 'report:view-customer', 'report:view-staff',
  'report:export',
  
  // Notification
  'notification:read', 'notification:list'
];
```

---

### 3. Staff/Kasir
**POS Operations** - Create booking, payment, basic operations

```javascript
const staffPermissions = [
  // Product (read only)
  'product:read', 'product:list',
  
  // Customer (full)
  'customer:create', 'customer:read', 'customer:update', 'customer:list',
  
  // Booking (create & own)
  'booking:create', 'booking:read', 'booking:update',
  'booking:list-own', 'booking:checkin', 'booking:checkout',
  
  // Payment (create)
  'payment:create', 'payment:read', 'payment:list',
  
  // Maintenance (read only)
  'maintenance:read', 'maintenance:list',
  
  // Reports (limited)
  'report:view-dashboard', 'report:view-rental',
  
  // Notification
  'notification:read', 'notification:list'
];
```

---

### 4. Inventory Staff
**Inventory & Maintenance** - Manage produk & maintenance

```javascript
const inventoryStaffPermissions = [
  // Product (full)
  'product:create', 'product:read', 'product:update', 
  'product:list', 'product:export',
  
  // Category (read)
  'category:read', 'category:list',
  
  // Customer (read only)
  'customer:read', 'customer:list',
  
  // Booking (read only untuk cek availability)
  'booking:read', 'booking:list',
  
  // Maintenance (full)
  'maintenance:create', 'maintenance:read', 'maintenance:update',
  'maintenance:delete', 'maintenance:complete', 'maintenance:list',
  
  // Reports (product related)
  'report:view-dashboard', 'report:view-product',
  
  // Notification
  'notification:read', 'notification:list'
];
```

---

## 🔒 Permission Checking Logic

### Backend Middleware
```javascript
const checkPermission = (requiredPermission) => {
  return async (req, res, next) => {
    const user = req.user; // dari JWT
    const userPermissions = await getUserPermissions(user.id);
    
    if (userPermissions.includes(requiredPermission)) {
      next();
    } else {
      res.status(403).json({
        success: false,
        error: {
          code: 'FORBIDDEN',
          message: 'You do not have permission to perform this action'
        }
      });
    }
  };
};

// Usage
router.post('/products', 
  authenticate, 
  checkPermission('product:create'), 
  createProduct
);
```

### Frontend Permission Check
```javascript
const hasPermission = (permission) => {
  const userPermissions = useAuthStore(state => state.permissions);
  return userPermissions.includes(permission);
};

// Usage in component
{hasPermission('product:create') && (
  <Button onClick={handleCreateProduct}>
    Create Product
  </Button>
)}
```

---

## 📊 Permission Matrix

| Feature | Owner | Manager | Staff | Inventory |
|---------|-------|---------|-------|-----------|
| **Users** |
| Create/Invite User | ✅ | ❌ | ❌ | ❌ |
| Edit User | ✅ | ❌ | ❌ | ❌ |
| Delete User | ✅ | ❌ | ❌ | ❌ |
| View Users | ✅ | ✅ | ❌ | ❌ |
| **Products** |
| Create Product | ✅ | ✅ | ❌ | ✅ |
| Edit Product | ✅ | ✅ | ❌ | ✅ |
| Delete Product | ✅ | ✅ | ❌ | ❌ |
| View Products | ✅ | ✅ | ✅ | ✅ |
| **Customers** |
| Create Customer | ✅ | ✅ | ✅ | ❌ |
| Edit Customer | ✅ | ✅ | ✅ | ❌ |
| Delete Customer | ✅ | ✅ | ❌ | ❌ |
| View Customers | ✅ | ✅ | ✅ | ✅ |
| **Bookings** |
| Create Booking | ✅ | ✅ | ✅ | ❌ |
| Edit Booking | ✅ | ✅ | ✅* | ❌ |
| Cancel Booking | ✅ | ✅ | ❌ | ❌ |
| View All Bookings | ✅ | ✅ | ❌ | ✅ |
| View Own Bookings | ✅ | ✅ | ✅ | ❌ |
| Check-in | ✅ | ✅ | ✅ | ❌ |
| Check-out | ✅ | ✅ | ✅ | ❌ |
| **Payments** |
| Record Payment | ✅ | ✅ | ✅ | ❌ |
| Refund | ✅ | ✅ | ❌ | ❌ |
| View Payments | ✅ | ✅ | ✅ | ❌ |
| **Maintenance** |
| Schedule | ✅ | ✅ | ❌ | ✅ |
| Complete | ✅ | ✅ | ❌ | ✅ |
| View | ✅ | ✅ | ✅ | ✅ |
| **Reports** |
| Dashboard | ✅ | ✅ | ✅ | ✅ |
| Revenue Report | ✅ | ✅ | ❌ | ❌ |
| Rental Report | ✅ | ✅ | ✅ | ❌ |
| Product Report | ✅ | ✅ | ❌ | ✅ |
| Export Reports | ✅ | ✅ | ❌ | ❌ |
| **Settings** |
| Tenant Settings | ✅ | ❌ | ❌ | ❌ |
| Billing | ✅ | ❌ | ❌ | ❌ |
| Roles | ✅ | ❌ | ❌ | ❌ |

*Staff hanya bisa edit booking yang dibuat sendiri

---

## 🎯 Custom Role Examples

### Example 1: Customer Service Role
**Use Case**: Handle customer inquiries, manage customer data, view bookings

```javascript
const customerServicePermissions = [
  'customer:create',
  'customer:read',
  'customer:update',
  'customer:list',
  'booking:read',
  'booking:list',
  'payment:read',
  'payment:list',
  'notification:send',
  'report:view-customer'
];
```

### Example 2: Accountant Role
**Use Case**: View financial reports, manage payments

```javascript
const accountantPermissions = [
  'payment:read',
  'payment:list',
  'booking:read',
  'booking:list',
  'report:view-revenue',
  'report:view-rental',
  'report:export'
];
```

### Example 3: Read-Only Viewer
**Use Case**: View all data but cannot modify

```javascript
const viewerPermissions = [
  'product:read',
  'product:list',
  'customer:read',
  'customer:list',
  'booking:read',
  'booking:list',
  'payment:read',
  'payment:list',
  'maintenance:read',
  'maintenance:list',
  'report:view-dashboard',
  'report:view-revenue',
  'report:view-rental',
  'report:view-product'
];
```

---

## 🔄 Permission Migration & Seeding

### Seed Default Roles
Roles bersifat **global** (tidak per tenant) dan permissions disimpan sebagai JSON array.

```sql
-- Seed default system roles dengan permissions
-- Lihat detail di DATABASE_SCHEMA.md

-- Owner Role
INSERT INTO roles (name, description, type, permissions, is_default, is_system)
VALUES (
  'Owner',
  'Full access to all features',
  'system',
  '["user:create", "user:read", "product:create", "booking:create", ...]'::jsonb,
  true,
  true
);

-- Manager, Staff, Inventory Staff roles...
-- (See full implementation in DATABASE_SCHEMA.md)
```

### Create Custom Role (Example)
```sql
-- Create custom role via application
INSERT INTO roles (name, description, type, permissions, is_system)
VALUES (
  'Customer Service',
  'Handle customer inquiries',
  'tenant',
  '[
    "customer:create",
    "customer:read",
    "customer:update",
    "booking:read",
    "booking:list"
  ]'::jsonb,
  false
);
```

### Assign Role to User
```sql
-- Assign role to user via user_roles junction table
INSERT INTO user_roles (user_id, role_id)
VALUES (
  'user-uuid-here',
  (SELECT id FROM roles WHERE name = 'Staff')
);
```

---

**Document Version**: 1.0  
**Last Updated**: April 15, 2026
