Pair Programming Application - Complete Documentation (MongoDB Version)
Project Overview
A real-time collaborative coding platform built with FastAPI (backend), MongoDB (database), and React (frontend). Features include room-based collaboration, WebSocket synchronization, MongoDB persistence, and AI-powered autocomplete suggestions.

📁 Complete Folder Structure
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
├── docker-compose.yml
├── README.md
└── .gitignore

🚀 Quick Start Guide
Method 1: Docker (Recommended)
Prerequisites:

Docker & Docker Compose installed

Steps:
bash# Clone the repository
git clone <your-repo-url>
cd pair-programming-app

# Start all services (MongoDB, Backend, Frontend)
docker-compose up --build

# Access the application
# Backend API: http://localhost:8000
# Frontend: http://localhost:5173
# API Docs: http://localhost:8000/docs
# MongoDB: localhost:27017
Method 2: Manual Setup
Step 1: Install and Start MongoDB
Option A: Using Docker
bashdocker run -d -p 27017:27017 --name mongodb mongo:latest
Option B: Local Installation

macOS: brew install mongodb-community && brew services start mongodb-community
Ubuntu: sudo apt-get install mongodb && sudo systemctl start mongodb
Windows: Download from https://www.mongodb.com/try/download/community

Verify MongoDB is running:
bashmongosh --eval "db.version()"
# Should show MongoDB version
Step 2: Backend Setup
Prerequisites:

Python 3.9+
MongoDB running

Steps:
bash# 1. Navigate to backend
cd backend

# 2. Create virtual environment
python -m venv venv

# 3. Activate virtual environment
# On macOS/Linux:
source venv/bin/activate
# On Windows:
venv\Scripts\activate

# 4. Install dependencies
pip install -r requirements.txt

# 5. Configure environment
# Create .env file
echo "MONGODB_URL=mongodb://localhost:27017" > .env
echo "DATABASE_NAME=pair_programming" >> .env

# 6. Initialize MongoDB (optional)
python init_mongo.py

# 7. Test MongoDB connection
python test_mongodb.py

# 8. Start the server
uvicorn app.main:app --reload --host 0.0.0.0 --port 8000
Backend will run on: http://localhost:8000
Step 3: Frontend Setup (Optional)
Prerequisites:

Node.js 16+ and npm

Steps:
bash# 1. Navigate to frontend
cd frontend

# 2. Install dependencies
npm install

# 3. Start development server
npm run dev
Frontend will run on: http://localhost:5173

🧪 Testing the Application
Using Postman or cURL
1. Create a Room
bashcurl -X POST http://localhost:8000/api/rooms \
  -H "Content-Type: application/json" \
  -d '{"language": "python"}'

# Response:
# {
#   "_id": "507f1f77bcf86cd799439011",
#   "room_id": "abc123ef",
#   "code": "# Welcome to room abc123ef\n# Start coding here...\n",
#   "language": "python",
#   "created_at": "2024-11-26T10:30:00.000Z",
#   "updated_at": null
# }
2. Get Room Details
bashcurl http://localhost:8000/api/rooms/abc123ef

# Response:
# {
#   "_id": "507f1f77bcf86cd799439011",
#   "room_id": "abc123ef",
#   "code": "# Welcome to room abc123ef\n# Start coding here...\n",
#   "language": "python",
#   "created_at": "2024-11-26T10:30:00.000Z"
# }
3. Update Room Code
bashcurl -X PUT http://localhost:8000/api/rooms/abc123ef/code \
  -H "Content-Type: application/json" \
  -d '{"code": "print(\"Hello World\")"}'
4. Get Autocomplete Suggestion
bashcurl -X POST http://localhost:8000/api/autocomplete \
  -H "Content-Type: application/json" \
  -d '{
    "code": "def ",
    "cursor_position": 4,
    "language": "python"
  }'

# Response:
# {
#   "suggestion": "def function_name(param):\n    pass",
#   "confidence": 0.85
# }
5. WebSocket Connection (using wscat)
bash# Install wscat
npm install -g wscat

# Connect to room
wscat -c ws://localhost:8000/ws/abc123ef

# You'll receive a connection message:
# {"type": "connected", "content": "Connected to room abc123ef", "timestamp": "..."}

# Send a code update:
{"type": "code_update", "content": "print('Hello from WebSocket')"}

# Open another terminal and connect to the same room to see real-time updates!
Using MongoDB Compass (GUI Tool)

Download MongoDB Compass: https://www.mongodb.com/products/compass
Connect to: mongodb://localhost:27017
Select database: pair_programming
Browse collections: rooms
View and edit documents directly

Using mongosh (CLI)
bash# Connect to MongoDB
mongosh

# Switch to database
use pair_programming

# List all rooms
db.rooms.find().pretty()

