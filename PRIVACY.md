# Privacy Policy

**Galan MCP Server — Valiant Quantum UG (haftungsbeschränkt)**

Last updated: 27 March 2026

---

## 1. Who we are

Galan is operated by Valiant Quantum UG (haftungsbeschränkt), Munich, Germany ("we", "us").

Contact: hello@valiant-quantum.com

## 2. What Galan processes

When you use Galan through Claude or another MCP client, the following data is transmitted to our servers:

| Data | Purpose | Retained |
|---|---|---|
| Quantum circuit code | Analysis, compilation, execution | Not stored after response |
| QUBO matrices | Solving, hardness analysis | Not stored after response |
| Search queries | Paper search | Not stored after response |
| Tool name + problem size | Usage metering, billing | Stored (see Section 5) |
| API token or OAuth session | Authentication | Token hash stored |
| IP address | Rate limiting, abuse prevention | Not stored |

**We do not store your circuit code, QUBO matrices, or search queries after the response is delivered.** These are processed in memory and discarded.

## 3. Circuit anonymization

Before analysis, circuits are automatically anonymized:

**Removed:**
- Comments (may contain names, project info, proprietary details)
- String literals (may contain file paths, credentials)
- Custom variable and register names (normalized to generic identifiers)
- Custom function and class names
- Non-standard imports (custom modules, internal libraries)

**Preserved:**
- Gate operations (h, cx, rz, etc.)
- Qubit and bit declarations
- Numeric values (rotation angles, parameters)
- Standard library imports
- Circuit structure, measurement operations, depth

Anonymization runs before any processing. The original code never reaches the analysis engine.

## 4. Encryption

- All data in transit is encrypted via TLS 1.3 (HTTPS)
- Circuits submitted for QPU execution are encrypted at rest using AES-256-GCM via the Garm Vault service
- HMAC-SHA256 signatures are applied to audit records

## 5. Usage data we retain

For billing and service improvement, we store:

- **Tool name** (e.g., "compile_circuit", "solve_qubo")
- **Problem size** (e.g., matrix dimensions, qubit count, shot count)
- **Timestamp**
- **Latency**
- **Client identifier** (linked to your API key or OAuth session)
- **Error status** (success or failure, no error details from your data)

This data is stored in a PostgreSQL database on our infrastructure in Germany. It does not contain your circuit code, matrices, or query text.

## 6. Third-party QPU providers

When you execute a circuit on quantum hardware, your (anonymized, encrypted) circuit is transmitted to the selected provider:

| Provider | Jurisdiction | Data transmitted |
|---|---|---|
| IBM Quantum | USA | Anonymized circuit, shot count |
| IQM | Finland / AWS EU | Anonymized circuit, shot count |
| IonQ | USA (Azure/AWS) | Anonymized circuit, shot count |
| Quantinuum | USA/UK (Azure) | Anonymized circuit, shot count |
| AQT | Austria | Anonymized circuit, shot count |
| Rigetti | USA (AWS) | Anonymized circuit, shot count |

Each provider processes data under their own privacy policy. We anonymize and encrypt circuits before transmission. Provider access to your original code is not possible.

When using the simulator, no data leaves our infrastructure.

## 7. Cookies and tracking

Galan does not use cookies, browser fingerprinting, or tracking pixels. The MCP protocol is a server-to-server connection — no browser is involved.

## 8. Data location

All Galan infrastructure is hosted on servers in Germany (IONOS, Karlsruhe). Usage data is stored in Germany. Circuit execution on third-party QPUs may involve data transfer to the provider's jurisdiction (see Section 6).

## 9. Your rights (GDPR)

Under the EU General Data Protection Regulation, you have the right to:

- **Access** — Request what data we hold about you
- **Rectification** — Correct inaccurate data
- **Erasure** — Request deletion of your data
- **Portability** — Receive your data in a machine-readable format
- **Object** — Object to processing based on legitimate interest
- **Complaint** — File a complaint with the Bavarian Data Protection Authority (BayLDA)

To exercise these rights, email hello@valiant-quantum.com with your client identifier or API key prefix.

## 10. Data retention

| Data type | Retention period |
|---|---|
| Circuit code, matrices, queries | Not retained (processed in memory) |
| Usage logs (tool, size, timestamp) | 12 months |
| API key hashes | Until key revoked or account closed |
| OAuth session data | 24 hours after expiry |

## 11. Changes to this policy

We may update this policy. Changes are published in this repository with the updated date. Continued use of Galan after changes constitutes acceptance.

## 12. Contact

Valiant Quantum UG (haftungsbeschränkt)
Munich, Germany
hello@valiant-quantum.com
