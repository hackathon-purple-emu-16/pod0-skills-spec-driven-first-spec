# Readiness record

> Copy this file into your change folder as `readiness.md`, next to `proposal.md`. Synthetic data only.
> Write `unconfirmed` where evidence is missing: an AI draft is not confirmation. If you simulate a
> confirmation for this exercise, label it `simulated`.

## Capability confirmation

Which capabilities does your spec assume exist (a field, an index, a lookup, an API, a queue)? Who can confirm
each one, and how?

| Capability we assume exists | Who can confirm it (role) | Evidence or status |
| --- | --- | --- |
| Both sources expose record number and service date with understood semantics | Claims-system and legacy-billing data owners (candidate confirming roles) | unconfirmed; request field definitions and a synthetic demonstration of date meaning, formatting, and record-number semantics |
| Lookup can find all candidates in both sources using the proposed normalization | Records Platform engineering lead and source-system owners (candidate confirming roles) | unconfirmed; demonstrate complete lookup against synthetic zero-, one-, and two-candidate fixtures; no index or unique key is assumed |
| Lookup distinguishes successful empty results from failures, timeouts, or partial results | Records Platform engineering lead (candidate confirming role) | unconfirmed; provide a lookup contract and synthetic failure demonstration, including pagination or result limits if applicable |
| Authorization distinguishes permitted intake callers from other callers | Identity/access owner and Operations Intake product owner (candidate confirming roles) | unconfirmed; confirm role assignments and demonstrate denied lookup and selection without candidate disclosure |
| Explicit selection can associate the intended candidate without assuming the matching fields are unique | Records Platform engineering lead (candidate confirming role) | unconfirmed; explain existing candidate addressing and demonstrate explicit selection; do not invent a unique identifier |
| A referral action reaches a designated resolver without granting extra access | Operations Intake product owner and workflow owner (candidate confirming roles) | unconfirmed; identify the resolver role and available referral mechanism; no queue or API is assumed |
| Result and error presentation can suppress non-permitted source attributes | Records Platform engineering lead and data-protection reviewer (candidate confirming roles) | unconfirmed; inspect synthetic result and error outputs against the proposed disclosure limits |

Prompt for Copilot: "From this draft, list capability assumptions, questions for their owners, and candidate synthetic evidence that could confirm or disprove them. Do not assert that a capability exists, invent an identifier, or make approval decisions."

## Dependencies

Which upstream and downstream systems or teams does this change depend on? What happens if one is not ready?

| System or team | Owner (role) | Behavior if it is not ready |
| --- | --- | --- |
| Claims system | Claims-system owner; assignment unconfirmed | Draft requires selection to remain disabled on incomplete lookup; timeout and retry handling need owner agreement |
| Legacy billing system | Legacy-billing owner; assignment unconfirmed | Draft requires selection to remain disabled even if claims returns one candidate; source coverage and availability need confirmation |
| Records Platform lookup and selection integration | Records Platform engineering lead; responsibility unconfirmed | Implementation remains blocked until complete candidate retrieval and explicit candidate selection are demonstrated |
| Identity/access integration | Identity/access owner; assignment unconfirmed | Candidate fallback: deny lookup and selection if authorization cannot be established; outage behavior needs human agreement |
| Operations Intake referral workflow | Operations Intake product owner and workflow owner; assignments unconfirmed | Selection stays disabled for unresolved results under the draft; referral-failure notification and recovery remain unanswered, not a silent successful referral |
| Synthetic fixture preparation and acceptance review | Candidate owners: engineering lead for fixtures, product owner for expected behavior | Do not claim scenarios verified or readiness confirmed until fixtures, expected results, and responsible roles are agreed |

Prompt for Copilot: "Suggest dependency-owner questions and candidate not-ready behaviors for each integration. Identify unresolved failure paths and evidence needed. Do not assign owners, invent integrations, or decide readiness."

## Edge cases

List at least three non-happy-path inputs, one per bullet, using fictional examples only.

