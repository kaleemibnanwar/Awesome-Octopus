# Feature Specification: Secure Sandboxed Runtime & AST Guardrails 🔒🛡️

> **Zero-Trust Execution Boundaries, Static AST Validation & Token-Budget Guardrails**  
> Integrated across: `OctopusStudio`, `OctopusMCP-Manager`

---

## 📌 Overview

The **Secure Sandboxed Runtime & AST Guardrails** feature enforces strict execution security, memory limits, and static code verification across all agent-generated scripts, tool dispatches, and live preview rendering pipelines. It ensures that autonomous code generation cannot perform malicious system calls, leak environment credentials, or exhaust host resources.

---

## 🛡 Layered Security Architecture

```
┌────────────────────────────────────────────────────────┐
│               Generated Agent Code / Tool Call         │
└───────────────────────────┬────────────────────────────┘
                            │
                            ▼
┌────────────────────────────────────────────────────────┐
│           Layer 1: Static AST Guardrail Analyzer       │
│  - Blocks banned modules (child_process, fs escape)    │
│  - Detects infinite loops, deep recursion hazards      │
│  - Flags unsafe eval(), Function(), prototype pollution│
└───────────────────────────┬────────────────────────────┘
                            │
                            ▼
┌────────────────────────────────────────────────────────┐
│           Layer 2: Isolated Execution Boundary         │
│  - WebContainer / V8 Isolated-VM Containerization      │
│  - Strict RAM (256MB) & CPU (500ms) Watchdog Limits    │
│  - Virtual in-memory File System (tmpfs)               │
└───────────────────────────┬────────────────────────────┘
                            │
                            ▼
┌────────────────────────────────────────────────────────┐
│         Layer 3: Network & Token Budget Controller     │
│  - Whitelist-only egress domain proxy                  │
│  - Automatic secret masking & token quota enforcement  │
└────────────────────────────────────────────────────────┘
```

---

## ⚙️ Core Technical Capabilities

### 1. Static AST (Abstract Syntax Tree) Verification
- Parses generated JavaScript/TypeScript into an AST via Babel/SWC before execution.
- Immediately blocks disallowed Node.js globals (`process.env`, `require('child_process')`, direct filesystem manipulation outside project root).

### 2. Isolated-VM & WebContainer Runtime
- Runs user-facing code and dynamic transformations in isolated memory segments with zero direct host kernel access.
- Implements strict CPU timeouts and garbage-collected memory boundaries.

### 3. Masked Secret Vaulting
- Environment variables and API keys are stored in encrypted vaults managed by `OctopusMCP-Manager`.
- Secrets are injected at runtime into outbound requests without ever being reflected into LLM context logs or client-side bundles.

### 4. Deterministic TypeScript Type Checker
- Verifies synthesized code against project TypeScript schemas in real time.
- Emits structured compilation errors directly back into the agent DAG for targeted sub-agent fixes before code is saved.

---

## 📊 Security & Isolation Benchmarks

| Parameter | Specification / Metric |
| :--- | :--- |
| **AST Parse & Validation Latency** | `< 4.5ms` per 1,000 LOC |
| **Sandbox Memory Limit** | Default `256 MB` configurable per agent |
| **Execution Watchdog Timeout** | `3,000 ms` default (prevents infinite loops) |
| **Egress Filtering Overhead** | `< 0.8ms` proxy inspection |
| **Vulnerability Detection Rate** | `100%` blocking of unescaped eval/child_process |
