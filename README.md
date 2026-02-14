# PGoldapp Crypto Trading API

A robust RESTful API for managing a **Naira wallet** and trading cryptocurrencies (BTC, ETH, USDT).  
Built with Laravel 11, PostgreSQL, and Redis, this project demonstrates secure financial transactions, external API integration, and automated testing.

---

## 📚 Table of Contents

- Quick Start
- Test Credentials
- Technical Architecture
- Financial Integrity
- Fee Structure
- External API Integration
- Trade-offs & Assumptions
- API Endpoints
- API Testing (Postman)
- Tech Stack

---

## 🚀 Quick Start (Setup Instructions)

This project is fully containerized using **Laravel Sail**.

### Requirements

- Docker Desktop installed
- Git installed
- WSL2 (for Windows users)

---

### 1. Clone Repository

```bash
git clone https://github.com/Htech00/PGold_assessment.git
cd pgoldapp-api

```

### 2. Install Dependencies (Cold Start)
if you don't have PHP installed locally, use this Docker command to install composer dependencies

```bash
docker run --rm \
    -u "$(id -u):$(id -g)" \
    -v "$(pwd):/var/www/html" \
    -w /var/www/html \
    laravelsail/php83-composer:latest \
    composer install --ignore-platform-reqs
```

### 3. Environment Setup
Add your CoinGecko API key to the environment variables.

```bash
cp .env.example .env
./vendor/bin/sail up -d
./vendor/bin/sail artisan key:generate
```

### 4. Database Migrations & Seed Data
Initialize the database and create the seeder test user:

```bash
./vendor/bin/sail artisan migrate:fresh --seed
```

### 5. Run Automated Tests

```bash
./vendor/bin/sail artisan test
```


### Test Credentials
A default test user is created during seeding:

- Email: test@pgold.com

- Password: password123

- Starting Balance: 1,000,000 NGN

- Initial Assets: 0.5 BTC


### Technical Architecture

Service-Pattern Design

Business logic is abstracted into service classes to improve maintainability and testability.

 CoinGeckoService

- Handles external API communication
- Implements rate limiting

 TradeService

- Manages buy/sell operations
- Coordinates wallet and transaction updates

 Financial Integrity & Precision

 Data Types

- decimal(15,2) Naira values
- decimal(16,8) crypto precision (satoshi/Gwei)

Prevents floating-point rounding errors common in financial applications.


 Atomic Transactions

- All trades use DB::transaction()
- Eloquent increment/decrement prevents race conditions
- Ensures consistency during concurrent operations



### Fee Structure & Business Logic

- Platform Fee: 1.5% per trade.
- Minimum Trade Amount: 1,000 NGN
- Purpos: Covers operational and external API costs


### External API Integration

Provider: CoinGecko API

- Rate Limit Handling: Prices cached for 60 seconds using Laravel Cache
- Failure Handling: Trades blocked if price verification fails
- Logging: API failures recorded for monitoring 

This protects users from price volatility and prevents API throttling


### Trade-offs & Assumptions

Synchronous Ledger: 

 - Used for MVP simplicity
 - Production systems would use background queues(Redis)

Caching Strategy

 - 60 second cache balances accuracy and stability

Authentication

 - Laravel Sanctum for secure token-based authentication


### API Endpoints

Method	Endpoint	         Description	                 Auth Required

POST	/api/register	     Create user & wallet	             No

POST	/api/login	         Get Bearer Token	                 No

GET	/api/wallet	             View NGN & Crypto balance	         Yes

POST	/api/trade/buy	     Buy Crypto with Naira	             Yes

POST	/api/trade/sell	     Sell Crypto for Naira	             Yes

GET	/api/transactions	     View history (Paginated)	         Yes


### API Testing (Postman)

A postman collection is included for easy testing

Steps

    1. Import  `/docs/pgold_api_collection.json` into Postman.
    2. Base URL `http://localhost`.
    3. Authenticate:
        - Run the `Login` request.
        - Copy the `access_token` from the response.
        - Paste token into the collection Autorization tab

### Tech Stack

 - Laravel 11 (Framework)

 - PostgreSQL (Database)

 - Redis (Caching)

Laravel Sail (Docker Environment)

Pest/PHPUnit (Testing)

### Project Purpose
this project demonstrates:

 - Secure financial transaction handling
 - External API integration
 - Precision handling for monetary systems
 - Production ready architecture patterns
 - Automated testing practices


# PGold_assessment
