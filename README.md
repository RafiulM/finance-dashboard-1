# Finance Dashboard

A modern, secure financial dashboard that provides a clean, interactive interface for monitoring, analyzing, and visualizing financial data all in one place. Built with Next.js 15, featuring user authentication, dynamic charting, and intuitive data management.

## Overview

The Finance Dashboard simplifies personal finance management by combining secure user authentication with dynamic charting and tabular displays. It helps individuals and small teams track budgets, expenses, and performance metrics without juggling between spreadsheets or manual tools.

### Key Objectives
- **Fast Load Times**: Optimized for quick data visualization and smooth user experience
- **Intuitive Navigation**: Clean, user-friendly interface that's easy to navigate
- **Modular Structure**: Extensible codebase designed for future enhancements

### Target Audience
- Individuals managing personal finances
- Small teams tracking shared budgets and expenses
- Developers seeking a foundation for financial applications

### Core Features
- **Secure Authentication**: Complete sign-up, sign-in, and sign-out flows with JWT tokens
- **Interactive Dashboard**: Financial summary cards, dynamic charts, and comprehensive data tables
- **Data Visualization**: Charts and graphs powered by static data (extensible to live APIs)
- **Responsive Design**: Works seamlessly across desktop, tablet, and mobile devices

## Tech Stack

