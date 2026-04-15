# Database Schema - SaaS Persewaan Barang

## 📊 Entity Relationship Overview

```
┌─────────────┐
│   Tenants   │
└──────┬──────┘
       │
       ├─────────────────┬──────────────┬───────────────┬──────────────┐
       │                 │              │               │              │
       ▼                 ▼              ▼               ▼              ▼
  ┌─────────┐      ┌──────────┐   ┌──────────┐   ┌──────────┐   ┌──────────┐
  │  Users  │      │ Products │   │Customers │   │Bookings  │   │Settings  │
  └────┬────┘      └────┬─────┘   └────┬─────┘   └────┬─────┘   └──────────┘
       │                │              │              │
       ▼                ▼              │              ▼
  ┌─────────┐    ┌───────────┐        │        ┌────────────┐
  │  Roles  │    │Categories │        │        │BookingItems│
  └────┬────┘    └───────────┘        │        └────────────┘
       │                │              │              │
       ▼                ▼              │              ▼
┌────────────┐   ┌────────────┐       │        ┌──────────┐
│Permissions │   │Maintenance │       └────────│ Payments │
└────────────┘   └────────────┘                └──────────┘
```

---

## 🗂 Tables Detail

### 1. tenants
```sql
CREATE TABLE tenants (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name VARCHAR(255) NOT NULL,
    slug VARCHAR(100) UNIQUE NOT NULL,
    email VARCHAR(255) NOT NULL,
    phone VARCHAR(20),
    address TEXT,
    logo_url VARCHAR(500),
    
    -- Settings
    timezone VARCHAR(50) DEFAULT 'Asia/Jakarta',
    currency VARCHAR(3) DEFAULT 'IDR',
    locale VARCHAR(10) DEFAULT 'id_ID',
    
    -- Business Hours (JSON)
    business_hours JSONB DEFAULT '{
        "monday": {"open": "09:00", "close": "18:00", "is_open": true},
        "tuesday": {"open": "09:00", "close": "18:00", "is_open": true},
        "wednesday": {"open": "09:00", "close": "18:00", "is_open": true},
        "thursday": {"open": "09:00", "close": "18:00", "is_open": true},
        "friday": {"open": "09:00", "close": "18:00", "is_open": true},
        "saturday": {"open": "09:00", "close": "18:00", "is_open": true},
        "sunday": {"open": "09:00", "close": "18:00", "is_open": false}
    }',
    
    -- Subscription
    subscription_plan VARCHAR(50) DEFAULT 'trial',
    subscription_status VARCHAR(20) DEFAULT 'active',
    trial_ends_at TIMESTAMP,
    subscription_ends_at TIMESTAMP,
    
    -- Metadata
    status VARCHAR(20) DEFAULT 'active',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    deleted_at TIMESTAMP
);

CREATE INDEX idx_tenants_slug ON tenants(slug);
CREATE INDEX idx_tenants_status ON tenants(status);
```

### 2. users
```sql
CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    tenant_id UUID NOT NULL REFERENCES tenants(id) ON DELETE CASCADE,
    
    -- Auth
    email VARCHAR(255) NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    email_verified_at TIMESTAMP,
    
    -- Personal Info
    first_name VARCHAR(100) NOT NULL,
    last_name VARCHAR(100),
    phone VARCHAR(20),
    avatar_url VARCHAR(500),
    
    -- Status
    status VARCHAR(20) DEFAULT 'active',
    last_login_at TIMESTAMP,
    
    -- Metadata
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    deleted_at TIMESTAMP,
    
    UNIQUE(tenant_id, email)
);

CREATE INDEX idx_users_tenant ON users(tenant_id);
CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_users_status ON users(status);
```

