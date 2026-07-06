# CampusMarket 🎓

A campus-only marketplace where students can buy and sell textbooks, gadgets, and other items with people from their own college (or across colleges). Built with a vanilla JS/HTML frontend and a REST + WebSocket backend, with real-time buyer–seller chat baked in.

[Link](https://campus-market-frontend-6nl6.onrender.com)

## ✨ Features

- **Browse & search** — paginated product grid with sort (newest/oldest/price), category filter, and college filter
- **Auth** — signup/login with JWT access + refresh tokens, auto-refresh on 401, session-expiry banner
- **Password recovery** — mobile-number + OTP based forgot-password flow
- **Sell items** — list products with title, description, price, category, and an image (file upload or pasted URL, client-side compression before upload)
- **Manage listings** — edit price/description/status, mark as sold, delete
- **Profile** — update username/college, change password, view your listings and purchases in one place
- **Real-time chat** — buyers message sellers per-product over WebSocket (SockJS + STOMP); sellers can mark the item as sold directly from the chat thread
- **Reporting** — flag listings that violate community guidelines
- **Responsive UI** — mobile, tablet, and desktop layouts
- **Admin Panel** (`admin.html`) — separate, role-gated dashboard for platform management (see below)

## 🛠 Tech Stack

**Frontend**
- Vanilla HTML/CSS/JavaScript (no framework, no build step)
- [SockJS](https://github.com/sockjs/sockjs-client) + [Stomp.js](https://stomp-js.github.io/) for real-time chat
- Google Fonts (Space Grotesk / Space Mono)

**Backend** *(adjust to match your actual implementation)*
- REST API (JWT-based auth, refresh tokens)
- WebSocket endpoint at `/chat` for messaging
- Image upload endpoint (`/upload/image`) — Cloudinary or similar
- Runs on `http://localhost:8084` in development

## 📁 Project Structure

```
campus-market/
├── frontend/
│   ├── index.html        # Customer-facing marketplace (markup, styles, JS in one file)
│   └── admin.html        # Admin Panel — restricted to ADMIN / SUPER_ADMIN roles
├── backend/               # Spring Boot / your API service
│   └── ...
└── README.md
```

> Update the tree above to match your actual repo layout.

## 🚀 Getting Started

### Prerequisites
- A modern browser
- Backend service running locally (default expected at `http://localhost:8084`)

### Run the frontend
Since it's a single static HTML file, you can just open it directly or serve it:

```bash
# Option 1: open directly
open frontend/index.html

# Option 2: serve locally
npx serve frontend
```

### Point the frontend at your backend
In `index.html`, update the `API` constant near the top of the `<script>` block:

```js
const API = 'http://localhost:8084';
// const API = 'https://your-deployed-backend.example.com';
```

### Run the backend
Add backend setup instructions here, e.g.:

```bash
cd backend
./mvnw spring-boot:run
```

## 🔌 API Endpoints Used by the Frontend

| Area | Endpoint | Method |
|---|---|---|
| Auth | `/auth/signup` | POST |
| Auth | `/auth/login` | POST |
| Auth | `/auth/refresh` | POST |
| Auth | `/auth/logout` | POST |
| Auth | `/auth/forgot-password` | POST |
| Auth | `/auth/verify-otp` | POST |
| Auth | `/auth/reset-password` | POST |
| Products | `/products/filter` | GET |
| Products | `/products/getProductTitle` | GET |
| Products | `/products/addProduct/{categoryId}` | POST |
| Products | `/products/updateProduct/{id}` | PUT |
| Products | `/products/deleteProduct/{id}` | DELETE |
| Products | `/products/sell/{buyerId}/{productId}` | PUT |
| Products | `/products/getProductSellId/{userId}` | GET |
| Products | `/products/getProductBuyId/{userId}` | GET |
| Products | `/products/colleges`, `/products/colleges/by-category` | GET |
| Categories | `/categorys/allCategory` | GET |
| Users | `/users/profile` | GET |
| Users | `/users/updateProfile/{id}` | PUT |
| Users | `/users/changePassword` | PUT |
| Users | `/users/deleteUser` | DELETE |
| Reports | `/reports/addReport/{productId}` | POST |
| Uploads | `/upload/image` | POST |
| Chat rooms | `/api/v1/rooms`, `/api/v1/rooms/{roomId}`, `/api/v1/rooms/seller/{id}`, `/api/v1/rooms/buyer/{id}` | GET/POST |
| Chat messages | `/api/v1/rooms/{roomId}/message` | GET |
| Chat (live) | `/chat` (SockJS), topic `/topic/room/{roomId}`, send `/app/sendMessage/{roomId}` | WS |

## 🛡 Admin Panel

A separate page (`admin.html`) provides a role-gated dashboard for managing the platform. It's not linked from public navigation beyond an "Admin" link — access is enforced by role, not by hiding the URL.

### Roles & Permissions

| Role | Can view dashboard | Can delete USER | Can delete ADMIN | Can delete SUPER_ADMIN | Can promote USER → ADMIN |
|---|---|---|---|---|---|
| `USER` | ❌ | — | — | — | — |
| `ADMIN` | ✅ | ✅ | ❌ | ❌ | ✅ |
| `SUPER_ADMIN` | ✅ | ✅ | ✅ | ❌ | ✅ |

> No role can delete a `SUPER_ADMIN` account through the panel.

### Panels

- **Dashboard** — total users, products, categories, and available-listing counts; recent users list; quick action shortcuts
- **Users** — searchable/paginated user table (by name and college), per-user detail modal showing their listings, reports made, and reports received, promote-to-admin, and permission-aware delete (with a confirmation modal)
- **Products** — read-only paginated view of all listings, filterable by status (Available / Sold)
- **Categories** — add one or more categories at once, delete existing ones
- **Reports** — pointer to the per-user report view, plus a quick lookup by user ID
- **Add Admin** — promote a user to `ADMIN` by username + user ID

### Admin-only Endpoints

| Area | Endpoint | Method |
|---|---|---|
| Users | `/users/allUser` | GET |
| Users | `/users/search` | GET |
| Users | `/users/userId/{id}` | GET |
| Users | `/users/delete/{id}` | DELETE |
| Admin | `/admin/onBordNewAdmin` | POST |
| Categories | `/categorys/addCategory` | POST |
| Categories | `/categorys/delete` | DELETE |

These share the same `/auth/login` endpoint as the main site, but access to the panel itself is gated client-side on the returned `roles` array (`ADMIN` or `SUPER_ADMIN` required) — make sure your backend also enforces this server-side on every admin route, since a client-side check alone isn't a security boundary.

## 📱 Responsive Design

The UI adapts across three breakpoints:
- **Desktop** — full sidebar with category list + college filter alongside the product grid
- **Tablet (769–1024px)** — condensed sidebar and search bar
- **Mobile (≤768px)** — category filters render as a horizontally scrollable chip bar so filtering stays accessible without hiding functionality

## 🤝 Contributing

1. Fork the repo
2. Create a feature branch (`git checkout -b feature/my-feature`)
3. Commit your changes
4. Open a pull request

## 📄 License

This project is licensed under the MIT License — see the [LICENSE](LICENSE) file for details.
