# Payment Gateway

A full-featured payment gateway implementation similar to Razorpay and Stripe, supporting multiple payment methods including UPI and Card payments. Built with modern web technologies and fully containerized for easy deployment.

## 🚀 Features

- **Multi-Method Payment Processing**: Support for UPI and Card payments
- **Merchant Dashboard**: Real-time analytics, transaction history, and API credential management
- **Hosted Checkout Page**: Professional, responsive payment interface
- **Secure Authentication**: API key-based merchant authentication
- **Payment Validation**: VPA validation, Luhn algorithm for cards, card network detection
- **Docker Deployment**: Fully containerized with Docker Compose
- **Database Persistence**: PostgreSQL with proper schema and relationships
- **Test Merchant**: Pre-seeded test credentials for immediate testing

## 🛠️ Tech Stack

### Backend
- **Runtime**: Node.js
- **Framework**: Express.js
- **Database**: PostgreSQL 15
- **ORM**: pg (node-postgres)
- **Authentication**: Custom API key/secret validation

### Frontend
- **Framework**: React 18
- **Build Tool**: Vite
- **Routing**: React Router v6
- **HTTP Client**: Axios
- **Styling**: Custom CSS

### DevOps
- **Containerization**: Docker & Docker Compose
- **Web Server**: Nginx (for serving React apps)

## 📋 Prerequisites

Before you begin, ensure you have the following installed:
- [Docker](https://docs.docker.com/get-docker/) (version 20.10 or higher)
- [Docker Compose](https://docs.docker.com/compose/install/) (version 2.0 or higher)
- [Git](https://git-scm.com/downloads)

## 🚀 Quick Start

### 1. Clone the Repository

```bash
git clone <your-repository-url>
cd payment-gateway
2. Start All Services
```
``` bash
docker-compose up -d
This single command will:

Build all Docker images

Start PostgreSQL database

Initialize database schema

Seed test merchant data

Start backend API server

Start merchant dashboard frontend

Start checkout page frontend
```

### 3. Wait for Services to Initialize
Wait approximately 20-30 seconds for all services to fully start and the database to be initialized.

### 4. Verify Services are Running
bash
docker-compose ps
You should see all four containers in "Up" status:

pg_gateway (PostgreSQL)

gateway_api (Backend API)

gateway_dashboard (Dashboard Frontend)

gateway_checkout (Checkout Frontend)

### 5. Access the Application
Merchant Dashboard: http://localhost:3000

Checkout Page: http://localhost:3001

Backend API: http://localhost:8000

Health Check: http://localhost:8000/health

## 🔑 Test Credentials
Merchant Login
Email: test@example.com

Password: password123 (or any password)

API Credentials
API Key: key_test_abc123

API Secret: secret_test_xyz789

## Test Cards
Visa: 4111111111111111

Mastercard: 5555555555554444

Expiry: Any future date (e.g., 12/25)

CVV: Any 3 digits (e.g., 123)

Test UPI IDs
Any format: username@bankname (e.g., test@paytm, user@upi)

### 📖 Usage Guide
Testing the Complete Payment Flow
Login to Dashboard

Navigate to http://localhost:3000

Login with test credentials

View API credentials and current stats

Create Test Order

Click "Create test order" button on dashboard

You'll be redirected to checkout page automatically

Complete Payment

Choose payment method (UPI or Card)

Fill in payment details

Click pay button

Wait for processing (5-10 seconds)

View success/failure result

View Transaction History

Return to dashboard

Click "View transactions"

See all payment records with details

Using the API Directly
#### Create an Order

curl -X POST http://localhost:8000/api/v1/orders \
  -H "X-Api-Key: key_test_abc123" \
  -H "X-Api-Secret: secret_test_xyz789" \
  -H "Content-Type: application/json" \
  -d '{
    "amount": 50000,
    "currency": "INR",
    "receipt": "receipt_123",
    "notes": {
      "customer_name": "John Doe"
    }
  }'
Response:

json
{
  "id": "order_NXhj67fGH2jk9mPq",
  "merchant_id": "550e8400-e29b-41d4-a716-446655440000",
  "amount": 50000,
  "currency": "INR",
  "receipt": "receipt_123",
  "notes": {
    "customer_name": "John Doe"
  },
  "status": "created",
  "created_at": "2024-01-15T10:30:00Z"
}
#### Get Order Details

curl -X GET http://localhost:8000/api/v1/orders/order_NXhj67fGH2jk9mPq \
  -H "X-Api-Key: key_test_abc123" \
  -H "X-Api-Secret: secret_test_xyz789"
#### Create Payment (UPI)

curl -X POST http://localhost:8000/api/v1/payments/public \
  -H "Content-Type: application/json" \
  -d '{
    "order_id": "order_NXhj67fGH2jk9mPq",
    "method": "upi",
    "vpa": "user@paytm"
  }'
#### Create Payment (Card)

curl -X POST http://localhost:8000/api/v1/payments/public \
  -H "Content-Type: application/json" \
  -d '{
    "order_id": "order_NXhj67fGH2jk9mPq",
    "method": "card",
    "card": {
      "number": "4111111111111111",
      "expiry_month": "12",
      "expiry_year": "2025",
      "cvv": "123",
      "holder_name": "John Doe"
    }
  }'
