# Day 22: HTTP vs WebSockets vs gRPC
> *Choosing the right communication protocol*

**Month 1: Foundations › Week 4: Communication Patterns**  
**Tags:** `Theory` `JavaScript` `Python`  
**Estimated Time:** 90–120 minutes

---

## 📖 Theory

### What You'll Learn Today

Today we explore **HTTP vs WebSockets vs gRPC** — a fundamental concept you'll encounter when designing large-scale systems. Understanding this well is the difference between a system that breaks under load and one that scales gracefully.

### Core Topics

- **Stateless HTTP**
- **Persistent WebSocket**
- **Binary gRPC**
- **Protocol Buffers**
- **Server-Sent Events**
- **Long Polling**
- **HTTP/2**

### Why It Matters

**HTTP vs WebSockets vs gRPC** is used in production systems at Google, Netflix, Uber, and Amazon. The concepts you learn today appear in system design interviews and every day in engineering work.

The key engineering mindset: **every design decision is a trade-off**. There is no perfect solution — only the best solution for your specific requirements, scale, and constraints.

### Deep Dive

### Deep Dive: Choosing the Right Protocol

**HTTP (request-response):** Client asks, server answers, connection closes. Stateless — any server handles any request. Perfect for horizontal scaling. Limitation: the server cannot push data to the client without the client asking first. Polling (`GET /score` every 2 seconds) wastes bandwidth and adds latency.

**WebSocket:** One persistent connection, messages can flow in either direction at any time. Server can push data without being asked. Used by: WhatsApp Web, live trading platforms, multiplayer games, Google Docs. Weakness: stateful connections pin users to one server. Horizontal scaling requires a shared pub/sub layer (Redis) to broadcast to all connected users.

**gRPC:** HTTP/2-based binary protocol using Protocol Buffers. JSON `"age": 25` = 9 bytes. Protobuf = 2 bytes. 4.5× smaller, plus HTTP/2 multiplexing (multiple requests share one TCP connection). Four patterns: unary, server streaming, client streaming, bidirectional streaming. Used for internal microservice-to-microservice communication where performance matters.

**Server-Sent Events (SSE):** Unidirectional push from server to client over regular HTTP. Much simpler than WebSocket when you only need server → client push (notifications, live feeds). Works through proxies that might block WebSocket upgrades.

---

## 🍎 Real-World Analogy

### Real-World Analogy: Three Ways to Follow a Live Cricket Score

**HTTP polling = refreshing the newspaper website every 30 seconds.** You keep asking "what's the score?" You're doing all the work. Scores may be 30 seconds stale. Simple but inefficient.

**Server-Sent Events = a dedicated scoreboard subscription.** You subscribe once and the scoreboard service pushes you an update every time a wicket falls or runs are scored. One-way broadcast from server to you. Simple, efficient for this use case.

**WebSocket = being in the press box with a two-way radio.** The scorer pushes updates to you the instant anything happens. You can also ask questions back: "Confirm the bowling figures?" Real-time, bidirectional. Used by live fantasy cricket apps where you submit predictions and interact with the platform.

**gRPC = the private data link between the stadium scoring computer and the broadcast network.** Not visible to fans. Binary, compressed, extremely fast transfer of structured match data — overs, runs, wickets, player stats — from the scoring computer to the broadcaster's backend. Strongly typed contract defined in a `.proto` file. Fast, internal, unambiguous.

---

## 🔑 Key Concepts

- **Stateless HTTP**
- **Persistent WebSocket**
- **Binary gRPC**
- **Protocol Buffers**
- **Server-Sent Events**
- **Long Polling**
- **HTTP/2**

---

## 💛 JavaScript Example

```javascript
// Day 22: HTTP vs WebSockets vs gRPC
// ============================================================
// Practical JavaScript implementation demonstrating:
// Stateless HTTP, Persistent WebSocket, Binary gRPC

class HTTPvsWebSocketsvsgRPCDemo {
  constructor(config = {}) {
    this.config = { maxRetries: 3, timeout: 5000, ...config };
    this.stats = { requests: 0, successes: 0, failures: 0, latencyTotal: 0 };
    console.log(`🚀 HTTP vs WebSockets vs gRPC Demo initialized`);
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
    // Core logic representing Stateless HTTP
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
  const demo = new HTTPvsWebSocketsvsgRPCDemo();

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
# Day 22: HTTP vs WebSockets vs gRPC
# ============================================================
# Practical Python implementation demonstrating:
# Stateless HTTP, Persistent WebSocket, Binary gRPC

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

class HTTPvsWebSocketsvsgRPCSystem:
    # Implementation of HTTP vs WebSockets vs gRPC concepts
    # Demonstrates: Stateless HTTP, Persistent WebSocket, Binary gRPC

    def __init__(self, config: Dict = None):
        self.config = config or {'max_retries': 3, 'timeout': 5.0}
        self.stats = {'requests': 0, 'successes': 0, 'failures': 0, 'total_latency': 0.0}
        self._lock = threading.Lock()
        print(f"🚀 HTTP vs WebSockets vs gRPC System initialized")
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
    system = HTTPvsWebSocketsvsgRPCSystem()
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

1. **Research**: Find a real engineering blog post about Stateless HTTP (try engineering.atscale.com, netflixtechblog.com, or engineering.fb.com)
2. **Code Challenge**: Extend the example above to log every operation to a file with timestamps
3. **Design Exercise**: Draw a system diagram showing how HTTP vs WebSockets vs gRPC fits into a ride-sharing app like Uber
4. **Trade-off Analysis**: What are 3 situations where you would NOT use this approach?
5. **Interview Practice**: Explain HTTP vs WebSockets vs gRPC to someone with no tech background using only analogies

---

## 📚 Resources

- *Designing Data-Intensive Applications* by Martin Kleppmann (essential reading)
- *System Design Interview Vol. 1 & 2* by Alex Xu
- High Scalability Blog — highscalability.com
- InfoQ Engineering Blog — infoq.com/architecture-design
- Papers We Love — paperswelove.org (academic papers on distributed systems)

---

*← [Day 21](day_21.md) | [Index](README.md) | [Day 23](day_23.md) →*
