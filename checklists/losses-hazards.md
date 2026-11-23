# Losses, Hazards and Control — STPA-light Checklist

**Version 3**

**The fixed catalogue for concept §5 *Losses, hazards and constraints* and §9 *Control structure* — STPA-light, aimed at reliability, security and robustness, not safety.**

Companion to `RULES.md` (§12 Scope boundary) and `checklists/ai-threats.md`. Inspired by the work of Nancy Leveson (STAMP / STPA). Intake picks losses from this list; it never invents new ones. The concept turns every selected loss into hazards and every hazard into constraints (§1a); a testable constraint is itself a requirement with a test. Loss scenarios — causal paths in context (§1a) — are answered in the design order. STPA ends in the constraint's **response**: what must be detectable, which immediate action, which remedy, within which time — a requirement on the design. Error handling sits below STPA and carries it out after the architecture (`checklists/error-handling.md`). One ID chain from loss to test:

```
L0–L12          loss            this catalogue
H-<FEAT>-n      hazard          concept §5
SC-<FEAT>-n     constraint      concept §5
UCA-<FEAT>-n    unsafe control action → H / SC     concept §9, causes = coarse scenarios
M-<FEAT>-n      remedy          concept §5 — from the SC response; people or external mechanisms, later
A-<FEAT>-n      immediate action concept §11 — the system, now, bridging the gap to the remedy
E-<FEAT>-n      matrix row      concept §11 reaction matrix — from an SC requirement, an error of the design, or an unexpected error at an outside action: detector, immediate action, bound, remedy; source: SC / UCA / H, or an operational requirement
F/U/NF-<FEAT>-n requirement     for what an SC needs outside itself or a scenario answer (eliminate, prevent, limit) — Origin: SC-/UCA-<FEAT>-n
module          Traces to: … + Regulation line     concept §11
test            per UCA / SC / E row    synthesis §13
gap matrix      SC → requirement → passing test    synthesis §14
```

---

## 1. Losses

Fixed, project-wide, never renumbered. A hazard is a **system state** that leads to a loss under bad conditions; a constraint is the rule that keeps the system out of that state.

| ID | Loss | Typical hazards (system states) | Typical constraints |
| --- | --- | --- | --- |
| L0 | Harm to people or property; a safety function | — | **out of scope → scope-boundary warning (RULES §12)** |
| L1 | Data loss or corruption | data in one place only, no verified backup; partial write left behind; two writers on one record; migration without rollback | every write atomic or compensated; backup with tested restore; one owner per data element |
| L2 | Personal data breach / GDPR violation | personal data reachable by unauthorised parties; data kept beyond purpose; personal data sent to a third-party model without basis | least data; access by role; retention and deletion; processing agreement for every processor |
| L3 | Loss of control to outsiders: unauthorised access, manipulation, execution of foreign code or prompts | instructions accepted from untrusted content; generated or uploaded code executed; unauthenticated entry point | every trust-boundary crossing validated; untrusted content never reaches a control channel; no execution of foreign code outside a sandbox |
| L4 | Wrong or damaging information relayed — to users, customers, downstream systems or the public | output based on incomplete, stale or fabricated input, or a wrong calculation, period boundary or aggregation on correct input, presented as reliable; uncertainty hidden; harmful or defamatory content passed on | no output without required inputs confirmed; source and freshness shown; "cannot answer" is a valid answer; output checks before publishing or relaying |
| L5 | Service or workflow unavailable, degraded or stuck | single point of failure; unbounded wait on a dependency; overload without shedding; a workflow that cannot continue | timeouts everywhere; defined degraded mode; critical path protected |
| L6 | Excessive token spending or resource consumption (tokens, compute, memory, storage, bandwidth) | usage-dependent cost without cap; loops or retries multiplying metered calls; unbounded jobs, files or logs on the host | budget guard and quotas (→ `requirements.md` § Operating Cost); resource limits per job, user and tenant; retention |
| L7 | Breach of legal, contractual or licence obligations (other than GDPR) | content used against its licence; transparency duties not met (e.g. AI disclosure); SLA not measurable | licence check for every source; disclosure in the UI; measured SLA |
| L8 | Disclosure of confidential business information | prices, internal documents, system prompts reachable through the product or its outputs | confidentiality class per data source; output filtering |
| L9 | Leakage of credentials or secrets (keys, tokens, passwords, certificates) | secrets in code, prompts, logs or outputs; secrets readable by an AI component; a secret typed into an agent command and saved in a permission allow list; host-local agent files or `.env` committed; no rotation | secret manager; secrets never in prompts, commands, logs or outputs; repository hygiene (RULES §13); least-privilege keys; rotation and revocation tested |
| L10 | System abused against third parties (hosting a DoS attack, spam or phishing relay, scraping, attack platform) | unthrottled outbound calls; open relay; AI component can send messages or requests to arbitrary targets | outbound allowlist; rate limits on outbound traffic; abuse monitoring |
| L11 | Maintenance access cut off — operators can no longer maintain, update, roll back or stop the system | single admin account; lost keys; self-modifying configuration; no out-of-band access; no kill switch | break-glass access, tested; configuration changes reviewed; kill switch for AI components; rollback path |
| L12 | Financial loss — money spent, lost or owed through the product's actions or outputs | unwanted, duplicate or excessive orders, payments, bookings or paid messages; actions beyond a budget or limit; wrong prices or quantities passed on; refunds or penalties caused by the product | idempotent writes and reconciliation with the authoritative system; hard limits and budgets enforced in code; confirmation above a threshold; committed spend visible and bounded per period |

