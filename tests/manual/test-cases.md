# Manual test cases (draft for review)

> Independent, self-directed portfolio demo for a fictional city. Not affiliated with any real city or government agency.

**Status:** Drafted with AI assistance from the user stories and the configuration actually built; to be reviewed and edited by Pan before import into Azure Test Plans.
**Import:** one CSV per story in `ado-import/`, imported into that story's requirement-based suite (so each test case links to its story). Assigned To is left blank on purpose (no tenant identities in the public repo).
**Count:** 29 test cases across US-01 to US-05 and US-07. US-06 (notification) and US-08 (reporting) are *Next phase*.

## US-01

### TC-01.1 [AC-01.1] Category lists exactly the 5 values

| # | Action | Expected |
|---|---|---|
| 1 | As admin, open Copilot Service workspace and select New Case. | The case form opens with a required Category field. |
| 2 | Open the Category drop-down. | Exactly 5 values: Facility Rental, Registration Issue, Refund Request, Complaint, Asset Maintenance (plus the empty --Select-- entry). |

### TC-01.2 [AC-01.2] Case without Category is not saved

| # | Action | Expected |
|---|---|---|
| 1 | New Case: enter Case Title and Customer; leave Category empty. | Fields accept input. |
| 2 | Select Save. | The case is not saved; Category is flagged as required. |

### TC-01.3 [AC-01.3] Invalid Category value is rejected through the Web API
*Precondition:* Needs a Web API client that can send POST requests.

| # | Action | Expected |
|---|---|---|
| 1 | Precondition check: Needs a Web API client that can send POST requests. | Precondition met. |
| 2 | With a Web API client signed in as admin, POST a case with lkv_category set to an integer that isn't one of the 5 option values. | The request fails with an error; no case is created. |
| 3 | Query cases by that title. | No record exists. |

### TC-01.4 [AC-01.4] Front Desk can change Category; minimal user can't

| # | Action | Expected |
|---|---|---|
| 1 | Sign in as Front Desk test user (user1). Open an existing test case, change Category, Save. | Saved; the new Category is shown. |
| 2 | Sign in as minimal test user (user3, Basic User only). Try to open the same case. | The case can't be opened or edited (no access). |

### TC-01.5 [AC-01.5] Category change is recorded in audit history
*Precondition:* Auditing enabled for the environment, Case table and Category column.

| # | Action | Expected |
|---|---|---|
| 1 | Precondition check: Auditing enabled for the environment, Case table and Category column. | Precondition met. |
| 2 | As admin, change the Category of a test case and Save. | Saved. |
| 3 | Open the case's Audit History. | An entry shows old value, new value, the user and the time. |

## US-02

### TC-02.1 [AC-02.1] Save & Route sends each category to its queue

| # | Action | Expected |
|---|---|---|
| 1 | Create a case with Category Facility Rental; select Save & Route, then Route. | Case appears in Facility Rentals; timeline shows 'added to Facility Rentals'. |
| 2 | Repeat with Registration Issue. | Case appears in Registration & Payments. |
| 3 | Repeat with Refund Request. | Case appears in Registration & Payments. |
| 4 | Repeat with Complaint. | Case appears in Supervisor Review(Public). |
| 5 | Repeat with Asset Maintenance (with a Parks Asset). | Case appears in Parks Maintenance. |

### TC-02.2 [AC-02.2] Case matching no category rule goes to General Intake
*Precondition:* Needs a Web API client (Category is only form-required).

| # | Action | Expected |
|---|---|---|
| 1 | Precondition check: Needs a Web API client (Category is only form-required). | Precondition met. |
| 2 | Create a case with no Category through the Web API (Route Case = Yes). | Case is created. |
| 3 | Check the queues. | Case appears in General Intake (catch-all rule). |

### TC-02.3 [AC-02.3] Re-categorized case moves to the new queue

| # | Action | Expected |
|---|---|---|
| 1 | Route a case with Category Registration Issue. | Case is in Registration & Payments. |
| 2 | Change Category to Facility Rental; Save & Route, then Route. | Case is in Facility Rentals. |
| 3 | Open Registration & Payments queue items. | The case is no longer listed there. |

### TC-02.4 [AC-02.4] Case created through the Web API routes on create
*Precondition:* Needs a Web API client.

| # | Action | Expected |
|---|---|---|
| 1 | Precondition check: Needs a Web API client. | Precondition met. |
| 2 | POST a case with Category Complaint through the Web API (no explicit Route Case). | Case is created. |
| 3 | Check Supervisor Review(Public). | Case appears without any manual routing step. |

