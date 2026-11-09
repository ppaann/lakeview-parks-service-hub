# Solution export: what's inside and what isn't

> Independent, self-directed portfolio demo for a fictional city. Not affiliated with any real city or government agency.

**File:** `solution/LakeviewResidentServiceHub_1_0_0_1.zip`, **unmanaged**, version **1.0.0.1**, publisher *Lakeview Parks Demo* (prefix `lkv`), exported Nov 9, 2026.
**Verified by inspecting the zip** (solution.xml and customizations.xml), not just by the export message.

## Included (11 root components)

| Component | Details |
|---|---|
| Tables (3) | **Parks Asset** (`lkv_parksasset`, with alternate key `lkv_assetidkey` on Asset ID); **Case** and **Contact** segmented (only my columns and form changes) |
| Case columns | `lkv_category` (choice), `lkv_parksasset` (lookup), `lkv_paymentnotes` (secured text) |
| Business rule | *Require asset for Asset Maintenance* (Entity scope) |
| Model-driven app + site map | *Lakeview Parks Assets* |
| Security roles (2) | Lakeview Front Desk, Lakeview Supervisor |
| Column security profile (1) | Lakeview Supervisors (Birthday, Payment Notes) |
| Routing ruleset | *Lakeview Case Routing* with 6 rule items (conditions on `lkv_category`) |
| Unified-routing intake configuration | Decision ruleset and contracts for Case (intake rule mapped to the basic ruleset) |
| SLA | *Lakeview Response SLA* with its TEST item |

**DEF-03 clean-up verified:** the strings "Broken" and "Swing Set" don't appear anywhere in the export.
**Public-repo check:** no environment URL, tenant domain or user email in the export.

## Not included (recreate manually after import)

| Item | Why | How to recreate |
|---|---|---|
| **Queues** (Facility Rentals, Registration & Payments, Supervisor Review(Public), Parks Maintenance, General Intake) | Queues are data records; Learn: routing rulesets are solution-aware but their queue mappings aren't. The rule items' conditions are exported; their **Route to** queues aren't. | Create the 5 basic Public queues, then edit each rule item's Route to queue and activate the ruleset. |
| **SLA KPI** *First Response By – Case* | Not listed as a component or dependency of the SLA | Create the KPI (Case, First Response By KPI, Applicable From = Created On), activate, and relink the SLA item if needed. |
| **Unified routing setting, intake rule activation** | Environment settings | Confirm unified routing state; check the Case intake rule maps to the ruleset. |
| **Users, licenses, role assignments, column-profile membership** | Environment data | Assign roles and add the supervisor user to the profile. |
| **Parks Assets, cases, synthetic test data** | Data | Import `data/parks-assets.csv`; create test cases per `tests/manual/test-cases.md`. |

## Prerequisites for import

The target environment needs **Dynamics 365 Customer Service** installed: the solution depends on Microsoft managed solutions including *Service*, *Customer Service Hub* and *Enhanced Case Experience* (shown by the export wizard).
