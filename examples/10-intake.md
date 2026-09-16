---
stage: intake
owner: build-intake
status: done
revision: 2
created: 2026-09-18T09:05
updated: 2026-09-21T15:40
input: planning/maquette/OFA-offer-draft-assistant/60-brief.md@2 (contract H2/2), planning/maquette/OFA-offer-draft-assistant/vcode/ (frozen)
feature: OFA
path: mixed
---
<!-- Example — fictitious Example GmbH. Shows what a finished file looks like; not a template. -->

# Intake OFA: Offer draft assistant

_Result of the intake stage. Part 1 is what the prototype and the brief already answer (Path B — extracted, not asked). Part 2 is what had to be grilled (Path A). Part 3 is the draft the concept stage starts from. Nothing here is ratified; the concept stage numbers and freezes._

## Part 1 — Extracted from the brief and the prototype (Path B)

### Field of application

Internal web app (document assistant) for sales engineers: CRM opportunity in, offer draft out (brief Part 3).

### Purpose and the specific person

"Our engineers spend two hours per offer copying data and hunting for old offers, so customers wait three days for a quote." The person: a sales engineer with three requests in the inbox on a Monday morning (brief Part 1). Build started after the Head of Sales approved it at the sales leadership meeting on 2026-09-17 (brief Part 4).

### What the prototype really does

| Behaviour | In vcode/ | Real / faked / scripted | Evidence (file, line or screen) |
| --- | --- | --- | --- |
| Opportunity list and navigation | yes | real, state in memory, lost on reload | `index.html`, `app.js`; slice S1 |
| Prefill of 16 of 19 offer fields with origin tag | yes | real mapping from mock record columns | `data/data.js` 19-field list with source mapping; S1 passed |
| Missing-field detection (3 amber fields) | yes | real, record vs. field list; validation only "not empty" | S1 passed; brief shortcuts |
| Text-block suggestions per pump series | yes | scripted — fixed list per series, no ranking or search | `data/data.js` 12 blocks, 9 earlier offers; S2 passed |
| Unit conversion | partial | real for l/s → m³/h only | brief prototype facts |
| Preview with origin count | yes | real HTML render, count computed | S3 passed ("16 from CRM · 3 typed · 2 blocks reused") |
| Export to Word | no | faked — disabled button with a note | S3: "Export stays disabled" |
| Saving, printing, narrow screens | no | not built — no persistence, ≥ 1024 px, no page breaks | brief shortcuts |

Brief and code agree; no discrepancy found.

### Candidate requirements taken over

| Brief ID | Requirement | Status in maquette | Intake verdict |
| --- | --- | --- | --- |
| cF-1 | List of open opportunities with customer, pump series, quantity, stage | shown | confirmed by vcode/ (S1) |
| cF-2 | Offer fields prefilled from the CRM record, each with an origin tag | shown | confirmed by vcode/ (S1) — real data: #1 |
| cF-3 | Fields the record does not provide, or provides empty, flagged as missing | shown | confirmed by vcode/ (S1) |
| cF-4 | Text blocks from earlier offers of the same pump series suggested | partial | to grill (Part 2) — lookup scripted; OQ-2, OQ-3 |
| cF-5 | Preview with all sections and a count of values by origin | shown | confirmed by vcode/ (S3) |
| cF-6 | Each suggested block shows the earlier offer's number and date | shown | confirmed by vcode/ (S2) |
| cF-7 | Word export based on the binding offer layout | not shown | to grill (Part 2) — OQ-7, OQ-9 |
| cF-8 | Flow rates in l/s converted to m³/h before reuse | partial | to grill (Part 2) — OQ-11 |
| cU-1 | One click from an opportunity to its workbench | shown | confirmed by vcode/ (S1) |
| cU-2 | Each suggested block can be accepted or rejected | shown | confirmed by vcode/ (S2) |
| cU-3 | Preview enabled only when no missing field is empty | shown | confirmed by vcode/ (S3) |
| cNF-1 | Draft assembled in under 10 minutes (today ~2 h) | partial | confirmed on mock data (Q5: 7 min [evidenced]); real data untested → Part 2 |
| cNF-2 | Preview prints on A4 without cut-off sections | partial | to grill (Part 2) |
| cNF-3 | Missing-field markers readable without colour | shown | confirmed by vcode/ (harden fix) |

### Guardrails taken over

