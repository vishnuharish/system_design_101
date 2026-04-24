# Day 11: Load Balancing
> *Distribute traffic so no single server is overwhelmed*

**Month 1: Foundations > Week 2: Performance and Infrastructure**
**Tags:** `Theory` `JavaScript` `Python`
**Estimated Time:** 90-120 minutes

---

## Theory

### The Problem Load Balancers Solve

A single server handles ~1,000 requests/sec. On Black Friday you need 50,000 req/s. You can't make one server 50x bigger — you add 50 servers and put a **load balancer** in front with a single IP address. It distributes incoming requests across the fleet.

### Load Balancing Algorithms

**Round Robin** — Requests cycle: Server A, B, C, A, B, C... Equal distribution when all servers are the same size. Default in most setups.

**Weighted Round Robin** — Servers with more capacity get proportionally more traffic. 8-core server gets weight 4, 2-core gets weight 1. Server A handles 4x more requests.

**Least Connections** — Each new request goes to the server with the fewest active connections. Best when requests take variable time (some finish in 1ms, others in 5 seconds).

**IP Hash** — Hash the client IP to always route them to the same server. Essential for **sticky sessions** — if Server A stores the user's shopping cart in memory, they must always hit Server A. Downside: uneven distribution if requests cluster by region.

### Layer 4 vs Layer 7

**L4 (TCP)** — Routes at the IP+port level. Extremely fast, doesn't read request content. Use for: raw TCP, database proxies, gaming.

**L7 (HTTP)** — Understands HTTP. Routes by URL path, headers, cookies. `/api/*` goes to API servers, `/images/*` goes to image servers. Nginx and AWS ALB operate here.

### Health Checks — Removing Failed Servers

Every 5 seconds the load balancer sends `GET /health` to each server. Three consecutive failures → server removed from pool automatically. When the server recovers → added back. Your app becomes self-healing without any engineer intervention.

---

## Real-World Analogy - The Bank Teller Analogy

A busy bank has 6 teller windows (servers) but one door (the load balancer). A greeter at the door directs each customer to an available window:

**Round Robin greeter**: sends customers to windows 1, 2, 3, 4, 5, 6, 1, 2, 3... in order. Simple and fair when every transaction takes the same time.

**Least Connections greeter**: watches all windows and always sends the next customer to whichever window has the fewest people being served. Smarter — avoids the window where one customer is doing a complex transaction that takes 15 minutes.

**Health Checks**: if Window 3 has a "Closed" sign, the greeter stops sending customers there immediately and resumes when it reopens.

**Sticky Sessions**: a customer doing a loan application in multiple steps must always see the same teller (they have all the paperwork). The greeter remembers which teller handles which customer.

The greeter never processes transactions themselves — they just optimally direct traffic. That's the load balancer.

---

## Key Concepts

- **Round Robin**
- **Least Connections**
- **IP Hash**
- **Health Checks**
- **Layer 4 vs Layer 7**
- **Nginx Config**
- **Sticky Sessions**

---

## JavaScript Example

