Great, let's dive into Topic 2. This one trips people up in interviews constantly because the words sound similar but mean very different things. Let's fix that permanently.

## Performance vs Scalability

**Performance** = how fast your system responds *right now*, under current load.
**Scalability** = how well your system *maintains* that performance as load grows.

Here's the distinction that matters most:

> A system can be fast but not scalable, and it can be scalable but not (currently) fast.

**The classic definition:** A service is scalable if, when you increase resources (servers, CPU, memory), it results in a proportional increase in performance/capacity.

### A concrete example

Imagine a single Postgres database serving your app.

- At 100 users: queries return in 20ms. **Performance: excellent.**
- At 100,000 users: same queries now take 4 seconds because the single machine is CPU/IO-bound. **Performance degraded because the system doesn't scale.**

Now imagine you'd built it with sharding and read replicas from day one.

- At 100 users: maybe queries take 30ms (a bit slower due to added complexity — network hops, routing logic). **Performance: slightly worse.**
- At 100,000 users: queries still return in ~35ms because load is spread across many machines. **Performance held steady — the system scaled.**

This is why "just make it fast" and "just make it scale" are different engineering problems, sometimes even in *tension* — the second system was slightly slower at low load because scalability infrastructure (routing, coordination) adds overhead.

### Table: Performance vs Scalability

| | Performance | Scalability |
|---|---|---|
| **Question it answers** | "How fast is one request?" | "What happens as requests multiply?" |
| **Measured by** | Latency, response time | Throughput retention as load increases, cost per unit of growth |
| **Fixing it means** | Optimizing code, queries, algorithms, caching | Adding capacity horizontally/vertically, removing bottlenecks that block parallelism |
| **Failure symptom** | Slow even at low load | Fast at low load, falls apart at high load |
| **Typical fix** | Better indexing, algorithmic complexity, reduce I/O | Load balancers, sharding, statelessness, horizontal scaling |

A good mental model from interviews: **if a fix works for one server, it's a performance fix. If a fix works by adding more servers, it's a scalability fix.**

---

## Latency vs Throughput

These two are the actual *metrics* you use to quantify performance and scalability.

**Latency** = time to complete a single operation (measured in ms, seconds).
**Throughput** = number of operations completed per unit time (measured in requests/sec, RPS — remember this from Topic 1).

### Analogy: the highway

Think of a highway:

- **Latency** = how long it takes one car to drive from entrance to exit.
- **Throughput** = how many cars pass through per hour.

You can have **low latency, low throughput**: an empty highway where every car drives at top speed, but there just aren't many lanes, so total cars/hour is capped.

You can have **high latency, high throughput**: a busy highway with traffic, where each individual car takes longer (say, due to congestion), but overall thousands of cars get through per hour because there are many lanes running in parallel.

**The key insight:** latency and throughput are not the same axis, and improving one doesn't automatically improve the other — sometimes they trade off against each other.

### Real-world example: batch processing

Say you're processing image uploads.

- **Optimize for latency**: process each image immediately upon upload. User gets their result in 200ms. But if 10,000 images hit at once, the server chokes trying to handle each individually with full priority.
- **Optimize for throughput**: batch images into groups of 100 and process them together every 2 seconds. Individual latency goes *up* (an image might wait up to 2 seconds before processing starts), but total throughput goes *up* significantly because batching amortizes overhead (one DB write instead of 100, one model inference call instead of 100).

This is a genuine trade-off you'll be asked about in interviews: **"Would you rather have low latency or high throughput for this system?"** The answer depends entirely on the use case:

| System type | Priority | Why |
|---|---|---|
| Real-time chat, gaming, video calls | **Latency** | Users feel every millisecond of delay |
| Search autocomplete | **Latency** | Needs to feel instant |
| Batch analytics pipeline (e.g., nightly reports) | **Throughput** | Nobody's watching in real time — total volume processed matters |
| Video encoding service | **Throughput** | Users expect to wait; total videos/hour processed is what matters for cost |
| Payment processing | Both, carefully balanced | Needs to feel fast per-transaction AND handle Black Friday volume |

### How they relate to Performance & Scalability

Putting it all together:

- **Latency** is your primary signal for **performance** (is a single request fast?).
- **Throughput** is your primary signal for **scalability** (can the system handle growing volume?).

So when someone in a system design interview says "let's talk about performance," they usually mean latency. When they say "let's talk about scalability," they usually mean throughput under growing load.

---

### Quick recap table

| Term | What it measures | Improves via |
|---|---|---|
| Performance | Speed of the system currently | Code/query optimization, caching, reducing work per request |
| Scalability | Whether performance holds as load grows | Horizontal scaling, load balancing, statelessness, sharding |
| Latency | Time for one request | Reducing hops, faster algorithms, caching, closer servers (CDN) |
| Throughput | Requests handled per unit time | Parallelism, batching, more servers, async processing |

A useful gut-check question to ask yourself in any interview: **"Am I being asked to make this faster for one user, or make this hold up for a million users?"** That single question tells you whether you're solving a performance/latency problem or a scalability/throughput problem — and it changes your entire design approach.
