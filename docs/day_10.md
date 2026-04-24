# Day 10: CDN — Content Delivery Networks
> *Serve content from the nearest server globally*

**Month 1: Foundations › Week 2: Performance and Infrastructure**  
**Tags:** `Theory` `JavaScript` `Python`  
**Estimated Time:** 90–120 minutes

---

## 📖 Theory

### What You'll Learn Today

Today we explore **CDN — Content Delivery Networks** — a fundamental concept you'll encounter when designing large-scale systems. Understanding this well is the difference between a system that breaks under load and one that scales gracefully.

### Core Topics

- **Edge Nodes**
- **Origin Server**
- **Cache-Control Headers**
- **Cache Invalidation**
- **Anycast Routing**
- **Static vs Dynamic Content**
- **Cache Busting**

### Why It Matters

**CDN — Content Delivery Networks** is used in production systems at Google, Netflix, Uber, and Amazon. The concepts you learn today appear in system design interviews and every day in engineering work.

The key engineering mindset: **every design decision is a trade-off**. There is no perfect solution — only the best solution for your specific requirements, scale, and constraints.

### Deep Dive

### Deep Dive: The Physics Problem CDNs Solve

The speed of light in fibre optic cable is approximately 200,000 km/second. Mumbai to New York is 12,000 km — minimum round-trip: 120ms just from physics. Real routing adds more, typically 200–250ms.

A webpage with 60 asset requests (CSS, JS, images) from a Mumbai server for a New York user: 60 × 200ms = **12 seconds** to load. Unusable.

With Cloudflare's CDN, those assets are cached on a New Jersey edge server 50km from the user: 60 × 5ms = **300ms**. A 40× improvement from physics alone.

**Cache busting prevents serving stale files.** You cache `app.js` for 1 year. Three days later you fix a critical bug. The CDN serves the buggy version for another 362 days. Fix: rename the file using a content hash on every build — `app.a3f5b2c1.js`. New filename = CDN has never seen it = fetches fresh. Old users keep their cached copy until it naturally expires.

**Never cache personalised or private responses on CDN.** `GET /api/me`, shopping carts, and private messages must always have `Cache-Control: private, no-store`. If a CDN caches User A's profile, User B requesting the same URL gets User A's data — a serious security incident.

---

## 🍎 Real-World Analogy

### Real-World Analogy: Zomato's Dark Store Network

When Zomato Instant promises 10-minute grocery delivery, they don't ship from one central warehouse. They stock small "dark stores" — micro-warehouses stocked with fast-moving items — within 2km of dense residential areas.

**Your origin server = the main Zomato warehouse.** Has everything, but is far away.

**CDN edge nodes = neighbourhood dark stores.** Copies of the most popular items stocked locally. Your order is fulfilled from the nearest dark store — not the central warehouse.

**Cache hit = item in stock at the dark store.** Your request for a product image, CSS file, or JS bundle is served from the nearest edge node in milliseconds.

**Cache miss = item not in the local store.** First visitor from Chennai requests a rarely-accessed file. Chennai edge node doesn't have it — fetches from origin, serves the user, caches it locally for all future Chennai requests.

**TTL = perishable vs non-perishable items.** Fresh bread has a 1-day shelf life (short TTL). Rice sits for months (long TTL, like hashed JS bundles). The dark store clears stale bread without waiting for someone to request it.

**Cache busting = a new product code.** When a new improved product replaces an old one, it gets a new product code. The dark store stocks the new code fresh — the old code is simply no longer ordered. CDN cache busting works identically.

---

## 🔑 Key Concepts

- **Edge Nodes**
- **Origin Server**
- **Cache-Control Headers**
- **Cache Invalidation**
- **Anycast Routing**
- **Static vs Dynamic Content**
- **Cache Busting**

---

## 💛 JavaScript Example

```javascript
// Day 10: CDN — Content Delivery Networks
// ============================================================
// Practical JavaScript implementation demonstrating:
// Edge Nodes, Origin Server, Cache-Control Headers

class CDNContentDeliveryNetworksDemo {
  constructor(config = {}) {
    this.config = { maxRetries: 3, timeout: 5000, ...config };
    this.stats = { requests: 0, successes: 0, failures: 0, latencyTotal: 0 };
    console.log(`🚀 CDN — Content Delivery Networks Demo initialized`);
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
    // Core logic representing Edge Nodes
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
  const demo = new CDNContentDeliveryNetworksDemo();

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
# Day 10: CDN — Content Delivery Networks
# ============================================================
# Practical Python implementation demonstrating:
# Edge Nodes, Origin Server, Cache-Control Headers

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

class CDNContentDeliveryNetworksSystem:
    # Implementation of CDN — Content Delivery Networks concepts
    # Demonstrates: Edge Nodes, Origin Server, Cache-Control Headers

    def __init__(self, config: Dict = None):
        self.config = config or {'max_retries': 3, 'timeout': 5.0}
        self.stats = {'requests': 0, 'successes': 0, 'failures': 0, 'total_latency': 0.0}
        self._lock = threading.Lock()
        print(f"🚀 CDN — Content Delivery Networks System initialized")
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
    system = CDNContentDeliveryNetworksSystem()
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

1. **Research**: Find a real engineering blog post about Edge Nodes (try engineering.atscale.com, netflixtechblog.com, or engineering.fb.com)
2. **Code Challenge**: Extend the example above to log every operation to a file with timestamps
3. **Design Exercise**: Draw a system diagram showing how CDN — Content Delivery Networks fits into a ride-sharing app like Uber
4. **Trade-off Analysis**: What are 3 situations where you would NOT use this approach?
5. **Interview Practice**: Explain CDN — Content Delivery Networks to someone with no tech background using only analogies

---

## 📚 Resources

- *Designing Data-Intensive Applications* by Martin Kleppmann (essential reading)
- *System Design Interview Vol. 1 & 2* by Alex Xu
- High Scalability Blog — highscalability.com
- InfoQ Engineering Blog — infoq.com/architecture-design
- Papers We Love — paperswelove.org (academic papers on distributed systems)

---

*← [Day 09](day_09.md) | [Index](README.md) | [Day 11](day_11.md) →*
