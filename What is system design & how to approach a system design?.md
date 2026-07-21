# What is System Design?

**System design** is the process of defining the architecture, components, modules, interfaces, and data flow of a system to satisfy specific requirements — both functional (what it does) and non-functional (how well it does it: speed, reliability, scale).

Think of it like designing a building. Anyone can stack bricks to make a wall, but an architect has to think about:
- How many people will use this building? (scale)
- What happens during an earthquake? (failure handling)
- Should we prioritize open floor plans or private offices? (trade-offs)
- How do plumbing, electrical, and structural systems all fit together without conflicting? (component interaction)

System design is the same exercise, but for software: you're deciding how services, databases, caches, queues, and networks fit together so the system works *at scale* and *survives failure*, not just in a demo.

It matters for three main reasons:
1. **Real-world systems must handle scale** — millions of users, requests per second, terabytes of data — which a simple "make it work" solution won't survive.
2. **Trade-offs are unavoidable** — there's no universally "correct" architecture, only the right architecture for a given set of constraints (cost, team size, latency needs, consistency needs).
3. **It's the core interview and career skill** for senior engineering roles, because it tests judgment, not just syntax.

---

## How to Approach a System Design Problem

There's no single "correct" design for something like "design Twitter" — there are better and worse designs *relative to a set of requirements*. So the approach is really a structured way of narrowing an infinite design space down to a good, defensible one. A commonly used six-step framework:

### 1. Requirements Clarification
Before designing anything, pin down:
- **Functional requirements** — what must the system do? (e.g., users can post tweets, follow others, see a feed)
- **Non-functional requirements** — what qualities matter? (low latency, high availability, strong vs. eventual consistency, durability)
- **Scope** — what's explicitly out of scope? (You can't design everything; state your boundaries.)

This step is the one people skip most often — and it's the one that determines whether the rest of your design even makes sense. Jumping straight to "I'll use Kafka and Redis" without knowing the read/write ratio is building on sand.

### 2. Scale Estimation (Back-of-the-Envelope Math)
Rough numbers that shape every later decision:
- Users: DAU (Daily Active Users) / MAU (Monthly Active Users)
- Traffic: requests per second (RPS), read/write ratio
- Storage: data size per record × number of records × retention period
- Bandwidth: data in/out per second

You don't need precision — you need *order of magnitude*. Knowing "we need to handle ~50K RPS" vs. "~50 RPS" completely changes whether you need a single database or a globally distributed, sharded, cached architecture.

### 3. High-Level Architecture (Draw the Boxes)
Sketch the major components and how data flows between them:
- Clients → Load Balancer → Application Servers → Database
- Where do caches sit? Where do queues sit? Where's the CDN?

At this stage you're establishing the skeleton — don't dive into implementation details of any one box yet.

### 4. Component Deep Dives
Now zoom into the pieces that matter most for *this specific problem*. For a URL shortener, that's the ID-generation scheme and the database schema. For a chat app, that's the real-time messaging layer (WebSockets, pub/sub) and message ordering/delivery guarantees. Not every component deserves equal depth — go deep where the interesting engineering challenges actually are.

### 5. Identify Failure Modes & Bottlenecks
Ask "what breaks this, and how do we survive it?":
- Single points of failure (SPOFs)
- What happens when a server dies mid-request?
- What happens when traffic spikes 10x?
- What happens when the database can't keep up?

This is where you show you're not just designing for the happy path.

### 6. Trade-off Discussion
Every design decision costs something. Be explicit about what you're trading:
- SQL (strong consistency, joins) vs. NoSQL (horizontal scalability, flexible schema)
- Strong consistency (correctness) vs. availability (uptime) — this is the CAP theorem in practice
- Caching (speed) vs. staleness (data can be outdated)
- Microservices (independent scaling/deployment) vs. monolith (simplicity, fewer moving parts)

Good system design isn't about knowing the "best" answer — it's about reasoning transparently about trade-offs given the constraints from Step 1.

---

### Quick sanity check
| Step | Question you're answering |
|---|---|
| 1. Requirements | What are we actually building, and for whom? |
| 2. Scale estimation | How big is "big" here? |
| 3. High-level design | What are the major moving pieces? |
| 4. Deep dive | How does the hard part actually work? |
| 5. Failure modes | What breaks, and how do we recover? |
| 6. Trade-offs | What did we give up, and why was it worth it? |

---

You've been through this framework before as our starting point — want to move on to the next topic in the sequence, **Performance vs. Scalability and Latency vs. Throughput**? That's where these six steps start getting applied concretely.
