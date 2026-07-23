# Princes Cafe — Online Table Booking System

## What was fixed
The booking-time field previously let a customer pick any time (e.g. 5:10 AM), then rejected it
after submission. The `<input type="time">` now has `min="11:00"` and `max="23:00"` set directly,
so the native picker only offers times inside business hours in the first place. The JS and the
backend validator both still double-check the range as a safety net.

## Structure
```
PrincesCafeBooking/
├── client/              # Plain HTML/CSS/JS frontend (no build step needed)
│   ├── index.html
│   ├── style.css
│   └── script.js
├── server/               # Node.js + Express + MySQL backend
│   ├── config/db.js
│   ├── controllers/
│   ├── middleware/
│   ├── models/Booking.js
│   ├── routes/
│   ├── server.js
│   ├── package.json
│   └── .env.example
├── database/
│   └── schema.sql
└── README.md
```

## Running it locally

### 1. Database
```bash
mysql -u root -p < database/schema.sql
```
This creates the `princes_cafe` database with `bookings` and `admins` tables.

To create an admin login, hash a password with bcrypt and insert it:
```js
const bcrypt = require('bcryptjs');
bcrypt.hash('yourpassword', 10).then(console.log);
```
```sql
INSERT INTO admins (email, password_hash) VALUES ('owner@princescafe.com', '<hash from above>');
```

### 2. Backend
```bash
cd server
cp .env.example .env   # then fill in your DB password and a JWT secret
npm install
npm run dev
```
API runs on `http://localhost:5000/api`.

### 3. Frontend
Just open `client/index.html` in a browser, or serve it:
```bash
cd client
npx serve .
```
If your backend runs on a different host/port, update `API_BASE_URL` at the top of `script.js`.

## API endpoints
| Method | Endpoint | Access |
|---|---|---|
| POST | /api/booking | Public — create a booking |
| GET | /api/bookings | Admin — list/search/filter/sort bookings |
| GET | /api/bookings/stats | Admin — dashboard counts |
| GET | /api/bookings/:id | Admin — single booking |
| PUT | /api/bookings/:id | Admin — edit a booking |
| DELETE | /api/bookings/:id | Admin — delete a booking |
| PATCH | /api/bookings/:id/approve | Admin — approve |
| PATCH | /api/bookings/:id/reject | Admin — reject |
| POST | /api/auth/login | Admin login, returns a JWT |

Admin routes require `Authorization: Bearer <token>` from `/api/auth/login`.

## Security included
JWT auth on admin routes, bcrypt password hashing, Helmet security headers, rate limiting,
parameterized SQL queries (no injection risk), server-side validation mirroring the frontend rules,
and CORS restricted via `CLIENT_ORIGIN`.

## Not included yet
The admin dashboard UI (search/filter table, export to Excel/PDF, calendar view) — say the word
and I'll build that next as its own set of files.
