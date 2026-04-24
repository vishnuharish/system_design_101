# Day 24: Publish-Subscribe Pattern
> *Decoupled event broadcasting at massive scale*

**Month 1: Foundations › Week 4: Communication Patterns**  
**Tags:** `Theory` `JavaScript` `Python`  
**Estimated Time:** 90–120 minutes

---

## 📖 Theory

### What You'll Learn Today

Today we explore **Publish-Subscribe Pattern** — a fundamental concept you'll encounter when designing large-scale systems. Understanding this well is the difference between a system that breaks under load and one that scales gracefully.

### Core Topics

- **Topics**
- **Subscribers**
- **Event Fan-out**
- **Message Filtering**
- **Kafka Topics**
- **Consumer Groups**
- **Event Ordering**
- **Backpressure**

### Why It Matters

**Publish-Subscribe Pattern** is used in production systems at Google, Netflix, Uber, and Amazon. The concepts you learn today appear in system design interviews and every day in engineering work.

The key engineering mindset: **every design decision is a trade-off**. There is no perfect solution — only the best solution for your specific requirements, scale, and constraints.

### Deep Dive

### Deep Dive: Pub/Sub vs Message Queue — The Critical Difference

In a **message queue**: one producer puts a message in; one consumer takes it out. The message is consumed exactly once. Use for work queues — tasks done once by one worker.

In **pub/sub**: one producer publishes to a topic; every subscriber receives their own copy. The message is broadcast to N consumers simultaneously. Use when multiple independent systems react to the same event.

**Kafka combines both with consumer groups.** Within a consumer group: each message goes to exactly one consumer (queue behaviour — for parallelism). Across different consumer groups: each group gets its own copy (pub/sub — for fan-out). Example: `order.created` event published → Email service (group "email") sends confirmation → Inventory (group "inventory") decrements stock → Analytics (group "analytics") records the conversion. Same event, three independent groups, each acting independently.

**Kafka retains messages** even after consumption — configurable for days or weeks. Consumers track their own position (offset). A new service can replay all past events to rebuild its state. A traditional queue deletes messages once consumed.

---

## 🍎 Real-World Analogy

### Real-World Analogy: School Homework Box vs Morning Assembly PA

**Message queue = homework submission box.** One student puts homework in, one teacher takes it out and grades it. Each piece is processed once and removed. If three teachers are grading, each takes a different submission — parallel processing, but each piece handled by exactly one teacher.

**Pub/Sub = morning assembly PA system.** The principal speaks into the microphone (publishes to "All School Announcements"). Every classroom speaker (subscriber) receives the same announcement simultaneously. The principal doesn't know or care how many classrooms are listening. The announcement isn't "consumed" — all 40 classrooms hear it.

**Kafka consumer groups = multiple noticeboard types.** The same announcement goes to: the Sports Club noticeboard (consumer group "sports"), the Academic Club noticeboard (consumer group "academics"), and the Library noticeboard (consumer group "library"). Same event, three independent groups, each doing something different with it.

**Message retention = recording the morning assembly.** Unlike a live-only PA (traditional queue), Kafka records every announcement. A teacher who was absent today can play back this morning's session on return. A new service can replay all past events to build its state — like catching up on missed assembly recordings.

---

## 🔑 Key Concepts

- **Topics**
- **Subscribers**
- **Event Fan-out**
- **Message Filtering**
- **Kafka Topics**
- **Consumer Groups**
- **Event Ordering**
- **Backpressure**

---

## 💛 JavaScript Example

```javascript
// Day 24: Publish-Subscribe Pattern
// ============================================================
// Practical JavaScript implementation demonstrating:
// Topics, Subscribers, Event Fan-out

class PublishSubscribePatternDemo {
  constructor(config = {}) {
    this.config = { maxRetries: 3, timeout: 5000, ...config };
    this.stats = { requests: 0, successes: 0, failures: 0, latencyTotal: 0 };
    console.log(`🚀 Publish-Subscribe Pattern Demo initialized`);
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
    // Core logic representing Topics
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
  const demo = new PublishSubscribePatternDemo();

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
# Day 24: Publish-Subscribe Pattern
# ============================================================
# Practical Python implementation demonstrating:
# Topics, Subscribers, Event Fan-out

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

class PublishSubscribePatternSystem:
    # Implementation of Publish-Subscribe Pattern concepts
    # Demonstrates: Topics, Subscribers, Event Fan-out

    def __init__(self, config: Dict = None):
        self.config = config or {'max_retries': 3, 'timeout': 5.0}
        self.stats = {'requests': 0, 'successes': 0, 'failures': 0, 'total_latency': 0.0}
        self._lock = threading.Lock()
        print(f"🚀 Publish-Subscribe Pattern System initialized")
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
    system = PublishSubscribePatternSystem()
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

1. **Research**: Find a real engineering blog post about Topics (try engineering.atscale.com, netflixtechblog.com, or engineering.fb.com)
2. **Code Challenge**: Extend the example above to log every operation to a file with timestamps
3. **Design Exercise**: Draw a system diagram showing how Publish-Subscribe Pattern fits into a ride-sharing app like Uber
4. **Trade-off Analysis**: What are 3 situations where you would NOT use this approach?
5. **Interview Practice**: Explain Publish-Subscribe Pattern to someone with no tech background using only analogies

---

## 📚 Resources

- *Designing Data-Intensive Applications* by Martin Kleppmann (essential reading)
- *System Design Interview Vol. 1 & 2* by Alex Xu
- High Scalability Blog — highscalability.com
- InfoQ Engineering Blog — infoq.com/architecture-design
- Papers We Love — paperswelove.org (academic papers on distributed systems)

---

*← [Day 23](day_23.md) | [Index](README.md) | [Day 25](day_25.md) →*
