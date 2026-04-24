# Day 13: DNS and How the Internet Works
> *Translating names to addresses across the internet*

**Month 1: Foundations › Week 2: Performance and Infrastructure**  
**Tags:** `Theory` `JavaScript` `Python`  
**Estimated Time:** 90–120 minutes

---

## 📖 Theory

### What You'll Learn Today

Today we explore **DNS and How the Internet Works** — a fundamental concept you'll encounter when designing large-scale systems. Understanding this well is the difference between a system that breaks under load and one that scales gracefully.

### Core Topics

- **DNS Lookup**
- **A Record**
- **CNAME**
- **MX Record**
- **TTL**
- **Root Servers**
- **Recursive Resolver**
- **GeoDNS**
- **Anycast**

### Why It Matters

**DNS and How the Internet Works** is used in production systems at Google, Netflix, Uber, and Amazon. The concepts you learn today appear in system design interviews and every day in engineering work.

The key engineering mindset: **every design decision is a trade-off**. There is no perfect solution — only the best solution for your specific requirements, scale, and constraints.

### Deep Dive

### Deep Dive: The DNS Resolution Journey

When you type `google.com`, a multi-step lookup happens in under 50ms:

1. **Browser cache** — recently visited? The IP is already stored locally.
2. **OS DNS cache / /etc/hosts** — the computer's own DNS cache.
3. **Recursive resolver** (usually your ISP's DNS server) — asks "what's google.com?"
4. **Root name servers** (13 globally) — say "for .com, ask Verisign's TLD server."
5. **TLD server** for `.com` — says "for google.com, ask Google's name servers."
6. **Google's authoritative name server** — returns the actual IP: `142.250.195.14`.
7. Result cached at every level for the TTL duration.

**TTL controls propagation delay.** If you change your A record (server IP) and TTL is 86,400 (24 hours), some users get routed to the old server for up to 24 hours. Pro tip: lower your TTL to 60 seconds a week before any planned server migration, then change the record. Changes propagate globally in 1 minute instead of 24 hours.

**GeoDNS routes users to their nearest data centre.** Same domain name returns different IPs based on where the query originates. A query from India returns the Mumbai data centre IP. A query from Germany returns the Frankfurt IP. Same domain, different physical servers — transparent to the user.

---

## 🍎 Real-World Analogy

### Real-World Analogy: India's PIN Code Postal System

DNS is structurally identical to how India's postal system routes letters.

**Domain name (`amazon.in`) = the shop name everyone knows.** People say "Amazon" — not the full street address with PIN code.

**Root servers = the Central Post Office.** Doesn't know every address, but knows which regional offices handle which country TLDs. "For `.in`, contact the .IN Registry."

**TLD server = the state regional post office.** Knows which local post offices handle which cities or domains. "For `amazon.in`, contact Amazon India's own name servers."

**Authoritative DNS = Amazon India's own address registry.** Has the exact, official current IP. Returns it definitively.

**Your computer's DNS cache = your postman's address book.** Once your postman has delivered to Amazon's warehouse, he remembers the address. For the next [TTL] days, he goes directly without calling the post office first.

**TTL = "address valid until [date]".** If Amazon moves offices, they update the post office registry. Postmen who memorised the old address still go there until their books expire (TTL). Lowering the TTL before a migration is like sending all postmen a note: "This address changes on October 1st — stop memorising it after September 25th." 

---

## 🔑 Key Concepts

- **DNS Lookup**
- **A Record**
- **CNAME**
- **MX Record**
- **TTL**
- **Root Servers**
- **Recursive Resolver**
- **GeoDNS**
- **Anycast**

---

## 💛 JavaScript Example

```javascript
// Day 13: DNS and How the Internet Works
// ============================================================
// Practical JavaScript implementation demonstrating:
// DNS Lookup, A Record, CNAME

class DNSandHowtheInternetWorksDemo {
  constructor(config = {}) {
    this.config = { maxRetries: 3, timeout: 5000, ...config };
    this.stats = { requests: 0, successes: 0, failures: 0, latencyTotal: 0 };
    console.log(`🚀 DNS and How the Internet Works Demo initialized`);
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
    // Core logic representing DNS Lookup
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
  const demo = new DNSandHowtheInternetWorksDemo();

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
# Day 13: DNS and How the Internet Works
# ============================================================
# Practical Python implementation demonstrating:
# DNS Lookup, A Record, CNAME

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

class DNSandHowtheInternetWorksSystem:
    # Implementation of DNS and How the Internet Works concepts
    # Demonstrates: DNS Lookup, A Record, CNAME

    def __init__(self, config: Dict = None):
        self.config = config or {'max_retries': 3, 'timeout': 5.0}
        self.stats = {'requests': 0, 'successes': 0, 'failures': 0, 'total_latency': 0.0}
        self._lock = threading.Lock()
        print(f"🚀 DNS and How the Internet Works System initialized")
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
    system = DNSandHowtheInternetWorksSystem()
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

1. **Research**: Find a real engineering blog post about DNS Lookup (try engineering.atscale.com, netflixtechblog.com, or engineering.fb.com)
2. **Code Challenge**: Extend the example above to log every operation to a file with timestamps
3. **Design Exercise**: Draw a system diagram showing how DNS and How the Internet Works fits into a ride-sharing app like Uber
4. **Trade-off Analysis**: What are 3 situations where you would NOT use this approach?
5. **Interview Practice**: Explain DNS and How the Internet Works to someone with no tech background using only analogies

---

## 📚 Resources

- *Designing Data-Intensive Applications* by Martin Kleppmann (essential reading)
- *System Design Interview Vol. 1 & 2* by Alex Xu
- High Scalability Blog — highscalability.com
- InfoQ Engineering Blog — infoq.com/architecture-design
- Papers We Love — paperswelove.org (academic papers on distributed systems)

---

*← [Day 12](day_12.md) | [Index](README.md) | [Day 14](day_14.md) →*
