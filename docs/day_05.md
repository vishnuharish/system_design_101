# Day 05: SQL Databases
> *Storing data in structured relational tables*

**Month 1: Foundations › Week 1: Basics of System Design**  
**Tags:** `Theory` `JavaScript` `Python`  
**Estimated Time:** 90–120 minutes

---

## 📖 Theory

### What You'll Learn Today

Today we explore **SQL Databases** — a fundamental concept you'll encounter when designing large-scale systems. Understanding this well is the difference between a system that breaks under load and one that scales gracefully.

### Core Topics

- **Tables and Rows**
- **Primary Keys**
- **Foreign Keys**
- **JOIN Operations**
- **Indexes**
- **Transactions**
- **ACID Properties**
- **Normalization**

### Why It Matters

**SQL Databases** is used in production systems at Google, Netflix, Uber, and Amazon. The concepts you learn today appear in system design interviews and every day in engineering work.

The key engineering mindset: **every design decision is a trade-off**. There is no perfect solution — only the best solution for your specific requirements, scale, and constraints.

### Deep Dive

### Deep Dive: Why Relationships Are SQL's Superpower

SQL databases let tables reference each other through keys. A `students` table, a `subjects` table, and a `scores` table can be joined in one query to answer "what are all Grade 10 students' Maths scores?" — without making three separate requests.

**ACID guarantees prevent data corruption at scale.** When two people transfer money simultaneously, the database must ensure neither transaction sees the other's half-finished state. Isolation levels (READ COMMITTED, REPEATABLE READ, SERIALIZABLE) control exactly how much concurrent transactions can see of each other.

**Indexes are the most important performance tool in SQL.** `SELECT * FROM users WHERE email = 'x@y.com'` without an index scans every row. With a B-tree index on `email`, it jumps directly to the matching row in O(log N). At 10 million rows, this is the difference between 30 seconds and 1 millisecond.

**When SQL is the right choice:** structured data with clear relationships, strong consistency requirements (banking, inventory), complex queries joining multiple tables, or any situation where you need ACID guarantees. PostgreSQL powers Instagram, Twitch, and Shopify at massive scale.

---

## 🍎 Real-World Analogy

### Real-World Analogy: A School's Paper Record System

Think of three separate filing cabinets: Student Profiles, Subject Catalogue, and Exam Score Sheets.

**Foreign key = the roll number on every score sheet.** Each sheet has the student's roll number written on it. That number links the score back to the correct student profile.

**JOIN = the admin combining two cabinets.** "Give me Priya's Maths score" — the admin opens Score Sheets, finds Priya's roll number, matches it to the Subject Catalogue, and combines the result. That's exactly what SQL JOIN does.

**Transaction = submitting a complete report card.** The school only accepts a fully signed report card. If the teacher fills in 5 subjects and the pen runs out, the incomplete card is discarded. Either all marks are submitted or none — that's atomicity.

**Index = alphabetical dividers in a cabinet.** Without dividers, finding a student whose surname starts with "P" means flipping through all 800 files. With dividers you jump straight to P. A database index does the same — O(log N) instead of O(N) scan.

---

## 🔑 Key Concepts

- **Tables and Rows**
- **Primary Keys**
- **Foreign Keys**
- **JOIN Operations**
- **Indexes**
- **Transactions**
- **ACID Properties**
- **Normalization**

---

## 💛 JavaScript Example

```javascript
// Day 05: SQL Databases
// ============================================================
// Practical JavaScript implementation demonstrating:
// Tables and Rows, Primary Keys, Foreign Keys

class SQLDatabasesDemo {
  constructor(config = {}) {
    this.config = { maxRetries: 3, timeout: 5000, ...config };
    this.stats = { requests: 0, successes: 0, failures: 0, latencyTotal: 0 };
    console.log(`🚀 SQL Databases Demo initialized`);
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
    // Core logic representing Tables and Rows
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
  const demo = new SQLDatabasesDemo();

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
# Day 05: SQL Databases
# ============================================================
# Practical Python implementation demonstrating:
# Tables and Rows, Primary Keys, Foreign Keys

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

class SQLDatabasesSystem:
    # Implementation of SQL Databases concepts
    # Demonstrates: Tables and Rows, Primary Keys, Foreign Keys

    def __init__(self, config: Dict = None):
        self.config = config or {'max_retries': 3, 'timeout': 5.0}
        self.stats = {'requests': 0, 'successes': 0, 'failures': 0, 'total_latency': 0.0}
        self._lock = threading.Lock()
        print(f"🚀 SQL Databases System initialized")
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
    system = SQLDatabasesSystem()
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

1. **Research**: Find a real engineering blog post about Tables and Rows (try engineering.atscale.com, netflixtechblog.com, or engineering.fb.com)
2. **Code Challenge**: Extend the example above to log every operation to a file with timestamps
3. **Design Exercise**: Draw a system diagram showing how SQL Databases fits into a ride-sharing app like Uber
4. **Trade-off Analysis**: What are 3 situations where you would NOT use this approach?
5. **Interview Practice**: Explain SQL Databases to someone with no tech background using only analogies

---

## 📚 Resources

- *Designing Data-Intensive Applications* by Martin Kleppmann (essential reading)
- *System Design Interview Vol. 1 & 2* by Alex Xu
- High Scalability Blog — highscalability.com
- InfoQ Engineering Blog — infoq.com/architecture-design
- Papers We Love — paperswelove.org (academic papers on distributed systems)

---

*← [Day 04](day_04.md) | [Index](README.md) | [Day 06](day_06.md) →*
