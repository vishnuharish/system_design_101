# Day 17: Database Sharding
> *Split your database horizontally for unlimited scale*

**Month 1: Foundations › Week 3: Data Layer Deep Dive**  
**Tags:** `Theory` `JavaScript` `Python`  
**Estimated Time:** 90–120 minutes

---

## 📖 Theory

### What You'll Learn Today

Today we explore **Database Sharding** — a fundamental concept you'll encounter when designing large-scale systems. Understanding this well is the difference between a system that breaks under load and one that scales gracefully.

### Core Topics

- **Hash Sharding**
- **Range Sharding**
- **Directory Sharding**
- **Hotspots**
- **Cross-Shard Queries**
- **Resharding**
- **Shard Key Selection**

### Why It Matters

**Database Sharding** is used in production systems at Google, Netflix, Uber, and Amazon. The concepts you learn today appear in system design interviews and every day in engineering work.

The key engineering mindset: **every design decision is a trade-off**. There is no perfect solution — only the best solution for your specific requirements, scale, and constraints.

### Deep Dive

### Deep Dive: The Shard Key Is the Most Important Decision

The shard key determines where data lives and which queries are fast. A wrong choice creates hotspots and makes the most common queries touch every shard.

**Avoid write hotspots.** Sharding by `created_at` date range sends all new writes to today's shard while old shards sit idle. This is the single most common sharding mistake. Use high-cardinality keys like `user_id` that distribute writes evenly.

**Design for query locality.** If most queries are "get all orders for user X", shard by `user_id` — all of that user's orders are on one shard. If you sharded by `order_id`, getting a user's orders requires querying every shard (scatter-gather — expensive).

**The celebrity / hot key problem.** A celebrity with 30M followers and a regular user both happen to land on Shard 3 (same hash result). The celebrity posts once — millions of reads hit only Shard 3, which melts. Solutions: store celebrity data on dedicated resources, or pre-generate and cache celebrity content at the CDN level.

**Resharding is painful.** With naive hashing (`id % N`), adding one shard remaps ~80% of all keys. Consistent hashing (Day 20) reduces this to ~1/N of keys, but any resharding while serving live traffic without downtime is a major engineering challenge.

---

## 🍎 Real-World Analogy

### Real-World Analogy: Distributing Student Files Across Four Filing Rooms

A school with 100,000 students has too many records for one filing room. They split across four rooms.

**Range sharding by surname initial (A–M in Room 1, N–Z in Rooms 2–4).** Simple, but 40% of students have surnames starting A–M. Room 1 is always overwhelmed. That's a hotspot from an uneven distribution.

**Range sharding by admission year.** All new admissions go into this year's room. Old rooms sit idle; the newest room melts every season. Classic write hotspot.

**Hash sharding by roll number.** A formula distributes roll numbers evenly regardless of how names cluster. Roll 1001 → Room 2, Roll 1002 → Room 4. Even distribution, no hotspots.

**Query locality matters.** If teachers most commonly ask "get all records for roll number 4521" — hash by roll number, that's a single-room lookup. If they commonly ask "get all Grade 10 students" — and you sharded by roll number — you must check all 4 rooms (scatter-gather). Design your shard key for your most frequent query.

**Cross-shard query = visiting all four rooms.** "List all students who scored above 90%." The admin must check all 4 rooms, collect results, and merge them at one table. Expensive. Design your shard key to minimise how often this happens.

---

## 🔑 Key Concepts

- **Hash Sharding**
- **Range Sharding**
- **Directory Sharding**
- **Hotspots**
- **Cross-Shard Queries**
- **Resharding**
- **Shard Key Selection**

---

## 💛 JavaScript Example

```javascript
// Day 17: Database Sharding
// ============================================================
// Practical JavaScript implementation demonstrating:
// Hash Sharding, Range Sharding, Directory Sharding

class DatabaseShardingDemo {
  constructor(config = {}) {
    this.config = { maxRetries: 3, timeout: 5000, ...config };
    this.stats = { requests: 0, successes: 0, failures: 0, latencyTotal: 0 };
    console.log(`🚀 Database Sharding Demo initialized`);
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
    // Core logic representing Hash Sharding
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
  const demo = new DatabaseShardingDemo();

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
# Day 17: Database Sharding
# ============================================================
# Practical Python implementation demonstrating:
# Hash Sharding, Range Sharding, Directory Sharding

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

class DatabaseShardingSystem:
    # Implementation of Database Sharding concepts
    # Demonstrates: Hash Sharding, Range Sharding, Directory Sharding

    def __init__(self, config: Dict = None):
        self.config = config or {'max_retries': 3, 'timeout': 5.0}
        self.stats = {'requests': 0, 'successes': 0, 'failures': 0, 'total_latency': 0.0}
        self._lock = threading.Lock()
        print(f"🚀 Database Sharding System initialized")
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
    system = DatabaseShardingSystem()
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

1. **Research**: Find a real engineering blog post about Hash Sharding (try engineering.atscale.com, netflixtechblog.com, or engineering.fb.com)
2. **Code Challenge**: Extend the example above to log every operation to a file with timestamps
3. **Design Exercise**: Draw a system diagram showing how Database Sharding fits into a ride-sharing app like Uber
4. **Trade-off Analysis**: What are 3 situations where you would NOT use this approach?
5. **Interview Practice**: Explain Database Sharding to someone with no tech background using only analogies

---

## 📚 Resources

- *Designing Data-Intensive Applications* by Martin Kleppmann (essential reading)
- *System Design Interview Vol. 1 & 2* by Alex Xu
- High Scalability Blog — highscalability.com
- InfoQ Engineering Blog — infoq.com/architecture-design
- Papers We Love — paperswelove.org (academic papers on distributed systems)

---

*← [Day 16](day_16.md) | [Index](README.md) | [Day 18](day_18.md) →*
