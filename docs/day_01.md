# Day 01: Introduction to System Design
> *Why systems break and how engineers plan them*

**Month 1: Foundations › Week 1: Basics of System Design**  
**Tags:** `Theory` `Concepts`  
**Estimated Time:** 90–120 minutes

---

## 📖 Theory

## What is System Design?

**System Design** is the process of planning how a software application will work at a large scale — handling millions of users, storing huge amounts of data, and staying fast and reliable.

Think of it like designing a city. A small village doesn't need traffic lights or highways. But a big city needs roads, bridges, water systems, and electricity grids — all working together. Software is exactly the same.

### Why Does It Matter?

A simple app for 10 friends is easy to build. But building **Instagram**, **YouTube**, or **WhatsApp** for billions of people requires careful planning before writing a single line of code.

### Key Questions System Design Answers:
- How do we store millions of user photos?
- How do we handle 100,000 simultaneous users?
- What happens when one server crashes?
- How do we keep the app fast for users worldwide?

### The 4 Pillars of System Design:

| Pillar | Meaning | Real Example |
|--------|---------|-------------|
| **Scalability** | Handle more users | Netflix scales to 220M subscribers |
| **Reliability** | Stay up always | Google targets 99.99% uptime |
| **Performance** | Be fast | Search results in < 200ms |
| **Maintainability** | Easy to fix/update | Teams can ship daily |

### The System Design Process:
1. **Clarify Requirements** — What does the system need to do?
2. **Estimate Scale** — How many users? How much data?
3. **Define APIs** — What are the interfaces between components?
4. **Design Data Model** — What data needs to be stored and how?
5. **High-Level Architecture** — Draw the main components
6. **Deep Dive** — Detail the most critical/complex components
7. **Identify Bottlenecks** — What could break at scale?

---

### 🏫 Real-World Analogy: The School Canteen

Your school canteen works perfectly for 200 students. Now imagine feeding **200,000 students** at the same time.

You'd need:
- Multiple food stalls **(multiple servers)**
- Separate queues for different foods **(load balancing)**
- Backup cooks for when one is sick **(redundancy)**
- A huge storage room **(database)**
- A manager to direct people **(orchestration)**

**That's system design** — thinking through all the components you need before problems hit.

---

## 🔑 Key Concepts

- **Scalability**
- **Reliability**
- **Performance**
- **Maintainability**
- **High Availability**
- **Fault Tolerance**
- **Throughput**
- **Latency**

---

## 💛 JavaScript Example

```javascript
// Simulating system capacity and what happens at scale
class SimpleSystem {
  constructor(name, capacity) {
    this.name = name;
    this.capacity = capacity;  // max concurrent users
    this.currentUsers = 0;
    this.rejectedRequests = 0;
    this.totalRequests = 0;
  }

  handleRequest(userId) {
    this.totalRequests++;
    if (this.currentUsers >= this.capacity) {
      this.rejectedRequests++;
      console.log(`❌ [${this.name}] OVERLOADED! User ${userId} rejected.`);
      return false;
    }
    this.currentUsers++;
    console.log(`✅ [${this.name}] Serving User ${userId}. Load: ${this.currentUsers}/${this.capacity}`);

    // Simulate request processing (user leaves after 100ms)
    setTimeout(() => this.currentUsers--, 100);
    return true;
  }

  getStats() {
    return {
      name: this.name,
      capacity: this.capacity,
      successRate: `${((this.totalRequests - this.rejectedRequests) / this.totalRequests * 100).toFixed(1)}%`,
      rejections: this.rejectedRequests
    };
  }
}

// Small system — breaks quickly
const blogServer = new SimpleSystem("My Blog", 3);
for (let i = 1; i <= 7; i++) {
  blogServer.handleRequest(`user_${i}`);
}
console.log("\nStats:", blogServer.getStats());

// Solution: multiple servers (scale out)
const servers = ['Server-A', 'Server-B', 'Server-C'].map(
  name => new SimpleSystem(name, 3)
);
let currentServer = 0;

function handleWithLoadBalancing(userId) {
  // Round-robin across servers
  const server = servers[currentServer % servers.length];
  currentServer++;
  return server.handleRequest(userId);
}

console.log("\n--- With Load Balancing ---");
for (let i = 1; i <= 7; i++) {
  handleWithLoadBalancing(`user_${i}`);
}
```

---

## 🐍 Python Example

```python
# Simulating system capacity and what happens under load

import time
import threading

class SimpleSystem:
    def __init__(self, name: str, capacity: int):
        self.name = name
        self.capacity = capacity
        self.current_users = 0
        self.total_requests = 0
        self.rejected = 0
        self._lock = threading.Lock()

    def handle_request(self, user_id: str) -> bool:
        with self._lock:
            self.total_requests += 1
            if self.current_users >= self.capacity:
                self.rejected += 1
                print(f"❌ [{self.name}] OVERLOADED! {user_id} rejected.")
                return False
            self.current_users += 1

        load_pct = (self.current_users / self.capacity) * 100
        print(f"✅ [{self.name}] Serving {user_id}. "
              f"Load: {self.current_users}/{self.capacity} ({load_pct:.0f}%)")

        # Simulate request processing time
        time.sleep(0.1)

        with self._lock:
            self.current_users -= 1
        return True

    def get_stats(self) -> dict:
        success_rate = ((self.total_requests - self.rejected) /
                       max(self.total_requests, 1) * 100)
        return {
            'name': self.name,
            'capacity': self.capacity,
            'total_requests': self.total_requests,
            'rejected': self.rejected,
            'success_rate': f"{success_rate:.1f}%"
        }

# Single small server — breaks fast
print("=== Single Small Server ===")
blog = SimpleSystem("My Blog", 3)
threads = [threading.Thread(target=blog.handle_request, args=(f"user_{i}",))
           for i in range(1, 8)]
for t in threads:
    t.start()
for t in threads:
    t.join()

print(f"\nStats: {blog.get_stats()}")

# With multiple servers (horizontal scaling)
print("\n=== With Multiple Servers (Scale Out) ===")
servers = [SimpleSystem(f"Server-{c}", 3) for c in ['A', 'B', 'C']]
counter = 0

def load_balanced_request(user_id: str):
    global counter
    server = servers[counter % len(servers)]
    counter += 1
    return server.handle_request(user_id)

threads = [threading.Thread(target=load_balanced_request, args=(f"user_{i}",))
           for i in range(1, 8)]
for t in threads:
    t.start()
for t in threads:
    t.join()

for s in servers:
    print(f"Stats {s.name}: {s.get_stats()}")
```

---

## 📝 Homework

1. Draw a simple diagram of Instagram and identify all its main components
2. Write down 5 questions you would ask before designing a new social media app
3. Research: What does '99.99% uptime' mean in actual downtime minutes per year?
4. Find an engineering blog post from Netflix or Uber about a scaling challenge they solved

---

## 📚 Resources

- System Design Interview by Alex Xu (must read)
- Designing Data-Intensive Applications by Martin Kleppmann
- System Design Primer on GitHub — github.com/donnemartin/system-design-primer
- High Scalability Blog — highscalability.com

---

*← [Index](README.md) | [Index](README.md) | [Day 02](day_02.md) →*
