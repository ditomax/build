# Error Handling Strategy — Checklist

**Version 3**

**Error handling sits below STPA: it carries out the safety constraints' responses at runtime.**

Companion to `RULES.md` §16 and `checklists/losses-hazards.md` (STPA-light). STPA comes first, before the architecture, and ends in each constraint's **response** — what must be detectable, which immediate action, which remedy, within which time — a requirement on the design (`losses-hazards.md` §1a). Scenarios the design could not eliminate or prevent join it. Error handling takes over after the architecture, in the four steps of the lecture method:

1. **Actions** — immediate actions and remedies, inherited from the constraints (§4);
2. **Errors** — what the design can detect, and the root causes behind it (§5);
3. **Reaction matrix** — error → immediate action (bounded) → remedy (§6, §7);
4. **Indication and storage** — what maintenance needs to diagnose the cause (§8).

Three links tie it to STPA: constraint → actions; constraint response and leftover scenarios → requirement → matrix rows; an error with no scenario → ignore test → back to STPA (§10). Small is fine, empty is not.

---

## 1. Words

| Term | Meaning | Example |
| --- | --- | --- |
| fault · error · failure | the cause · a wrong internal state, which is what can be detected · the visible deviation from the specified service | a bug · own belief ≠ exchange · a second, unwanted order |
| immediate action (`A-`) | what the system does **now**, to keep the error from becoming a loss until the remedy — and to keep the evidence and tell the right person | hold the item, hold the run, warn, protective step |
| remedy (`M-`) | what removes the cause **later** — people (restart, update, replace, reconfigure, correct data, decide a held item, do it by hand) or a mechanism (watchdog, failover, voter) | IT restarts the export; the accountant decides the held reminder |
| requirement (SC response) | a constraint's response as a demand on the design: detect what, immediate action, remedy, within which time | "stale import detected before selection; run held; IT restores within a working day" |
| outside action | a control action with an effect outside the product — a state effect (order, payment, write, message) or an information effect (an output someone acts on) | placing an order; mailing the weekly report |
| commit boundary | the moment an outside action's effect becomes final | the ERP has booked the credit note; the mail has left the relay |
| unexpected error | an error nobody wrote a row for — handled by the standing row of the outside action it hits (§7) | a library exception during the send |

## 2. What STPA hands down

Before the architecture — written in concept §5 and §9, decided at the PM gate:

- per hazard the **response**: what must be detectable (within which time, independent of what, against which reference), the **immediate-action class**, the **remedy** and the time — checked by the bridge test (`losses-hazards.md` §1a);
- the **remedies list** `M-` — the constraints' remedies merged, with who or what, triggered by what, time to remedy, each *confirmed* by its owner or still *proposed*; plus *classify and fix*;
- the **outside actions** with commit boundary, counterparty behaviour, repeaters and repeat safety (`losses-hazards.md` §3);
- the **leftover scenarios** — those the design order answers with *detect and control*;
- storage and retention as the sponsor wants them (C13a, C13b).

Error handling invents none of these. A need for one found later goes back to STPA (a concept redo, or a gate-6 finding).

## 3. Depth — per effect

"Reads only" is not harmless: an output someone acts on is a control action. The depth of the error handling is set per outside action — provisionally with the outside actions, confirmed once the scenarios are answered; what eliminate or prevent removed does not count:

| Depth | Effect | Error handling |
| --- | --- | --- |
| **output** | an output someone acts on (information effect) | rows for **wrong · missing · late**, an absence detector (the output did not arrive) and a value band on its figures; on a gate loss also one check against an independent reference; no more where a real human check stands before use and no gate loss is reached |
| **short** | a state effect, or an output used unchecked, that reaches no gate loss | its requirement rows and its standing row for unexpected errors |
| **full** | a state effect that reaches a gate loss not removed by eliminate or prevent, in a learning loop, or acting alone | all rows, a detector against an independent reference, an outcome band where the effect can be counted |

A human check counts only if it is real: named in the control structure (who checks, against what) and passing the UCA check — a confirmation that decays into a rubber stamp does not count.

## 4. Step 1 — Actions (C10b)

**Immediate actions** — the concrete actions of this product, each serving the immediate-action class a constraint named. Several matrix rows share one; the sponsor decides autonomy once per action:

