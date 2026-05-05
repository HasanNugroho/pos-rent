# Feature Spec — Authentication & Multi-Tenancy

> **Module**: Auth | **UI Pages**: `login.html`, `register.html`
> **Priority**: P0 | **Status**: ✅ UI Complete, 🔧 Backend Needed

---

## 1. Overview

The authentication system supports a **two-tier login** that routes users to the correct interface based on their role. It powers a multi-tenant architecture where each outlet is an isolated tenant.

---

## 2. User Flows

### 2.1 Login Flow

```
User enters email + password
        ↓
  Validate credentials (JWT)
        ↓
  Check user role
        ↓
  ├── Super Admin → /admin-dashboard.html
  └── Outlet User → /dashboard.html
```

**UI Reference**: `login.html`
- Email + Password form
- "Remember me" checkbox
- Demo account shortcuts (Owner / Staff / Super Admin)
- Link to registration page
- Forgot password link

### 2.2 Registration Flow

**UI Reference**: `register.html`
- New tenant registration with:
  - Owner name, email, phone
  - Business/outlet name
  - Password + confirmation
- Auto-creates: Tenant record, Owner user, Free Trial subscription

### 2.3 Forgot Password

- User enters email
- System sends reset link via email (template in admin communication settings)
- User clicks link → enters new password
- JWT token invalidation on password change

---

## 3. Authentication Mechanism

### JWT Strategy

| Token | Lifetime | Storage |
|-------|----------|---------|
| Access Token | 15 minutes | Memory / HTTP-only cookie |
| Refresh Token | 7 days | HTTP-only cookie |

### Token Payload

```json
{
  "sub": "user-uuid",
  "email": "user@outlet.com",
  "tenantIds": ["OUT-001", "OUT-002"],  // all outlets owned/assigned
  "roles": ["owner"],
  "permissions": ["product:create", "booking:read", ...],
  "iat": 1713500000,
  "exp": 1713500900
}
```

> **Note**: The JWT carries the list of tenants a user has access to, but does **not** determine the active tenant. The active tenant is set per-request via the `X-MERCHANT-ID` header (see below).

### Required Request Headers

All tenant-scoped API endpoints **must** include the `X-MERCHANT-ID` header:

```
Authorization: Bearer <access_token>
X-MERCHANT-ID: OUT-001
```

| Header | Required | Description |
|--------|----------|-------------|
| `Authorization` | ✅ All authenticated routes | Bearer JWT access token |
| `X-MERCHANT-ID` | ✅ All tenant-scoped routes | ID of the active tenant/outlet. Must be a tenant the user has access to (validated against `tenantIds` in JWT). |

**Validation flow:**

```
Request arrives with X-MERCHANT-ID: OUT-002
        ↓
Extract tenantIds from JWT → ["OUT-001", "OUT-002"]
        ↓
Check: is OUT-002 in ["OUT-001", "OUT-002"]? → ✅ Yes
        ↓
Inject tenantId = OUT-002 into request context
        ↓
All queries scoped to tenant OUT-002
```

**When `X-MERCHANT-ID` is NOT required:**
- Auth endpoints (`/api/auth/*`)
- Super Admin endpoints (`/api/admin/*`) — admin has cross-tenant access
- Public endpoints (`/api/public/*`)

**Error responses:**

| Case | HTTP Code | Error Code |
|------|-----------|------------|
| Missing `X-MERCHANT-ID` on tenant route | 400 | `MISSING_MERCHANT_ID` |
| User does not have access to that tenant | 403 | `MERCHANT_ACCESS_DENIED` |
| Tenant not found / inactive | 404 | `MERCHANT_NOT_FOUND` |

---

## 4. Role System

### 4.1 System Roles (immutable)

| Role | Scope | Description |
|------|-------|-------------|
| Super Admin | Platform | Full access to all tenants and system config |
| Owner | Tenant | Full access within their own tenant |
| Manager | Tenant | Operational access, no billing/user management |
| Staff / Kasir | Tenant | POS operations (booking, payment, customer) |
| Inventory Staff | Tenant | Product and maintenance management |

### 4.2 Custom Roles

- Tenant owners can create custom roles via the Settings UI
- Custom roles inherit a subset of permissions
- Examples: Customer Service, Accountant, Read-only Viewer

### 4.3 Permission Format

```
resource:action
```

Full permission list: see `PERMISSIONS_LIST.md`

---

## 5. Multi-Tenancy Model

### Isolation Strategy

**Recommended**: Shared database with `tenant_id` column on all tenant-scoped tables.

```
┌─────────────────────────┐
│  Shared Infrastructure  │
├─────────────────────────┤
│  tenants                │  ← Global
│  users                  │  ← Global (linked via tenant_id)
│  roles                  │  ← Global (system) + per-tenant (custom)
│  subscriptions          │  ← Global
├─────────────────────────┤
│  products               │  ← tenant_id scoped
│  bookings               │  ← tenant_id scoped
│  customers              │  ← tenant_id scoped
│  payments               │  ← tenant_id scoped
│  maintenances           │  ← tenant_id scoped
└─────────────────────────┘
```

### Data Isolation Rules

1. Every query MUST include `tenant_id` filter (enforced at ORM middleware level)
2. API endpoints extract `tenant_id` from the `X-MERCHANT-ID` request header, validated against the JWT's `tenantIds` claim
3. Owners with multiple outlets switch context by changing the `X-MERCHANT-ID` header value
4. Super Admin routes bypass tenant filtering (no `X-MERCHANT-ID` required)
5. Cross-tenant data access is prohibited at the application layer

---

## 6. API Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/auth/login` | Authenticate user, return JWT |
| POST | `/api/auth/register` | Register new tenant + owner |
| POST | `/api/auth/refresh` | Refresh access token |
| POST | `/api/auth/forgot-password` | Send password reset email |
| POST | `/api/auth/reset-password` | Reset password with token |
| GET  | `/api/auth/me` | Get current user profile |
| POST | `/api/auth/logout` | Invalidate refresh token |

---

## 7. Security Considerations

- Passwords hashed with **bcrypt** (min 12 rounds)
- Rate limiting on login endpoint (5 attempts / 15 min)
- Super Admin accounts require **2FA** (TOTP)
- IP whitelist option for admin access
- Audit log for all login/logout events
- CORS configured per-domain
