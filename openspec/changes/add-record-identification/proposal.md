## Why

Contoso's intake staff currently find records for Northwind Traders requests using a submitted record number and service date. Synthetic examples show that these values can return multiple candidates across source systems or no candidate, undermining the assumption that they identify exactly one record. Staff need clear guidance on when they may select a record, when they must stop, and who resolves uncertainty, without exposing unnecessary details.

This is a draft proposal, not an approved requirement. The business intent, actors, scope, and non-goals still require human confirmation.

The accompanying specification contains proposed decisions made in response to the request to settle the open questions, not approved requirements. The intent brief confirms the constraints against guessing, unnecessary disclosure, real data, and assuming an index or unique key; it does not confirm the proposed matching or permission rules.

## What Changes

Proposed behaviors, pending confirmation:

- Distinguish zero, one, and multiple candidate results without guessing which record belongs to a request.
- Define when intake staff may select a record and when they must stop; a single candidate is not yet an approved selection rule.
- Define how unresolved matches reach the responsible resolver, with ownership and timing still undecided.
- Limit the details shown to staff to those necessary for their permitted decision.
- Specify exact matching fields and normalization rules after human decisions; neither the current field combination nor any normalization is approved by this proposal.

The lookup's actual search scope and how it will be verified remain open. No linked issue, active change, or related Gherkin scenarios were supplied or found; the existing member-eligibility specification addresses a separate capability.

Proposed scope in the draft specification: identification for Northwind Traders requests by authorized Contoso intake staff, searching the claims and legacy billing systems. A designated Operations Intake resolver owns unresolved cases. This change does not grant that role additional data access or permit it to override the matching rule. Actual source coverage, lookup feasibility, and role assignments require verification before approval or implementation. No resolution-time commitment is proposed without operational confirmation.

## Non-goals

Confirmed constraints from the intent brief:

- Do not guess a record association.
- Do not assume a searchable index or unique key exists.
- Do not use real requests, records, or people; examples and validation use synthetic data only.
- Do not disclose more record details than staff need.

Proposed scope exclusions, pending human confirmation:

- Do not introduce a new unique identifier or prescribe an indexing implementation.
- Do not merge, deduplicate, or modify source records.
- Do not change member-eligibility behavior.
- Do not settle matching fields, normalization, actor permissions, or ambiguity-resolution ownership and timing without explicit human decisions.
