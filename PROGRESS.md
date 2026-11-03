# Project record: City of Lakeview Parks & Recreation Resident Service Hub

> Independent, self-directed portfolio demo for a fictional city. Not affiliated with any real city or government agency.

**Last updated:** Mon Nov 2, 2026 (end of the planning session)

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

**Not yet confirmed.** Ask Pan at the start of the next session. Don't infer it.

## Status legend

**Planned** → **Configured/Built** → **Executed** → **Verified (evidence captured)**

## Status

| Item                                                                          | Status                    | private notes                                                               |
| ----------------------------------------------------------------------------- | ------------------------- | --------------------------------------------------------------------------- |
| Dynamics 365 Customer Service Enterprise trial active                         | Verified                  | learning-notes/images/s0-02 (26 days left on Nov 2)                         |
| Project environment = CustomerService Trial (Trial, Ready, Dataverse Yes, US) | Verified                  | learning-notes/images/s0-04 (25 days left on Nov 2, so expiry about Nov 27) |
| Copilot Service admin center opens                                            | Planned (check at Day 1)  | n/a                                                                         |
| Copilot Service workspace browser support (Edge/Chrome)                       | Planned (verify on Learn) | n/a                                                                         |
| GitHub repo, README notice                                                    | Planned (Day 1)           | n/a                                                                         |
| User stories (6–8)                                                            | Planned (Day 1)           | n/a                                                                         |
| Solution and publisher                                                        | Planned (Day 1)           | n/a                                                                         |
| Everything else                                                               | Planned                   | n/a                                                                         |

## Decisions

- **D-01:** All project work happens in the **CustomerService Trial** environment.
- **D-02:** Create a new Azure DevOps org with the work account. Start the **Azure Test Plans 30-day trial in Phase 1** (Nov 3–4).
- **D-03:** Top priority is the full QA lifecycle in ADO (requirements → test cases → runs → bugs → triage → fix validation → execution report). Automation is a differentiator.
- **D-04:** Learning notes are Markdown plus an images folder, in `learning-notes/`, with a .zip snapshot for importing into a notes app.
- **D-05:** Saturday Nov 7 isn't available, so verification moves to Friday Nov 6 afternoon.
- **D-06:** CV lists only verified work with real counts. Planned work is labeled "Next phase."
- **D-07:** Test platforms are Mac (Edge, Chrome, Safari), iPhone and iPad (Power Apps mobile app). Windows and Android are documented as a coverage gap.

## Risks

- **R-01:** The trial environment is deleted after expiry (about Nov 27; extendable once). Mitigation: export the solution and keep everything in GitHub.
- **R-02:** The SQL (TDS) endpoint's documented client is SSMS, which is Windows-only. Try VS Code `mssql`; fall back to Web API aggregates.
- **R-03:** Power BI Desktop is Windows-only. Phase 2 uses the Power BI service, after a license check.
- **R-04:** Friday is overloaded. Cut order: loader to 50 records → Playwright to 2 tests → SQL reconciliation to Phase 2.
- **R-05:** The trial expires after 14 days of inactivity. Log in at least every few days.
- **R-06:** Copilot Service workspace browser support isn't verified yet.

## Approved plan, Phase 1 (CV-ready core)

| Day      | Hours | Build                                                                                                                                                                             | private notes                                                       |
| -------- | ----- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------- |
| Mon 11/2 | 2     | GitHub repo, README notice, PROGRESS, 6–8 user stories, solution and publisher in CustomerService Trial                                                                           | Repo committed, solution exists                                     |
| Tue 11/3 | 5     | ADO org, project, sprint, stories and estimates; start the Test Plans trial; Parks Asset table, Category choice, Case→Asset lookup, case form, business rule, 30-asset CSV import | Sprint board, import counts and errors, business rule firing        |
| Wed 11/4 | 7     | Queues, routing (Web API trigger check included), response SLA (test plus business version), notification flow (channel verified first), ~20 ADO test cases traced to stories     | Routed cases, SLA warning and failure, flow run history, test suite |
| Thu 11/5 | 3     | Front-desk and supervisor roles, 2 non-admin users, column security profiles (synthetic DOB, payment notes), UI verification                                                      | Denied vs allowed screenshots                                       |
| Fri 11/6 | 4.5   | App registration, Web API CRUD and security tests, loader (~100 records), SQL reconciliation attempt, 3 Playwright tests, axe and keyboard checks, Mac/iPhone/iPad pass           | API results, test output, platform matrix                           |
| Fri 11/6 | 2.5   | Verification: ADO test runs, bugs, triage, retest, execution report, solution export, README results, CV entry                                                                    | Execution report, exported solution                                 |

**Note:** Monday's 2-hour block wasn't used (the session ran to planning). The next session should start with Day 1 tasks and rebalance if needed.

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

- **Ready (assumed):** GitHub, Node.js and VS Code, Playwright on the Mac
- **Missing:** Azure DevOps (create on Tue)
- **Devices:** Mac, iPhone, iPad

## Open questions for the next session

1. Actual implementation start date
