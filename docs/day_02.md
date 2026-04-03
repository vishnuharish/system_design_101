# Day 02: Client-Server Architecture
> *How your browser talks to the internet*

**Month 1: Foundations › Week 1: Basics of System Design**  
**Tags:** `Theory` `JavaScript` `Python`  
**Estimated Time:** 90–120 minutes

---

## 📖 Theory

## Client-Server Architecture

Every app you use has two fundamental parts:

**Client** — Your device (browser, mobile app, desktop app). It makes requests and displays results.

**Server** — A powerful computer somewhere that stores data, runs business logic, and sends responses.

### The Request-Response Cycle

When you open YouTube:
1. Browser (client) sends: *"Give me the homepage"*
2. YouTube's server receives the HTTP request
3. Server queries its databases for trending videos
4. Server assembles the response (HTML + video metadata)
5. Browser renders and displays the page

This happens in **milliseconds** for millions of simultaneous users.

### Types of Servers

| Type | Purpose | Technology |
|------|---------|------------|
| **Web Server** | Serves HTML/CSS/JS | Nginx, Apache |
| **App Server** | Business logic | Node.js, Django, Spring |
| **Database Server** | Store and query data | PostgreSQL, MongoDB |
| **Cache Server** | Fast temporary store | Redis, Memcached |
| **File Server** | Store files/media | AWS S3 |

### Communication Protocols

- **HTTP/HTTPS** — Most common, stateless, request-response
- **WebSocket** — Persistent, bidirectional connection
- **gRPC** — Fast binary protocol, great for internal services
- **TCP/IP** — The underlying foundation of the internet

### Stateless vs Stateful

**Stateless (HTTP)**: Each request is independent. Server doesn't remember previous requests. Scales well.

**Stateful (WebSocket)**: Server maintains connection state per client. Harder to scale but enables real-time features.

---

### 🍕 Pizza Delivery Analogy

You (the **client**) call a pizza shop (the **server**) and order a pizza:

- **Your call** = HTTP Request
- **The order details** = Request body/payload
- **Pizza being made** = Server processing
- **Pizza delivered** = HTTP Response
- **Pizza in your hands** = Data rendered in your browser

You don't bake it yourself — you request it from the server. The server doesn't know who you are without your address (stateless — you provide all context with each request).

---

## 🔑 Key Concepts

- **HTTP Request/Response**
- **Client**
- **Server**
- **Protocol**
- **Stateless**
- **Stateful**
- **TCP/IP**
- **DNS Resolution**

---

## 💛 JavaScript Example

```javascript
// Client-side: Making HTTP requests from browser/Node.js
async function fetchUserProfile(userId) {
  try {
    const response = await fetch(`https://api.example.com/users/${userId}`, {
      method: 'GET',
      headers: {
        'Authorization': 'Bearer my_token_here',
        'Accept': 'application/json'
      }
    });

    // Check HTTP status code
    if (response.status === 404) throw new Error('User not found');
    if (!response.ok) throw new Error(`HTTP Error: ${response.status}`);

    const user = await response.json();
    console.log(`✅ User: ${user.name} from ${user.city}`);
    return user;

  } catch (error) {
    console.error(`❌ Failed: ${error.message}`);
    throw error;
  }
}

// Server-side: Handling requests (Node.js + Express)
const express = require('express');
const app = express();
app.use(express.json());

// In-memory users (replace with DB in production)
const users = new Map([
  [1, { id: 1, name: 'Priya', city: 'Mumbai', age: 15 }],
  [2, { id: 2, name: 'Arjun', city: 'Chennai', age: 16 }]
]);

app.get('/users/:id', (req, res) => {
  const id = parseInt(req.params.id);
  const user = users.get(id);

  if (!user) {
    return res.status(404).json({ error: 'User not found' });
  }

  console.log(`📤 Sending user ${id}: ${user.name}`);
  res.status(200).json(user);
});

// Start server
app.listen(3000, () => {
  console.log('🚀 Server is listening on port 3000');
  console.log('   Try: GET http://localhost:3000/users/1');
});

// Test: fetchUserProfile(1) → { id:1, name:'Priya', city:'Mumbai' }
```

---

## 🐍 Python Example

```python
# Client-Server demo with Flask (server) and requests (client)
from flask import Flask, jsonify, request, abort
from dataclasses import dataclass, asdict
import requests  # pip install requests

# ── Server ──────────────────────────────────────────────────
app = Flask(__name__)

@dataclass
class User:
    id: int
    name: str
    city: str
    age: int

# In-memory storage (use a real DB in production)
users_db = {
    1: User(1, "Priya", "Mumbai", 15),
    2: User(2, "Arjun", "Chennai", 16),
    3: User(3, "Kavya", "Bangalore", 15),
}

@app.route('/users', methods=['GET'])
def list_users():
    # Support ?city=Mumbai filter
    city_filter = request.args.get('city')
    result = list(users_db.values())
    if city_filter:
        result = [u for u in result if u.city == city_filter]
    return jsonify([asdict(u) for u in result])

@app.route('/users/<int:user_id>', methods=['GET'])
def get_user(user_id):
    user = users_db.get(user_id)
    if not user:
        return jsonify({'error': f'User {user_id} not found'}), 404
    print(f"📤 Serving user {user_id}: {user.name}")
    return jsonify(asdict(user))

@app.route('/users', methods=['POST'])
def create_user():
    data = request.json
    new_id = max(users_db.keys()) + 1
    user = User(new_id, data['name'], data['city'], data['age'])
    users_db[new_id] = user
    return jsonify(asdict(user)), 201

# ── Client ──────────────────────────────────────────────────
def run_client_demo():
    base = 'http://localhost:5000'

    # GET — fetch a user
    res = requests.get(f'{base}/users/1')
    print(f"GET user 1: {res.json()}")

    # GET with filter
    res = requests.get(f'{base}/users?city=Mumbai')
    print(f"GET Mumbai users: {res.json()}")

    # POST — create user
    new_user = {'name': 'Ravi', 'city': 'Delhi', 'age': 17}
    res = requests.post(f'{base}/users', json=new_user)
    print(f"POST new user: {res.status_code} {res.json()}")

if __name__ == '__main__':
    # Start server: python server.py
    # Then run client_demo() separately
    app.run(debug=True, port=5000)
```

---

## 📝 Homework

1. Draw the complete request-response cycle for loading instagram.com
2. Open Chrome DevTools (F12 → Network tab), visit any website, and describe what you see
3. What is the difference between HTTP and HTTPS? Why does the 'S' matter?
4. Research: What is a 'load balancer'? How does it relate to client-server architecture?

---

## 📚 Resources

- MDN Web Docs — HTTP Overview (developer.mozilla.org/en-US/docs/Web/HTTP/Overview)
- How the Web Works — The Odin Project (theodinproject.com)
- HTTP: The Definitive Guide by David Gourley
- Flask Quickstart — flask.palletsprojects.com/quickstart

---

*← [Day 01](day_01.md) | [Index](README.md) | [Day 03](day_03.md) →*