### 3. roles
```sql
CREATE TYPE role_type AS ENUM ('system', 'tenant', 'customer');

CREATE TABLE roles (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    
    name VARCHAR(50) NOT NULL UNIQUE,
    description TEXT,
    type role_type DEFAULT 'customer',
    
    -- Permissions stored as JSON array
    -- Example: ["product:create", "product:read", "booking:create"]
    permissions JSONB DEFAULT '[]',
    
    -- Flags
    is_default BOOLEAN DEFAULT FALSE,
    is_system BOOLEAN DEFAULT FALSE,
    
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    deleted_at TIMESTAMP
);

CREATE INDEX idx_roles_name ON roles(name);
CREATE INDEX idx_roles_type ON roles(type);
CREATE INDEX idx_roles_default ON roles(is_default);

-- Default system roles: owner, manager, staff, inventory_staff
```

### 4. user_roles
```sql
CREATE TABLE user_roles (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    role_id UUID NOT NULL REFERENCES roles(id) ON DELETE CASCADE,
    
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    UNIQUE(user_id, role_id)
);

CREATE INDEX idx_user_roles_user ON user_roles(user_id);
CREATE INDEX idx_user_roles_role ON user_roles(role_id);
```

### 5. refresh_tokens
```sql
CREATE TABLE refresh_tokens (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    
    token VARCHAR(500) NOT NULL UNIQUE,
    expires_at TIMESTAMP NOT NULL,
    
    -- Device info
    device_name VARCHAR(255),
    ip_address VARCHAR(45),
    user_agent TEXT,
    
    -- Status
    is_revoked BOOLEAN DEFAULT FALSE,
    revoked_at TIMESTAMP,
    
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_refresh_tokens_user ON refresh_tokens(user_id);
CREATE INDEX idx_refresh_tokens_token ON refresh_tokens(token);
CREATE INDEX idx_refresh_tokens_expires ON refresh_tokens(expires_at);
```

### 6. password_resets
```sql
CREATE TABLE password_resets (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    
    token VARCHAR(500) NOT NULL UNIQUE,
    expires_at TIMESTAMP NOT NULL,
    
    is_used BOOLEAN DEFAULT FALSE,
    used_at TIMESTAMP,
    
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_password_resets_token ON password_resets(token);
CREATE INDEX idx_password_resets_expires ON password_resets(expires_at);
```

### 7. email_verifications
```sql
CREATE TABLE email_verifications (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    
    token VARCHAR(500) NOT NULL UNIQUE,
    expires_at TIMESTAMP NOT NULL,
    
    is_verified BOOLEAN DEFAULT FALSE,
    verified_at TIMESTAMP,
    
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_email_verifications_token ON email_verifications(token);
```

### 8. categories
```sql
CREATE TABLE categories (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    tenant_id UUID NOT NULL REFERENCES tenants(id) ON DELETE CASCADE,
    parent_id UUID REFERENCES categories(id) ON DELETE SET NULL,
    
    name VARCHAR(255) NOT NULL,
    slug VARCHAR(255) NOT NULL,
    description TEXT,
    icon VARCHAR(100),
    image_url VARCHAR(500),
    
    -- Display order
    sort_order INTEGER DEFAULT 0,
    
    -- Custom fields definition (JSON)
    custom_fields JSONB,
    
    status VARCHAR(20) DEFAULT 'active',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    UNIQUE(tenant_id, slug)
);

CREATE INDEX idx_categories_tenant ON categories(tenant_id);
CREATE INDEX idx_categories_parent ON categories(parent_id);
CREATE INDEX idx_categories_status ON categories(status);
```

