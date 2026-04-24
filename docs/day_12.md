# Day 12: Horizontal vs Vertical Scaling
> *Two strategies for handling more traffic*

**Month 1: Foundations > Week 2: Performance and Infrastructure**
**Tags:** `Theory` `JavaScript` `Python`
**Estimated Time:** 90-120 minutes

---

## Theory

### Vertical Scaling (Scale Up)

Replace your server with a bigger one. 4 CPU cores to 32, 16GB RAM to 256GB. Like upgrading from a bicycle to a motorbike.

**Pros**: Simple, no code changes, no architectural complexity.
**Cons**: Hard ceiling — the biggest cloud instance is finite. Single point of failure — when it crashes, everything stops. Non-linear cost — a 16x machine costs far more than 16x the price.

### Horizontal Scaling (Scale Out)

Add more servers of the same size and distribute traffic across them. 1 server grows to 10, then 100. Netflix uses thousands of small servers, not one giant one.

**Pros**: Theoretically unlimited scale. No single point of failure. Commodity hardware is cheap.
**Cons**: Application must be stateless. Requires a load balancer. More complex to manage.

### The Stateless Requirement

For horizontal scaling to work, any server must be able to handle any request. This means NO server-local state:

- Sessions must live in Redis, not in server memory
- Files must live in S3, not on the server's disk  
- JWT tokens carry the user's identity in the request itself

If Server A stores the user's login session in RAM, routing that user to Server B will log them out. Solution: move all state outside the application servers.

### Auto Scaling — The Cloud Superpower

AWS, GCP, and Azure support auto scaling groups: add servers when CPU > 70%, remove them when CPU < 20%. Traffic spike at IPL final → 50 servers spin up automatically. 3am quiet period → scales back to 3 servers. You pay only for what you actually use.

### When to Vertical vs Horizontal

| Use Case | Strategy |
|----------|----------|
| Database primary (hard to shard) | Vertical first, then read replicas |
| Web/API servers (stateless) | Horizontal — easy and cheap |
| Startup or simple app | Vertical — simpler to operate |
| Need 99.99% uptime | Horizontal — no single point of failure |

---

## Real-World Analogy - The Restaurant Kitchen Analogy

You run a restaurant with one chef. Business is growing.

**Vertical scaling**: Buy your one chef a bigger, faster oven and better tools. She can now cook faster. But there's only so large an oven you can buy, and if she calls in sick, the restaurant closes entirely.

**Horizontal scaling**: Hire three more chefs, each with their own standard oven. Now four chefs cook simultaneously. If one is sick, the other three keep working. As the dinner rush builds, you call in two more part-time chefs (auto scaling). After service, they leave (scale down, pay less).

**The stateless requirement**: All four chefs must use the shared pantry and shared recipe book. If Chef A keeps the secret sauce recipe in his personal notebook and goes home, no one else can make that dish. In software, the "notebook" (session state) must be in shared Redis, not on one server.

**Auto scaling** is like a staffing agency on speed dial — send extra chefs in 2 minutes when you get an unexpected booking flood, and they go home when it quiets down.

---

## Key Concepts

- **Scale Up**
- **Scale Out**
- **Auto Scaling Groups**
- **Stateless Servers**
- **Session Affinity**
- **Cloud Elasticity**
- **Cost Comparison**

---

## JavaScript Example

