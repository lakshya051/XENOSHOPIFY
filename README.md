# XENOFLOW 🛍️

**A Complete Multi-Tenant Shopify Analytics & Data Management Platform**

XENOFLOW is a production-ready, full-stack multi-tenant Shopify data ingestion and insights platform. Built with enterprise-level architecture, it enables seamless onboarding of multiple Shopify stores, automated data synchronization, and comprehensive business analytics visualization.

## 🚀 Live Demo

- **Frontend**: [xenoshopify.vercel.app](https://xenoshopify.vercel.app)
- **Backend**: [https://xenoshopify.onrender.com](https://xenoshopify.onrender.com)
- **Database**: [Neon PostgreSQL](https://neon.tech)

## ✨ Key Features

### 🔐 **Secure Multi-Tenant Architecture**
- Complete OAuth 2.0 Shopify store connection
- Tenant isolation and secure data access
- JWT-based authentication with httpOnly cookies
- Enterprise-grade security practices

### 📊 **Automated Data Synchronization**
- Real-time webhook integration for instant updates
- Comprehensive data ingestion (Products, Orders, Customers, Line Items)
- Abandoned checkout tracking and recovery
- Manual sync triggers with progress indicators

### 📈 **Advanced Analytics Dashboard**
- Daily revenue trends with interactive charts
- Customer segmentation and lifetime value analysis
- Best-selling products visualization
- Sales performance by hour analysis
- Abandoned cart recovery insights

### 🛠️ **Complete Store Management**
- Multi-store switching capability
- Full CRUD operations on store data
- Bulk data operations and cleanup
- Store connection status monitoring

### 📱 **Modern User Experience**
- Responsive design for all devices
- Professional UI with Tailwind CSS 4
- Interactive data visualizations
- Real-time loading states and error handling

## 🏗️ Technology Stack

| Category | Technology | Version |
|----------|------------|---------|
| **Frontend** | Next.js, React, TypeScript | Next.js 15.5.3, React 19.1.0 |
| **Backend** | Node.js, Express.js, Prisma ORM | Node.js 18+, Express 4.18.2 |
| **Database** | Neon PostgreSQL | PostgreSQL 15+ |
| **Authentication** | JWT, bcrypt, httpOnly cookies | jsonwebtoken 9.0.2 |
| **Deployment** | Vercel (Frontend), Render (Backend) | Production Ready |
| **UI Components** | Tailwind CSS, Radix UI, Lucide React | Latest Stable |
| **Charts** | Recharts | 2.8+ |
| **Development** | TypeScript, ESLint, Turbopack | Full Type Safety |

## 🏛️ System Architecture

### Architecture Overview
```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Next.js App   │    │  Express API    │    │ Neon PostgreSQL │
│   (Vercel)      │◄──►│   (Render)      │◄──►│   (Database)    │
└─────────────────┘    └─────────────────┘    └─────────────────┘
         ▲                       ▲
         │                       │
         ▼                       ▼
┌─────────────────┐    ┌─────────────────┐
│   User Browser  │    │  Shopify API    │
│   (Frontend)    │    │   (Webhooks)    │
└─────────────────┘    └─────────────────┘
```

### Data Flow
1. **User Authentication**: Secure login via JWT tokens stored in httpOnly cookies
2. **Store Connection**: OAuth 2.0 flow for Shopify store authorization
3. **Data Ingestion**: Automated sync of store data via Shopify Admin API
4. **Real-time Updates**: Webhook processing for instant data synchronization
5. **Analytics Processing**: Data aggregation and visualization in dashboard
6. **Multi-tenancy**: Complete isolation of data per connected store

## 🚀 Quick Start

### Prerequisites
- Node.js 18+ installed
- npm or yarn package manager
- Shopify Partner account for app credentials

### 1. Clone the Repository
```bash
git clone https://github.com/lakshya051/XENOFLOW.git
cd XENOFLOW
```

### 2. Backend Setup
```bash
# Navigate to backend directory
cd backend

# Install dependencies
npm install

# Create environment file
cp .env.example .env

# Configure your environment variables (see Environment Variables section)
```

### 3. Database Setup
```bash
# Apply database schema
npx prisma migrate dev

# Generate Prisma client
npx prisma generate

# Start backend server
npm run dev
```
The backend will be running on `http://localhost:3000`

### 4. Frontend Setup
```bash
# Navigate to frontend directory (from root)
cd frontend

# Install dependencies
npm install

# Create environment file
cp .env.local.example .env.local

# Configure frontend environment
echo 'NEXT_PUBLIC_API_BASE_URL="http://localhost:3000"' > .env.local

# Start frontend with Turbopack
npm run dev
```
The frontend will be running on `http://localhost:3001`

## 🔧 Environment Configuration

### Backend Environment (.env)
```env
# Neon Database URL
DATABASE_URL="postgresql://username:password@ep-xxxxx.us-east-1.aws.neon.tech/dbname?sslmode=require"

# Shopify App Credentials
SHOPIFY_API_KEY="your_shopify_api_key"
SHOPIFY_API_SECRET="your_shopify_api_secret"

# JWT Configuration
JWT_SECRET="your_super_secure_jwt_secret_key"

# Server Configuration
HOST_URL="http://localhost:3000"
PORT=3000

# CORS Configuration
FRONTEND_URL="http://localhost:3001"
```

### Frontend Environment (.env.local)
```env
# API Base URL
NEXT_PUBLIC_API_BASE_URL="http://localhost:3000"

# Optional: Analytics
NEXT_PUBLIC_GA_ID="your_google_analytics_id"
```

## 🔗 API Endpoints

### Authentication Routes
| Method | Endpoint | Description | Protection |
|--------|----------|-------------|------------|
| POST | `/api/auth/register` | Register new user | Public |
| POST | `/api/auth/login` | User login | Public |
| POST | `/api/auth/logout` | User logout | Public |
| POST | `/api/auth/change-password` | Change password | 🔒 Protected |

### Shopify Integration Routes
| Method | Endpoint | Description | Protection |
|--------|----------|-------------|------------|
| GET | `/api/shopify/install` | Start OAuth flow | Public |
| GET | `/api/shopify/callback` | OAuth callback | Public |
| POST | `/api/shopify/webhook/orders/create` | Order webhook | Webhook |
| POST | `/api/shopify/webhook/checkouts/create` | Checkout webhook | Webhook |

### Tenant Management Routes
| Method | Endpoint | Description | Protection |
|--------|----------|-------------|------------|
| POST | `/api/tenants/link` | Link tenant to user | 🔒 Protected |
| GET | `/api/tenants/me/data` | Get user tenant data | 🔒 Protected |
| POST | `/api/tenants/:id/sync` | Trigger data sync | 🔒 Protected |
| DELETE | `/api/tenants/:id` | Delete tenant | 🔒 Protected |

## 🗄️ Database Schema

### Core Models
```prisma
model Tenant {
  id           String    @id @default(cuid())
  storeUrl     String    @unique
  accessToken  String
  createdAt    DateTime  @default(now())
  
  // Relations
  customers    Customer[]
  products     Product[]
  orders       Order[]
  users        User[]     @relation("TenantToUser")
  lineItems    LineItem[]
  checkouts    Checkout[]
}

model User {
  id        String   @id @default(cuid())
  email     String   @unique
  password  String
  createdAt DateTime @default(now())
  
  // Relations
  tenants   Tenant[] @relation("TenantToUser")
}

model Customer {
  id         BigInt   @id
  tenantId   String
  firstName  String?
  lastName   String?
  email      String?
  phone      String?
  createdAt  DateTime
  orderCount Int      @default(0)
  
  // Relations
  tenant     Tenant   @relation(fields: [tenantId], references: [id], onDelete: Cascade)
  orders     Order[]
  
  @@unique([id, tenantId])
}

model Product {
  id          BigInt   @id
  tenantId    String
  title       String
  vendor      String?
  productType String?
  createdAt   DateTime
  imageUrl    String?
  
  // Relations
  tenant      Tenant     @relation(fields: [tenantId], references: [id], onDelete: Cascade)
  lineItems   LineItem[]
  
  @@unique([id, tenantId])
}

model Order {
  id              BigInt    @id
  tenantId        String
  totalPrice      Float
  currency        String
  financialStatus String?
  createdAt       DateTime
  customerId      BigInt?
  checkoutId      BigInt?   @unique
  
  // Relations
  tenant          Tenant    @relation(fields: [tenantId], references: [id], onDelete: Cascade)
  customer        Customer? @relation(fields: [customerId, tenantId], references: [id, tenantId])
  lineItems       LineItem[]
  
  @@unique([id, tenantId])
}

model LineItem {
  id        BigInt  @id
  tenantId  String
  orderId   BigInt
  productId BigInt?
  quantity  Int
  price     Float
  title     String
  name      String?
  vendor    String?
  
  // Relations
  tenant    Tenant  @relation(fields: [tenantId], references: [id], onDelete: Cascade)
  order     Order   @relation(fields: [orderId, tenantId], references: [id, tenantId], onDelete: Cascade)
  product   Product? @relation(fields: [productId, tenantId], references: [id, tenantId])
  
  @@unique([id, tenantId])
}

model Checkout {
  id            BigInt   @id
  tenantId      String
  totalPrice    Float
  currency      String
  customerEmail String?
  webUrl        String?
  createdAt     DateTime @default(now())
  updatedAt     DateTime @updatedAt
  
  // Relations
  tenant        Tenant   @relation(fields: [tenantId], references: [id], onDelete: Cascade)
  
  @@unique([id, tenantId])
}
```

## 📁 Project Structure

```
XENOFLOW/
├── backend/                    # Node.js/Express API Server
│   ├── config/                # Configuration files
│   │   ├── database.js        # Database configuration
│   │   └── shopify.js         # Shopify API setup
│   ├── controllers/           # Route controllers
│   │   ├── auth.controller.js # Authentication logic
│   │   ├── shopify.controller.js # Shopify integration
│   │   ├── tenant.controller.js # Tenant management
│   │   └── webhook.controller.js # Webhook handlers
│   ├── middleware/            # Express middleware
│   │   ├── auth.middleware.js # JWT verification
│   │   ├── cors.middleware.js # CORS configuration
│   │   └── error.middleware.js # Error handling
│   ├── routes/                # API routes
│   │   ├── auth.routes.js     # Authentication endpoints
│   │   ├── shopify.routes.js  # Shopify OAuth & webhooks
│   │   └── tenant.routes.js   # Tenant management
│   ├── services/              # Business logic
│   │   ├── shopify.service.js # Shopify API interactions
│   │   ├── auth.service.js    # Authentication services
│   │   └── tenant.service.js  # Tenant operations
│   ├── utils/                 # Utility functions
│   │   ├── bigint.util.js     # BigInt serialization
│   │   ├── validation.js      # Input validation
│   │   └── helpers.js         # Common helpers
│   ├── prisma/                # Database layer
│   │   ├── schema.prisma      # Database schema
│   │   └── migrations/        # Migration files
│   ├── server.js              # Express server entry
│   ├── package.json           # Dependencies
│   └── .env.example           # Environment template
├── frontend/                  # Next.js React Application
│   ├── app/                   # Next.js App Router
│   │   ├── (auth)/           # Authentication pages
│   │   │   ├── login/        # Login page
│   │   │   ├── register/     # Registration page
│   │   │   └── shopify/      # Shopify callback
│   │   ├── dashboard/        # Protected dashboard
│   │   │   ├── analytics/    # Analytics views
│   │   │   ├── customers/    # Customer management
│   │   │   ├── orders/       # Order management
│   │   │   ├── products/     # Product management
│   │   │   └── settings/     # Store settings
│   │   ├── api/              # API routes (if needed)
│   │   ├── globals.css       # Global styles
│   │   ├── layout.tsx        # Root layout
│   │   └── page.tsx          # Home page
│   ├── components/           # Reusable components
│   │   ├── ui/              # Base UI components
│   │   ├── charts/          # Chart components
│   │   ├── forms/           # Form components
│   │   └── layout/          # Layout components
│   ├── lib/                 # Utility libraries
│   │   ├── api.ts           # API client
│   │   ├── auth.ts          # Auth utilities
│   │   ├── utils.ts         # Common utilities
│   │   └── types.ts         # TypeScript types
│   ├── hooks/               # Custom React hooks
│   │   ├── useAuth.ts       # Authentication hook
│   │   ├── useApi.ts        # API interaction hook
│   │   └── useLocalStorage.ts # Local storage hook
│   ├── styles/              # Additional styles
│   ├── public/              # Static assets
│   ├── package.json         # Dependencies
│   ├── next.config.js       # Next.js configuration
│   ├── tailwind.config.js   # Tailwind CSS config
│   └── .env.local.example   # Environment template
├── docs/                    # Documentation
│   ├── api.md              # API documentation
│   ├── deployment.md       # Deployment guide
│   └── development.md      # Development guide
├── .gitignore              # Git ignore rules
├── README.md               # This file
└── LICENSE                 # MIT License
```

## 🚀 Deployment

### Production Deployment Stack
- **Frontend**: [Vercel](https://vercel.com) - Automatic deployments from GitHub
- **Backend**: [Render](https://render.com) - Containerized Node.js deployment
- **Database**: [Neon](https://neon.tech) - Serverless PostgreSQL with branching

### Deployment Configuration

#### Frontend (Vercel)
1. Connect your GitHub repository to Vercel
2. Set environment variables in Vercel dashboard:
   ```
   NEXT_PUBLIC_API_BASE_URL=https://xenoshopify.onrender.com
   ```
3. Deploy automatically on push to main branch

#### Backend (Render)
1. Create a new Web Service on Render
2. Connect your GitHub repository
3. Configure build settings:
   ```
   Build Command: cd backend && npm install && npx prisma generate
   Start Command: cd backend && npm start
   ```
4. Set environment variables in Render dashboard:
   ```
   DATABASE_URL=your_neon_database_url
   SHOPIFY_API_KEY=your_shopify_api_key
   SHOPIFY_API_SECRET=your_shopify_api_secret
   JWT_SECRET=your_jwt_secret
   HOST_URL=https://xenoshopify.onrender.com
   FRONTEND_URL=https://xenoshopify.vercel.app
   ```

#### Database (Neon)
1. Create a new project on Neon
2. Copy the connection string
3. Run migrations: `npx prisma migrate deploy`
4. Generate client: `npx prisma generate`

### Local Development with Production Services
```bash
# Use production database locally
DATABASE_URL="your_neon_production_url"

# Point to production backend from local frontend
NEXT_PUBLIC_API_BASE_URL="https://xenoshopify.onrender.com"
```

## 🔧 Development Workflow

### Setting up Shopify Development App
1. Create a Shopify Partner account
2. Create a new app in your Partner Dashboard
3. Configure App URLs:
   - **App URL**: `https://xenoshopify.vercel.app`
   - **Allowed redirection URLs**: `https://xenoshopify.onrender.com/api/shopify/callback`
4. Copy API credentials to your environment variables

### Local Development with ngrok (Optional)
For testing webhooks locally:
```bash
# Install ngrok
npm install -g ngrok

# Expose local backend
ngrok http 3000

# Update your Shopify app settings with ngrok URL
```

### Available Scripts

#### Backend
```bash
npm run dev          # Start development server with nodemon
npm run start        # Start production server
npm run build        # Build application
npm run migrate      # Run database migrations
npm run generate     # Generate Prisma client
npm run studio       # Open Prisma Studio
npm run seed         # Seed database (if implemented)
```

#### Frontend
```bash
npm run dev          # Start development server with Turbopack
npm run build        # Build for production
npm run start        # Start production server
npm run lint         # Run ESLint
npm run type-check   # Run TypeScript checks
```

## 🔐 Security Features

### Authentication & Authorization
- JWT tokens with secure httpOnly cookies
- Password hashing using bcrypt
- Protected API routes with middleware
- Session management with automatic refresh

### Data Protection
- Multi-tenant data isolation
- SQL injection prevention with Prisma ORM
- CORS configuration for cross-origin requests
- Input validation and sanitization

### Shopify Integration Security
- OAuth 2.0 flow with PKCE
- Webhook signature verification
- Secure token storage and rotation
- API rate limiting compliance

## 📊 Analytics Features

### Revenue Analytics
- Daily, weekly, monthly revenue trends
- Revenue comparison periods
- Currency conversion support
- Export functionality

### Customer Insights
- Customer lifetime value (CLV)
- Customer segmentation
- New vs returning customer analysis
- Geographic distribution

### Product Performance
- Best-selling products
- Product category analysis
- Inventory turnover metrics
- Profit margin calculations

### Operational Metrics
- Sales by hour of day
- Order fulfillment status
- Abandoned cart recovery rates
- Payment method analysis

## 🐛 Troubleshooting

### Common Issues

#### Database Connection Issues
```bash
# Check connection string format
DATABASE_URL="postgresql://username:password@host:port/database?sslmode=require"

# Test connection
npx prisma db pull
```

#### Shopify Authentication Problems
```bash
# Verify app credentials
echo $SHOPIFY_API_KEY
echo $SHOPIFY_API_SECRET

# Check callback URL configuration
# Must match exactly in Shopify Partner Dashboard
```

#### Frontend API Connection Issues
```bash
# Verify API base URL
echo $NEXT_PUBLIC_API_BASE_URL

# Check CORS configuration in backend
# Ensure frontend URL is allowed
```

### Development Tips
1. Use `npm run migrate:reset` to reset database during development
2. Check browser console for frontend errors
3. Monitor backend logs for API issues
4. Use Prisma Studio for database inspection
5. Verify webhook URLs in Shopify admin

## 📈 Performance Optimization

### Database Optimization
- Indexed queries on frequently accessed fields
- Connection pooling with Prisma
- Efficient data pagination
- Query optimization with proper relations

### Frontend Performance
- Next.js automatic code splitting
- Image optimization with next/image
- Lazy loading for heavy components
- Service worker for caching (if implemented)

### API Performance
- Response compression
- Caching strategies for static data
- Rate limiting for API protection
- Efficient data serialization

## 🤝 Contributing

We welcome contributions! Please follow these steps:

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/amazing-feature`
3. Make your changes and add tests
4. Commit your changes: `git commit -m 'Add amazing feature'`
5. Push to the branch: `git push origin feature/amazing-feature`
6. Open a Pull Request

### Development Guidelines
- Follow TypeScript best practices
- Add tests for new features
- Update documentation as needed
- Follow the existing code style
- Ensure all tests pass before submitting

## 📝 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🔗 Links

- **Live Application**: [https://xenoshopify.vercel.app](https://xenoshopify.vercel.app)
- **API Documentation**: [API Docs](https://xenoshopify.onrender.com/api/docs)
- **GitHub Repository**: [https://github.com/lakshya051/XENOFLOW](https://github.com/lakshya051/XENOFLOW)
- **Issues**: [GitHub Issues](https://github.com/lakshya051/XENOFLOW/issues)

## 🙏 Acknowledgments

- [Shopify](https://shopify.dev) for comprehensive API documentation
- [Next.js](https://nextjs.org) for the amazing React framework
- [Prisma](https://prisma.io) for excellent database tooling
- [Neon](https://neon.tech) for serverless PostgreSQL
- [Render](https://render.com) for reliable backend hosting
- [Vercel](https://vercel.com) for seamless frontend deployment

## 📞 Support

For support and questions:
- Contact: [lakshyavarshney20@gmail.com](lakshyavarshney20@gmail.com)

---

**Built with ❤️ by [Lakshya](https://github.com/lakshya051)**

*XENOFLOW - Transforming Shopify data into actionable business insights*
