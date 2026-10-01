# Feature Specification: MCP Fleet Mesh & Dynamic Tool Routing 🌐🛡️

> **Enterprise-Scale Model Context Protocol Mesh, Dynamic Tool Discovery & Isolation Scoping**  
> Integrated across: `OctopusMCP-Manager`, `OctopusStudio`, `octocut`, `octolimb`

---

## 📌 Overview

The **MCP Fleet Mesh** delivers universal connectivity across tools, data sources, and hardware controllers using the standardized Model Context Protocol (MCP). Rather than hardcoding static APIs or overwhelming LLMs with massive tool schemas, the mesh dynamically indexes, filters, vaults, and executes tool calls across local and distributed microservices.

---

## 🏛 Tool Discovery & Routing Pipeline

```
┌─────────────────────────┐
│     AI Model Prompt     │
└────────────┬────────────┘
             │ Context & User Intention
             ▼
┌────────────────────────────────────────────────────────┐
│               OctopusMCP-Manager Router                │
│  1. Vector Similarity Match over Tool Catalog          │
│  2. Token Budget Sizing (Max 1,500 tokens)             │
│  3. Agent Role & Permission Filter (RBAC)              │
└────────────┬───────────────────────────────────────────┘
             │ Dynamic Scoped Tool Definitions (JSON-RPC)
             ▼
┌─────────────────────────┐
│  AI Generates Tool Call │
└────────────┬────────────┘
             │
             ▼
┌────────────────────────────────────────────────────────┐
│              Zero-Trust Execution Proxy                │
│  - Parameter Schema Validation (Zod)                   │
│  - Secret Decryption from Vault                        │
│  - Rate Limiting & Audit Log Generation                │
└────────────┬───────────────────────────────────────────┘
             │
     ┌───────┴───────┬───────────────┬───────────────┐
     ▼               ▼               ▼               ▼
┌──────────┐   ┌───────────┐   ┌───────────┐   ┌───────────┐
│ octocut  │   │ octolimb  │   │ Studio FS │   │ PostgreSQL│
│ (Media)  │   │(Robotics) │   │ (Editor)  │   │ (Database)│
└──────────┘   └───────────┘   └───────────┘   └───────────┘
```

---

## ⚙️ Core Technical Capabilities

### 1. Vector-Indexed Dynamic Tool Pruning
- Traditional LLM agents face context window bloat when dozens of tool schemas are injected into every prompt.
- The MCP Mesh uses cosine similarity against tool function embeddings, injecting only the **top-K most relevant tools** (typically 3–5 tools) based on the current subtask goal.

### 2. Multi-Transport Agility
- Native dual-channel support for:
  - **STDIO**: Fast local child-process execution.
  - **SSE (Server-Sent Events)**: Asynchronous HTTP streaming for cloud services and webhooks.
  - **WebSocket / gRPC**: Real-time bi-directional streaming for low-latency robotics (`octolimb`) and video streams (`octocut`).

### 3. Role-Based Access Control (RBAC) & Hard Sandboxing
- Strict segregation of tool permissions based on active agent role:
  - `explorer`: Restricted to `read_file`, `grep`, `octocut_inspect_metadata`, `octolimb_get_telemetry`.
  - `executor`: Permitted to invoke `search_replace`, `write_file`, `octocut_slice_clip`, `octolimb_move_to_pose`.
  - `admin`: Full system privileges, server lifecycle management, and credential updates.

### 4. Circuit Breaking & Fault Isolation
- When an individual MCP backend encounters socket drops, timeout spikes, or process crashes, the mesh isolates the faulty node, returns a clean error envelope to the calling agent, and spins up a fallback replica.

---

## 📋 Mesh Configuration Example

```yaml
# mesh-policy.yaml
security_level: "enterprise-strict"
token_budget_per_turn: 1500
rate_limits:
  global: "1000/min"
  hardware_actuation: "30/min"

audit_pipeline:
  destination: "elasticsearch://audit-cluster:9200"
  mask_patterns: ["*token*", "*key*", "*password*", "*secret*"]

nodes:
  - id: "octocut-cluster"
    provider: "mcp-service"
    transport: "sse"
    url: "http://octocut.mesh.local/mcp"
    health_check_interval_sec: 5

  - id: "octolimb-factory-cell-1"
    provider: "mcp-edge"
    transport: "grpc"
    endpoint: "10.0.4.15:9000"
    require_physical_estop: true
```

---

## 📊 Performance Benchmark

| Benchmark | Value | Target SLA |
| :--- | :--- | :--- |
| **Tool Resolution Latency** | `1.8ms` | `< 5.0ms` |
| **Tool Call Dispatch Latency** | `3.1ms` | `< 10.0ms` |
| **Context Token Savings** | **84% reduction** vs static tool dumps | `> 70%` |
| **Simultaneous Active Nodes** | 256 MCP servers per manager node | `100+` |
