# workflow-data Migration Candidates

Tracking list of `workflow-data` records and their migration status into `clarvia-graph`.
Generated from inventory of `workflow-data` as of 2026-06-03.

---

## Legend

| Status | Meaning |
|--------|---------|
| ✅ Migrated | Record exists in clarvia-graph |
| 🔄 Partial | Partially covered by existing graph records |
| 🎯 Candidate | High-quality migration candidate, not yet migrated |
| ⚠️ Low priority | Exists in workflow-data but needs further verification before migration |

---

## Sources (`data/sources/lu/`)

| File | ID | Status | clarvia-graph target | Notes |
|------|----|--------|---------------------|-------|
| `guichet-death-life-event.yaml` | `source:lu:guichet:death-life-event` | ✅ Migrated | `source.guichet_lu.bereavement` | URL verified, title updated in PR #39 |
| `guichet-death-certificate.yaml` | `source:lu:guichet:death-certificate` | 🎯 Candidate | `source.guichet_lu.death_certificate` | URL verified 2026-05-27. Needs source + assertion batch + consequence chain |
| `cnap-survivor-pension.yaml` | `source:lu:cnap:survivor-pension` | 🔄 Partial | `source.cnap_lu.survivor_pension` | Survivor pension consequence already exists via guichet source. Needs standalone CNAP source record + CNAP-specific assertions |
| `cns-death-notification.yaml` | `source:lu:cns:death-notification` | 🎯 Candidate | `source.cns_lu.death_notification` | CNS funeral allowance. No equivalent in graph yet. Needs source + assertions |

---

## Institutions (`data/institutions/lu/`)

| File | ID | Status | clarvia-graph target | Notes |
|------|----|--------|---------------------|-------|
| `bureau-etat-civil.yaml` | `institution:lu:bureau-etat-civil` | ✅ Migrated | `authority.lu.bureau_etat_civil` | In `graph/authorities/lu/bureau_etat_civil.yml` |
| `guichet.yaml` | `institution:lu:guichet` | ✅ Migrated | Referenced as publisher on source records | Guichet is source publisher, not a standalone authority in the graph model |

---

## Document Requirements (`data/document_requirements/lu/`)

| File | ID | Status | clarvia-graph target | Notes |
|------|----|--------|---------------------|-------|
| `death-certificate.yaml` | `document:lu:death-certificate` | 🎯 Candidate | `evidence_type.lu.death_certificate` | Accepted forms (certified_copy, copy) and issuing authority verified in PR #34 and #38. Maps to evidence_type schema |

---

## Deadlines (`data/deadlines/lu/`)

| File | ID | Status | clarvia-graph target | Notes |
|------|----|--------|---------------------|-------|
| `death-declaration-deadline.yaml` | `deadline:lu:death-declaration` | ✅ Migrated | `deadline.lu.bereavement.death_registration.declaration_deadline` | 24-hour deadline verified from Guichet in PR #35. Referenced in `file_death_declaration` task template |

---

## Conditions (`data/conditions/common/`)

| File | ID | Status | clarvia-graph target | Notes |
|------|----|--------|---------------------|-------|
| `death-in-jurisdiction.yaml` | `condition:death-in-jurisdiction` | ✅ Migrated | `condition.lu.bereavement.death_registration.death_occurred_in_lu` | JsonLogic expression in graph |
| `deceased-is-foreign-national.yaml` | `condition:deceased-is-foreign-national` | 🎯 Candidate | `condition.xborder.bereavement.succession.deceased_is_foreign_national` | Cross-border condition. No equivalent yet |
| `deceased-is-frontalier.yaml` | `condition:deceased-is-frontalier` | 🔄 Partial | `condition.xborder.bereavement.survivor_pension.cross_border_pension_applies` | Partially covered. Frontalier-specific condition not yet explicit |
| `survivor-resides-abroad.yaml` | `condition:survivor-resides-abroad` | 🎯 Candidate | `condition.xborder.bereavement.survivor_pension.survivor_resides_abroad` | No equivalent yet |
| `assets-in-multiple-jurisdictions.yaml` | `condition:assets-in-multiple-jurisdictions` | 🎯 Candidate | `condition.xborder.bereavement.succession.assets_in_multiple_jurisdictions` | No equivalent yet |
| `repatriation-of-remains.yaml` | `condition:repatriation-of-remains` | 🎯 Candidate | `condition.xborder.bereavement.death_registration.repatriation_requested` | No equivalent yet |

---

## Scenarios (`data/scenarios/`)

| File | ID | Status | clarvia-graph target | Notes |
|------|----|--------|---------------------|-------|
| `luxembourg-core.yaml` | `scenario:luxembourg-core` | ✅ Migrated | Covered by LU consequence chain | Core LU death declaration + survivor pension chain live in graph |
| `corridor-lu-fr.yaml` | `scenario:corridor-lu-fr` | 🎯 Candidate | `scenario_test.xborder.bereavement.lu_fr_corridor` | France side completely missing from graph (#13) |
| `corridor-lu-de.yaml` | `scenario:corridor-lu-de` | 🔄 Partial | DE authorities (DRV, Standesamt) in graph. DE consequences missing | |
| `corridor-lu-be.yaml` | `scenario:corridor-lu-be` | ⚠️ Low priority | No BE records in graph yet | |
| `corridor-lu-pt.yaml` | `scenario:corridor-lu-pt` | ⚠️ Low priority | No PT records in graph yet. Sources marked discovered in workflow-data | |

---

## Migration Priority Queue

Based on source quality and graph completeness, recommended migration order:

### Priority 1 — High confidence, source-backed, self-contained
1. `guichet-death-certificate.yaml` → new source + assertion batch + `consequence.lu.bereavement.death_registration.obtain_death_certificate` + task template
2. `death-certificate.yaml` (document requirement) → `evidence_type.lu.death_certificate`
3. `cns-death-notification.yaml` → new source + assertion batch (CNS funeral allowance)

### Priority 2 — Cross-border conditions (foundation for corridors)
4. `deceased-is-foreign-national.yaml` → new cross-border condition
5. `survivor-resides-abroad.yaml` → new cross-border condition
6. `assets-in-multiple-jurisdictions.yaml` → new cross-border condition
7. `repatriation-of-remains.yaml` → new cross-border condition

### Priority 3 — Corridor scenarios (depends on Priority 2)
8. `corridor-lu-fr.yaml` → FR authorities + consequences + task templates (Issue #13)
9. `corridor-lu-de.yaml` → DE consequences + task templates (DE authorities already in graph)

### Priority 4 — Deferred pending further source verification
10. `cnap-survivor-pension.yaml` → standalone CNAP source record (partially covered)
11. `corridor-lu-be.yaml` — pending BE source verification
12. `corridor-lu-pt.yaml` — pending PT source verification (sources marked discovered)

---

## Notes

- All migrated records reference `snapshot.guichet_lu.bereavement.2026_06_03` as provenance
- Cross-border conditions (Priority 2) should use `jurisdiction: EU` or `jurisdiction: xborder` per existing graph conventions
- `cnap-survivor-pension.yaml` and `cns-death-notification.yaml` both have `verification_status: discovered` in workflow-data — assertions from these sources should carry `confidence: low` until further verified
- Sources from `data/sources/eu/` (Brussels IV, EC 883/2004) are not listed here — they are EU-level sources that feed the cross-border chain, tracked separately

