# Functional vs. Non-Functional Requirements

This is really the foundation of Step 1 (Requirements Clarification) from the framework we just covered — so it's worth slowing down on. Getting this distinction right is often what separates a strong system design answer from a weak one, because it forces you to ask the right questions *before* you start drawing boxes.

## Functional Requirements — "What must the system DO?"

These describe the actual **behaviors and features** — the concrete actions a user (or another system) can perform. They answer: *what capabilities does this system need?*

Think of functional requirements as the **verbs** of your system.

**Examples for a Twitter-like system:**
- A user can post a tweet (max 280 characters)
- A user can follow/unfollow another user
- A user can view a home timeline of tweets from people they follow
- A user can like, retweet, and reply to a tweet
- A user can search for tweets by keyword

**Examples for a URL shortener:**
- A user can submit a long URL and receive a shortened one
- A user can visit the shortened URL and get redirected to the original
- A user can optionally set a custom alias
- Links can optionally expire after a set time

Functional requirements are usually the *easy* part to gather — they map directly to product features, and most people naturally start here. The mistake is stopping here.

## Non-Functional Requirements — "How well must the system do it?"

These describe the **qualities and constraints** of the system — the properties that determine whether it survives contact with real-world usage. They're often called "quality attributes" because they don't add new capabilities, they define the *standard* the capabilities must be delivered at.

Think of non-functional requirements as the **adjectives/adverbs** of your system.

**Common categories:**

| Category | Question it answers | Example |
|---|---|---|
| **Performance** | How fast? | Timeline loads in < 200ms |
| **Scalability** | How much growth can it handle? | Support 500M DAU, 10K tweets/sec |
| **Availability** | How much uptime? | 99.99% uptime (four 9s) |
| **Consistency** | How up-to-date must data be across nodes? | Tweet likes can be eventually consistent; account balance must be strongly consistent |
| **Durability** | Can data be lost? | Tweets must never be lost once acknowledged |
| **Latency** | Response time per request | p99 latency < 300ms |
| **Throughput** | Requests handled per unit time | 50K reads/sec, 5K writes/sec |
| **Security** | How is data protected? | Encrypted at rest/in transit, auth required |
| **Maintainability** | How easy to modify/extend? | New feature rollout without downtime |
| **Cost** | Budget constraints | Must run on existing infra budget |

## Why This Distinction Actually Matters

Here's the part that trips people up: **the same functional requirement can lead to wildly different architectures depending on the non-functional requirements attached to it.**

Take "a user can view their home timeline":
- If non-functional requirement = "must be real-time, sub-100ms, for 500M users" → you need precomputed/cached timelines (fan-out-on-write), CDNs, heavy caching layers
- If non-functional requirement = "can tolerate a few seconds delay, for 10K users" → you can compute the timeline on-the-fly at read time (fan-out-on-read) with a simple database query

Same feature. Completely different system. The non-functional requirements are what actually drive your architecture — the functional requirements just tell you *what* to build, not *how*.

This is also why Step 1 in the framework insists you clarify requirements out loud in an interview: guessing wrong on "does this need strong consistency?" can invalidate 20 minutes of design work downstream.

## A Useful Mental Model

> **Functional = the contract with the user.**
> **Non-functional = the contract with reality** (physics of networks, hardware limits, business SLAs).

You can satisfy every functional requirement and still have a failed system if the non-functional requirements aren't met — e.g., an app that "does everything" but takes 8 seconds to load, or falls over at 1,000 users. Conversely, a system that's blazing fast and infinitely scalable but doesn't actually let users post tweets is useless. You need both, but they're evaluated completely differently: functional requirements are testable with a checklist (does X work? yes/no), non-functional requirements are testable with *measurements under load*.

---
