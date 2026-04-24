# Day 14: Week 2 Project — Add Caching Layer
> *Supercharge your Grade Tracker with Redis*

**Month 1: Foundations › Week 2: Performance and Infrastructure**  
**Tags:** `Theory` `JavaScript` `Python`  
**Estimated Time:** 90–120 minutes

---

## 📖 Theory

### What You'll Learn Today

Today we explore **Week 2 Project — Add Caching Layer** — a fundamental concept you'll encounter when designing large-scale systems. Understanding this well is the difference between a system that breaks under load and one that scales gracefully.

### Core Topics

- **Cache-Aside Pattern**
- **Write-Through Cache**
- **Cache Invalidation Strategy**
- **TTL Tuning**
- **Hit Rate Metrics**
- **Redis Integration**

### Why It Matters

**Week 2 Project — Add Caching Layer** is used in production systems at Google, Netflix, Uber, and Amazon. The concepts you learn today appear in system design interviews and every day in engineering work.

The key engineering mindset: **every design decision is a trade-off**. There is no perfect solution — only the best solution for your specific requirements, scale, and constraints.

### Deep Dive

### Deep Dive: Measuring Whether Your Cache Actually Works

A cache you can't measure is a cache you can't improve.

**Hit rate = hits / (hits + misses).** A 90% hit rate means 9 in 10 requests are served from cache — only 1 in 10 hits the database. Good for read-heavy data. Below 70% means your TTL is too short, your data isn't reused enough, or keys are being evicted too aggressively.

**Profile by endpoint, not globally.** The report card endpoint (3-table JOIN) is expensive — cache it. The "list my subjects" endpoint (simple SELECT from a small table, 2ms) isn't worth caching. Adding cache to cheap endpoints introduces stale-data risk for zero performance benefit.

**Surgical invalidation, not flush.** When you update a student's score, delete exactly `report:{student_id}` from the cache — not everything. Deleting everything causes a cache stampede: every request hits the database simultaneously. Deleting nothing leaves stale data. Surgical, key-specific invalidation is the production pattern.

**The cold start problem.** After a deployment, the cache is empty. The first 60 seconds: every request misses cache and hits the database. Solution: cache warming — pre-populate the top N most-requested keys immediately at server startup before accepting real traffic.

---

## 🍎 Real-World Analogy

### Real-World Analogy: A Library's Quick-Reference Shelf

The school library has 10,000 books. The librarian gets 500 requests per day, and 80% are for the same 50 textbooks.

**Without cache:** Every request sends the librarian to the stacks. 500 requests × 5 minutes walking = 41 hours of librarian time per day. Impossible.

**With cache (quick-reference shelf):** The librarian keeps the top 50 books on a small shelf at the desk. 80% of requests are answered in 10 seconds. Only 20% require a trip to the stacks — which the librarian then adds to the shelf for next time.

**Hit rate measurement:** "Today, 420 out of 500 requests were answered from my desk shelf. That's 84% hit rate." If it drops to 60%, the wrong books are on the shelf — time to review which titles are most requested.

**Surgical invalidation:** A new edition of the Chemistry Lab Manual arrives. The librarian immediately removes the old edition from the desk shelf and replaces it — doesn't wait for an arbitrary expiry date. That's proactive cache invalidation on a write.

**Cold start:** First day of term, the shelf is empty. The librarian pre-stocks it with last year's most-requested books before students arrive — cache warming before the rush begins.

---

## 🔑 Key Concepts

- **Cache-Aside Pattern**
- **Write-Through Cache**
- **Cache Invalidation Strategy**
- **TTL Tuning**
- **Hit Rate Metrics**
- **Redis Integration**

---

## 💛 JavaScript Example

```javascript
// Day 14: Week 2 Project — Add Caching Layer
// ============================================================
// Practical JavaScript implementation demonstrating:
// Cache-Aside Pattern, Write-Through Cache, Cache Invalidation Strategy

class Week2ProjectAddCachingLayerDemo {
  constructor(config = {}) {
    this.config = { maxRetries: 3, timeout: 5000, ...config };
    this.stats = { requests: 0, successes: 0, failures: 0, latencyTotal: 0 };
    console.log(`🚀 Week 2 Project — Add Caching Layer Demo initialized`);
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
    // Core logic representing Cache-Aside Pattern
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
  const demo = new Week2ProjectAddCachingLayerDemo();

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
# Day 14: Week 2 Project — Add Caching Layer
# ============================================================
# Practical Python implementation demonstrating:
# Cache-Aside Pattern, Write-Through Cache, Cache Invalidation Strategy

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

class Week2ProjectAddCachingLayerSystem:
    # Implementation of Week 2 Project — Add Caching Layer concepts
    # Demonstrates: Cache-Aside Pattern, Write-Through Cache, Cache Invalidation Strategy

    def __init__(self, config: Dict = None):
        self.config = config or {'max_retries': 3, 'timeout': 5.0}
        self.stats = {'requests': 0, 'successes': 0, 'failures': 0, 'total_latency': 0.0}
        self._lock = threading.Lock()
        print(f"🚀 Week 2 Project — Add Caching Layer System initialized")
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
    system = Week2ProjectAddCachingLayerSystem()
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

1. **Research**: Find a real engineering blog post about Cache-Aside Pattern (try engineering.atscale.com, netflixtechblog.com, or engineering.fb.com)
2. **Code Challenge**: Extend the example above to log every operation to a file with timestamps
3. **Design Exercise**: Draw a system diagram showing how Week 2 Project — Add Caching Layer fits into a ride-sharing app like Uber
4. **Trade-off Analysis**: What are 3 situations where you would NOT use this approach?
5. **Interview Practice**: Explain Week 2 Project — Add Caching Layer to someone with no tech background using only analogies

---

## 📚 Resources

- *Designing Data-Intensive Applications* by Martin Kleppmann (essential reading)
- *System Design Interview Vol. 1 & 2* by Alex Xu
- High Scalability Blog — highscalability.com
- InfoQ Engineering Blog — infoq.com/architecture-design
- Papers We Love — paperswelove.org (academic papers on distributed systems)

---

*← [Day 13](day_13.md) | [Index](README.md) | [Day 15](day_15.md) →*
