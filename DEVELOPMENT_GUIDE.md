# Development Guide

## 🚀 Getting Started

### Prerequisites
- Node.js 18+ / Go 1.21+
- PostgreSQL 14+
- Redis 6+
- Git

### Environment Setup

#### 1. Clone Repository
```bash
git clone https://github.com/your-org/pos-sewa.git
cd pos-sewa
```

#### 2. Backend Setup
```bash
cd backend
npm install
# or
go mod download
```

#### 3. Database Setup
```bash
# Create database
createdb pos_sewa_dev

# Run migrations
npm run migrate
# or
make migrate

# Seed data
npm run seed
```

#### 4. Environment Variables
Create `.env` file:
```env
# App
NODE_ENV=development
PORT=3000

# Database
DATABASE_URL=postgresql://user:password@localhost:5432/pos_sewa_dev
DB_HOST=localhost
DB_PORT=5432
DB_NAME=pos_sewa_dev
DB_USER=postgres
DB_PASSWORD=yourpassword

# Redis
REDIS_URL=redis://localhost:6379
REDIS_HOST=localhost
REDIS_PORT=6379

# JWT
JWT_SECRET=your-super-secret-jwt-key-change-this
JWT_EXPIRES_IN=15m
REFRESH_TOKEN_SECRET=your-refresh-token-secret
REFRESH_TOKEN_EXPIRES_IN=7d

# Email
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=your-email@gmail.com
SMTP_PASSWORD=your-app-password
EMAIL_FROM=noreply@yoursaas.com

# File Upload
AWS_ACCESS_KEY_ID=your-access-key
AWS_SECRET_ACCESS_KEY=your-secret-key
AWS_BUCKET_NAME=pos-sewa-dev
AWS_REGION=ap-southeast-1

# App URLs
FRONTEND_URL=http://localhost:3001
API_URL=http://localhost:3000

# Features
ENABLE_EMAIL_VERIFICATION=true
ENABLE_WHATSAPP=false
```

#### 5. Frontend Setup
```bash
cd frontend
npm install
cp .env.example .env.local
```

Frontend `.env.local`:
```env
NEXT_PUBLIC_API_URL=http://localhost:3000/v1
NEXT_PUBLIC_APP_URL=http://localhost:3001
```

---

## 📁 Project Structure

### Backend (Node.js + Express)
```
backend/
├── src/
│   ├── config/           # Configuration files
│   ├── controllers/      # Route controllers
│   ├── middlewares/      # Express middlewares
│   ├── models/           # Database models
│   ├── routes/           # API routes
│   ├── services/         # Business logic
│   ├── utils/            # Utility functions
│   ├── validators/       # Request validators
│   └── app.js            # Express app
├── migrations/           # Database migrations
├── seeders/              # Database seeders
├── tests/                # Tests
├── .env.example
├── package.json
└── server.js             # Entry point
```

### Frontend (Next.js)
```
frontend/
├── src/
│   ├── app/              # Next.js app directory
│   │   ├── (auth)/       # Auth pages
│   │   ├── (dashboard)/  # Dashboard pages
│   │   └── layout.tsx
│   ├── components/       # React components
│   │   ├── ui/           # shadcn/ui components
│   │   ├── forms/        # Form components
│   │   └── layouts/      # Layout components
│   ├── lib/              # Utility libraries
│   ├── hooks/            # Custom React hooks
│   ├── stores/           # Zustand stores
│   └── types/            # TypeScript types
├── public/               # Static files
├── .env.local.example
└── package.json
```

---

## 🔨 Development Workflow

### Run Development Servers

#### Backend
```bash
cd backend
npm run dev
# Backend running on http://localhost:3000
```

#### Frontend
```bash
cd frontend
npm run dev
# Frontend running on http://localhost:3001
```

#### Database Migrations
```bash
# Create new migration
npm run migration:create add_new_table

# Run migrations
npm run migrate

# Rollback
npm run migrate:rollback
```

---

## 🧪 Testing

### Backend Tests
```bash
# Run all tests
npm test

# Run with coverage
npm run test:coverage

# Run specific test
npm test -- auth.test.js

# Watch mode
npm run test:watch
```

### Frontend Tests
```bash
# Run unit tests
npm test

# Run e2e tests
npm run test:e2e

# Run with UI
npm run test:ui
```

---

## 📝 Coding Standards

### Code Style
- ESLint + Prettier
- Run before commit: `npm run lint`
- Auto-fix: `npm run lint:fix`

### Naming Conventions
- **Files**: `kebab-case.js`
- **Components**: `PascalCase.tsx`
- **Functions**: `camelCase`
- **Constants**: `UPPER_SNAKE_CASE`
- **Database tables**: `snake_case`

### Git Commit Messages
Format: `<type>(<scope>): <subject>`

Types:
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation
- `style`: Code style (formatting)
- `refactor`: Code refactoring
- `test`: Tests
- `chore`: Maintenance

Examples:
```
feat(auth): add email verification
fix(booking): resolve double booking issue
docs(api): update API documentation
```

---

