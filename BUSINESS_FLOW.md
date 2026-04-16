# Business Flow - POS Rental System

## 📋 Table of Contents
1. [System Overview](#system-overview)
2. [Stakeholders](#stakeholders)
3. [Business Model](#business-model)
4. [User Journey Flows](#user-journey-flows)
5. [Operational Flows](#operational-flows)
6. [Payment & Billing Flow](#payment--billing-flow)
7. [System Architecture](#system-architecture)

---

## 🏗️ System Overview

**POS Rental System** adalah platform **multi-tenant SaaS** untuk mengelola bisnis rental (penyewaan) dengan fokus pada rental sepatu/equipment.

### Konsep Utama:
```
┌─────────────────────────────────────────────────────┐
│         SUPER ADMIN (Platform Owner)                │
│  - Manage semua outlets                             │
│  - Monitor revenue & metrics                        │
│  - Handle subscriptions                             │
└─────────────────┬───────────────────────────────────┘
                  │
        ┌─────────┴─────────┐
        │                   │
┌───────▼────────┐  ┌──────▼─────────┐
│  OUTLET A      │  │  OUTLET B      │  ... (127 outlets)
│  Sepatu        │  │  Sport Gear    │
│  Premium       │  │  Rental        │
└────────────────┘  └────────────────┘
        │                   │
   ┌────┴────┐         ┌────┴────┐
   │         │         │         │
┌──▼──┐  ┌──▼──┐   ┌──▼──┐  ┌──▼──┐
│Owner│  │Staff│   │Owner│  │Staff│
└─────┘  └─────┘   └─────┘  └─────┘
   │         │         │         │
   └────┬────┴─────────┴─────────┘
        │
   ┌────▼────┐
   │CUSTOMERS│ (Public - No Login)
   └─────────┘
```

---

## 👥 Stakeholders

### 1. **Super Admin** (Platform Level)
**Role**: Pemilik & operator platform POS Rental
**Akses**: `admin-*.html`
**Tanggung Jawab**:
- ✅ Onboard outlet baru
- ✅ Manage subscriptions & billing
- ✅ Monitor system metrics
- ✅ Handle technical support
- ✅ System configuration

### 2. **Outlet Owner** (Tenant Level)
**Role**: Pemilik bisnis rental
**Akses**: Semua fitur outlet
**Tanggung Jawab**:
- ✅ Setup bisnis & konfigurasi
- ✅ Manage produk & inventory
- ✅ Approve bookings
- ✅ Financial decisions
- ✅ Staff management
- ✅ Business reports

### 3. **Staff** (Kasir/Admin Outlet)
**Role**: Karyawan outlet
**Akses**: Terbatas sesuai role
**Tanggung Jawab**:
- ✅ Process bookings
- ✅ Check-in / Check-out
- ✅ Handle payments
- ✅ Customer service
- ✅ Update inventory status

### 4. **Customer** (End User)
**Role**: Penyewa produk
**Akses**: Public catalog + WhatsApp
**Tanggung Jawab**:
- ✅ Browse catalog
- ✅ Inquiry availability
- ✅ Booking confirmation
- ✅ Payment
- ✅ Pickup & return

---

## 💼 Business Model

### Multi-Tenant SaaS Model

```
┌────────────────────────────────────────────┐
│  SUBSCRIPTION PLANS                        │
├────────────┬─────────┬─────────┬───────────┤
│ Trial      │ Basic   │ Pro     │ Enterprise│
│ FREE       │ 99K/mo  │ 199K/mo │ 499K/mo   │
│ 14 days    │         │ ⭐      │           │
├────────────┼─────────┼─────────┼───────────┤
│ 1 user     │ 3 users │ 10 users│ Unlimited │
│ 10 products│ 100 prod│ 500 prod│ Unlimited │
│ 50 booking │ Unlimited│Unlimited│ Unlimited│
│ Basic      │ Standard│ Advanced│ Full      │
│ support    │ support │ support │ support   │
└────────────┴─────────┴─────────┴───────────┘

Revenue Streams:
1. Monthly subscription fees (MRR)
2. Transaction fees (optional)
3. Premium features (add-ons)
4. Custom integrations
```

### Value Proposition:

**For Outlet Owners:**
- 📊 Digital inventory management
- 💰 Automated billing & invoicing
- 📈 Business analytics
- 👥 Customer database
- ⏰ Time-saving automation

**For Customers:**
- 🔍 Easy product discovery
- 📱 Quick inquiry (WhatsApp)
- 💳 Flexible payment
- ⭐ Transparent pricing
- 📞 Direct communication

---

## 🔄 User Journey Flows

### 1️⃣ SUPER ADMIN JOURNEY

```
┌─────────────────────────────────────────────────────┐
│ PHASE 1: Platform Setup                             │
└─────────────────────────────────────────────────────┘
1. Login ke Super Admin panel
2. Configure system settings
3. Setup pricing plans
4. Prepare onboarding materials

┌─────────────────────────────────────────────────────┐
│ PHASE 2: Outlet Onboarding                          │
└─────────────────────────────────────────────────────┘
1. Receive outlet registration
2. Review & verify business info
3. Create outlet account
4. Assign subscription plan
5. Send activation email
   ↓
   Owner receives credentials

┌─────────────────────────────────────────────────────┐
│ PHASE 3: Monitoring & Support                       │
└─────────────────────────────────────────────────────┘
1. Monitor dashboard metrics
2. Track subscription status
3. Handle support tickets
4. Analyze system performance
5. Identify growth opportunities

┌─────────────────────────────────────────────────────┐
│ PHASE 4: Billing & Revenue                          │
└─────────────────────────────────────────────────────┘
1. Auto-generate invoices
2. Process payments
3. Handle failed payments
4. Manage plan upgrades/downgrades
5. Revenue reporting
```

### 2️⃣ OUTLET OWNER JOURNEY

```
┌─────────────────────────────────────────────────────┐
│ PHASE 1: Getting Started (Day 1-3)                  │
└─────────────────────────────────────────────────────┘
1. Register outlet via landing page
2. Receive activation email
3. Login pertama kali
4. Complete business profile
5. Setup outlet configuration:
   - Business hours
   - Payment methods
   - Tax settings
   - Categories

┌─────────────────────────────────────────────────────┐
│ PHASE 2: Setup Inventory (Day 3-7)                  │
└─────────────────────────────────────────────────────┘
1. Add product categories
2. Input products:
   - Upload photos
   - Set pricing
   - Add descriptions
   - Set stock quantity
   - Configure sizes/variants
3. Test booking flow
4. Setup custom fields (optional)

┌─────────────────────────────────────────────────────┐
│ PHASE 3: Team Setup (Day 7-14)                      │
└─────────────────────────────────────────────────────┘
1. Invite staff members
2. Assign roles & permissions
3. Training staff
4. Test scenarios

┌─────────────────────────────────────────────────────┐
│ PHASE 4: Go Live (Day 14+)                          │
└─────────────────────────────────────────────────────┘
1. Publish public catalog
2. Share catalog link
3. Enable online booking
4. Start processing orders

┌─────────────────────────────────────────────────────┐
│ PHASE 5: Daily Operations                           │
└─────────────────────────────────────────────────────┘
1. Review dashboard metrics
2. Check new bookings
3. Monitor inventory
4. Handle customer inquiries
5. Review financials
6. Schedule maintenance

┌─────────────────────────────────────────────────────┐
│ PHASE 6: Growth & Optimization                      │
└─────────────────────────────────────────────────────┘
1. Analyze reports
2. Identify top products
3. Optimize pricing
4. Expand product range
5. Consider plan upgrade
```

### 3️⃣ CUSTOMER JOURNEY

```
┌─────────────────────────────────────────────────────┐
│ PHASE 1: Discovery                                  │
└─────────────────────────────────────────────────────┘
1. Find catalog via:
   - Google search
   - Social media
   - Word of mouth
   - Direct link
2. Browse catalog
3. Filter by category
4. Search specific product

┌─────────────────────────────────────────────────────┐
│ PHASE 2: Product Selection                          │
└─────────────────────────────────────────────────────┘
1. View product details:
   - Photos
   - Description
   - Price
   - Available sizes
   - Stock status
2. Compare products
3. Check availability

┌─────────────────────────────────────────────────────┐
│ PHASE 3: Inquiry & Booking                          │
└─────────────────────────────────────────────────────┘
1. Click "Cek Ketersediaan"
2. Fill booking form:
   - Tanggal rental
   - Size
   - Quantity
3. Click "Tanya via WhatsApp"
4. WhatsApp opens dengan pre-filled message
5. Chat dengan owner/staff
6. Konfirmasi detail & harga
7. Agreement booking

┌─────────────────────────────────────────────────────┐
│ PHASE 4: Payment & Confirmation                     │
└─────────────────────────────────────────────────────┘
1. Receive payment instructions
2. Pay DP (Down Payment):
   - Transfer bank
   - E-wallet
   - Cash
3. Send payment proof
4. Staff verify payment
5. Booking confirmed
6. Receive booking confirmation

┌─────────────────────────────────────────────────────┐
│ PHASE 5: Pickup                                     │
└─────────────────────────────────────────────────────┘
1. Visit outlet on rental date
2. Bring ID & booking confirmation
3. Staff check-in:
   - Verify identity
   - Product inspection
   - Sign rental agreement
4. Pay remaining amount (if any)
5. Receive product
6. Receive return instructions

┌─────────────────────────────────────────────────────┐
│ PHASE 6: Usage Period                               │
└─────────────────────────────────────────────────────┘
1. Use product
2. Receive reminder (H-1 return)
3. Care for product

┌─────────────────────────────────────────────────────┐
│ PHASE 7: Return                                     │
└─────────────────────────────────────────────────────┘
1. Visit outlet on return date
2. Staff check-out:
   - Product inspection
   - Check damages
   - Calculate additional fees (if late)
3. Pay additional fees (if any)
4. Receive deposit refund (if applicable)
5. Booking complete
6. Optional: Review/feedback
```

---

## ⚙️ Operational Flows

### 📦 BOOKING FLOW (End-to-End)

```
┌──────────────┐
│  CUSTOMER    │
│  Browsing    │
└──────┬───────┘
       │
       ↓
┌──────────────┐
│ Select       │
│ Product      │
└──────┬───────┘
       │
       ↓
┌──────────────┐
│ Check        │
│ Availability │
│ Modal        │
└──────┬───────┘
       │
       ↓
┌──────────────┐
│ WhatsApp     │
│ Inquiry      │
└──────┬───────┘
       │
       ↓
┌──────────────┐     ┌──────────────┐
│   STAFF      │────►│ Create       │
│   Receives   │     │ Booking      │
│   Inquiry    │     │ in System    │
└──────┬───────┘     └──────┬───────┘
       │                     │
       ↓                     ↓
┌──────────────┐     ┌──────────────┐
│ Confirm      │     │ Status:      │
│ Availability │     │ PENDING      │
└──────┬───────┘     └──────────────┘
       │
       ↓
┌──────────────┐
│ Send Payment │
│ Instructions │
└──────┬───────┘
       │
       ↓
┌──────────────┐
│  CUSTOMER    │
│  Pays DP     │
└──────┬───────┘
       │
       ↓
┌──────────────┐     ┌──────────────┐
│   STAFF      │────►│ Verify       │
│   Receives   │     │ Payment      │
│   Proof      │     └──────┬───────┘
└──────────────┘            │
                            ↓
                    ┌──────────────┐
                    │ Status:      │
                    │ PAID (DP)    │
                    └──────┬───────┘
                           │
                           ↓
                    ┌──────────────┐
                    │ Send Booking │
                    │ Confirmation │
                    └──────┬───────┘
                           │
       ┌───────────────────┴───────────────────┐
       │                                       │
       ↓                                       ↓
┌──────────────┐                      ┌──────────────┐
│ Rental Date  │                      │ Reminder     │
│ Arrives      │                      │ Sent (H-1)   │
└──────┬───────┘                      └──────────────┘
       │
       ↓
┌──────────────┐     ┌──────────────┐
│  CUSTOMER    │────►│ Staff        │
│  Pickup      │     │ Check-in     │
└──────────────┘     └──────┬───────┘
                            │
                            ↓
                    ┌──────────────┐
                    │ Status:      │
                    │ ACTIVE       │
                    └──────┬───────┘
                           │
                           ↓
                    ┌──────────────┐
                    │ Usage Period │
                    └──────┬───────┘
                           │
                           ↓
┌──────────────┐     ┌──────────────┐
│  CUSTOMER    │────►│ Staff        │
│  Return      │     │ Check-out    │
└──────────────┘     └──────┬───────┘
                            │
                            ↓
                    ┌──────────────┐
                    │ Status:      │
                    │ COMPLETED    │
                    └──────────────┘
```

### 🔧 MAINTENANCE FLOW

```
1. Product needs maintenance
   ↓
2. Staff/Owner creates maintenance schedule
   - Select product
   - Set maintenance date
   - Add notes/issue
   - Mark product as unavailable
   ↓
3. Product blocked from booking
   ↓
4. Maintenance performed
   - Cleaning
   - Repair
   - Inspection
   ↓
5. Staff completes maintenance
   - Update status
   - Add completion notes
   - Upload photos (optional)
   ↓
6. Product available for booking again
```

### 📊 DAILY OPERATIONS

```
┌─────────────────────────────────────┐
│  MORNING (Opening)                  │
├─────────────────────────────────────┤
│  1. Staff login                     │
│  2. Check dashboard                 │
│  3. Review today's pickups          │
│  4. Prepare products                │
│  5. Check pending payments          │
└─────────────────────────────────────┘

┌─────────────────────────────────────┐
│  MIDDAY (Operations)                │
├─────────────────────────────────────┤
│  1. Process check-ins               │
│  2. Handle new inquiries            │
│  3. Verify payments                 │
│  4. Update inventory                │
│  5. Customer service                │
└─────────────────────────────────────┘

┌─────────────────────────────────────┐
│  AFTERNOON (Returns)                │
├─────────────────────────────────────┤
│  1. Process check-outs              │
│  2. Inspect returned products       │
│  3. Handle late returns             │
│  4. Calculate additional fees       │
│  5. Update product status           │
└─────────────────────────────────────┘

┌─────────────────────────────────────┐
│  EVENING (Closing)                  │
├─────────────────────────────────────┤
│  1. Reconcile cash                  │
│  2. Update bookings                 │
│  3. Generate daily report           │
│  4. Plan tomorrow's operations      │
│  5. Logout                          │
└─────────────────────────────────────┘
```

---

## 💰 Payment & Billing Flow

### For Customers (Rental Payment)

```
┌────────────────────────────────────────┐
│  BOOKING AMOUNT CALCULATION            │
├────────────────────────────────────────┤
│  Base Price      = Rp 75,000/day       │
│  Duration        = 3 days              │
│  Quantity        = 2 units             │
│  ────────────────────────────────────  │
│  Subtotal        = 75,000 × 3 × 2      │
│                  = Rp 450,000          │
│  ────────────────────────────────────  │
│  DP (30%)        = Rp 135,000 ✓        │
│  Remaining       = Rp 315,000 (pickup) │
│  ────────────────────────────────────  │
│  TOTAL           = Rp 450,000          │
└────────────────────────────────────────┘

Payment Timeline:
1. DP paid: When booking confirmed
2. Remaining: When pickup
3. Additional fees: When return (if any)
   - Late fee: Rp X/day
   - Damage fee: Varies
```

### For Outlets (Subscription Billing)

```
┌────────────────────────────────────────┐
│  MONTHLY SUBSCRIPTION CYCLE            │
├────────────────────────────────────────┤
│  Day 1:  Billing cycle starts          │
│  Day 25: Reminder sent                 │
│  Day 30: Invoice generated             │
│  Day 30: Auto-charge (if configured)   │
│  Day 35: Payment overdue notice        │
│  Day 40: Service suspension warning    │
│  Day 45: Service suspended             │
└────────────────────────────────────────┘

Subscription Status:
- TRIAL: Free 14 days
- ACTIVE: Paid & current
- EXPIRING: Within 7 days
- EXPIRED: Payment overdue
- SUSPENDED: Service disabled
```

---

## 🏗️ System Architecture

### 3-Tier Architecture

```
┌─────────────────────────────────────────────────────┐
│  PRESENTATION LAYER (Frontend)                      │
├─────────────────────────────────────────────────────┤
│  - catalog.html (Public)                            │
│  - admin-*.html (Super Admin)                       │
│  - *.html (Outlet Pages)                            │
│                                                      │
│  Tech: HTML5, TailwindCSS, Alpine.js, Chart.js     │
└────────────────┬────────────────────────────────────┘
                 │
                 ↓
┌─────────────────────────────────────────────────────┐
│  APPLICATION LAYER (Backend) - To Be Implemented    │
├─────────────────────────────────────────────────────┤
│  - Authentication & Authorization                    │
│  - Business Logic                                    │
│  - API Endpoints (REST/GraphQL)                     │
│  - Multi-tenancy Handler                            │
│  - Payment Gateway Integration                      │
│  - Email/SMS Service                                │
│  - File Upload Handler                              │
│                                                      │
│  Tech: Node.js/PHP/Python + Express/Laravel/Django  │
└────────────────┬────────────────────────────────────┘
                 │
                 ↓
┌─────────────────────────────────────────────────────┐
│  DATA LAYER (Database)                              │
├─────────────────────────────────────────────────────┤
│  - outlets (tenants)                                │
│  - users (all outlets)                              │
│  - products (per outlet)                            │
│  - bookings (per outlet)                            │
│  - customers (per outlet)                           │
│  - subscriptions (platform)                         │
│  - payments (transactions)                          │
│                                                      │
│  Tech: PostgreSQL/MySQL + Redis (cache)             │
└─────────────────────────────────────────────────────┘
```

### Multi-Tenancy Strategy

**Recommended: Single Database with tenant_id (Shared Schema)**

```
Benefits:
✅ Cost effective
✅ Easy maintenance
✅ Centralized backups
✅ Easy cross-tenant reporting

Implementation:
- Every table has tenant_id/outlet_id
- Row-level security
- Query always filtered by tenant
- Middleware validates tenant access
```

### Data Isolation

```
┌────────────────────────────────────┐
│  Request Flow                      │
└────────────────────────────────────┘
1. User login → Get tenant_id
2. Store in session/JWT
3. All queries filter by tenant_id
4. Middleware enforces isolation
5. Super admin can access all

Example Query:
SELECT * FROM products 
WHERE tenant_id = '${current_tenant_id}' 
AND status = 'active'
```

---

## 📈 Business Metrics & KPIs

### For Super Admin (Platform Level)

```
┌────────────────────────────────────┐
│  GROWTH METRICS                    │
├────────────────────────────────────┤
│  - Total Outlets                   │
│  - New Signups (monthly)           │
│  - Churn Rate                      │
│  - Customer Acquisition Cost       │
│  - Lifetime Value                  │
└────────────────────────────────────┘

┌────────────────────────────────────┐
│  REVENUE METRICS                   │
├────────────────────────────────────┤
│  - MRR (Monthly Recurring Revenue) │
│  - ARR (Annual Recurring Revenue)  │
│  - ARPU (Avg Revenue Per User)     │
│  - Revenue by Plan                 │
│  - Payment Success Rate            │
└────────────────────────────────────┘

┌────────────────────────────────────┐
│  ENGAGEMENT METRICS                │
├────────────────────────────────────┤
│  - Daily Active Outlets            │
│  - Feature Adoption Rate           │
│  - Support Ticket Volume           │
│  - User Satisfaction Score         │
└────────────────────────────────────┘
```

### For Outlet Owner (Business Level)

```
┌────────────────────────────────────┐
│  FINANCIAL METRICS                 │
├────────────────────────────────────┤
│  - Daily/Monthly Revenue           │
│  - Average Booking Value           │
│  - Revenue per Product             │
│  - Payment Collection Rate         │
│  - Outstanding Payments            │
└────────────────────────────────────┘

┌────────────────────────────────────┐
│  OPERATIONAL METRICS               │
├────────────────────────────────────┤
│  - Booking Volume                  │
│  - Utilization Rate                │
│  - Average Rental Duration         │
│  - On-time Return Rate             │
│  - Product Availability            │
└────────────────────────────────────┘

┌────────────────────────────────────┐
│  CUSTOMER METRICS                  │
├────────────────────────────────────┤
│  - Total Customers                 │
│  - New vs Returning                │
│  - Customer Retention Rate         │
│  - Average Customer Value          │
│  - Customer Satisfaction           │
└────────────────────────────────────┘
```

---

## 🎯 Success Criteria

### For Platform (Super Admin)
- ✅ 100+ active outlets in Year 1
- ✅ 80% retention rate
- ✅ Rp 100M+ ARR
- ✅ < 5% churn rate
- ✅ 4.5+ rating from outlets

### For Outlets (Owners)
- ✅ Break-even in 3 months
- ✅ 70%+ utilization rate
- ✅ 50+ bookings/month
- ✅ 30+ returning customers
- ✅ Rp 10M+ monthly revenue

### For Customers
- ✅ Easy product discovery
- ✅ < 5 min booking time
- ✅ Clear pricing
- ✅ Fast response (< 1 hour)
- ✅ Quality products

---

## 🚀 Implementation Roadmap

### Phase 1: MVP (Month 1-2)
- ✅ UI Design (DONE!)
- 🔨 Backend API
- 🔨 Database setup
- 🔨 Authentication
- 🔨 Core booking flow

### Phase 2: Beta (Month 3-4)
- 🔨 Payment integration
- 🔨 Email/SMS notifications
- 🔨 Multi-tenant isolation
- 🔨 Beta testing (5 outlets)

### Phase 3: Launch (Month 5-6)
- 🔨 Production deployment
- 🔨 Marketing website
- 🔨 Onboarding flow
- 🔨 Customer support

### Phase 4: Growth (Month 7+)
- 🔨 Mobile app
- 🔨 Advanced analytics
- 🔨 Integrations
- 🔨 Scale infrastructure

---

## 📞 Support & Escalation

```
┌────────────────────────────────────┐
│  CUSTOMER ISSUE                    │
└────────┬───────────────────────────┘
         ↓
┌────────────────────────────────────┐
│  Contact Outlet (WhatsApp/Phone)   │
└────────┬───────────────────────────┘
         ↓
    Resolved? ──YES──> End
         │
         NO
         ↓
┌────────────────────────────────────┐
│  Outlet Support Ticket             │
└────────┬───────────────────────────┘
         ↓
┌────────────────────────────────────┐
│  Super Admin Review                │
└────────┬───────────────────────────┘
         ↓
    Resolution & Follow-up
```

---

## 📝 Summary

**POS Rental System** adalah solusi comprehensive untuk:

1. **Super Admin**: Platform management & revenue
2. **Outlet Owners**: Business operations & growth
3. **Staff**: Daily operations & efficiency
4. **Customers**: Easy discovery & booking

**Key Success Factors:**
- ✅ Simple & intuitive UX
- ✅ Reliable & scalable tech
- ✅ Responsive support
- ✅ Competitive pricing
- ✅ Continuous improvement

---

**Document Version**: 1.0  
**Last Updated**: April 2024  
**Status**: ✅ UI Complete, Backend In Progress
