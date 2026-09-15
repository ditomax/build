# Architecture Consistency Checklist

**Version 1**

**A checklist for validating cross-module consistency of an architecture — used by Phase 6 of the [MANIFEST.md](../MANIFEST.md).**

Companion to the manifest and `RULES.md`. A single requirement is rarely satisfied inside one module — a typical `F-x` spans a producer, a transport, and a consumer across several modules. This checklist drives the Phase 6 review: **for every requirement whose fulfillment is distributed across modules, trace each architectural element end-to-end and confirm that both endpoints agree and that nothing dangles.**

The recurring failure mode is a *broken or mismatched chain*: a producer with no consumer, a writer and a reader disagreeing on layout, a signal whose meaning differs at each end, a mechanism that stops halfway across the system.

Each row is: **element type → the full chain to trace → the cross-module agreement to confirm.** Add new rows whenever a project teaches us a new consistency hazard.

---

## Element Consistency Checklist

| Architectural element | Trace the full chain | Confirm agreement / no dangling |
| --- | --- | --- |
| **Data elements** | source → transport → destination | type/unit/range/encoding agree end-to-end; ownership & lifetime; where validated & transformed |
| **Functions / operations** | input parameters → execution → output/return | caller ↔ callee agree on signature, pre/post-conditions, and error returns |
| **Mechanisms (producer/consumer)** | producer → binding/registration → consumer integration | exactly one producer; every consumer wired; cardinality; init/teardown order |
| **Memory & storage** | layout/schema → writer(s) → reader(s) | access ordering, locking, capacity/bounds, persistence consistency, alignment |
| **Control signals** | semantics → source/emitter → receivers | timing/ordering, edge vs. level, missed/duplicate handling, priority |
| **Interfaces / APIs / contracts** | provider → contract/protocol/version → consumer(s) | both sides agree; every interface has ≥1 provider and ≥1 consumer; none dangling |
| **State machines / modes** | states & transitions → trigger owner → observers | no unreachable/deadlock states; global mode agreement across modules |
| **Events / messages / queues** | publisher → channel/topic → subscriber(s) | schema, delivery guarantee, ordering, backpressure |
| **Shared resources** (buffers, handles, pools, connections) | allocator/owner → users → release/free | contention, exhaustion, lifecycle, leak-free |
| **Concurrency / synchronization** | shared state → protecting primitive → all accessors | every accessor honors the primitive; lock ordering; reentrancy |
| **Timing / real-time budgets** | deadline owner → contributing chain → each contributor's budget | periods/phases align producer ↔ consumer; end-to-end ≤ deadline |
| **Configuration / parameters** | definition/default → setter → readers | single source of truth; units/range; cross-module consistency |
| **Error / fault handling** | raise site → propagation path → handler/recovery | every error path ends in a handler; fault → safe-state mapping |
| **End-to-end mechanisms** | system-boundary entry → each intermediate step/module → system-boundary exit | the complete path exists and is unbroken; every hop preserves semantics and data; no gap, dead-end, or silent drop; boundary in/out contracts honored |
| **Component life cycle** | creation/init → configured → started/active → suspended/resumed → stopped/destroyed | who owns each transition; init/shutdown ordering & dependencies; acquire/release paired; no use-before-init or use-after-destroy; restart/re-init is safe |
| **Hardware / peripherals / registers** *(embedded/HW)* | device/register → owning driver → users | exclusive access, pin-mux/bus arbitration, init order |
| **Clock / power domains** *(HW)* | domain → enabler/gater → dependents | enabled and sequenced before use |
| **Security / trust boundaries** | boundary crossing → authorizer/validator → downstream | every crossing authenticated/validated before trust |

The hardware-specific rows apply according to the `Field of application` established in Phase 1.

---

## Output Artifact — Cross-Module Consistency Matrix

Phase 6 produces one matrix. One row per (cross-cutting requirement × architectural element it touches):

| Requirement | Element | Chain endpoints (source → … → destination) | Modules involved | Verdict | Note / gap |
| --- | --- | --- | --- | --- | --- |
| F-x | Data element "Foo" | ModA.produce → bus → ModC.consume | A, bus, C | OK / MISMATCH / DANGLING | e.g. unit mismatch: A emits ms, C expects s |

---

## Gate Policy — Advisory with Human Review

Phase 6 is an **advisory gate**, not a hard stop. It always runs and always produces the consistency matrix, but it does not automatically block progress to Phase 7.

* **PASS** — no unmatched endpoints and no contract mismatches: record the matrix and proceed.
* **FINDINGS** — any `MISMATCH` or `DANGLING` row: surface them prominently and **offer a human review**. The human may accept the risk (with a logged rationale), request fixes to the architecture (loop back to Phase 5), or defer specific items. Progress is allowed once findings are acknowledged — the gate advises, the human decides.

Every accepted finding is logged so the decision is traceable, mirroring the ADR discipline used elsewhere in the manifest.

---

## Learning Rule

When a project reveals a new class of cross-module inconsistency that these rows don't cover, add it here before closing the work, so future architecture reviews inherit it.
