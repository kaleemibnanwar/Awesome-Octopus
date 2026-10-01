# Product Specification: OctopusStudio 🐙💻

> **The Next-Generation AI-Native Multi-Agent IDE & Visual Real-Time Code Synthesis Platform**  
> Repository: [github.com/kaleemibnanwar/OctopusStudio](https://github.com/kaleemibnanwar/OctopusStudio)

---

## 📌 Executive Summary

**OctopusStudio** is an interactive multi-agent software engineering environment designed for real-time application synthesis, autonomous code generation, sandboxed execution, and live hot-reloading previews. It bridges the gap between natural language intention, Directed Acyclic Graph (DAG) task orchestration, and deterministic software engineering.

OctopusStudio empowers developers and autonomous AI agents to collaborate seamlessly inside an integrated browser-based IDE featuring isolated execution sandboxes, dynamic tool injection, and bidirectional live previews.

---

## 🏛 Architecture Overview

```
                      ┌─────────────────────────────────────────┐
                      │             OctopusStudio UI            │
                      │   (React / TypeScript / Tailwind CSS)   │
                      └────────────────────┬────────────────────┘
                                           │
                        ┌──────────────────┴──────────────────┐
                        │   Multi-Agent Orchestration Engine  │
                        │    (Planner / Executor / Verifier)   │
                        └──────────────────┬──────────────────┘
                                           │
         ┌─────────────────────────────────┼─────────────────────────────────┐
         ▼                                 ▼                                 ▼
┌──────────────────┐             ┌───────────────────┐             ┌──────────────────┐
│  DAG Task Graph  │             │   MCP Tool Mesh   │             │ In-Browser/Node  │
│  State Machine   │             │   Client Layer    │             │ Sandboxed Runner │
└────────┬─────────┘             └─────────┬─────────┘             └────────┬─────────┘
         │                                 │                                │
         └─────────────────────────────────┼────────────────────────────────┘
                                           ▼
                               ┌───────────────────────┐
                               │ Real-Time Live Preview│
                               │  (Hot Module Reload)  │
                               └───────────────────────┘
```

---

## 🚀 Key Features

### 1. Dual-Plane Agent & Human Collaboration
- **Human-in-the-Loop IDE**: Simultaneous code editor, terminal logs, git provenance tracking, and interactive iframe preview.
- **Autonomous Role Specialization**: Dynamic sub-agent spawning across roles:
  - `explorer`: Read-only indexing, AST code navigation, dependency analysis.
  - `executor`: Targeted surgery via line-based `search_replace` and complete atomic file generation.
  - `debugger`: Automated log trace ingestion, breakpoint simulation, and root-cause analysis.
  - `verifier`: Strict TypeScript type checks, unit testing, and layout regression checks.

### 2. Deterministic DAG Task Scheduling
- Asynchronous sub-task resolution with built-in cycle detection and dependency bubbling.
- Real-time task progress reporting and dynamic graph recalculation upon test failures.

### 3. Native Model Context Protocol (MCP) Integration
- Direct interoperability with `OctopusMCP-Manager` for dynamic tool discovery (databases, APIs, shell, file systems, robotics).
- Token-budget-aware tool schema injection preventing LLM context bloat.

### 4. Zero-Friction Live Execution Engine
- In-memory bundler and hot-module reload engine updating the DOM in `< 50ms`.
- Strict isolation boundary isolating untrusted runtime scripts from host privileges.

---

## 🔌 Interconnection Interface

OctopusStudio serves as the **central cognitive & orchestration control plane** across the Octopus ecosystem:

```yaml
# octopus-studio-connection-manifest.yaml
ecosystem:
  hub: "OctopusStudio"
  connectors:
    mcp_gateway:
      target: "OctopusMCP-Manager"
      protocol: "mcp-jsonrpc/2.0"
      endpoint: "http://localhost:8080/mcp"
      capabilities: ["tool_discovery", "dynamic_routing", "secret_vaulting"]
    
    media_pipeline:
      target: "octocut"
      protocol: "grpc/rest"
      endpoint: "http://localhost:5050/media"
      capabilities: ["video_slicing", "audio_transcription", "auto_subtitles"]
      
    hardware_runtime:
      target: "octolimb"
      protocol: "ws/telemetry"
      endpoint: "ws://localhost:9000/actuation"
      capabilities: ["spatial_telemetry", "robotic_kinematics", "sensor_stream"]
```

---

## 💻 Code Example: Dispatching Multi-Tool Agent Tasks

```typescript
import { OctopusStudioAgent, TaskDAG } from '@octopus/studio-sdk';

const studio = new OctopusStudioAgent({
  apiKey: process.env.OCTOPUS_API_KEY,
  mcpManagerUrl: 'http://localhost:8080',
});

// Define DAG bridging code synthesis and media processing
const taskDAG: TaskDAG = {
  goal: "Build interactive video trimmer dashboard and connect to octocut engine",
  subtasks: [
    {
      id: "scaffold-ui",
      role: "executor",
      goal: "Create React timeline scrubbing component with Tailwind",
      allowed_files: ["src/components/Timeline.tsx", "src/pages/Index.tsx"]
    },
    {
      id: "bind-octocut-mcp",
      role: "executor",
      goal: "Generate client hook communicating with octocut video slicing endpoint",
      depends_on: ["scaffold-ui"],
      allowed_files: ["src/hooks/useVideoSlice.ts"]
    },
    {
      id: "typecheck-and-verify",
      role: "verifier",
      goal: "Run TypeScript checks and verify render pipeline in sandbox",
      depends_on: ["bind-octocut-mcp"]
    }
  ]
};

const result = await studio.executeDAG(taskDAG);
console.log(`Execution status: ${result.status}`);
```

---

## 📊 Technical Specifications & Metrics

| Attribute | Specification |
| :--- | :--- |
| **Language Runtime** | Node.js 20+, TypeScript 5.4+, React 18 / Vite |
| **Execution Sandboxing** | Web Workers, Isolated VM, WebContainers |
| **Hot Reload Latency** | `< 45ms` for React components |
| **DAG Concurrency** | Up to 16 parallel sub-agents |
| **MCP Protocol Support** | JSON-RPC 2.0 (stdio, SSE, HTTP streaming) |
| **Memory Footprint** | `< 120MB` base idle RAM |
