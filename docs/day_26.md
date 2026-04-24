# Day 26: Event-Driven Architecture
> *Building reactive systems that respond to events*

**Month 1: Foundations › Week 4: Communication Patterns**  
**Tags:** `Theory` `JavaScript` `Python`  
**Estimated Time:** 90–120 minutes

---

## 📖 Theory

### What You'll Learn Today

Today we explore **Event-Driven Architecture** — a fundamental concept you'll encounter when designing large-scale systems. Understanding this well is the difference between a system that breaks under load and one that scales gracefully.

### Core Topics

- **Domain Events**
- **Commands vs Events**
- **CQRS**
- **Event Store**
- **Outbox Pattern**
- **Eventual Consistency**
- **Event Sourcing**

### Why It Matters

**Event-Driven Architecture** is used in production systems at Google, Netflix, Uber, and Amazon. The concepts you learn today appear in system design interviews and every day in engineering work.

The key engineering mindset: **every design decision is a trade-off**. There is no perfect solution — only the best solution for your specific requirements, scale, and constraints.

### Deep Dive

### Deep Dive: Events vs Commands — A Fundamental Distinction

**Commands** are imperative and directed: "CreateOrder", "SendEmail", "ChargePayment". They tell a specific service to DO something. The sender must know who processes it and expects a success/failure response.

**Events** are declarative: "OrderCreated", "PaymentSucceeded", "UserRegistered". They announce something that HAS happened. No recipient is specified. Any interested service can subscribe and react. The publisher doesn't know how many services are listening.

**Events decouple services.** With commands: Order Service calls Payment Service, which calls Inventory, which calls Notification. A chain of synchronous dependencies. Notification being slow makes the entire order slow. With events: Order Service publishes `OrderCreated`. Payment, Inventory, and Notification all subscribe independently. Notification being slow doesn't affect order creation speed.

**The Outbox Pattern prevents lost events.** Your service writes to the database AND publishes an event. If the publish fails after the write, the order is saved but nobody knows about it. Solution: write the event to an `outbox` table in the SAME database transaction. A separate relay process reads the outbox and publishes to the broker. Database write and event "publishing" are atomic; actual delivery is eventual but guaranteed.

---

## 🍎 Real-World Analogy

### Real-World Analogy: School Enrollment — Command-Driven vs Event-Driven

**Command-driven enrollment (synchronous chain).** Admissions officer registers Priya and manually calls each department in sequence: ID office (wait 5 min) → class assignment (wait 3 min) → library (wait 4 min) → IT (wait 6 min). Total: 18 minutes of waiting, one call at a time.

**Event-driven enrollment.** Admissions officer records the enrollment and posts a notice: "New student enrolled: Priya Sharma, Grade 10." All departments react in parallel: ID office creates an ID card, class assignment assigns 10-B, library issues a library card, IT creates an app login. Admissions officer is free in 30 seconds; each department handles their part simultaneously.

**Outbox pattern = the official notice board log.** Instead of pinning notices directly (which might get lost), the admissions officer first writes the notice in the official register (outbox table — same database transaction as the enrollment record). A dedicated prefect reads the register and pins notices to the board. Even if the notice board blows away, the register remains — the prefect can re-pin from it. The enrollment is never lost.

---

## 🔑 Key Concepts

- **Domain Events**
- **Commands vs Events**
- **CQRS**
- **Event Store**
- **Outbox Pattern**
- **Eventual Consistency**
- **Event Sourcing**

---

## 💛 JavaScript Example

```javascript
// Day 26: Event-Driven Architecture
// ============================================================
// Practical JavaScript implementation demonstrating:
// Domain Events, Commands vs Events, CQRS

class EventDrivenArchitectureDemo {
  constructor(config = {}) {
    this.config = { maxRetries: 3, timeout: 5000, ...config };
    this.stats = { requests: 0, successes: 0, failures: 0, latencyTotal: 0 };
    console.log(`🚀 Event-Driven Architecture Demo initialized`);
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
    // Core logic representing Domain Events
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
  const demo = new EventDrivenArchitectureDemo();

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
# Day 26: Event-Driven Architecture
# ============================================================
# Practical Python implementation demonstrating:
# Domain Events, Commands vs Events, CQRS

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

class EventDrivenArchitectureSystem:
    # Implementation of Event-Driven Architecture concepts
    # Demonstrates: Domain Events, Commands vs Events, CQRS

    def __init__(self, config: Dict = None):
        self.config = config or {'max_retries': 3, 'timeout': 5.0}
        self.stats = {'requests': 0, 'successes': 0, 'failures': 0, 'total_latency': 0.0}
        self._lock = threading.Lock()
        print(f"🚀 Event-Driven Architecture System initialized")
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
    system = EventDrivenArchitectureSystem()
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

1. **Research**: Find a real engineering blog post about Domain Events (try engineering.atscale.com, netflixtechblog.com, or engineering.fb.com)
2. **Code Challenge**: Extend the example above to log every operation to a file with timestamps
3. **Design Exercise**: Draw a system diagram showing how Event-Driven Architecture fits into a ride-sharing app like Uber
4. **Trade-off Analysis**: What are 3 situations where you would NOT use this approach?
5. **Interview Practice**: Explain Event-Driven Architecture to someone with no tech background using only analogies

---

## 📚 Resources

- *Designing Data-Intensive Applications* by Martin Kleppmann (essential reading)
- *System Design Interview Vol. 1 & 2* by Alex Xu
- High Scalability Blog — highscalability.com
- InfoQ Engineering Blog — infoq.com/architecture-design
- Papers We Love — paperswelove.org (academic papers on distributed systems)

---

*← [Day 25](day_25.md) | [Index](README.md) | [Day 27](day_27.md) →*