## 🔄 Git Workflow

### Branches
- `main` - Production
- `develop` - Development
- `feature/*` - New features
- `bugfix/*` - Bug fixes
- `hotfix/*` - Production hotfixes

### Feature Development
```bash
# Create feature branch from develop
git checkout develop
git pull origin develop
git checkout -b feature/booking-management

# Make changes and commit
git add .
git commit -m "feat(booking): add check-in functionality"

# Push and create PR
git push origin feature/booking-management
```

### Pull Request
1. Create PR to `develop`
2. Add description & screenshots
3. Request review
4. Fix review comments
5. Merge after approval

---

## 🗄 Database Migrations

### Create Migration
```bash
npm run migration:create add_booking_status_field
```

Example migration:
```javascript
// migrations/20260415_add_booking_status_field.js
exports.up = function(knex) {
  return knex.schema.table('bookings', (table) => {
    table.string('status').defaultTo('pending');
    table.index('status');
  });
};

exports.down = function(knex) {
  return knex.schema.table('bookings', (table) => {
    table.dropColumn('status');
  });
};
```

### Run Migration
```bash
npm run migrate
```

---

## 🎨 UI Component Development

### Using shadcn/ui
```bash
# Add new component
npx shadcn-ui@latest add button
npx shadcn-ui@latest add dialog
```

### Custom Component Example
```tsx
// components/ProductCard.tsx
import { Card } from '@/components/ui/card';
import { Badge } from '@/components/ui/badge';

export const ProductCard = ({ product }) => {
  return (
    <Card>
      <img src={product.image} alt={product.name} />
      <h3>{product.name}</h3>
      <Badge>{product.status}</Badge>
      <p>Rp {product.price_per_day.toLocaleString()}/hari</p>
    </Card>
  );
};
```

---

## 🔐 Authentication Implementation

### Backend Middleware
```javascript
// middlewares/auth.js
const jwt = require('jsonwebtoken');

const authenticate = async (req, res, next) => {
  try {
    const token = req.headers.authorization?.split(' ')[1];
    if (!token) throw new Error('No token');
    
    const decoded = jwt.verify(token, process.env.JWT_SECRET);
    req.user = await User.findById(decoded.userId);
    req.tenantId = decoded.tenantId;
    
    next();
  } catch (error) {
    res.status(401).json({ error: 'Unauthorized' });
  }
};

const checkPermission = (permission) => {
  return async (req, res, next) => {
    const userPermissions = await getUserPermissions(req.user.id);
    if (!userPermissions.includes(permission)) {
      return res.status(403).json({ error: 'Forbidden' });
    }
    next();
  };
};

module.exports = { authenticate, checkPermission };
```

### Frontend Auth Hook
```typescript
// hooks/useAuth.ts
import { create } from 'zustand';

interface AuthState {
  user: User | null;
  token: string | null;
  login: (email: string, password: string) => Promise<void>;
  logout: () => void;
}

export const useAuth = create<AuthState>((set) => ({
  user: null,
  token: null,
  login: async (email, password) => {
    const response = await fetch('/api/auth/login', {
      method: 'POST',
      body: JSON.stringify({ email, password }),
    });
    const data = await response.json();
    set({ user: data.user, token: data.token });
  },
  logout: () => set({ user: null, token: null }),
}));
```

---

## 📊 Performance Guidelines

### Database
- Use indexes on frequently queried fields
- Use pagination (limit 20-50 items)
- Avoid N+1 queries
- Use database views for complex queries

### API
- Implement caching with Redis
- Use compression (gzip)
- Rate limiting
- Response time < 500ms

### Frontend
- Code splitting
- Lazy loading
- Image optimization
- Debounce user input

---

## 🐛 Debugging

### Backend
```javascript
// Use debug library
const debug = require('debug')('app:booking');

debug('Creating booking for customer %s', customerId);
```

### Frontend
```typescript
// React DevTools
console.log('[ProductList] Rendering with', products.length, 'items');
```

### Database Queries
```javascript
// Log queries in development
if (process.env.NODE_ENV === 'development') {
  knex.on('query', (query) => {
    console.log('[SQL]', query.sql);
  });
}
```

---

## 📦 Build & Deploy

### Backend Build
```bash
npm run build
npm start
```

### Frontend Build
```bash
npm run build
npm run start
```

### Docker
```bash
docker-compose up -d
```

---

## 🔍 Code Review Checklist

- [ ] Code follows style guide
- [ ] Tests added/updated
- [ ] Documentation updated
- [ ] No console.logs in production code
- [ ] Error handling implemented
- [ ] Security considerations checked
- [ ] Performance optimized
- [ ] Accessible (a11y)
- [ ] Mobile responsive

---

## 📚 Resources

- [Next.js Docs](https://nextjs.org/docs)
- [PostgreSQL Docs](https://www.postgresql.org/docs/)
- [shadcn/ui](https://ui.shadcn.com/)
- [TailwindCSS](https://tailwindcss.com/)

---

**Document Version**: 1.0  
**Last Updated**: April 15, 2026
