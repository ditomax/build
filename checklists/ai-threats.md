# AI Threats — STPA-light Catalogue

**Version 1**

**Threats specific to products with AI components, read as STPA causes. Used in concept §9 (*Causes* column of the UCA table), in requirement *Origin* and in synthesis §13 (adversarial tests).**

Companion to `checklists/losses-hazards.md`. Built only from public sources (list below). IDs `T01–T22` are catalogue references like checklist rows — they get no numbering of their own in a concept. Apply the catalogue when the system card (concept §0) names an AI component; skip the rows whose phase or asset does not exist in the product (no training → T01, T02 skipped with one line).

---

## Catalogue

| T | Threat | STPA reading | Cause | Typical losses | Typical constraint | Typical test |
| --- | --- | --- | --- | --- | --- | --- |
| T01 | Training / fine-tuning data poisoning | bad input into the model's learning | CS3 | L4, L3 | approved data sources with provenance; review before training | evaluation set with known-bad samples |
| T02 | Backdoor in a model or adapter | compromised controller algorithm via the supply chain | CS3 | L3, L4 | models only from approved sources, hash-pinned | behaviour check on trigger inputs after every model change |
| T03 | Learning-loop poisoning — manipulated feedback or corrections shift behaviour | corrupted feedback corrupts the process model | CS2 | L4 | feedback validated before it enters the loop; rate limit per user | injected feedback burst does not change the output |
| T04 | RAG / knowledge-base poisoning | corrupted source of the process model | CS1, CS3 | L4, L8 | write access to the corpus restricted; source shown with every answer | planted document is not cited as fact |
| T05 | Direct prompt injection, jailbreak | data channel becomes control channel | CS3 | L3, L8, L9 | system rules outside user reach; output checks for what must never leave | jailbreak set in synthetic test data |
| T06 | Indirect prompt injection (documents, mail, web, tool results) | data channel becomes control channel | CS3 | L3, L2, L8, L9, L10 | retrieved content is quoted as data, never executed as instructions; tool calls from retrieved content need confirmation | injection set: manipulated documents and tool results |
| T07 | Evasion — adversarial input to a classifier | bad input to a deciding component | CS3 | L4 | confidence threshold; "cannot decide" path | adversarial samples per class |
| T08 | Sensitive information disclosure | UCA type P on output | CS6 | L2, L8, L9 | least data in context; output filter for personal data and secrets | probing questions for data from other users or tenants |
| T09 | Membership inference, model inversion | adversarial queries | CS3 | L2 | no personal data in training; query rate limits | — (training on personal data → flag at the PM gate) |
| T10 | Model extraction by querying | adversarial queries | CS3 | L8, L6 | rate limits and quotas; no raw scores in outputs | quota test |
| T11 | System prompt / configuration leakage | UCA type P on output | CS6 | L8, L9, L3 | no secrets in prompts; prompt treated as public | extraction prompts in the injection set |
| T12 | Improper output handling — output becomes payload downstream (XSS, SQL, commands) | downstream controller trusts AI output | CS3 | L3, L1, L4 | AI output validated and escaped like any untrusted input | payloads in model output reach no interpreter |
| T13 | Excessive agency, tool misuse | authority beyond constraints | CS6 | L1, L3, L10 | least privilege per tool; write actions need confirmation or a hard limit | tool call outside the allowed set is refused |
| T14 | Identity and privilege abuse of agents | authority beyond constraints | CS6 | L3, L9 | one identity per agent; scoped, short-lived credentials | agent credential cannot reach another tenant |
| T15 | Unexpected code execution | authority beyond constraints | CS6 | L3, L1, L10 | no execution of generated code outside a sandbox without network and secrets | sandbox escape attempts fail |
| T16 | Supply-chain compromise (models, datasets, libraries, MCP servers) | bad input via components | CS3 | L3, L9 | approved sources, pinned versions, SBOM including models and MCP servers | dependency and model hash check in CI |
| T17 | Insecure inter-agent communication, cascading failures | uncoordinated controllers, wrong feedback | CS5, CS2 | L5, L4 | authenticated messages between agents; a failing agent degrades, never cascades | one agent down → defined degraded result |
| T18 | Resource exhaustion, denial of wallet | UCA type D | CS2 | L6, L5 | token budget per call, quota per user, provider spending cap | budget exhaustion test |
| T19 | Over-reliance, misinformation | process model flaw in the **human** controller | CS1 | L4 | uncertainty and sources visible; confirmation step for consequential actions | usability check: is "not sure" visible? |
| T20 | Rogue agent, goal drift | missing higher-level control | CS6, CS2 | L3, L4, L11 | human override and kill switch; actions logged and reviewed | kill switch drill |
| T21 | Unvalidated model update or retraining (silent behaviour change) | process model no longer matches the new model | CS1 | L4, L5, L11 | regression evaluation per model version; rollback to the previous model | evaluation set run on every model change |
| T22 | Insecure retirement (data and model remnants, orphaned keys and endpoints) | control ends, assets remain | CS6 | L2, L3, L8, L9 | retirement checklist: delete data and models, revoke keys, close endpoints | — (checked at the retirement point) |

A model or provider change, a new data source, a new tool or permission, wider exposure, a new learning loop or an incident are **review triggers**: the Director asks for them at the start of a new feature and of every rescue round (question R0).

---

## Sources

- NIST AI 100-2 E2025 — *Adversarial Machine Learning: Taxonomy and Terminology*
- OWASP Top 10 for LLM Applications 2025
- OWASP Top 10 for Agentic Applications 2026
- MITRE ATLAS (technique lookup per finding)
- ETSI EN 304 223 — *Securing Artificial Intelligence* (principles for maintenance and end of life)

---

## Learning Rule

When a project meets an AI threat that none of these rows describes, add it with its STPA reading, cause, losses, a constraint and a test before closing the work.
