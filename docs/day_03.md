# Day 03: APIs & HTTP Methods
> *The universal language connecting all systems*

**Month 1: Foundations › Week 1: Basics of System Design**  
**Tags:** `Theory` `JavaScript` `Python`  
**Estimated Time:** 90–120 minutes

---

## 📖 Theory

## APIs and HTTP

### What is an API?

An **API (Application Programming Interface)** is a contract between two systems that defines how they can communicate. It specifies:
- What operations are available
- What data format to use
- What authentication is required
- What responses to expect

### HTTP Methods (CRUD Operations)

| Method | Operation | SQL Equivalent | Use Case |
|--------|-----------|---------------|---------|
| **GET** | Read | SELECT | Fetch a user profile |
| **POST** | Create | INSERT | Create a new post |
| **PUT** | Replace | UPDATE (whole) | Replace entire profile |
| **PATCH** | Update | UPDATE (partial) | Change just the email |
| **DELETE** | Delete | DELETE | Remove a post |

### HTTP Status Codes — The Server's Reply

**2xx — Success**
- `200 OK` — Request succeeded
- `201 Created` — New resource created
- `204 No Content` — Success, nothing to return

**4xx — Client Error**
- `400 Bad Request` — Invalid data sent
- `401 Unauthorized` — Not logged in
- `403 Forbidden` — Logged in but no permission
- `404 Not Found` — Resource doesn't exist
- `429 Too Many Requests` — Rate limited

**5xx — Server Error**
- `500 Internal Server Error` — Server crashed
- `503 Service Unavailable` — Server overloaded

### REST API Design Principles

1. **Stateless** — Each request contains all needed information
2. **Resource-based URLs** — `/users/1/posts` not `/getUserPosts?id=1`
3. **Use HTTP verbs correctly** — GET to read, POST to create
4. **Consistent response format** — Always return JSON with same structure
5. **Version your API** — `/v1/users` so old clients don't break

---

### 📺 TV Remote Analogy

Your TV remote is an API:
- It defines **what buttons you can press** (available endpoints)
- Each button does one specific thing (resource + method)
- You don't know HOW the TV processes it internally
- You just use the defined interface

APIs work the same way. When you use Google Maps to get directions, you send a request to their API and get directions back — without knowing how their routing algorithm works internally.

---

## 🔑 Key Concepts

- **REST API**
- **HTTP Methods**
- **Status Codes**
- **Endpoints**
- **Request Headers**
- **Response Body**
- **JSON**
- **API Versioning**

---

## 💛 JavaScript Example

```javascript
// Complete CRUD REST API with Express.js
const express = require('express');
const app = express();
app.use(express.json());

// In-memory store with auto-increment IDs
let posts = new Map();
let nextId = 1;

// Helper: standardised response format
const success = (data, status = 200) => ({ success: true, data });
const error = (msg, status = 400) => ({ success: false, error: msg });

// GET /posts — list all (with optional search)
app.get('/posts', (req, res) => {
  const { author, page = 1, limit = 10 } = req.query;
  let result = Array.from(posts.values());

  if (author) result = result.filter(p => p.author === author);

  // Pagination
  const start = (page - 1) * limit;
  const paginated = result.slice(start, start + parseInt(limit));

  res.json({ success: true, data: paginated, total: result.length, page: +page });
});

// GET /posts/:id — get one post
app.get('/posts/:id', (req, res) => {
  const post = posts.get(parseInt(req.params.id));
  if (!post) return res.status(404).json(error('Post not found'));
  res.json(success(post));
});

// POST /posts — create new post
app.post('/posts', (req, res) => {
  const { title, body, author } = req.body;
  if (!title) return res.status(400).json(error('Title is required'));
  if (!author) return res.status(400).json(error('Author is required'));

  const post = { id: nextId++, title, body: body || '', author, createdAt: new Date().toISOString() };
  posts.set(post.id, post);
  res.status(201).json(success(post));
});

// PATCH /posts/:id — partial update
app.patch('/posts/:id', (req, res) => {
  const id = parseInt(req.params.id);
  if (!posts.has(id)) return res.status(404).json(error('Post not found'));

  const post = posts.get(id);
  const updated = { ...post, ...req.body, id, updatedAt: new Date().toISOString() };
  posts.set(id, updated);
  res.json(success(updated));
});

// DELETE /posts/:id
app.delete('/posts/:id', (req, res) => {
  const id = parseInt(req.params.id);
  if (!posts.delete(id)) return res.status(404).json(error('Post not found'));
  res.status(204).send(); // No content
});

app.listen(3000, () => console.log('🚀 Posts API running on :3000'));
```

