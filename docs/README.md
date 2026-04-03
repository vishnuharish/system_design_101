# 🚀 System Design in 3 Months

> Learn system design from zero to interview-ready. Each day has theory, real-world analogies, and hands-on code in both JavaScript and Python.

## 📊 Course Overview

| | Month 1 | Month 2 | Month 3 |
|-|---------|---------|---------|
| **Focus** | Foundations | Security & Architecture | Design Practice |
| **Days** | 1–30 | 31–60 | 61–90 |
| **Weeks** | W1–W4 | W5–W8 | W9–W12 |

---

## 📅 Full Curriculum

### Month 1: Foundations


**Week 1: Basics of System Design**

| [Day 01](day_01.md) | Introduction to System Design | *Why systems break* |
| [Day 02](day_02.md) | Client-Server Architecture | *How browsers talk to the internet* |
| [Day 03](day_03.md) | APIs and HTTP Methods | *The universal language* |
| [Day 04](day_04.md) | REST vs GraphQL | *Two philosophies for API design* |
| [Day 05](day_05.md) | SQL Databases | *Storing data in structured relational tables* |
| [Day 06](day_06.md) | NoSQL Databases | *Flexible storage for modern high-scale apps* |
| [Day 07](day_07.md) | Week 1 Project — Grade Tracker API | *Build a complete REST API from scratch* |

**Week 2: Performance and Infrastructure**

| [Day 08](day_08.md) | Caching Fundamentals | *Never compute the same thing twice* |
| [Day 09](day_09.md) | Redis — Distributed Caching | *The Swiss Army knife of backend infrastructure* |
| [Day 10](day_10.md) | CDN — Content Delivery Networks | *Serve content from the nearest server globally* |
| [Day 11](day_11.md) | Load Balancing | *Distribute traffic so no single server drowns* |
| [Day 12](day_12.md) | Horizontal vs Vertical Scaling | *Growing your system the right way* |
| [Day 13](day_13.md) | DNS and How the Internet Works | *Translating names to addresses across the internet* |
| [Day 14](day_14.md) | Week 2 Project — Add Caching Layer | *Supercharge your Grade Tracker with Redis* |

**Week 3: Data Layer Deep Dive**

| [Day 15](day_15.md) | Database Indexing | *Make your queries 1000x faster* |
| [Day 16](day_16.md) | Database Replication | *Copies everywhere for reliability and read performance* |
| [Day 17](day_17.md) | Database Sharding | *Split your database horizontally for unlimited scale* |
| [Day 18](day_18.md) | ACID Transactions | *Guaranteeing data integrity in concurrent systems* |
| [Day 19](day_19.md) | CAP Theorem | *The impossible triangle of distributed systems* |
| [Day 20](day_20.md) | Consistent Hashing | *Minimise disruption when nodes join or leave* |
| [Day 21](day_21.md) | Week 3 Project — Sharded DB Simulator | *Build a multi-shard routing layer with consistent hashing* |

**Week 4: Communication Patterns**

| [Day 22](day_22.md) | HTTP vs WebSockets vs gRPC | *Choosing the right communication protocol* |
| [Day 23](day_23.md) | Message Queues | *Async communication that decouples services* |
| [Day 24](day_24.md) | Publish-Subscribe Pattern | *Decoupled event broadcasting at massive scale* |
| [Day 25](day_25.md) | API Gateway | *The single front door for all microservice traffic* |
| [Day 26](day_26.md) | Event-Driven Architecture | *Building reactive systems that respond to events* |
| [Day 27](day_27.md) | Service Mesh | *Managing inter-service communication automatically* |
| [Day 28](day_28.md) | Month 1 Project Day 1 — Twitter Clone Architecture | *Plan the system before writing code* |
| [Day 29](day_29.md) | Month 1 Project Day 2 — Twitter Clone Implementation | *Build the tweet and feed services* |
| [Day 30](day_30.md) | Month 1 Review and Interview Prep | *Consolidate and practice what you have learned* |

### Month 2: Security and Architecture


**Week 5: Security Essentials**