| Kind | What it does | Typical level |
| --- | --- | --- |
| log and continue | record the event; nothing else changes | item |
| skip | leave out one item, one cycle, one request | item |
| retry | repeat — bounded, with backoff; repeat-safe operations only | item |
| correct from the authoritative source | replace the own belief by the owner's state — "not found" means **unknown** until the visibility window (C16) has passed; without a known window the bound ends at a person with an instruction; after the window, one new automatic attempt under *at most once* only with reservation, stop and budget re-checked and no other writer | item · component |
| cancel own action | withdraw what this product started | item |
| hold before effect | stop the item before its effect; commit nothing half-done | item · run |
| protective step | a move that only reduces risk | item · component |
| reduced service | keep the critical path, switch off the rest | component · service |
| safe state | defined, left only on purpose by the role named for it (C17), survives a restart | item · component · system |
| report | tell a named role over a named channel, with what they need to choose the remedy | any |

- **Level** — the smallest that holds, within the class the constraint set; escalation one level at a time. Too many open item-level holds at once is an error of its own.
- **Escalation counts per source**, so outside input cannot stop the whole product.
- **Autonomy** — automatic · automatic with warning · operator only. For every action with an effect outside the product that the product cannot undo alone, the sponsor decides — directly, through the principle (C18), or delegated to a named role, each dated; never assume *automatic*.
- **Bridge** — the immediate action holds until the remedy can realistically happen and acts before the loss event; it is never released silently, and released only by the role named for it.

**Remedies** come from §2; this step only adds the IDs of the rows that trigger them.

## 5. Step 2 — Errors and root causes (C11)

After the architecture the coarse scenarios of the UCA table are refined per module and interface into causal paths (`losses-hazards.md` §1a) and answered in the design order there. What the design can **detect** is listed here, **by category, not by exception type** — "supplier: no answer · garbage · refusal · unknown outcome after a write" covers every exception the client library throws today and after its next update — each with its **root causes** (often more than one, not always certain) and the **scenario** it belongs to. Walk every boundary:

- every external call — no answer, a late answer, a malformed or implausible answer, a refusal, a rate limit, **unknown outcome after a write**;
- the handler's own writes — a cancel, a report, a stored pause are external calls too, and **the alarm itself can fail**;
- every **own assumption** an action relies on (quantity > 0, budget ≥ 0, the week is complete) — an invariant check right before the action is an ordinary detector;
- every calculation and aggregation on correct input (CS7) — period boundaries, units, rounding, duplicates; where a gate loss depends on one, its verification oracle comes from a reference the fault cannot reach (historic figures, a hand calculation, a parallel run);
- every repeater of the outside actions, every cached belief, every input, every own cycle or deadline, every awaited event, every resource, every shared limit, every kept state after a restart, every unconfirmed effect, every combination (two errors at once, a restart in the middle of a write).

A detectable error with no scenario goes to the ignore test (§10). A scenario that reveals a loss intake did not select (a webhook secret in a new alert channel → L9) is a consistency-gate finding for the sponsor. A scenario answered by a definition (what "the week" is) is a single decision for its owner.

## 6. Step 3 — The reaction matrix (C12)

One row per error with its own handling. Rows come from three sources:

| Source | Row |
| --- | --- |
| a constraint's requirement (top-down) | known before the architecture as a demand; now gets its detector and its concrete immediate action |
| a detectable error of the design (bottom-up, §5) | mapped to an immediate action and a remedy that already exist |
| an outside action (standing, §7) | the unexpected error at that action |

| Column | Rule |
| --- | --- |
| Error | a category, merged states separated by · |
| Detected by · reference | the detector, when it looks, and what it compares against (§6a) |
| Root causes | the causes behind the error — a column, not rows |
| Immediate action | one action or a short sequence; sub-cases only where one detector tells them apart — each branch named, each tested |
| Bound → then | always bounded, may chain; counted in the cycle of the thing that is late, or as calendar time or the run's deadline; wall-clock in brackets where cycles differ; checked against the time to loss; a counter behind "n in a row" survives a restart |
| Remedy | `M-` by whom or what — or "none needed" |
| Log | counter · warning · critical |
| Source | the constraint, UCA or scenario and hazard; or an operational requirement (`F/NF-<FEAT>-n` — support, availability, security outside the selected losses); or *unexpected at <action>*. A row with none is a gap (§10) or noise |

