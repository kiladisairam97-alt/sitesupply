# SiteSupply — Supabase full-stack starter

This upgrades the existing SiteSupply frontend into a customer-facing MVP with Supabase authentication, a live product catalogue, database-backed order creation, and customer order history. It does **not** contact suppliers, collect payments, or provide a driver app yet.

## Included
- Existing SiteSupply design, bilingual Telugu/English UI, and 73 sample construction materials.
- Email/password sign-up and sign-in via Supabase Auth.
- Product list loaded from Supabase, including active status and optional stock quantity.
- Atomic `place_order` PostgreSQL RPC that reads prices from the database (not browser-submitted prices), validates the request, and saves order + line items.
- Row Level Security: public visitors can read active products; authenticated customers can only read their own orders and order items.
- My Orders view; order records persist across devices after sign-in.

## Important before launch
- Catalogue prices are placeholders copied from the prototype, **not verified Khammam market prices**. Update them in Supabase before taking real customer orders.
- Delivery fee is currently a simple ₹80 estimate in the SQL function. Confirm a sensible fee/rule before launch.
- Orders are requests with status `placed`; there is no admin/supplier status dashboard yet.
- Online payments, SMS/WhatsApp, email notifications, driver assignment and live GPS are not implemented.
- Use a Supabase project and never put the `service_role` secret in browser code. Only the project URL and publishable/anon key belong in `supabase-config.js`; RLS is essential.

## Step 1 — Create Supabase project
1. Open https://supabase.com/ and create a project.
2. In project settings, copy the **Project URL** and **publishable key** (or legacy anon key).
3. In Supabase, open **SQL Editor → New query**.
4. Paste the full contents of `supabase/schema.sql` and click **Run**. This creates the tables, security policies, order RPC, and sample products.
5. In **Authentication → URL Configuration**, add your deployed site URL to Site URL and Redirect URLs (e.g. `https://YOUR-USERNAME.github.io/YOUR-REPO/`). For local testing, also add `http://localhost:5500/**`.

## Step 2 — Connect the website
1. Open `supabase-config.js`.
2. Replace `https://YOUR_PROJECT_ID.supabase.co` with your Project URL.
3. Replace `YOUR_SUPABASE_PUBLISHABLE_OR_ANON_KEY` with the publishable/anon key.
4. Save and commit. These two browser settings are public by design; never paste a `service_role` key.

## Step 3 — Push to your existing GitHub repository
1. Unzip this package.
2. Upload/replace `index.html`, `styles.css`, `app.js`, and `supabase-config.js` in the root of the same repository where your current SiteSupply `index.html` lives.
3. Upload the `supabase` folder, including `schema.sql` (GitHub Pages does not execute SQL; run it in Supabase SQL Editor).
4. Commit changes. In GitHub repository **Settings → Pages**, select your publishing branch/folder if Pages is not already enabled.
5. Wait for deployment and open the published URL. Keep the project root paths as supplied; GitHub Pages serves these files as static assets while Supabase handles backend requests.

## Step 4 — Test end-to-end
1. Open the deployed URL over HTTPS.
2. Create an account with your email and a password of at least 8 characters. If email confirmation is enabled, confirm the email before signing in.
3. Sign in, add 1–2 products to cart, fill a real test site address and place an order request.
4. Check **My orders** and Supabase **Table Editor → orders / order_items**. Confirm only your account can see its orders.
5. Sign out and try viewing orders again; it should require sign-in.
6. Test on mobile and verify Telugu toggle, validation, refresh, and an unavailable product.

## Troubleshooting
- **“Setup required”**: check both values in `supabase-config.js`; URL should start `https://` and key must be the publishable/anon key.
- **No products / query error**: run `supabase/schema.sql` completely; check the `products` table and RLS policies.
- **Sign-up works but sign-in fails**: check email confirmation in Supabase Auth and confirm the deployed URL is in Redirect URLs.
- **Order fails**: confirm you're signed in and `place_order` function exists; inspect browser console and Supabase logs.
- **GitHub Pages blank/404**: confirm `index.html`, `app.js`, `styles.css`, and `supabase-config.js` are in the same published folder; ensure the HTML script tag uses `type="module"`.

## Project structure
```
index.html
styles.css
app.js
supabase-config.js
supabase/schema.sql
README.md
.nojekyll
```
