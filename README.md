# Galan

**Quantum computing for your AI assistant.**

Galan is an MCP server that gives Claude access to quantum computing — circuit analysis, paper search, QUBO solving, hardware pricing, and circuit execution on real QPUs.

No SDK to learn. No API docs to read. Just connect and ask.

## Connect

### Claude.ai / Claude Desktop (Remote)

Go to [claude.ai/settings/connectors](https://claude.ai/settings/connectors) and add:

```
https://garm.valiant-quantum.com/mcp
```

Select **Streamable HTTP** as transport type. Six tools become available immediately.

### Claude Desktop (Local Client)

Alternatively, install the local client:

```bash
pip install galan-mcp
galan init
```

This writes the config to `claude_desktop_config.json` automatically. Restart Claude Desktop.

## Tools

### `compile_circuit`

Analyze a quantum circuit. Accepts QASM3, Qiskit, PennyLane, or Cirq code.

Returns: circuit profile (qubits, gates, depth, algorithm class), hardness prediction via the N(N−1)/2 pair-counting bound, optimization suggestions, and backend compatibility with estimated fidelity.

**Example prompt:** *"Analyze this QAOA circuit and tell me which hardware can run it."*

### `research`

Search 7,000+ quantum computing papers from arXiv, Qiskit, PennyLane, Classiq, D-Wave, and peer-reviewed publications.

Returns: matched papers with confidence scores, algorithm classifications, key findings, and framework references.

**Example prompt:** *"Find papers on VQE ansatz optimization for molecular simulation."*

### `solve_qubo`

Solve a QUBO (Quadratic Unconstrained Binary Optimization) problem with hardness analysis and solver comparison.

Returns: best solution, objective value, solver comparison (simulated annealing vs greedy), hardness prediction, and QPU recommendation.

**Example prompt:** *"Solve this 10×10 QUBO matrix and tell me if a quantum computer would help."*

### `get_pricing`

Compare quantum hardware pricing across all providers. Optionally pass a circuit to get a concrete cost comparison.

Returns: per-provider pricing models, Aleta index, merit-order ranking, and circuit-specific cost estimates.

**Example prompt:** *"How much would it cost to run a 14-qubit VQE circuit on different backends?"*

### `list_backends`

List all available quantum backends with specifications.

Returns: backend IDs, technology type, circuit budget, gate fidelity, connectivity info.

**Example prompt:** *"Which quantum computers are available right now?"*

### `run_circuit`

Run a quantum circuit on a QPU or simulator. Accepts QASM3, Qiskit, PennyLane, or Cirq code.

Returns: measurement counts, probabilities, statistics (entropy, dominant states), execution metadata.

**Example prompt:** *"Run this circuit on the simulator with 4096 shots."*

## Hardness Prediction

Galan includes the N(N−1)/2 gate-count bound for circuit hardness estimation:

> For any Hamiltonian with kinetic energy p² in N-qubit binary encoding, the free propagator requires exactly N(N−1)/2 entangling gates. This structural bound predicts circuit feasibility on current hardware before compilation.

Reference: Hinderink, D. (2026). *Pair-Counting Scaling of the Quantum Chaos Threshold.* Valiant Quantum.

## Available Backends

| Backend | Technology | Qubits | 2Q Fidelity | Connectivity |
|---|---|---|---|---|
| Quantinuum H2 | Trapped ion | 56 | 99.9% | All-to-all |
| IonQ Forte | Trapped ion | 36 | 99.7% | All-to-all |
| IonQ Aria | Trapped ion | 25 | 99.5% | All-to-all |
| AQT IBEX Q1 | Trapped ion | 12 | 98.9% | All-to-all |
| IBM Torino | Superconducting | 133 | 99.5% | Heavy-hex |
| IBM Marrakesh | Superconducting | 156 | 99.5% | Heavy-hex |
| Rigetti Ankaa-3 | Superconducting | 84 | 99.5% | Square-octagon |
| IQM Garnet | Superconducting | 20 | 99.0% | Square lattice |
| Simulator | Classical | Unlimited | 100% | All-to-all |

## Pricing

Galan normalizes pricing across providers using the **Aleta Index** (EUR/Aleta). Current rates:

- **IBM** — Free via Open Access (10 min/month QPU time)
- **IQM** — $0.30/task + $0.00145/shot (AWS Braket)
- **IonQ** — Token-based, min ~$12/execution (Azure Quantum)
- **Quantinuum** — HQC-based, subscription required
- **Simulator** — Always free

Use `get_pricing` with a circuit to see exact costs.

## Privacy

- Circuits are anonymized before analysis (comments, variable names, string literals stripped)
- All data encrypted in transit (HTTPS/TLS 1.3)
- No circuit data is stored after execution
- Usage is logged for billing purposes only (tool name, problem size, timestamp)

## Issues

Found a bug? Have a feature request? [Open an issue.](https://github.com/hiq-lab/galan/issues)

## About

Galan is part of the [Garm quantum computing platform](https://garm.valiant-quantum.com) by [Valiant Quantum](https://valiant-quantum.com). The circuit analysis engine is powered by [Nathan](https://arvak.io/nathan), the quantum research optimizer from [Arvak](https://github.com/hiq-lab/arvak).