**Merge before you add**: rows with the same immediate action, bound, remedy and log level are one row. **Show by decision**: to the sponsor, rows that share an immediate action, deadline and owner are one business decision, asked once and carried forward from C15, C10a and C17 where already given; the technical differences go to IT in one block (RULES §5). A guard that refuses as specified is a requirement, not a row; it gets a row only for what happens after the refusal.

**The harmless row** collects what the ignore test found harmless: counted, reported when frequent, with **per-source baselines**; each entry with a one-line reason and a revisit trigger.

**Bands** — for an output, a value band on its figures (a KPI outside the range a person set); per gate loss whose effect can be counted, an outcome-rate band (orders per day, credits per customer). Set by a person or a second source, never derived from the product's own outputs. A gate loss without a countable effect gets one line saying why not.

### 6a. Detectors

- **Independent** — no detector, alarm channel or stored safe state depends on the resource whose failure it reports; a job cannot detect its own absence, crash or hang — something outside does.
- **Against an independent reference** — for every effect on a gate loss that eliminate or prevent did not remove, at least one check compares against a reference the fault cannot reach (the counterparty's totals, a second source, a baseline set by a person). Independence is shown, not assumed: name the faults the check must catch and the dependencies it shares with them; keys and selection do not come from the state under test and are sourced outside the fault's failure domain. An unavailable or unverified reference is a gate finding.
- **Shared limits** (one credit per customer, a budget, a token cap) — reserved against before the effect; checked with two intents that are each allowed alone.

## 7. Unexpected errors — one standing row per outside action

An error nobody wrote a row for lands at the outside action it hits. Each outside action has one standing row, decided by where the error falls relative to the commit boundary (`losses-hazards.md` §3):

| Where | Immediate action |
| --- | --- |
| before the commit boundary | what the constraint's class says — typically hold, store the context, report; only where holding is safe |
| during (outcome unknown — a timeout, or an error answer the C16 code table classes as unclear) | reconcile against the counterparty, or at most once then a person — never a blind repeat, unless the ignore test shows a duplicate reaches no loss (recorded) |
| after | complete the product's own record; a failure there is the next row |

- **Process dies or hangs** — the standing row names the external mechanism that keeps the action safe (watchdog, redundant component with fencing, voter, receiver default), or it is a finding.
- **Repeaters** — every repeater of the action finds it repeat-safe; switching one off is never relied on alone.
- **Entry point** — a catch-all exists only in the profile's one action-boundary helper (which carries the standing rows) and at the entry point, as backstop for errors outside any outside action; the entry point's handling is declared and assessed against what the run has already started — it is not safe by construction.
- Every unexpected error is classified afterwards with the ignore test (§10).

## 8. Step 4 — Indication and storage (C13a, C13b, C13c)

- **Where** warning and critical events are kept durably (C13a) and **for how long** (C13b) — business questions, asked before the PM gate, one question when the same person owns both; container and console logs are ephemeral and do not count;
- a **fallback** while the store is down (a local file, flushed later); a **safe state survives a restart**, stored where it does not depend on what failed (C13c);
- **evidence before remedy**: a restart or a cleanup comes after the context is kept;
- each event carries what the remedy owner needs to diagnose and choose the remedy — which item, step, input, version, since when; no cell content or personal data unless a requirement allows it;
- counters for frequent harmless events, entries for the rest.

## 9. The error handling checked

- **UCA check** of every immediate action, remedy and repeater: not provided (nobody told, nothing stored, never triggered) · harmful (a silent fallback, an earlier answer reused, a restart that destroys the evidence, a retry that repeats a booking) · too early or too late (escalation, a remedy too late for the bridge) · too long or too short (retry forever, a safe state left too early, a change stopped half-way). A finding needing a new SC, hazard or row is written as such; otherwise the result goes into the row.
- **Common mode** — record in the *Independence* line what each detector, channel, watchdog and stored safe state depends on.
- **Feedback adequacy** — stored events and alarms are *inputs* to the remedy owner's picture, not the picture itself: check what they need to know, what they receive, whether it arrives in time and can be understood.
- **One return round** — what this check finds is answered once; what is still open becomes an `OQ-n` and a gate finding — never shown as covered.

## 10. Ignore test — back to STPA (C14)

For a detectable error with no scenario — found in step 2, while coding, in rescue, or as an unexpected error — and for every handler rescue finds without a row: *if the product ignored it and carried on, what would be wrong one step later — and does that reach a loss?*

| Result | What happens |
| --- | --- |
| reaches a loss, existing hazard | a missing **scenario** — the common case: a new row |
| reaches a loss, no hazard fits | a missing **hazard** — the rare case: a new hazard and its constraint with response; a gate loss → finding |
| reaches no selected loss, but support, availability or security needs it handled | an **operational requirement** and its row |
| reaches no loss | the harmless row |
| nobody can say | a work state, never a result: trace one step, ask the owner (C14), then the designer decides — still unclear counts as reaching a loss, recorded in an ADR with a revisit trigger |

Occurrences that recur before classification are counted per action and source; the threshold triggers the standing row's escalation.

## 11. Size

The ranges are prompts, not targets: above them, merge before you add; below them is fine when nothing is missing.

| Item | Typical |
| --- | --- |
| remedies | 2–6 |
| outside actions (or groups) | 1–5 |
| immediate actions | 2–10 |
| matrix rows | 3 per output (wrong · missing · late) + one standing row per outside action + 1–3 per requirement or error group, merged |

## 12. Living

A new scenario or error — a new dependency, an exception found while coding, an incident — gets its answer through a concept redo *before* code handles it: a requirement for eliminate, prevent or limit, a matrix row for detect and control, a recorded acceptance; until then the standing row of the outside action applies and the case is logged as a discovered requirement. Errors seen in operation are classified in the next rescue round (R0). Code that handles an error which maps to no row, no requirement and no standing row is drift.

## 13. Pitfalls

| Pitfall | Instead |
| --- | --- |
| Inventing actions in the error handling | Immediate actions and remedies come from the constraints; a missing one goes back to STPA |
| Asking the sponsor again what C15 already settled, or one question per matrix row | Carry answers forward; ask by decision, only what the design changed |
| Treating a proposed deputy, delivery time or alarm channel as given | Proposed until the responsible role confirms it (name, date); unconfirmed → OQ at the gate |
| A remedy no constraint asks for | No constraint, no remedy (except *classify and fix*) |
| Prescribing one immediate action for every product ("fail-silent") | The class per hazard; the bridge test decides whether holding is safe |
| A bridge test with the nominal remedy time | A justified bound — detect, decide, take effect, on the slowest realistic path; unknown → finding |
| Treating "reads only" as harmless | An output someone acts on is a control action — wrong, missing, late |
| Depth set for the whole product | Per effect; what eliminate or prevent removed does not count |
| Asking the sponsor how an API behaves | C16 to IT or the service owner; a probe if nobody knows |
| "Hold, store, report" after a timeout on a write, or an error answer read as "not done" | Classify every answer — happened · not · unclear (C16 code table, unknown codes = unclear); unclear: reconcile, or at most once then a person |
| Starting the matrix before the architecture | STPA first; detectors need modules to sit in |
| Listing errors per exception type | By category, with root causes as a column |
| A matrix row without a source | The constraint, the scenario, an operational requirement, or *unexpected at <action>* |
| A detector comparing against what the fault can reach, or one assumed independent | Name the faults and shared dependencies; keys not from the state under test; unverified → finding |
| "Not found" read as "not done" | Unknown until the visibility window has passed |
| A catch-all deep inside the code | The standing row at the outside action, via the one helper; otherwise only the entry point |
| A restart or a repeater that repeats the effect | Repeat-safe per action; "switched off" never relied on alone |
| Escalation counting all sources together | Per source — outside input must not stop everything |
| "Retry until it works", or waiting for a person without limit | A bound and a next step; time to remedy against how long the immediate action holds |
| Releasing an immediate action before the remedy, or without a named role | A next step (deputy, stronger action); released by the role named in C17 |
| Escalating through the failed resource | Independence (§6a, §9) |
| Stored events taken as the operator's process model | Check need, receipt, timeliness and understanding |
| A remedy that weakens a detector forever | It expires, or it is a concept change |
| One test per row taken as proof | One test per row is the minimum; timing, misleading feedback, partial effects, common mode and recovery interactions need their own |
| Writing the same rule as SC, row, UCA and requirement | Define each rule once, in its owning table; elsewhere cite the ID |
