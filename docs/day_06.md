# Day 06: NoSQL Databases
> *Flexible storage for modern high-scale apps*

**Month 1: Foundations › Week 1: Basics of System Design**  
**Tags:** `Theory` `JavaScript` `Python`  
**Estimated Time:** 90–120 minutes

---

## 📖 Theory

### What You'll Learn Today

Today we explore **NoSQL Databases** — a fundamental concept you'll encounter when designing large-scale systems. Understanding this well is the difference between a system that breaks under load and one that scales gracefully.

### Core Topics

- **Document Store**
- **Key-Value Store**
- **Column-Family**
- **Graph Database**
- **Schema-less Design**
- **Horizontal Scaling**
- **Eventual Consistency**

### Why It Matters

**NoSQL Databases** is used in production systems at Google, Netflix, Uber, and Amazon. The concepts you learn today appear in system design interviews and every day in engineering work.

The key engineering mindset: **every design decision is a trade-off**. There is no perfect solution — only the best solution for your specific requirements, scale, and constraints.

### Deep Dive

### Deep Dive: Four NoSQL Models, Four Different Problems

**Document Stores (MongoDB)** store JSON-like objects with no fixed schema. One user document can have 3 hobbies; another might have none. Used by Uber for trip data and eBay for product listings — every product has different attributes.

**Key-Value Stores (Redis, DynamoDB)** are pure dictionaries. Given a key, return the value in sub-millisecond time. Perfect for session storage, feature flags, and rate-limiting counters. If your query is always "give me the thing for key X", key-value is the right model.

**Column-Family Stores (Cassandra)** are optimised for massive write throughput and time-ordered queries. All writes are sequential — 100x faster than random disk I/O. Netflix uses Cassandra to store viewing history for 230 million users: billions of rows, simple queries by user ID and timestamp.

**Graph Databases (Neo4j)** store nodes and edges. "Who are friends-of-friends of User 5 who also like cricket?" is recursive in SQL but a natural traversal in a graph database. Used by LinkedIn for professional connections and fraud detection systems for finding rings of suspicious accounts.

**Never switch to NoSQL because it's fashionable.** Switch because your specific access pattern demands it: flexible schema, extreme write throughput, graph traversals, or pure key lookups at huge scale.

---

## 🍎 Real-World Analogy

### Real-World Analogy: Four Storage Systems in a School

**Document Store = Student Files (flexible folders).** Priya's folder has an allergy certificate, a sports award, and 3 medical forms. Arjun's has only academic records. No two student folders have the same structure — and that's perfectly fine. Document databases work the same way.

**Key-Value Store = Staff ID Card System.** The security guard looks up "ID-4521" and instantly gets the staff member's access level. Pure lookup by key, no searching through names or descriptions. Redis works identically: `GET user:session:abc123` returns the session in under 1ms.

**Column-Family = Attendance Register.** The register records every student's attendance every period. To find "all absences in March for Grade 10", you scan only the date and status columns — skipping name, address, and parent contact columns. Cassandra's columnar storage makes this column-specific scan extremely fast.

**Graph Database = Contact Tracing Map.** A student tests positive for a contagious illness. "Who sat next to them? Who shared lab equipment? Who was in the same group?" You need to trace relationships between people, not look up records by key. A graph database traverses these connections instantly.

---

## 🔑 Key Concepts

- **Document Store**
- **Key-Value Store**
- **Column-Family**
- **Graph Database**
- **Schema-less Design**
- **Horizontal Scaling**
- **Eventual Consistency**

---

## 💛 JavaScript Example

```javascript
// Day 06: NoSQL Databases
// ============================================================
// Practical JavaScript implementation demonstrating:
// Document Store, Key-Value Store, Column-Family

class NoSQLDatabasesDemo {
  constructor(config = {}) {
    this.config = { maxRetries: 3, timeout: 5000, ...config };
    this.stats = { requests: 0, successes: 0, failures: 0, latencyTotal: 0 };
    console.log(`🚀 NoSQL Databases Demo initialized`);
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
    // Core logic representing Document Store
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
  const demo = new NoSQLDatabasesDemo();

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
# Day 06: NoSQL Databases
# ============================================================
# Practical Python implementation demonstrating:
# Document Store, Key-Value Store, Column-Family

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

class NoSQLDatabasesSystem:
    # Implementation of NoSQL Databases concepts
    # Demonstrates: Document Store, Key-Value Store, Column-Family

    def __init__(self, config: Dict = None):
        self.config = config or {'max_retries': 3, 'timeout': 5.0}
        self.stats = {'requests': 0, 'successes': 0, 'failures': 0, 'total_latency': 0.0}
        self._lock = threading.Lock()
        print(f"🚀 NoSQL Databases System initialized")
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
    system = NoSQLDatabasesSystem()
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

1. **Research**: Find a real engineering blog post about Document Store (try engineering.atscale.com, netflixtechblog.com, or engineering.fb.com)
2. **Code Challenge**: Extend the example above to log every operation to a file with timestamps
3. **Design Exercise**: Draw a system diagram showing how NoSQL Databases fits into a ride-sharing app like Uber
4. **Trade-off Analysis**: What are 3 situations where you would NOT use this approach?
5. **Interview Practice**: Explain NoSQL Databases to someone with no tech background using only analogies

---

## 📚 Resources

- *Designing Data-Intensive Applications* by Martin Kleppmann (essential reading)
- *System Design Interview Vol. 1 & 2* by Alex Xu
- High Scalability Blog — highscalability.com
- InfoQ Engineering Blog — infoq.com/architecture-design
- Papers We Love — paperswelove.org (academic papers on distributed systems)

---

*← [Day 05](day_05.md) | [Index](README.md) | [Day 07](day_07.md) →*