### 9. products
```sql
CREATE TABLE products (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    tenant_id UUID NOT NULL REFERENCES tenants(id) ON DELETE CASCADE,
    category_id UUID REFERENCES categories(id) ON DELETE SET NULL,
    
    -- Basic Info
    name VARCHAR(255) NOT NULL,
    sku VARCHAR(100),
    description TEXT,
    
    -- Pricing
    price_per_day DECIMAL(15, 2) NOT NULL,
    price_per_week DECIMAL(15, 2),
    price_per_month DECIMAL(15, 2),
    deposit_amount DECIMAL(15, 2) DEFAULT 0,
    
    -- Fees
    late_fee_per_day DECIMAL(15, 2) DEFAULT 0,
    damage_fee DECIMAL(15, 2) DEFAULT 0,
    
    -- Rental Rules
    min_rental_days INTEGER DEFAULT 1,
    max_rental_days INTEGER,
    min_notice_hours INTEGER DEFAULT 0,
    max_advance_days INTEGER DEFAULT 90,
    
    -- Inventory
    quantity_total INTEGER DEFAULT 1,
    quantity_available INTEGER DEFAULT 1,
    
    -- Condition & Status
    condition VARCHAR(50) DEFAULT 'good',
    status VARCHAR(20) DEFAULT 'active',
    
    -- Specifications (JSON)
    specifications JSONB,
    
    -- SEO
    slug VARCHAR(255),
    
    -- Metadata
    created_by UUID REFERENCES users(id),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    deleted_at TIMESTAMP,
    
    UNIQUE(tenant_id, sku)
);

CREATE INDEX idx_products_tenant ON products(tenant_id);
CREATE INDEX idx_products_category ON products(category_id);
CREATE INDEX idx_products_status ON products(status);
CREATE INDEX idx_products_sku ON products(sku);
```

### 10. product_images
```sql
CREATE TABLE product_images (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    product_id UUID NOT NULL REFERENCES products(id) ON DELETE CASCADE,
    
    url VARCHAR(500) NOT NULL,
    alt_text VARCHAR(255),
    sort_order INTEGER DEFAULT 0,
    is_primary BOOLEAN DEFAULT FALSE,
    
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_product_images_product ON product_images(product_id);
```

### 11. product_variants (Optional - untuk future)
```sql
CREATE TABLE product_variants (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    product_id UUID NOT NULL REFERENCES products(id) ON DELETE CASCADE,
    
    name VARCHAR(255) NOT NULL,
    sku VARCHAR(100),
    
    -- Price override (jika berbeda dari parent)
    price_per_day DECIMAL(15, 2),
    price_per_week DECIMAL(15, 2),
    price_per_month DECIMAL(15, 2),
    
    -- Inventory per variant
    quantity_total INTEGER DEFAULT 1,
    quantity_available INTEGER DEFAULT 1,
    
    -- Variant attributes (JSON)
    attributes JSONB,
    
    status VARCHAR(20) DEFAULT 'active',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    UNIQUE(product_id, sku)
);

CREATE INDEX idx_product_variants_product ON product_variants(product_id);
```

### 12. customers
```sql
CREATE TABLE customers (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    tenant_id UUID NOT NULL REFERENCES tenants(id) ON DELETE CASCADE,
    
    -- Personal Info
    first_name VARCHAR(100) NOT NULL,
    last_name VARCHAR(100),
    email VARCHAR(255),
    phone VARCHAR(20) NOT NULL,
    whatsapp VARCHAR(20),
    
    -- Address
    address TEXT,
    city VARCHAR(100),
    postal_code VARCHAR(20),
    
    -- Identity
    id_card_type VARCHAR(50),
    id_card_number VARCHAR(100),
    id_card_photo_url VARCHAR(500),
    
    date_of_birth DATE,
    
    -- Status
    status VARCHAR(20) DEFAULT 'active',
    
    -- Stats
    total_bookings INTEGER DEFAULT 0,
    total_spent DECIMAL(15, 2) DEFAULT 0,
    
    -- Tags
    tags JSONB,
    
    -- Notes
    notes TEXT,
    
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    deleted_at TIMESTAMP,
    
    UNIQUE(tenant_id, email),
    UNIQUE(tenant_id, phone)
);

CREATE INDEX idx_customers_tenant ON customers(tenant_id);
CREATE INDEX idx_customers_email ON customers(email);
CREATE INDEX idx_customers_phone ON customers(phone);
CREATE INDEX idx_customers_status ON customers(status);
```

