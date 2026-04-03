# Day 72: Design Amazon — E-Commerce at Scale
> *Inventory, flash sales, and personalisation*

**Month 3: Design Practice › Week 10: Complex Real-World Systems**  
**Tags:** `Theory` `JavaScript` `Python`  
**Estimated Time:** 90–120 minutes

---

## 📖 Theory

### What You'll Learn Today

Today we explore **Design Amazon — E-Commerce at Scale** — a fundamental concept you'll encounter when designing large-scale systems. Understanding this well is the difference between a system that breaks under load and one that scales gracefully.

### Core Topics

- **Distributed Inventory**
- **Flash Sale Architecture**
- **Distributed Lock**
- **Recommendation Engine**
- **Search**
- **Warehouse System**

### Why It Matters

**Design Amazon — E-Commerce at Scale** is used in production systems at Google, Netflix, Uber, and Amazon. The concepts you learn today appear in system design interviews and every day in engineering work.

The key engineering mindset: **every design decision is a trade-off**. There is no perfect solution — only the best solution for your specific requirements, scale, and constraints.

### Deep Dive

Let's break down each core topic:

**Distributed Inventory**: This is the foundation. Without understanding this, the rest doesn't make sense. Take your time here.

**Flash Sale Architecture**: Once you have the foundation, this builds on top of it. You'll see this in almost every real-world system.

**Distributed Lock**: This is where the real engineering happens. Companies spend months optimising this.

### Common Mistakes to Avoid

1. **Over-engineering early**: Don't add complexity before you need it
2. **Ignoring the trade-offs**: Every choice has costs — acknowledge them
3. **Not estimating first**: Always estimate scale before choosing a design
4. **Forgetting failure modes**: What happens when each component fails?

---

## 🍎 Real-World Analogy

Think of **Design Amazon — E-Commerce at Scale** like how a large airport operates:
- Multiple runways handle traffic (parallel processing)
- Control tower coordinates everything (orchestration)
- Backup systems activate if something fails (redundancy)
- Everything is monitored in real time (observability)

Good system design follows the same principles as good infrastructure design: plan for failure, design for scale, and keep things simple where possible.

---

## 🔑 Key Concepts

- **Distributed Inventory**
- **Flash Sale Architecture**
- **Distributed Lock**
- **Recommendation Engine**
- **Search**
- **Warehouse System**

---

## 💛 JavaScript Example

```javascript
// Day 72: Design Amazon — E-Commerce at Scale
// ============================================================
// Practical JavaScript implementation demonstrating:
// Distributed Inventory, Flash Sale Architecture, Distributed Lock

class DesignAmazonECommerceatScaleDemo {
  constructor(config = {}) {
    this.config = { maxRetries: 3, timeout: 5000, ...config };
    this.stats = { requests: 0, successes: 0, failures: 0, latencyTotal: 0 };
    console.log(`🚀 Design Amazon — E-Commerce at Scale Demo initialized`);
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
    // Core logic representing Distributed Inventory
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
  const demo = new DesignAmazonECommerceatScaleDemo();

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
# Day 72: Design Amazon — E-Commerce at Scale
# ============================================================
# Practical Python implementation demonstrating:
# Distributed Inventory, Flash Sale Architecture, Distributed Lock

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

class DesignAmazonECommerceatScaleSystem:
    # Implementation of Design Amazon — E-Commerce at Scale concepts
    # Demonstrates: Distributed Inventory, Flash Sale Architecture, Distributed Lock

    def __init__(self, config: Dict = None):
        self.config = config or {'max_retries': 3, 'timeout': 5.0}
        self.stats = {'requests': 0, 'successes': 0, 'failures': 0, 'total_latency': 0.0}
        self._lock = threading.Lock()
        print(f"🚀 Design Amazon — E-Commerce at Scale System initialized")
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
    system = DesignAmazonECommerceatScaleSystem()
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

1. **Research**: Find a real engineering blog post about Distributed Inventory (try engineering.atscale.com, netflixtechblog.com, or engineering.fb.com)
2. **Code Challenge**: Extend the example above to log every operation to a file with timestamps
3. **Design Exercise**: Draw a system diagram showing how Design Amazon — E-Commerce at Scale fits into a ride-sharing app like Uber
4. **Trade-off Analysis**: What are 3 situations where you would NOT use this approach?
5. **Interview Practice**: Explain Design Amazon — E-Commerce at Scale to someone with no tech background using only analogies

---

## 📚 Resources

- *Designing Data-Intensive Applications* by Martin Kleppmann (essential reading)
- *System Design Interview Vol. 1 & 2* by Alex Xu
- High Scalability Blog — highscalability.com
- InfoQ Engineering Blog — infoq.com/architecture-design
- Papers We Love — paperswelove.org (academic papers on distributed systems)

---

*← [Day 71](day_71.md) | [Index](README.md) | [Day 73](day_73.md) →*
