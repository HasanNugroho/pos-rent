# Technical Architecture

> **Runtime**: Bun | **Framework**: NestJS | **ORM**: Prisma | **DB**: PostgreSQL
> **Cache**: Redis | **Storage**: Cloudflare R2 | **Structure**: Modular

---

## 1. Technology Stack

| Layer | Technology | Notes |
|-------|-----------|-------|
| **Runtime** | Bun | Fast JS/TS runtime, drop-in Node.js replacement |
| **Framework** | NestJS | Modular, decorator-based, DI container |
| **ORM** | Prisma | Type-safe queries, migrations, schema-first |
| **Database** | PostgreSQL | Primary datastore, multi-tenant with `tenant_id` |
| **Cache** | Redis | Session, rate limiting, report caching, pub/sub |
| **Object Storage** | Cloudflare R2 | Product images, avatars, receipts, invoice PDFs |
| **Auth** | JWT (access + refresh tokens) | bcrypt for passwords |
| **Payment Gateway** | Midtrans / Xendit | Configurable per platform |
| **Push Notifications** | Firebase Cloud Messaging | Mobile + web push |
| **Email** | SMTP (Mailgun / SES) | Transactional emails |
| **WhatsApp** | Wablas / Fonnte | Automated messages |
| **Frontend (Mockup)** | HTML + TailwindCSS + Alpine.js | Current UI prototypes |
| **Frontend (Production)** | Next.js / React | TBD |

---

## 2. Project Structure (Modular NestJS)

```
src/
├── main.ts                          # Bun entrypoint
├── app.module.ts                    # Root module
│
├── common/                          # Shared utilities
│   ├── decorators/                  # @CurrentUser, @Permissions, @TenantId
│   ├── guards/                      # AuthGuard, RolesGuard, TenantGuard
│   ├── interceptors/                # ResponseInterceptor, LoggingInterceptor
│   ├── filters/                     # HttpExceptionFilter
│   ├── pipes/                       # ValidationPipe
│   ├── middlewares/                 # TenantMiddleware (inject tenantId)
│   └── interfaces/                  # Shared types
│
├── prisma/                          # Prisma module
│   ├── prisma.module.ts
│   ├── prisma.service.ts            # Extended PrismaClient with tenant scoping
│   └── schema.prisma
│
├── redis/                           # Redis module
│   ├── redis.module.ts
│   └── redis.service.ts
│
├── storage/                         # R2/S3 module
│   ├── storage.module.ts
│   └── storage.service.ts           # Upload, presigned URLs, delete
│
├── jobs/                            # Scheduled tasks (Cron)
│   ├── jobs.module.ts
│   ├── overdue-checker.job.ts
│   ├── subscription-renewal.job.ts
│   ├── trial-expiry.job.ts
│   └── report-aggregation.job.ts
│
└── modules/                         # Application Feature Modules
    ├── auth/                        # Authentication module
    │   ├── auth.module.ts
    │   ├── auth.controller.ts       # login, register, refresh, forgot-password
    │   ├── auth.service.ts
    │   ├── strategies/              # JwtStrategy, LocalStrategy
    │   └── dto/
    │
    ├── users/                       # User management
    │   ├── users.module.ts
    │   ├── users.controller.ts
    │   ├── users.service.ts
    │   └── dto/
    │
    ├── roles/                       # Role & permission management
    │   ├── roles.module.ts
    │   ├── roles.controller.ts
    │   ├── roles.service.ts
    │   └── dto/
    │
    ├── tenants/                     # Tenant / outlet management
    │   ├── tenants.module.ts
    │   ├── tenants.controller.ts    # CRUD outlets (owner-side)
    │   ├── tenants.service.ts
    │   └── dto/
    │
    ├── products/                    # Product management
    │   ├── products.module.ts
    │   ├── products.controller.ts
    │   ├── products.service.ts
    │   ├── categories.controller.ts
    │   ├── categories.service.ts
    │   └── dto/
    │
    ├── bookings/                    # Booking management
    │   ├── bookings.module.ts
    │   ├── bookings.controller.ts
    │   ├── bookings.service.ts
    │   └── dto/
    │
    ├── payments/                    # Payment processing
    │   ├── payments.module.ts
    │   ├── payments.controller.ts
    │   ├── payments.service.ts
    │   ├── gateways/
    │   │   ├── payment-gateway.interface.ts
    │   │   ├── midtrans.service.ts
    │   │   └── xendit.service.ts
    │   └── dto/
    │
    ├── customers/                   # Customer CRM
    │   ├── customers.module.ts
    │   ├── customers.controller.ts
    │   ├── customers.service.ts
    │   └── dto/
    │
    ├── maintenance/                 # Maintenance tracking
    │   ├── maintenance.module.ts
    │   ├── maintenance.controller.ts
    │   ├── maintenance.service.ts
    │   └── dto/
    │
    ├── reports/                     # Reporting & analytics
    │   ├── reports.module.ts
    │   ├── reports.controller.ts
    │   ├── reports.service.ts
    │   └── dto/
    │
    ├── subscriptions/               # Subscription & billing
    │   ├── subscriptions.module.ts
    │   ├── subscriptions.controller.ts
    │   ├── subscriptions.service.ts
    │   ├── invoices.service.ts
    │   └── dto/
    │
    ├── notifications/               # Multi-channel notifications
    │   ├── notifications.module.ts
    │   ├── notifications.service.ts
    │   ├── channels/
    │   │   ├── email.service.ts     # SMTP
    │   │   ├── push.service.ts      # Firebase FCM
    │   │   └── whatsapp.service.ts  # Wablas/Fonnte
    │   └── templates/
    │
    ├── settings/                    # Tenant settings
    │   ├── settings.module.ts
    │   ├── settings.controller.ts
    │   ├── settings.service.ts
    │   └── dto/
    │
    ├── catalog/                     # Public catalog (no auth)
    │   ├── catalog.module.ts
    │   ├── catalog.controller.ts
    │   └── catalog.service.ts
    │
    └── admin/                       # Super Admin module
        ├── admin.module.ts
        ├── admin-dashboard.controller.ts
        ├── admin-outlets.controller.ts
        ├── admin-users.controller.ts
        ├── admin-subscriptions.controller.ts
        ├── admin-reports.controller.ts
        ├── admin-settings.controller.ts
        └── services/

prisma/
├── schema.prisma                    # Full database schema
├── migrations/                      # Prisma migrations
└── seed.ts                          # Seed default roles, plans, admin user
```

