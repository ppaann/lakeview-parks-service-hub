# Project record: City of Lakeview Parks & Recreation Resident Service Hub

> Independent, self-directed portfolio demo for a fictional city. Not affiliated with any real city or government agency.

**Last updated:** Tue Nov 3, 2026

## What it is and who it serves

A Dynamics 365 Customer Service configuration for a fictional city's Parks & Recreation department. Residents contact staff about:

- facility rentals
- registration problems
- refunds
- complaints
- asset-maintenance issues

Staff log cases, route them to queues, and meet a response-time SLA. A small asset-maintenance extension adds basic EAM-style asset tracking (not a full EAM or QAlert implementation).

**Audience:** hiring managers for local-government QA / business analyst roles, especially a King County IT QA Engineer role.

## Actual implementation start date

**Mon Nov 2, 2026** (confirmed by Pan).

## Status legend

**Planned** → **Configured/Built** → **Executed** → **Verified (evidence captured)**

## Status

| Item                                                                          | Status                    | Evidence                                                                    |
| ----------------------------------------------------------------------------- | ------------------------- | --------------------------------------------------------------------------- |
| Dynamics 365 Customer Service Enterprise trial active                         | Verified                  | Private notes s0-02 (26 days left on Nov 2)                                 |
| Project environment = CustomerService Trial (Trial, Ready, Dataverse Yes, US) | Verified                  | Private notes s0-04 (25 days left on Nov 2, so expiry about Nov 27)         |
| Copilot Service admin center opens                                            | Planned (check at Day 1)  | n/a                                                                         |
| Copilot Service workspace browser support (Edge/Chrome)                       | Planned (verify on Learn) | n/a                                                                         |
| GitHub repo, README notice, .gitignore, PROGRESS.md                           | Verified                  | Commit 13d378a pushed Nov 2; private notes s1-01; .gitignore rules checked with `git check-ignore` |
| User stories (6–8)                                                            | Drafted, awaiting Pan's review (8 stories, 43 ACs; US-04–08 AI-assisted drafts) | n/a                                                                         |
| Solution and publisher                                                        | Planned (Task 1.3)        | n/a                                                                         |
| Everything else                                                               | Planned                   | n/a                                                                         |

## Decisions

- **D-01:** All project work happens in the **CustomerService Trial** environment.
- **D-02:** Create a new Azure DevOps org with the work account. Start the **Azure Test Plans 30-day trial in Phase 1** (Nov 3–4).
- **D-03:** Top priority is the full QA lifecycle in ADO (requirements → test cases → runs → bugs → triage → fix validation → execution report). Automation is a differentiator.
- **D-04:** Learning notes are Markdown plus an images folder, in `learning-notes/`, with a .zip snapshot for importing into a notes app.
- **D-05:** Saturday Nov 7 isn't available, so verification moves to Friday Nov 6 afternoon.
- **D-06:** CV lists only verified work with real counts. Planned work is labeled "Next phase."
- **D-07:** Test platforms are Mac (Edge, Chrome, Safari), iPhone and iPad (Power Apps mobile app). Windows and Android are documented as a coverage gap.
- **D-08 (Nov 2):** This repo's `PROGRESS.md` is the master project record. The copy outside the repo is only a pointer.
- **D-09 (Nov 2):** Monday's 2 hours were lost to planning, so the plan was rebalanced (below). The cut order was applied early: loader to 50 records, Playwright to 1 smoke test in Phase 1, SQL reconciliation to Phase 2. The ADO QA lifecycle stays protected. Pan can revise.
- **D-10 (Nov 3):** Any user with the Front Desk or Supervisor role can change a case's Category (US-01, AC-01.4). No column-level security on Category; Category changes are audited (AC-01.5).
- **D-11 (Nov 3):** Queue design (US-02): Facility Rental → Facility Rentals; Registration Issue and Refund Request → Registration & Payments; Complaint → Supervisor Review; Asset Maintenance → Parks Maintenance; no rule match → General Intake. Use **basic queues** plus routing rules (pick-based). Unified routing is a Phase 2 stretch goal, after a licensing check. General Intake kept as the safety net for API, email-to-case and unmatched categories (Pan, Nov 3).
- **D-12 (Nov 3):** Queue views list the oldest items first (FIFO, AC-02.6), sorted by the case's **Created On** (Pan, Nov 3), so cases closest to an SLA breach stay visible, even after re-categorization.
- **D-13 (Nov 3):** Business first-response SLA (US-03): all categories except Asset Maintenance, warning at 1 business day and failure at 3 business days; Asset Maintenance, warning at 2 and failure at 6 business hours. Clock starts at Created On, pauses while On Hold, runs only in business hours (default Mon–Fri 8:00–17:00 Pacific unless Pan changes it). A short test SLA is a test-plan item, not a requirement.

