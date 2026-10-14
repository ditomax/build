---
stage: intake
owner: build-intake
status: done
revision: 2
created: 2026-10-14T20:05
updated: 2026-10-14T21:02
input: ../maquette/EXF-service-report-assistant/60-brief.md@2 (contract H2/3), ../maquette/EXF-service-report-assistant/vcode/ (frozen)
feature: SRA
path: mixed+R
---
<!-- Example — fictitious Example GmbH (test run-04). Intake on Path mixed+R from an H2/3 brief: Register trace with coverage close. Not a template. -->

# Intake SRA: Service report assistant

_Result of the intake stage. Part 1 is what the prototype, the brief, the register or the shortlist entry already answer (Path B, R, S — extracted, not asked). Part 2 is what had to be grilled (Path A). Part 3 is the draft the concept stage starts from. Nothing here is ratified; the concept stage numbers and freezes._

## Part 1 — Extracted from the brief, the prototype, the register or the shortlist entry (Path B · R · S)

### Field of application

Mobile web app for field service technicians (phone, offline-capable: voice note, draft check, approval) with a backend service that calls an EU-hosted speech-to-text / LLM provider and the ERP. The brief's "small dispatch view in the browser" moves to feature DSB (see Register trace, R-9). Checklist sections used: Cross-Cutting, Web application, Mobile application, Backend / API service, Data / ML system, By Technology, General Robustness (Software), Operating Cost, Capacity & Scaling.

### Purpose and the specific person

Our technicians type every service report at night — 25 minutes each, 40 a day — and customers still get them days late and in different shapes. For the field service technician with five to six visits a day who types reports at the kitchen table at 9 pm: dictate a short voice note after the visit, get a draft on QM-F-12, check it in at most 5 minutes, approve it — nothing leaves without the technician, and an invented value cannot be overlooked.

### What the prototype really does

| Behaviour | In vcode/ | Real / faked / scripted | Evidence (file, line or screen) |
| --- | --- | --- | --- |
| Visit list "My visits today" with status badge per visit | yes | real (in-session state), data scripted | index.html `list()`, `visits` array (3 visits), `label` map |
| Draft on QM-F-12 with findings, parts, measurements, next maintenance | yes | scripted — drafts are inline constants, no AI, no STT | `visits[0].draft`, `reconnect()`; form only labelled "Form QM-F-12", no real layout |
| Fault code | text only | scripted — "fault E07" inside the findings string, no fault code list, no validation | `draft.findings` |
| Source passage per value | yes | real — snippet cut from the transcript around the value's key | `snip()`, `row()` |
| Flag "not in voice note" | yes | real — substring test of a hand-set `key` against the transcript | `inNote()` = `transcript.includes(key)`; the key is set by hand per value, not extracted |
| Approval blocked while a measurement is flagged | yes | real — only measurements are checked, parts are not | `document.getElementById("ap").disabled = d.meas.some(...)` |
| Correction of a flagged value | measurements only | real; corrected value is accepted unchecked and shown "corrected by technician" | `fix()` |
| Flagged part | renders an input and "Correct" button, but `fix(id,-1)` reads `meas[-1]` → error | defect — brief says "parts have no correction field"; the code renders one that fails | `row("Part",p,-1)` → `fix(${v.id},-1)` |
| Timer from opening to approval | yes | real, display only — not stored | `openedAt`, `setInterval`, kept across a correction |
| Approve and send | yes | faked — sets status "sent", no e-mail, no ERP | `approve()`, `sentView()` text "not sent — maquette" |
| Log line "approved by …, time, version 1" | yes | faked — hard-coded name "J. Huber" and date 2026-10-14, not stored | `approve()` `v.log` |
| Named technician | header text | faked — no login; name hard-coded | `<header>Service reports · J. Huber` |
| Offline voice note, later upload | yes | scripted — "Simulate connection" button replaces state with a second scripted draft; nothing is recorded | `reconnect()`; alert text in `openVisit()` |
| "Voice note: deleted after transcription" | label | faked — nothing is recorded or deleted | `openVisit()` small text |
| Sample-data banner | yes | real | `.sample` div |
| Persistence | none | state lost on reload | no storage API used |

