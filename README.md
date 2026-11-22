# Jiya Zain — Think Like a Founder eBook Store

A responsive, single-author digital eBook store for **Think Like a Founder** by Jiya Zain.
The only product format in this build is the uploaded **PDF eBook** (178 pages).

## Confirmed product and prices

- International price: **US$7.00**
- Pakistan price: **PKR 1,999**
- Product: PDF eBook only. A separate EPUB file was not supplied, so EPUB is not advertised.
- Uploaded book file is stored in `private/books/` and is not served as a public static asset.

## Project structure

```text
jiya-zain-store/
├── public/
│   ├── index.html           # Store frontend
│   ├── admin.html           # Private admin interface
│   ├── css/styles.css       # Responsive styles
│   ├── js/app.js            # Store interactions and API connection
│   ├── js/admin.js          # Admin login and dashboard interactions
│   └── assets/book_cover.jpg
├── private/books/           # Original PDF; do not move into public/
├── database/schema.sql      # Database schema
├── database/seed.sql        # Initial product data
├── data/store.sqlite        # SQLite database (created and seeded)
├── backups/initial-store.sqlite
├── scripts/                 # Admin creation and DB backup/restore
├── server.js                # Express API and SQLite connection
└── .env.example
```

## Run locally (Windows PowerShell)

1. Install Node.js 20 or newer.
2. Extract the ZIP into a folder, then open PowerShell in that folder.
3. Install dependencies:

   ```powershell
   npm install
   ```

4. Create your local environment file:

   ```powershell
   Copy-Item .env.example .env
   ```

5. Edit `.env` and set `JWT_SECRET` to a unique random string of at least 32 characters. Keep `.env` private.
6. Start the server:

   ```powershell
   npm start
   ```

7. Open `http://localhost:3000`.
8. Create your private admin login in a second PowerShell window:

   ```powershell
   npm run create-admin -- admin@example.com "Use-A-Unique-Password-Here"
   ```

   Then open `http://localhost:3000/admin`.

The database is `data/store.sqlite`. The included initial database has the book record and prices already inserted. The server also creates tables and seeds the first book on first startup if a new database is used.

## Frontend / backend connection

- The frontend fetches the book and prices from `GET /api/book`.
- The order form submits to `POST /api/orders`.
- Admin endpoints are protected with an HTTP-only signed session cookie.
- Admin can update the book text and prices, list order requests, change order states and download the PDF for orders marked `paid` or `fulfilled`.
- The private PDF is never placed under `public/` and is not exposed through a public URL.

## Important payment and fulfilment limitation

The API and SQLite database are connected. Order requests are recorded, but **no payment gateway is connected** in this package. The form does not charge a card, JazzCash, Easypaisa or bank account. It explicitly says the request is not a payment. Verify payment separately, then mark the order paid in Admin. Admin can then download the PDF and send it to the customer manually. Automatic customer email delivery and secure self-service download links require an email provider and a payment provider/merchant account.

Do not publish this store as a live payment checkout until a payment provider is configured and server-side payment confirmation/webhook handling has been implemented and tested.

## Database backup and restore

Create a current backup:

```powershell
npm run backup
```

Restore from a backup file inside the project folder (the restore script first creates a safety backup of the current database):

```powershell
npm run restore -- backups/initial-store.sqlite
```

You can also use the included `backups/initial-store.sqlite` as the clean starting backup. Keep additional backups somewhere separate from your computer as well.

## Production checklist

- Use HTTPS and set `NODE_ENV=production` and `COOKIE_SECURE=true`.
- Set a strong private `JWT_SECRET` (32+ characters; a password manager is recommended).
- Use a managed server and persistent disk/database backups; do not rely on an ephemeral hosting filesystem for SQLite.
- Configure and test a payment gateway for Pakistan and international customers before taking payments.
- Configure transactional email for order confirmation and digital delivery.
- Confirm tax, consumer, privacy and digital-product terms applicable to the markets you sell into.
- Test database restore before launch.

## Checks performed for this delivered package

- Checked SQL schema and seed using SQLite integrity checks.
- Checked JavaScript syntax with Node's parser.
- Confirmed the uploaded manuscript is included in private storage and not in the public asset folder.
- Dependency installation could not be completed in this build environment, so the Express server has not been live-run here. Run `npm install` on your computer before starting it.


## Easypaisa and JazzCash payment requests
For Pakistan orders, customers can now select Easypaisa or JazzCash and optionally enter a transaction reference if they have already paid. The order is saved as `payment_review` when a reference is supplied, otherwise `pending_payment`. The admin dashboard displays the selected method and reference. The store owner must independently verify the payment before changing the order to `paid`.

**Important:** This is manual payment-request support, not a direct Easypaisa/JazzCash API integration. No merchant account number is displayed because none was provided. To enable automatic checkout/payment confirmation, obtain an approved merchant account and API credentials from each provider, then implement their current merchant API, signed callbacks/webhooks, and server-side verification. Never put API secrets in frontend JavaScript. Do not send the private PDF until payment is verified.