### 13. bookings
```sql
CREATE TABLE bookings (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    tenant_id UUID NOT NULL REFERENCES tenants(id) ON DELETE CASCADE,
    customer_id UUID NOT NULL REFERENCES customers(id),
    
    -- Booking Info
    booking_number VARCHAR(50) UNIQUE NOT NULL,
    
    -- Dates
    start_date DATE NOT NULL,
    end_date DATE NOT NULL,
    start_time TIME,
    end_time TIME,
    
    -- Actual dates (saat pickup & return)
    actual_pickup_at TIMESTAMP,
    actual_return_at TIMESTAMP,
    
    -- Pricing
    subtotal DECIMAL(15, 2) NOT NULL,
    deposit_amount DECIMAL(15, 2) DEFAULT 0,
    late_fee DECIMAL(15, 2) DEFAULT 0,
    damage_fee DECIMAL(15, 2) DEFAULT 0,
    discount_amount DECIMAL(15, 2) DEFAULT 0,
    tax_amount DECIMAL(15, 2) DEFAULT 0,
    total_amount DECIMAL(15, 2) NOT NULL,
    
    -- Payment
    paid_amount DECIMAL(15, 2) DEFAULT 0,
    deposit_paid DECIMAL(15, 2) DEFAULT 0,
    deposit_returned DECIMAL(15, 2) DEFAULT 0,
    remaining_amount DECIMAL(15, 2) DEFAULT 0,
    
    -- Status
    status VARCHAR(20) DEFAULT 'pending',
    payment_status VARCHAR(20) DEFAULT 'unpaid',
    
    -- Conditions
    pickup_condition_notes TEXT,
    pickup_photos JSONB,
    return_condition_notes TEXT,
    return_photos JSONB,
    
    -- Staff
    created_by UUID REFERENCES users(id),
    checked_in_by UUID REFERENCES users(id),
    checked_out_by UUID REFERENCES users(id),
    
    -- Notes
    notes TEXT,
    internal_notes TEXT,
    
    -- Metadata
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    deleted_at TIMESTAMP
);

CREATE INDEX idx_bookings_tenant ON bookings(tenant_id);
CREATE INDEX idx_bookings_customer ON bookings(customer_id);
CREATE INDEX idx_bookings_number ON bookings(booking_number);
CREATE INDEX idx_bookings_status ON bookings(status);
CREATE INDEX idx_bookings_dates ON bookings(start_date, end_date);
CREATE INDEX idx_bookings_created_at ON bookings(created_at);
```

### 14. booking_items
```sql
CREATE TABLE booking_items (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    booking_id UUID NOT NULL REFERENCES bookings(id) ON DELETE CASCADE,
    product_id UUID NOT NULL REFERENCES products(id),
    product_variant_id UUID REFERENCES product_variants(id),
    
    -- Snapshot data (saat booking dibuat)
    product_name VARCHAR(255) NOT NULL,
    product_sku VARCHAR(100),
    
    -- Quantity
    quantity INTEGER NOT NULL DEFAULT 1,
    
    -- Pricing snapshot
    price_per_day DECIMAL(15, 2) NOT NULL,
    rental_days INTEGER NOT NULL,
    subtotal DECIMAL(15, 2) NOT NULL,
    
    -- Condition tracking
    pickup_condition VARCHAR(50),
    pickup_notes TEXT,
    return_condition VARCHAR(50),
    return_notes TEXT,
    
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_booking_items_booking ON booking_items(booking_id);
CREATE INDEX idx_booking_items_product ON booking_items(product_id);
```

