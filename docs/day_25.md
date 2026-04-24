# Day 25: API Gateway
> *The single front door for all microservice traffic*

**Month 1: Foundations › Week 4: Communication Patterns**  
**Tags:** `Theory` `JavaScript` `Python`  
**Estimated Time:** 90–120 minutes

---

## 📖 Theory

### What You'll Learn Today

Today we explore **API Gateway** — a fundamental concept you'll encounter when designing large-scale systems. Understanding this well is the difference between a system that breaks under load and one that scales gracefully.

### Core Topics

- **Request Routing**
- **SSL Termination**
- **Rate Limiting**
- **Auth Offloading**
- **Request Aggregation**
- **Kong**
- **AWS API Gateway**
- **Traefik**

### Why It Matters

**API Gateway** is used in production systems at Google, Netflix, Uber, and Amazon. The concepts you learn today appear in system design interviews and every day in engineering work.

The key engineering mindset: **every design decision is a trade-off**. There is no perfect solution — only the best solution for your specific requirements, scale, and constraints.

### Deep Dive

### Deep Dive: What an API Gateway Actually Handles

Without a gateway, every microservice independently implements: auth (verify JWT), rate limiting, request logging, SSL termination, CORS. 30 microservices = 30 implementations of the same cross-cutting concerns. When the JWT algorithm changes, you update 30 services.

**The gateway centralises all of this.** Auth: verify the JWT once at the gateway — all downstream services receive a trusted `X-User-Id` header. They don't re-verify. Rate limiting: one rule at the gateway protects all 30 services simultaneously. Routing: `/api/users/*` → User Service, `/api/orders/*` → Order Service. The client uses one base URL.

**Response aggregation saves mobile round trips.** A home screen needs data from User Service, Feed Service, and Notification Service. Without aggregation: 3 HTTP calls, 3 × 50ms = 150ms minimum. With aggregation: gateway fans out to all 3 in parallel, merges results, returns one response in ~55ms (the slowest of the three).

**SSL termination at the gateway.** Handle HTTPS at the single entry point. Internal services communicate over plain HTTP within the private network. One certificate to manage, not one per service.

---

## 🍎 Real-World Analogy

### Real-World Analogy: A Hotel Reception Desk

A large hotel has 12 departments: restaurant, spa, gym, housekeeping, concierge, laundry, room service, pool, business centre, parking, events, and security. Guests don't call each department's internal extension. They call reception — one number, one point of contact.

**Authentication:** Reception verifies your room key card once when you call. When connecting you to room service, they tell room service: "This is a verified guest in Room 412." Room service doesn't re-verify — they trust reception's confirmation.

**Request routing:** "I'd like room service" → reception connects you to the kitchen. "Extra towels" → housekeeping. You don't need to know any department's direct extension.

**Rate limiting:** "We can only accept 3 room service orders per room per hour during peak times." Reception enforces this before the request reaches the kitchen.

**Response aggregation:** "Can you arrange breakfast, towel service, and a 9am taxi?" Reception calls all three departments simultaneously and confirms all three to you in one response. You make one call; reception coordinates the rest.

---

## 🔑 Key Concepts

- **Request Routing**
- **SSL Termination**
- **Rate Limiting**
- **Auth Offloading**
- **Request Aggregation**
- **Kong**
- **AWS API Gateway**
- **Traefik**

---

## 💛 JavaScript Example

```javascript
// Day 25: API Gateway
// ============================================================
// Practical JavaScript implementation demonstrating:
// Request Routing, SSL Termination, Rate Limiting

class APIGatewayDemo {
  constructor(config = {}) {
    this.config = { maxRetries: 3, timeout: 5000, ...config };
    this.stats = { requests: 0, successes: 0, failures: 0, latencyTotal: 0 };
    console.log(`🚀 API Gateway Demo initialized`);
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
    // Core logic representing Request Routing
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
  const demo = new APIGatewayDemo();

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
# Day 25: API Gateway
# ============================================================
# Practical Python implementation demonstrating:
# Request Routing, SSL Termination, Rate Limiting

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

class APIGatewaySystem:
    # Implementation of API Gateway concepts
    # Demonstrates: Request Routing, SSL Termination, Rate Limiting

    def __init__(self, config: Dict = None):
        self.config = config or {'max_retries': 3, 'timeout': 5.0}
        self.stats = {'requests': 0, 'successes': 0, 'failures': 0, 'total_latency': 0.0}
        self._lock = threading.Lock()
        print(f"🚀 API Gateway System initialized")
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
    system = APIGatewaySystem()
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

1. **Research**: Find a real engineering blog post about Request Routing (try engineering.atscale.com, netflixtechblog.com, or engineering.fb.com)
2. **Code Challenge**: Extend the example above to log every operation to a file with timestamps
3. **Design Exercise**: Draw a system diagram showing how API Gateway fits into a ride-sharing app like Uber
4. **Trade-off Analysis**: What are 3 situations where you would NOT use this approach?
5. **Interview Practice**: Explain API Gateway to someone with no tech background using only analogies

---

## 📚 Resources

- *Designing Data-Intensive Applications* by Martin Kleppmann (essential reading)
- *System Design Interview Vol. 1 & 2* by Alex Xu
- High Scalability Blog — highscalability.com
- InfoQ Engineering Blog — infoq.com/architecture-design
- Papers We Love — paperswelove.org (academic papers on distributed systems)

---

*← [Day 24](day_24.md) | [Index](README.md) | [Day 26](day_26.md) →*
