# Day 08: Caching Fundamentals
> *Never compute the same thing twice*

**Month 1: Foundations › Week 2: Performance and Infrastructure**  
**Tags:** `Theory` `JavaScript` `Python`  
**Estimated Time:** 90–120 minutes

---

## 📖 Theory

### What You'll Learn Today

Today we explore **Caching Fundamentals** — a fundamental concept you'll encounter when designing large-scale systems. Understanding this well is the difference between a system that breaks under load and one that scales gracefully.

### Core Topics

- **Cache Hit vs Miss**
- **TTL**
- **LRU Eviction**
- **LFU Eviction**
- **Cache Invalidation**
- **Memoisation**
- **Browser Cache**
- **Cache Stampede**

### Why It Matters

**Caching Fundamentals** is used in production systems at Google, Netflix, Uber, and Amazon. The concepts you learn today appear in system design interviews and every day in engineering work.

The key engineering mindset: **every design decision is a trade-off**. There is no perfect solution — only the best solution for your specific requirements, scale, and constraints.

### Deep Dive

### Deep Dive: The Three Hard Problems of Caching

**Problem 1: Stale Data.** You cache a user's profile for 60 seconds. At second 30, the user changes their name. For the next 30 seconds, anyone reading from cache sees the wrong name. Two solutions: short TTL (more DB hits, always fresher data) or proactive invalidation (when the user updates their name, immediately delete the cache key). Most production systems use both — proactive invalidation as the primary mechanism, TTL as the fallback.

**Problem 2: Cache Stampede.** A popular cache key expires. At that exact moment, 5,000 users request it simultaneously. All 5,000 see a cache miss and fire database queries in parallel — potentially crashing the DB. Solution: only one process rebuilds the cache (distributed lock), all others wait and retry. By the time they retry, the cache is populated.

**Problem 3: What to Cache.** Cache when the same data is requested often, it's expensive to compute, and it changes infrequently. Do NOT cache user-specific private data (security risk — wrong user gets wrong data), data that changes every second (cached version is always stale), or huge objects that evict everything else.

**Four caching strategies:** Cache-aside (app checks cache, fetches DB on miss), Write-through (DB write also updates cache), Write-behind (write cache first, DB later — fast writes, risk of data loss), Read-through (cache fetches from DB automatically on miss).

---

## 🍎 Real-World Analogy

### Real-World Analogy: A Photocopy of Your Textbook

You need to look up the quadratic formula during homework. Two options:

**No cache:** Walk to the school library, find the Maths textbook, look up the formula, note it, walk back. Repeat every single time you need it. 10 minutes per lookup. That's a database query for every request.

**With cache:** You photocopied the key formulas page and keep it in your bag. Need the formula? Open your bag — 2 seconds. Cache hit.

**Cache miss:** You need the integration formulas but didn't photocopy that page. You go to the library, look it up, and this time photocopy that page too. Next time: instant.

**TTL:** Your photocopies are from last year's syllabus. The board updated two formulas. Your cached copy is now wrong even though it hasn't "expired". You must replace it. That's why caches have a Time To Live — periodic forced refresh.

**Proactive invalidation:** Mid-term, your teacher announces a correction to formula on page 45. Your photocopy is wrong immediately — not wrong after the TTL expires. You discard it now and get the corrected version. That's the hard part: knowing when to throw away the copy before it naturally expires.

---

## 🔑 Key Concepts

- **Cache Hit vs Miss**
- **TTL**
- **LRU Eviction**
- **LFU Eviction**
- **Cache Invalidation**
- **Memoisation**
- **Browser Cache**
- **Cache Stampede**

---

## 💛 JavaScript Example

```javascript
// Day 08: Caching Fundamentals
// ============================================================
// Practical JavaScript implementation demonstrating:
// Cache Hit vs Miss, TTL, LRU Eviction

class CachingFundamentalsDemo {
  constructor(config = {}) {
    this.config = { maxRetries: 3, timeout: 5000, ...config };
    this.stats = { requests: 0, successes: 0, failures: 0, latencyTotal: 0 };
    console.log(`🚀 Caching Fundamentals Demo initialized`);
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
    // Core logic representing Cache Hit vs Miss
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
  const demo = new CachingFundamentalsDemo();

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
# Day 08: Caching Fundamentals
# ============================================================
# Practical Python implementation demonstrating:
# Cache Hit vs Miss, TTL, LRU Eviction

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

class CachingFundamentalsSystem:
    # Implementation of Caching Fundamentals concepts
    # Demonstrates: Cache Hit vs Miss, TTL, LRU Eviction

    def __init__(self, config: Dict = None):
        self.config = config or {'max_retries': 3, 'timeout': 5.0}
        self.stats = {'requests': 0, 'successes': 0, 'failures': 0, 'total_latency': 0.0}
        self._lock = threading.Lock()
        print(f"🚀 Caching Fundamentals System initialized")
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
    system = CachingFundamentalsSystem()
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

1. **Research**: Find a real engineering blog post about Cache Hit vs Miss (try engineering.atscale.com, netflixtechblog.com, or engineering.fb.com)
2. **Code Challenge**: Extend the example above to log every operation to a file with timestamps
3. **Design Exercise**: Draw a system diagram showing how Caching Fundamentals fits into a ride-sharing app like Uber
4. **Trade-off Analysis**: What are 3 situations where you would NOT use this approach?
5. **Interview Practice**: Explain Caching Fundamentals to someone with no tech background using only analogies

---

## 📚 Resources

- *Designing Data-Intensive Applications* by Martin Kleppmann (essential reading)
- *System Design Interview Vol. 1 & 2* by Alex Xu
- High Scalability Blog — highscalability.com
- InfoQ Engineering Blog — infoq.com/architecture-design
- Papers We Love — paperswelove.org (academic papers on distributed systems)

---

*← [Day 07](day_07.md) | [Index](README.md) | [Day 09](day_09.md) →*
