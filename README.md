# Distributed Change Data Capture (CDC) & Migration Engine

A high-performance, transactional data replication engine designed to stream database mutations across live, heterogeneous storage instances with zero application downtime and deterministic human-in-the-loop schema drift validation.

The system leverages a hybrid architecture: a low-latency, multi-threaded C++ Core Engine optimized for network ingestion and lock-free memory buffering, wrapped inside an intelligent Python/FastAPI Automation Workbench that streams live telemetry and manages orchestration checkpoints via WebSockets.

## Core Architecture  Overview         
```             
                      [ Python Mock Traffic Generator ]
                                      │
                                      ▼
                        ┌───────────────────────────┐
                        │ PostgreSQL (Source Prod)  │
                        └─────────────┬─────────────┘
                                      │ (Captures Mutations)
                                      ▼ 
 ┌────────────────────────────────────────────────────────────────────────────────────┐
 │  ┌────────────────────────────────┐               ┌─────────────────────────────┐  │
 │  │  FastAPI Dashboard             │               │  C++ Ingestion Daemon       │  │                                                    
 │  │   • Real-Time Metrics          │ ◄───(IPC)───► │   • Asio TCP Listener       │  │    
 │  │   • Chaos Controls             │   pybind11    │   • Lock-Free Ring Buffer   │  │    
 │  │   • Drift Resolver             │               │   • LSN Checkpoint Logic    │  │   
 │  └────────────────────────────────┘               └──────────────┬──────────────┘  │
 └──────────────────────────────────────────────────────────────────┼─────────────────┘
                                                                    | (Idempotent Replay)
                                                                    ▼
                                                      ┌─────────────────────────────┐
                                                      │ PostgreSQL (Target Replica) │
                                                      └─────────────────────────────┘
```
## Tech Stack & Dependencies
**Core Systems & Engine:** C++20, Asio (Asynchronous Networking), Protocol Buffers v3, CMake

**Orchestration & Control Layer:** Python 3.12, FastAPI, WebSockets, pybind11

**Storage & Ingestion infrastructure:** PostgreSQL 16, Docker, Docker Compose

**Testing & Telemetry Verification:** PyTest, Faker, Locust (Distributed Stress Testing)