### 🏗️ Project Structure
``` bash
payment-gateway/
├── docker-compose.yml          # Docker orchestration configuration
├── README.md                   # This file
├── .env.example               # Environment variables template
│
├── backend/                    # Backend API service
│   ├── Dockerfile
│   ├── package.json
│   ├── src/
│   │   ├── config/
│   │   │   └── database.js    # Database connection & initialization
│   │   ├── controllers/
│   │   │   ├── orderController.js      # Order management
│   │   │   ├── paymentController.js    # Payment processing
│   │   │   ├── dashboardController.js  # Stats & history
│   │   │   └── healthController.js     # Health checks
│   │   ├── routes/
│   │   │   └── index.js       # API route definitions
│   │   ├── services/
│   │   │   └── validation.js  # Payment validation logic
│   │   └── index.js           # Application entry point
│   └── .dockerignore
│
├── frontend/                   # Merchant Dashboard (React)
│   ├── Dockerfile
│   ├── nginx.conf             # Nginx configuration
│   ├── package.json
│   ├── vite.config.js
│   ├── src/
│   │   ├── pages/
│   │   │   ├── Login.jsx      # Login page
│   │   │   ├── Dashboard.jsx  # Dashboard home
│   │   │   └── Transactions.jsx # Transaction history
│   │   ├── styles/
│   │   │   ├── Login.css
│   │   │   ├── Dashboard.css
│   │   │   └── Transactions.css
│   │   ├── App.jsx            # Main app component
│   │   └── main.jsx           # React entry point
│   └── .dockerignore
│
└── checkout-page/              # Checkout Page (React)
    ├── Dockerfile
    ├── nginx.conf
    ├── package.json
    ├── vite.config.js
    ├── src/
    │   ├── pages/
    │   │   └── Checkout.jsx   # Payment checkout flow
    │   ├── styles/
    │   │   └── Checkout.css
    │   ├── App.jsx
    │   └── main.jsx
    └── .dockerignore
```

### 🗄️ Database Schema
Merchants Table
sql
id              UUID PRIMARY KEY
name            VARCHAR(255)
email           VARCHAR(255) UNIQUE
api_key         VARCHAR(64) UNIQUE
api_secret      VARCHAR(64)
webhook_url     TEXT
is_active       BOOLEAN DEFAULT true
created_at      TIMESTAMP
updated_at      TIMESTAMP
Orders Table
sql
id              VARCHAR(64) PRIMARY KEY  -- Format: order_XXXXXXXXXXXXXXXX
merchant_id     UUID REFERENCES merchants(id)
amount          INTEGER  -- Amount in paise (smallest currency unit)
currency        VARCHAR(3) DEFAULT 'INR'
receipt         VARCHAR(255)
notes           JSONB
status          VARCHAR(20) DEFAULT 'created'
created_at      TIMESTAMP
updated_at      TIMESTAMP
Payments Table
sql
id                  VARCHAR(64) PRIMARY KEY  -- Format: pay_XXXXXXXXXXXXXXXX
order_id            VARCHAR(64) REFERENCES orders(id)
merchant_id         UUID REFERENCES merchants(id)
amount              INTEGER
currency            VARCHAR(3) DEFAULT 'INR'
method              VARCHAR(20)  -- 'upi' or 'card'
status              VARCHAR(20)  -- 'processing', 'success', 'failed'
vpa                 VARCHAR(255)  -- For UPI payments
card_network        VARCHAR(20)   -- For card payments
card_last4          VARCHAR(4)    -- For card payments
error_code          VARCHAR(50)
error_description   TEXT
created_at          TIMESTAMP
updated_at          TIMESTAMP
### 🔒 Security Features
API Authentication: All protected endpoints require valid API key and secret

