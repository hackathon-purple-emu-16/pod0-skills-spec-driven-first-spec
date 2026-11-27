## ADDED Requirements

### Requirement: Exact normalized matching
The system MUST return as candidates only source records whose record number and service date both equal the normalized submitted values.

Source system is provenance, not an additional matching field. Records from different sources remain separate candidates even when their matching fields are identical; no unique identifier is assumed or introduced.

#### Scenario: Case and surrounding whitespace are normalized
- **GIVEN** synthetic request NWT-REQ-20261124-0007 submits record number ` r-100245 ` and service date `2026-10-03`, and a complete lookup finds only the claims record with number `R-100245` and that date
- **WHEN** authorized intake staff request identification
- **THEN** the result contains exactly one candidate from the claims system

### Requirement: Record number normalization
The system MUST normalize submitted and source record numbers by removing surrounding ASCII whitespace and converting ASCII letters to uppercase, while preserving internal whitespace, punctuation, and leading zeros.

Empty normalized record numbers are rejected with a validation error before lookup.

#### Scenario: Leading zeros are not discarded
- **GIVEN** a synthetic variation of request NWT-REQ-20261124-0007 submits record number `R-0100245` and service date `2026-10-03`, and the only available source record has number `R-100245` and that date
- **WHEN** authorized intake staff request identification
- **THEN** the complete lookup returns zero candidates rather than treating the record numbers as equivalent

### Requirement: Service date validation
The system MUST reject invalid service dates with a validation error before lookup.

Submitted and source service dates must be valid calendar dates in exactly `YYYY-MM-DD` format, with no surrounding whitespace; no locale inference or time-zone conversion is performed.

#### Scenario: Ambiguous date format is rejected
- **GIVEN** synthetic request NWT-REQ-20261124-0010 submits record number ` r-100245 ` and service date `03/10/2026`
- **WHEN** authorized intake staff request identification
- **THEN** no lookup is performed, no record is selected, and staff see a validation error requiring a valid `YYYY-MM-DD` service date

### Requirement: Complete lookup before selection
The system MUST keep record selection disabled unless both the claims and legacy billing lookups complete successfully and return exactly one candidate in total.

#### Scenario: One candidate permits explicit selection
- **GIVEN** synthetic request NWT-REQ-20261124-0007 submits `R-100245` and `2026-10-03`, and the complete lookup returns only its claims-system candidate
- **WHEN** authorized intake staff request identification
- **THEN** staff can explicitly select that candidate, but the system does not select it automatically

#### Scenario: Incomplete lookup cannot appear unique
- **GIVEN** synthetic request NWT-REQ-20261124-0008 submits `R-100311` and `2026-10-07`, the claims lookup returns one candidate, and the legacy billing lookup fails
- **WHEN** authorized intake staff request identification
- **THEN** staff see an incomplete-lookup error and record selection remains disabled

### Requirement: Unresolved result handling
The system MUST show a distinct unresolved status and a referral action to the designated Operations Intake resolver when a complete lookup returns zero or multiple candidates.

The referral carries the synthetic request reference and unresolved status, does not select a record, and does not grant additional access. Resolver investigation is outside this selection flow; any corrected input must undergo a new complete lookup before selection.

#### Scenario: No match requires referral
- **GIVEN** synthetic request NWT-REQ-20261124-0009 submits `R-109999` and `2026-10-12`, and a complete lookup returns no candidates
- **WHEN** authorized intake staff request identification
- **THEN** staff see a no-match status, record selection is disabled, and a referral action is available

#### Scenario: Cross-source ambiguity requires referral
- **GIVEN** synthetic request NWT-REQ-20261124-0008 submits `R-100311` and `2026-10-07`, and a complete lookup returns one claims record and one legacy billing record with those values
- **WHEN** authorized intake staff request identification
- **THEN** staff see an ambiguous-match status with two candidates, record selection is disabled, and a referral action is available without either source being preferred

### Requirement: Minimal intake disclosure
The system MUST restrict identification-result details shown to intake staff to the result status, candidate count, candidate source-system names, and, for a single candidate only, its matching record number and service date.

These permitted details exclude person information and all non-matching record attributes. Validation and lookup errors must not expose source-record payloads.

#### Scenario: Ambiguity does not expose additional record details
- **GIVEN** synthetic request NWT-REQ-20261124-0008 has two candidates from the claims and legacy billing systems, including non-matching attributes in their source records
- **WHEN** authorized intake staff view the identification result
- **THEN** they see the ambiguous-match status, count of two, and both source-system names, but no person information or non-matching record attributes

### Requirement: Authorized intake access
The system MUST deny identification and selection actions to callers without the authorized intake role, without returning candidate details.

#### Scenario: Unauthorized caller receives no candidates
- **GIVEN** a caller without the authorized intake role requests identification for synthetic request NWT-REQ-20261124-0007
- **WHEN** the caller submits the identification request
- **THEN** access is denied and no candidate details or selection action are returned