### 15. payments
```sql
CREATE TABLE payments (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    tenant_id UUID NOT NULL REFERENCES tenants(id) ON DELETE CASCADE,
    booking_id UUID NOT NULL REFERENCES bookings(id),
    
    -- Payment Info
    payment_number VARCHAR(50) UNIQUE NOT NULL,
    amount DECIMAL(15, 2) NOT NULL,
    payment_method VARCHAR(50) NOT NULL,
    
    -- Payment Details
    payment_date TIMESTAMP NOT NULL,
    reference_number VARCHAR(255),
    proof_url VARCHAR(500),
    
    -- Type
    payment_type VARCHAR(50) DEFAULT 'rental',
    
    -- Status
    status VARCHAR(20) DEFAULT 'completed',
    
    -- Notes
    notes TEXT,
    
    -- Staff
    received_by UUID REFERENCES users(id),
    
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_payments_tenant ON payments(tenant_id);
CREATE INDEX idx_payments_booking ON payments(booking_id);
CREATE INDEX idx_payments_number ON payments(payment_number);
CREATE INDEX idx_payments_date ON payments(payment_date);
```

### 16. maintenance
```sql
CREATE TABLE maintenance (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    tenant_id UUID NOT NULL REFERENCES tenants(id) ON DELETE CASCADE,
    product_id UUID NOT NULL REFERENCES products(id),
    
    -- Maintenance Info
    maintenance_number VARCHAR(50) UNIQUE NOT NULL,
    type VARCHAR(50) NOT NULL,
    title VARCHAR(255) NOT NULL,
    description TEXT,
    
    -- Dates
    start_date DATE NOT NULL,
    end_date DATE NOT NULL,
    actual_start_date DATE,
    actual_end_date DATE,
    
    -- Status
    status VARCHAR(20) DEFAULT 'scheduled',
    
    -- Cost
    cost DECIMAL(15, 2) DEFAULT 0,
    
    -- Performed by
    performed_by VARCHAR(255),
    is_external BOOLEAN DEFAULT FALSE,
    
    -- Photos
    before_photos JSONB,
    after_photos JSONB,
    
    -- Notes
    notes TEXT,
    
    -- Staff
    created_by UUID REFERENCES users(id),
    completed_by UUID REFERENCES users(id),
    
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    completed_at TIMESTAMP
);

CREATE INDEX idx_maintenance_tenant ON maintenance(tenant_id);
CREATE INDEX idx_maintenance_product ON maintenance(product_id);
CREATE INDEX idx_maintenance_dates ON maintenance(start_date, end_date);
CREATE INDEX idx_maintenance_status ON maintenance(status);
```

### 17. availability_blocks
```sql
CREATE TABLE availability_blocks (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    tenant_id UUID NOT NULL REFERENCES tenants(id) ON DELETE CASCADE,
    product_id UUID REFERENCES products(id) ON DELETE CASCADE,
    
    -- Block Info
    start_date DATE NOT NULL,
    end_date DATE NOT NULL,
    
    -- Reason
    reason VARCHAR(50) NOT NULL,
    description TEXT,
    
    -- Recurring (untuk future)
    is_recurring BOOLEAN DEFAULT FALSE,
    recurring_rule JSONB,
    
    created_by UUID REFERENCES users(id),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_availability_blocks_tenant ON availability_blocks(tenant_id);
CREATE INDEX idx_availability_blocks_product ON availability_blocks(product_id);
CREATE INDEX idx_availability_blocks_dates ON availability_blocks(start_date, end_date);
```

### 18. notifications
```sql
CREATE TABLE notifications (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    tenant_id UUID NOT NULL REFERENCES tenants(id) ON DELETE CASCADE,
    user_id UUID REFERENCES users(id) ON DELETE CASCADE,
    
    -- Notification Info
    type VARCHAR(50) NOT NULL,
    title VARCHAR(255) NOT NULL,
    message TEXT NOT NULL,
    
    -- Action
    action_url VARCHAR(500),
    action_text VARCHAR(100),
    
    -- Related entities
    related_type VARCHAR(50),
    related_id UUID,
    
    -- Status
    is_read BOOLEAN DEFAULT FALSE,
    read_at TIMESTAMP,
    
    -- Channels
    sent_via JSONB,
    
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_notifications_tenant ON notifications(tenant_id);
CREATE INDEX idx_notifications_user ON notifications(user_id);
CREATE INDEX idx_notifications_read ON notifications(is_read);
CREATE INDEX idx_notifications_created ON notifications(created_at);
```