Reputation and trust are consequences of L1–L12, not losses of their own. The product's own running cost is L6; money the product moves or commits — for the sponsor or its users — is L12, for most products that act on the outside world the central loss. L9 stands apart from L3 and L8 on purpose: one leaked secret opens every other loss, and revocation is the only remedy. A loss that does not apply gets one line: "not relevant — <reason>".

## 1a. Hazards, constraints, scenarios — the tests

**Hazard** — a state of the system that leads to a loss under bad but realistic conditions. Three tests: (1) a **state**, not an event or a cause ("the engineer works on stale data without seeing it", not "the CRM export fails"); (2) **inside our boundary**, so we can prevent it; (3) **system level**, not a component failure ("sensor broken" is too low). Pattern: *system + unsafe condition + [losses]*.

**Constraint (SC)** — the inverted hazard, written in two halves (build's convention on top of STPA): **keep out** (the rule that keeps the system out of the state) and **response, where applicable** — what happens when the system gets there regardless: **what must be detectable**, the **immediate action** (the system, now), the **remedy** (people or a mechanism, later) and **within which time** ("…if it does not, the crew is warned in time to recover"). Where the hazard cannot be detected or reversed once present, the response names what limits the loss — or "none viable — <reason>". The response is a **requirement on the design**: it becomes rows of the reaction matrix (`checklists/error-handling.md`), and its remedy joins the remedies list — no constraint, no remedy. Constraints for single controllers may also come from UCAs and scenarios. Tests: if the rule holds, can the hazard still happen? Can you write a test for it? A constraint is a rule, not a solution.

**Loss scenario** — a **causal path in context**: the circumstances, what the controller got wrong or could not do, the UCA or violated constraint, the hazard, the loss. Two types: (a) **why a controller issues an unsafe action or omits a needed one** — a flawed process model, missing or misleading feedback, bad input, conflicting controllers; (b) **why a safe action is not carried out correctly** — lost, delayed, partial, executed twice, or with an outcome nobody knows (a timeout after the remote side booked). A scenario needs no exception and no failed component: ordinary interactions and wrong logic on correct input (CS7) are enough. Before the PM gate they are **coarse** (the *Causes* column of the UCA table, §5); after the architecture each is refined to a full path and answered in the **design order**:

1. **eliminate** the possibility (never reuse blocks across customers);
2. **prevent** it by design (a version check, an idempotency key, fencing);
3. **detect and control** it at runtime — it joins the constraint's response as a requirement and becomes a row of the reaction matrix;
4. **limit the damage** (caps, quotas, smaller blast radius);
5. **accept** as residual risk.

Every answer records its **reason, owner and verification** (the requirement and its test, the row, the acceptance with name and date). An elimination or prevention that rests on an assumption (the counterparty keeps its key, nobody edits the file by hand) names it with a revisit trigger.

**Immediate action classes** — the constraint's response names one, per hazard; the reaction matrix later names the concrete action:

| Class | Fits when |
| --- | --- |
| hold | doing nothing is safe until the remedy — the bridge test passes |
| safe state | a defined safe state exists and it is not "nothing" (close the valve, cancel open orders) |
| protective step | a move that only reduces risk is available (add collateral, reduce-only, close) — safe even when the product's picture is wrong, safe to repeat |
| complete or compensate | a started chain must reach a defined end (offboarding, saga) |
| at most once, then a person | the effect must not happen twice and cannot be made repeat-safe (no key, no reversal): never re-send automatically; reconcile; an unknown outcome goes to a named person with an instruction |
| reduced service | the function must continue at a reduced level; if that needs a second controller → a capability or scope decision at the PM gate (STPA sets its requirements and can assess a proposed design; build does not design the redundancy) |

**Bridge test** (per gate-loss hazard, §6): *can the immediate action hold until the remedy has taken effect, before the loss occurs — and what loss does the immediate action cause by itself?* The remedy time is a **justified bound**: time to detect, to decide and to take effect, on the slowest realistic path (nights, holidays, the person or channel not available) — for a held item the time until its own remedy acts (an agent decides the held credit), for a drifting situation the time until the cause is removed. For *hold* it is the null-action question: what would the situation do by itself if the product did nothing? Bound met and no loss of its own → the response stands; the immediate action causes another loss → a trade-off for the sponsor (`OQ-n`); otherwise another class, a faster remedy, or a finding. An unknown bound is a PM-gate finding, not a pass. Fail-silent is a failure mode of a component that is safe because something else takes over — never assume it for the whole product.

**Remedies** — the union of the constraints' remedies, each with who or what (role and deputy, or the mechanism), triggered by what and the time to remedy, checked against the operating environment (intake I7: who is there, when), and **confirmed by the person or team who will carry it out** (name, date) — until then *proposed* (RULES §5). One standing remedy needs no constraint: *classify and fix* — find the cause of an unplanned error and ship a fix.

## 2. System card (intake, concept §0)

| Line | Values |
| --- | --- |
| Autonomy | suggests only · acts with human confirmation · acts alone |
| Exposure | internal · partner · public |
| Learning loop | no · yes — what feeds back (corrections, feedback, memory, retraining) |
| Actions | state effects: tools, write access, code execution, messages or requests to others, other agents |
| Outputs | information effects: reports, recommendations, screens, files or feeds — and **who uses them**, checked or unchecked |

Exposure *public*, autonomy *acts alone*, a learning loop and any state effect each widen the hazards worth looking for. "Reads only" is not harmless: an output someone acts on is a control action — wrong (P), missing (N), late or stale (T). The card also sets the depth of the error handling per effect (`checklists/error-handling.md` §3).

## 3. Control structure (concept §9)

A **controller** issues **control actions** to a **controlled process** and receives **feedback**; it acts on a **process model** — what it believes about the process. Kinds:

| Kind | Examples |
| --- | --- |
| human user role | sales engineer, clerk, customer |
| operator | admin, support, content maintainer — and whoever carries out the remedies `M-<FEAT>-n` (restart, update, replace, reconfigure, correct data, decide a held item) |
| external mechanism | watchdog, supervisor, redundant component, voter — keeps an action safe when the process that should act is dead or hanging |
| repeater | scheduler wrapper, restart policy, library retry, queue redelivery, inbound webhook retry, failover, restore (intake I13) — and the product's own re-poll of unacknowledged input, people redoing work — can run an action again for the same intent (see *Outside actions* below) |
| monitoring | uptime or health check, log alerting — feedback for the remedy owners |
| system component | a service, a module that decides |
| error handler | retry logic, fallback to a reduced service, circuit breaker, the handler for unexpected errors at each outside action — its response to an error is a control action (immediate action `A-<FEAT>-n`, `checklists/error-handling.md`) |
| AI component | an LLM agent, a classifier, a ranking model |
| external service | a provider API, a partner system |
| scheduled job | sync, import, cleanup |
| mechanical | a script, hook or CI check that blocks or allows |

**Outside actions** (concept §9 *Outside actions*) — every control action with an effect outside the product, state (order, write, message) or information (an output someone acts on), is part of the control structure with its actuator and feedback path. One row per action that passes the filter — a state effect on a gate loss, or an output used unchecked; actions with the same counterparty and the same commit boundary form one group; more than about five groups is a PM-gate finding about *acts alone*:

| Field | Who | Content |
| --- | --- | --- |
| effect, losses | author | state or information; the losses it reaches |
| commit boundary; before · during · after | author | where the effect becomes final, and what an exception, a timeout or a dead process means before it, during it and after it |
| counterparty (C16) | IT or the service owner — one batch per external write on a gate loss, documentation first | for each error answer and for a timeout: does it tell whether the effect happened — **sent · not sent · unclear**, kept in a code table the owner maintains, a code not in it counting as unclear? can the effect be queried, after how long is it visible (the **visibility window**)? is there an idempotency key or a reference field? rate limits? Nobody knows → a short probe against a test system, or `OQ-n` and a PM-gate finding |
| repeaters | author, from intake I13 and the design | everything that can run the action again for the same intent — the environment (scheduler, restart policy, library retry, queue redelivery, restore), the product's own schedule over unacknowledged input, people redoing the work; "switched off" is never relied on alone |
| repeat safety | author | idempotent · resumable · compensable · at most once, then a person |
| decisions (C17) | sponsor, only where they arise | two hazards wanting different immediate actions at this action (a loss trade-off, `OQ-n`, decided at the PM gate — a call already in flight that cannot be cancelled is a bounded residual to accept instead); who releases each stop; who completes after *at most once* |

The author's columns are shown in one block, confirmed in one line by the sponsor (business columns) and IT (technical columns). These are type (b) scenarios — a safe action not carried out correctly — answered here by design; what is left becomes a requirement for the reaction matrix (an unexpected error at this action, before · during · after the commit boundary).

Every component is a small control loop: it senses, compares against its constraint, and refuses, degrades, contains or alerts. In §11 every controlling module gets a **Regulation** line: *enforces SC-…; watches <feedback>; on violation <refuse · degrade · contain · alert>; refreshes its process model by <…>.*

## 4. Unsafe control actions (concept §9)

One row per control action that can lead to a hazard, for users **and** for the system itself — outputs that someone acts on included.

| Type | Meaning | Typical for |
| --- | --- | --- |
| N | not provided when needed | missing alert, missing confirmation, missed retry |
| P | provided and causes a hazard | following an injected instruction, silent fallback to stale data |
| T | too early, too late or in the wrong order | recommending before inputs are confirmed, writing before validation |
| D | stopped too soon or applied too long | retrying a non-idempotent write during an outage, a loop that keeps spending tokens |

Every UCA names its coarse scenarios in the *Causes* column (§5). A control action with no path to a selected loss is skipped with one line. Where the SC would be a sponsor choice between two losses: `SC: — (OQ-n, trade-off Lx↔Ly)`, decided at the PM gate. After the architecture, the error-handling controllers — error handler, remedy owners, external mechanisms, repeaters — get the same four types (`checklists/error-handling.md` §9); a crashed or hanging process is a **possible cause** of a needed action not being provided — checked per outside action, not assumed for all.

## 5. Causes — the coarse scenarios (concept §9 *Causes* column)

| ID | Cause | Look for |
| --- | --- | --- |
| CS1 | Process model flaw — stale, incomplete or fabricated belief | cached state without age, an agent "remembering" an earlier turn, a human trusting the AI |
| CS2 | Missing, delayed or wrong feedback | no confirmation of the effect, errors swallowed, no monitoring |
| CS3 | Bad, untrusted or adversarial input (incl. supply chain) | uploads, retrieved documents, tool results, third-party models and libraries → `ai-threats.md` |
| CS4 | Action not carried out as intended | partial write, lost message, side effect |
| CS5 | Conflicting or uncoordinated controllers | two writers, human override vs. automation, two agents |
| CS6 | Inadequate authority — a controller may do more than its constraints need, or nobody controls | broad keys, AI with write access, no owner for a decision |
| CS7 | Wrong logic on correct input — a flawed control algorithm | a period cut at export time instead of midnight, a unit or rounding slip, an aggregation that double-counts, two correct inputs that disagree, a rule that does not fit an edge case |

## 6. Enforcement levels (concept §5 *Enforced by*)

| Level | Meaning |
| --- | --- |
| code | the product's code makes the unsafe action impossible or detects it |
| configuration | a setting outside the code (permissions, limits, network rules) |
| prompt + test | an instruction to an AI component, backed by a negative or adversarial test — the accepted level for constraints on an AI component's own behaviour, which code cannot enforce |
| prompt | an instruction to an AI component only — the weakest form: a model can ignore or lose it |
| human | a person checks or decides |
| organisation | a process, a contract, a training |

**Gate losses** are L1, L2, L3, L9, L10, L11, L12 and the loss the sponsor named worst at I11 (*Worst for this sponsor*). A constraint for a gate loss enforced only by *prompt*, *human* or *organisation* is a finding for the PM gate; for a mixed level (*code · human*) the level that holds the critical branch counts. The same constraint enforced by prompt in two independent controllers is defence in depth, not a level of its own. On Path B and in rescue rounds the column reads *now → target*.

---

## Learning Rule

When a project surfaces a hazard, constraint or cause that recurs and is not covered here, tell the user before closing the work, with the row it would need; they report it upstream (this file is read-only in a workspace — AGENTS.md). A new loss (L13 …) needs a reason it is not a hazard of an existing loss.