| [Day 31](day_31.md) | Authentication with JWT and OAuth | *Proving who you are to a system* |
| [Day 32](day_32.md) | Authorization and RBAC | *Controlling what each user can do* |
| [Day 33](day_33.md) | Advanced Rate Limiting | *Protecting APIs from abuse and bots* |
| [Day 34](day_34.md) | Encryption and HTTPS | *Keeping data safe in transit and at rest* |
| [Day 35](day_35.md) | Common Attack Vectors | *SQL Injection, XSS, CSRF, and how to stop them* |
| [Day 36](day_36.md) | DDoS Protection and Bot Prevention | *Keeping your system alive under attack* |
| [Day 37](day_37.md) | Week 5 Project — Secure API | *Add JWT auth, RBAC, and rate limiting to your app* |

**Week 6: Microservices Architecture**

| [Day 38](day_38.md) | Monolith vs Microservices | *One big app or many focused small services* |
| [Day 39](day_39.md) | Service Discovery | *How microservices find each other dynamically* |
| [Day 40](day_40.md) | Circuit Breaker Pattern | *Stop cascading failures from bringing down everything* |
| [Day 41](day_41.md) | Saga Pattern | *Distributed transactions without two-phase commit* |
| [Day 42](day_42.md) | API Versioning | *Evolving your API without breaking existing clients* |
| [Day 43](day_43.md) | Containerisation with Docker | *Package and run apps consistently anywhere* |
| [Day 44](day_44.md) | Week 6 Project — Microservice Chat App | *Split a monolith into three independent services* |

**Week 7: Distributed Systems**

| [Day 45](day_45.md) | Distributed Systems Fundamentals | *Unique challenges of systems spanning many machines* |
| [Day 46](day_46.md) | Leader Election | *Choosing a coordinator in a distributed system* |
| [Day 47](day_47.md) | Consensus Algorithms — Raft | *Getting distributed nodes to agree* |
| [Day 48](day_48.md) | Distributed Locking | *Coordinating writes across multiple servers safely* |
| [Day 49](day_49.md) | Clocks and Ordering in Distributed Systems | *Time is fundamentally different across machines* |
| [Day 50](day_50.md) | Fault Tolerance and Chaos Engineering | *Designing systems that expect failure* |
| [Day 51](day_51.md) | Week 7 Project — Distributed Counter | *A counter that works correctly across 3 nodes* |

**Week 8: Advanced Storage**

| [Day 52](day_52.md) | Object Storage | *Storing files and blobs at massive scale* |
| [Day 53](day_53.md) | Block vs File vs Object Storage | *Choosing the right storage primitive* |
| [Day 54](day_54.md) | Data Warehousing | *Analytics at scale with columnar storage* |
| [Day 55](day_55.md) | Column-Oriented Databases | *How Cassandra and BigTable scale to petabytes* |
| [Day 56](day_56.md) | Time-Series Databases | *Storing and querying metrics and events over time* |
| [Day 57](day_57.md) | Full-Text Search with Elasticsearch | *Powerful search beyond simple database queries* |
| [Day 58](day_58.md) | Month 2 Project Day 1 — E-Commerce Core Services | *Build product, cart, and inventory services* |
| [Day 59](day_59.md) | Month 2 Project Day 2 — Orders and Payments | *Transactions, sagas, and payment flows* |
| [Day 60](day_60.md) | Month 2 Review and Interview Practice | *Consolidate Month 2 and run mock questions* |

### Month 3: Design Practice


**Week 9: Classic System Designs**

| [Day 61](day_61.md) | Design a URL Shortener | *Like bit.ly — long URLs become short ones* |
| [Day 62](day_62.md) | Design a Pastebin | *Store and share text snippets publicly* |
| [Day 63](day_63.md) | Design Instagram — Photo Sharing | *Store and serve billions of photos* |
| [Day 64](day_64.md) | Design Twitter — Social Media Feed | *Timeline at scale for hundreds of millions of users* |
| [Day 65](day_65.md) | Design WhatsApp — Messaging at Scale | *Real-time messaging for 2 billion users* |
| [Day 66](day_66.md) | Design YouTube — Video Streaming | *Upload, process, and stream video globally* |
| [Day 67](day_67.md) | Week 9 Patterns Review | *Common patterns across all classic system designs* |

