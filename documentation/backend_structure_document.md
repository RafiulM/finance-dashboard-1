# Backend Structure Document

This document outlines the backend architecture, database strategy, API design, hosting environment, infrastructure components, security measures, monitoring, and maintenance plans for the Finance Dashboard project. It is written in everyday language to make it clear for all readers.

## 1. Backend Architecture

### Overall Architecture
- We use **Next.js API Routes** as our backend framework. This means our server code lives alongside our frontend code, but runs on Node.js whenever an API endpoint is called.  
- Next.js gives us a file-based routing system for APIs under the `/app/api` folder. Each file or folder there becomes a separate endpoint.  
- All backend logic (authentication, data fetching, etc.) is implemented in these API route files.

### Design Patterns and Frameworks
- **API Routes**: A simple folder/file arrangement maps URLs to functions. No separate Express or Koa server is needed.  
- **Stateless Authentication**: We use JWTs (JSON Web Tokens) stored in HTTP-only cookies. The server checks these tokens on each request to decide if the user is allowed.

### Scalability, Maintainability, and Performance
- **Scalability**: Next.js apps on hosting platforms like Vercel automatically spin up more serverless function instances under load. This lets the backend handle more users without manual server management.  
- **Maintainability**: Keeping backend code close to the frontend simplifies project structure. Each API route is small and focused on a single job (sign-in, sign-up, data fetch).  
- **Performance**: Serverless functions start quickly and shut down when idle. Static assets (CSS, JS) are served directly from a CDN. We also use built-in caching headers to speed up repeated API calls.

## 2. Database Management

### Current and Future Data Storage
- **Version 1**: We rely on a static `data.json` file placed in `/app/dashboard`. This mock file provides sample financial data for development and demos.  
- **Future Phases**: We will migrate to a production database. Likely options are:  
  • **SQL (PostgreSQL)**: A relational database that stores structured tables and supports complex queries.  
  • **NoSQL (MongoDB)**: A document database for flexible, nested data structures.  

### Data Structure and Access
- **Static JSON (v1)**: The frontend reads `data.json` directly via a local fetch call.  
- **Planned Database Access**:  
  • We will use an ORM (e.g., Prisma or TypeORM) to connect the Next.js API routes to the database.  
  • Tables or collections will hold users, transactions, and possibly dashboard configurations.  
  • The backend will expose endpoints for reading and writing that data, replacing the static file.

### Data Management Practices
- **Migrations**: Employ a migration tool (e.g., Prisma Migrate) so database changes are versioned and repeatable.  
- **Backups**: Set up automated daily backups for production databases.  
- **Validation**: Validate all incoming data on both client and server sides to prevent bad or malicious data.

## 3. Database Schema

### Human-Readable Overview
1. **User Table**  
   Contains one row per registered user, storing credentials and basic profile info.  
2. **Transaction Table**  
   Stores each financial transaction with amount, date, category, and linked user.  
3. **Summary Table** (optional)  
   Precomputes and caches aggregated values (monthly spend, income) per user.

### SQL Schema (PostgreSQL)
```sql
-- 1. Users
CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  email VARCHAR(255) NOT NULL UNIQUE,
  password_hash VARCHAR(255) NOT NULL,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- 2. Transactions
CREATE TABLE transactions (
  id SERIAL PRIMARY KEY,
  user_id INTEGER NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  date DATE NOT NULL,
  amount NUMERIC(12, 2) NOT NULL,
  category VARCHAR(100) NOT NULL,
  description TEXT,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- 3. Summaries (optional)
CREATE TABLE summaries (
  id SERIAL PRIMARY KEY,
  user_id INTEGER NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  month VARCHAR(7) NOT NULL,        -- e.g., '2024-06'
  total_income NUMERIC(12, 2) NOT NULL,
  total_expenses NUMERIC(12, 2) NOT NULL,
  updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);
```

## 4. API Design and Endpoints

### Approach
- We follow a **RESTful** style inside Next.js API Routes.  
- Each route handles one resource (auth, transactions, summaries).  
- HTTP methods (GET, POST, PUT, DELETE) map to read, create, update, and delete operations.

### Key Endpoints
1. **Authentication** (`/app/api/auth/route.ts`)  
   - POST `/api/auth/signup`: Create a new user account.  
   - POST `/api/auth/signin`: Verify credentials and issue JWT.  
   - POST `/api/auth/signout`: Clear the JWT cookie.  

