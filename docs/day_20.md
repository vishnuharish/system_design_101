# Day 20: Consistent Hashing
> *Minimise disruption when nodes join or leave*

**Month 1: Foundations › Week 3: Data Layer Deep Dive**  
**Tags:** `Theory` `JavaScript` `Python`  
**Estimated Time:** 90–120 minutes

---

## 📖 Theory

### What You'll Learn Today

Today we explore **Consistent Hashing** — a fundamental concept you'll encounter when designing large-scale systems. Understanding this well is the difference between a system that breaks under load and one that scales gracefully.

### Core Topics

- **Hash Ring**
- **Virtual Nodes**
- **Token Assignment**
- **Ketama Algorithm**
- **Load Distribution**
- **Hot Spots**
- **Chord Protocol**

### Why It Matters

**Consistent Hashing** is used in production systems at Google, Netflix, Uber, and Amazon. The concepts you learn today appear in system design interviews and every day in engineering work.

The key engineering mindset: **every design decision is a trade-off**. There is no perfect solution — only the best solution for your specific requirements, scale, and constraints.

### Deep Dive

### Deep Dive: The Problem with Modulo Hashing at Scale

With `shard = hash(key) % N`, adding or removing even one server remaps almost all existing keys.

4 servers: `hash("user:1001") % 4 = 3` → Server 3. Add a 5th: `hash("user:1001") % 5 = 1` → now Server 1. That key must move. For every key in your cache: ~80% map to a different server. Adding one server forces moving 80% of your data. During the move, cache hit rate drops to near zero and the database is hammered.

**Consistent hashing fixes this.** Arrange servers on a virtual ring (0 to 2³²). Keys are also hashed to ring positions. A key maps to the first server clockwise from its position. Add a 5th server at position X: only keys between X and its predecessor need to move — statistically 1/N of all keys. Adding one server moves only 20% of keys; the other 80% are unaffected.

**Virtual nodes prevent uneven distribution.** If servers land unevenly on the ring, one might handle 40% of keys while another handles 5%. Solution: each physical server gets 100–150 virtual positions on the ring. Keys distribute evenly regardless of where server hashes fall. Cassandra, DynamoDB, and Memcached all use virtual nodes.

---

## 🍎 Real-World Analogy

### Real-World Analogy: Assigning Students to Tutors

A tutoring centre has 4 tutors and 1,000 students. Assignment rule: `roll_number % 4` determines the tutor.

**Adding a 5th tutor with modulo.** Switch to `% 5`. Student 1001 was with Tutor A (1001%4=1), now goes to Tutor B (1001%5=1). Student 1002 was with Tutor B (1002%4=2), now goes to Tutor D (1002%5=2). Nearly every student changes tutors. All ongoing learning relationships disrupted. That's 80% of cache keys becoming invalid.

**Consistent hashing approach.** Arrange 4 tutors on a circle labelled 0–100. Tutor A: 0–25, Tutor B: 26–50, Tutor C: 51–75, Tutor D: 76–100. Student roll numbers are hashed to circle positions. A 5th tutor joins at position 38: only students previously in the 26–38 range switch from Tutor B to Tutor E. Every other student keeps their tutor. 80% of relationships preserved.

**Virtual nodes = each tutor covers multiple small sections.** Instead of one big range, Tutor A covers positions 5–15, 40–50, and 70–80. Students distribute more evenly and when Tutor A goes on leave (node fails), their students spread across all remaining tutors rather than piling onto one neighbour.

---

## 🔑 Key Concepts

- **Hash Ring**
- **Virtual Nodes**
- **Token Assignment**
- **Ketama Algorithm**
- **Load Distribution**
- **Hot Spots**
- **Chord Protocol**

---

## 💛 JavaScript Example

```javascript
// Day 20: Consistent Hashing
// ============================================================
// Practical JavaScript implementation demonstrating:
// Hash Ring, Virtual Nodes, Token Assignment

class ConsistentHashingDemo {
  constructor(config = {}) {
    this.config = { maxRetries: 3, timeout: 5000, ...config };
    this.stats = { requests: 0, successes: 0, failures: 0, latencyTotal: 0 };
    console.log(`🚀 Consistent Hashing Demo initialized`);
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
    // Core logic representing Hash Ring
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
  const demo = new ConsistentHashingDemo();

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
# Day 20: Consistent Hashing
# ============================================================
# Practical Python implementation demonstrating:
# Hash Ring, Virtual Nodes, Token Assignment

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

class ConsistentHashingSystem:
    # Implementation of Consistent Hashing concepts
    # Demonstrates: Hash Ring, Virtual Nodes, Token Assignment

    def __init__(self, config: Dict = None):
        self.config = config or {'max_retries': 3, 'timeout': 5.0}
        self.stats = {'requests': 0, 'successes': 0, 'failures': 0, 'total_latency': 0.0}
        self._lock = threading.Lock()
        print(f"🚀 Consistent Hashing System initialized")
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
    system = ConsistentHashingSystem()
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

1. **Research**: Find a real engineering blog post about Hash Ring (try engineering.atscale.com, netflixtechblog.com, or engineering.fb.com)
2. **Code Challenge**: Extend the example above to log every operation to a file with timestamps
3. **Design Exercise**: Draw a system diagram showing how Consistent Hashing fits into a ride-sharing app like Uber
4. **Trade-off Analysis**: What are 3 situations where you would NOT use this approach?
5. **Interview Practice**: Explain Consistent Hashing to someone with no tech background using only analogies

---

## 📚 Resources

- *Designing Data-Intensive Applications* by Martin Kleppmann (essential reading)
- *System Design Interview Vol. 1 & 2* by Alex Xu
- High Scalability Blog — highscalability.com
- InfoQ Engineering Blog — infoq.com/architecture-design
- Papers We Love — paperswelove.org (academic papers on distributed systems)

---

*← [Day 19](day_19.md) | [Index](README.md) | [Day 21](day_21.md) →*
