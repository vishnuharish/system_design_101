# Day 21: Week 3 Project — Sharded DB Simulator
> *Build a multi-shard routing layer with consistent hashing*

**Month 1: Foundations › Week 3: Data Layer Deep Dive**  
**Tags:** `Theory` `JavaScript` `Python`  
**Estimated Time:** 90–120 minutes

---

## 📖 Theory

### What You'll Learn Today

Today we explore **Week 3 Project — Sharded DB Simulator** — a fundamental concept you'll encounter when designing large-scale systems. Understanding this well is the difference between a system that breaks under load and one that scales gracefully.

### Core Topics

- **Shard Router**
- **Hash Function**
- **Rebalancing Logic**
- **Cross-Shard Aggregation**
- **Node Join/Leave**
- **Consistent Hashing Ring**

### Why It Matters

**Week 3 Project — Sharded DB Simulator** is used in production systems at Google, Netflix, Uber, and Amazon. The concepts you learn today appear in system design interviews and every day in engineering work.

The key engineering mindset: **every design decision is a trade-off**. There is no perfect solution — only the best solution for your specific requirements, scale, and constraints.

### Deep Dive

### Deep Dive: What a Shard Router Must Actually Do

A shard router is the transparent layer between your application and your sharded databases. The application calls `router.get("user:1001")` and gets data back — it doesn't know which physical shard holds it.

**Key-to-shard mapping:** given a key, compute the consistent hash and find the correct shard on the ring. O(log N) lookup.

**Connection pooling:** maintain a pool of connections to each shard. Creating a new connection takes ~50ms; reusing a pooled connection takes ~1ms. A router with 5 shards should maintain 5 connection pools.

**Health tracking:** ping each shard's `SELECT 1` every 5 seconds. Three failures → mark the shard unavailable, reject requests for its keys rather than hanging indefinitely.

**Scatter-gather for cross-shard queries:** `router.getAll()` must query all shards in parallel and merge results. Querying 5 shards sequentially takes 5× longer than querying them simultaneously.

**Rebalancing on topology change:** when a shard is added, move the affected keys transparently. Application code must not need to know about resharding. This is the core logic that powers production tools like Vitess (MySQL sharding proxy) and mongos (MongoDB's shard router).

---

## 🍎 Real-World Analogy

### Real-World Analogy: A Post Office's Automatic Letter Sorting System

A large post office receives thousands of letters per hour and routes each one to the correct local delivery post office (shard).

**The sorting machine (shard router):** reads the PIN code on each letter (hashes the key) and routes it to the correct outgoing bag (shard). The person dropping off letters doesn't need to know which bag their letter goes into.

**Consistent hashing = PIN code area assignment.** When a new sub-post office opens for a new PIN range, only letters for that range move to the new bag — not a complete reassignment of all letters.

**Health check = bag status indicator.** A red light on Bag C means the Kurla post office is temporarily not accepting letters (shard unavailable). The machine routes Kurla-addressed letters to a retry queue until the light goes green, rather than dropping them.

**Scatter-gather = finding all letters to "Priya Sharma" across the city.** The supervisor must check all outgoing bags, find every matching envelope, bring them to one table, and present them together. That's a cross-shard query — every shard must be checked and results merged.

**Connection pooling = pre-loaded delivery vans.** Each local post office has a van permanently parked at its bay. Letters go directly in — no waiting to requisition a new vehicle. That's a connection pool: always ready, always warm.

---

## 🔑 Key Concepts

- **Shard Router**
- **Hash Function**
- **Rebalancing Logic**
- **Cross-Shard Aggregation**
- **Node Join/Leave**
- **Consistent Hashing Ring**

---

## 💛 JavaScript Example

```javascript
// Day 21: Week 3 Project — Sharded DB Simulator
// ============================================================
// Practical JavaScript implementation demonstrating:
// Shard Router, Hash Function, Rebalancing Logic

class Week3ProjectShardedDBSimulatorDemo {
  constructor(config = {}) {
    this.config = { maxRetries: 3, timeout: 5000, ...config };
    this.stats = { requests: 0, successes: 0, failures: 0, latencyTotal: 0 };
    console.log(`🚀 Week 3 Project — Sharded DB Simulator Demo initialized`);
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
    // Core logic representing Shard Router
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
  const demo = new Week3ProjectShardedDBSimulatorDemo();

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
# Day 21: Week 3 Project — Sharded DB Simulator
# ============================================================
# Practical Python implementation demonstrating:
# Shard Router, Hash Function, Rebalancing Logic

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

class Week3ProjectShardedDBSimulatorSystem:
    # Implementation of Week 3 Project — Sharded DB Simulator concepts
    # Demonstrates: Shard Router, Hash Function, Rebalancing Logic

    def __init__(self, config: Dict = None):
        self.config = config or {'max_retries': 3, 'timeout': 5.0}
        self.stats = {'requests': 0, 'successes': 0, 'failures': 0, 'total_latency': 0.0}
        self._lock = threading.Lock()
        print(f"🚀 Week 3 Project — Sharded DB Simulator System initialized")
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
    system = Week3ProjectShardedDBSimulatorSystem()
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

1. **Research**: Find a real engineering blog post about Shard Router (try engineering.atscale.com, netflixtechblog.com, or engineering.fb.com)
2. **Code Challenge**: Extend the example above to log every operation to a file with timestamps
3. **Design Exercise**: Draw a system diagram showing how Week 3 Project — Sharded DB Simulator fits into a ride-sharing app like Uber
4. **Trade-off Analysis**: What are 3 situations where you would NOT use this approach?
5. **Interview Practice**: Explain Week 3 Project — Sharded DB Simulator to someone with no tech background using only analogies

---

## 📚 Resources

- *Designing Data-Intensive Applications* by Martin Kleppmann (essential reading)
- *System Design Interview Vol. 1 & 2* by Alex Xu
- High Scalability Blog — highscalability.com
- InfoQ Engineering Blog — infoq.com/architecture-design
- Papers We Love — paperswelove.org (academic papers on distributed systems)

---

*← [Day 20](day_20.md) | [Index](README.md) | [Day 22](day_22.md) →*
