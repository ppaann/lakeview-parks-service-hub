# Test execution report: Phase 1 (Sprint 1)

> Independent, self-directed portfolio demo for a fictional city. Not affiliated with any real city or government agency. All data is synthetic.

**Status:** Draft for Pan's review (Nov 9, 2026). Numbers come from the Azure Test Plans Progress report.
**System under test:** Dynamics 365 Customer Service (Enterprise trial) configuration in a Dataverse trial environment: Lakeview Resident Service Hub solution (custom Parks Asset table, Case customizations, business rule, basic routing ruleset behind a unified-routing intake rule, SLA, security roles and column security).
**Test management:** Azure DevOps Test Plans, plan *Phase 1 – Sprint 1*, six requirement-based suites (one per user story), 29 manual test cases traced to acceptance criteria.
**Test period:** Nov 4–9, 2026 (smoke checks during the build, Nov 4–7; recorded in ADO on Nov 9).

## 1. Summary

| Measure | Result |
|---|---|
| Test points | 29 |
| Run | 29 (100%) |
| **Passed** | **21 (72%)** |
| **Failed** | **2 (7%)** |
| **Blocked** | **6 (21%)** |
| Not run | 0 |

### By user story

| Suite | Test points | Passed | Failed | Blocked |
|---|---|---|---|---|
| US-01 Case category | 5 | 4 | 0 | 1 |
| US-02 Routing | 7 | 5 | 0 | 2 |
| US-03 SLA | 4 | 2 | 0 | 2 |
| US-04 Asset required | 4 | 3 | 0 | 1 |
| US-05 Asset CSV import | 4 | 2 | **2** | 0 |
| US-07 Data protection | 5 | 5 | 0 | 0 |
| **Total** | **29** | **21** | **2** | **6** |

US-06 (requester notification) and US-08 (reporting) are *Next phase* and have no test cases in this plan.

## 2. Failed tests

| Test case | Result | Defect |
|---|---|---|
| TC-05.1 Import rejects rows missing Asset ID or Asset Name | Failed: both rows were imported | DEF-01 / DEF-02 |
| TC-05.3 Import rejects invalid choice values | Failed: rows imported and the values were **added as new choice options** | DEF-03 |

## 3. Blocked tests and why

| Test case | Reason |
|---|---|
| TC-01.3, TC-02.2, TC-02.4, TC-04.4 | Need Web API POST requests; no request tool set up in Phase 1 (planned: browser-console requests or an app registration in Phase 2). |
| TC-03.3 SLA pause | Pause behavior not verified in Phase 1. |
| TC-03.4 Business SLA (business hours, category durations) | Business SLA items and calendar not built (*Next phase*). |

Blocked means *not yet executable*, not *failed*. AC-04.4 (business rule enforced through the Web API) is therefore **unverified**; the form showed a server-style Business Process Error, which suggests server-side enforcement.

## 4. Defects

| ID | ADO bug | Title | Severity | Priority | Triage decision | State (Nov 9) |
|---|---|---|---|---|---|---|
| DEF-01/02 | #105 | CSV import accepts assets with blank Asset ID or Asset Name | 2 – High | 2 | Defer to Phase 2 (server-side validation + clean retest) | Open, tag phase-2 |
| DEF-03 | #106 | CSV import adds invalid values as new choice options and reports Success | 2 – High | 1 | Clean-up done Nov 9 (records deleted, stray options removed); root cause deferred to Phase 2 | Active (Reason: Not fixed), tag phase-2 |
| DEF-04 | #107 | Parks Asset form shows each field in its own "New Section" | 4 – Low | 3 | Fix now | **Closed (Verified)**: fixed, retested, closed |
| DEF-05 | #108 | Case First Response SLA Status stays "In Progress" while the KPI instance changes | 3 – Medium | 3 | Investigate (tag Needs-investigation) | Active |
| DEF-06 | #109 | Case form labels and duplicate field | 4 – Low | 3 | Fix now | **Closed (Verified)**: labels renamed (SLA status, SLA KPI instances), duplicate First Response Sent and Payment Notes removed, retested on CAS-01006 |
| DEF-07 | #110 | Hidden secured date shows an empty box | 4 – Low | 4 | As designed / document in user guide | Resolved |
| — | #104 | "Blank Id" | 3 – Medium | 2 | Duplicate of #105 | Resolved |

**Full lifecycle examples (#107, #109):** New → Active (approved) → Resolved (Fixed) → Closed (Verified). Fix: all asset fields moved into one *Asset Details* section; retested on PA-0012 in the Lakeview Parks Assets app.

## 5. Key verified behaviors

- **Column security holds at the data layer:** in the Front Desk user's own session, a Web API GET returned `birthdate: null`; the Supervisor's session returned the secured Payment Notes value.
- **Routing:** with unified routing enabled, the basic routing ruleset only ran after a Case intake rule was mapped to it; Save & Route then routed by category and moved re-categorized cases out of the old queue.
- **SLA (test item, 5/10 minutes):** In progress → Nearing noncompliance → Noncompliant observed; Succeeded when a first response was recorded in time.
- **Alternate key:** duplicate Asset IDs were rejected on import ("Entity Key Asset ID Key violated").

## 6. Limitations and evidence notes

- **Configuration label:** all runs carry ADO's default configuration "Windows 10". **Actual platform, per Test Runner system info: macOS (arm64), Google Chrome 156.** Real configurations (macOS browsers, iPhone/iPad Power Apps app) are created in Phase 2 before the platform pass.
- **Recorded later:** many results were executed as smoke checks Nov 4–7 and recorded in ADO on Nov 9, with dates and evidence references in the run comments.
- **Evidence gaps:** some passed steps have no captured screenshot in the project record (US-07 views/export, minimal-user access, edit/update steps; TC-01.5 audit history). They're reported as passed by the tester; evidence to be added in Phase 2.
- **Test data:** synthetic only; sample trial records (e.g., contact "Claudia Mazzanti") used as test customers.

## 7. Readiness (Phase 1 demo)

**Conditional go for a demo of the configured service flow** (categories, routing, SLA, asset linking, column security). **No-go for the asset CSV import** until DEF-01 to DEF-03 are fixed and retested. This is a portfolio demo, not a production release.