- **Framework:** [Next.js 15](https://nextjs.org/) (App Router with Turbopack)
- **Language:** TypeScript
- **Authentication:** [Better Auth](https://better-auth.com/) with JWT tokens
- **Database:** [Drizzle ORM](https://orm.drizzle.team/) with PostgreSQL
- **Styling:** [Tailwind CSS v4](https://tailwindcss.com/)
- **UI Components:** [shadcn/ui](https://ui.shadcn.com/) (New York style)
- **Charts:** [Recharts](https://recharts.org/) for data visualization
- **Theme System:** [next-themes](https://github.com/pacocoursey/next-themes)
- **Icons:** [Lucide React](https://lucide.dev/)

## Prerequisites

Before getting started, ensure you have:
- **Node.js 18+** installed ([Download here](https://nodejs.org/))
- **Docker and Docker Compose** (for database setup)
- **Git** for cloning the repository

## Installation and Setup

### 1. Clone the Repository
```bash
git clone https://github.com/RafiulM/finance-dashboard-1.git
cd finance-dashboard-1
```

### 2. Install Dependencies
```bash
# Using npm
npm install

# Using yarn
yarn install

# Using pnpm
pnpm install
```

### 3. Environment Configuration
Copy the environment example file and configure your secrets:
```bash
cp .env.example .env
```

Update the `.env` file with your configuration:
```env
# Database Configuration (defaults work with Docker)
DATABASE_URL=postgresql://postgres:postgres@localhost:5433/postgres
POSTGRES_DB=postgres
POSTGRES_USER=postgres
POSTGRES_PASSWORD=postgres

# Authentication - Generate a secure secret key
BETTER_AUTH_SECRET=your_very_secure_32_character_secret_key_here
BETTER_AUTH_URL=http://localhost:3000
NEXT_PUBLIC_BETTER_AUTH_URL=http://localhost:3000
```

### 4. Database Setup
Choose one of the following options:

#### Option A: Docker Setup (Recommended)
```bash
# Start PostgreSQL with Docker
npm run db:up

# Push database schema
npm run db:push
```

#### Option B: Local PostgreSQL
```bash
# Update DATABASE_URL in .env with your local database
# Then push the schema
npm run db:push
```

### 5. Start Development Server
```bash
npm run dev
# or
yarn dev
# or
pnpm dev
```

### 6. Access the Application
Open [http://localhost:3000](http://localhost:3000) in your browser. You should see the Finance Dashboard landing page with sign-up and sign-in options.

### Initial Setup
1. Click "Sign Up" to create your first account
2. Enter your email and password
3. You'll be automatically redirected to the dashboard
4. Explore the financial summary cards, charts, and data table

## Usage Guide

### User Flow

1. **Landing Page**: Navigate to [http://localhost:3000](http://localhost:3000)
2. **Sign Up**: 
   - Click "Sign Up" button
   - Enter email address and create a password
   - Click "Create Account"
   - Automatically redirected to dashboard upon successful registration
3. **Sign In** (returning users):
   - Click "Sign In" button
   - Enter your email and password
   - Click "Sign In"
   - Redirected to dashboard
4. **Dashboard Navigation**:
   - View financial summary cards (total income, expenses, etc.)
   - Explore interactive charts and data visualizations
   - Browse transaction data in the comprehensive data table
   - Use the sidebar for navigation
   - Click profile menu to sign out

### API Documentation

#### Authentication Endpoints

All authentication is handled through Better Auth at `/api/auth/*`. The system supports:

##### Sign Up
```http
POST /api/auth/sign-up
Content-Type: application/json

{
  "email": "user@example.com",
  "password": "securepassword"
}
```

**Success Response (201)**:
```json
{
  "user": {
    "id": "uuid",
    "email": "user@example.com"
  },
  "token": "jwt_token"
}
```

##### Sign In
```http
POST /api/auth/sign-in
Content-Type: application/json

{
  "email": "user@example.com", 
  "password": "securepassword"
}
```

**Success Response (200)**:
```json
{
  "user": {
    "id": "uuid",
    "email": "user@example.com"
  },
  "token": "jwt_token"
}
```

##### Sign Out
```http
POST /api/auth/sign-out
```

**Success Response (200)**:
```json
{
  "success": true
}
```

#### Error Responses

All authentication endpoints return standard HTTP status codes:
- `400`: Invalid request data
- `401`: Invalid credentials
- `409`: User already exists (sign-up)
- `500`: Server error

### Extending the Dashboard

#### Adding New Financial Data

The dashboard currently reads from `/app/dashboard/data.json`. To modify the financial data:

1. **Update data.json structure**:
```json
[
  {
    "id": 1,
    "header": "Monthly Income",
    "type": "Income",
    "status": "Active", 
    "target": "5000",
    "limit": "4500",
    "reviewer": "John Doe"
  }
]
```

2. **Create new chart components**:
```typescript
// components/custom-chart.tsx
import { Chart } from "@/components/ui/chart"

export function CustomChart({ data }) {
  return (
    <Chart>
      {/* Your chart configuration */}
    </Chart>
  )
}
```

3. **Add to dashboard page**:
```typescript
// app/dashboard/page.tsx
import { CustomChart } from "@/components/custom-chart"

export default function Dashboard() {
  return (
    <div>
      <CustomChart data={financialData} />
    </div>
  )
}
```

## Project Structure

```
finance-dashboard-1/
├── app/                         # Next.js App Router
│   ├── (auth)/                 # Authentication routes
│   │   ├── sign-in/           # Sign-in page
│   │   └── sign-up/           # Sign-up page
│   ├── api/                   # API routes
│   │   └── auth/              # Authentication endpoints
│   │       └── [...all]/      # Better Auth handler
│   ├── dashboard/             # Main dashboard
│   │   ├── data.json         # Financial data (static)
│   │   ├── layout.tsx        # Dashboard layout
│   │   ├── page.tsx          # Dashboard main page
│   │   └── theme.css         # Dashboard-specific styles
│   ├── globals.css           # Global styles
│   ├── layout.tsx            # Root layout
│   └── page.tsx              # Landing page
├── components/               # React components
│   ├── ui/                   # shadcn/ui components (40+)
│   ├── app-sidebar.tsx      # Dashboard sidebar
│   ├── auth-buttons.tsx     # Authentication buttons
│   ├── chart-area-interactive.tsx # Financial charts
│   ├── data-table.tsx       # Financial data table
│   ├── section-cards.tsx    # Summary cards
│   └── theme-provider.tsx   # Theme management
├── db/                       # Database configuration
│   ├── index.ts             # Database connection
│   └── schema/              # Database schemas
│       └── auth.ts          # Authentication schema
├── lib/                      # Utility functions
│   ├── auth.ts              # Better Auth configuration
│   ├── auth-client.ts       # Client-side auth
│   └── utils.ts             # General utilities
├── hooks/                    # Custom React hooks
├── documentation/            # Project documentation
└── docker-compose.yml       # Docker services
```

## Development Guidelines

### Code Organization
- **File-based Routing**: Next.js App Router conventions (`/app` directory)
- **Component Structure**: Reusable components in `/components`, UI primitives in `/components/ui`
- **Type Safety**: Full TypeScript implementation with strict mode
- **Styling**: Tailwind CSS with CSS variables for theming

### Development Standards
- **Authentication**: JWT tokens with HTTP-only cookies via Better Auth
- **Database**: Type-safe operations using Drizzle ORM
- **State Management**: React hooks and context for client state
- **Data Fetching**: Native `fetch` API (extensible to SWR/React Query)
- **Error Handling**: Consistent error boundaries and user feedback

### Architecture Principles
- **Modular Design**: Components and pages are self-contained
- **Separation of Concerns**: Clear separation between UI, business logic, and data
- **Extensibility**: Structure supports adding new dashboard widgets and features
- **Performance**: SSR optimization and efficient data loading

## Development Commands

### Application
```bash
npm run dev          # Start development server with Turbopack
npm run build        # Build for production
npm run start        # Start production server
npm run lint         # Run ESLint
```

### Database Management
```bash
npm run db:up        # Start PostgreSQL in Docker
npm run db:down      # Stop PostgreSQL container
npm run db:push      # Push schema changes to database
npm run db:studio    # Open Drizzle Studio (database GUI)
npm run db:generate  # Generate migration files
npm run db:reset     # Reset database (development only)
```

### Extending Components
```bash
# Add new shadcn/ui components
npx shadcn@latest add [component-name]

# Available components: button, card, input, table, chart, etc.
# All components support dark mode and are fully customizable
```

## Deployment

### Vercel (Recommended)
1. **Deploy to Vercel:**
   ```bash
   npm i -g vercel
   vercel
   ```

2. **Configure environment variables in Vercel dashboard:**
   - `DATABASE_URL`: Your managed PostgreSQL connection string
   - `BETTER_AUTH_SECRET`: Generate a secure 32-character secret
   - `BETTER_AUTH_URL`: Your Vercel deployment URL
   - `NEXT_PUBLIC_BETTER_AUTH_URL`: Your Vercel deployment URL

3. **Push database schema:**
   ```bash
   npm run db:push
   ```

### Docker Deployment
```bash
# Build and run with Docker
npm run docker:up

# For production, configure environment variables:
# - Strong database passwords
# - Secure JWT secrets
# - Production domain URLs
```

### Netlify Alternative
Compatible with Netlify with serverless functions for API routes. Configure build settings and environment variables similarly to Vercel.

## Known Limitations

### Current Version (v1.0)
- **Static Data**: Financial data is stored in `data.json` (no live API integration)
- **Single User**: No multi-user support or role-based access control
- **Basic Auth**: Email/password only (no OAuth or SSO)
- **No Exports**: PDF/CSV export functionality not implemented
- **Local Storage**: No persistent user preferences beyond authentication

### Future Roadmap
- **Live Data Integration**: Connect to financial APIs (Plaid, Yodlee, etc.)
- **Multi-user Support**: Team collaboration and shared dashboards
- **Advanced Analytics**: AI-powered insights and forecasting
- **Export Features**: PDF reports and CSV data exports
- **Mobile App**: Native iOS and Android applications
- **Real-time Updates**: WebSocket integration for live data
- **Advanced Charts**: More visualization types and customization

## Contributing

We welcome contributions to the Finance Dashboard! Please follow these guidelines:

### Development Workflow
1. Fork the repository
2. Create a feature branch: `git checkout -b feature/your-feature-name`
3. Make your changes following the coding standards
4. Test your changes thoroughly
5. Submit a pull request with a clear description

### Coding Standards
- Follow TypeScript best practices
- Use meaningful component and variable names
- Add JSDoc comments for complex functions
- Maintain consistent file structure
- Test authentication flows and UI interactions

### Areas for Contribution
- **UI Components**: New chart types, dashboard widgets
- **Data Integration**: API connectors and data transformation
- **Authentication**: OAuth providers, session management
- **Performance**: Optimization and caching improvements
- **Testing**: Unit tests, integration tests, E2E testing
- **Documentation**: Tutorials, API guides, troubleshooting

## License

This project is open source and available under the [MIT License](LICENSE).

## Support

For questions, issues, or feature requests:
- Create an [issue](https://github.com/RafiulM/finance-dashboard-1/issues)
- Review the [documentation](./documentation/)
- Check the [project requirements](./documentation/project_requirements_document.md)
