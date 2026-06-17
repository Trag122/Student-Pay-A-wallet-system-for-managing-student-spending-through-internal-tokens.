# StudentPay - He thong thanh toan thong minh cho hoc sinh

## Mo ta

StudentPay la he thong thanh toan dua tren mang luoi Stellar Blockchain (Testnet), cho phep hoc sinh chi tieu duoi su giam sat cua phu huynh. He thong su dung co che 3 luong kiem soat (Xanh/Do/Vang) va ho tro da chu ky (Multi-sig) ket hop gioi han thoi gian (Timebound 5 phut).

## Kien truc he thong

He thong gom 3 thanh phan chinh:

### Backend

- **Compliance Server (cong 3001)** - file `app.js`: Xu ly logic thanh toan voi 3 luong:
  - Luong Xanh (Whitelist): Tu dong duyet, tien di ngay
  - Luong Do (Blacklist): Tu dong chan, tu choi giao dich
  - Luong Vang (Ngoai luong): Treo lenh cho phu huynh duyet trong 5 phut

- **Merchant Callbacks Server (cong 3030)** - file `merchant-callbacks-3030.js`: Quan ly so du va lich su giao dich cua cac cua hang. Cung cap API cho MerchantView polling du lieu moi 3 giay.

### Frontend (React - cong 3000)

Ung dung React don trang (SPA) voi man hinh Mock Login phan quyen 3 vai tro:

- **StudentView**: Giao dien hoc sinh chon noi thanh toan va so tien. Hien thi ket qua theo 3 mau (xanh/do/vang).
- **ParentView**: Giao dien phu huynh gom 2 phan - cai dat (whitelist/blacklist/han muc) va duyet lenh dang cho.
- **MerchantView**: Dashboard cua hang hien thi so du real-time, lich su giao dich, hieu ung Ting Ting khi co tien vao, va nut Rut tien (Burn Token).

## Nhung gi da lam (phan bo sung)

Du an ban dau da co StudentView va ParentView (khoang 70%). Phan bo sung gom:

1. **Tao MerchantDashboard.js** - Component React cho giao dien cua hang:
   - REST Polling moi 3 giay goi API lay so du
   - So sanh balance cu va moi, neu tang thi no hieu ung Ting Ting
   - Bang lich su giao dich (thoi gian, loai, so tien, mo ta)
   - Nut Rut tien cuoi ngay goi API Burn Token

2. **Nang cap merchant-callbacks-3030.js** - Bo sung backend cho merchant:
   - Them 3 merchant mau (canteen, pizza, game)
   - Them API GET /api/merchant/transactions/:id (lich su giao dich)
   - Them API GET /api/merchant/list (danh sach merchant)
   - Cap nhat callback ghi log giao dich khi nhan tien

3. **Cap nhat App.js** - Them man hinh Mock Login:
   - 3 nut chon vai tro: Hoc Sinh / Phu Huynh / Cua Hang
   - Thanh navigation tren cung voi nut Doi vai tro
   - Routing noi bo render view theo vai tro duoc chon

4. **Viet lai App.css** - Thiet ke giao dien moi:
   - Dark mode theme voi hieu ung glassmorphism
   - Font Inter tu Google Fonts
   - Animation cho Ting Ting, pulse balance, fade in
   - Responsive cho man hinh nho

## Huong dan chay

### Yeu cau

- Node.js (phien ban 18 tro len)
- npm

### Cai dat

```bash
# Cai dependencies cho backend (thu muc goc)
cd StudentPay
npm install

# Cai dependencies cho frontend (thu muc bank-portal)
cd bank-portal
npm install
```

### Chay du an

Mo 3 terminal rieng biet:

```bash
# Terminal 1: Chay Compliance Server (cong 3001)
cd StudentPay
node app.js

# Terminal 2: Chay Merchant Callbacks Server (cong 3030)
cd StudentPay
node merchant-callbacks-3030.js

# Terminal 3: Chay React Frontend (cong 3000)
cd StudentPay/bank-portal
npm start
```

Truy cap http://localhost:3000 de su dung ung dung.

### Test nhanh luong Ting Ting

Mo terminal moi va gui lenh gia lap tien vao:

```bash
# Windows PowerShell
Invoke-RestMethod -Uri "http://localhost:3030/internal/stellar-callback" -Method POST -ContentType "application/json" -Body '{"targetWallet":"canteen*studentpay.com","amount":50,"fromStudent":"hs01"}'

# Linux/Mac
curl -X POST http://localhost:3030/internal/stellar-callback \
  -H "Content-Type: application/json" \
  -d '{"targetWallet":"canteen*studentpay.com","amount":50,"fromStudent":"hs01"}'
```

Sau do vao Merchant Dashboard se thay so du tang va hieu ung Ting Ting xuat hien.

## Cau truc thu muc

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

## Cong nghe su dung

- React 19
- Node.js + Express
- Stellar SDK (Testnet)
- REST API + Polling

#Copyright@Team10BlockchainandFinancialProjectsfromVNUIS2026(Trang,Phuc,Tuong,Ngan)