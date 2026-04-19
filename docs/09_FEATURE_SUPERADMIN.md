# Feature Spec — Super Admin Panel

> **Module**: Super Admin | **UI Pages**: `admin-dashboard.html`, `admin-outlets.html`, `admin-users.html`, `admin-subscriptions.html`, `admin-reports.html`, `admin-settings.html`
> **Priority**: P0–P1 | **Status**: ✅ UI Complete, 🔧 Backend Needed

---

## 1. Overview

The Super Admin panel is a platform-level management console for operating the multi-tenant SaaS. It provides centralized control over all tenants (outlets), users, subscriptions, system configuration, and third-party integrations.

**Design**: Purple-indigo gradient sidebar with crown logo, distinct from the white/slate tenant sidebar.

---

## 2. Modules

### 2.1 Admin Dashboard (`admin-dashboard.html`)

**System-wide KPIs:**

| Stat | Description |
|------|-------------|
| Total Outlets | All registered tenants |
| Total Users | Users across all tenants |
| Monthly Revenue | Platform MRR from subscriptions |
| Active Subscriptions | Currently paying tenants |

**Charts (Chart.js):**
- Revenue trend (6 months)
- Outlet growth per month

**Widgets:**
- Recent outlets (4 newest)
- System activity log (real-time)
- Subscription overview breakdown (Active/Trial/Expiring/Expired)

---

### 2.2 Manage Outlets (`admin-outlets.html`)

CRUD for tenants/outlets:

| Feature | Description |
|---------|-------------|
| Stats | Total, Active, Trial, Inactive counts |
| Search & Filter | By name/owner, by status |
| Outlet Cards | Avatar, name, ID, owner, status badge, metrics (users/products/bookings) |
| Add Outlet Modal | Name, owner, email, phone, address, plan selection, status |
| Edit Outlet | Update any outlet field |
| View | Navigate to outlet detail |

---

### 2.3 Manage Users (`admin-users.html`)

Cross-tenant user management:

| Feature | Description |
|---------|-------------|
| Stats | Total Users, Owners, Staff, Active Today |
| Filters | Search by name/email, filter by outlet, filter by role |
| Table | User (photo+name+email), Outlet, Role badge, Status, Last Login, Actions |
| Actions | View, Suspend/Unsuspend |
| Export | CSV export |

---

### 2.4 Subscriptions (`admin-subscriptions.html`)

See `08_FEATURE_SUBSCRIPTIONS.md` for full spec. Key admin views:
- All subscriptions table with MRR stats
- Plans overview cards
- Billing export

---

### 2.5 System Reports (`admin-reports.html`)

See `07_FEATURE_REPORTS.md` for full spec. Admin-specific:
- Revenue by outlet (Top 10 bar chart)
- User growth trend line
- Subscription distribution doughnut
- Booking trends (12 months)
- Export PDF/Excel

---

### 2.6 System Settings (`admin-settings.html`)

#### Tab: System Configuration
- Application name, support email, default currency
- Default trial days
- Global feature toggles: New registrations, Maintenance mode

#### Tab: Roles & Permissions
- Table of all system + custom roles
- Permissions matrix modal (scrollable grid)
- System roles locked from editing (Owner, Manager, Staff, Inventory)
- Custom role creation

#### Tab: Pricing Plans
- Plan cards grid (Free Trial, Basic, Pro, Enterprise)
- Plan editor form: name, price, billing cycle, limits
- **Accessible Features** toggles per plan (Multi-Branch Sync, Reporting, API Access, etc.)
- **Add-ons** configuration (price, enable/disable per add-on)

#### Tab: Communication Templates
- Event triggers: Email Verification, Forgot Password, Subscription Reminder, Payment Receipt, Welcome, Overdue Notice
- Multi-channel editor: Email, WhatsApp/Telegram, Push
- Dynamic variables support (`{{user_name}}`, `{{otp_code}}`, `{{reset_url}}`, etc.)

#### Tab: API & Integrations

| Service | Provider Options | Key Fields |
|---------|-----------------|------------|
| Payment Gateway | Midtrans / Xendit | Server Key, Client Key (Midtrans); Secret API Key, Webhook Token (Xendit); Environment toggle |
| Cloud Storage | Cloudflare R2 / AWS S3 | Account ID, Bucket, Access Key, Secret Key, Public Domain URL (R2); Region (S3) |
| WhatsApp Gateway | Wablas / Fonnte | Server URL, API Token |
| Firebase (Push) | FCM | Server Key, Service Account JSON |
| SMTP Email | Any | Host, Port, Username, Password |

All credential fields use `type="password"` with visibility toggle.

---

## 3. API Endpoints (Admin)

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/admin/dashboard` | Dashboard stats + charts |
| GET | `/api/admin/outlets` | List all outlets |
| POST | `/api/admin/outlets` | Create outlet |
| PATCH | `/api/admin/outlets/:id` | Update outlet |
| DELETE | `/api/admin/outlets/:id` | Deactivate outlet |
| GET | `/api/admin/users` | List all users |
| POST | `/api/admin/users/:id/suspend` | Suspend user |
| GET | `/api/admin/settings` | Get system settings |
| PATCH | `/api/admin/settings` | Update system settings |
| GET | `/api/admin/settings/integrations` | Get integration configs |
| PATCH | `/api/admin/settings/integrations/:provider` | Update integration |
| POST | `/api/admin/settings/integrations/:provider/test` | Test connection |
| GET | `/api/admin/roles` | List roles |
| POST | `/api/admin/roles` | Create custom role |
| PATCH | `/api/admin/roles/:id` | Update role permissions |
| GET | `/api/admin/templates` | List communication templates |
| PATCH | `/api/admin/templates/:id` | Update template content |

---

## 4. Security

- Super Admin routes protected by role check middleware
- Separate JWT claim: `isSuperAdmin: true`
- 2FA required for super admin accounts
- All admin actions logged to audit table
- IP whitelist support
- Rate limiting on sensitive endpoints
