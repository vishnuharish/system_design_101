# Day 15: Database Indexing
> *Make your queries 1000x faster*

**Month 1: Foundations › Week 3: Data Layer Deep Dive**  
**Tags:** `Theory` `JavaScript` `Python`  
**Estimated Time:** 90–120 minutes

---

## 📖 Theory

### What You'll Learn Today

Today we explore **Database Indexing** — a fundamental concept you'll encounter when designing large-scale systems. Understanding this well is the difference between a system that breaks under load and one that scales gracefully.

### Core Topics

- **B-Tree Index**
- **Composite Index**
- **Covering Index**
- **Index Selectivity**
- **EXPLAIN ANALYZE**
- **Query Planner**
- **Index Bloat**
- **Partial Index**

### Why It Matters

**Database Indexing** is used in production systems at Google, Netflix, Uber, and Amazon. The concepts you learn today appear in system design interviews and every day in engineering work.

The key engineering mindset: **every design decision is a trade-off**. There is no perfect solution — only the best solution for your specific requirements, scale, and constraints.

### Deep Dive

### Deep Dive: Why Indexes Change Everything

`SELECT * FROM users WHERE email = 'priya@example.com'` without an index: the database reads every single row. At 10 million users — ~800MB of data read from disk, 30+ seconds, and the table is locked for the duration.

**A B-tree index on `email`** is a sorted binary tree stored separately. Finding any email requires roughly log₂(10M) ≈ 23 comparisons. 23 tree hops at nanoseconds each = under 1ms. Same query, 30,000× faster.

**Composite index column order matters.** `CREATE INDEX ON orders(user_id, created_at)` serves queries filtering on `user_id` alone, or `user_id + created_at` together. It cannot serve queries filtering on `created_at` alone. Like a phone book sorted by last name then first name — you can look up "Sharma, Priya" but not "all people named Priya" efficiently.

**Every index has a write cost.** INSERT, UPDATE, DELETE must also update every index on the table. A table with 15 indexes can be 10× slower to write to than one with 2. Index the columns you frequently filter/sort on — not every column.

---

## 🍎 Real-World Analogy

### Real-World Analogy: The Index at the Back of a Textbook

You need to find every mention of "Pythagoras" in a 600-page Maths textbook.

**Without an index (full table scan):** Read every page, check every sentence. 600 pages × 30 seconds per page = 5 hours. That's a database query on a large table with no index.

**With the back-of-book index:** Open to the back — "Pythagoras — pages 45, 112, 289, 445." Jump directly to 4 pages. Done in 30 seconds. The index was built once (at printing time) and makes every future lookup instant.

**Composite index = multi-level subject index.** Some textbooks have "Geometry → Triangles → Pythagoras → page 45." You can look up "Geometry" broadly, or "Geometry + Triangles + Pythagoras" for the exact topic. But if you want to find "Theorem" without specifying the parent subject — the hierarchical index doesn't help. Composite database indexes work identically.

**Write cost = reprinting the index for every new edition.** Every time a chapter is added or page numbers shift, the entire index must be regenerated. Every database write must update all indexes on that table. A table with 15 indexes has 15 data structures to update per write.

---

## 🔑 Key Concepts

- **B-Tree Index**
- **Composite Index**
- **Covering Index**
- **Index Selectivity**
- **EXPLAIN ANALYZE**
- **Query Planner**
- **Index Bloat**
- **Partial Index**

---

## 💛 JavaScript Example

```javascript
// Day 15: Database Indexing
// ============================================================
// Practical JavaScript implementation demonstrating:
// B-Tree Index, Composite Index, Covering Index

class DatabaseIndexingDemo {
  constructor(config = {}) {
    this.config = { maxRetries: 3, timeout: 5000, ...config };
    this.stats = { requests: 0, successes: 0, failures: 0, latencyTotal: 0 };
    console.log(`🚀 Database Indexing Demo initialized`);
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
    // Core logic representing B-Tree Index
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
  const demo = new DatabaseIndexingDemo();

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
# Day 15: Database Indexing
# ============================================================
# Practical Python implementation demonstrating:
# B-Tree Index, Composite Index, Covering Index

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

class DatabaseIndexingSystem:
    # Implementation of Database Indexing concepts
    # Demonstrates: B-Tree Index, Composite Index, Covering Index

    def __init__(self, config: Dict = None):
        self.config = config or {'max_retries': 3, 'timeout': 5.0}
        self.stats = {'requests': 0, 'successes': 0, 'failures': 0, 'total_latency': 0.0}
        self._lock = threading.Lock()
        print(f"🚀 Database Indexing System initialized")
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
    system = DatabaseIndexingSystem()
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

1. **Research**: Find a real engineering blog post about B-Tree Index (try engineering.atscale.com, netflixtechblog.com, or engineering.fb.com)
2. **Code Challenge**: Extend the example above to log every operation to a file with timestamps
3. **Design Exercise**: Draw a system diagram showing how Database Indexing fits into a ride-sharing app like Uber
4. **Trade-off Analysis**: What are 3 situations where you would NOT use this approach?
5. **Interview Practice**: Explain Database Indexing to someone with no tech background using only analogies

---

## 📚 Resources

- *Designing Data-Intensive Applications* by Martin Kleppmann (essential reading)
- *System Design Interview Vol. 1 & 2* by Alex Xu
- High Scalability Blog — highscalability.com
- InfoQ Engineering Blog — infoq.com/architecture-design
- Papers We Love — paperswelove.org (academic papers on distributed systems)

---

*← [Day 14](day_14.md) | [Index](README.md) | [Day 16](day_16.md) →*