**Week 10: Complex Real-World Systems**

| [Day 68](day_68.md) | Design a Search Engine | *Web crawling, indexing, and ranking at internet scale* |
| [Day 69](day_69.md) | Design Google Maps | *Navigation and real-time location services* |
| [Day 70](day_70.md) | Design Uber — Ride Sharing | *Matching drivers and riders in real time* |
| [Day 71](day_71.md) | Design Swiggy — Food Delivery | *Multi-party coordination at city scale* |
| [Day 72](day_72.md) | Design Amazon — E-Commerce at Scale | *Inventory, flash sales, and personalisation* |
| [Day 73](day_73.md) | Design Google Drive — File Storage | *Store, sync, and collaborate on files* |
| [Day 74](day_74.md) | Week 10 Patterns Review | *Geospatial, multi-party, and real-time matching systems* |

**Week 11: Advanced Design Problems**

| [Day 75](day_75.md) | Design a Notification System | *Reliable push, email, and SMS at massive scale* |
| [Day 76](day_76.md) | Design a Distributed Rate Limiter | *Rate limiting that works correctly across servers* |
| [Day 77](day_77.md) | Design a Distributed Cache | *Cache that scales to hundreds of nodes* |
| [Day 78](day_78.md) | Design a Web Crawler | *Index the entire internet systematically* |
| [Day 79](day_79.md) | Design Typeahead and Autocomplete | *Real-time search suggestions as you type* |
| [Day 80](day_80.md) | Design a Payment System | *Handling money safely, reliably, and securely* |
| [Day 81](day_81.md) | Week 11 Project — Full Design Document | *Write a production-level system design document* |

**Week 12: Production and Interview Prep**

| [Day 82](day_82.md) | Monitoring and Observability | *Know what is happening in your system at all times* |
| [Day 83](day_83.md) | Logging Systems at Scale | *Centralised logging for distributed systems* |
| [Day 84](day_84.md) | Disaster Recovery and Business Continuity | *Preparing for the worst case* |
| [Day 85](day_85.md) | Cost Optimisation in System Design | *Building systems that are efficient and cheap to run* |
| [Day 86](day_86.md) | System Design Interview Framework | *How to structure your answer in 45 minutes* |
| [Day 87](day_87.md) | Mock Interview 1 — URL Shortener | *Full practice interview with evaluation* |
| [Day 88](day_88.md) | Mock Interview 2 — Chat Application | *Full practice interview with evaluation* |
| [Day 89](day_89.md) | Mock Interview 3 — Social Media Feed | *Full practice interview with evaluation* |
| [Day 90](day_90.md) | Graduation and What Comes Next | *Celebrate your achievement and plan the road ahead* |

---

## 🗺️ Learning Path

```
Month 1 (Days 1-30): Foundations
├── Week 1: APIs, HTTP, SQL, NoSQL
├── Week 2: Caching, CDN, Load Balancing, DNS
├── Week 3: DB Indexing, Replication, Sharding, CAP
└── Week 4: Message Queues, Events, API Gateway

Month 2 (Days 31-60): Security & Architecture
├── Week 5: JWT, RBAC, Encryption, Attacks
├── Week 6: Microservices, Docker, Circuit Breaker
├── Week 7: Distributed Systems, Raft, Locking
└── Week 8: Object Storage, Elasticsearch, Data Warehouse

Month 3 (Days 61-90): Design Practice
├── Week 9: URL Shortener, Instagram, Twitter, YouTube
├── Week 10: Google Maps, Uber, Swiggy, Amazon
├── Week 11: Notifications, Rate Limiter, Web Crawler
└── Week 12: Monitoring, DR, Interview Framework, Mock Interviews
```

---

## 🛠️ Prerequisites

- Basic programming knowledge (Python or JavaScript)
- Curiosity and willingness to think at scale

## 📚 Recommended Books

- *Designing Data-Intensive Applications* — Martin Kleppmann
- *System Design Interview Vol 1 & 2* — Alex Xu
- *The Art of Scalability* — Martin Abbott & Michael Fisher

---

*Built for curious minds. Happy learning! 🎓*