No Sensitive Data Storage: Card CVV and full card numbers are never stored

Payment Validation: VPA format, Luhn algorithm, card expiry validation

Test Mode: Configurable test mode for evaluation purposes

Environment Variables: Sensitive configuration via environment variables

### 🧪 Payment Processing Logic
Payment Success Rates
UPI: 90% success rate (simulated)

Card: 95% success rate (simulated)

#### Processing Flow
Payment created with status processing

5-10 second delay (simulates bank processing)

Random success/failure based on configured rates

Status updated to success or failed

Error details populated if failed

### Validation Rules
VPA Validation:

Format: username@provider

Example: user@paytm, john.doe@bank

Card Validation:

Luhn algorithm check

Network detection (Visa, Mastercard, Amex, RuPay)

Expiry date validation (must be future date)

CVV format check

### 🌐 API Endpoints
Base URL:
http://localhost:8000
Public Endpoints
GET /health - Health check

GET /api/v1/test/merchant - Test merchant verification

GET /api/v1/orders/{order_id}/public - Get order (public)

POST /api/v1/payments/public - Create payment (public)

GET /api/v1/payments/{payment_id}/public - Get payment (public)

Protected Endpoints (Require Authentication)
POST /api/v1/orders - Create order

GET /api/v1/orders/{order_id} - Get order

POST /api/v1/payments - Create payment

GET /api/v1/payments/{payment_id} - Get payment

GET /api/v1/payments/list - List all payments

GET /api/v1/payments/stats - Get payment statistics

### ⚙️ Configuration
Environment Variables
See .env.example for all available configuration options:

text
### Database
DATABASE_URL=postgresql://gateway_user:gateway_pass@postgres:5432/payment_gateway

### Server
PORT=8000

### Test Merchant Credentials
TEST_MERCHANT_EMAIL=test@example.com
TEST_API_KEY=key_test_abc123
TEST_API_SECRET=secret_test_xyz789

### Payment Simulation
UPI_SUCCESS_RATE=0.90
CARD_SUCCESS_RATE=0.95
PROCESSING_DELAY_MIN=5000
PROCESSING_DELAY_MAX=10000

### Test Mode (for automated evaluation)
TEST_MODE=false
TEST_PAYMENT_SUCCESS=true
TEST_PROCESSING_DELAY=1000
🛠️ Development
Running in Development Mode
For local development without Docker:

### Start PostgreSQL


docker-compose up -d postgres
Start Backend


cd backend
npm install
npm run dev
Start Dashboard Frontend


cd frontend
npm install
npm run dev
Start Checkout Frontend


cd checkout-page
npm install
npm run dev
Viewing Logs
bash
### All services
docker-compose logs -f

### Specific service
docker-compose logs -f api
docker-compose logs -f dashboard
docker-compose logs -f checkout
docker-compose logs -f postgres
Rebuilding After Changes
bash
### Rebuild all services
docker-compose down
docker-compose up -d --build

### Rebuild specific service
docker-compose up -d --build api
🧹 Maintenance
Stopping the Application

docker-compose down
Removing All Data (Including Database)

docker-compose down -v
Restarting Services

docker-compose restart
🐛 Troubleshooting
Services Not Starting
Check Docker logs:


docker-compose logs
Ensure ports are not in use:


### Check if ports are available
netstat -an | grep 3000
netstat -an | grep 3001
netstat -an | grep 8000
netstat -an | grep 5432
Database Connection Issues
bash
### Restart database and API
docker-compose restart postgres
sleep 10
docker-compose restart api
Frontend Not Loading
bash
### Clear browser cache and hard refresh (Ctrl+Shift+R)
### Or rebuild frontend
docker-compose up -d --build dashboard
docker-compose up -d --build checkout
Reset Everything
bash
docker-compose down -v
docker-compose up -d --build
### 📊 Testing
Manual Testing Checklist
 Health endpoint returns correct response

 Can login to merchant dashboard

 API credentials displayed correctly

 Stats show accurate data

 Can create order via API

 Can create order from dashboard button

 