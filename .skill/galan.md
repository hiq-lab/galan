---
name: Galan — Quantum Circuit Intelligence
description: Guides Claude to analyze quantum circuits, predict QPU hardness, compare backends, and search 7,000+ quantum computing papers using the Galan MCP server.
mcp_server: https://garm.valiant-quantum.com/mcp
version: 1.0.0
author: Valiant Quantum UG (haftungsbeschränkt)
license: MIT
---

## What Galan Does

Galan is a **structured-JSON-only** quantum intelligence engine — it returns no prose, only machine-readable results. Claude does all synthesis. This Skill teaches Claude how to interpret Galan's output correctly and present it in the most useful way.

Galan exposes five tools:

| Tool | What it returns |
|---|---|
| `compile_circuit` | Circuit profile, hardness score, backend compatibility |
| `run_circuit` | Execution results, measurement counts |
| `list_backends` | Available QPU backends with specs |
| `get_pricing` | Pricing comparison across backends |
| `research` | Semantic search over 7,000+ quantum computing papers |

---

## Core Interpretation Rules

### 1. Always lead with the hardness verdict

When `compile_circuit` returns, **always state `interpretation_key` first** before presenting numbers. The four keys map to plain-language recommendations:

| `interpretation_key` | Plain language | Recommended action |
|---|---|---|
| `trivial_classical_or_qpu` | Classically simulable, negligible QPU cost | Use a simulator |
| `moderate_qpu_feasible` | Good candidate for current NISQ hardware | Run on trapped-ion or test on simulator |
| `hard_limited_backends` | Only all-to-all backends survive | Quantinuum H2 or IonQ Forte only |
| `intractable_nisq` | Beyond all current NISQ hardware | Fault-tolerant era only |

### 2. Explain SWAP routing overhead

When `estimated_transpiled_2q_gates` exceeds the circuit's native 2Q gate count, flag this explicitly. It means the backend's planar topology can't natively route the circuit — SWAP gates are inserted, multiplying error.

**Rule of thumb:** Every +50% in transpiled 2Q gates costs roughly 0.4–0.5 in fidelity on superconducting hardware. If fidelity drops below 0.55, tell the user the result will be statistically indistinguishable from noise.

### 3. Translate pricing into plain language

Quantum backends use incompatible billing models. Always convert to a common unit (cost per 1000-shot run) when presenting pricing:

- **Quantinuum HQC:** 1 HQC ≈ £6.50. Circuit cost = f(qubit-count, 2Q gates, measurement).
- **IonQ per-gate:** straightforward — multiply gate count × rate.
- **IBM per-second:** estimate ~0.1s per 1000-shot run for typical circuits.
- **IQM per-shot:** multiply directly.

Never leave users with raw pricing units without translating them.

### 4. Cross-reference research against circuit class

When `algorithm_class` is returned by `compile_circuit`, it maps to relevant literature:

| `algorithm_class` | Research search suggestion |
|---|---|
| `qaoa` | "QAOA hardness Max-Cut approximation ratio" |
| `vqe` | "VQE ansatz expressibility noise NISQ" |
| `variational` | "variational quantum circuit barren plateau" |
| `qft` | "quantum Fourier transform fault tolerant" |
| `grover` | "Grover search quadratic speedup amplitude amplification" |
| `unknown` | Search by dominant gate type or problem structure |

### 5. Handle research tool failures gracefully

The `research` tool requires Scholar DB connectivity. If the response contains:
```json
{"note": "Scholar DB not connected in remote MCP mode."}
```
Tell the user directly — do not attempt to fill in paper results from memory or hallucinate citations.

---

## Presentation Guidelines

### compile_circuit response structure
1. **Hardness verdict** (interpretation_key + plain language) — always first
2. **Circuit profile** (qubits, depth, gate counts, algorithm_class)
3. **Hardness score** with visual scale: trivial → moderate → hard → intractable
4. **Backend table** sorted by fidelity descending, flagging SWAP overhead
5. **Recommendation** — one clear sentence on what to do next

### list_backends response structure
- Group by technology: Trapped Ion / Superconducting / Photonic
- Lead with connectivity type (all-to-all vs planar) — this is the dominant factor for complex circuits

### research response structure
- Lead with the single most relevant result and why it's relevant
- State confidence scores (80% agent / 40% heuristic)
- Link arXiv IDs directly

---

## Example Interactions

**Good:**
> User: "Can I run this 20-qubit VQE circuit on IBM?"
>
> Claude calls `compile_circuit`, gets `hard_limited_backends` + IBM fidelity 0.501.
>
> Claude responds: "This circuit exceeds IBM's routing capacity — the planar heavy-hex topology requires 50% extra SWAP gates, collapsing fidelity to ~0.50, which is statistically indistinguishable from noise. You'd need Quantinuum H2 (fidelity 0.91) or a simulator."

**Bad:**
> Claude presents raw JSON fields without interpreting hardness_score or explaining why IBM fidelity is low.

---

## Notes for Skill Consumers

- Galan returns `null` for many `meta` fields in standard MCP mode (`noise_summary`, `session_ref`, `calibration_id`). These are populated in direct REST API mode. Do not flag null meta fields as errors.
- The `research` tool's `corpus_size` in `meta.backend_diagnostics` reflects the connected index — currently a curated quantum computing subset, not the full 7,000+ until Scholar DB is fully connected via MCP.
- `cp` (controlled-phase) gates are now in Galan's decomposition table (fixed 2026-03-28). Each `cp` decomposes to 2 CX gates on superconducting backends. Fidelity estimates account for this decomposition overhead.
