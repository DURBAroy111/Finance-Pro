# 💰 FinancePro

A full-stack personal finance management web application built with **React + Node.js + MySQL**. FinancePro helps users track transactions, manage budgets, plan debt payoff, monitor investments, set savings goals, and generate detailed financial reports — all in one place.

---

## 📋 Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Tech Stack](#tech-stack)
- [Project Structure](#project-structure)
- [Database Models](#database-models)
- [API Reference](#api-reference)
- [Getting Started](#getting-started)
  - [Prerequisites](#prerequisites)
  - [Backend Setup](#backend-setup)
  - [Frontend Setup](#frontend-setup)
- [Environment Variables](#environment-variables)
- [Running the App](#running-the-app)
- [Seeding Demo Data](#seeding-demo-data)
- [Scheduled Jobs (Cron)](#scheduled-jobs-cron)
- [Financial Calculators](#financial-calculators)
- [Authentication Flow](#authentication-flow)
- [Security](#security)
- [Pages & Navigation](#pages--navigation)

---

## Overview

FinancePro is a personal finance tracker designed with a Bangladesh-first audience in mind (default currency: **BDT**, timezone: **Asia/Dhaka**, locale: **en-BD**), while being fully configurable for any currency or locale. It features a dark-themed, mobile-responsive UI built on React 19 with Tailwind CSS, backed by a RESTful Express API connected to a MySQL database via Sequelize ORM.

---

## Features

### 🔐 Authentication
- JWT-based auth with short-lived **access tokens** (15 min) and long-lived **refresh tokens** (7 days)
- Secure registration and login with bcrypt password hashing
- Auto token refresh via Axios interceptors
- Persistent login state across page refreshes

### 🏦 Accounts
- Multiple account types: `bank`, `cash`, `credit_card`, `savings`, `investment`, `crypto`, `wallet`
- Per-account balance tracking with credit limit support
- Toggle whether an account is included in net worth calculation
- Custom colors and icons per account

### 💸 Transactions
- Three transaction types: `income`, `expense`, `transfer`
- Rich metadata: merchant name, tags (JSON), receipt URL, notes
- Link transactions to categories and accounts
- Support for recurring transaction references
- Transfer between accounts with automatic balance updates

### 🏷️ Categories
- Hierarchical categories (parent → child) for fine-grained classification
- Separate types: `income`, `expense`, `both`
- Default system categories + user-created custom categories
- Custom icons and colors

### 📊 Budgets
- Budget periods: `weekly`, `monthly`, `custom`
- Budget rollover support
- Configurable alert threshold (default: 80% spent triggers a notification)
- Budget suggestion endpoint to auto-generate budgets from spending history

### 🎯 Goals
- Goal types: `emergency_fund`, `vacation`, `home`, `car`, `education`, `retirement`, `custom`
- Track current saved amount vs target amount
- Set a deadline and monthly contribution target
- Priority ranking across multiple goals
- Contribution history log per goal
- Status lifecycle: `active` → `completed` / `paused` / `cancelled`
- Optional link to a specific savings account

### 💳 Debt Management
- Debt types: `credit_card`, `personal_loan`, `home_loan`, `car_loan`, `student_loan`, `medical`, `other`
- Track principal, current balance, interest rate, minimum payment, and due day
- Two automated payoff strategies:
  - **Avalanche** — pays off highest-interest debt first (minimizes total interest)
  - **Snowball** — pays off smallest balance first (motivational)
  - **Manual** — user-defined order
- Full payment log with principal/interest breakdown
- Payoff plan projector: calculates months to debt-free and total interest paid

### 📈 Investments
- Asset types: `stocks`, `etf`, `mutual_fund`, `crypto`, `real_estate`, `bonds`, `gold`, `fixed_deposit`, `other`
- Track buy price, current price, quantity, platform, and ticker symbol
- Portfolio summary with gain/loss calculations

### 💼 Income Sources
- Source types: `salary`, `freelance`, `rental`, `dividends`, `side_hustle`, `business`, `other`
- Frequencies: `weekly`, `biweekly`, `monthly`, `quarterly`, `yearly`, `one_time`
- Taxable flag
- Link to a specific account

### 🔁 Recurring Transactions
- Frequencies: `daily`, `weekly`, `biweekly`, `monthly`, `quarterly`, `yearly`
- Auto-log mode: system automatically creates transactions on the due date via cron
- View upcoming recurring items
- Manual process trigger for individual items

### 📑 Reports
- **Monthly Summary** — income vs expenses for any month
- **Cash Flow** — time-series view of money in vs money out
- **Net Worth History** — daily/monthly snapshots of assets minus liabilities
- **Category Trends** — spending patterns per category over time
- **Savings Rate History** — `(income - expenses) / income × 100` over time
- **Top Merchants** — highest-spend merchants ranked

### 🔔 Notifications
- Notification types: `budget_alert`, `bill_due`, `goal_reminder`, `large_transaction`, `low_balance`, `monthly_review`, `goal_reached`, `subscription`
- Mark individual or all notifications as read
- Auto-generated by cron jobs (budget thresholds, bill reminders, goal milestones)

### 🧮 Financial Calculators
Five built-in calculators (no auth required):
1. **EMI** — Equated Monthly Installment for any loan
2. **SIP** — Systematic Investment Plan future value
3. **Compound Interest** — with configurable compounding frequency
4. **Retirement Planner** — projects corpus needed and monthly savings required
5. **Emergency Fund** — calculates recommended fund size based on monthly expenses

### 💡 Insights
- Automated financial insights generated from the user's own data (spending patterns, budget health, savings rate)

---

## Tech Stack

### Backend

| Technology | Purpose |
|---|---|
| Node.js | Runtime |
| Express.js 4 | Web framework |
| MySQL | Relational database |
| Sequelize 6 | ORM + migrations |
| bcryptjs | Password hashing |
| jsonwebtoken | JWT access & refresh tokens |
| express-rate-limit | Rate limiting (auth routes) |
| helmet | HTTP security headers |
| cors | Cross-origin resource sharing |
| morgan | HTTP request logging |
| winston | Application logging |
| node-cron | Scheduled background jobs |
| nodemailer | Email (bill reminders) |
| express-validator + Joi | Request validation |
| multer | File uploads |
| dotenv | Environment variable management |

### Frontend

| Technology | Purpose |
|---|---|
| React 19 | UI framework |
| Vite 7 | Build tool & dev server |
| React Router DOM 7 | Client-side routing |
| Redux Toolkit 2 | Global state management |
| React-Redux 9 | React-Redux bindings |
| Axios | HTTP client with interceptors |
| Tailwind CSS 3 | Utility-first CSS |
| Recharts | Data visualization / charts |
| Lucide React | Icon library |
| React Hot Toast | Toast notifications |
| date-fns 4 | Date formatting & manipulation |
| PostCSS + Autoprefixer | CSS processing |

---

## Project Structure
101 - Copy/
├── financepro-backend-fixed/
│   └── server/
│       ├── .env                         # Environment variables
│       ├── app.js                       # Express app, middleware, routes
│       ├── server.js                    # Entry point — DB connect + listen
│       ├── config/
│       │   └── db.js                    # Sequelize connection config
│       ├── models/
│       │   └── index.js                 # All Sequelize models + associations
│       ├── controllers/
│       │   ├── auth.controller.js
│       │   ├── account.controller.js
│       │   ├── transaction.controller.js
│       │   ├── budget.controller.js
│       │   ├── goal.controller.js
│       │   ├── debt.controller.js
│       │   ├── investment.controller.js
│       │   ├── recurring.controller.js
│       │   ├── report.controller.js
│       │   ├── insight.controller.js
│       │   ├── notification.controller.js
│       │   ├── calculator.controller.js
│       │   └── user.controller.js
│       ├── routes/
│       │   ├── auth.routes.js
│       │   ├── user.routes.js
│       │   ├── account.routes.js
│       │   ├── transaction.routes.js
│       │   ├── category.routes.js
│       │   ├── income.routes.js
│       │   └── combined.routes.js       # Budgets, goals, debts, investments,
│       │                                #   recurring, reports, insights,
│       │                                #   notifications, calculators
│       ├── middleware/
│       │   ├── auth.middleware.js       # JWT verification
│       │   └── errorHandler.js         # Global error handler
│       ├── services/
│       │   └── insight.service.js      # Insight generation logic
│       ├── jobs/
│       │   └── cronJobs.js             # Scheduled tasks
│       ├── utils/
│       │   ├── financeCalculations.js  # EMI, SIP, compound, payoff plans
│       │   └── response.js             # Standardized API response helpers
│       └── seeders/
│           └── seed.js                 # Demo data seeder
│
└── financepro-frontend-fixed/
└── client/
├── .env                         # VITE_API_URL
├── index.html
├── vite.config.js
├── tailwind.config.js
├── postcss.config.js
├── src/
│   ├── main.jsx                 # React entry point
│   ├── App.jsx                  # Router + auth guards
│   ├── index.css                # Global styles + CSS variables
│   ├── api/
│   │   └── axios.js             # Axios instance + token interceptors
│   ├── store/
│   │   ├── index.js             # Redux store
│   │   └── authSlice.js         # Auth state + async thunks
│   ├── hooks/
│   │   └── useApi.js            # Generic API hook
│   ├── components/
│   │   ├── common/
│   │   │   ├── ConfirmDialog.jsx
│   │   │   ├── EmptyState.jsx
│   │   │   ├── Modal.jsx
│   │   │   └── Spinner.jsx
│   │   └── layout/
│   │       ├── AppLayout.jsx
│   │       ├── Header.jsx
│   │       └── Sidebar.jsx
│   ├── pages/
│   │   ├── LoginPage.jsx
│   │   ├── RegisterPage.jsx
│   │   ├── DashboardPage.jsx
│   │   ├── TransactionsPage.jsx
│   │   ├── AccountsPage.jsx
│   │   ├── CategoriesPage.jsx
│   │   ├── BudgetsPage.jsx
│   │   ├── GoalsPage.jsx
│   │   ├── DebtsPage.jsx
│   │   ├── InvestmentsPage.jsx
│   │   ├── ReportsPage.jsx
│   │   ├── CalculatorsPage.jsx
│   │   └── NotificationsPage.jsx
│   └── utils/
│       ├── format.js            # Number/currency/date formatters
│       └── iconMap.js           # Icon name → Lucide component map
└── public/
└── vite.svg

---

## Database Models

FinancePro uses **15 Sequelize models** that map to MySQL tables:

| Model | Table | Description |
|---|---|---|
| `User` | `users` | Core user account with preferences |
| `Category` | `categories` | Hierarchical income/expense categories |
| `Account` | `accounts` | Financial accounts (bank, cash, crypto…) |
| `Transaction` | `transactions` | Individual income/expense/transfer records |
| `Budget` | `budgets` | Spending budgets per category and period |
| `Goal` | `goals` | Savings goals with deadlines |
| `GoalContribution` | `goal_contributions` | Individual deposits toward a goal |
| `RecurringTransaction` | `recurring_transactions` | Scheduled recurring income/expenses |
| `Debt` | `debts` | Loans and debt obligations |
| `DebtPayment` | `debt_payments` | Individual debt payment history |
| `Investment` | `investments` | Investment positions (stocks, crypto…) |
| `IncomeSource` | `income_sources` | Regular income stream definitions |
| `Notification` | `notifications` | System and alert notifications |
| `NetWorthSnapshot` | `net_worth_snapshots` | Daily net worth history |

### Key Model Fields

**User**
id, name, email, password_hash, avatar_url, currency (default: BDT),
locale (default: en-BD), timezone (default: Asia/Dhaka),
income_type (salaried|freelance|business|mixed),
monthly_income, onboarding_done, theme (light|dark|system), refresh_token

**Transaction**
id, user_id, account_id, category_id, type (income|expense|transfer),
amount, description, merchant, tags (JSON), date, notes,
receipt_url, is_recurring, recurring_id, transfer_to_account_id

**Budget**
id, user_id, category_id, name, amount,
period (weekly|monthly|custom), start_date, end_date,
rollover, alert_at_percent (default: 80), is_active

**Debt**
id, user_id, name, type, principal_amount, current_balance,
interest_rate, minimum_payment, due_day, lender_name,
start_date, end_date, payoff_strategy (avalanche|snowball|manual),
linked_account_id, notes, is_active

**Investment**
id, user_id, name, type, symbol, quantity,
buy_price, current_price, buy_date, platform, notes, is_active

---

## API Reference

All endpoints are prefixed with `/api`. Protected routes require an `Authorization: Bearer <token>` header.

### Auth — `/api/auth`

| Method | Endpoint | Auth | Description |
|---|---|---|---|
| POST | `/register` | ❌ | Create new account |
| POST | `/login` | ❌ | Login, returns access + refresh tokens |
| POST | `/refresh-token` | ❌ | Get a new access token |
| POST | `/logout` | ✅ | Invalidate refresh token |
| GET | `/me` | ✅ | Get authenticated user profile |

### Users — `/api/users`

| Method | Endpoint | Auth | Description |
|---|---|---|---|
| GET | `/` | ✅ | Get user profile |
| PUT | `/` | ✅ | Update profile |

### Accounts — `/api/accounts`

| Method | Endpoint | Auth | Description |
|---|---|---|---|
| GET | `/` | ✅ | List all accounts |
| POST | `/` | ✅ | Create account |
| GET | `/:id` | ✅ | Get single account |
| PUT | `/:id` | ✅ | Update account |
| DELETE | `/:id` | ✅ | Delete account |

### Transactions — `/api/transactions`

| Method | Endpoint | Auth | Description |
|---|---|---|---|
| GET | `/` | ✅ | List transactions (filterable) |
| POST | `/` | ✅ | Create transaction |
| GET | `/:id` | ✅ | Get single transaction |
| PUT | `/:id` | ✅ | Update transaction |
| DELETE | `/:id` | ✅ | Delete transaction |

### Categories — `/api/categories`

| Method | Endpoint | Auth | Description |
|---|---|---|---|
| GET | `/` | ✅ | List all categories |
| POST | `/` | ✅ | Create category |
| PUT | `/:id` | ✅ | Update category |
| DELETE | `/:id` | ✅ | Delete category |

### Income — `/api/income`

| Method | Endpoint | Auth | Description |
|---|---|---|---|
| GET | `/` | ✅ | List income sources |
| POST | `/` | ✅ | Create income source |
| PUT | `/:id` | ✅ | Update income source |
| DELETE | `/:id` | ✅ | Delete income source |

### Budgets — `/api/budgets`

| Method | Endpoint | Auth | Description |
|---|---|---|---|
| GET | `/` | ✅ | List budgets with spending progress |
| POST | `/` | ✅ | Create budget |
| POST | `/suggest` | ✅ | Auto-suggest budgets from history |
| GET | `/:id` | ✅ | Get single budget |
| PUT | `/:id` | ✅ | Update budget |
| DELETE | `/:id` | ✅ | Delete budget |

### Goals — `/api/goals`

| Method | Endpoint | Auth | Description |
|---|---|---|---|
| GET | `/` | ✅ | List all goals |
| POST | `/` | ✅ | Create goal |
| GET | `/:id` | ✅ | Get single goal |
| PUT | `/:id` | ✅ | Update goal |
| DELETE | `/:id` | ✅ | Delete goal |
| POST | `/:id/contribute` | ✅ | Add contribution to goal |
| GET | `/:id/history` | ✅ | Contribution history |

### Debts — `/api/debts`

| Method | Endpoint | Auth | Description |
|---|---|---|---|
| GET | `/` | ✅ | List all debts |
| POST | `/` | ✅ | Create debt |
| GET | `/payoff-plan` | ✅ | Generate payoff plan (avalanche/snowball) |
| GET | `/:id` | ✅ | Get single debt |
| PUT | `/:id` | ✅ | Update debt |
| DELETE | `/:id` | ✅ | Delete debt |
| POST | `/:id/payment` | ✅ | Log a payment |

### Investments — `/api/investments`

| Method | Endpoint | Auth | Description |
|---|---|---|---|
| GET | `/` | ✅ | List all investments |
| POST | `/` | ✅ | Create investment |
| GET | `/portfolio` | ✅ | Portfolio summary with gain/loss |
| GET | `/:id` | ✅ | Get single investment |
| PUT | `/:id` | ✅ | Update investment |
| DELETE | `/:id` | ✅ | Delete investment |

### Recurring — `/api/recurring`

| Method | Endpoint | Auth | Description |
|---|---|---|---|
| GET | `/` | ✅ | List recurring transactions |
| POST | `/` | ✅ | Create recurring rule |
| GET | `/upcoming` | ✅ | List upcoming due items |
| GET | `/:id` | ✅ | Get single rule |
| PUT | `/:id` | ✅ | Update rule |
| DELETE | `/:id` | ✅ | Delete rule |
| POST | `/:id/process` | ✅ | Manually trigger processing |

### Reports — `/api/reports`

| Method | Endpoint | Auth | Description |
|---|---|---|---|
| GET | `/monthly-summary` | ✅ | Income vs expenses for a month |
| GET | `/cash-flow` | ✅ | Time-series cash flow |
| GET | `/net-worth-history` | ✅ | Net worth over time |
| GET | `/category-trends` | ✅ | Category spending trends |
| GET | `/savings-rate` | ✅ | Historical savings rate |
| GET | `/top-merchants` | ✅ | Top spend merchants |

### Insights — `/api/insights`

| Method | Endpoint | Auth | Description |
|---|---|---|---|
| GET | `/` | ✅ | Personalized financial insights |

### Notifications — `/api/notifications`

| Method | Endpoint | Auth | Description |
|---|---|---|---|
| GET | `/` | ✅ | List notifications |
| PUT | `/read-all` | ✅ | Mark all as read |
| PUT | `/:id/read` | ✅ | Mark one as read |
| DELETE | `/:id` | ✅ | Delete notification |

### Calculators — `/api/calculators`

> No authentication required.

| Method | Endpoint | Description |
|---|---|---|
| POST | `/emi` | Calculate EMI |
| POST | `/sip` | Calculate SIP future value |
| POST | `/compound` | Calculate compound interest |
| POST | `/retirement` | Retirement corpus planner |
| POST | `/emergency-fund` | Emergency fund estimator |

### Health Check
GET /api/health  →  { "status": "ok", "message": "FinancePro API is running 🚀" }

---

## Getting Started

### Prerequisites

- **Node.js** v18 or later
- **npm** v9 or later
- **MySQL** (XAMPP recommended for local dev, or any MySQL 8+ server)
- **Git**

### Backend Setup

```bash
# 1. Navigate to the backend directory
cd "financepro-backend-fixed/server"

# 2. Install dependencies
npm install

# 3. Configure environment variables
cp .env .env.local   # then edit .env with your values (see below)

# 4. Create the MySQL database
#    In XAMPP phpMyAdmin (or MySQL CLI):
CREATE DATABASE financepro;

# 5. Start the server (it auto-syncs tables on first run)
npm run dev
```

On startup you will see:
✅ MySQL connected successfully
✅ Database synced
🚀 FinancePro API running at http://localhost:5000

### Frontend Setup

```bash
# 1. Navigate to the frontend directory
cd "financepro-frontend-fixed/client"

# 2. Install dependencies
npm install

# 3. Configure environment (default works for local dev)
# VITE_API_URL=http://localhost:5000/api  ← already set in .env

# 4. Start the dev server
npm run dev
```

The frontend will be available at **http://localhost:5173**

---

## Environment Variables

### Backend (`server/.env`)

```env
# Server
PORT=5000
CLIENT_URL=http://localhost:5173

# MySQL
DB_HOST=localhost
DB_PORT=3306
DB_NAME=financepro
DB_USER=root
DB_PASS=                          # Leave empty for XAMPP default

# JWT
JWT_SECRET=your_secret_here       # Change in production!
JWT_EXPIRES_IN=15m
JWT_REFRESH_SECRET=your_refresh_secret_here
JWT_REFRESH_EXPIRES_IN=7d

# Email — optional, for bill reminder emails
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=your@gmail.com
SMTP_PASS=your_app_password       # Gmail App Password, not your account password

NODE_ENV=development
```

### Frontend (`client/.env`)

```env
VITE_API_URL=http://localhost:5000/api
```

---

## Running the App

### Development

```bash
# Backend (with hot reload via nodemon)
cd financepro-backend-fixed/server
npm run dev

# Frontend (in a separate terminal)
cd financepro-frontend-fixed/client
npm run dev
```

### Production Build (Frontend)

```bash
cd financepro-frontend-fixed/client
npm run build
# Output in dist/ — serve with any static host or nginx
```

### Production Start (Backend)

```bash
cd financepro-backend-fixed/server
NODE_ENV=production npm start
```

> ⚠️ In production, change `sequelize.sync({ alter: true })` to use proper Sequelize migrations to avoid unintended schema changes.

---

## Seeding Demo Data

Populate the database with realistic sample users, accounts, transactions, budgets, goals, debts, and investments:

```bash
cd financepro-backend-fixed/server
npm run seed
```

After seeding, you can log in with the demo credentials printed by the seeder.

---

## Scheduled Jobs (Cron)

The backend runs three automated background jobs via `node-cron`:

| Schedule | Job | Description |
|---|---|---|
| `0 0 * * *` — Daily at midnight | Recurring transaction processor | Creates transaction records for all due recurring items, updates account balances, advances the next due date |
| `0 9 * * *` — Daily at 9 AM | Bill due reminders | Creates `bill_due` notifications for any recurring expenses due in exactly 3 days |
| Monthly / configurable | Net worth snapshot | Takes a snapshot of total assets, total debts, and net worth for trend tracking |

The cron scheduler starts automatically when the server starts (imported in `server.js`).

---

## Financial Calculators

All calculators are implemented in `utils/financeCalculations.js` and exposed via `/api/calculators`.

### EMI Calculator
**Formula:** `EMI = P × r × (1+r)^n / ((1+r)^n - 1)`
- `P` = principal, `r` = monthly interest rate, `n` = tenure in months

### SIP Calculator
**Formula:** `FV = M × ((1+r)^n - 1) / r × (1+r)`
- `M` = monthly investment, `r` = monthly return rate, `n` = months

### Compound Interest
**Formula:** `A = P × (1 + r/f)^(f×t)`
- `P` = principal, `r` = annual rate, `f` = compounding frequency per year, `t` = years

### Debt Payoff Plans
- **Avalanche:** sorts debts by interest rate descending — minimizes total interest paid
- **Snowball:** sorts debts by current balance ascending — builds momentum with quick wins
- Both strategies simulate month-by-month payoff up to 360 months and return a full timeline

### Retirement Planner & Emergency Fund
Available via the API and the Calculators page in the UI.

---

## Authentication Flow
User submits login → POST /api/auth/login
↓
Server returns: { accessToken (15min), refreshToken (7d), user }
↓
Frontend stores both tokens in localStorage
↓
Every request attaches: Authorization: Bearer <accessToken>
↓
When accessToken expires → Axios interceptor calls POST /api/auth/refresh-token
↓
Server issues new accessToken → request retried automatically
↓
On logout → POST /api/auth/logout → refresh token cleared in DB → localStorage cleared

---

## Security

| Measure | Implementation |
|---|---|
| Password hashing | bcryptjs (salt rounds 10+) |
| JWT tokens | Short-lived access (15 min) + refresh (7 days) |
| HTTP headers | Helmet.js — sets CSP, HSTS, X-Frame-Options, etc. |
| Rate limiting | 20 requests per 15 minutes on `/api/auth/*` |
| CORS | Restricted to `CLIENT_URL` with credentials |
| Input validation | express-validator + Joi on all write endpoints |
| Request size limit | 10 MB cap on JSON body |
| SQL injection | Prevented by Sequelize parameterized queries (ORM) |

---

## Pages & Navigation

| Route | Page | Description |
|---|---|---|
| `/login` | LoginPage | Email/password login form |
| `/register` | RegisterPage | New user registration |
| `/dashboard` | DashboardPage | Overview — balances, recent activity, charts |
| `/transactions` | TransactionsPage | Full transaction list with filters and add/edit |
| `/accounts` | AccountsPage | Account cards, balances, add/edit accounts |
| `/categories` | CategoriesPage | Manage income and expense categories |
| `/budgets` | BudgetsPage | Budget progress bars and management |
| `/goals` | GoalsPage | Savings goals with progress and contributions |
| `/debts` | DebtsPage | Debt tracker with payoff strategy visualization |
| `/investments` | InvestmentsPage | Portfolio overview and individual positions |
| `/reports` | ReportsPage | Charts for cash flow, net worth, category trends |
| `/calculators` | CalculatorsPage | EMI, SIP, compound interest, retirement tools |
| `/notifications` | NotificationsPage | Alerts and system notifications |

All routes under `/` are protected by a `PrivateRoute` guard. Unauthenticated users are redirected to `/login`. Authenticated users visiting `/login` or `/register` are redirected to `/dashboard`.

---

## Default User Preferences

| Preference | Default |
|---|---|
| Currency | `BDT` (Bangladeshi Taka) |
| Locale | `en-BD` |
| Timezone | `Asia/Dhaka` |
| Theme | `system` (respects OS dark/light mode) |
| Income Type | `salaried` |
| Budget Alert | 80% of budget spent |

All preferences are configurable per user via the profile/settings API.

---

## Available Scripts

### Backend (`server/`)

| Command | Description |
|---|---|
| `npm run dev` | Start with nodemon (hot reload) |
| `npm start` | Start in production mode |
| `npm run seed` | Seed demo data into the database |

### Frontend (`client/`)

| Command | Description |
|---|---|
| `npm run dev` | Start Vite dev server |
| `npm run build` | Build for production |
| `npm run preview` | Preview production build locally |
| `npm run lint` | Run ESLint |