### TC-02.5 [AC-02.5] Worked By is empty until a representative picks the item

| # | Action | Expected |
|---|---|---|
| 1 | Route a case to Facility Rentals. | Queue item listed; Worked By is empty; case Owner unchanged. |
| 2 | As Front Desk test user (user1), open the queue item and select Pick. | Worked By shows the Front Desk user. |

### TC-02.6 [AC-02.6] Queue lists oldest cases first by Created On

| # | Action | Expected |
|---|---|---|
| 1 | Route two cases to the same queue, the older one second (e.g., re-route an older case). | Both items listed. |
| 2 | Open the queue's items view. | Items are ordered oldest first by the case's Created On. |

### TC-02.7 [AC-02.1 (negative)] Plain Save doesn't route a form-created case

| # | Action | Expected |
|---|---|---|
| 1 | Create a case with Category Complaint and select Save only. | Saved. |
| 2 | Wait 2 minutes; check the timeline and Supervisor Review(Public). | No 'added to queue' post; case not in any queue. |

## US-03

### TC-03.1 [AC-03.4] SLA succeeds when a first response is sent before failure (test SLA)
*Precondition:* Lakeview Response SLA active and default; TEST item: titles starting SLA-TEST, warn 5 min, fail 10 min, 24/7.

| # | Action | Expected |
|---|---|---|
| 1 | Precondition check: Lakeview Response SLA active and default; TEST item: titles starting SLA-TEST, warn 5 min, fail 10 min, 24/7. | Precondition met. |
| 2 | Create a case titled 'SLA-TEST <n> success', any category; Save; note the time. | SLA KPI instance First Response By – Case is In progress. |
| 3 | Within 5 minutes set First Response Sent = Yes; Save; refresh the SLA grid. | KPI instance status Succeeded; Succeeded On filled in. |

### TC-03.2 [AC-03.5] SLA warns then fails without a response (test SLA)
*Precondition:* As TC-03.1.

| # | Action | Expected |
|---|---|---|
| 1 | Precondition check: As TC-03.1. | Precondition met. |
| 2 | Create a case titled 'SLA-TEST <n> warn and fail'; Save; note the time. | KPI instance In progress. |
| 3 | After about 5 minutes refresh the SLA grid. | Nearing noncompliance; Warning Time shown. |
| 4 | After about 10 minutes refresh. | Noncompliant; Failure Time shown. |

### TC-03.3 [AC-03.6] SLA clock pauses while the case is On Hold
*Precondition:* SLA item allows pause and resume with an On Hold pause condition.

| # | Action | Expected |
|---|---|---|
| 1 | Precondition check: SLA item allows pause and resume with an On Hold pause condition. | Precondition met. |
| 2 | Create an SLA-TEST case; after 2 minutes set Status Reason = On Hold; Save. | KPI instance Paused. |
| 3 | Wait 6 minutes, then set status back to In Progress. | KPI resumes; Warning/Failure times shift by the paused time (no warning reached during hold). |

### TC-03.4 [AC-03.1–03.3] Business SLA uses business hours and category durations
*Precondition:* Business SLA items and business-hours calendar configured (Next phase).

| # | Action | Expected |
|---|---|---|
| 1 | Precondition check: Business SLA items and business-hours calendar configured (Next phase). | Precondition met. |
| 2 | Create a Complaint case on a weekday during business hours. | Warning time = Created On + 9 business hours; Failure = + 27 business hours. |
| 3 | Create an Asset Maintenance case. | Warning = + 2 business hours; Failure = + 6 business hours. |
| 4 | Create a case on Friday at 4:30 pm. | Clock skips the weekend (Mon–Fri 8:00–17:00 Pacific). |

## US-04

### TC-04.1 [AC-04.1] Asset Maintenance requires a Parks Asset

| # | Action | Expected |
|---|---|---|
| 1 | New case: Category Asset Maintenance, no Parks Asset; Save. | Save blocked: Business Process Error 'Asset Maintenance cases must be linked to a Parks Asset.' |
| 2 | Select Parks Asset 'Lakeview Commons Playground A' (PA-0001); Save. | Case saves. |

### TC-04.2 [AC-04.2] Other categories save without an asset

| # | Action | Expected |
|---|---|---|
| 1 | New case: Category Complaint, no Parks Asset; Save. | Case saves without error. |

### TC-04.3 [AC-04.3] Asset record lists its linked cases

| # | Action | Expected |
|---|---|---|
| 1 | In Lakeview Parks Assets, open PA-0001. | Cases grid lists every case linked to PA-0001. |
| 2 | Open an asset with no linked cases (e.g., PA-0002). | Cases grid is empty. |

