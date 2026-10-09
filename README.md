<div align="center">

# 🎨 OneDraw

### *Next-Gen Real-Time Collaborative Whiteboard & Vector Design Engine*

[![Author](https://img.shields.io/badge/Author-Satvik%20Sharma-blue.svg?style=for-the-badge&logo=github)](https://github.com/SatvikPy)
[![GitHub stars](https://img.shields.io/github/stars/SatvikPy/OneDraw?style=for-the-badge&color=gold)](https://github.com/SatvikPy/OneDraw/stargazers)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg?style=for-the-badge)](LICENSE)
[![Next.js](https://img.shields.io/badge/Next.js-15.1-black?style=for-the-badge&logo=next.js)](https://nextjs.org/)
[![React](https://img.shields.io/badge/React-19.0-61DAFB?style=for-the-badge&logo=react)](https://reactjs.org/)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.0-blue?style=for-the-badge&logo=typescript)](https://www.typescriptlang.org/)
[![Node.js](https://img.shields.io/badge/Node.js-20+-339933?style=for-the-badge&logo=node.js)](https://nodejs.org/)
[![Prisma](https://img.shields.io/badge/Prisma-ORM-2D3748?style=for-the-badge&logo=prisma)](https://www.prisma.io/)
[![Tailwind CSS](https://img.shields.io/badge/Tailwind-CSS-38B2AC?style=for-the-badge&logo=tailwind-css)](https://tailwindcss.com/)

[**Explore Demo**](http://localhost:3000) • [**Report Bug**](https://github.com/SatvikPy/OneDraw/issues) • [**Request Feature**](https://github.com/SatvikPy/OneDraw/issues) • [**Connect on GitHub**](https://github.com/SatvikPy)

---

</div>

## 📖 Overview

**OneDraw** is a high-performance, real-time collaborative whiteboard and vector designing suite designed for seamless visual brainstorming, architectural diagramming, prototyping, and team sketching. 

Engineered with a **Next.js 15 App Router** frontend, a **Node.js/Express REST API**, and a dedicated **high-throughput WebSocket server**, OneDraw enables ultra-low-latency real-time multi-user interaction with live cursor synchronization, shape locking, and persistent state management.

---

## ✨ Key Features

### 🖌️ Creative Toolkit
- **Vector & Freehand Drawing**: Smooth brush, pen, pencil, rectangle, circle/ellipse, line, and arrow tools.
- **Custom Styling & Palettes**: Dynamic color picker, hex presets, custom stroke widths, opacity, and fill options.
- **Shape Manipulation & Dragging**: Move, resize, and reposition canvas elements seamlessly.
- **Undo / Redo History**: Complete state history tracking for uninterrupted creative flow.

### ⚡ Real-Time Collaboration
- **Sub-millisecond WebSocket Sync**: Real-time broadcast of drawing actions across all connected clients.
- **Live User Presence & Cursors**: See collaborators' names, live cursor pointers, and hover focus in real-time.
- **Shape Locking & Conflict Resolution**: Automatic temporal locking mechanism (`lockedBy`, `lockExpire`) prevents conflicting simultaneous edits on the same vector element.
- **Instant Room Generation & Sharing**: Shareable room links with one-click clipboard copy.

### 🔐 Security & Persistence
- **JWT-Based Authentication**: Secure sign-up, sign-in, and token-verified WebSocket handshakes.
- **Type-Safe ORM Persistence**: Powered by Prisma ORM supporting SQLite for effortless local dev and PostgreSQL for production scale.
- **Zod Data Validation**: Robust schema validation on all incoming API payloads.

---

## 🏗️ System Architecture

```mermaid
flowchart TD
    subgraph Client["Frontend Client (Next.js 15 + React 19)"]
        UI["UI Layer / Toolbar / Navbar"]
        Canvas["HTML5 Canvas / DrawHandler"]
        WSClient["SignalingManager (WebSocket Client)"]
        State["Jotai & React State"]
    end

    subgraph BackendAPI["Backend REST Service (:5000)"]
        Express["Express.js Server"]
        AuthMiddleware["JWT Middleware"]
        AuthRoute["/backend/signIn & /signUp"]
        RoomRoute["/backend/createRoom & /getRoom"]
        ShapeRoute["/backend/getMessages & /checkIfLocked"]
    end

    subgraph RealtimeServer["WebSocket Service (:8080)"]
        WSS["WebSocket Server (ws)"]
        RoomManager["Room & Participant Manager"]
        CursorTracker["Live Cursor Broadcast Engine"]
        SyncEngine["Shape Move & Create Broadcast"]
    end

    subgraph Database["Data Layer (Prisma ORM)"]
        DB[(SQLite / PostgreSQL)]
    end

    UI --> Canvas
    Canvas --> WSClient
    UI --> Express
    WSClient <-->|Bi-directional WS Events| WSS
    Express --> AuthMiddleware
    AuthMiddleware --> DB
    WSS --> DB
```

---

## 📂 Repository Structure

```
OneDraw/
├── Frontend/                 # Next.js 15 frontend application
│   ├── app/                  # App router pages (Landing, Signin, Signup, Whiteboard/[roomId])
│   ├── Classes/              # Canvas engine (DrawHandler, OfflineDrawHandler, SignalingManager)
│   ├── Components/           # UI components (Navbar, ToolBar, Whiteboard, ColorDrawer, Participants)
│   ├── store/                # Jotai / React state atoms
│   └── utils/                # API helpers (getShapes, userFetcher)
├── backend/                  # Node.js + Express REST API
│   ├── prisma/               # Prisma schema & migration files
│   └── src/                  # Controllers, JWT middleware, routes & schemas
├── WsServer/                 # Standalone WebSocket synchronization server
│   ├── prisma/               # Shared Prisma schema
│   └── src/                  # WebSocket connection, room broadcasting & cursor engine
├── manifests/                # Kubernetes deployment manifests
├── buildspec.yml             # AWS CodeBuild / ECR CI/CD pipeline
└── package.json              # Workspace root scripts
```

---

## 🚀 Quickstart Guide

### Prerequisites
- [Node.js](https://nodejs.org/) (v18.0.0 or higher recommended)
- [npm](https://www.npmjs.com/) (v9.0.0 or higher)
- [Git](https://git-scm.com/)

---

### 1. Clone the Repository
```bash
git clone https://github.com/SatvikPy/OneDraw.git
cd OneDraw
```

---

### 2. Configure Environment Variables

Create the local environment files for each service:

#### Backend (`backend/.env`):
```env
DATABASE_URL="file:../../dev.db"
PORT=5000
FRONTEND_URL="http://localhost:3000"
```

#### WebSocket Server (`WsServer/.env`):
```env
DATABASE_URL="file:../../dev.db"
PORT=8080
```

#### Frontend (`Frontend/.env.local`):
```env
NEXT_PUBLIC_BACKEND_URL="http://localhost:5000/backend"
NEXT_PUBLIC_WEBSOCKET_SERVER_URL="ws://localhost:8080"
```

---

### 3. Install Dependencies & Initialize Database

#### Step A: Backend
```bash
cd backend
npm install
npx prisma db push
cd ..
```

#### Step B: WebSocket Server
```bash
cd WsServer
npm install
npx prisma generate
cd ..
```

#### Step C: Frontend
```bash
cd Frontend
npm install --legacy-peer-deps
cd ..
```

---

### 4. Start the Application

You can start each service in separate terminal tabs:

```bash
# Terminal 1 - Backend API (Port 5000)
npm run dev:backend

# Terminal 2 - WebSocket Server (Port 8080)
npm run dev:ws

# Terminal 3 - Frontend Web App (Port 3000)
npm run dev:frontend
```

Open **[http://localhost:3000](http://localhost:3000)** in your browser to start drawing and collaborating!

---

## 📡 API & WebSocket Protocols

### REST Endpoints (`http://localhost:5000/backend`)

| Method | Endpoint | Description | Auth Required |
| :--- | :--- | :--- | :--- |
| `POST` | `/signUp` | Register a new user account | No |
| `POST` | `/signIn` | Authenticate user & receive JWT token | No |
| `GET` | `/getUser/:userId` | Fetch user profile details | No |
| `POST` | `/getUsers` | Batch query user details by ID array | No |
| `POST` | `/createRoom` | Create or retrieve user whiteboard room | **Yes** (Bearer Token) |
| `GET` | `/getRoom/:slug` | Retrieve room metadata by slug identifier | No |
| `GET` | `/getMessages/:roomId` | Load persistent canvas shapes for a room | No |
| `GET` | `/checkIfLocked/:shapeId` | Check and claim temporary lock on a shape | **Yes** (Bearer Token) |

---

### WebSocket Message Protocol (`ws://localhost:8080?token=<JWT>`)

```json
// Join Room
{ "type": "joinRoom", "roomId": "room-uuid" }

// Draw / Create Shape
{ "type": "msg", "roomID": "room-uuid", "shape": { "id": "uuid", "type": "rect", "x": 100, "y": 100, ... } }

// Move / Update Existing Shape
{ "type": "move", "roomID": "room-uuid", "shape": { "id": "uuid", "x": 150, "y": 120 } }

// Live Cursor Synchronization
{ "type": "cursorMove", "roomId": "room-uuid", "x": 420, "y": 280 }

// Leave Room
{ "type": "leaveRoom", "roomId": "room-uuid" }
```

---

## 🐳 Containerization & Deployment

Each service contains a dedicated `Dockerfile` for containerized environments:

```bash
# Build Frontend
docker build -t onedraw-frontend ./Frontend

# Build Backend
docker build -t onedraw-backend ./backend

# Build WebSocket Server
docker build -t onedraw-ws ./WsServer
```

Kubernetes manifests are ready in the [`manifests/`](./manifests) directory for deployment to AWS EKS or standard K8s clusters.

---

## 🤝 Contributing

Contributions, issues, and feature requests are welcome!

1. Fork the Project
2. Create your Feature Branch (`git checkout -b feature/AmazingFeature`)
3. Commit your Changes (`git commit -m 'feat: Add AmazingFeature'`)
4. Push to the Branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

---

## 📄 License

Distributed under the **MIT License**. See [`LICENSE`](LICENSE) for more information.

---

## 👨‍💻 Author & Maintainer

<div align="center">

### **Satvik Sharma**
*Full-Stack Engineer & Open Source Developer*

[![GitHub](https://img.shields.io/badge/GitHub-SatvikPy-181717?style=for-the-badge&logo=github)](https://github.com/SatvikPy)
[![Email](https://img.shields.io/badge/Email-satviksharma.bpl%40gmail.com-EA4335?style=for-the-badge&logo=gmail&logoColor=white)](mailto:satviksharma.bpl@gmail.com)

*Made with ❤️ by Satvik Sharma*

</div>