### 19. activity_logs
```sql
CREATE TABLE activity_logs (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    tenant_id UUID NOT NULL REFERENCES tenants(id) ON DELETE CASCADE,
    user_id UUID REFERENCES users(id) ON DELETE SET NULL,
    
    -- Activity Info
    action VARCHAR(100) NOT NULL,
    entity_type VARCHAR(50) NOT NULL,
    entity_id UUID NOT NULL,
    
    -- Changes
    old_values JSONB,
    new_values JSONB,
    
    -- Request Info
    ip_address VARCHAR(45),
    user_agent TEXT,
    
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_activity_logs_tenant ON activity_logs(tenant_id);
CREATE INDEX idx_activity_logs_user ON activity_logs(user_id);
CREATE INDEX idx_activity_logs_entity ON activity_logs(entity_type, entity_id);
CREATE INDEX idx_activity_logs_created ON activity_logs(created_at);
```

### 20. tenant_settings
```sql
CREATE TABLE tenant_settings (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    tenant_id UUID NOT NULL REFERENCES tenants(id) ON DELETE CASCADE,
    
    -- Setting key-value
    setting_key VARCHAR(100) NOT NULL,
    setting_value JSONB NOT NULL,
    
    updated_by UUID REFERENCES users(id),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    UNIQUE(tenant_id, setting_key)
);

CREATE INDEX idx_tenant_settings_tenant ON tenant_settings(tenant_id);
```

---

## 🔗 Key Relationships

1. **Tenant → Everything**: Semua data terisolasi per tenant
2. **User ↔ Roles**: Many-to-Many (user bisa punya multiple roles)
3. **Role ↔ Permissions**: Many-to-Many
4. **Product → Category**: Many-to-One
5. **Product → Maintenance**: One-to-Many
6. **Booking → Customer**: Many-to-One
7. **Booking → BookingItems**: One-to-Many
8. **Booking → Payments**: One-to-Many
9. **Product ↔ Booking**: Many-to-Many (through booking_items)

---

## 🔒 Row-Level Security (RLS)

Untuk PostgreSQL, implementasi RLS:

```sql
-- Enable RLS
ALTER TABLE products ENABLE ROW LEVEL SECURITY;
ALTER TABLE bookings ENABLE ROW LEVEL SECURITY;
-- ... enable untuk semua tenant-specific tables

-- Policy: User hanya bisa akses data tenant mereka
CREATE POLICY tenant_isolation ON products
    USING (tenant_id = current_setting('app.current_tenant_id')::uuid);

-- Implement untuk semua tenant-specific tables
```

---

## 📈 Indexes & Performance

### Composite Indexes
```sql
CREATE INDEX idx_bookings_tenant_status_dates 
    ON bookings(tenant_id, status, start_date, end_date);

CREATE INDEX idx_products_tenant_status_category 
    ON products(tenant_id, status, category_id);

CREATE INDEX idx_payments_tenant_booking_date 
    ON payments(tenant_id, booking_id, payment_date);
```

### Full-Text Search
```sql
-- Products search
CREATE INDEX idx_products_search ON products 
    USING GIN(to_tsvector('indonesian', 
        COALESCE(name, '') || ' ' || COALESCE(description, '')
    ));

-- Customers search
CREATE INDEX idx_customers_search ON customers 
    USING GIN(to_tsvector('indonesian', 
        COALESCE(first_name, '') || ' ' || 
        COALESCE(last_name, '') || ' ' || 
        COALESCE(phone, '')
    ));
```

---

