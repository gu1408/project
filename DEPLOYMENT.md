# TravelCompare — Deployment Guide

## Stack Overview

| Layer    | Service         | Notes                                      |
|----------|-----------------|--------------------------------------------|
| Frontend | Vercel          | Auto-detects Next.js, zero-config deploy   |
| Backend  | Render          | Free tier available, auto-deploys from Git |
| Database | MongoDB Atlas   | Free M0 cluster (512 MB)                   |
| Cache    | Redis Cloud     | Free 30 MB tier via upstash.io             |

---

## Step 1 — MongoDB Atlas Setup

1. Go to https://cloud.mongodb.com
2. Create a free M0 cluster (AWS / GCP / Azure)
3. Create a database user: **Security → Database Access → Add User**
4. Whitelist all IPs: **Security → Network Access → 0.0.0.0/0**
5. Get your connection string: **Connect → Connect your application**
   ```
   mongodb+srv://<user>:<password>@cluster0.mongodb.net/travelcompare?retryWrites=true&w=majority
   ```

---

## Step 2 — Redis Cloud Setup (Optional but recommended)

1. Go to https://upstash.com → Create database
2. Choose Redis, free tier, pick region closest to your Render server
3. Copy the **REST URL** — convert it to a Redis URL:
   ```
   redis://:<password>@<host>:<port>
   ```

---

## Step 3 — Deploy Backend to Render

1. Push your code to GitHub
2. Go to https://render.com → **New → Web Service**
3. Connect your GitHub repo, select `travelcompare/backend`
4. Settings:
   - **Build Command**: `npm install`
   - **Start Command**: `node server.js`
   - **Node version**: 18
5. Add environment variables:
   ```
   PORT=5000
   NODE_ENV=production
   MONGODB_URI=<your Atlas URI>
   REDIS_URL=<your Redis URL>
   FRONTEND_URL=https://your-app.vercel.app
   CACHE_TTL=600
   RATE_LIMIT_MAX=100
   ENABLE_SCRAPING=false
   ```
6. Click **Create Web Service**
7. Note your Render URL: `https://travelcompare-api.onrender.com`

> **Note**: Free Render tier sleeps after 15 min inactivity. Use a ping service (UptimeRobot) to keep it warm.

---

## Step 4 — Deploy Frontend to Vercel

1. Go to https://vercel.com → **Add New Project**
2. Import your GitHub repo, select `travelcompare/frontend` as root directory
3. Framework preset: **Next.js** (auto-detected)
4. Add environment variable:
   ```
   NEXT_PUBLIC_API_URL=https://travelcompare-api.onrender.com
   ```
5. Click **Deploy**
6. Your app is live at `https://travelcompare.vercel.app`

---

## Step 5 — Update CORS on Backend

After deployment, update `FRONTEND_URL` in Render environment to your Vercel URL:
```
FRONTEND_URL=https://travelcompare.vercel.app
```

---

## Local Development

```bash
# Clone the repo
git clone <your-repo>
cd travelcompare

# Backend
cd backend
cp .env.example .env     # fill in your values
npm install
npm run dev              # runs on http://localhost:5000

# Frontend (new terminal)
cd ../frontend
cp .env.example .env.local
npm install
npm run dev              # runs on http://localhost:3000
```

---

## Enable Puppeteer Scraping (Production)

By default, `ENABLE_SCRAPING=false` — all results come from mock data.

To enable real scraping:
1. Set `ENABLE_SCRAPING=true` in backend env
2. Install Chrome on your server: on Render, use a Docker image with Chromium
3. In `scrapers/redbusScraper.js`, add `executablePath` option:
   ```js
   browser = await puppeteer.launch({
     executablePath: '/usr/bin/chromium-browser', // path on your server
     headless: 'new',
     args: ['--no-sandbox', ...]
   });
   ```

> ⚠️ Important: Always check each platform's ToS before scraping in production. Consider using official partner APIs instead.

---

## Future Enhancements

- **AI Price Prediction** — Add a `/api/predict` endpoint with a trained model
- **User Accounts** — Add NextAuth.js + MongoDB user schema
- **Email Alerts** — Notify users when prices drop for saved routes
- **Mobile App** — Reuse the Express backend with React Native frontend
- **Partner APIs** — Replace scrapers with official RedBus / AbhiBus API keys
