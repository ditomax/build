# Losses, Hazards and Control — STPA-light Checklist

**Version 1**

**The fixed catalogue for concept §5 *Losses, hazards and constraints* and §9 *Control structure* — STPA-light, aimed at reliability, security and robustness, not safety.**

Companion to `RULES.md` (§12 Scope boundary) and `checklists/ai-threats.md`. Inspired by the work of Nancy Leveson (STAMP / STPA). Intake picks losses from this list; it never invents new ones. The concept turns every selected loss into hazards, every hazard into constraints, and every constraint into numbered requirements with tests — one ID chain from loss to test:

```
L0–L11          loss            this catalogue
H-<FEAT>-n      hazard          concept §5
SC-<FEAT>-n     constraint      concept §5
UCA-<FEAT>-n    unsafe control action → H / SC     concept §9
F/U/NF-<FEAT>-n requirement     concept §3–§5, Origin: SC-<FEAT>-n
module          Traces to: … + Regulation line     concept §11
test            per UCA / SC    synthesis §13
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
| L4 | Wrong or damaging information relayed — to users, customers, downstream systems or the public | output based on incomplete, stale or fabricated input, presented as reliable; uncertainty hidden; harmful or defamatory content passed on | no output without required inputs confirmed; source and freshness shown; "cannot answer" is a valid answer; output checks before publishing or relaying |
| L5 | Service or workflow unavailable, degraded or stuck | single point of failure; unbounded wait on a dependency; overload without shedding; a workflow that cannot continue | timeouts everywhere; defined degraded mode; critical path protected |
| L6 | Excessive token spending or resource consumption (tokens, compute, memory, storage, bandwidth) | usage-dependent cost without cap; loops or retries multiplying metered calls; unbounded jobs, files or logs on the host | budget guard and quotas (→ `requirements.md` § Operating Cost); resource limits per job, user and tenant; retention |
| L7 | Breach of legal, contractual or licence obligations (other than GDPR) | content used against its licence; transparency duties not met (e.g. AI disclosure); SLA not measurable | licence check for every source; disclosure in the UI; measured SLA |
| L8 | Disclosure of confidential business information | prices, internal documents, system prompts reachable through the product or its outputs | confidentiality class per data source; output filtering |
| L9 | Leakage of credentials or secrets (keys, tokens, passwords, certificates) | secrets in code, prompts, logs or outputs; secrets readable by an AI component; a secret typed into an agent command and saved in a permission allow list; host-local agent files or `.env` committed; no rotation | secret manager; secrets never in prompts, commands, logs or outputs; repository hygiene (RULES §13); least-privilege keys; rotation and revocation tested |
| L10 | System abused against third parties (hosting a DoS attack, spam or phishing relay, scraping, attack platform) | unthrottled outbound calls; open relay; AI component can send messages or requests to arbitrary targets | outbound allowlist; rate limits on outbound traffic; abuse monitoring |
| L11 | Maintenance access cut off — operators can no longer maintain, update, roll back or stop the system | single admin account; lost keys; self-modifying configuration; no out-of-band access; no kill switch | break-glass access, tested; configuration changes reviewed; kill switch for AI components; rollback path |

Reputation and trust are consequences of L1–L11, not losses of their own. L9 stands apart from L3 and L8 on purpose: one leaked secret opens every other loss, and revocation is the only remedy. A loss that does not apply gets one line: "not relevant — <reason>".

## 2. System card (intake, concept §0)

| Line | Values |
| --- | --- |
| Autonomy | suggests only · acts with human confirmation · acts alone |
| Exposure | internal · partner · public |
| Learning loop | no · yes — what feeds back (corrections, feedback, memory, retraining) |
| Actions | tools, write access, code execution, messages or requests to others, other agents |

Exposure *public*, autonomy *acts alone*, a learning loop and any action beyond reading each widen the hazards worth looking for.

## 3. Control structure (concept §9)

A **controller** issues **control actions** to a **controlled process** and receives **feedback**; it acts on a **process model** — what it believes about the process. Kinds:

| Kind | Examples |
| --- | --- |
| human user role | sales engineer, clerk, customer |
| operator | admin, support, content maintainer |
| system component | a service, a module that decides |
| error handler | retry logic, fallback, circuit breaker — its response to an error is a control action |
| AI component | an LLM agent, a classifier, a ranking model |
| external service | a provider API, a partner system |
| scheduled job | sync, import, cleanup |
| mechanical | a script, hook or CI check that blocks or allows |

Every component is a small control loop: it senses, compares against its constraint, and refuses, degrades, contains or alerts. In §11 every controlling module gets a **Regulation** line: *enforces SC-…; watches <feedback>; on violation <refuse · degrade · contain · alert>; refreshes its process model by <…>.*

## 4. Unsafe control actions (concept §9)

One row per control action that can lead to a hazard, for users **and** for the system itself.

| Type | Meaning | Typical for |
| --- | --- | --- |
| N | not provided when needed | missing alert, missing confirmation, missed retry |
| P | provided and causes a hazard | following an injected instruction, silent fallback to stale data |
| T | too early, too late or in the wrong order | recommending before inputs are confirmed, writing before validation |
| D | stopped too soon or applied too long | retrying a non-idempotent write during an outage, a loop that keeps spending tokens |

Typically 5–15 UCAs per feature. A control action with no path to a selected loss is skipped with one line.

## 5. Causes (concept §9 *Causes* column)

| ID | Cause | Look for |
| --- | --- | --- |
| CS1 | Process model flaw — stale, incomplete or fabricated belief | cached state without age, an agent "remembering" an earlier turn, a human trusting the AI |
| CS2 | Missing, delayed or wrong feedback | no confirmation of the effect, errors swallowed, no monitoring |
| CS3 | Bad, untrusted or adversarial input (incl. supply chain) | uploads, retrieved documents, tool results, third-party models and libraries → `ai-threats.md` |
| CS4 | Action not carried out as intended | partial write, lost message, side effect |
| CS5 | Conflicting or uncoordinated controllers | two writers, human override vs. automation, two agents |
| CS6 | Inadequate authority — a controller may do more than its constraints need, or nobody controls | broad keys, AI with write access, no owner for a decision |

## 6. Enforcement levels (concept §5 *Enforced by*)

| Level | Meaning |
| --- | --- |
| code | the product's code makes the unsafe action impossible or detects it |
| configuration | a setting outside the code (permissions, limits, network rules) |
| prompt + test | an instruction to an AI component, backed by a negative or adversarial test — the accepted level for constraints on an AI component's own behaviour, which code cannot enforce |
| prompt | an instruction to an AI component only — the weakest form: a model can ignore or lose it |
| human | a person checks or decides |
| organisation | a process, a contract, a training |

A constraint for L1, L2, L3, L9, L10 or L11 enforced only by *prompt*, *human* or *organisation* is a finding for the PM gate. The same constraint enforced by prompt in two independent controllers is defence in depth, not a level of its own. On Path B and in rescue rounds the column reads *now → target*.

---

## Learning Rule

When a project surfaces a hazard, constraint or cause that recurs and is not covered here, add it to the matching table before closing the work. A new loss (L12 …) needs a reason it is not a hazard of an existing loss.
