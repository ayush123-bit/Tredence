# 📚 Pair Programming App — Real-Time Collaborative Coding Platform

A real-time collaborative coding platform built using **FastAPI**, **MongoDB**, and **React**.  
Features include room-based collaboration, WebSocket synchronization, MongoDB persistence, and AI-powered autocomplete suggestions.

---

## 📁 Folder Structure

```
pair-programming-app/
├── backend/
│   ├── app/
│   │   ├── __init__.py
│   │   ├── main.py
│   │   ├── database.py
│   │   ├── models.py
│   │   ├── schemas.py
│   │   ├── routers/
│   │   │   ├── __init__.py
│   │   │   ├── rooms.py
│   │   │   ├── autocomplete.py
│   │   │   └── websocket.py
│   │   └── services/
│   │       ├── __init__.py
│   │       ├── room_service.py
│   │       └── autocomplete_service.py
│   ├── requirements.txt
│   ├── .env
│   ├── init_mongo.py
│   └── test_mongodb.py
│
├── frontend/
│   ├── src/
│   │   ├── components/
│   │   │   ├── CodeEditor.tsx
│   │   │   └── RoomJoin.tsx
│   │   ├── store/
│   │   │   ├── store.ts
│   │   │   └── editorSlice.ts
│   │   ├── services/
│   │   │   ├── websocket.ts
│   │   │   └── api.ts
│   │   ├── App.tsx
│   │   └── main.tsx
│   ├── package.json
│   ├── tsconfig.json
│   └── vite.config.ts
│
├── docker-compose.yml
├── README.md
└── .gitignore
```

---

## 🚀 Quick Start Guide

### Method 1 — Docker (Recommended)

**Prerequisites**
- Docker
- Docker Compose

**Steps**
```bash
git clone https://github.com/ayush123-bit/Tredence
cd pair-programming-app

docker-compose up --build
```

**Access**
- Backend: http://localhost:8000  
- Frontend: http://localhost:5173  
- API Docs: http://localhost:8000/docs  
- MongoDB: localhost:27017

---

## ⚙️ Manual Setup

### Step 1 — Install & Start MongoDB

**Option A — Docker**
```bash
docker run -d -p 27017:27017 --name mongodb mongo:latest
```

**Option B — Local Installation**
- macOS: `brew install mongodb-community && brew services start mongodb-community`
- Ubuntu: `sudo apt install mongodb && sudo systemctl start mongodb`
- Windows: Download and install from MongoDB's official site.

Verify MongoDB:
```bash
mongosh --eval "db.version()"
```

---

### Step 2 — Backend Setup

**Requirements**
- Python 3.9+
- MongoDB running

**Steps**
```bash
cd backend

python -m venv venv
```

Activate the virtual environment:

**Windows**
```bash
venv\Scripts\activate
```

**macOS / Linux**
```bash
source venv/bin/activate
```

Install dependencies:
```bash
pip install -r requirements.txt
```

Create `.env`:
```bash
echo "MONGODB_URL=mongodb://localhost:27017" > .env
echo "DATABASE_NAME=pair_programming" >> .env
```

(Optional) initialize DB:
```bash
python init_mongo.py
```

Test DB:
```bash
python test_mongodb.py
```

Start backend:
```bash
uvicorn app.main:app --reload --host 0.0.0.0 --port 8000
```

---

### Step 3 — Frontend Setup

**Prerequisites**
- Node.js 16+

**Steps**
```bash
cd frontend
npm install
npm run dev
```

Open the frontend at: `http://localhost:5173`

---

## 🧪 Testing the Application

**Create Room**
```bash
curl -X POST http://localhost:8000/api/rooms   -H "Content-Type: application/json"   -d '{"language": "python"}'
```

**Get Room Details**
```bash
curl http://localhost:8000/api/rooms/abc123ef
```

**Update Room Code**
```bash
curl -X PUT http://localhost:8000/api/rooms/abc123ef/code   -H "Content-Type: application/json"   -d '{"code":"print("Hello World")"}'
```

**Get Autocomplete Suggestion**
```bash
curl -X POST http://localhost:8000/api/autocomplete   -H "Content-Type: application/json"   -d '{
    "code": "def ",
    "cursor_position": 4,
    "language": "python"
  }'
```

**WebSocket Test (wscat)**
```bash
npm install -g wscat

wscat -c ws://localhost:8000/ws/abc123ef
```

---

## 🌐 Web Interface

- Visit `http://localhost:5173`  
- Create a room, share the room ID, join from another tab to see real-time updates.  
- Autocomplete triggers after a 600ms debounce.

---

## 🏗️ Architecture Overview

**Backend** — FastAPI  
- High performance, async-first design, seamless WebSocket support, and automatic API docs.

**Database** — MongoDB (Motor)
- Flexible JSON-like schema, great async support, and a natural fit for storing code snippets.

**Real-Time Sync**
- Code changes are broadcast to all connected clients in a room.
- Strategy: Last-Write-Wins (simple prototype approach).
- ConnectionManager handles active WebSocket connections per room.

**MongoDB Room Document**
```js
{
  _id: ObjectId(),
  room_id: "abc123ef",
  code: "print()",
  language: "python",
  created_at: ISODate(),
  updated_at: ISODate()
}
```

Indexes:
- `room_id` (unique)
- `created_at`

---

## 🔌 API Endpoints

**REST**
- `POST /api/rooms` — Create a new room
- `GET /api/rooms/{room_id}` — Get room details
- `PUT /api/rooms/{room_id}/code` — Update room code
- `POST /api/autocomplete` — Get code suggestions
- `GET /health` — Health check
- `GET /docs` — Swagger UI

**WebSocket**
- `ws://localhost:8000/ws/{room_id}` — Connect to a room for real-time collaboration

---

## 🎯 Key Features Implemented

- Real-time collaboration via WebSockets  
- Room creation and joining with unique 8-character room IDs  
- MongoDB persistence for room state  
- Debounced (600ms) AI autocomplete (mock implementation)  
- Clean service-layer architecture and Pydantic schemas

---

## 🚧 Known Limitations

- No user authentication — anyone with a room ID can join  
- Autocomplete is pattern-based (mock) — not a full AI model  
- No cursor position sharing between users  
- No chat or voice/video — editor only  
- No conflict-resolution algorithm (OT/CRDT) — last-write-wins behavior  
- WebSocket state is in-memory and is lost on server restart

---

## 🔮 Roadmap & Future Improvements

**Short-Term**
- Integrate CRDT/OT for operational transforms  
- Add cursor sharing and presence indicators  
- Room password protection and basic access control  
- Improved error handling and retry logic

**Medium-Term**
- Integrate a real AI model for smarter autocomplete (e.g., OpenAI / code-specific model)  
- Add code execution sandbox (isolated)  
- Add chat and multi-file support

**Long-Term**
- Audio/video chat and screen sharing  
- Git integration and code review workflows  
- Mobile app and IDE extensions  
- Production-grade MongoDB setup (replica sets / sharding)

---

## ✅ Contribution & License

Contributions are welcome. Please open issues or PRs against the repository. Include tests and update documentation where appropriate.

---

If you'd like, I can also:
- Add badges (build, coverage, license)  
- Insert screenshots or a short GIF demo  
- Generate a compact Table of Contents for the README  
- Create sample `.env.example` and `docker-compose.override.yml`

