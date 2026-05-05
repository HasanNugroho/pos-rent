

**POS RENTAL SYSTEM**

Product & Technical Documentation

| **Multi-Tenant SaaS Rental Management Platform**  Version 2.0  |  April 2026 |
| :-: |


**Document Type**

**Product & Technical Documentation**

**System**

**POS Rental — Multi-Tenant SaaS**

**Version**

**2.0**

**Status**

**Active Development**

**Date**

**April 2026**


# **Table of Contents**

1. Product Overview

2. System Architecture & Scope

3. User Roles & Permissions

4. Module Functional Scope

5. Data Models & ERD

6. Business & Application Flow

7. API Contract — Authentication

8. API Contract — Outlets (Tenant Management)

9. API Contract — Products

10. API Contract — Bookings

11. API Contract — Customers

12. API Contract — Maintenance

13. API Contract — Staff

14. API Contract — Reports

15. API Contract — Subscriptions

16. API Contract — Super Admin

17. Validation Rules

18. Third-Party Integrations

19. Subscription & Billing Model

20. Security Considerations


# **1. Product Overview**

## **1.1 Product Description**

POS Rental is a multi-tenant SaaS (Software as a Service) platform designed specifically for rental businesses — primarily shoe rental shops. The system provides a complete point-of-sale and inventory management solution that allows business owners (tenants) to manage bookings, products, customers, staff, maintenance, and financial reports from a single web-based dashboard.


## **1.2 Target Users**

| **User Type** | **Description** | **Access Level** |
| - | - | - |
| **Super Admin** | **Platform operator — manages all tenants, subscriptions, system config** | **Full system access** |
| **Owner (Tenant)** | **Business owner — full access to their outlet data** | **Full outlet access** |
| **Manager** | **Operational manager — all features except billing & user management** | **Outlet operational** |
| **Kasir / Staff** | **Cashier — creates bookings, processes payments & returns** | **POS operations** |
| **Inventory Staff** | **Manages products and maintenance scheduling** | **Product & maintenance** |
| **Customer (Public)** | **End customer — views public catalog, contacts via WhatsApp** | **Read-only catalog** |


## **1.3 Core Value Propositions**

- Multi-tenant architecture — one platform, many independent rental businesses

- Complete booking lifecycle — from booking creation to pickup, return, and invoicing

- DP (Down Payment) system — flexible partial payment with settlement tracking

- Product availability calendar — real-time per-product availability view

- Maintenance tracking — per-unit maintenance scheduling with cost logging

- Customer blacklist — block problematic customers with documented reasons

- Multi-outlet support — manage multiple branches under one subscription

- Tiered subscription plans — Trial, Basic, Pro, Enterprise with add-on upsells

- Public product catalog — customer-facing page with WhatsApp inquiry integration


## **1.4 Technology Stack**

| **Layer** | **Technology** | **Notes** |
| - | - | - |
| **Frontend** | **HTML5, TailwindCSS 3.x (CDN)** | **Utility-first CSS framework** |
| **Interactivity** | **Alpine.js 3.x** | **Lightweight reactive JS — no build step** |
| **Charts** | **Chart.js** | **Revenue, booking, subscription charts** |
| **Icons** | **Font Awesome 6.4** | **UI iconography** |
| **Typography** | **Inter (Google Fonts)** | **Primary UI font** |
| **Backend (planned)** | **REST API** | **JSON responses, token-based auth** |
| **Database (planned)** | **Relational (PostgreSQL recommended)** | **Multi-tenant with row-level isolation** |


# **2. System Architecture & Scope**

## **2.1 Multi-Tenant Architecture**

POS Rental uses a shared database multi-tenant model where all tenant data resides in the same database, isolated by a tenant\_id (outlet\_id). The Super Admin layer operates independently and has cross-tenant visibility.


| **Architecture Pattern** Shared Database, Shared Schema — each table has an outlet\_id foreign key. Super Admin has a separate schema/role without outlet\_id scoping. |
| - |


## **2.2 System Layers**

| **Layer** | **Description** | **Pages / Modules** |
| - | - | - |
| **Super Admin Panel** | **Platform management layer — purple theme (from-purple-900 to-indigo-900 sidebar)** | **admin-dashboard, admin-outlets, admin-users, admin-subscriptions, admin-reports, admin-settings** |
| **Tenant Dashboard** | **Outlet operational layer — slate/white theme with dark sidebar** | **dashboard, bookings, booking-create, products, availability, customers, maintenance, staff, reports, settings, outlets** |
| **Public Catalog** | **Customer-facing product listing — no authentication required** | **catalog** |
| **Auth Pages** | **Login and registration — tenant onboarding** | **login, register** |


## **2.3 In-Scope Features**

- Tenant (outlet) registration, activation, and subscription management

- Product catalog management with per-unit stock tracking

- Booking creation (3-step: Customer → Product/Date → Payment)

- DP and full payment handling with pelunasan (settlement) flow

- Custom pickup & return scheduling with availability blocking logic

- Product availability calendar with status indicators

- Customer management with blacklisting capability

- Maintenance scheduling per product unit with cost tracking

- Multi-outlet management with outlet switching

- Staff management with role-based permissions

- Invoice generation and payment history

- Financial and operational reports with export (PDF/Excel)

- Super Admin: outlet CRUD, subscription billing, system settings

- Super Admin: role & permission matrix management

- Super Admin: pricing plan configuration with add-ons

- Super Admin: communication template management (Email, WhatsApp, Push)


## **2.4 Out-of-Scope (Current Version)**

- Native mobile application (iOS/Android)

- Real-time payment gateway integration (planned via Midtrans/Xendit)

- Automated WhatsApp/Telegram bot messaging (manual link only)

- QR code-based product scanning

- GPS-based outlet tracking

- Customer-facing booking portal (customers contact via WhatsApp only)


# **3. User Roles & Permissions**

## **3.1 Super Admin Roles**

Super Admin is a system-level role that exists outside the tenant structure. There is only one Super Admin in the system, and it cannot be assigned at the outlet level.


## **3.2 Tenant Roles**

The following roles are defined as System Roles (cannot be deleted, permissions are predefined) and are available to all tenants:


| **Role** | **Type** | **Description** | **Permission Count** |
| - | - | - | - |
| **Owner** | **System** | **Full access to all outlet features including billing and user management** | **85 / 85** |
| **Manager** | **System** | **Operational access — all features except users and billing management** | **62 / 85** |
| **Staff / Kasir** | **System** | **POS operations — bookings, payments, customer management** | **30 / 85** |
| **Inventory Staff** | **System** | **Product and maintenance management** | **35 / 85** |
| **Customer Service** | **Custom** | **View-only access to bookings and customer data** | **15 / 85** |


## **3.3 Permission Matrix**

Permissions are grouped by resource module. The following table shows available permission actions per module:


| **Module** | **Permissions Available** |
| - | - |
| **User Management** | **user:create, user:read, user:update, user:delete, user:list, user:assign-role** |
| **Role Management** | **role:create, role:read, role:update, role:delete, role:list** |
| **Product Management** | **product:create, product:read, product:update, product:delete, product:list, product:import, product:export** |
| **Booking Management** | **booking:create, booking:read, booking:update, booking:cancel, booking:list, booking:list-own, booking:checkin, booking:checkout** |
| **Payment Management** | **payment:create, payment:read, payment:refund, payment:list** |
| **Tenant Management** | **tenant:settings, tenant:billing, tenant:branding** |
| **Reports** | **report:view-dashboard, report:view-revenue, report:export** |


## **3.4 Custom Roles**

Owners can create custom roles with any combination of available permissions. Custom roles can be deleted; System Roles cannot. Custom roles are scoped to the outlet that created them.


# **4. Module Functional Scope**

## **4.1 Authentication Module**

| **Feature** | **Description** |
| - | - |
| **Login** | **Email + password login. Demo accounts: owner@sepatu.com, kasir@sepatu.com** |
| **Register** | **New tenant onboarding — business info + owner info + 14-day free trial activation** |
| **Session** | **Token-based authentication (JWT planned)** |
| **Logout** | **Clear session and redirect to login** |


## **4.2 Dashboard Module**

| **Widget** | **Data Displayed** |
| - | - |
| **Revenue Card** | **Today's revenue with percentage vs previous period** |
| **Active Bookings** | **Count of bookings with status = active or confirmed** |
| **Product Availability** | **Available units vs total units (e.g., 45/120)** |
| **Overdue Count** | **Bookings past return deadline not yet returned** |
| **Revenue Chart** | **7-day revenue trend line chart** |
| **Top Products** | **Top 3 products by booking count and revenue** |
| **Recent Bookings** | **Last 5 bookings with status and quick action links** |


