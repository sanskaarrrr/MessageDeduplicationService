# MsgDeDup — Message Deduplication Service

A full-stack message deduplication system that detects and ignores duplicate messages using SHA-256 hashing and MongoDB-style fast indexed lookups.

---

## 🏗 Tech Stack

| Layer | Technology | Purpose |
|---|---|---|
| Backend | Node.js + Express.js | RESTful API, async request handling |
| Database | MongoDB | Scalable hash-indexed storage |
| Hashing | SHA-256 (crypto module) | Unique message fingerprinting |
| API Testing | Postman | Endpoint validation |
| Frontend | HTML + CSS + JS | Interactive UI Dashboard |

---

## 📁 Project Structure

```
msg-dedup/
├── backend/
│   ├── server.js          # Express REST API with SHA-256 dedup logic
│   └── package.json
├── frontend/
│   └── index.html         # Full interactive dashboard
└── README.md
```

---

## 🚀 Running the Backend

```bash
cd backend
npm install
npm start
# Server runs on http://localhost:3001
```

---

## 🔌 API Endpoints

| Method | Endpoint | Description |
|---|---|---|
| POST | `/api/messages` | Submit a message (returns 201 new / 200 duplicate) |
| GET | `/api/messages` | List all unique stored messages |
| GET | `/api/messages/log` | Full activity log (last 100) |
| GET | `/api/stats` | Stats: total, unique, duplicates, rate |
| POST | `/api/hash` | Hash a message without storing |
| DELETE | `/api/messages` | Clear all data |

---

## 📡 Postman Testing Examples

### Submit a Message
```
POST http://localhost:3001/api/messages
Content-Type: application/json

{ "message": "Order #12345 placed successfully" }
```

**Response (new):**
```json
{
  "status": "accepted",
  "message": "Message accepted and stored.",
  "hash": "a3f5c...",
  "id": 1,
  "timestamp": "2025-01-01T10:00:00.000Z"
}
```

**Response (duplicate):**
```json
{
  "status": "duplicate",
  "message": "Duplicate message detected. Ignored.",
  "hash": "a3f5c...",
  "originalTimestamp": "2025-01-01T10:00:00.000Z",
  "occurrences": 2
}
```

### Get Stats
```
GET http://localhost:3001/api/stats
```
```json
{
  "totalReceived": 10,
  "totalUnique": 6,
  "totalDuplicates": 4,
  "deduplicationRate": "40.0"
}
```

---

## ⚙️ How It Works

1. **Message Received** — Client sends POST with `{ message: "..." }`
2. **SHA-256 Hash Generated** — Deterministic 64-char fingerprint created
3. **MongoDB Lookup** — Hash checked against indexed collection
4. **Decision Made**:
   - Hash not found → Store document, return `201 Created`
   - Hash found → Increment counter, return `200 OK` with duplicate notice

---

## 🌐 MongoDB Schema

```json
{
  "_id": "ObjectId(...)",
  "hash": "sha256-64-char-hex",
  "message": "original message text",
  "timestamp": "ISO 8601",
  "lastSeen": "ISO 8601",
  "count": 1
}
```

**Index:** `{ hash: 1 }` — unique, sparse — enables O(1) lookup performance.
