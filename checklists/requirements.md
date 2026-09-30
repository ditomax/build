# Requirement Checklists

**Version 1**

_No exact field for your product? Take the closest one (an internal document-generation tool → Web application), add the cross-cutting and technology sections, and name the sections you used in `10-intake.md`._

**A living library of learned, reusable requirements — keyed by field of application and by technology.**

Companion to the [MANIFEST.md](../MANIFEST.md) and `RULES.md`. During Phase 1 the field of application is inferred; consult the matching sections here and turn every applicable entry into an explicit, numbered requirement (`F-`, `NF-`, `U-`). Anything not applicable is explicitly deferred with a rationale — never silently skipped.

Each entry is: **trigger → required check/standard → how it is verified.** Add new rows whenever a project teaches us something reusable, so future projects inherit it.

---

## Cross-Cutting Mandatory Standards

| Trigger | Required | Verified by |
| --- | --- | --- |
| Processing personal data of EU subjects | DSGVO/GDPR compliance (lawful basis, data minimization, deletion, DSAR) | Data-flow map, privacy review, deletion test |
| Handling of information security at org level | ISO/IEC 27001 controls where in scope | Control mapping, audit evidence |
| Software with quality-management obligations | ISO 9001 / ISO/IEC 25010 quality attributes | Quality plan, metrics review |
| Product sold in the EU | CE marking + applicable directives | Declaration of conformity, technical file |
| Accessibility obligation (public sector / EU) | EN 301 549 / WCAG 2.2 AA | Automated + manual accessibility audit |
| Payment card data | PCI-DSS | Scoping review, ASV scan |
| US health data | HIPAA | Safeguards review, BAA in place |

---

## By Field of Application

### Web application

| Trigger | Required | Verified by |
| --- | --- | --- |
| Public-facing web app | Security audit (OWASP Top 10, OWASP ASVS) | Pen test / SAST + DAST report |
| Any web app | AuthN/AuthZ model, session & secret management | Threat model, auth tests |
| Any web app | HTTPS/TLS everywhere, security headers (CSP, HSTS) | Header scan, TLS grade |
| User-facing UI | Usability, responsive UI/UX, i18n/l10n | UX review, cross-device test |
| Accessibility in scope | WCAG 2.2 AA | axe/Lighthouse + manual audit |
| Any web app | Deployment strategy, rollback, blue/green or canary | Deploy runbook, rollback drill |
| Any web app | Rate limiting, input validation, CSRF/XSS/SQLi defenses | Security tests |
| Any web app | Observability: logging, metrics, tracing, alerting | Dashboards + alert test |

### Backend / API service

| Trigger | Required | Verified by |
| --- | --- | --- |
| Public or internal API | Versioning & backward-compatibility policy | Contract tests, deprecation plan |
| Any API | Schema/contract definition (OpenAPI/proto) | Schema lint, contract tests |
| Stateful service | Idempotency, timeouts, retries, circuit breakers | Fault-injection tests |
| Any service | Health/readiness endpoints, graceful shutdown | Probe test, drain test |
| Persistent data | Migration strategy, backup & restore, PITR | Restore drill |

### Mobile application

| Trigger | Required | Verified by |
| --- | --- | --- |
| iOS/Android app | Store guideline compliance, permissions minimization | Store review checklist |
| Any mobile app | Offline behavior, secure local storage | Airplane-mode test, storage audit |
| Any mobile app | Battery, network, and memory footprint | Profiler measurements |

### Embedded system

| Trigger | Required | Verified by |
| --- | --- | --- |
| Real-time behavior | Deadlines, WCET analysis, jitter bounds | Timing analysis, scheduling proof |
| Safety-relevant | IEC 61508 / ISO 26262 (automotive) / IEC 62304 (medical) | Safety case, hazard analysis |
| Constrained resources | RAM/flash/CPU budgets, stack-depth analysis | Static analysis, high-water-mark test |
| Battery/powered device | Power budget, sleep modes, brown-out handling | Power measurement, brown-out test |
| Firmware in the field | Bootloader + OTA update with rollback | Update/rollback test on target |
| Non-volatile storage | Flash/EEPROM wear leveling, write-cycle budget | Endurance test |
| Watchdog present | Watchdog kick strategy, fault recovery | Fault-injection reset test |
| Buses (I2C/SPI/CAN) | Bus contention, error handling, protocol version | Bus stress test |

### Distributed real-time system (DRS)

_Valid for distributed real-time systems — several nodes that together close a loop through the physical world. Applies in addition to **Embedded system**; skip it for a single-node product._

| Trigger | Required | Verified by |
| --- | --- | --- |
| Several nodes coordinate actions | Global time base: precision π, granularity g with g > π | Measured clock offset between nodes, synchronization test |
| Timestamps compared across nodes | Reasonableness condition; limits of time measurement (±2g) | Evaluation of distributed timestamps |
| Observation taken from the physical process | Temporal accuracy `d_acc` per RT image; phase sensitivity checked against the period of the reading task | Age measurement at the point of use, scheduling proof |
| Message triggers an action | Permanence / action delay defined; hidden channels considered | Test with a deliberately delayed channel |
| End-to-end function across nodes (a transaction) | End-to-end deadline and a budget per stage (input, action, transmission, reception, output) | Worst-case sum ≤ deadline; measurement per stage on the target |
| Node failure must be tolerated | Fault hypothesis: failure mode per node, k, degree of redundancy (k+1 / 2k+1 / 3k+1) | Fault injection, including beyond the hypothesis |
| Replicated nodes vote | Replica determinism; exact vs. inexact voting decided | Comparison of replica outputs |
| Nodes join / leave | Membership service, clique detection, degraded mode | Membership test with network partition |
| Peak load specified | Load hypothesis per channel and node; freedom from thrashing | Load test above peak, throughput curve |
| Subsystems are integrated | Composability: which property survives integration | Property shown before and after integration |
| Gateway between clusters | Flow-control mode of both sides, buffering, skipping strategy | Gateway overflow test |
| Safety-relevant actuator | End-to-end protocol: the effect is monitored, not the command | Test with a blocked actuator |
| Node is reintegrated | Ground state defined, minimal h-state, reintegration point | Restart of a node in the running system |