- NWT-REQ-20261124-0008, `R-100311`, `2026-10-07`: two candidates in different sources. Draft expects ambiguity, disabled selection, and referral without preferring a source.
- NWT-REQ-20261124-0009, `R-109999`, `2026-10-12`: both lookups succeed with zero candidates. Draft expects no-match status, disabled selection, and referral.
- NWT-REQ-20261124-0010, ` r-100245 `, `03/10/2026`: ambiguous date format. Draft expects validation failure before lookup, not an inferred locale.
- Isolated variation of NWT-REQ-20261124-0007, `R-0100245`, `2026-10-03`: only `R-100245` exists. Draft preserves leading zeros and expects zero candidates.
- NWT-REQ-20261124-0008: claims returns one candidate while legacy billing fails. Draft expects an incomplete-lookup error, not a unique match.
- Isolated variation of NWT-REQ-20261124-0007, record number containing only ASCII spaces: draft expects a validation error before lookup. A dedicated acceptance scenario is still missing.
- Isolated variation of NWT-REQ-20261124-0007, date `2026-02-30`: draft expects a calendar-date validation error before lookup. A dedicated acceptance scenario is still missing.
- NWT-REQ-20261124-0007: a source record has an invalid service date. The draft requires valid source dates but does not specify how malformed source data affects completeness or error reporting; human clarification is needed.
- NWT-REQ-20261124-0007: candidates change between lookup and explicit selection. Revalidation or stale-result handling is unresolved; ask the product owner and engineering lead for the intended behavior.

Prompt for Copilot: "Suggest additional synthetic edge-case inputs and questions where the draft lacks observable outcomes. Separate existing scenario expectations from candidate additions; do not choose missing behavior or weaken scenarios."

## Synthetic data per scenario

| Scenario | Synthetic input and expected result | Data source, owner and availability |
| --- | --- | --- |
| Case and surrounding whitespace are normalized | NWT-REQ-20261124-0007 submits ` r-100245 ` and `2026-10-03`; claims contains `R-100245` on that date, legacy billing is empty, both complete. Draft expects exactly one claims candidate. | Intent brief plus draft scenario variation; candidate fixture owner: Records Platform engineering lead, unconfirmed; values documented, executable fixture unavailable/unconfirmed |
| Leading zeros are not discarded | Isolated NWT-REQ-20261124-0007 variation submits `R-0100245` and `2026-10-03`; claims contains only `R-100245` on that date, legacy billing is empty, both complete. Draft expects zero candidates. | Draft scenario using intent-brief values; candidate fixture owner: Records Platform engineering lead, unconfirmed; executable fixture unavailable/unconfirmed |
| Ambiguous date format is rejected | NWT-REQ-20261124-0010 submits ` r-100245 ` and `03/10/2026`. Draft expects a valid-ISO-date error, no lookup, and no selection. | Intent brief marks this input not tested; candidate fixture owner: Records Platform engineering lead, unconfirmed; input documented, execution and lookup-call assertion unconfirmed |
| One candidate permits explicit selection | NWT-REQ-20261124-0007 submits `R-100245` and `2026-10-03`; authorized synthetic caller, one claims candidate, legacy billing empty, both complete. Draft expects selection enabled but no automatic association. | Intent brief and draft scenario; candidate fixture owner: Records Platform engineering lead, unconfirmed; role fixture and selection test availability unconfirmed |
| Incomplete lookup cannot appear unique | NWT-REQ-20261124-0008 submits `R-100311` and `2026-10-07`; claims returns its candidate, legacy billing returns a synthetic lookup failure. Draft expects incomplete-lookup error and disabled selection. | Draft failure variation of intent example; candidate fixture owner: Records Platform engineering lead, unconfirmed; failure-injection fixture unavailable/unconfirmed |
| No match requires referral | NWT-REQ-20261124-0009 submits `R-109999` and `2026-10-12`; both sources complete with no candidates. Draft expects no-match status, disabled selection, and referral action. | Intent brief and draft scenario; candidate fixture owners: engineering lead for lookup, Operations Intake workflow owner for referral, both unconfirmed; executable fixtures unavailable/unconfirmed |
| Cross-source ambiguity requires referral | NWT-REQ-20261124-0008 submits `R-100311` and `2026-10-07`; claims and legacy billing each return one record with those values. Draft expects count two, ambiguous status, disabled selection, and referral without source preference. | Intent brief and draft scenario; candidate fixture owners: engineering lead and Operations Intake workflow owner, both unconfirmed; source fixtures and referral test availability unconfirmed |
| Ambiguity does not expose additional record details | Same two candidates for NWT-REQ-20261124-0008; add only a fictional non-matching attribute `test_only_note: SYNTHETIC-NOT-FOR-DISPLAY` to each source payload, with no person data. Draft expects only ambiguity status, count two, and source names in result details; marker absent. | Intent brief plus proposed non-person test marker; candidate fixture owner: Records Platform engineering lead, unconfirmed; executable output-inspection fixture unavailable/unconfirmed |
| Unauthorized caller receives no candidates | NWT-REQ-20261124-0007; synthetic caller lacks the authorized intake role. Draft expects access denied, no candidate details, and no selection action. | Draft scenario with intent-brief request; candidate fixture owners: identity/access owner and engineering lead, both unconfirmed; synthetic role configuration and access tests unavailable/unconfirmed |