### TC-04.4 [AC-04.4] Web API create without an asset is rejected
*Precondition:* Needs a Web API client.

| # | Action | Expected |
|---|---|---|
| 1 | Precondition check: Needs a Web API client. | Precondition met. |
| 2 | POST a case with Category Asset Maintenance and no lkv_parksasset through the Web API. | Request fails with the business rule error; no case created. |

## US-05

### TC-05.1 [AC-05.1] Import rejects rows missing Asset ID or Asset Name
*Precondition:* parks-assets.csv already imported (30 assets).

| # | Action | Expected |
|---|---|---|
| 1 | Precondition check: parks-assets.csv already imported (30 assets). | Precondition met. |
| 2 | Import data/parks-assets-negative.csv (no alternate key, Allow Duplicates No). | Import job completes. |
| 3 | Open the job's Failures and Success tabs. | Rows without Asset ID or Asset Name are listed under Failures, not imported. |

### TC-05.2 [AC-05.2] Import rejects duplicate Asset IDs and keeps the existing asset

| # | Action | Expected |
|---|---|---|
| 1 | In the same import job, check rows PA-0001 and the second PA-0031. | Both listed under Failures (Asset ID key violated). |
| 2 | Open PA-0001. | Name is still 'Lakeview Commons Playground A'. |

### TC-05.3 [AC-05.3] Import rejects invalid choice values

| # | Action | Expected |
|---|---|---|
| 1 | In the same import job, check rows PA-0033 (Condition 'Broken') and PA-0034 (Asset Type 'Swing Set'). | Both rows rejected and reported. |
| 2 | Check the Condition and Asset Type choice lists. | No new options were added. |

### TC-05.4 [AC-05.4, AC-05.5] Import counts are accurate and re-import creates no duplicates

| # | Action | Expected |
|---|---|---|
| 1 | Import data/parks-assets.csv into an empty table. | Success 30, Failures 0; view shows 30 rows. |
| 2 | Import the same file again. | Success 0, Errors 30; row count unchanged. |

## US-07

### TC-07.1 [AC-07.1] Front Desk can't read or edit DOB and Payment Notes on forms
*Precondition:* Synthetic Birthday and Payment Notes set by admin; column security enabled; Lakeview Supervisors profile = supervisor only.

| # | Action | Expected |
|---|---|---|
| 1 | Precondition check: Synthetic Birthday and Payment Notes set by admin; column security enabled; Lakeview Supervisors profile = supervisor only. | Precondition met. |
| 2 | Sign in as Front Desk test user (user1); open contact Claudia Mazzanti, Details tab. | Birthday shows a lock; no value visible; not editable. |
| 3 | Open the test case with synthetic Payment Notes. | Payment Notes shows a lock and ******; not editable. |

### TC-07.2 [AC-07.2] Front Desk views and exports don't show secured values

| # | Action | Expected |
|---|---|---|
| 1 | As Front Desk test user (user1), open a Contacts view and add the Birthday column. | Values hidden. |
| 2 | Export the view to Excel and open the file. | Birthday column empty or masked for every row. |

### TC-07.3 [AC-07.3] Web API hides secured columns from Front Desk

| # | Action | Expected |
|---|---|---|
| 1 | In Front Desk test user (user1)'s signed-in browser, GET /api/data/v9.2/contacts?$select=fullname,birthdate&$filter=fullname eq 'Claudia Mazzanti'. | birthdate is null. |
| 2 | GET /api/data/v9.2/incidents?$select=title,lkv_paymentnotes&$filter=title eq '<test case title>'. | lkv_paymentnotes is null. |
| 3 | Send a PATCH setting birthdate (Web API client, as Front Desk). | Request is rejected. |

### TC-07.4 [AC-07.4] Supervisor can read and update secured columns (form and API)

| # | Action | Expected |
|---|---|---|
| 1 | As Supervisor test user (user2), open Claudia Mazzanti and the test case. | Birthday and Payment Notes values visible (key icon). |
| 2 | Update Payment Notes to a new synthetic value; Save. | Saved; new value shown. |
| 3 | Run the two GET requests from TC-07.3 in the Supervisor's session. | birthdate and lkv_paymentnotes return the values. |

### TC-07.5 [AC-07.5] Minimal user can't open cases or contacts

| # | Action | Expected |
|---|---|---|
| 1 | Sign in as minimal test user (user3, Basic User only); open Copilot Service workspace. | No access to the app, or no case records visible. |
| 2 | Open a case or contact by URL. | Access denied. |