| GR | Guardrail | Origin | Intake verdict |
| --- | --- | --- | --- |
| GR-1 | No real customer or contact data in the maquette (waiver W-1) | seed | maquette-only; the product handles real data → data protection OQ-4 |
| GR-2 | No prices, discounts or margins — pricing stays in the ERP quotation | seed | out of scope; price section per OQ-8 |
| GR-3 | Every value shows its origin tag | seed | cNF candidate |
| GR-4 | Draft never sent, nothing written back to the CRM | seed | out of scope |
| GR-5 | No CRM connection; mirror the export's shape | seed | maquette-only; the product's CRM access is grilling #2 |
| GR-6 | Files only: no server, no install, no external calls | plan | maquette-only for server/install; "no external calls" → cNF candidate (#11) |
| GR-7 | Blocks only from offers of the same pump series | build | cF candidate |

### Deliberately-cannot rows (brief Part 2)

| Cannot | Reason category | Intake verdict |
| --- | --- | --- |
| Connect to the real CRM or read real records | data | requirement not shown (#1, #2) |
| Show or calculate prices, discounts, margins | scope | out of scope (GR-2) |
| Save a draft | time | requirement not shown (#12) |
| Find blocks by similarity | decision pending | to grill (decision pending) — OQ-2, OQ-3 |
| Export to Word or send the offer | decision pending | to grill (decision pending) — export OQ-7, OQ-9; sending out of scope (GR-4) |
| Handle hundreds of opportunities, search or filter | scope | out of scope for now (#13) |

### As-built architecture sketch

Plain HTML + JavaScript, no framework, no build step; entry `vcode/index.html`; files `index.html`, `app.js`, `styles.css`, `data/data.js`. Mock data frozen 2026-09-01: 6 opportunities, 4 customers, 9 earlier offers (3 series × 3), 12 text blocks, the 19-field list with source mapping; column names follow the real CRM export header row. Design: variant B "two-column workbench" (fields left, blocks right). **Reuse:** the 19-field list and source mapping as the data-model seed; the origin-tag and origin-count pattern. **Do not reuse:** `app.js` state handling, the scripted block lookup, the mock records.

**Slices and acceptance criteria as built:** S1 opportunity → prefilled workbench ("OPP-104 shows 16 fields tagged CRM and 3 flagged as missing"). S2 block suggestions ("three P-80 blocks with number and date; accepting two marks exactly those"). S3 preview ("16 from CRM · 3 typed · 2 blocks reused; Export stays disabled"). All passed.

### Decided and open questions carried over

- **Decided:** OQ-2 (maquette: nine mock earlier offers; real search out of scope) · OQ-7 (maquette: neutral layout; binding template decided with cF-7) · F1 (frozen mock data) · F3 (data in one file, no fetch). OQ-2 and OQ-7 were decided for the maquette only and are re-asked for the product; F1 and F3 are maquette-only and not carried.
- **Open:** OQ-1, OQ-3, OQ-4, OQ-5, OQ-6, OQ-8, OQ-9, OQ-10, OQ-11.

## Part 2 — Grilling (Path A)

### Agenda worked through

| # | Topic | Source | Answer (short) | Evidence |
| --- | --- | --- | --- | --- |
| 1 | OQ-1 / riskiest assumption — five-record test (Part 4 first step) | brief | Run 2026-09-18 with sales ops on 5 real records: 14 of the 16 mapped fields filled in all 5; delivery address empty in 2, contact e-mail empty in 1; the 3 fields without a column confirmed absent | [evidenced] — mapping sheet |
| 2 | How does the product read CRM data? (new: OQ-12) | GR-5 | Nightly CSV export of open opportunities to the sales share, same header row as the sample; no API licence | [evidenced] — CRM admin |
| 3 | OQ-10 — the 3 fields without a column | brief | Lead time, pump curve reference and warranty variant are typed every time for now | [evidenced] — Head of Sales |
| 4 | OQ-2 — where earlier offers live | brief | Sales ops keeps an offer register (Excel: offer no., date, customer, series, file path) since 2023; files are Word documents | [evidenced] — sales ops |
| 5 | Can blocks be cut out of old offers by heading? (new: OQ-13) | #4 | Probably — sections "Scope of supply" and "Technical notes" — not checked | [unknown] |
| 6 | OQ-3 — approved blocks, standard terms | brief | Blocks only from offers sent in the last 36 months; standard terms never reused, always taken from the current terms file owned by Legal | [evidenced] — Head of Sales, Legal |
| 7 | OQ-7 / OQ-9 — layout and format | brief | Word, on the current offer template; the engineer finishes it and creates the PDF himself | [evidenced] — Head of Sales |
| 8 | Do origin tags go into the Word file? | GR-3 | No — tags stay in the tool; the customer document is clean | [evidenced] |
| 9 | OQ-8 — price section | brief | Placeholder the engineer fills from the ERP quotation | [evidenced] |
| 10 | OQ-11 — units | brief | Flow standard m³/h; head standard m; older offers sometimes give head in bar, which needs the fluid density — flag, never convert | [evidenced] — engineering lead |
| 11 | OQ-6 — deployment; external calls | brief, GR-6 | Internal Linux VM run by IT; no calls outside the company network | [evidenced] — IT |
| 12 | Saving drafts (brief "cannot save") | brief Part 2 | Needed — engineers pause for technical clarification | [evidenced] |
| 13 | Many opportunities, search | brief Part 2 | About 40 open at a time; a sorted list is enough for the first release | [estimated] |
| 14 | OQ-5 — access | brief | Sales engineers only in the first release; inside sales later | [evidenced] |
| 15 | OQ-4 — data protection | brief | Data protection officer consulted 2026-09-19: contact name and e-mail may be used; drafts need a retention period, not yet set (new: OQ-14) | [evidenced] / [unknown] |
| 16 | cNF-2 — A4 printing | brief | Not needed; engineers print from Word | [evidenced] |
| 17 | Q5 surprise — "which of these did I type?" | brief | Yes: preview should show typed values distinctly | [evidenced] — Head of Sales |

### Field-standard concerns

Source: `checklists/requirements.md` — Cross-Cutting, "Web application" (closest match to "internal web app, document assistant"), "By Technology / Artifact".

| Concern | Standard / check | Applies | Candidate requirement or deferral rationale |
| --- | --- | --- | --- |
| Contact names and e-mails | GDPR | yes | d-6; retention OQ-14 |
| Org security; accessibility | ISO/IEC 27001; WCAG 2.2 AA | deferred | Not certified; private internal tool [evidenced]. Keep cNF-3 and fix focus order (brief shortcut) — d-8 |
| Public-facing audit; rate limiting | OWASP ASVS; rate limits | deferred | Internal only, ~6 users [evidenced] |
| AuthN/AuthZ, secrets, HTTPS | Web application, Technology | yes | d-5, d-7 |
| Deployment and rollback | Web application | yes | d-9 |
| Observability | Web application | yes | d-10 |

### New candidate requirements

| Draft ID | Requirement | Type | Origin | Evidence |
| --- | --- | --- | --- | --- |
| d-1 | Save a draft and reopen it | F | #12 | [evidenced] |
| d-2 | Head values in bar are flagged, never converted | F | #10 | [evidenced] |
| d-3 | Price section is a placeholder | F | #9 | [evidenced] |
| d-4 | Standard terms from the current terms file; blocks only from offers ≤ 36 months | F | #6 | [evidenced] |
| d-5 | Access for group "sales engineers" via single sign-on | NF | #14 | [evidenced] |
| d-6 | Only contact name and e-mail stored; drafts purged after retention | NF | #15 | [unknown] — OQ-14 |
| d-7 | HTTPS only; no credentials in the repository; no external calls | NF | checklist, #11 | [evidenced] |
| d-8 | Logical keyboard focus order | U | checklist, brief shortcut | [evidenced] |
| d-9 | Release can be rolled back | NF | checklist | [evidenced] |
| d-10 | Structured logs without customer content | NF | checklist | [evidenced] |
| d-11 | Preview shows typed values distinctly | U | #17 (Q5) | [evidenced] |
| d-12 | Word export carries no origin tags | F | #8 | [evidenced] |

### Still open after grilling

- **OQ-13** — Do offers since 2023 use the section headings consistently enough to cut blocks? — sales ops, sample of 20 offers, by 2026-09-30
- **OQ-14** — Retention period for saved drafts — data protection officer, by 2026-10-02

## Part 3 — Draft for the concept stage

- **Path:** mixed — flow, field list, origin pattern and preview come from the prototype (B); CRM access, block source, export, saving and all field concerns were grilled (A).
- **Ubiquitous language seeds:** the brief's nine terms (opportunity … origin count) unchanged; new: offer register, terms file, price placeholder.
- **Out of scope (for now):** prices (GR-2); sending, CRM write-back (GR-4); similarity search; search and filter; inside-sales access; printing from the tool.
- **Integration points known:** nightly CRM CSV export; offer register and offer archive; current Word offer template; terms file; company single sign-on.
- **Riskiest assumption after intake:** "the CRM export contains every field an offer needs" — holds for 14 of 19 fields; 2 are sometimes empty (handled as missing), 3 have no column (typed). New riskiest: old offers can be cut into blocks by heading (OQ-13).
- **Recommended first vertical slice:** one real opportunity from the nightly export → workbench with origin tags and missing flags → preview with origin count.

## Notes (human)

Head of Sales, 2026-09-21: the 14-of-19 result is good enough to go on — keep the missing-field flow prominent.