Brief "Prototype facts" and code agree except: (1) the flagged-part defect above (brief: "parts have no correction field"); (2) the approval block covers measurements only, while R-M2 says "a value". Code wins; both go to the agenda (#4).

### Candidate requirements taken over

| Brief ID | Requirement | Status in maquette | Intake verdict |
| --- | --- | --- | --- |
| cF-1 | Four parts on QM-F-12 with valid fault codes | shown | confirmed for the four parts (`openVisit()`); layout and fault codes faked → d-1, d-2 (R-5, R-12) |
| cF-2 | Source passage per value; value without passage flagged | shown | confirmed by vcode/ (`snip()`, `inNote()`); key extraction still to grill → d-3 (R-7) |
| cF-3 | Visit list with report status | shown | confirmed by vcode/ (`list()`) → d-9 (R-M1) |
| cF-4 | Offline voice note "recorded offline — will upload", draft after upload | shown | UI confirmed; upload scripted → to grill → d-8 (R-3) |
| cF-5 | Voice note shows "deleted after transcription" | partial | to grill (Part 2 #6) → d-12 (R-15) |
| cF-6 | Dispatch list of drafts not approved 24 h after the visit, reminder | not shown | → feature DSB (R-9) |
| cF-7 | Sent report shows its log line | partial | to grill (Part 2 #7) → d-7 (R-19) |
| cF-8 | "Approve and send" disabled while a measurement is flagged | shown | confirmed for measurements (`ap.disabled`); parts gap → to grill → d-4 (R-M2) |
| cF-9 | Offline visit explains the voice note is safe and uploads later | shown | confirmed by vcode/ (alert in `openVisit()`) → merged into d-8 (R-3) |
| cU-1 | Inline correction of a flagged value | partial | measurements confirmed (`fix()`), parts defective → to grill → d-5 (R-2) |
| cU-2 | "Approve and send" is the only way out; records the named technician | shown | button confirmed; named technician hard-coded → d-6 (R-4), identity via SSO d-14 |
| cNF-1 | Only fictitious sample data, labelled | shown | maquette only → out of scope for the product; replaced by GR-2 test-data rule (d-24) |
| cNF-2 | Timer opening → approval, target ≤ 5 min | shown | display confirmed; not stored → to grill → d-10 (R-2) |

### Guardrails taken over

| GR | Guardrail | Origin | Intake verdict |
| --- | --- | --- | --- |
| GR-1 | EU-hosted processing under a DPA, deletion right after transcription, customer data only in the ERP (R-18) | seed / K.o. V8 | cNF candidate → d-11, d-12, d-13 |
| GR-2 | No real personal data in the maquette | seed | out of scope as stated (maquette only); product rule: no real customer voice notes before OQ-4 is closed → d-24 |
| GR-3 | A wrong result must be visible as such (R-7, V7) | seed | cNF candidate → d-3, d-4 |
| GR-4 | Sent only after approval of the named technician; no automatic sending, not even after 24 h (R-4) | seed | cNF candidate → d-6 |
| GR-5 | Layout QM-F-12, valid fault codes (R-12) | seed | cNF candidate → d-2 |
| GR-6 | Voice note never lost when recorded offline (R-3) | seed | cNF candidate → d-8 |
| GR-7 | Monthly AI cost cap with alert (R-17) | seed | cNF candidate → d-15 |
| GR-8 | Not shown: no real AI, ERP or e-mail in the model | seed | out of scope (maquette only) |
| GR-9 | Access only via SSO with company accounts (R-16) | plan | cNF candidate → d-14 |
| GR-10 | Every sent report logged: who, when, which version (R-19) | plan | cNF candidate → d-7 |

### Deliberately-cannot rows (brief Part 2)

| Cannot | Reason category | Intake verdict |
| --- | --- | --- |
| Generate a draft from a real voice note (no AI, no STT) | scope | requirement not shown → d-1 |
| Measure real checking time of real technicians | data | requirement not shown → d-10; riskiest-assumption test (OQ-8) |
| Actually send an e-mail or write to the ERP | scope | requirement not shown → d-16 |
| Show upload failures or many visits | scope | requirement not shown → d-8, d-21 |
| Prove EU hosting, deletion, cost cap or SSO (R-14 … R-18) | decision pending | to grill (decision pending) → #6, OQ-4, OQ-7 |
| Show the dispatch 24-hour list (R-9) | time | → feature DSB |

### As-built architecture sketch

One static `index.html` with inline CSS and JS, no build, no server, no network, no storage; data inline (3 visits, 2 transcripts, 2 drafts), described in `vcode/data/SOURCE.md`. Modules by function: `list()` visit list · `openVisit()`/`row()` draft view with source passage and flag · `inNote()`/`snip()` flag rule · `fix()` correction · `approve()`/`sentView()` approval and log line · `reconnect()` offline simulation. Design: plain style, design stage skipped. Reuse: the flag rule ("value not in transcript → flagged, approval blocked") as acceptance-test idea and the two sample transcripts as test data. Not reused: the page itself — rebuild from concept.

**Slices and acceptance criteria as built:** S1 check and approve one draft — passed (invented 55 °C flagged, approval blocked, correction unblocks, sent with log line) · S2 offline voice note — passed (offline visit becomes a draft after "connection") · S3 dispatch 24-hour list — dropped. Seeds for 30-synthesis §13.

### Decided and open questions carried over

- **Decided:** OQ-3 (IT operates integration and SSO; ERP read access via the existing interface) → I7 prefill, d-16 · OQ-5 (waiting > 24 h list with reminder, never automatic sending) → feature DSB, GR-4 · OQ-6 (register v2 arrived) → nothing to carry · OQ-7 maquette part (model runs in the laptop browser) → nothing to carry.
- **Open:** OQ-1 ISO 9001 procedure (QM lead) · OQ-2 liability (Head of Service with legal) · OQ-4 verify R-18 in the provider contract (IT lead, DPO) · OQ-7 product part: how technicians reach the app, where it runs · OQ-8 STT copes with dialect and plant-room noise · OQ-9 block or warn on a flagged value.

### Register trace (Path R)

- **Register:** ../idea/10-register.v2.md@2 (via brief H2/3) · **outcome:** go-with-conditions (conditions R-18, R-19) · **complete:** yes — round 2, IT lead
- **K.o. conditions → guardrails:** R-18: EU hosting under DPA, deletion after transcription, customer data only in ERP (GR-1) · R-19: every sent report traceable — who, when, version (GR-10) · R-13: unknown, resolvable by procedure change → OQ-1 · V3, V6: pass — no guardrail.

| Item | Priority | Statement (short) | Coverage in maquette | Verdict | Draft ID |
| --- | --- | --- | --- | --- | --- |
| R-2 | MUST | Checking a draft ≤ 5 min | faked | → d-10, d-5 (real checking time to grill; riskiest assumption) | d-10, d-5 |
| R-3 | MUST | Works offline; voice note uploaded later | faked | → d-8 (reconnection scripted; real upload grilled) | d-8 |
| R-4 | MUST | Sent only after approval of the named technician | shown | → d-6 (vcode: button only; identity hard-coded → with d-14) | d-6 |
| R-5 | MUST | Draft has findings, parts, measurements, next maintenance | faked | → d-1 (draft scripted; real generation grilled) | d-1 |
| R-7 | MUST | AI invents a value, technician misses it | shown | → d-3, d-4 (flag rule real, key hand-set) | d-3, d-4 |
| R-12 | MUST | QM-F-12 layout and fault code list | faked | → d-2 (label only; fault code list grilled) | d-2 |
| R-14 | MUST | Voice notes (personal data) must not leave the EU | not shown (later) | → d-11 | d-11 |
| R-15 | MUST | Voice notes deleted right after transcription | faked | → d-12 (label only) | d-12 |
| R-16 | MUST | Access only via SSO with company accounts | not shown (later) | → d-14 | d-14 |
| R-17 | MUST | Monthly AI cost cap with alert at 80 % | not shown (later) | → d-15 | d-15 |
| R-18 | MUST | EU-hosted services under DPA, deletion, customer data only in ERP | not shown (later) | → d-11, d-12, d-13 (contract part: OQ-4) | d-11, d-12, d-13 |
| R-19 | MUST | Every sent report logged (who, when, version), kept with the ERP record | faked | → d-7 (log line not stored) | d-7 |
| R-9 | SHOULD | Report reaches the customer within 24 h | not shown (later) | → feature DSB (dispatch list with reminder, brief cF-6, OQ-5); SRA supplies visit and send timestamps (d-17) | — |
| R-10 | SHOULD | Parts in the report match the ERP stock booking | not shown (later) | deferred: v1.1 — stock is booked by the back office up to two days after the visit, so a comparison at approval time would flag most reports; reconciliation needs that booking step changed first (sponsor) | — |
| R-1 | COULD | Status quo: ~25 min per report, 3–5 days delay, ~1 h/day dispatch calls | not shown (later) | → d-17 (baseline for the KPIs time per report and visit-to-sent time) | d-17 |
| R-8 | COULD | Liability for a disputed AI-drafted report | not shown (later) | deferred: question item, no requirement until OQ-2 is answered | — |
| R-13 | COULD | ISO 9001 documentation procedure acceptance | not shown (later) | deferred: question item, no requirement until OQ-1 is answered (d-23 holds the record-keeping side) | — |
| R-6 | — | Photos attached to the report | out | out: won't (round 2) — photos add personal data and storage questions beyond R-18; revisit after first release | — |
| R-11 | — | Customer portal | out | out: won't — no customer portal today; e-mail delivery covers R-9 | — |
| R-M1 | SHOULD (proposed) | Technician sees today's visits with report status | shown | → d-9 (origin maquette, brief cF-3) | d-9 |
| R-M2 | SHOULD (proposed) | Approval blocked while a value is flagged and uncorrected | shown | → d-4 (origin maquette, brief cF-8; block vs. warn = OQ-9) | d-4 |

**Count:** register items 19 · rows 21 · MUST 12: mapped 12 · other feature 0 · deferred 0 · open 0 — all rows: mapped 15 · other feature 1 · deferred 3 · out 2 (+ 2 maquette items R-M1, R-M2, priority as proposed by maquette, not committee)

### Taken from the shortlist entry (Path S)

not relevant for this feature

## Part 2 — Grilling (Path A)

### Agenda worked through

| # | Topic | Source | Answer (short) | Evidence |
| --- | --- | --- | --- | --- |
| 0 | I0 demo match — anything the prototype did that the brief does not mention? | brief / vcode | "Matches. In the demo nobody tried a wrong part — fine that parts must be correctable too." → d-5 covers parts | [evidenced] |
| 0R | I0R register trace block — anything you would map differently? | brief Part 3 | Accepts all MUST mappings. R-9 → own feature DSB (dispatch dashboard, later). R-10 deferred to v1.1 (reason above). R-M1/R-M2 keep "SHOULD (proposed)". | [evidenced] |
| 1 | R-5 faked: how is a real draft produced? | coverage faked | Voice note 1–4 min, German, dictated in the van right after the visit; draft must be ready within 10 min of upload | [estimated] |
| 2 | R-12 faked: fault code list and QM-F-12 | coverage faked | Fault code list (~140 codes) is maintained by QM as an Excel export, updated ~2×/year; QM-F-12 is a PDF form with 4 sections + header (customer, unit serial, date, technician) | [evidenced] |
| 3 | R-7: how is a "value" identified in the draft? | vcode key hand-set | Every measurement and part number in the draft must be traceable to a transcript passage; the AI must return the passage, the product checks it literally (number + unit) | [estimated] |
| 4 | R-M2 / vcode defect: flagged parts | vcode discrepancy | Parts are values too: a part not in the voice note is flagged, correctable, and blocks approval | [evidenced] |
| 5 | R-3 faked: offline upload | coverage faked | Recordings stay on the phone until the server confirms receipt; typical offline span ≤ 4 h, worst case next morning | [estimated] |
| 6 | R-15 / R-18 / cF-5: deletion | coverage faked, decision pending | Audio deleted at the provider and on our server once the transcript exists; on the phone once the upload is confirmed. Contract proof → OQ-4 | [evidenced] for the rule, [unknown] for the contract (OQ-4) |
| 7 | R-19 / cF-7: audit log | coverage faked | Log entry (technician, timestamp, version, report hash) is written to the ERP service record together with the report PDF | [estimated]; ERP write path → OQ-12 |
| 8 | R-2 / cNF-2: checking time | coverage faked | Timer is stored per report (open → approval) as the KPI; target ≤ 5 min at P80 | [estimated] |
| 9 | OQ-7 product part: device and access | brief open question | Company Android phones under MDM, Chrome; SSO = the company's Microsoft Entra ID; no app store | [estimated] — IT lead to confirm |
| 10 | OQ-9 block or warn | brief open question | Sponsor tends to block (GR-3); decision deferred to concept | [estimated] |
| 11 | Sending to the customer | frame scope, cannot row | E-mail with the PDF on QM-F-12 to the customer address from the ERP; sender service@ address | [evidenced] |
| 12 | Misheard values (riskiest assumption, untouched by the maquette) | brief riskiest assumption | Technician must be able to see the full transcript next to the draft; the parallel test with 20 real voice notes will show more | [estimated] |
| 13 | Checklist batch: cross-cutting standards | checklist | GDPR yes; ISO 9001 yes (OQ-1); ISO 27001 — company not certified, IT baseline applies; CE, PCI, HIPAA no; accessibility — no legal obligation (internal, private company) | [evidenced] |
| 14 | Checklist batch: web + mobile | checklist | Internet-reachable → OWASP ASVS L1; TLS; German UI only; offline + secure local storage; no store (web app) | [estimated] |
| 15 | Checklist batch: backend, data/ML, technology | checklist | Upload idempotent; job queue for STT/LLM; health endpoints; backups of pending drafts; IT house rules for CI, review, secrets apply; no own model training | [estimated] |
| 16 | Data retention of transcripts and drafts after sending | GDPR row | "Don't know — QM and the DPO have to say how long we keep the transcript." | [unknown] → OQ-11 |
| 17 | ERP write interface for report + log | R-19, d-16 | "Don't know if the existing interface can write a document to the service record — only reading is confirmed." | [unknown] → OQ-12 |
| 18 | I7 Operation (prefilled, read back) | V6 note | see § Operation | — |
| 19 | I8 Running cost (prefilled, read back) | V6 note | see § Running cost | — |
| 20 | I9 Load and growth (prefilled, read back) | frame Users and scale | see § Load and growth; provider rate limits: "don't know" → OQ-10 | [unknown] → OQ-10 |

### Field-standard concerns

| Concern | Standard / check | Applies | Candidate requirement or deferral rationale |
| --- | --- | --- | --- |
| Personal data (names, addresses in voice notes) | GDPR: lawful basis, minimisation, deletion, DSAR | yes | d-11, d-12, d-13, d-18 (data-flow map, deletion test, retention OQ-11) |
| Information security | ISO/IEC 27001 | deferred | company not certified; IT security baseline applies via d-19 |
| Quality-management obligation | ISO 9001 documentation | yes | d-23 (report is a controlled QM record); acceptance of AI drafts = OQ-1 |
| CE / PCI-DSS / HIPAA | — | no | not a sold product; no card or US health data |
| Accessibility | EN 301 549 / WCAG 2.2 AA | deferred | no legal obligation (internal tool, private company); basic legibility on phone in d-22 |
| Security audit, internet-reachable | OWASP ASVS L1, TLS, CSP/HSTS, input validation, CSRF/XSS | yes | d-19 |
| AuthN/AuthZ, sessions, secrets | SSO, session handling | yes | d-14 (R-16) |
| Usability, responsive, i18n | UX review on phone | yes | d-22 (German only, phone-first) |
| Deployment, rollback | runbook | yes | d-25 |
| Observability | logs, metrics, alerting | yes | d-20 |
| Mobile offline, secure local storage | airplane-mode test, storage audit | yes | d-8 (offline queue), d-12 (on-device deletion after upload) |
| Store guidelines | — | no | web app, no store |
| Battery / network footprint | profiler | deferred | recordings 1–4 min; revisit if technicians complain |
| API versioning / schema | OpenAPI | yes | d-21 (internal API between phone and backend) |
| Idempotency, timeouts, retries | fault-injection | yes | d-8 (upload idempotent), d-21 |
| Health / readiness, backup & restore | probe, restore drill | yes | d-25 |
| ML model in product: model card, bias | evaluation report | deferred | third-party model, no own training; quality measured via flag rate in d-20 |
| Data pipeline: schema validation | pipeline tests | yes | d-1 (draft schema validated before display) |
| Technology rows (CI, review, linter, SBOM, secrets, semver) | house rules | yes | d-26 (IT house rules) |
| Operating cost (all rows) | cost model, caps, token budget, observability | yes | d-15, d-27 |
| Capacity & scaling | load model, queue, retries, rate limits, retention | yes | d-21, d-28; scale-out rows deferred (single instance; figures below) |

### Operation

| Role | Name | Time share | Evidence |
| --- | --- | --- | --- |
| content / data upkeep | QM lead's office (fault code list, QM-F-12) — K. Brandt | ~2 % (two updates a year + changes) | [estimated] — prefill gap: the brief's V6 note names no content role (the register's V5 note does: "QM maintains template and fault codes") |
| technology / hosting | IT department (ERP integration, SSO, hosting) — from the register: "IT operates the ERP integration and SSO"; the provider runs STT / LLM in the EU under contract | IT: ~10 % of one administrator (M. Weber) | from the register: …; time share [estimated] |
| user support | Service team lead — S. Koch, first line; IT second line | ~5 % | [estimated] |

### Running cost

| Question | Answer | Evidence |
| --- | --- | --- |
| Cost drivers that grow with use (AI calls, documents, compute, metered services) | from the register: "running cost per report far below value per report, capped by R-17" — grows with use: STT per audio minute (~2.5 min per report) and one LLM call per draft (plus one per redraft) | prefill [estimated]; figures from sponsor [estimated] |
| Who pays — price per user, fixed budget, nobody yet | fixed budget of the service department | [evidenced] |
| Heaviest realistic user — what, how much | technician with 8 visits a day in the heating season, 4-min voice notes | [estimated] |
| Monthly ceiling | 150 EUR per month for STT + LLM; alert at 80 % (R-17) | [estimated] |

### Load and growth

| Quantity | At launch | In 1 year | In 3 years | Evidence |
| --- | --- | --- | --- | --- |
| Users (named / active per day; internal or external) | from the register: 18 technicians (+ 4 dispatch, feature DSB); ~16 active per day | 20 technicians | ~25 technicians | prefill [estimated]; growth [estimated] |
| Concurrent users at peak | ~10 (evening 17–19 h) | ~12 | ~15 | [estimated] |
| Peak pattern and factor (time of day, month end, campaign) | from the register: heating season Oct–Feb; factor ~1.5 on reports per day; daily peak after the last visit | same | same | prefill [estimated] |
| Data volume (records, documents, GB) | ~40 reports/day, peak 60 → ~10,000 per year; audio transient; transcript + draft ~10 KB, PDF ~200 KB | ~11,000 per year | ~14,000 per year | [estimated] |
| External limits (rate limits or quotas of services we call) | provider limits unknown; ERP interface limits unknown | — | — | [unknown] → OQ-10 |

### New candidate requirements

| Draft ID | Requirement | Type | Origin | Evidence |
| --- | --- | --- | --- | --- |
| d-1 | From an uploaded voice note, transcribe (STT) and draft a service report with findings, parts used, measurements and next maintenance date; the draft is validated against a schema before display; ready within 10 min of upload | F | R-5 · brief cF-1 · grilling #1 | [estimated] |
| d-2 | The draft and the sent PDF use template QM-F-12 (header + four sections); every fault code is from the QM fault code list, an unknown code is flagged | F | R-12 · brief cF-1 · GR-5 · grilling #2 | [evidenced] |
| d-3 | Every measurement and part shows the transcript passage it came from; a value whose number+unit (or part number) does not occur literally in the passage is flagged "not in voice note" | F | R-7 · brief cF-2 · GR-3 · grilling #3 | [estimated] |
| d-4 | "Approve and send" stays blocked while any value (measurement or part) is flagged and uncorrected (block vs. warn: OQ-9) | F | R-M2 · R-7 · brief cF-8 · grilling #4 | [evidenced] |
| d-5 | The technician corrects a flagged measurement or part inline; the correction is marked "corrected by technician" | U | R-2 · brief cU-1 · grilling #4 | [evidenced] |
| d-6 | A report leaves only through "Approve and send" by the signed-in technician of the visit; no automatic sending | F | R-4 · brief cU-2 · GR-4 | [evidenced] |
| d-7 | Every sent report gets an audit log entry (technician, timestamp, version, report hash) stored with the ERP service record | F | R-19 · brief cF-7 · GR-10 · grilling #7 | [estimated] |
| d-8 | A voice note recorded without connection is kept on the phone, shown "recorded offline — will upload", uploaded idempotently when the connection returns, and deleted on the phone only after the server confirms receipt | F | R-3 · brief cF-4, cF-9 · GR-6 · grilling #5 | [estimated] |
| d-9 | "My visits today" lists the technician's visits with the report status of each | U | R-M1 · brief cF-3 | [evidenced] |
| d-10 | Checking time (open → approval) is recorded per report; target ≤ 5 min at P80 | NF | R-2 · brief cNF-2 · grilling #8 | [estimated] |
| d-11 | Voice notes and transcripts are processed only by EU-hosted services under a DPA | NF | R-14 · R-18 · GR-1 | [evidenced] rule, contract OQ-4 |
| d-12 | Audio is deleted at the provider and on our server when the transcript exists, and on the phone after upload confirmation; no recording older than its transcription job | NF | R-15 · R-18 · brief cF-5 · grilling #6 | [evidenced] |
| d-13 | Customer data is kept only in the ERP; the product holds customer data only for open drafts and deletes it after transfer (retention OQ-11) | NF | R-18 · grilling #16 | [evidenced] / OQ-11 |
| d-14 | Access only via SSO with the company's Entra ID accounts | NF | R-16 · GR-9 · grilling #9 | [estimated] |
| d-15 | Monthly AI cost cap (150 EUR) with an alert at 80 %; provider-side hard cap plus in-product budget guard; on exhaustion drafting pauses, recording and upload keep working | NF | R-17 · GR-7 · Running cost | [estimated] |
| d-16 | On approval the report is sent as PDF by e-mail to the customer address from the ERP and transferred with its log entry to the ERP service record | F | frame scope · cannot row · grilling #11 | [evidenced] e-mail / [unknown] ERP write (OQ-12) |
| d-17 | Visit time, upload time, draft-ready time, approval time and send time are recorded per report (KPIs: time per report, visit-to-sent; input for DSB) | NF | R-1 · R-9 (DSB) · V1 | [estimated] |
| d-18 | GDPR: data-flow map, deletion test, DSAR answerable from ERP | NF | checklist cross-cutting | [evidenced] |
| d-19 | OWASP ASVS L1, TLS everywhere, CSP/HSTS, input validation | NF | checklist web | [estimated] |
| d-20 | Observability: structured logs, alert on failed uploads, failed drafts and flag rate per week | NF | checklist web / data-ML | [estimated] |
| d-21 | Drafting runs as a job with status, timeout, retry with backoff and a cap; defined behaviour when the provider limit is hit | NF | checklist backend / capacity | [estimated] |
| d-22 | German UI, phone-first (Android Chrome), legible in daylight | U | checklist web | [estimated] |
| d-23 | The sent report is a controlled QM record (template version, report version, approver) | NF | checklist ISO 9001 · R-13 side | [estimated] |
| d-24 | No real customer voice notes are processed before OQ-4 is closed; test data are fictitious | NF | GR-2 · brief OQ-4 | [evidenced] |
| d-25 | Deployment runbook with rollback, health endpoint, daily backup of open drafts and log entries | NF | checklist web / backend | [estimated] |
| d-26 | IT house rules: code review, CI with lint/test/security scan, no secrets in repo, SBOM | NF | checklist technology | [estimated] |
| d-27 | Token budget per LLM call (transcript + template only, max output set); cost per report logged | NF | checklist operating cost | [estimated] |
| d-28 | The technician can open the full transcript next to the draft (to catch misheard values) | U | brief riskiest assumption · grilling #12 | [estimated] |

### Still open after grilling

- **OQ-1** — ISO 9001: is an AI-drafted report acceptable, who signs off the procedure change? — QM lead, before go-live with real data
- **OQ-2** — Liability for a disputed AI-drafted report — Head of Service with legal, before go-live
- **OQ-4** — Verify R-18 in the provider contract (EU, DPA, deletion) — IT lead, DPO, before real voice notes
- **OQ-7** — (product part, partly answered) Android under MDM, Chrome, Entra ID — IT lead to confirm, before concept second half
- **OQ-8** — STT copes with dialect and plant-room noise — the parallel test with 20 real voice notes (Head of Service), before consistency review
- **OQ-9** — Block or warn on a flagged value — sponsor, in concept
- **OQ-10** — Rate limits / quotas of the STT/LLM provider and the ERP interface — IT lead, before concept second half
- **OQ-11** — Retention of transcripts, drafts and log entries after sending — QM lead and DPO, before go-live
- **OQ-12** — Can the existing ERP interface write a document and a log entry to the service record (only read access is confirmed)? — IT lead, before concept second half

## Part 3 — Draft for the concept stage

- **Path:** mixed+R — the maquette showed the check-and-approve flow (Path B on brief + vcode/), the brief carries the full register with coverage (Path R: every R-ID traced), everything not shown was grilled (Path A).
- **Ubiquitous language seeds:** service report, voice note, transcript (new), draft, approval, parts used, measurement (new), QM-F-12, fault code list (new), flagged value, source passage (new), log entry (brief: log line), voice note deleted, offline queue (new), visit (new), budget guard (new); "waiting > 24 h" goes to DSB.
- **Out of scope (for now):** dispatch list (feature DSB, R-9); parts reconciliation with ERP stock booking (v1.1, R-10); photos (R-6); customer portal (R-11); analytics across reports (register out of scope EXF-C018); planning, ordering, invoicing, commissioning reports (frame).
- **Integration points known:** EU STT/LLM provider (out: audio, transcript; in: transcript, draft) · ERP (in: visits, customer address, fault codes?; out: PDF + log entry — OQ-12) · Entra ID SSO · company mail server (out: e-mail to customer) · MDM-managed Android phones.
- **Riskiest assumption after intake:** technicians spot an invented value (now mechanically flagged) and a *misheard* value (not flagged — mitigated only by the transcript view d-28) within 5 minutes; plus OQ-8 (STT quality). Test with 20 real voice notes runs in parallel.
- **Recommended first vertical slice:** one uploaded (fictitious) voice note → transcript → draft on QM-F-12 with source passages and flags → correct → approve → PDF e-mail to a test inbox with log entry, signed in via SSO.
- **Sponsor note (priorities):** R-M1 and R-M2 carry the maquette's proposed priority SHOULD; the sponsor sees R-M2 as part of the R-7 MUST in practice. Not changed here — for the PM gate.

## Notes (human)

<!-- Off limits for all skills. Copied verbatim on every rewrite. -->
