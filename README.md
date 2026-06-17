# Student Pay: A Blockchain application building a wallet system for managing student spending through internal tokens.

## Describe

StudentPay is a payment system based on the Stellar Blockchain (Testnet), allowing students to spend under parental supervision. The system uses a 3-tier control mechanism (Green/Red/Yellow) and supports multi-signature payments combined with a time-bound (5-minute) limit.

## System Architecture

The system consists of three main components:

### Backend

- **Compliance Server (3001)** - file `app.js`: Processing payment logic with 3 threads
  - Green (Whitelist): Auto-approved, instant transfer.
  - Red (Blacklist): Auto-blocked, transaction denied.
  - Yellow (Unlisted): Pending 5 minutes for parent approval.

- **Merchant Callbacks Server (cong 3030)** - file `merchant-callbacks-3030.js`: Manage store balances and transaction history. Provide an API for MerchantView to poll data every 3 seconds.

### Frontend (React - 3000)

React Single Page Application (SPA) featuring a Mock Login interface for Role-Based Access Control (3 distinct roles):

- **StudentView**: The interface allows students to select the payment method and amount. The results are displayed in three colors (green/red/yellow).
- **ParentView**: The parent interface consists of two parts - settings (whitelist/blacklist/limits) and pending order approval.
- **MerchantView**: The company's dashboard displays real-time balance, transaction history, a Ting Ting effect when she enters, and a Withdraw (Burn Token) button.

## The updating for Merchant

1. **Tao MerchantDashboard.js** - Component React for the frontend:
   - Automatically calls the API every 3 seconds to fetch real-time balances.
   - Compares the previous and current balance, triggering a "Ting Ting" notification effect upon increment.
   - Displays a detailed history table including timestamp, transaction type, amount, and description.
   - End-of-day "Withdraw" button that explicitly triggers the Burn Token API to simulate fiat payout.

2. **Nang cap merchant-callbacks-3030.js** - Backend for merchant:
   - Initialized 3 mock merchants in the database (canteen, pizza, game).
   - Implemented GET /api/merchant/transactions/:id API to fetch transaction history.
   - Implemented GET /api/merchant/list API to retrieve the merchant directory.
   - Upgraded webhook callbacks to systematically log transactions upon receiving funds.

3. **Cap nhat App.js** - Mock Login Frontend:
   - Mock Login Screen: Added selection buttons for 3 roles: Student / Parent / Merchant.
   - Navigation: Implemented a top navigation bar featuring a "Switch Role" action.
   - Dynamic Routing: Configured internal routing to dynamically render the corresponding view based on the selected role.
     
4. **Viet lai App.css** - Design Frontend:
   - Dark mode theme voi hieu ung glassmorphism
   - Integrated the Inter font family from Google Fonts for a clean, professional typography.
   - Engineered custom CSS animations, including "Ting Ting" popups, balance pulsing, and fade-in transitions.
   - Fully responsive layout optimized for mobile and small screens.

## How to run

### Condition

- Node.js (version 18)
- npm

### Setting

```bash
# Set dependencies for the backend (original file)
cd StudentPay
npm install

# Serrinf dependencies for the frontend (bank-portal file)
cd bank-portal
npm install
```

### Run

Open 3 terminal:

```bash
# Terminal 1: Run Compliance Server (3001)
cd StudentPay
node app.js

# Terminal 2: Run Merchant Callbacks Server (3030)
cd StudentPay
node merchant-callbacks-3030.js

# Terminal 3: Run React Frontend (3000)
cd StudentPay/bank-portal
npm start
```

Access http://localhost:3000.

### Test the "Ting Ting"

Open a new terminal and send the following command to the emulator:

```bash
# Windows PowerShell
Invoke-RestMethod -Uri "http://localhost:3030/internal/stellar-callback" -Method POST -ContentType "application/json" -Body '{"targetWallet":"canteen*studentpay.com","amount":50,"fromStudent":"hs01"}'

# Linux/Mac
curl -X POST http://localhost:3030/internal/stellar-callback \
  -H "Content-Type: application/json" \
  -d '{"targetWallet":"canteen*studentpay.com","amount":50,"fromStudent":"hs01"}'
```

## Directory structure

```
StudentPay/
  app.js                        # Compliance Server (cong 3001)
  merchant-callbacks-3030.js    # Merchant Callbacks Server (cong 3030)
  index.html                    # Trang thanh toan don gian (standalone)
  .env                          # Bien moi truong (STELLAR_SECRET_KEY)
  bank-portal/
    src/
      App.js                    # Component goc voi Mock Login
      App.css                   # Toan bo CSS
      ParentDashboard.js        # Cai dat phu huynh
      components/
        Dashboard.js            # StudentView - cong thanh toan
        RightSidebar.js         # ParentView - duyet lenh
        MerchantDashboard.js    # MerchantView - dashboard cua hang
        Sidebar.js              # Menu (chua su dung)
    controllers/
      paymentController.js      # Xu ly thanh toan
    routes/
      paymentRoutes.js          # Dinh tuyen API
    services/
      stellarService.js         # Ket noi Stellar Blockchain
```

## Technical tools

- React 19
- Node.js + Express
- Stellar SDK (Testnet)
- REST API + Polling

#Copyright@Team10BlockchainandFinancialProjectsfromVNUIS2026(Trang,Phuc,Tuong,Ngan)