---

## 3. Multi-Tenancy Architecture

### Strategy: Shared Database + `tenantId` Column

```
┌────────────────────────────┐
│       PostgreSQL           │
│  ┌──────────────────────┐  │
│  │ Global Tables        │  │
│  │  - users             │  │
│  │  - roles             │  │
│  │  - tenants           │  │
│  │  - plans             │  │
│  │  - subscriptions     │  │
│  │  - invoices          │  │
│  │  - system_settings   │  │
│  ├──────────────────────┤  │
│  │ Tenant-Scoped Tables │  │
│  │  - products          │  │ ← tenantId on every row
│  │  - categories        │  │
│  │  - bookings          │  │
│  │  - customers         │  │
│  │  - payments          │  │
│  │  - maintenances      │  │
│  └──────────────────────┘  │
└────────────────────────────┘
```

### Tenant Middleware

```typescript
// common/middlewares/tenant.middleware.ts
@Injectable()
export class TenantMiddleware implements NestMiddleware {
  use(req: Request, res: Response, next: NextFunction) {
    const user = req.user; // from JWT
    if (user && user.tenantId) {
      req['tenantId'] = user.tenantId;
    }
    next();
  }
}
```

### Prisma Tenant Scoping

```typescript
// prisma/prisma.service.ts
@Injectable()
export class PrismaService extends PrismaClient {
  forTenant(tenantId: string) {
    // Returns a scoped client that auto-filters by tenantId
    return this.$extends({
      query: {
        $allOperations({ args, query }) {
          args.where = { ...args.where, tenantId };
          return query(args);
        },
      },
    });
  }
}
```

---

## 4. Authentication Flow

```
Client                    Server                    Database
  │                         │                          │
  ├── POST /auth/login ────→│                          │
  │   {email, password}     ├── Verify credentials ──→ │
  │                         │← User + permissions ─────│
  │                         ├── Sign JWT ──────────────│
  │←── {accessToken,        │                          │
  │     refreshToken} ──────│                          │
  │                         │                          │
  ├── GET /api/products ───→│                          │
  │   Authorization:        ├── Verify JWT ───────────→│
  │   Bearer <token>        ├── Extract tenantId ──────│
  │                         ├── Query with scope ─────→│
  │←── {products[]} ────────│                          │
```

---

## 5. Caching Strategy (Redis)

| Use Case | Key Pattern | TTL |
|----------|-------------|-----|
| Session/Refresh Token | `session:{userId}` | 7 days |
| Rate Limiting | `ratelimit:{ip}:{endpoint}` | 15 min |
| Dashboard Stats | `report:dashboard:{tenantId}` | 5 min |
| Product List | `product:list:{tenantId}:{hash}` | 5 min |
| Availability Calendar | `availability:{productId}:{month}` | 1 min |
| Public Catalog | `catalog:{tenantSlug}` | 10 min |
| Admin Dashboard | `admin:dashboard` | 10 min |

**Invalidation**: Cache-aside pattern. Invalidate on relevant mutations via service-level hooks.

---

## 6. File Storage (Cloudflare R2)

### Bucket Structure

```
pos-sewa-storage/
├── tenants/{tenantId}/
│   ├── products/{productId}/       # Product images
│   ├── avatars/{userId}.webp       # User avatars
│   ├── receipts/{maintenanceId}/   # Maintenance receipts
│   ├── invoices/{invoiceId}.pdf    # Generated invoice PDFs
│   └── branding/logo.png           # Outlet logo
└── system/
    └── defaults/                    # Default images
```

### Upload Flow

1. Client requests presigned upload URL: `POST /api/storage/presign`
2. Server generates R2 presigned PUT URL (expires 15 min)
3. Client uploads directly to R2
4. Client confirms upload, server saves R2 key to database

---

