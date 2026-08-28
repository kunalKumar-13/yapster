# Yapster

A real-time chat application — **Socket.IO** messaging over an **Express +
MongoDB** API, with a **React + Vite** client, JWT authentication and image
sharing through Cloudinary.

**Live:** <https://full-stack-project-azure.vercel.app>

## Features

- **Real-time messaging** — messages arrive over a Socket.IO connection, with a
  server-side `userSocketMap` tracking who is currently online
- **Online presence** — the sidebar reflects connected users live
- **Authentication** — signup and login issuing a JWT, verified by an auth
  middleware on every protected route
- **Media messages** — image uploads stored on Cloudinary rather than in the
  database
- **Profile management** — display name, bio and avatar
- **Seen state** — messages are marked as seen when the conversation is opened

## Stack

| Layer | Technology |
|---|---|
| Client | React 19, Vite, React Router, Context API |
| Realtime | Socket.IO |
| API | Node.js, Express |
| Database | MongoDB with Mongoose |
| Auth | JSON Web Tokens, bcrypt |
| Media | Cloudinary |

## Architecture

```
client/                     React + Vite
├── context/
│   ├── AuthContext.jsx     token, current user, socket lifecycle
│   └── ChatContext.jsx     conversations, messages, unseen counts
├── pages/                  Home · Login · Profile
└── components/             Sidebar · ChatContainer · RightSidebar

server/                     Express
├── controllers/            userController · messageController
├── middleware/auth.js      JWT verification
├── models/                 User · Message
├── routes/                 /api/auth · /api/messages
├── lib/                    db.js · cloudinary.js · utils.js
└── server.js               HTTP server + Socket.IO, userSocketMap
```

## Run it locally

**1. Server**

```bash
cd server
npm install
npm run server        # http://localhost:5000
```

Create `server/.env`:

```env
MONGODB_URI=your_mongodb_connection_string
PORT=5000
JWT_SECRET=your_jwt_secret
CLOUDINARY_CLOUD_NAME=your_cloud_name
CLOUDINARY_API_KEY=your_api_key
CLOUDINARY_API_SECRET=your_api_secret
```

**2. Client**

```bash
cd client
npm install
npm run dev           # http://localhost:5173
```

Create `client/.env`:

```env
VITE_BACKEND_URL=http://localhost:5000
```

> Keep both `.env` files out of version control — they hold live credentials.

## API

| Method | Endpoint | Purpose |
|---|---|---|
| `POST` | `/api/auth/signup` | Create an account |
| `POST` | `/api/auth/login` | Log in, receive a JWT |
| `GET`  | `/api/auth/check` | Validate the current token |
| `PUT`  | `/api/auth/update-profile` | Update profile and avatar |
| `GET`  | `/api/messages/users` | Sidebar users with unseen counts |
| `GET`  | `/api/messages/:id` | Conversation with one user |
| `PUT`  | `/api/messages/mark/:id` | Mark a message as seen |
| `POST` | `/api/messages/send/:id` | Send a message |
| `GET`  | `/api/status` | Health check |

Every route except `signup`, `login` and `status` is behind `protectRoute`.
