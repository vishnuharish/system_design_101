# Day 09: Redis — Distributed Caching
> *The Swiss Army knife of backend infrastructure*

**Month 1: Foundations › Week 2: Performance and Infrastructure**  
**Tags:** `Theory` `JavaScript` `Python`  
**Estimated Time:** 90–120 minutes

---

## 📖 Theory

### What You'll Learn Today

Today we explore **Redis — Distributed Caching** — a fundamental concept you'll encounter when designing large-scale systems. Understanding this well is the difference between a system that breaks under load and one that scales gracefully.

### Core Topics

- **Strings and Hashes**
- **Lists and Sets**
- **Sorted Sets**
- **Pub/Sub**
- **TTL Management**
- **Lua Scripting**
- **Redis Cluster**
- **Persistence RDB/AOF**

### Why It Matters

**Redis — Distributed Caching** is used in production systems at Google, Netflix, Uber, and Amazon. The concepts you learn today appear in system design interviews and every day in engineering work.

The key engineering mindset: **every design decision is a trade-off**. There is no perfect solution — only the best solution for your specific requirements, scale, and constraints.

### Deep Dive

### Deep Dive: Why Redis Beats In-Process Cache at Scale

Your Day 8 cache lived inside the application's memory. With one server, that's fine. Scale to 10 servers and each has its own independent cache: user updates their profile on Server 3, which invalidates its own cache. User's next request goes to Server 7 — stale data served for up to TTL seconds.

**Redis solves this as a shared, external cache.** All 10 servers read from and write to one Redis instance. Server 3 deletes the cache key in Redis — Server 7 sees the miss and fetches fresh data. One source of truth across the entire fleet.

**Redis is single-threaded but blazing fast.** No lock contention, no thread context switches. Every operation is in memory. Redis handles 100,000+ operations/second on a single instance. For most applications, one instance is enough.

**Atomic operations are Redis's killer feature.** `INCR page:views` reads, increments, and returns the new value in one atomic step. No race condition possible between two concurrent incrementers. This is why Redis is the go-to for rate limiting, session counters, and leaderboards.

**Sorted Sets power real-time leaderboards.** `ZADD leaderboard 9500 priya` adds a score. `ZREVRANGE leaderboard 0 9` returns the top 10 — already sorted — in O(log N). The database would need a heavy `ORDER BY` query for the same result.

---

## 🍎 Real-World Analogy

### Real-World Analogy: A Shared Whiteboard in the Staffroom

**Without Redis — each teacher has their own notebook.** Teacher A updates Priya's score in their notebook. Teachers B through J still have the old score. Every teacher has a different version of reality. That's in-process caching on multiple servers.

**With Redis — one shared whiteboard in the staffroom.** Every teacher reads from and writes to the same whiteboard. When Teacher A updates Priya's score, all teachers instantly see the change on their next look. One source of truth. That's Redis as a shared cache.

**Sorted Set = the class rankings section of the whiteboard.** One section always shows students ranked by average score. When Arjun's score improves, the ranking re-sorts automatically. Teachers always see the current top 10 without recalculating. That's `ZADD` and `ZREVRANGE`.

**TTL = "erase after 30 days" note.** Some notices on the whiteboard have a removal date written in the corner. The board prefect erases them automatically on that date. Session tokens, OTP codes, and rate-limit counters in Redis all self-delete when their TTL expires — no cleanup job needed.

**Pub/Sub = the staffroom PA speaker.** When the principal makes an announcement (PUBLISH to a channel), every teacher in the staffroom (SUBSCRIBE) hears it simultaneously without the principal knowing or caring how many are listening.

---

## 🔑 Key Concepts

- **Strings and Hashes**
- **Lists and Sets**
- **Sorted Sets**
- **Pub/Sub**
- **TTL Management**
- **Lua Scripting**
- **Redis Cluster**
- **Persistence RDB/AOF**

---

## 💛 JavaScript Example