```javascript
// Simulating vertical vs horizontal scaling capacity limits

class Server {
  constructor(name, cpuCores, reqPerCore = 100) {
    this.name = name;
    this.capacity = cpuCores * reqPerCore;
    this.load = 0;
    this.rejected = 0;
  }
  handle(reqId) {
    if (this.load >= this.capacity) { this.rejected++; return false; }
    this.load++;
    setTimeout(() => this.load--, 100); // request takes 100ms
    return true;
  }
  get utilPct() { return (this.load / this.capacity * 100).toFixed(1); }
}

// Vertical: one 32-core machine
function testVertical(numRequests) {
  const server = new Server('Mega-Server', 32);
  let handled = 0;
  for (let i = 0; i < numRequests; i++) {
    if (server.handle(i)) handled++; 
  }
  console.log(`Vertical: handled ${handled}/${numRequests}, util ${server.utilPct}%`);
}

// Horizontal: four 8-core machines
function testHorizontal(numRequests) {
  const servers = ['A','B','C','D'].map(n => new Server(`Server-${n}`, 8));
  let idx = 0, handled = 0;
  for (let i = 0; i < numRequests; i++) {
    const s = servers[idx++ % servers.length];
    if (s.handle(i)) handled++;
  }
  servers.forEach(s => console.log(`  ${s.name}: util ${s.utilPct}%`));
  console.log(`Horizontal: handled ${handled}/${numRequests}`);
}

testVertical(3000);
testHorizontal(3000);

// Auto Scaling Group simulation
class AutoScalingGroup {
  constructor(min=2, max=8, coresPerServer=4) {
    this.min = min; this.max = max; this.cores = coresPerServer;
    this.servers = Array.from({length: min}, (_, i) =>
      new Server(`asg-${i}`, coresPerServer));
  }

  avgUtil() {
    const totalLoad = this.servers.reduce((s,sv) => s + sv.load, 0);
    const totalCap  = this.servers.reduce((s,sv) => s + sv.capacity, 0);
    return totalLoad / totalCap;
  }

  tick() {
    const u = this.avgUtil();
    if (u > 0.70 && this.servers.length < this.max) {
      this.servers.push(new Server(`asg-${this.servers.length}`, this.cores));
      console.log(`Scale OUT: ${this.servers.length} servers (util ${(u*100).toFixed(0)}%)`);
    } else if (u < 0.20 && this.servers.length > this.min) {
      this.servers.pop();
      console.log(`Scale IN:  ${this.servers.length} servers (util ${(u*100).toFixed(0)}%)`);
    }
  }
}
```

---

## Python Example

```python
import threading, time, random

class Server:
    def __init__(self, name, cores, rpc=100):
        self.name = name; self.capacity = cores * rpc
        self.load = 0; self.total = 0; self._l = threading.Lock()

    @property
    def util(self): return self.load / self.capacity

    def handle(self):
        with self._l:
            if self.load >= self.capacity: return False
            self.load += 1; self.total += 1
        time.sleep(random.uniform(0.02, 0.12))  # variable work
        with self._l: self.load -= 1
        return True

class AutoScalingGroup:
    def __init__(self, min_s=2, max_s=8, cores=4):
        self.min = min_s; self.max = max_s; self.cores = cores
        self.pool = [Server(f's{i}', cores) for i in range(min_s)]
        self._l = threading.Lock()

    def avg_util(self):
        return sum(s.util for s in self.pool) / len(self.pool)

    def route(self): return random.choice(self.pool)

    def scale_check(self):
        u = self.avg_util()
        with self._l:
            if u > 0.70 and len(self.pool) < self.max:
                self.pool.append(Server(f's{len(self.pool)}', self.cores))
                print(f"  Scale OUT -> {len(self.pool)} servers ({u:.0%} util)")
            elif u < 0.20 and len(self.pool) > self.min:
                self.pool.pop()
                print(f"  Scale IN  -> {len(self.pool)} servers ({u:.0%} util)")

asg = AutoScalingGroup(min_s=2, max_s=8, cores=4)
for label, rps in [("Low traffic", 30), ("Rush hour", 500), ("Post-rush", 60)]:
    print(f"\n{label}: {rps} concurrent requests")
    threads = [threading.Thread(target=asg.route().handle) for _ in range(rps)]
    for t in threads: t.start()
    for t in threads: t.join()
    asg.scale_check()
```

---

## Homework

1. At what EC2 instance size does vertical scaling stop being cost-effective? Research AWS pricing curves
2. List every change you must make to a Flask app to make it horizontally scalable (hint: sessions, files, config)
3. Research how Kubernetes Horizontal Pod Autoscaler works — what metrics trigger it?
4. Calculate: c5.2xlarge ($0.34/hr, 2000 req/s) vs 10x t3.small ($0.02/hr each, 200 req/s each) for 10k req/s load

---

## Resources

- The Twelve-Factor App (stateless processes) - 12factor.net
- AWS Auto Scaling Docs - docs.aws.amazon.com/autoscaling
- Kubernetes HPA - kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale
- High Scalability Blog - highscalability.com

---

*<- [Day 11](day_11.md) | [Index](README.md) | [Day 13](day_13.md) ->*