# Find specific room
db.rooms.findOne({room_id: "abc123ef"})

# Count rooms
db.rooms.countDocuments()

# Delete a room
db.rooms.deleteOne({room_id: "abc123ef"})
Using the Web Interface

Open Browser: Navigate to http://localhost:5173
Create Room: Click "Create New Room" button
Share Room ID: Copy the room ID from the URL (e.g., /room/abc123ef)
Join from Another Tab: Open a new tab, enter the room ID
Start Coding: Type in one tab and see updates in the other instantly
Test Autocomplete: Type def  and wait 600ms to see suggestions

🏗️ Architecture & Design Choices
Backend Architecture
Framework: FastAPI

Why? High performance, automatic API documentation, native async support, WebSocket support

Database: MongoDB with Motor (async driver)

Why?

Schema flexibility for rapid development
Native JSON/BSON storage (perfect for code snippets)
Excellent async Python support via Motor
Simple setup with no migrations needed
Horizontal scalability

Structure:

Routers: Handle HTTP endpoints and WebSocket connections
Services: Business logic layer (room management, autocomplete)
Models: Pydantic models for data validation
Schemas: Request/response validation

MongoDB vs PostgreSQL Benefits
FeatureMongoDBPostgreSQLSchemaFlexible, schemalessRigid, structuredSetupNo migrationsRequires Alembic migrationsData FormatNative JSON/BSONRelational tablesAsync SupportMotor (native)Limited async supportScalabilityHorizontal (sharding)Vertical mainlyCode StorageNatural fitRequires TEXT columns
Real-Time Synchronization
Strategy: Last-Write-Wins with WebSocket Broadcasting

Simple and effective for the prototype
Each code change is broadcast to all connected clients
ConnectionManager tracks active WebSocket connections per room
Automatic cleanup of disconnected clients

MongoDB Collections Schema
javascript// rooms collection
{
  _id: ObjectId("507f1f77bcf86cd799439011"),
  room_id: "abc123ef",  // Unique 8-char ID (indexed)
  code: "# Start coding...",
  language: "python",
  created_at: ISODate("2024-11-26T10:30:00Z"),
  updated_at: ISODate("2024-11-26T11:45:00Z")
}
Indexes:

room_id (unique)
created_at (for sorting/querying recent rooms)


🔌 API Endpoints
REST Endpoints
MethodEndpointDescriptionPOST/api/roomsCreate new roomGET/api/rooms/{room_id}Get room detailsPUT/api/rooms/{room_id}/codeUpdate room codePOST/api/autocompleteGet code suggestionsGET/healthHealth checkGET/docsInteractive API docs (Swagger)GET/redocAlternative API docs
WebSocket Endpoint

URL: ws://localhost:8000/ws/{room_id}
Message Types:

Receive on connect: {"type": "connected", "content": "Connected to room abc123ef", "timestamp": "..."}
Send: {"type": "code_update", "content": "...", "cursor_position": 0}
Receive: {"type": "user_joined", "content": "...", "user_count": 2}
Receive: {"type": "user_left", "content": "...", "user_count": 1}




🎯 Key Features Implemented
✅ Room Creation & Joining

Unique 8-character room IDs
No authentication required
URL-based room access

✅ Real-Time Collaboration

WebSocket-based synchronization
Instant code updates across clients
Connection state management
User count tracking

✅ AI Autocomplete (Mocked)

Pattern-based suggestions
Debounced API calls (600ms)
Confidence scoring
Language-specific patterns

✅ MongoDB Persistence

Async operations via Motor
Room state saved to database
Automatic indexing
No migrations needed

✅ Clean Code Architecture

Separation of concerns
Service layer pattern
Type-safe schemas (Pydantic)
Async/await throughout


🚧 Limitations & Known Issues

Last-Write-Wins: No conflict resolution for simultaneous edits
No User Authentication: Anyone with room ID can join
In-Memory WebSocket State: Connection tracking lost on restart
Simple Autocomplete: Pattern matching only, not true AI
No Code Execution: Editor only, cannot run code
No Cursor Synchronization: Can't see other users' cursors
No Chat Feature: No built-in communication
Limited Error Handling: Minimal retry logic for WebSocket


🔮 Future Improvements
Short-term (Next Sprint)

 Operational Transform (OT) or CRDT for conflict resolution
 User cursor position sharing
 Syntax highlighting language selection
 Room password protection
 Session timeout handling
 MongoDB replica set for high availability

Medium-term

 Integrate real AI model (OpenAI Codex, GitHub Copilot)
 Code execution sandbox
 Chat functionality
 File upload/download
 Multiple file support
 Git integration
 MongoDB Atlas (cloud) deployment

Long-term

 Video/audio chat
 User authentication & profiles
 Room analytics & history
 Code review features
 IDE plugins
 Mobile applications
 MongoDB sharding for scale
