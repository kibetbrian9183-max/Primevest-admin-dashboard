# PrimeVest Admin (standalone)

Plain HTML/CSS/JS admin dashboard for PrimeVest — no build step, no
framework. Deploys to Vercel as a static site, separate from the
backend on Render.

## 1. Point it at your backend

Open `config.js` and confirm the URL matches your real backend:

```js
const PRIMEVEST_API_BASE = "https://primevestbackend.onrender.com";
```

That's the only setting in this whole project.

## 2. Push to GitHub

```bash
cd primevest-admin
git init
git add .
git commit -m "Initial commit"
git branch -M main
git remote add origin https://github.com/YOUR-USERNAME/primevest-admin.git
git push -u origin main
```

## 3. Deploy on Vercel

1. vercel.com → **Add New… → Project**
2. Select the `primevest-admin` repo → **Import**
3. Framework Preset: Vercel should detect **Other** (plain static) —
   leave build command and output directory blank, this needs neither
4. **Deploy** — no environment variables needed, `config.js` handles it

You'll get a URL like `https://primevest-admin.vercel.app`.

## 4. Tell the backend to trust this new origin

This is not optional — without it, login will fail with a CORS error
because the browser will block the request.

1. Render dashboard → your backend service → **Environment**
2. Find `CORS_ORIGIN`
3. Add your new Vercel URL to the list (comma-separated if there's
   already a value there), e.g.:
   ```
   https://primevest-frontend.vercel.app,https://primevest-admin.vercel.app
   ```
4. Save — Render redeploys automatically

## 5. Log in

Visit your new Vercel URL and log in with the same `ADMIN_EMAIL` /
password as before — nothing changed about the admin account itself,
only where the page is hosted.

## Why this needed backend changes too

Since this panel now lives on a different domain than the API
(`vercel.app` vs `onrender.com`), the login session cookie has to be
explicitly marked for cross-site use (`SameSite=None; Secure`) or
browsers silently refuse to send it back on the next request. That's
already handled in the backend's `src/middleware/adminAuth.js` — make
sure that file is deployed on the backend side too, or login will
appear to succeed but the dashboard will immediately bounce back to
the login screen.