## Risks

- **R-01:** The trial environment is deleted after expiry (about Nov 27; extendable once). Mitigation: export the solution and keep everything in GitHub.
- **R-02:** The SQL (TDS) endpoint's documented client is SSMS, which is Windows-only. Try VS Code `mssql`; fall back to Web API aggregates.
- **R-03:** Power BI Desktop is Windows-only. Phase 2 uses the Power BI service, after a license check.
- **R-04:** Friday is overloaded. Cut order: loader to 50 records → Playwright to 2 tests → SQL reconciliation to Phase 2. *(Applied early on Nov 2; see D-09.)*
- **R-05:** The trial expires after 14 days of inactivity. Log in at least every few days.
- **R-06:** Copilot Service workspace browser support isn't verified yet.
- **R-07:** Microsoft Learn documents basic routing rules for cases created from email and social posts. Automatic routing of form- and Web API-created cases is unverified (AC-02.4); check Wed.
- **R-08:** The SLA for Asset Maintenance depends on Category (Applicable When), and Category can change (D-10). Microsoft Learn: changing an Applicable When field after the SLA starts cancels the KPI instance. Test re-categorization on Wed and document the behavior.
- **R-09:** Unverified: whether column security can be enabled on the out-of-box Contact Birthday column (US-07). Fallback: a custom synthetic DOB column. Check on Learn before Thu.
- **R-10:** 43 ACs vs a 20–30 test-case budget. Mitigation: one test case can cover several related ACs; US-08 ACs are Next phase.

## Approved plan, Phase 1 (CV-ready core), rebalanced Nov 2

| Day | Hours | Build | Evidence |
|---|---|---|---|
| Mon 11/2 | ~0.5 | 1.1 GitHub repo, README notice, PROGRESS.md | Repo pushed |
| Tue 11/3 | 5 | 1.2 User stories, 1.3 solution and publisher; ADO org, project, sprint, stories and estimates; Test Plans trial; Parks Asset table, Category choice, Case→Asset lookup, case form | Sprint board, solution components |
| Wed 11/4 | 7 | Business rule, 30-asset CSV import, queues and routing (Web API trigger check included), response SLA (test plus business), notification flow (channel verified first) | Import counts and errors, routed cases, SLA warning and failure, flow run history |
| Thu 11/5 | 3 | Front-desk and supervisor roles, 2 non-admin users, column security profiles (synthetic DOB, payment notes), UI verification | Denied vs allowed screenshots |
| Fri 11/6 | 4.5 | ~20 ADO test cases traced to stories, app registration, Web API CRUD and security tests, loader (50 records), 1 Playwright smoke test, axe and keyboard checks, Mac/iPhone/iPad pass | Test suite, API results, platform matrix |
| Fri 11/6 | 2.5 | Verification: ADO test runs, bugs, triage, retest, execution report, solution export, README results, CV entry | Execution report, exported solution |

**Moved to Phase 2 (D-09):** SQL (TDS) reconciliation, Playwright tests 2–3.

## Phase 2 (Nov 8 to trial end)

1. Full loader (200–500 records)
2. 8–12 Playwright tests
3. CI
4. Power BI (service)
5. AI case-summary testing
6. Performance and throttling notes
7. User guide
8. Readiness report
9. Demo script
10. Extend the trial before expiry

## Tools

- **Ready:** GitHub (verified Nov 2: push works). **Assumed:** Node.js and VS Code, Playwright on the Mac
- **Missing:** Azure DevOps (create on Tue)
- **Devices:** Mac, iPhone, iPad

## Open questions

- None right now.
