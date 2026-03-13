# TravelCompare 🚌

A production-ready bus ticket price comparison web application that aggregates results from multiple platforms (RedBus, AbhiBus, Paytm Travel) and helps users find the best deals.

## Features

- 🔍 **Route Search** — Search buses by source, destination, and date
- 💰 **Price Comparison** — Compare prices across platforms for the same operator
- 🎯 **Smart Filters** — Filter by price, departure time, bus type, and rating
- 📊 **Sorting** — Sort by cheapest, fastest, or best rated
- 🔗 **Booking Redirect** — Direct users to the cheapest booking platform
- ⚡ **Redis Caching** — 10-minute cache to reduce scraping load
- 📈 **Analytics** — Track searches, popular routes, and clicks

## Tech Stack

| Layer       | Technology                          |
|-------------|-------------------------------------|
| Frontend    | Next.js 14, React, TailwindCSS      |
| Backend     | Node.js, Express.js                 |
| Database    | MongoDB Atlas + Mongoose ORM        |
| Cache       | Redis                               |
| Scraping    | Puppeteer                           |

## Project Structure

```
travelcompare/
├── frontend/          # Next.js 14 app
│   ├── app/           # App router pages
│   ├── components/    # Reusable UI components
│   ├── services/      # API service layer
│   └── styles/        # Global CSS
│
└── backend/           # Express.js API
    ├── routes/        # API route definitions
    ├── controllers/   # Request handlers
    ├── services/      # Business logic
    ├── scrapers/      # Puppeteer scrapers
    ├── models/        # Mongoose schemas
    ├── utils/         # Utilities (cache, logger)
    └── config/        # Configuration files
```

## Quick Start

### Prerequisites
- Node.js 18+
- MongoDB Atlas account (or local MongoDB)
- Redis (local or Redis Cloud)

### 1. Clone & Install

```bash
# Backend
cd backend
cp .env.example .env   # fill in your values
npm install
npm run dev

# Frontend (new terminal)
cd frontend
cp .env.example .env.local
npm install
npm run dev
```

### 2. Environment Variables

**Backend `.env`**
```
PORT=5000
MONGODB_URI=mongodb+srv://<user>:<pass>@cluster.mongodb.net/travelcompare
REDIS_URL=redis://localhost:6379
NODE_ENV=development
FRONTEND_URL=http://localhost:3000
```

**Frontend `.env.local`**
```
NEXT_PUBLIC_API_URL=http://localhost:5000
```

### 3. Visit the App

- Frontend: http://localhost:3000
- Backend API: http://localhost:5000/api

## Deployment

| Service   | Platform       |
|-----------|----------------|
| Frontend  | Vercel         |
| Backend   | Render         |
| Database  | MongoDB Atlas  |
| Cache     | Redis Cloud    |

See DEPLOYMENT.md for step-by-step instructions.

## API Reference

### Search Buses

```
GET /api/search?from=bangalore&to=hyderabad&date=2026-04-10
```

**Response:**
```json
{
  "route": "Bangalore → Hyderabad",
  "buses": [
    {
      "operator": "VRL Travels",
      "platform": "RedBus",
      "busType": "AC Sleeper",
      "departure": "21:00",
      "arrival": "06:00",
      "duration": "9h",
      "price": 950,
      "rating": 4.5,
      "bookingUrl": "https://redbus.in/..."
    }
  ]
}
```

### Analytics

```
GET /api/analytics/popular-routes
GET /api/analytics/search-count
```

## License

MIT