---

## 🐍 Python Example

```python
from flask import Flask, jsonify, request
from datetime import datetime
from typing import Optional
import math

app = Flask(__name__)

# Storage and auto-increment
posts: dict = {}
next_id: int = 1

def success(data, status=200):
    return jsonify({'success': True, 'data': data}), status

def error(msg, status=400):
    return jsonify({'success': False, 'error': msg}), status

# GET /api/v1/posts — list with pagination + filter
@app.route('/api/v1/posts', methods=['GET'])
def list_posts():
    author = request.args.get('author')
    page   = int(request.args.get('page', 1))
    limit  = int(request.args.get('limit', 10))

    result = list(posts.values())
    if author:
        result = [p for p in result if p['author'] == author]

    total_pages = math.ceil(len(result) / limit)
    start = (page - 1) * limit
    paginated = result[start: start + limit]

    return jsonify({
        'success': True, 'data': paginated,
        'meta': {'total': len(result), 'page': page, 'total_pages': total_pages}
    })

# GET /api/v1/posts/<id>
@app.route('/api/v1/posts/<int:post_id>', methods=['GET'])
def get_post(post_id):
    post = posts.get(post_id)
    if not post:
        return error(f'Post {post_id} not found', 404)
    return success(post)

# POST /api/v1/posts
@app.route('/api/v1/posts', methods=['POST'])
def create_post():
    global next_id
    data = request.json or {}
    if not data.get('title'):
        return error('title is required')
    if not data.get('author'):
        return error('author is required')

    post = {
        'id': next_id,
        'title': data['title'],
        'body': data.get('body', ''),
        'author': data['author'],
        'created_at': datetime.utcnow().isoformat()
    }
    posts[next_id] = post
    next_id += 1
    return success(post, 201)

# PATCH /api/v1/posts/<id> — partial update
@app.route('/api/v1/posts/<int:post_id>', methods=['PATCH'])
def update_post(post_id):
    if post_id not in posts:
        return error(f'Post {post_id} not found', 404)
    data = request.json or {}
    posts[post_id].update({**data, 'updated_at': datetime.utcnow().isoformat()})
    return success(posts[post_id])

# DELETE /api/v1/posts/<id>
@app.route('/api/v1/posts/<int:post_id>', methods=['DELETE'])
def delete_post(post_id):
    if post_id not in posts:
        return error(f'Post {post_id} not found', 404)
    del posts[post_id]
    return jsonify({}), 204

if __name__ == '__main__':
    app.run(debug=True)
```

---

## 📝 Homework

1. Design REST API endpoints for a school management system (students, teachers, classes, grades)
2. What status code would you return if a user tries to access another user's private data?
3. Install Postman and test a public API like https://jsonplaceholder.typicode.com
4. Research: What is the difference between REST and SOAP? Why did REST win?

---

## 📚 Resources

- REST API Design Best Practices — restfulapi.net
- HTTP Status Codes Reference — httpstatuses.com
- Postman Learning Center — learning.postman.com
- OpenAPI Specification — swagger.io/specification

---

*← [Day 02](day_02.md) | [Index](README.md) | [Day 04](day_04.md) →*