## 7. Payment Gateway Architecture

```typescript
// payments/gateways/payment-gateway.interface.ts
export interface IPaymentGateway {
  createTransaction(params: CreateTransactionDto): Promise<TransactionResult>;
  getStatus(transactionId: string): Promise<TransactionStatus>;
  webhook(payload: any): Promise<WebhookResponse>;
}
```

### Midtrans Flow
```
Client → Server: Create booking payment
Server → Midtrans: Create Snap transaction
Midtrans → Server: Return snap_token + redirect_url
Client → Midtrans: User pays on Midtrans page
Midtrans → Server: Webhook notification (settlement/expire/deny)
Server: Update invoice status
```

### Xendit Flow
```
Client → Server: Create booking payment
Server → Xendit: Create invoice
Xendit → Server: Return invoice_url
Client → Xendit: User pays on Xendit page
Xendit → Server: Webhook callback
Server: Update invoice status
```

---

## 8. Scheduled Jobs (Cron)

| Job | Schedule | Description |
|-----|----------|-------------|
| `OverdueChecker` | Every 1 hour | Detect bookings past endDate → mark OVERDUE, send notification |
| `SubscriptionRenewal` | Daily 00:00 | Charge subscriptions due today via payment gateway |
| `TrialExpiry` | Daily 06:00 | Warn 3d/1d before trial ends, expire on day 14 |
| `ReportAggregation` | Daily 02:00 | Pre-compute daily aggregates for reporting |
| `PastDueEnforcer` | Daily 12:00 | PAST_DUE → EXPIRED after grace period |

---

## 9. Environment Variables

```env
# Runtime
PORT=3000
NODE_ENV=production

# Database
DATABASE_URL=postgresql://user:pass@host:5432/pos_sewa

# Redis
REDIS_URL=redis://host:6379

# JWT
JWT_SECRET=<secret>
JWT_REFRESH_SECRET=<secret>
JWT_EXPIRATION=15m
JWT_REFRESH_EXPIRATION=7d

# Cloudflare R2
R2_ACCOUNT_ID=<id>
R2_ACCESS_KEY_ID=<key>
R2_SECRET_ACCESS_KEY=<secret>
R2_BUCKET_NAME=pos-sewa-storage
R2_PUBLIC_URL=https://storage.pos-sewa.com

# Payment - Midtrans
MIDTRANS_SERVER_KEY=<key>
MIDTRANS_CLIENT_KEY=<key>
MIDTRANS_IS_PRODUCTION=false

# Payment - Xendit
XENDIT_SECRET_KEY=<key>
XENDIT_WEBHOOK_TOKEN=<token>

# Firebase
FIREBASE_SERVER_KEY=<key>

# SMTP
SMTP_HOST=smtp.mailgun.org
SMTP_PORT=587
SMTP_USER=<user>
SMTP_PASS=<pass>

# WhatsApp
WA_PROVIDER=wablas  # or fonnte
WA_API_URL=https://api.wablas.com
WA_API_TOKEN=<token>
```

---

## 10. Deployment

### Recommended Setup

```
┌─────────────┐     ┌──────────────┐     ┌──────────┐
│   Client    │────→│  Cloudflare  │────→│  Bun +   │
│  (Browser)  │     │  (CDN/WAF)   │     │  NestJS  │
└─────────────┘     └──────────────┘     └────┬─────┘
                                               │
                    ┌──────────────────────────┤
                    │              │            │
              ┌─────▼─────┐ ┌─────▼────┐ ┌─────▼─────┐
              │ PostgreSQL │ │  Redis   │ │   R2      │
              │ (Primary)  │ │ (Cache)  │ │ (Storage) │
              └───────────┘ └──────────┘ └───────────┘
```

### Docker Compose (Development)

```yaml
version: '3.8'
services:
  app:
    build: .
    ports: ["3000:3000"]
    environment:
      - DATABASE_URL=postgresql://postgres:password@db:5432/pos_sewa
      - REDIS_URL=redis://redis:6379
    depends_on: [db, redis]

  db:
    image: postgres:16
    environment:
      POSTGRES_DB: pos_sewa
      POSTGRES_PASSWORD: password
    volumes: [postgres_data:/var/lib/postgresql/data]
    ports: ["5432:5432"]

  redis:
    image: redis:7-alpine
    ports: ["6379:6379"]

volumes:
  postgres_data:
```

### Dockerfile

```dockerfile
FROM oven/bun:1 AS base
WORKDIR /app

COPY package.json bun.lockb ./
RUN bun install --frozen-lockfile

COPY . .
RUN bun run build
RUN bunx prisma generate

EXPOSE 3000
CMD ["bun", "run", "start:prod"]
```

---

## 11. Database Migrations

```bash
# Generate migration from schema changes
bunx prisma migrate dev --name <migration_name>

# Apply migrations in production
bunx prisma migrate deploy

# Seed default data
bunx prisma db seed

# View database
bunx prisma studio
```

### Seed Data

- Default system roles (Owner, Manager, Staff, Inventory)
- Default subscription plans (Free Trial, Basic, Pro, Enterprise)
- Super admin user account
- Default communication templates