```javascript
// Load balancer implementation with multiple strategies
class LoadBalancer {
  constructor(servers) {
    this.servers = servers.map(s => ({
      url: s, healthy: true, connections: 0, requests: 0
    }));
    this.rrIndex = 0;
  }

  // Round Robin - simple, fair for equal servers
  roundRobin() {
    const healthy = this.servers.filter(s => s.healthy);
    const server  = healthy[this.rrIndex % healthy.length];
    this.rrIndex++;
    return server;
  }

  // Least Connections - best when request durations vary
  leastConnections() {
    return this.servers
      .filter(s => s.healthy)
      .reduce((min, s) => s.connections < min.connections ? s : min);
  }

  // IP Hash - same client always hits same server (sticky sessions)
  ipHash(clientIp) {
    const hash    = clientIp.split('.').reduce((a, n) => a + parseInt(n), 0);
    const healthy = this.servers.filter(s => s.healthy);
    return healthy[hash % healthy.length];
  }

  async route(clientIp, strategy = 'round-robin') {
    const server =
      strategy === 'round-robin'   ? this.roundRobin() :
      strategy === 'least-conn'    ? this.leastConnections() :
                                     this.ipHash(clientIp);

    server.connections++;
    server.requests++;
    console.log(`${clientIp} -> ${server.url} (${server.connections} active conns)`);

    // Simulate request work
    await new Promise(r => setTimeout(r, Math.random() * 100));
    server.connections--;
    return server.url;
  }

  // Periodic health check
  async healthCheck() {
    for (const server of this.servers) {
      try {
        await fetch(`${server.url}/health`);
        if (!server.healthy) {
          server.healthy = true;
          console.log(`+ ${server.url} recovered`);
        }
      } catch {
        server.healthy = false;
        console.log(`- ${server.url} marked unhealthy`);
      }
    }
  }

  stats() {
    return this.servers.map(s =>
      `${s.url}: ${s.requests} reqs, healthy=${s.healthy}`
    );
  }
}

const lb = new LoadBalancer([
  'http://server-a:3001', 'http://server-b:3002', 'http://server-c:3003'
]);

(async () => {
  const ips = ['1.2.3.4','5.6.7.8','9.10.11.12','1.2.3.4','13.14.15.16'];
  console.log('--- Round Robin ---');
  for (const ip of ips) await lb.route(ip, 'round-robin');
  console.log('--- IP Hash (same IP = same server) ---');
  for (const ip of ips) await lb.route(ip, 'ip-hash');
  console.log(lb.stats());
})();
```

---

## Python Example

```python
import threading, time, random

class Server:
    def __init__(self, name):
        self.name = name; self.healthy = True
        self.connections = 0; self.total = 0
        self._lock = threading.Lock()

    def handle(self, req_id):
        with self._lock: self.connections += 1; self.total += 1
        time.sleep(random.uniform(0.01, 0.15))  # variable work time
        with self._lock: self.connections -= 1

class LoadBalancer:
    def __init__(self, servers):
        self.servers = servers; self._rr = 0; self._lock = threading.Lock()

    def healthy(self): return [s for s in self.servers if s.healthy]

    def round_robin(self):
        h = self.healthy()
        with self._lock: s = h[self._rr % len(h)]; self._rr += 1
        return s

    def least_connections(self):
        return min(self.healthy(), key=lambda s: s.connections)

    def ip_hash(self, ip):
        h = sum(int(p) for p in ip.split('.') if p.isdigit())
        return self.healthy()[h % len(self.healthy())]

    def route(self, ip, strategy='round-robin'):
        if strategy == 'least-conn': return self.least_connections()
        if strategy == 'ip-hash':    return self.ip_hash(ip)
        return self.round_robin()

servers = [Server(f'Server-{c}') for c in 'ABC']
lb = LoadBalancer(servers)

# Simulate 12 concurrent requests
threads = []
for i in range(12):
    ip  = f'10.0.{random.randint(0,3)}.{random.randint(1,5)}'
    srv = lb.route(ip, 'least-conn')
    t   = threading.Thread(target=srv.handle, args=(f'req_{i}',))
    print(f'req_{i:02d} [{ip}] -> {srv.name} ({srv.connections} active)')
    threads.append(t); t.start()
for t in threads: t.join()

print("\nFinal stats:")
for s in servers: print(f"  {s.name}: {s.total} requests handled")
```

---

## Homework

1. Configure Nginx as a load balancer for 3 local servers using docker-compose - test round_robin and least_conn
2. What breaks when you add a load balancer to a stateful app without sticky sessions? Give a concrete example
3. How does AWS Elastic Load Balancer differ from Nginx? List 3 differences
4. Calculate: if each server handles 800 req/s and you need 12,000 req/s with 20% headroom, how many servers?

---

## Resources

- Nginx Load Balancing Docs - nginx.org/en/docs/http/load_balancing.html
- AWS ALB vs NLB - docs.aws.amazon.com/elasticloadbalancing
- HAProxy Documentation - haproxy.org
- Load Balancing Algorithms Explained - kemp.ax

---

*<- [Day 10](day_10.md) | [Index](README.md) | [Day 12](day_12.md) ->*