## 🔢 Auto-Generated Numbers

Implementasi auto-increment dengan format custom:

```sql
-- Function untuk generate booking number
CREATE OR REPLACE FUNCTION generate_booking_number(p_tenant_id UUID)
RETURNS VARCHAR AS $$
DECLARE
    v_date VARCHAR(8);
    v_sequence INTEGER;
    v_number VARCHAR(50);
BEGIN
    v_date := TO_CHAR(CURRENT_DATE, 'YYYYMMDD');
    
    SELECT COALESCE(MAX(
        CAST(SUBSTRING(booking_number FROM 10) AS INTEGER)
    ), 0) + 1
    INTO v_sequence
    FROM bookings
    WHERE tenant_id = p_tenant_id
    AND booking_number LIKE 'BK' || v_date || '%';
    
    v_number := 'BK' || v_date || LPAD(v_sequence::TEXT, 4, '0');
    
    RETURN v_number;
END;
$$ LANGUAGE plpgsql;

-- Similar functions untuk payment_number, maintenance_number, etc.
```

---

## 📊 Sample Data Queries

### Get Product Availability
```sql
SELECT 
    p.id,
    p.name,
    p.quantity_total,
    p.quantity_available,
    COUNT(DISTINCT bi.id) as booked_count,
    COUNT(DISTINCT m.id) as maintenance_count
FROM products p
LEFT JOIN booking_items bi ON p.id = bi.product_id
LEFT JOIN bookings b ON bi.booking_id = b.id
    AND b.status IN ('confirmed', 'active')
    AND :check_date BETWEEN b.start_date AND b.end_date
LEFT JOIN maintenance m ON p.id = m.product_id
    AND m.status IN ('scheduled', 'in_progress')
    AND :check_date BETWEEN m.start_date AND m.end_date
WHERE p.tenant_id = :tenant_id
GROUP BY p.id;
```

### Dashboard Metrics
```sql
-- Today's revenue
SELECT 
    COALESCE(SUM(amount), 0) as today_revenue
FROM payments
WHERE tenant_id = :tenant_id
AND DATE(payment_date) = CURRENT_DATE;

-- Active rentals
SELECT COUNT(*) 
FROM bookings
WHERE tenant_id = :tenant_id
AND status = 'active';

-- Overdue rentals
SELECT COUNT(*)
FROM bookings
WHERE tenant_id = :tenant_id
AND status = 'active'
AND end_date < CURRENT_DATE;
```

---

## 🔄 Migration Strategy

1. Create tables in dependency order
2. Seed default system roles dengan permissions
3. Create triggers untuk updated_at
4. Create RLS policies
5. Create indexes
6. Create helper functions

---

## 🌱 Seed Data

### Default System Roles