### Hardware system

| Trigger | Required | Verified by |
| --- | --- | --- |
| Any electronics | EMC/EMI compliance (e.g. EN 55032/55035), ESD | Pre-compliance + certified lab test |
| Any hardware | Environmental spec: temperature, humidity, vibration, IP rating | Environmental test campaign |
| Powered hardware | Electrical safety (e.g. IEC 62368-1), power sequencing | Safety test, sequencing scope trace |
| Multiple HW revisions | Revision matrix, silicon errata review, backward compat | Regression on each revision |
| Signal integrity concern | Voltage/level matching, timing, termination | SI simulation / measurement |
| Manufacturing | DFM/DFT, test points, ICT/boundary scan, calibration | Board bring-up + production test plan |
| Reliability target | MTBF/FIT estimate, derating, thermal analysis | Reliability calculation, thermal imaging |

### Lab equipment / instrumentation

| Trigger | Required | Verified by |
| --- | --- | --- |
| Measurement device | Calibration procedure, traceability to standards | Calibration record |
| Regulated lab | GLP / ISO/IEC 17025 as applicable | Audit evidence |
| Data-producing instrument | Data integrity (ALCOA+), audit trail | Data-integrity review |
| Safety in lab | Interlocks, emergency stop, fail-safe defaults | Safety FMEA, interlock test |

### Desktop application

| Trigger | Required | Verified by |
| --- | --- | --- |
| Installed desktop app | Cross-platform packaging, signing, auto-update | Install/update test per OS |
| Any desktop app | Crash reporting, local data protection | Crash-report pipeline test |

### Data / ML system

| Trigger | Required | Verified by |
| --- | --- | --- |
| ML model in product | Data provenance, bias/fairness review, model card | Evaluation report |
| Any data pipeline | Schema validation, data quality checks, lineage | Pipeline tests, lineage graph |
| Model in production | Drift monitoring, retraining trigger, rollback | Monitoring + rollback drill |

---

## By Technology / Artifact

| Trigger | Required | Verified by |
| --- | --- | --- |
| Docker build | Smoke tests on each target architecture (arm64, amd64, ...) | Multi-arch CI matrix |
| Container image | Minimal base, non-root user, image vulnerability scan | Trivy/Grype scan in CI |
| Any software change | Mandatory code review before merge | PR approval gate |
| Any repository | CI pipeline: build, lint, test, security scan on every PR | Green pipeline required to merge |
| Any codebase | Static analysis + formatter + linter enforced | CI check |
| Dependencies | SBOM, license check, dependency vulnerability scan | SCA report (e.g. Dependabot) |
| Release | Semantic versioning, changelog, signed artifacts | Release checklist |
| Infrastructure | IaC (reviewed, versioned), reproducible environments | Plan/apply review |
| Secrets | No secrets in repo; use a secret manager | Secret-scanning in CI |
| Database | Migration scripts, reversible, tested on prod-like data | Migration dry-run |
| Concurrency present | Race/deadlock analysis, stress test | Thread sanitizer / stress run |
| Public-facing endpoint | Load/performance test against defined SLOs | Load-test report |

---

## General Robustness (Software)

| Area | Required | Verified by |
| --- | --- | --- |
| Testing | Unit + integration + end-to-end; coverage target agreed | Coverage report |
| Failure handling | Defined behavior for every error path; no silent failures | Negative tests |
| Boundaries | Input validation and boundary/fuzz testing | Fuzz run |
| Resource use | No leaks (memory, handles, connections); pool limits | Soak test |
| Observability | Structured logs, metrics, tracing, actionable alerts | Alert fire drill |
| Recovery | Backup/restore, graceful degradation, disaster-recovery plan | Restore + failover drill |
| Config | Externalized, validated, environment-specific, documented | Config schema test |
| Documentation | README, ADRs, runbooks, API docs kept current | Doc review in PR |
| Reproducibility | Deterministic builds, pinned dependencies | Clean-room build |

---

## General Robustness (Hardware)

| Area | Required | Verified by |
| --- | --- | --- |
| Failure modes | FMEA/FMECA; single-point-of-failure review | FMEA document |
| Environmental | Full operating and storage envelope tested | Environmental campaign |
| Compliance | EMC, safety, RoHS/REACH, regional certifications | Certified lab reports |
| Reliability | Derating, thermal margins, MTBF, burn-in | Reliability test data |
| Manufacturing | DFM/DFT, production test coverage, yield plan | Bring-up + FAI |
| Serviceability | Diagnostics, field-replaceable units, firmware recovery | Service procedure test |
| Supply chain | Second sources, obsolescence plan, component traceability | BOM risk review |

---

## Learning Rule

Whenever a project surfaces a reusable, field- or technology-specific requirement that is not yet captured here, add a row before closing the work. This file is how the team's hard-won knowledge compounds across projects.
