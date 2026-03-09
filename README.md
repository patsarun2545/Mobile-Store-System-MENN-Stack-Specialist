# 📱 Mobile Shop System

A full-stack web application for managing a mobile phone shop — covering stock buying, sales processing, repair/service jobs, and company settings. Built with **Next.js** (frontend) and **Node.js + Express** (backend), using **MongoDB** via **Prisma ORM**.

---

## 🚀 Tech Stack

| Layer | Technology |
|-------|-----------|
| Frontend | Next.js 14 (App Router), TypeScript, Tailwind CSS |
| Backend | Node.js, Express.js |
| Database | MongoDB (via Prisma ORM) |
| Auth | JWT (jsonwebtoken) |
| Libraries | Axios, SweetAlert2, Day.js, Recharts |
| Deployment | Ubuntu Linux, PM2 |

---

## ✨ Features

| Page | Description |
|------|-------------|
| **Dashboard** | Summary stats — total income, total sales count, total repair jobs. Monthly income bar chart (Recharts). |
| **Buy (Stock In)** | Record purchased phones with serial number, name, model, color, price, and customer info. Supports bulk entry via quantity field (max 10,000 units). CRUD with modal form. |
| **Sell** | Sell phones by serial number lookup. Builds a pending sell list with running total. Confirm all pending sales at once — updates product status to `sold`. |
| **Service (Repair)** | Log repair/service jobs with name, price, remark, and date. Full CRUD with modal form. |
| **Users** | Manage staff accounts — name, username, password (with confirm), and level (`admin` / `user`). Soft-delete sets status to `inactive`. |
| **Company Settings** | Set shop info — name, address, phone, email, tax code. Upsert pattern (create on first save, update thereafter). |

---

## 🔄 Product Status Flow

```
instock  →  (added to sell list)  →  sold
                ↓
            delete  (soft-deleted from buy list)
```

| Status | Description |
|--------|-------------|
| `instock` | Product available for sale |
| `sold` | Product has been sold (confirmed) |
| `delete` | Soft-deleted, hidden from buy list |

---

## 💰 Sell Workflow

```
1. Enter serial + price  →  POST /api/sell/create
2. System looks up product (status = "instock")
3. Added to pending sell list
4. Admin reviews list + total amount
5. Confirm  →  GET /api/sell/confirm
   - All pending sells → status: "paid"
   - All related products → status: "sold"
```

---

## 🗃️ Database Models (MongoDB / Prisma)

### User
| Field | Type | Notes |
|-------|------|-------|
| id | ObjectId | Auto |
| name | String | Display name |
| username | String | Unique |
| password | String | |
| level | String | `admin` / `user` (default: `user`) |
| status | String | `active` / `inactive` (default: `active`) |

### Product
| Field | Type | Notes |
|-------|------|-------|
| id | ObjectId | Auto |
| serial | String | Device serial number |
| name | String | Product name |
| release | String | Model / version |
| color | String | |
| price | Int | Purchase price |
| customerName | String | Supplier contact |
| customerPhone | String | |
| customerAddress | String | |
| remark | String | |
| status | String | `instock` / `sold` / `delete` |

### Sell
| Field | Type | Notes |
|-------|------|-------|
| id | ObjectId | Auto |
| productId | ObjectId | Ref → Product |
| price | Int | Selling price |
| status | String | `pending` / `paid` |
| payDate | DateTime | |

### Service
| Field | Type | Notes |
|-------|------|-------|
| id | ObjectId | Auto |
| name | String | Service description |
| price | Int | |
| remark | String | Optional |
| payDate | DateTime | |

### Company
| Field | Type | Notes |
|-------|------|-------|
| id | ObjectId | Auto |
| name | String | Shop name |
| address | String | |
| phone | String | |
| email | String | Optional |
| taxCode | String | Tax ID |

---

## 🔌 API Endpoints

### Auth & Users
| Method | Endpoint | Description |
|--------|----------|-------------|
| `POST` | `/api/user/signin` | Sign in — returns JWT |
| `GET` | `/api/user/info` | Get current user (JWT) |
| `PUT` | `/api/user/update` | Update own profile |
| `GET` | `/api/user/list` | List active users |
| `POST` | `/api/user/create` | Create user |
| `PUT` | `/api/user/update/:id` | Update user by ID |
| `DELETE` | `/api/user/remove/:id` | Soft-delete user (→ inactive) |

### Company
| Method | Endpoint | Description |
|--------|----------|-------------|
| `POST` | `/api/company/create` | Upsert company info |
| `GET` | `/api/company/list` | Get company info |

### Buy (Stock)
| Method | Endpoint | Description |
|--------|----------|-------------|
| `POST` | `/api/buy/create` | Add stock (supports `qty` bulk insert) |
| `GET` | `/api/buy/list` | List products (excludes deleted) |
| `PUT` | `/api/buy/update/:id` | Update product |
| `DELETE` | `/api/buy/remove/:id` | Soft-delete product (→ status: delete) |

### Sell
| Method | Endpoint | Description |
|--------|----------|-------------|
| `POST` | `/api/sell/create` | Add to pending sell list (lookup by serial) |
| `GET` | `/api/sell/list` | List pending sells |
| `DELETE` | `/api/sell/remove/:id` | Remove from pending list |
| `GET` | `/api/sell/confirm` | Confirm all pending → paid, products → sold |
| `GET` | `/api/sell/dashboard` | Stats: total income, total sales, total repairs |

### Service
| Method | Endpoint | Description |
|--------|----------|-------------|
| `POST` | `/api/service/create` | Log service job |
| `GET` | `/api/service/list` | List all service jobs |
| `PUT` | `/api/service/update/:id` | Update service job |
| `DELETE` | `/api/service/remove/:id` | Delete service job |

---

## ⚙️ Getting Started

### Prerequisites
- Node.js >= 18
- MongoDB (local or Atlas)

### Installation

```bash
# Clone the repository
git clone https://github.com/patsarun2545/<repo-name>.git
cd <repo-name>
```

**Backend:**
```bash
cd backend
npm install
```

**Frontend:**
```bash
cd frontend
npm install
```

### Environment Variables

Create `.env` in `backend/`:

```env
DATABASE_URL="mongodb+srv://<user>:<password>@cluster.mongodb.net/mobileshop"
SECRET_KEY="your_jwt_secret"
PORT=3001
```

Create `.env.local` in `frontend/`:

```env
NEXT_PUBLIC_API_URL="http://localhost:3001/api"
```

### Database Setup

```bash
cd backend
npx prisma generate
npx prisma db push
```

### Run Development

```bash
# Start backend (port 3001)
cd backend
npm run dev

# Start frontend (port 3000)
cd frontend
npm run dev
```

---

## 👨‍💻 Author

**Patsarun Kathinthong**
- Email: patsarun2545@gmail.com
- GitHub: [github.com/patsarun2545](https://github.com/patsarun2545)

---
