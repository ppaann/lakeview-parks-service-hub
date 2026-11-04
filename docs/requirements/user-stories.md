# User stories: City of Lakeview Parks & Recreation Resident Service Hub

> Independent, self-directed portfolio demo for a fictional city. Not affiliated with any real city or government agency.

**Authorship:** US-01 to US-03 written by Pan, with tutor review. US-04 to US-08 drafted with AI assistance, then reviewed and edited by Pan.
**Data rule:** All resident data in this project is synthetic. No real personal data, card numbers or banking details.
**Status labels:** Phase 1 = being built Nov 3–6, 2026. *Next phase* = planned, not built.

---

### US-01: Every case has one service category

As a Parks & Rec customer representative, I want every case to be assigned one category
from a fixed list, so that the right team is assigned.

- AC-01.1: The Category list contains exactly these 5 values: Facility Rental, Registration Issue, Refund Request, Complaint, Asset Maintenance.
- AC-01.2: Given Category is empty, when the user saves, then the case is not saved and Category is flagged as required.
- AC-01.3: A value not in the list is rejected on the form and through the Web API.
- AC-01.4: Given a case has a category, a user with the Front Desk or Supervisor role can change it. A user with neither role cannot change it.
- AC-01.5: When Category changes, the old value, new value, who and when are shown in the case's audit history.

### US-02: Cases route to the right queue by category

As a Parks & Rec front-desk representative, I want each new case routed to the queue of the
team that resolves it, so that I don't forward cases by hand and nothing gets lost.

- AC-02.1: Given a new case, when it is saved, then it appears in the queue for its category:

  | Category           | Queue                   |
  | ------------------ | ----------------------- |
  | Facility Rental    | Facility Rentals        |
  | Registration Issue | Registration & Payments |
  | Refund Request     | Registration & Payments |
  | Complaint          | Supervisor Review       |
  | Asset Maintenance  | Parks Maintenance       |

- AC-02.2: Given a case with no category or with a category that matches no routing rule
  (e.g., created through the Web API or from email), when it is routed, then it goes to the
  General Intake queue for front-desk triage.
- AC-02.3: Given a routed case, when its Category is changed and saved, then it moves to the
  queue for the new category and is no longer in the old queue.
- AC-02.4: A case created through the Web API routes the same way as one created on the form
  (same mapping as AC-02.1).
- AC-02.5: Given a case routed to a queue, then its queue item's Worked By is empty.
  When a representative picks it, then Worked By shows that representative.
- AC-02.6: Each queue lists the oldest items first, by the case's Created On.

### US-03: Response SLA with warning and failure

As a Parks & Rec supervisor, I want each case to get a first response within a set time,
so that residents aren't left waiting and I can see which cases are at risk.

- AC-03.1: Business hours are Monday–Friday, 8:00–17:00 Pacific. The SLA clock runs only during business hours.
- AC-03.2: For all categories except Asset Maintenance, warning (Nearing noncompliance) at
  9 business hours (1 business day) and failure (Noncompliant) at 27 business hours
  (3 business days) after Created On.
- AC-03.3: For Asset Maintenance, warning at 2 business hours and failure at 6 business hours.
- AC-03.4: Given a first response is sent before the failure time, the SLA shows Succeeded.
- AC-03.5: Given no response by the failure time, the SLA shows Noncompliant.
- AC-03.6: While the case is On Hold, the SLA clock pauses and resumes afterwards.
- AC-03.7: The case form shows the time remaining for the SLA.

### US-04: Asset Maintenance cases require an asset

As a Parks & Rec front-desk representative, I want every Asset Maintenance case linked to a
specific park asset, so that maintenance crews know exactly what to fix and we can see
each asset's case history.

- AC-04.1: Given Category = Asset Maintenance and Asset is empty, when the user saves,
  then the case is not saved and the message names the Asset field.
- AC-04.2: Given any other category, Asset is optional and the case saves without it.
- AC-04.3: Given an asset with linked cases, the asset's record lists all of those cases.
- AC-04.4: Given a case is created through the Dataverse Web API with Category = Asset
  Maintenance and no asset, then it is rejected, the same as on the form.

### US-05: Import the asset inventory from CSV

As a Parks maintenance supervisor, I want to load our asset inventory from a spreadsheet,
so that staff can link cases to real assets without typing each one in.

- AC-05.1: The CSV columns are Asset ID, Name, Type, Location, Condition, Responsible Team
  and Status. Asset ID and Name are required; a row missing either is rejected.
- AC-05.2: Asset ID is unique. A row whose Asset ID already exists, or appears twice in the
  file, does not create a second record.
- AC-05.3: A row with a value that isn't in a choice list (for example, Condition = "Broken")
  is rejected, and the import log identifies the row.
- AC-05.4: After the import, the success and failure counts in the import log match the
  rows in the file, and the number of new asset records matches the success count.
- AC-05.5: Importing the same file a second time creates no new records.
- AC-05.6: The column mapping, required columns and allowed choice values are documented.

### US-06: Requester is notified about a maintenance case

As a resident who reports a maintenance problem, I want to be told my report was received
and when it is resolved, so that I know the city is acting on it.

- AC-06.1: Given an Asset Maintenance case is created for a resident with contact details,
  then the resident is notified within 15 minutes. The message includes the case number,
  the asset name and the location.
- AC-06.2: Given an Asset Maintenance case is resolved, then the resident is notified that
  it was resolved.
- AC-06.3: Cases in other categories do not trigger this notification.
- AC-06.4: The message contains no date of birth, payment notes or other protected details.
- AC-06.5: Given the resident has no contact details, then no message is sent, the case still
  saves, and the failure is visible to staff.
- Channel: to be decided after we verify what the trial supports (email first; agree an
  alternative if email isn't available).

### US-07: Front desk can't see resident DOB or payment notes; supervisors can

As a Parks & Rec supervisor, I want residents' dates of birth and payment notes visible only
to supervisors, so that personal and payment details are seen only by staff who need them.

- AC-07.1: Given a Front Desk user opens a case or contact, then the DOB and Payment Notes
  values are not shown and can't be edited.
- AC-07.2: Given a Front Desk user views lists or exports them, then DOB and Payment Notes
  values are not shown.
- AC-07.3: Given a Front Desk user queries through the Dataverse Web API, then DOB and
  Payment Notes values are not returned, and an update to either is rejected.
- AC-07.4: Given a Supervisor user, then DOB and Payment Notes can be read and updated on
  forms, in lists and through the Web API.
- AC-07.5: Given a user with neither the Front Desk nor the Supervisor role, then the user
  can't open cases or contacts at all.
- AC-07.6: Hiding a field on a form doesn't count as protection; every check above is also
  verified through the Web API.

### US-08: Supervisor sees backlog and SLA results (*Next phase*)

As a Parks & Rec supervisor, I want a report of case volume, backlog and SLA results, so that
I can staff the right teams and spot problems early.

- AC-08.1 (*Next phase*): The report shows open cases by category and by queue.
- AC-08.2 (*Next phase*): The report shows SLA Succeeded vs Noncompliant for a selected date range,
  and average resolution time by category.
- AC-08.3 (*Next phase*): The report shows open Asset Maintenance cases by asset and location.
- AC-08.4 (*Next phase*): Each total matches a direct Dataverse count for the same filter
  (difference = 0).