## **4.3 Booking Module**

- List all bookings with filter by status (confirmed, active, completed) and search

- Create booking — 3-step wizard: Step 1 Customer Info, Step 2 Product & Date Selection, Step 3 Payment

- DP (Down Payment) system — accept partial payment, track remaining balance

- Custom pickup & return scheduling — items blocked from pickup date to return date, charge calculated from startDate to endDate only

- Edit booking — modify dates, pickup/return schedule (only for confirmed status)

- Pickup processing — collateral documentation (KTP/SIM/Cash), photo upload, optional pelunasan

- Return processing — photo documentation, late fee calculation, damage fee, mandatory pelunasan

- Invoice generation — per payment (DP invoice, pelunasan invoice), print support

- Status flow: confirmed → active (after pickup) → completed (after return)


## **4.4 Product Module**

| **Feature** | **Description** |
| - | - |
| **Product List** | **Grid view with search, category filter, status filter** |
| **Product Detail** | **View modal with images, pricing, stock info, total rental count** |
| **Add Product** | **Form with name, SKU, category, size, stock count, price/day, description, photo upload** |
| **Edit Product** | **Update all product fields** |
| **Stock per Unit** | **Each product has multiple physical units (unit \#1, \#2, etc.) tracked independently** |
| **Status Tracking** | **Per-unit status: available, rented, maintenance** |


## **4.5 Availability Calendar**

- Per-product monthly calendar view with expandable/collapsible rows

- Color coding: green = mostly available (≥70% stock), orange = limited stock, red = fully booked, yellow = all units in maintenance

- Day detail modal: shows total stock, available count, booked count, maintenance count, list of active bookings

- Month navigation (previous/next) with category and search filters


## **4.6 Customer Module**

- Customer database with search and status filter

- Customer stats: total, active this month, new this month, total transactions, blacklisted count

- Blacklist feature — block customer with mandatory reason and date tracking

- Unblock feature — restore access to blacklisted customers

- Customer detail view — booking history, total spend, rating

- Add new customer inline from booking creation form


## **4.7 Maintenance Module**

- Per-product, per-unit maintenance tracking

- Schedule maintenance for specific unit of a product (only available units can be scheduled)

- Maintenance types: Cuci/Cleaning, Perbaikan (Repair), Inspeksi (Inspection)

- Progress tracking (0–100%) with status: scheduled → in\_progress → completed

- Completion flow: actual cost input, receipt photo upload, completion notes

- Product availability auto-updates: unit status changes to maintenance when scheduled, back to available when completed


## **4.8 Staff Module**

- Staff list with role, status, and contact info

- Add staff with role assignment and permission selection

- Optional email invitation on staff creation

- Edit staff — update info, role, and permissions


## **4.9 Outlet Module (Multi-Outlet)**

- Outlet list with metrics: staff count, product count, booking count, monthly revenue

- Active outlet switcher — all data (bookings, products, reports) scoped to active outlet

- Plan quota enforcement — Pro Plan allows up to 3 outlets; add-ons available

- Add/edit outlet with manager assignment, operating hours, catalog sync toggle

- Deactivate outlet — freezes all active bookings, staff loses access


## **4.10 Settings Module**

| **Tab** | **Configurable Items** |
| - | - |
| **Profile** | **Name, email, phone, avatar** |
| **Business** | **Business name, address, phone, email** |
| **Payment** | **Active payment methods: Cash, Bank Transfer, QRIS, E-Wallet** |
| **Notifications** | **Toggles: New Booking, Payment, Return Reminder, Overdue** |
| **Outlet Config** | **Product categories (CRUD), custom product fields (CRUD), tax & service charge %, booking rules (min/max duration, min DP %, late fee/day), operating hours per day** |
| **Subscription** | **Current plan display, add-on purchase modal, billing history with invoice download** |


## **4.11 Reports Module**

- Period selector: per month, per year

- Summary cards: total revenue, total bookings, average per booking, utilization rate

- Revenue trend chart (weekly breakdown)

- Booking by category doughnut chart

- Top 10 products by booking count and revenue

- Top 10 customers by booking count and spend

- Maintenance report summary: total, completed, in-progress, total cost

- Export to PDF and Excel


# **5. Data Models & ERD**

## **5.1 Core Entities**

The following tables represent the core data model derived from the application screens. All tenant-scoped tables include outlet\_id as a foreign key.


### **5.1.1 outlets (Tenants)**

| **Column** | **Type** | **Constraints** | **Description** |
| - | - | - | - |
| **id** | **VARCHAR(20)** | **PK** | **e.g., OUT-001** |
| **name** | **VARCHAR(255)** | **NOT NULL** | **Outlet business name** |
| **owner\_name** | **VARCHAR(255)** | **NOT NULL** | **Owner full name** |
| **email** | **VARCHAR(255)** | **UNIQUE NOT NULL** | **Owner/outlet email** |
| **phone** | **VARCHAR(20)** |  | **Contact phone number** |
| **address** | **TEXT** |  | **Full address** |
| **status** | **ENUM** | **NOT NULL** | **active | trial | inactive** |
| **is\_primary** | **BOOLEAN** | **DEFAULT false** | **Whether this is the primary outlet** |
| **open\_time** | **TIME** |  | **Opening time (e.g., 08:00)** |
| **close\_time** | **TIME** |  | **Closing time (e.g., 21:00)** |
| **sync\_catalog** | **BOOLEAN** | **DEFAULT false** | **Sync products with primary outlet** |
| **plan\_id** | **INT** | **FK → plans.id** | **Current subscription plan** |
| **trial\_ends\_at** | **TIMESTAMP** |  | **Trial expiry timestamp** |
| **created\_at** | **TIMESTAMP** | **NOT NULL** | **Registration date** |
| **parent\_outlet\_id** | **VARCHAR(20)** | **FK → outlets.id** | **Parent outlet for branches** |


### **5.1.2 users (Staff)**

| **Column** | **Type** | **Constraints** | **Description** |
| - | - | - | - |
| **id** | **BIGINT** | **PK AUTO\_INCREMENT** |  |
| **outlet\_id** | **VARCHAR(20)** | **FK → outlets.id** | **Owning outlet** |
| **name** | **VARCHAR(255)** | **NOT NULL** | **Full name** |
| **email** | **VARCHAR(255)** | **UNIQUE NOT NULL** | **Login email** |
| **password\_hash** | **VARCHAR(255)** | **NOT NULL** | **Bcrypt hashed password** |
| **phone** | **VARCHAR(20)** |  | **Contact number** |
| **role\_id** | **INT** | **FK → roles.id** | **Assigned role** |
| **status** | **ENUM** | **NOT NULL** | **active | inactive** |
| **last\_login\_at** | **TIMESTAMP** |  | **Last login timestamp** |
| **joined\_date** | **DATE** |  | **Employment start date** |
| **created\_at** | **TIMESTAMP** | **NOT NULL** |  |


### **5.1.3 roles**

| **Column** | **Type** | **Constraints** | **Description** |
| - | - | - | - |
| **id** | **INT** | **PK AUTO\_INCREMENT** |  |
| **outlet\_id** | **VARCHAR(20)** | **FK nullable** | **NULL for system roles** |
| **name** | **VARCHAR(100)** | **NOT NULL** | **Role display name** |
| **description** | **TEXT** |  | **Role description** |
| **is\_system** | **BOOLEAN** | **DEFAULT false** | **System roles cannot be deleted** |
| **permissions** | **JSON** | **NOT NULL** | **Array of permission strings** |
| **created\_at** | **TIMESTAMP** | **NOT NULL** |  |


### **5.1.4 products**

| **Column** | **Type** | **Constraints** | **Description** |
| - | - | - | - |
| **id** | **BIGINT** | **PK AUTO\_INCREMENT** |  |
| **outlet\_id** | **VARCHAR(20)** | **FK → outlets.id NOT NULL** |  |
| **name** | **VARCHAR(255)** | **NOT NULL** | **Product name (e.g., Nike Mercurial)** |
| **sku** | **VARCHAR(100)** | **UNIQUE per outlet** | **Stock Keeping Unit** |
| **category** | **VARCHAR(100)** |  | **Category name** |
| **size** | **VARCHAR(20)** |  | **Size identifier (e.g., 42)** |
| **stock** | **INT** | **NOT NULL DEFAULT 1** | **Total unit count** |
| **price\_per\_day** | **BIGINT** | **NOT NULL** | **Daily rental price in IDR (no decimals)** |
| **description** | **TEXT** |  | **Product description** |
| **status** | **ENUM** | **NOT NULL** | **available | rented | maintenance** |
| **created\_at** | **TIMESTAMP** | **NOT NULL** |  |


### **5.1.5 product\_units**

| **Column** | **Type** | **Constraints** | **Description** |
| - | - | - | - |
| **id** | **BIGINT** | **PK AUTO\_INCREMENT** |  |
| **product\_id** | **BIGINT** | **FK → products.id NOT NULL** |  |
| **unit\_number** | **INT** | **NOT NULL** | **Unit identifier (1, 2, 3...)** |
| **status** | **ENUM** | **NOT NULL** | **available | rented | maintenance** |


### **5.1.6 customers**

| **Column** | **Type** | **Constraints** | **Description** |
| - | - | - | - |
| **id** | **VARCHAR(20)** | **PK** | **e.g., CUST-001** |
| **outlet\_id** | **VARCHAR(20)** | **FK → outlets.id NOT NULL** |  |
| **name** | **VARCHAR(255)** | **NOT NULL** |  |
| **phone** | **VARCHAR(20)** | **NOT NULL** | **Primary contact** |
| **email** | **VARCHAR(255)** |  |  |
| **address** | **TEXT** |  |  |
| **status** | **ENUM** | **NOT NULL** | **active | blacklisted** |
| **blacklist\_reason** | **TEXT** |  | **Required when status = blacklisted** |
| **blacklist\_date** | **DATE** |  | **Date of blacklisting** |
| **total\_bookings** | **INT** | **DEFAULT 0** | **Denormalized count** |
| **total\_spend** | **BIGINT** | **DEFAULT 0** | **Denormalized total in IDR** |
| **member\_since** | **DATE** | **NOT NULL** |  |
| **created\_at** | **TIMESTAMP** | **NOT NULL** |  |


### **5.1.7 bookings**

| **Column** | **Type** | **Constraints** | **Description** |
| - | - | - | - |
| **id** | **VARCHAR(20)** | **PK** | **e.g., \#BK-001** |
| **outlet\_id** | **VARCHAR(20)** | **FK → outlets.id NOT NULL** |  |
| **customer\_id** | **VARCHAR(20)** | **FK → customers.id NOT NULL** |  |
| **created\_by** | **BIGINT** | **FK → users.id** | **Staff who created booking** |
| **start\_date** | **DATE** | **NOT NULL** | **Rental period start (charge from)** |
| **end\_date** | **DATE** | **NOT NULL** | **Rental period end (charge to)** |
| **pickup\_date** | **DATE** | **NOT NULL** | **Physical pickup date (block from)** |
| **pickup\_time** | **TIME** | **DEFAULT 09:00** |  |
| **return\_date** | **DATE** | **NOT NULL** | **Physical return date (block until)** |
| **return\_time** | **TIME** | **DEFAULT 17:00** |  |
| **has\_custom\_schedule** | **BOOLEAN** | **DEFAULT false** | **Pickup/return differs from start/end** |
| **payment\_type** | **ENUM** | **NOT NULL** | **dp | full** |
| **dp\_amount** | **BIGINT** | **DEFAULT 0** | **Down payment amount in IDR** |
| **total\_amount** | **BIGINT** | **NOT NULL** | **Total booking value** |
| **deposit\_amount** | **BIGINT** | **DEFAULT 100000** | **Security deposit** |
| **status** | **ENUM** | **NOT NULL** | **confirmed | active | completed** |
| **collateral\_type** | **ENUM** |  | **ktp | sim | passport | cash | other (set at pickup)** |
| **collateral\_proof** | **VARCHAR(255)** |  | **KTP number or cash amount** |
| **notes** | **TEXT** |  |  |
| **created\_at** | **TIMESTAMP** | **NOT NULL** |  |


### **5.1.8 booking\_items**

| **Column** | **Type** | **Constraints** | **Description** |
| - | - | - | - |
| **id** | **BIGINT** | **PK AUTO\_INCREMENT** |  |
| **booking\_id** | **VARCHAR(20)** | **FK → bookings.id NOT NULL** |  |
| **product\_id** | **BIGINT** | **FK → products.id NOT NULL** |  |
| **unit\_id** | **BIGINT** | **FK → product\_units.id** | **Specific unit assigned** |
| **quantity** | **INT** | **NOT NULL DEFAULT 1** |  |
| **price\_per\_day** | **BIGINT** | **NOT NULL** | **Price at time of booking (snapshot)** |


### **5.1.9 payments (Invoices)**

| **Column** | **Type** | **Constraints** | **Description** |
| - | - | - | - |
| **id** | **BIGINT** | **PK AUTO\_INCREMENT** |  |
| **outlet\_id** | **VARCHAR(20)** | **FK → outlets.id NOT NULL** |  |
| **booking\_id** | **VARCHAR(20)** | **FK → bookings.id NOT NULL** |  |
| **invoice\_number** | **VARCHAR(50)** | **UNIQUE NOT NULL** | **e.g., INV/2024/001** |
| **type** | **ENUM** | **NOT NULL** | **dp | full | pelunasan | late\_fee | damage\_fee** |
| **amount** | **BIGINT** | **NOT NULL** | **Amount in IDR** |
| **method** | **ENUM** | **NOT NULL** | **cash | transfer | qris | ewallet** |
| **notes** | **TEXT** |  |  |
| **paid\_at** | **TIMESTAMP** | **NOT NULL** |  |
| **created\_by** | **BIGINT** | **FK → users.id** | **Staff who recorded payment** |


### **5.1.10 maintenance\_records**

| **Column** | **Type** | **Constraints** | **Description** |
| - | - | - | - |
| **id** | **VARCHAR(20)** | **PK** | **e.g., MAINT-001** |
| **outlet\_id** | **VARCHAR(20)** | **FK → outlets.id NOT NULL** |  |
| **product\_id** | **BIGINT** | **FK → products.id NOT NULL** |  |
| **unit\_id** | **BIGINT** | **FK → product\_units.id NOT NULL** |  |
| **unit\_number** | **INT** | **NOT NULL** | **For display reference** |
| **type** | **ENUM** | **NOT NULL** | **cleaning | repair | inspection** |
| **status** | **ENUM** | **NOT NULL** | **scheduled | in\_progress | completed** |
| **start\_date** | **DATE** | **NOT NULL** |  |
| **estimated\_completion** | **DATE** |  |  |
| **actual\_completion** | **DATE** |  |  |
| **estimated\_cost** | **BIGINT** | **DEFAULT 0** |  |
| **actual\_cost** | **BIGINT** |  | **Set at completion** |
| **progress** | **TINYINT** | **DEFAULT 0** | **0–100** |
| **notes** | **TEXT** |  | **Damage description or maintenance notes** |
| **completion\_notes** | **TEXT** |  | **Notes recorded at completion** |
| **receipt\_filename** | **VARCHAR(255)** |  | **Uploaded receipt/nota file** |
| **created\_by** | **BIGINT** | **FK → users.id** |  |
| **created\_at** | **TIMESTAMP** | **NOT NULL** |  |


### **5.1.11 plans (Subscription Plans)**

| **Column** | **Type** | **Constraints** | **Description** |
| - | - | - | - |
| **id** | **INT** | **PK AUTO\_INCREMENT** |  |
| **name** | **VARCHAR(100)** | **NOT NULL** | **Trial | Basic | Pro | Enterprise** |
| **price** | **BIGINT** | **NOT NULL** | **Monthly price in IDR (0 for Trial)** |
| **interval** | **ENUM** | **NOT NULL** | **monthly | yearly** |
| **max\_outlets** | **INT** |  | **NULL = unlimited** |
| **max\_users** | **INT** |  | **NULL = unlimited** |
| **max\_products** | **INT** |  | **NULL = unlimited** |
| **trial\_days** | **INT** | **DEFAULT 0** | **14 for Trial plan** |
| **features** | **JSON** |  | **Array of enabled feature keys** |
| **is\_active** | **BOOLEAN** | **DEFAULT true** |  |


### **5.1.12 subscriptions**

| **Column** | **Type** | **Constraints** | **Description** |
| - | - | - | - |
| **id** | **BIGINT** | **PK AUTO\_INCREMENT** |  |
| **outlet\_id** | **VARCHAR(20)** | **FK → outlets.id NOT NULL** |  |
| **plan\_id** | **INT** | **FK → plans.id NOT NULL** |  |
| **status** | **ENUM** | **NOT NULL** | **active | trial | expiring | expired** |
| **starts\_at** | **TIMESTAMP** | **NOT NULL** |  |
| **expires\_at** | **TIMESTAMP** | **NOT NULL** |  |
| **next\_billing\_at** | **DATE** |  |  |
| **created\_at** | **TIMESTAMP** | **NOT NULL** |  |


### **5.1.13 subscription\_invoices**

| **Column** | **Type** | **Constraints** | **Description** |
| - | - | - | - |
| **id** | **BIGINT** | **PK AUTO\_INCREMENT** |  |
| **outlet\_id** | **VARCHAR(20)** | **FK → outlets.id NOT NULL** |  |
| **subscription\_id** | **BIGINT** | **FK → subscriptions.id NOT NULL** |  |
| **invoice\_number** | **VARCHAR(50)** | **UNIQUE NOT NULL** | **e.g., INV-202604-001** |
| **description** | **VARCHAR(255)** |  | **e.g., Pro Plan (Bulanan) + 1 Extra Outlet** |
| **amount** | **BIGINT** | **NOT NULL** |  |
| **status** | **ENUM** | **NOT NULL** | **paid | pending | failed** |
| **paid\_at** | **TIMESTAMP** |  |  |
| **created\_at** | **TIMESTAMP** | **NOT NULL** |  |


### **5.1.14 plan\_addons**

| **Column** | **Type** | **Constraints** | **Description** |
| - | - | - | - |
| **id** | **INT** | **PK AUTO\_INCREMENT** |  |
| **plan\_id** | **INT** | **FK → plans.id NOT NULL** |  |
| **name** | **VARCHAR(100)** | **NOT NULL** | **e.g., +1 Additional Outlet** |
| **price** | **BIGINT** | **NOT NULL** | **Monthly price in IDR** |
| **addon\_type** | **ENUM** | **NOT NULL** | **outlet | user | product** |
| **is\_enabled** | **BOOLEAN** | **DEFAULT true** |  |


# **6. Business & Application Flow**

## **6.1 Tenant Onboarding Flow**

1. Customer visits register page and fills business info + owner info

2. System creates outlet record with status = trial, creates Owner user account

3. 14-day free trial starts (trial\_ends\_at set)

4. Owner receives verification email (template: auth\_verify)

5. Owner logs in and configures business settings, adds products

6. Before trial ends, owner upgrades to paid plan via Settings → Subscription

7. Payment processed, subscription record created with status = active


## **6.2 Booking Lifecycle Flow**

| **Step** | **Action** | **Status Change** | **Notes** |
| - | - | - | - |
| **1** | **Staff creates booking (Step 1: Customer, Step 2: Products/Date, Step 3: Payment)** | **confirmed** | **DP or full payment recorded. Invoice created.** |
| **2** | **Customer arrives for pickup** | **confirmed → active** | **Collateral documented, initial product photos taken, optional pelunasan** |
| **3** | **Product in customer's possession** | **active** | **Unit status = rented. Blocked on calendar.** |
| **4** | **Customer returns product** | **active → completed** | **Return photos taken, late fee & damage fee calculated if applicable. Pelunasan mandatory if DP balance exists or extra charges.** |
| **5** | **Invoice finalized** | **completed** | **Full payment history available. Unit status returns to available.** |


## **6.3 Payment Flow**

### **6.3.1 DP (Down Payment) Flow**

8. Booking created with payment\_type = dp, dp\_amount recorded

9. DP Invoice (INV/YYYY/NNN) generated

10. At pickup: staff can collect remaining balance (pelunasan) — creates pelunasan invoice

11. At return: if balance still outstanding, mandatory pelunasan + any additional charges

12. Booking marked fully paid, status = completed


### **6.3.2 Full Payment Flow**

13. Booking created with payment\_type = full

14. Full amount collected, invoice created with type = full

15. At return: only late\_fee and damage\_fee collected if applicable


## **6.4 Availability Blocking Logic**

| **Availability Rule** Items are BLOCKED from pickup\_date to return\_date. Customer is CHARGED from start\_date to end\_date. The pickup\_date may be before start\_date (e.g., customer picks up the day before), and return\_date may be after end\_date (e.g., returns the day after). This allows flexible handover windows without affecting the charged rental period. |
| - |


| **Scenario** | **Example** |
| - | - |
| **Standard** | **Rental: Apr 2–10 (9 days charge). Pickup: Apr 2 09:00. Return: Apr 10 17:00. Blocked: 9 days.** |
| **Custom pickup** | **Rental: Apr 2–10 (9 days charge). Pickup: Apr 1 16:00. Return: Apr 11 09:00. Blocked: 11 days.** |


## **6.5 Maintenance Flow**

16. Staff schedules maintenance for a specific product unit (unit must be available)

17. Unit status changes to maintenance, appears as blocked on availability calendar

18. Staff updates progress percentage (0–100)

19. Staff completes maintenance: enters actual cost, uploads receipt photo, writes completion notes

20. Unit status returns to available automatically on completion


## **6.6 Subscription Expiry Flow**

21. System checks subscription expiry daily

22. 7 days before expiry: status changes to expiring, reminder email sent (template: sub\_remind)

23. On expiry date: status changes to expired, outlet access restricted

24. 6 days after expiry: follow-up notification sent

25. Owner renews or upgrades plan → status returns to active


# **7. API Contract — Overview & Conventions**

## **7.1 Base URL & Versioning**

Base URL: https://api.posrental.com/v1

Content-Type: application/json

Accept: application/json


## **7.2 Authentication**

All API endpoints (except /auth/login and /auth/register) require a Bearer token in the Authorization header.

Authorization: Bearer \<jwt\_token\>


| **Token Scoping** Tokens contain outlet\_id, user\_id, role, and permissions claims. All tenant-scoped endpoints auto-filter by outlet\_id from the token. Super Admin tokens contain no outlet\_id and access all data. |
| - |


## **7.3 Standard Response Envelope**

Success Response:

\{ "success": true, "data": \{ ... \}, "meta": \{ "page": 1, "per\_page": 20, "total": 150 \} \}


Error Response:

\{ "success": false, "error": \{ "code": "VALIDATION\_ERROR", "message": "...", "details": \{ "field": "error" \} \} \}


## **7.4 Standard Error Codes**

| **HTTP Status** | **Error Code** | **Description** |
| - | - | - |
| **400** | **VALIDATION\_ERROR** | **Request body or params failed validation** |
| **401** | **UNAUTHORIZED** | **Missing or invalid token** |
| **403** | **FORBIDDEN** | **Authenticated but lacks required permission** |
| **404** | **NOT\_FOUND** | **Resource does not exist** |
| **409** | **CONFLICT** | **Duplicate resource (e.g., duplicate SKU)** |
| **422** | **UNPROCESSABLE** | **Business logic error (e.g., unit not available)** |
| **429** | **RATE\_LIMITED** | **Too many requests** |
| **500** | **SERVER\_ERROR** | **Internal server error** |


## **7.5 Pagination Query Parameters**

| **Parameter** | **Type** | **Default** | **Description** |
| - | - | - | - |
| **page** | **integer** | **1** | **Page number (1-indexed)** |
| **per\_page** | **integer** | **20** | **Items per page (max 100)** |
| **sort** | **string** | **created\_at** | **Field to sort by** |
| **order** | **string** | **desc** | **asc | desc** |


# **8. API Contract — Authentication**


| **Method** | **Endpoint** | **Auth** | **Permission** | **Description** |
| - | - | - | - | - |
| **POST** | **/auth/login** | **None** | **—** | **Login with email and password** |
| **POST** | **/auth/register** | **None** | **—** | **Register new tenant + owner account** |
| **POST** | **/auth/logout** | **Bearer** | **—** | **Invalidate current token** |
| **POST** | **/auth/refresh** | **Bearer** | **—** | **Refresh access token** |


### **POST /auth/login**

Request Body:

| **Field** | **Type** | **Required** | **Validation** | **Description** |
| - | - | - | - | - |
| **email** | **string** | **Yes** | **valid email format** | **User email address** |
| **password** | **string** | **Yes** | **min 8 chars** | **User password** |


Response 200:

\{ "success": true, "data": \{ "token": "eyJ...", "expires\_in": 3600,

  "user": \{ "id": 1, "name": "Pak Budi", "email": "budi@sepatu.com",

    "role": "owner", "outlet\_id": "OUT-001", "outlet\_name": "Sepatu Premium Jakarta" \} \} \}


### **POST /auth/register**

Request Body:

| **Field** | **Type** | **Required** | **Validation** | **Description** |
| - | - | - | - | - |
| **business\_name** | **string** | **Yes** | **min 2, max 255** | **Outlet/business name** |
| **business\_type** | **string** | **Yes** | **predefined list** | **e.g., Sepatu Olahraga** |
| **business\_phone** | **string** | **No** | **valid phone** | **Business contact number** |
| **owner\_name** | **string** | **Yes** | **min 2, max 255** | **Owner full name** |
| **owner\_phone** | **string** | **Yes** | **valid Indonesian phone** | **Owner WhatsApp number** |
| **email** | **string** | **Yes** | **valid email, unique** | **Login email** |
| **password** | **string** | **Yes** | **min 8 chars** | **Account password** |
| **password\_confirmation** | **string** | **Yes** | **must match password** | **Password confirmation** |
| **agree\_terms** | **boolean** | **Yes** | **must be true** | **Terms & conditions acceptance** |


Response 201:

\{ "success": true, "data": \{ "outlet\_id": "OUT-003", "message": "Registration successful.",

  "trial\_ends\_at": "2026-05-03T00:00:00Z" \} \}


# **9. API Contract — Outlets**


| **Method** | **Endpoint** | **Auth** | **Permission** | **Description** |
| - | - | - | - | - |
| **GET** | **/outlets** | **Bearer** | **owner** | **List outlets for current tenant** |
| **POST** | **/outlets** | **Bearer** | **owner** | **Create new outlet (respects plan quota)** |
| **GET** | **/outlets/:id** | **Bearer** | **owner** | **Get single outlet detail** |
| **PUT** | **/outlets/:id** | **Bearer** | **owner** | **Update outlet info** |
| **PATCH** | **/outlets/:id/status** | **Bearer** | **owner** | **Activate or deactivate outlet** |
| **POST** | **/outlets/:id/switch** | **Bearer** | **owner** | **Switch active outlet context** |


### **POST /outlets — Create Outlet**

Request Body:

| **Field** | **Type** | **Required** | **Validation** | **Description** |
| - | - | - | - | - |
| **name** | **string** | **Yes** | **min 2, max 255** | **Outlet name** |
| **address** | **string** | **Yes** | **min 5** | **Full address** |
| **phone** | **string** | **No** | **valid phone** | **Contact phone** |
| **email** | **string** | **No** | **valid email** | **Outlet email** |
| **manager\_user\_id** | **integer** | **No** | **exists in users** | **Assigned manager** |
| **open\_time** | **string** | **No** | **HH:MM format** | **Opening time** |
| **close\_time** | **string** | **No** | **HH:MM format** | **Closing time** |
| **sync\_catalog** | **boolean** | **No** | **default false** | **Sync products with primary outlet** |


Business Rules:

- Outlet count must not exceed plan.max\_outlets. Returns 422 QUOTA\_EXCEEDED if limit reached.

- First outlet created per tenant is automatically set as is\_primary = true.


### **PATCH /outlets/:id/status**

Request Body:

\{ "status": "inactive" \}

Allowed values: active | inactive. Deactivating freezes all active bookings and revokes staff access for that outlet.


# **10. API Contract — Products**


| **Method** | **Endpoint** | **Auth** | **Permission** | **Description** |
| - | - | - | - | - |
| **GET** | **/products** | **Bearer** | **product:list** | **List products with filters** |
| **POST** | **/products** | **Bearer** | **product:create** | **Create new product** |
| **GET** | **/products/:id** | **Bearer** | **product:read** | **Get product detail** |
| **PUT** | **/products/:id** | **Bearer** | **product:update** | **Update product** |
| **DELETE** | **/products/:id** | **Bearer** | **product:delete** | **Delete product** |
| **GET** | **/products/:id/availability** | **Bearer** | **product:read** | **Get monthly availability calendar** |
| **GET** | **/products/:id/units** | **Bearer** | **product:read** | **List all units for product** |


### **GET /products — Query Parameters**

| **Parameter** | **Type** | **Description** |
| - | - | - |
| **search** | **string** | **Search in name and SKU** |
| **category** | **string** | **Filter by category name** |
| **status** | **string** | **Filter by status: available | rented | maintenance** |
| **page** | **integer** | **Pagination page** |
| **per\_page** | **integer** | **Items per page** |


### **POST /products — Request Body**

| **Field** | **Type** | **Required** | **Validation** | **Description** |
| - | - | - | - | - |
| **name** | **string** | **Yes** | **min 2, max 255** | **Product name** |
| **sku** | **string** | **Yes** | **unique per outlet, max 100** | **Stock keeping unit** |
| **category** | **string** | **Yes** | **must exist in outlet categories** | **Category name** |
| **size** | **string** | **No** | **max 20** | **Size identifier** |
| **stock** | **integer** | **Yes** | **min 1, max 9999** | **Total unit count** |
| **price\_per\_day** | **integer** | **Yes** | **min 1000 (IDR)** | **Daily rental price** |
| **description** | **string** | **No** | **max 1000** | **Product description** |


On create, system auto-creates product\_units records (1 to stock count) with status = available.


### **GET /products/:id/availability — Query Parameters**

| **Parameter** | **Type** | **Required** | **Description** |
| - | - | - | - |
| **year** | **integer** | **Yes** | **Calendar year (e.g., 2026)** |
| **month** | **integer** | **Yes** | **Month 1–12** |


Response data.days is an array of 28–31 objects:

\{ "day": 15, "date": "2026-04-15", "available": 3, "booked": 2, "maintenance": 0,

  "all\_maintenance": false, "current\_month": true \}


# **11. API Contract — Bookings**


| **Method** | **Endpoint** | **Auth** | **Permission** | **Description** |
| - | - | - | - | - |
| **GET** | **/bookings** | **Bearer** | **booking:list** | **List bookings with filters** |
| **POST** | **/bookings** | **Bearer** | **booking:create** | **Create new booking** |
| **GET** | **/bookings/:id** | **Bearer** | **booking:read** | **Get booking detail with invoices** |
| **PUT** | **/bookings/:id** | **Bearer** | **booking:update** | **Update booking (confirmed only)** |
| **PATCH** | **/bookings/:id/pickup** | **Bearer** | **booking:checkin** | **Process pickup / check-in** |
| **PATCH** | **/bookings/:id/return** | **Bearer** | **booking:checkout** | **Process return / check-out** |
| **PATCH** | **/bookings/:id/cancel** | **Bearer** | **booking:cancel** | **Cancel a confirmed booking** |
| **GET** | **/bookings/:id/invoices** | **Bearer** | **booking:read** | **List all invoices for booking** |


### **GET /bookings — Query Parameters**

| **Parameter** | **Type** | **Description** |
| - | - | - |
| **search** | **string** | **Search by booking ID or customer name** |
| **status** | **string** | **confirmed | active | completed** |
| **customer\_id** | **string** | **Filter by customer** |
| **date\_from** | **date** | **Filter bookings with start\_date \>= date\_from** |
| **date\_to** | **date** | **Filter bookings with end\_date \<= date\_to** |


### **POST /bookings — Request Body**

| **Field** | **Type** | **Required** | **Validation** | **Description** |
| - | - | - | - | - |
| **customer\_id** | **string** | **Yes** | **exists, not blacklisted** | **Customer reference** |
| **start\_date** | **date** | **Yes** | **YYYY-MM-DD, \>= today** | **Rental period start** |
| **end\_date** | **date** | **Yes** | **YYYY-MM-DD, \>= start\_date** | **Rental period end** |
| **pickup\_date** | **date** | **No** | **YYYY-MM-DD, \<= start\_date** | **Defaults to start\_date** |
| **pickup\_time** | **string** | **No** | **HH:MM, defaults 09:00** | **Pickup time** |
| **return\_date** | **date** | **No** | **YYYY-MM-DD, \>= end\_date** | **Defaults to end\_date** |
| **return\_time** | **string** | **No** | **HH:MM, defaults 17:00** | **Return deadline** |
| **items** | **array** | **Yes** | **min 1 item** | **Array of booking items** |
| **items\[\].product\_id** | **integer** | **Yes** | **exists, product:read** | **Product to rent** |
| **items\[\].quantity** | **integer** | **Yes** | **min 1, \<= available units** | **Quantity to rent** |
| **payment\_type** | **string** | **Yes** | **dp | full** | **Payment type** |
| **dp\_amount** | **integer** | **Conditional** | **required if payment\_type=dp, \> 0** | **Down payment amount** |
| **payment\_method** | **string** | **Yes** | **cash | transfer | qris | ewallet** | **Payment method** |
| **notes** | **string** | **No** | **max 500** | **Optional booking notes** |


Business Rules:

- Blacklisted customers cannot be booked — 422 CUSTOMER\_BLACKLISTED

- Product availability checked for entire pickup\_date to return\_date range — 422 UNIT\_NOT\_AVAILABLE

- System auto-assigns specific product units and creates booking\_items records

- Payment recorded as invoice immediately upon booking creation


### **PATCH /bookings/:id/pickup — Request Body**

| **Field** | **Type** | **Required** | **Validation** | **Description** |
| - | - | - | - | - |
| **collateral\_type** | **string** | **Yes** | **ktp | sim | passport | cash | other** | **Type of collateral** |
| **collateral\_proof** | **string** | **Yes** | **min 5** | **KTP number or cash description** |
| **pay\_now** | **boolean** | **No** | **default false** | **Collect remaining DP balance at pickup** |
| **payment\_method** | **string** | **Conditional** | **required if pay\_now=true** | **Payment method for pelunasan** |
| **notes** | **string** | **No** | **max 500** | **Pickup notes** |


### **PATCH /bookings/:id/return — Request Body**

| **Field** | **Type** | **Required** | **Validation** | **Description** |
| - | - | - | - | - |
| **item\_condition** | **string** | **Yes** | **good | minor\_damage | major\_damage** | **Condition assessment** |
| **late\_days** | **integer** | **No** | **min 0, default 0** | **Number of late days** |
| **late\_rate\_per\_day** | **integer** | **No** | **min 0** | **Late fee per day in IDR** |
| **damage\_fee** | **integer** | **No** | **min 0, default 0** | **Damage charge in IDR** |
| **damage\_description** | **string** | **Conditional** | **required if damage\_fee \> 0** | **Damage description** |
| **payment\_method** | **string** | **Conditional** | **required if any amount due \> 0** | **Payment method** |
| **notes** | **string** | **No** | **max 500** | **Return notes** |


Business Rules:

- If payment\_type = dp and remaining balance \> 0, payment\_method is mandatory

- System auto-calculates total\_due = remaining\_dp\_balance + late\_fee + damage\_fee

- All assigned product units return to status = available

- Booking status changes to completed


# **12. API Contract — Customers**


| **Method** | **Endpoint** | **Auth** | **Permission** | **Description** |
| - | - | - | - | - |
| **GET** | **/customers** | **Bearer** | **customer:read** | **List customers with filters** |
| **POST** | **/customers** | **Bearer** | **customer:create** | **Create new customer** |
| **GET** | **/customers/:id** | **Bearer** | **customer:read** | **Get customer detail + history** |
| **PUT** | **/customers/:id** | **Bearer** | **customer:update** | **Update customer info** |
| **PATCH** | **/customers/:id/blacklist** | **Bearer** | **customer:update** | **Blacklist customer** |
| **PATCH** | **/customers/:id/unblock** | **Bearer** | **customer:update** | **Remove from blacklist** |


### **PATCH /customers/:id/blacklist — Request Body**

| **Field** | **Type** | **Required** | **Validation** | **Description** |
| - | - | - | - | - |
| **reason** | **string** | **Yes** | **min 10, max 500** | **Documented reason for blacklisting** |


Business Rules:

- Blacklisted customers cannot create new bookings. Existing active bookings are not affected.

- Unblocking removes blacklist\_reason and blacklist\_date, sets status = active.


### **GET /customers — Query Parameters**

| **Parameter** | **Type** | **Description** |
| - | - | - |
| **search** | **string** | **Search by name, phone, customer ID** |
| **status** | **string** | **active | blacklisted** |


# **13. API Contract — Maintenance**


| **Method** | **Endpoint** | **Auth** | **Permission** | **Description** |
| - | - | - | - | - |
| **GET** | **/maintenance** | **Bearer** | **product:read** | **List maintenance records** |
| **POST** | **/maintenance** | **Bearer** | **product:update** | **Schedule maintenance for a unit** |
| **GET** | **/maintenance/:id** | **Bearer** | **product:read** | **Get maintenance detail** |
| **PATCH** | **/maintenance/:id/progress** | **Bearer** | **product:update** | **Update progress percentage** |
| **PATCH** | **/maintenance/:id/complete** | **Bearer** | **product:update** | **Mark maintenance as completed** |


### **POST /maintenance — Request Body**

| **Field** | **Type** | **Required** | **Validation** | **Description** |
| - | - | - | - | - |
| **product\_id** | **integer** | **Yes** | **exists in outlet** | **Product to maintain** |
| **unit\_number** | **integer** | **Yes** | **unit must be available** | **Specific unit number** |
| **type** | **string** | **Yes** | **cleaning | repair | inspection** | **Maintenance type** |
| **start\_date** | **date** | **Yes** | **YYYY-MM-DD** | **Scheduled start date** |
| **estimated\_completion** | **date** | **No** | **YYYY-MM-DD, \>= start\_date** | **Estimated end date** |
| **estimated\_cost** | **integer** | **No** | **min 0** | **Estimated cost in IDR** |
| **notes** | **string** | **No** | **max 500** | **Description of work needed** |


Business Rules:

- Unit must have status = available at time of scheduling. Returns 422 if unit is rented or already in maintenance.

- On schedule: unit\_status changes to maintenance, availability calendar updated.


### **PATCH /maintenance/:id/complete — Request Body**

| **Field** | **Type** | **Required** | **Validation** | **Description** |
| - | - | - | - | - |
| **actual\_cost** | **integer** | **Yes** | **min 0** | **Actual cost incurred in IDR** |
| **completion\_notes** | **string** | **No** | **max 500** | **Summary of work done** |


# **14. API Contract — Staff (Users)**


| **Method** | **Endpoint** | **Auth** | **Permission** | **Description** |
| - | - | - | - | - |
| **GET** | **/users** | **Bearer** | **user:list** | **List all staff in outlet** |
| **POST** | **/users** | **Bearer** | **user:create** | **Invite/create staff account** |
| **GET** | **/users/:id** | **Bearer** | **user:read** | **Get staff detail** |
| **PUT** | **/users/:id** | **Bearer** | **user:update** | **Update staff info and role** |
| **PATCH** | **/users/:id/status** | **Bearer** | **user:update** | **Activate or deactivate staff** |
| **POST** | **/users/:id/assign-role** | **Bearer** | **user:assign-role** | **Assign a role to staff** |


### **POST /users — Request Body**

| **Field** | **Type** | **Required** | **Validation** | **Description** |
| - | - | - | - | - |
| **name** | **string** | **Yes** | **min 2, max 255** | **Full name** |
| **email** | **string** | **Yes** | **valid email, unique** | **Login email** |
| **phone** | **string** | **No** | **valid phone** | **Contact number** |
| **role\_id** | **integer** | **Yes** | **exists, accessible to outlet** | **Role to assign** |
| **password** | **string** | **Conditional** | **min 8, required if send\_invite=false** | **Account password** |
| **password\_confirmation** | **string** | **Conditional** | **must match password** | **Password confirmation** |
| **permissions** | **array** | **No** | **valid permission strings** | **Override role permissions** |
| **send\_invite** | **boolean** | **No** | **default true** | **Send email invitation to set password** |


Business Rules:

- Total user count must not exceed plan.max\_users (NULL = unlimited)

- Owner role can only be assigned by Super Admin; tenants cannot self-upgrade to Owner


# **15. API Contract — Reports**


| **Method** | **Endpoint** | **Auth** | **Permission** | **Description** |
| - | - | - | - | - |
| **GET** | **/reports/dashboard** | **Bearer** | **report:view-dashboard** | **Today's dashboard metrics** |
| **GET** | **/reports/revenue** | **Bearer** | **report:view-revenue** | **Revenue report by period** |
| **GET** | **/reports/bookings** | **Bearer** | **report:view-revenue** | **Booking analytics** |
| **GET** | **/reports/products** | **Bearer** | **report:view-revenue** | **Product performance report** |
| **GET** | **/reports/customers** | **Bearer** | **report:view-revenue** | **Top customers report** |
| **GET** | **/reports/maintenance** | **Bearer** | **report:view-revenue** | **Maintenance cost report** |
| **POST** | **/reports/export** | **Bearer** | **report:export** | **Export report to PDF or Excel** |


### **GET /reports/revenue — Query Parameters**

| **Parameter** | **Type** | **Required** | **Description** |
| - | - | - | - |
| **period** | **string** | **Yes** | **month | year** |
| **month** | **integer** | **Conditional** | **1–12, required if period=month** |
| **year** | **integer** | **Yes** | **e.g., 2026** |


### **POST /reports/export — Request Body**

| **Field** | **Type** | **Required** | **Description** |
| - | - | - | - |
| **report\_type** | **string** | **Yes** | **revenue | bookings | products | customers | maintenance** |
| **format** | **string** | **Yes** | **pdf | excel** |
| **period** | **string** | **Yes** | **month | year** |
| **month** | **integer** | **Conditional** | **1–12** |
| **year** | **integer** | **Yes** |  |


Response: Returns a download URL valid for 5 minutes.

\{ "success": true, "data": \{ "download\_url": "https://cdn.posrental.com/exports/report-xxx.pdf",

  "expires\_at": "2026-04-19T10:05:00Z" \} \}


# **16. API Contract — Subscriptions**


| **Method** | **Endpoint** | **Auth** | **Permission** | **Description** |
| - | - | - | - | - |
| **GET** | **/subscriptions/current** | **Bearer** | **tenant:billing** | **Get current subscription status** |
| **POST** | **/subscriptions/upgrade** | **Bearer** | **tenant:billing** | **Upgrade to a different plan** |
| **POST** | **/subscriptions/addons** | **Bearer** | **tenant:billing** | **Purchase add-ons** |
| **GET** | **/subscriptions/invoices** | **Bearer** | **tenant:billing** | **List billing invoices** |
| **GET** | **/subscriptions/invoices/:id** | **Bearer** | **tenant:billing** | **Download billing invoice** |
| **DELETE** | **/subscriptions/cancel** | **Bearer** | **tenant:billing** | **Cancel current subscription** |


### **POST /subscriptions/addons — Request Body**

| **Field** | **Type** | **Required** | **Validation** | **Description** |
| - | - | - | - | - |
| **addons** | **array** | **Yes** | **min 1 item** | **Add-ons to purchase** |
| **addons\[\].addon\_id** | **integer** | **Yes** | **exists, enabled for current plan** | **Add-on ID from plan\_addons** |
| **addons\[\].quantity** | **integer** | **Yes** | **min 1, max 100** | **Number of units to add** |
| **payment\_method** | **string** | **Yes** | **cash | transfer | qris | ewallet** | **Payment method** |


Example: Purchase 1 extra outlet (+50,000/month) and 2 extra users (+15,000 each/month):

\{ "addons": \[\{ "addon\_id": 1, "quantity": 1 \}, \{ "addon\_id": 2, "quantity": 2 \}\],

  "payment\_method": "transfer" \}


# **17. API Contract — Super Admin**

| **Access Control** All Super Admin endpoints require a token with role = super\_admin. These endpoints are not scoped to any outlet\_id and access cross-tenant data. |
| - |


## **17.1 Outlet Management (Admin)**

| **Method** | **Endpoint** | **Description** |
| - | - | - |
| **GET** | **/admin/outlets** | **List all outlets with status, plan, and metrics** |
| **POST** | **/admin/outlets** | **Create outlet and send activation email to owner** |
| **GET** | **/admin/outlets/:id** | **Full outlet detail including subscription history** |
| **PUT** | **/admin/outlets/:id** | **Update any outlet field** |
| **PATCH** | **/admin/outlets/:id/status** | **Change outlet status (active/trial/inactive)** |


## **17.2 User Management (Admin)**

| **Method** | **Endpoint** | **Description** |
| - | - | - |
| **GET** | **/admin/users** | **List all users across all outlets** |
| **PATCH** | **/admin/users/:id/suspend** | **Suspend user — prevents login** |
| **PATCH** | **/admin/users/:id/restore** | **Restore suspended user** |


## **17.3 Subscription Management (Admin)**

| **Method** | **Endpoint** | **Description** |
| - | - | - |
| **GET** | **/admin/subscriptions** | **List all subscriptions with filters** |
| **PATCH** | **/admin/subscriptions/:id/upgrade** | **Force-upgrade outlet plan** |
| **PATCH** | **/admin/subscriptions/:id/cancel** | **Cancel outlet subscription** |
| **GET** | **/admin/subscriptions/:id/invoices** | **View billing invoices for outlet** |


## **17.4 Plans Management (Admin)**

| **Method** | **Endpoint** | **Description** |
| - | - | - |
| **GET** | **/admin/plans** | **List all subscription plans** |
| **POST** | **/admin/plans** | **Create new plan** |
| **PUT** | **/admin/plans/:id** | **Update plan limits and features** |
| **GET** | **/admin/plans/:id/addons** | **List add-ons for a plan** |
| **PUT** | **/admin/plans/:id/addons/:addon\_id** | **Update add-on price and enable state** |


## **17.5 System Reports (Admin)**

| **Method** | **Endpoint** | **Description** |
| - | - | - |
| **GET** | **/admin/reports/revenue** | **Platform-wide revenue analytics** |
| **GET** | **/admin/reports/outlets** | **Outlet performance comparison** |
| **GET** | **/admin/reports/subscriptions** | **Subscription distribution and MRR** |
| **GET** | **/admin/reports/growth** | **User and outlet growth trends** |
| **POST** | **/admin/reports/export** | **Export any admin report to PDF or Excel** |


## **17.6 System Settings (Admin)**

| **Method** | **Endpoint** | **Description** |
| - | - | - |
| **GET** | **/admin/settings** | **Get system configuration** |
| **PUT** | **/admin/settings** | **Update system config (app name, trial days, timezone, feature toggles)** |
| **GET** | **/admin/roles** | **List all system roles** |
| **POST** | **/admin/roles** | **Create custom role** |
| **PUT** | **/admin/roles/:id** | **Update role permissions** |
| **DELETE** | **/admin/roles/:id** | **Delete custom role (not system roles)** |
| **GET** | **/admin/templates** | **List communication templates** |
| **PUT** | **/admin/templates/:trigger/:channel** | **Update template (trigger: auth\_verify, sub\_remind, etc. | channel: email, whatsapp, push)** |


# **18. Validation Rules**

## **18.1 Global Field Validation**

| **Field** | **Rules** |
| - | - |
| **email** | **Valid RFC 5322 format. Unique per system for users. Unique per outlet for customers.** |
| **phone (Indonesian)** | **Starts with 08 or +62. Length 10–15 digits. No special characters except + prefix.** |
| **password** | **Minimum 8 characters. Must contain at least 1 letter and 1 number.** |
| **date fields** | **ISO 8601 format: YYYY-MM-DD. Must be a valid calendar date.** |
| **time fields** | **HH:MM 24-hour format. Range 00:00 – 23:59.** |
| **price/amount fields** | **Integer only (no decimals). IDR currency. Minimum values enforced per field.** |
| **string fields** | **Leading/trailing whitespace stripped automatically. HTML tags stripped.** |


## **18.2 Booking-Specific Business Validation**

| **Rule** | **Condition** | **Error Code** |
| - | - | - |
| **Blacklist check** | **customer.status must be active** | **CUSTOMER\_BLACKLISTED** |
| **Availability check** | **Available units \>= requested quantity for entire blocking period** | **UNIT\_NOT\_AVAILABLE** |
| **Date logic** | **pickup\_date \<= start\_date \<= end\_date \<= return\_date** | **INVALID\_DATE\_RANGE** |
| **DP amount** | **dp\_amount must be \> 0 and \< total\_amount when payment\_type = dp** | **INVALID\_DP\_AMOUNT** |
| **Edit restriction** | **Booking can only be edited when status = confirmed** | **BOOKING\_NOT\_EDITABLE** |
| **Pickup gate** | **Pickup can only be processed when status = confirmed** | **INVALID\_STATUS\_TRANSITION** |
| **Return gate** | **Return can only be processed when status = active** | **INVALID\_STATUS\_TRANSITION** |
| **Pelunasan gate** | **If remaining balance \> 0 at return, payment\_method is mandatory** | **PAYMENT\_REQUIRED** |
| **Damage description** | **Required when damage\_fee \> 0** | **DAMAGE\_DESCRIPTION\_REQUIRED** |


## **18.3 Subscription / Plan Validation**

| **Rule** | **Condition** | **Error Code** |
| - | - | - |
| **Outlet quota** | **outlet count \< plan.max\_outlets (or max\_outlets IS NULL)** | **QUOTA\_EXCEEDED** |
| **User quota** | **user count \< plan.max\_users (or max\_users IS NULL)** | **QUOTA\_EXCEEDED** |
| **Product quota** | **product count \< plan.max\_products (or max\_products IS NULL)** | **QUOTA\_EXCEEDED** |
| **Plan access** | **Outlet status must be active or trial to access APIs** | **SUBSCRIPTION\_INACTIVE** |
| **Trial expiry** | **trial\_ends\_at has not passed OR plan is paid** | **TRIAL\_EXPIRED** |
| **Add-on eligibility** | **Add-on must be enabled for current plan** | **ADDON\_NOT\_ELIGIBLE** |


## **18.4 Maintenance Validation**

| **Rule** | **Condition** | **Error Code** |
| - | - | - |
| **Unit availability** | **product\_unit.status must be available when scheduling** | **UNIT\_NOT\_AVAILABLE** |
| **Unit exists** | **unit\_number must exist in product\_units for given product\_id** | **UNIT\_NOT\_FOUND** |
| **Progress range** | **progress value must be 0–100 (integer)** | **INVALID\_PROGRESS** |
| **Completion cost** | **actual\_cost is required at completion, must be \>= 0** | **ACTUAL\_COST\_REQUIRED** |


## **18.5 Role & Permission Validation**

| **Rule** | **Condition** |
| - | - |
| **System role protection** | **System roles (is\_system = true) cannot be deleted or have their permissions modified by tenants** |
| **Custom role ownership** | **Custom roles can only be accessed/modified by the outlet that created them** |
| **Permission string format** | **Must match pattern: module:action (e.g., booking:create, product:read)** |
| **Owner role restriction** | **Owner role can only be assigned by Super Admin** |


# **19. Third-Party Integrations**

## **19.1 WhatsApp Integration**

| **Aspect** | **Detail** |
| - | - |
| **Integration Type** | **Deep Link only (wa.me URL) — no API key or webhook required** |
| **Usage in Public Catalog** | **Chat button opens WhatsApp with pre-filled product inquiry message including product name, rental dates, size, and quantity** |
| **Usage in Booking Module** | **Contact customer button opens WhatsApp with customer phone number** |
| **Usage in Customer Module** | **Chat button opens WhatsApp directly to customer number** |
| **Message Format** | **URL-encoded text pre-filled via query parameter: wa.me/\{phone\}?text=\{encoded\_message\}** |
| **Phone Format** | **Strip non-digits, prefix 62 (Indonesian country code)** |
| **Limitations** | **Customer must have WhatsApp installed. No delivery confirmation available.** |


## **19.2 Email Service (Planned)**

The Super Admin Settings panel defines templates for the following event triggers. These require integration with an email delivery service (e.g., Mailgun, SendGrid, AWS SES):

| **Trigger Key** | **Event** | **Recipient** | **Channels** |
| - | - | - | - |
| **auth\_verify** | **New user email verification** | **New user / owner** | **Email** |
| **auth\_forgot** | **Password reset request** | **User requesting reset** | **Email** |
| **sub\_remind** | **Subscription expiring soon (7 days)** | **Outlet owner** | **Email, WhatsApp, Push** |
| **payment\_success** | **Subscription payment confirmed** | **Outlet owner** | **Email** |
| **booking\_created** | **New booking alert** | **Outlet staff (owners/managers)** | **Email, Push** |
| **overdue** | **Customer has not returned product** | **Outlet staff** | **Email, Push** |


Template Variables:

- \{\{user\_name\}\} — Recipient name

- \{\{outlet\_name\}\} — Outlet/business name

- \{\{login\_url\}\} — Dashboard login URL

- \{\{trial\_end\_date\}\} — Trial expiry date

- \{\{verify\_url\}\} — Email verification link (auth\_verify)

- \{\{reset\_url\}\} — Password reset link (auth\_forgot)

- \{\{otp\_code\}\} — One-time password for WhatsApp verification


## **19.3 Payment Gateway (Planned)**

Current system records payments manually (cash, transfer, QRIS, e-wallet selected by staff). Future integration planned for automated payment processing:

| **Service** | **Use Case** | **Status** |
| - | - | - |
| **Midtrans / Xendit** | **Online subscription billing (Pro & Enterprise plan renewal)** | **Planned** |
| **QRIS Aggregator** | **In-outlet QRIS payment for booking transactions** | **Planned** |
| **Virtual Account** | **Bank transfer with auto-reconciliation for DP payments** | **Planned** |


## **19.4 File Storage (Planned)**

Photo uploads (collateral photos, product condition photos, maintenance receipts) require object storage:

| **Feature** | **Files Uploaded** | **Storage Path Pattern** |
| - | - | - |
| **Pickup collateral** | **KTP/SIM photo** | **bookings/\{booking\_id\}/collateral/\{filename\}** |
| **Pickup product condition** | **Product photos (multiple)** | **bookings/\{booking\_id\}/pickup/\{filename\}** |
| **Return product condition** | **Product photos (multiple)** | **bookings/\{booking\_id\}/return/\{filename\}** |
| **Maintenance receipt** | **Receipt/nota photo** | **maintenance/\{maint\_id\}/receipt/\{filename\}** |
| **Product photos** | **Catalog images** | **products/\{product\_id\}/\{filename\}** |


Recommended: AWS S3 or compatible (Cloudflare R2, MinIO). Max file size: 5MB. Accepted types: image/jpeg, image/png, application/pdf.


## **19.5 Google Fonts**

The application loads Inter font from Google Fonts CDN. This requires an external network request at page load time:

@import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700;800&display=swap');

For production deployments with strict CSP or offline requirements, self-host the font files.


# **20. Subscription & Billing Model**

## **20.1 Plan Tiers**

| **Feature** | **Trial (Free)** | **Basic (Rp 99K)** | **Pro (Rp 199K)** | **Enterprise (Rp 499K)** |
| - | - | - | - | - |
| **Duration** | **14 days** | **Monthly** | **Monthly** | **Monthly** |
| **Max Outlets** | **1** | **1** | **3** | **Unlimited** |
| **Max Users** | **5** | **10** | **Unlimited** | **Unlimited** |
| **Max Products** | **50** | **500** | **2,000** | **Unlimited** |
| **Multi-Branch Sync** | **No** | **No** | **Yes** | **Yes** |
| **Inventory Tracking** | **Yes** | **Yes** | **Yes** | **Yes** |
| **Advanced Reporting** | **No** | **No** | **No** | **Yes** |
| **API Access & Webhooks** | **No** | **No** | **No** | **Yes** |
| **Custom Domain** | **No** | **No** | **No** | **Yes** |
| **Add-ons Available** | **No** | **No** | **Yes** | **Yes** |


## **20.2 Add-Ons (Pro & Enterprise)**

| **Add-On** | **Price** | **Type** |
| - | - | - |
| **+1 Additional Outlet** | **Rp 50,000/month** | **outlet** |
| **+1 Additional User** | **Rp 15,000/month** | **user** |
| **+500 Additional Products** | **Rp 25,000/month** | **product** |


## **20.3 Subscription Status Flow**

| **Status** | **Description** | **Access Level** |
| - | - | - |
| **trial** | **Within 14-day free trial period** | **Full feature access (within plan limits)** |
| **active** | **Paid subscription, not yet expired** | **Full feature access** |
| **expiring** | **Subscription expires within 7 days** | **Full access + warning banner** |
| **expired** | **Past expiry date, no active subscription** | **Read-only access, no new bookings** |


## **20.4 MRR Calculation**

Monthly Recurring Revenue (MRR) visible in Super Admin Dashboard:

- MRR = Sum of all active subscriptions monthly price + add-on revenue

- Displayed in the Super Admin dashboard as Total MRR (e.g., Rp 18.2M)

- Growth rate calculated as (current MRR - previous MRR) / previous MRR × 100


# **21. Security Considerations**

## **21.1 Authentication & Authorization**

- JWT tokens with short expiry (1 hour access token, 7-day refresh token)

- All API routes validate token signature and expiry before processing

- Tenant isolation: outlet\_id from token applied automatically to all database queries

- Super Admin tokens explicitly excluded from outlet scoping

- Role and permission claims validated server-side on every request


## **21.2 Data Isolation**

- Every tenant-scoped query MUST include WHERE outlet\_id = :outlet\_id from JWT claims

- Direct object references (booking ID, product ID, etc.) are re-validated against outlet\_id to prevent IDOR attacks

- Super Admin endpoints require explicit super\_admin role check — not just any authenticated user


## **21.3 Input Security**

- All string inputs stripped of HTML tags to prevent XSS

- Parameterized queries only — no string concatenation in SQL

- File uploads validated for MIME type and file size before storage

- Rate limiting on auth endpoints: max 5 failed login attempts per 15 minutes


## **21.4 Sensitive Data**

- Passwords stored as bcrypt hashes (cost factor \>= 12)

- Customer collateral info (KTP numbers) treated as PII — access logged

- Invoice amounts stored as integers (IDR cents avoided — using full Rupiah integers)

- Subscription billing info not stored locally — delegated to payment gateway


## **21.5 Blacklist Customer Protection**

- Blacklisted customer check performed at booking creation time, not just at UI level

- API returns 422 CUSTOMER\_BLACKLISTED if booking attempted for blacklisted customer

- Blacklist reason is mandatory and stored with timestamp and staff ID for audit
