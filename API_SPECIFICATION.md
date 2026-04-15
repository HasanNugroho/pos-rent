# API Specification - SaaS Persewaan Barang

## 📝 Overview

RESTful API dengan authentication menggunakan JWT + Refresh Token.

**Base URL**: `https://api.yoursaas.com/v1`

---

## 🔐 Authentication Headers
```
Authorization: Bearer {access_token}
X-Tenant-ID: {tenant_id}
Content-Type: application/json
```

---

## 📚 API Endpoints Summary

### Authentication
- `POST /auth/register` - Register tenant baru
- `POST /auth/login` - Login
- `POST /auth/refresh` - Refresh token
- `POST /auth/logout` - Logout
- `POST /auth/forgot-password` - Request reset password
- `POST /auth/reset-password` - Reset password
- `POST /auth/verify-email` - Verify email
- `POST /auth/resend-verification` - Resend verification

### Users & Staff
- `GET /users` - List staff
- `POST /users` - Invite staff
- `GET /users/:id` - Get user detail
- `PUT /users/:id` - Update user
- `DELETE /users/:id` - Delete user
- `GET /users/me` - Current user

### Roles & Permissions
- `GET /roles` - List roles
- `POST /roles` - Create role
- `PUT /roles/:id` - Update role
- `GET /permissions` - List permissions

### Products
- `GET /products` - List products
- `POST /products` - Create product
- `GET /products/:id` - Get detail
- `PUT /products/:id` - Update
- `DELETE /products/:id` - Delete
- `GET /products/:id/availability` - Check availability

### Categories
- `GET /categories` - List
- `POST /categories` - Create
- `PUT /categories/:id` - Update
- `DELETE /categories/:id` - Delete

### Customers
- `GET /customers` - List
- `POST /customers` - Create
- `GET /customers/:id` - Get detail
- `PUT /customers/:id` - Update
- `DELETE /customers/:id` - Delete
- `GET /customers/:id/bookings` - Booking history

### Bookings
- `GET /bookings` - List
- `POST /bookings` - Create new booking
- `GET /bookings/:id` - Get detail
- `PUT /bookings/:id` - Update
- `DELETE /bookings/:id` - Cancel
- `POST /bookings/:id/check-in` - Check-in
- `POST /bookings/:id/check-out` - Check-out
- `GET /bookings/:id/invoice` - Get invoice

### Payments
- `GET /payments` - List
- `POST /payments` - Record payment
- `GET /payments/:id` - Get detail
- `POST /payments/:id/refund` - Refund

### Maintenance
- `GET /maintenance` - List
- `POST /maintenance` - Schedule maintenance
- `GET /maintenance/:id` - Get detail
- `PUT /maintenance/:id` - Update
- `POST /maintenance/:id/complete` - Mark complete

### Dashboard
- `GET /dashboard/stats` - Dashboard metrics
- `GET /dashboard/revenue` - Revenue data
- `GET /dashboard/bookings` - Booking stats

### Reports
- `GET /reports/rental` - Rental report
- `GET /reports/revenue` - Revenue report
- `GET /reports/products` - Product performance
- `GET /reports/customers` - Customer report

### Settings
- `GET /settings` - Get settings
- `PUT /settings` - Update settings

---

## 📄 Detailed Endpoint Examples

### POST /auth/register
**Request:**
```json
{
  "business_name": "Rental Sepatu Makmur",
  "email": "owner@rental.com",
  "password": "SecurePass123!",
  "first_name": "John",
  "last_name": "Doe"
}
```

### POST /products
**Request:**
```json
{
  "name": "Sepatu Futsal Nike",
  "sku": "SF-NIKE-001",
  "category_id": "uuid",
  "price_per_day": 50000,
  "price_per_week": 300000,
  "price_per_month": 1000000,
  "deposit_amount": 200000,
  "quantity_total": 5,
  "specifications": {
    "brand": "Nike",
    "size": "42"
  }
}
```

### POST /bookings
**Request:**
```json
{
  "customer_id": "uuid",
  "start_date": "2026-04-20",
  "end_date": "2026-04-25",
  "items": [
    {
      "product_id": "uuid",
      "quantity": 2
    }
  ],
  "notes": "Customer notes"
}
```

### POST /bookings/:id/check-in
**Request:**
```json
{
  "pickup_condition_notes": "Barang dalam kondisi baik",
  "pickup_photos": ["url1", "url2"]
}
```

### GET /dashboard/stats
**Response:**
```json
{
  "success": true,
  "data": {
    "today": {
      "revenue": 500000,
      "bookings": 5,
      "checkins": 3,
      "checkouts": 2
    },
    "month": {
      "revenue": 10000000,
      "bookings": 50,
      "customers": 30
    },
    "active_rentals": 15,
    "overdue_rentals": 2
  }
}
```

---

**Document Version**: 1.0  
**Last Updated**: April 15, 2026