```sql
-- Owner Role
INSERT INTO roles (id, name, description, type, permissions, is_default, is_system)
VALUES (
  gen_random_uuid(),
  'Owner',
  'Full access to all features',
  'system',
  '[
    "user:create", "user:read", "user:update", "user:delete", "user:list", "user:assign-role",
    "role:create", "role:read", "role:update", "role:delete", "role:list",
    "product:create", "product:read", "product:update", "product:delete", "product:list", "product:import", "product:export",
    "category:create", "category:read", "category:update", "category:delete", "category:list",
    "customer:create", "customer:read", "customer:update", "customer:delete", "customer:list", "customer:export",
    "booking:create", "booking:read", "booking:update", "booking:delete", "booking:cancel", "booking:list", "booking:checkin", "booking:checkout",
    "payment:create", "payment:read", "payment:update", "payment:delete", "payment:refund", "payment:list",
    "maintenance:create", "maintenance:read", "maintenance:update", "maintenance:delete", "maintenance:complete", "maintenance:list",
    "report:view-dashboard", "report:view-revenue", "report:view-rental", "report:view-product", "report:view-customer", "report:view-staff", "report:export",
    "tenant:settings", "tenant:billing", "tenant:branding",
    "notification:send", "notification:read", "notification:list"
  ]'::jsonb,
  true,
  true
);

-- Manager Role
INSERT INTO roles (id, name, description, type, permissions, is_default, is_system)
VALUES (
  gen_random_uuid(),
  'Manager',
  'Manage operations without user management',
  'system',
  '[
    "user:read", "user:list",
    "product:create", "product:read", "product:update", "product:delete", "product:list", "product:import", "product:export",
    "category:create", "category:read", "category:update", "category:delete", "category:list",
    "customer:create", "customer:read", "customer:update", "customer:delete", "customer:list", "customer:export",
    "booking:create", "booking:read", "booking:update", "booking:delete", "booking:cancel", "booking:list", "booking:checkin", "booking:checkout",
    "payment:create", "payment:read", "payment:refund", "payment:list",
    "maintenance:create", "maintenance:read", "maintenance:update", "maintenance:delete", "maintenance:complete", "maintenance:list",
    "report:view-dashboard", "report:view-revenue", "report:view-rental", "report:view-product", "report:view-customer", "report:view-staff", "report:export",
    "notification:read", "notification:list"
  ]'::jsonb,
  true,
  true
);

-- Staff Role
INSERT INTO roles (id, name, description, type, permissions, is_default, is_system)
VALUES (
  gen_random_uuid(),
  'Staff',
  'POS operations and customer service',
  'system',
  '[
    "product:read", "product:list",
    "customer:create", "customer:read", "customer:update", "customer:list",
    "booking:create", "booking:read", "booking:update", "booking:list-own", "booking:checkin", "booking:checkout",
    "payment:create", "payment:read", "payment:list",
    "maintenance:read", "maintenance:list",
    "report:view-dashboard", "report:view-rental",
    "notification:read", "notification:list"
  ]'::jsonb,
  true,
  true
);

-- Inventory Staff Role
INSERT INTO roles (id, name, description, type, permissions, is_default, is_system)
VALUES (
  gen_random_uuid(),
  'Inventory Staff',
  'Manage inventory and maintenance',
  'system',
  '[
    "product:create", "product:read", "product:update", "product:list", "product:export",
    "category:read", "category:list",
    "customer:read", "customer:list",
    "booking:read", "booking:list",
    "maintenance:create", "maintenance:read", "maintenance:update", "maintenance:delete", "maintenance:complete", "maintenance:list",
    "report:view-dashboard", "report:view-product",
    "notification:read", "notification:list"
  ]'::jsonb,
  true,
  true
);
```

### Helper Functions

```sql
-- Function to check if user has permission
CREATE OR REPLACE FUNCTION user_has_permission(p_user_id UUID, p_permission VARCHAR)
RETURNS BOOLEAN AS $$
DECLARE
  v_has_permission BOOLEAN;
BEGIN
  SELECT EXISTS (
    SELECT 1
    FROM user_roles ur
    JOIN roles r ON ur.role_id = r.id
    WHERE ur.user_id = p_user_id
    AND r.permissions @> to_jsonb(ARRAY[p_permission])
  ) INTO v_has_permission;
  
  RETURN v_has_permission;
END;
$$ LANGUAGE plpgsql;

-- Function to get user permissions
CREATE OR REPLACE FUNCTION get_user_permissions(p_user_id UUID)
RETURNS JSONB AS $$
DECLARE
  v_permissions JSONB;
BEGIN
  SELECT jsonb_agg(DISTINCT permission)
  INTO v_permissions
  FROM user_roles ur
  JOIN roles r ON ur.role_id = r.id,
  jsonb_array_elements_text(r.permissions) permission
  WHERE ur.user_id = p_user_id;
  
  RETURN COALESCE(v_permissions, '[]'::jsonb);
END;
$$ LANGUAGE plpgsql;
```

---

**Document Version**: 1.0  
**Last Updated**: April 15, 2026