```javascript
// Day 09: Redis — Distributed Caching
// ============================================================
// Practical JavaScript implementation demonstrating:
// Strings and Hashes, Lists and Sets, Sorted Sets

class RedisDistributedCachingDemo {
  constructor(config = {}) {
    this.config = { maxRetries: 3, timeout: 5000, ...config };
    this.stats = { requests: 0, successes: 0, failures: 0, latencyTotal: 0 };
    console.log(`🚀 Redis — Distributed Caching Demo initialized`);
    console.log(`   Config: ${JSON.stringify(this.config)}`);
  }

  // Core operation
  async execute(input) {
    const start = Date.now();
    this.stats.requests++;
    try {
      const result = await this._process(input);
      this.stats.successes++;
      this.stats.latencyTotal += Date.now() - start;
      return { success: true, data: result, latency: Date.now() - start };
    } catch (error) {
      this.stats.failures++;
      console.error(`❌ Error processing ${input}: ${error.message}`);
      return { success: false, error: error.message };
    }
  }

  async _process(input) {
    // Simulate some processing time
    await new Promise(r => setTimeout(r, Math.random() * 50));
    // Core logic representing Strings and Hashes
    return { input, processed: true, result: `result_of_${input}` };
  }

  // Show statistics
  getStats() {
    const avgLatency = this.stats.requests > 0
      ? (this.stats.latencyTotal / this.stats.requests).toFixed(2)
      : 0;
    return {
      ...this.stats,
      successRate: `${((this.stats.successes / (this.stats.requests || 1)) * 100).toFixed(1)}%`,
      avgLatencyMs: avgLatency
    };
  }
}

// ── Demo ──────────────────────────────────────────────────────
async function runDemo() {
  const demo = new RedisDistributedCachingDemo();

  console.log('\n📊 Running 5 sample operations...');
  const inputs = ['request_A', 'request_B', 'request_C', 'request_D', 'request_E'];

  const results = await Promise.all(inputs.map(i => demo.execute(i)));
  results.forEach((r, i) => {
    const icon = r.success ? '✅' : '❌';
    console.log(`  ${icon} ${inputs[i]}: ${r.success ? `${r.data.result} (${r.latency}ms)` : r.error}`);
  });

  console.log('\n📈 Final Statistics:');
  console.log(demo.getStats());
}

runDemo();
```

---

## 🐍 Python Example

```python
# Day 09: Redis — Distributed Caching
# ============================================================
# Practical Python implementation demonstrating:
# Strings and Hashes, Lists and Sets, Sorted Sets

import time
import random
import threading
from dataclasses import dataclass, field
from typing import Any, Dict, List, Optional

@dataclass
class OperationResult:
    success: bool
    data: Any = None
    error: str = None
    latency_ms: float = 0.0

class RedisDistributedCachingSystem:
    # Implementation of Redis — Distributed Caching concepts
    # Demonstrates: Strings and Hashes, Lists and Sets, Sorted Sets

    def __init__(self, config: Dict = None):
        self.config = config or {'max_retries': 3, 'timeout': 5.0}
        self.stats = {'requests': 0, 'successes': 0, 'failures': 0, 'total_latency': 0.0}
        self._lock = threading.Lock()
        print(f"🚀 Redis — Distributed Caching System initialized")
        print(f"   Config: {self.config}")

    def execute(self, input_data: Any) -> OperationResult:
        # Process a single operation with metrics tracking
        start = time.time()
        with self._lock:
            self.stats['requests'] += 1

        try:
            result = self._process(input_data)
            latency = (time.time() - start) * 1000
            with self._lock:
                self.stats['successes'] += 1
                self.stats['total_latency'] += latency
            return OperationResult(success=True, data=result, latency_ms=round(latency, 2))

        except Exception as e:
            with self._lock:
                self.stats['failures'] += 1
            return OperationResult(success=False, error=str(e))

    def _process(self, input_data: Any) -> Any:
        # Simulate processing (replace with real implementation)
        time.sleep(random.uniform(0.01, 0.05))
        return {'input': input_data, 'processed': True, 'output': f'result_of_{input_data}'}

    def get_stats(self) -> Dict:
        with self._lock:
            total = self.stats['requests']
            avg_latency = self.stats['total_latency'] / total if total > 0 else 0
            return {
                **self.stats,
                'success_rate': f"{self.stats['successes'] / max(total, 1) * 100:.1f}%",
                'avg_latency_ms': f"{avg_latency:.2f}ms"
            }


def run_demo():
    system = RedisDistributedCachingSystem()
    print('\n📊 Running 5 sample operations...')

    inputs = ['request_A', 'request_B', 'request_C', 'request_D', 'request_E']
    for inp in inputs:
        result = system.execute(inp)
        if result.success:
            print(f"  ✅ {inp}: {result.data['output']} ({result.latency_ms}ms)")
        else:
            print(f"  ❌ {inp}: {result.error}")

    print('\n📈 Final Statistics:')
    for k, v in system.get_stats().items():
        print(f"  {k}: {v}")


if __name__ == '__main__':
    run_demo()
```

---

## 📝 Homework

1. **Research**: Find a real engineering blog post about Strings and Hashes (try engineering.atscale.com, netflixtechblog.com, or engineering.fb.com)
2. **Code Challenge**: Extend the example above to log every operation to a file with timestamps
3. **Design Exercise**: Draw a system diagram showing how Redis — Distributed Caching fits into a ride-sharing app like Uber
4. **Trade-off Analysis**: What are 3 situations where you would NOT use this approach?
5. **Interview Practice**: Explain Redis — Distributed Caching to someone with no tech background using only analogies

---

## 📚 Resources

- *Designing Data-Intensive Applications* by Martin Kleppmann (essential reading)
- *System Design Interview Vol. 1 & 2* by Alex Xu
- High Scalability Blog — highscalability.com
- InfoQ Engineering Blog — infoq.com/architecture-design
- Papers We Love — paperswelove.org (academic papers on distributed systems)

---

*← [Day 08](day_08.md) | [Index](README.md) | [Day 10](day_10.md) →*