Each row is an isolated test case; reused request references are not new identifiers. Expected results describe the unapproved draft, not verified execution. No real requests, records, people, credentials, or customer data are required.

Prompt for Copilot: "Suggest minimal synthetic fixtures for every existing scenario, including source completion and caller-role conditions. Flag missing data, owner confirmation, and test observations. Do not fabricate availability, report tests as run, or approve expected behavior."

## Decision

> **Ready to build** means: the intent is clear and the scope is bounded; material questions are answered by
> named people; assumed capabilities are confirmed with evidence; dependency owners and not-ready behavior are
> agreed; edge cases are covered; and every scenario has synthetic data with a source and owner.
> Any unconfirmed material assumption blocks the gate. Write `ready` or `not ready` after the Decision label.
> A clear `not ready` with named blockers, owners and next actions is a respectable, useful answer.

**Decision:** not ready

**Reasons:**

- Matching fields, normalization rules, selection permissions, disclosure limits, and resolver ownership remain proposed rather than human-confirmed.
- All seven assumed capabilities are unconfirmed; candidate confirming roles and requested evidence are not confirmations.
- Dependency ownership and not-ready behavior are not agreed, particularly authorization outages and referral failures.
- Malformed source dates and changes between lookup and selection have no agreed observable outcomes; empty record numbers and invalid calendar dates lack dedicated acceptance scenarios.
- Synthetic inputs are documented for all nine scenarios, but fixture ownership, executable data availability, and test results are unconfirmed.

**Decided by (roles):**

- AI assistant: evidence-based readiness assessment only; no human approval or role-played confirmation.
- Operations Intake product owner and Records Platform engineering lead: candidate human decision roles; participation and decision authority unconfirmed.

**Missing confirmation or data, owner and next action:**

- Intent, actors, scope, non-goals, and proposed behavior: candidate owner, Operations Intake product owner. Next action: review the proposal and draft scenarios, answer material questions one at a time, and record explicit human decisions.
- Field semantics, actual search scope, complete candidate retrieval, and candidate selection mechanism: candidate owners, Records Platform engineering lead and claims/legacy-billing data owners. Next action: provide field contracts and synthetic demonstrations, including partial failures and result limits; do not assume an index or introduce a unique identifier.
- Authorization and disclosure capabilities: candidate owners, identity/access owner and data-protection reviewer. Next action: confirm permitted roles and allowed details, then provide synthetic access-denial and output-inspection evidence.
- Resolver assignment, referral mechanism, referral-failure handling, and any resolution-time commitment: candidate owners, Operations Intake product owner and workflow owner. Next action: identify the responsible role, demonstrate the workflow, and agree failure/recovery behavior and any timing expectations.
- Dependency responsibilities and outage behavior: candidate owners, Records Platform engineering lead and each integration owner listed above. Next action: confirm assignments and document agreed behavior when each dependency is unavailable.
- Malformed source dates and stale candidates: candidate owners, Operations Intake product owner and Records Platform engineering lead. Next action: agree observable outcomes before authorizing corresponding requirement and scenario changes.
- Empty-number and invalid-calendar-date acceptance coverage: candidate owner, Operations Intake product owner. Next action: review candidate edge-case scenarios and explicitly authorize any specification additions.
- Executable synthetic fixtures and scenario verification: candidate owner, Records Platform engineering lead, with expected behavior reviewed by the product owner. Next action: assign a fixture owner, make isolated synthetic fixtures available for every scenario, and record actual validation evidence.

All owner assignments above are unconfirmed candidates. No confirmation is simulated in this record; any future role-played confirmation must be labeled `simulated`.

## Measures

> Choose one or two delivery measures, such as clarification round-trips per work item or days blocked on a
> dependency. Agree what is counted before you measure. Never invent a baseline: if a value is not known yet,
> write `unknown — owner to provide` and treat it as a collection task, not zero. Do not count lines of
> generated code, AI prompts or checked task boxes.

| What is counted | Baseline period | Source | Owner | Available by |
| --- | --- | --- | --- | --- |
| Proposed measure, pending agreement: elapsed calendar days from recording a blocking dependency to recording its evidence-backed resolution, per dependency | unknown — owner to provide | unknown — owner to provide | Candidate: Records Platform engineering lead; assignment unconfirmed | unknown — owner to provide |