2. **Transactions** (to be created in `/app/api/transactions`)  
   - GET `/api/transactions`: Return the list of a user’s transactions.  
   - POST `/api/transactions`: Add a new transaction.  
   - PUT `/api/transactions/:id`: Update an existing transaction.  
   - DELETE `/api/transactions/:id`: Remove a transaction.  

3. **Summaries** (to be created in `/app/api/summaries`)  
   - GET `/api/summaries?month=YYYY-MM`: Return income/expense totals for that month.  

### Frontend-Backend Communication
- The React/Next.js front end uses the native `fetch` API or a client library (SWR/React Query) to call these endpoints.  
- Every request automatically includes the JWT cookie, so the backend can verify the user’s identity.

## 5. Hosting Solutions

### Cloud Provider
- **Vercel** (recommended) or **Netlify**: Both are optimized for Next.js applications.  
- Deploying is as simple as linking the GitHub repository and pushing to the `main` branch.

### Benefits
- **Reliability**: Platforms run on global serverless infrastructure with high uptime guarantees.  
- **Scalability**: Serverless functions automatically scale out when traffic spikes.  
- **Cost-Effectiveness**: You pay only for actual function invocations and bandwidth used.  
- **Built-in CDN**: Static assets and SSR pages are cached at edge locations worldwide.

## 6. Infrastructure Components

### Load Balancing and CDN
- A built-in global **CDN** balances user requests across the nearest edge nodes.  
- No additional load-balancer setup is needed—the hosting provider handles it.

### Caching Mechanisms
- **Edge Caching**: Static pages and API responses can be cached at the CDN edge.  
- **In-Memory Cache** (future): We may add Redis for query result caching and rate limiting.

### Content Delivery Network (CDN)
- All static assets (CSS, JS, images) are served from the CDN, ensuring low latency and fast load times.

## 7. Security Measures

### Authentication & Authorization
- **JWT Tokens**: Issued at sign-in, stored in **HTTP-only, SameSite** cookies.  
- **Route Protection**: API routes check for a valid JWT before running any logic.  

### Data Encryption & Transport
- **HTTPS Only**: All traffic is encrypted in transit.  
- **Password Hashing**: User passwords are hashed with a strong algorithm (e.g., bcrypt) before storing.

### Input Validation & Sanitization
- **Client-Side**: Forms validate email patterns and password rules before submission.  
- **Server-Side**: API routes re-validate all inputs to prevent injection attacks.

### Common Vulnerability Protections
- **CSRF**: Using same-site cookies and verifying origins for state-changing requests.  
- **XSS**: Escaping or sanitizing any user-supplied content before rendering.  
- **SQL Injection**: Parameterized queries via an ORM to avoid manual string concatenation.

## 8. Monitoring and Maintenance

### Monitoring Tools
- **Vercel Analytics** (or Netlify Analytics): Tracks request volume, latency, and error rates.  
- **Sentry** (future): Captures runtime errors and unhandled exceptions in both frontend and backend.

### Logging
- **Serverless Logs**: Next.js functions emit logs viewable in the hosting dashboard.  
- **Structured Logs**: Use a logging library (e.g., Winston or Pino) to format errors and warnings.

### Maintenance Strategies
- **Automated Tests**: Unit tests for API logic and integration tests for key flows (sign-up, sign-in, data fetch).  
- **Scheduled Dependencies Update**: Use Dependabot or a similar tool to keep packages up to date.  
- **Backups and Migrations**: Daily database backups and a migration pipeline ensure safe schema changes.

## 9. Conclusion and Overall Backend Summary

The Finance Dashboard backend is built on Next.js API Routes running in a serverless environment. It starts with a simple static data file for version 1 but is designed to grow into a robust, database-driven service using PostgreSQL. Our RESTful endpoints for authentication, transactions, and summaries provide clear, maintainable boundaries between features. Hosting on Vercel (or Netlify) delivers global performance, automatic scaling, and built-in CDNs. Security is ensured with HTTPS, JWTs in secure cookies, password hashing, and input validation. Monitoring and maintenance tools keep the system reliable and up to date. Altogether, this setup provides a solid foundation that matches project goals: fast development cycles, strong user data protection, and easy extension for future financial integrations.