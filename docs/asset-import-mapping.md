# Parks Asset CSV import: mapping and results (US-05)

> Independent, self-directed portfolio demo for a fictional city. All asset data is synthetic.

**Target table:** Parks Asset (`lkv_parksasset`)
**Method:** Model-driven app import wizard (**Import from CSV**), which reports Success / Failures / Partial Failures per import job and lets you export error rows.
**Files:** `data/parks-assets.csv` (30 valid rows), `data/parks-assets-negative.csv` (8 rows, mostly invalid by design)

## Column mapping

| CSV header | Dataverse column (display / schema) | Type | Required | Allowed values |
|---|---|---|---|---|
| Asset ID | Asset ID / `lkv_assetid` | Text, **alternate key** (Asset ID Key) | Business required | `PA-` + 4 digits; unique |
| Asset Name | Asset Name / `lkv_assetname` (primary) | Text | Business required | Free text |
| Asset Type | Asset Type / `lkv_assettype` | Local choice | Optional | Playground, Restroom, Sports Field Lighting, Irrigation, Bench & Picnic Table, Trail, Building (854,470,000–006) |
| Location | Location / `lkv_location` | Text | Optional | Fictional park name |
| Condition | Condition / `lkv_condition` | Local choice | Optional | Good, Fair, Poor, Critical |
| Response Team | Response Team / `lkv_responseteam` | Local choice | Optional | Parks Maintenance, Facilities Maintenance, Grounds & Irrigation |
| Asset Status | Asset Status / `lkv_assetstatus` | Local choice | Optional | In Service, Out of Service, Retired |

CSV headers match the column display names, so the wizard should map them automatically. Choices are matched by **label**.

**Duplicate rule (D-14):** a duplicate Asset ID must be **rejected**, never used to update an existing asset. So in the wizard, **don't select the alternate key** (selecting it makes the wizard *update* matching rows). The Asset ID Key on the table rejects duplicates at the database level.

## Expected vs actual results

### Run 1: `parks-assets.csv` (30 valid rows)

| Measure | Expected | Actual |
|---|---|---|
| Success | 30 | **30** (Nov 4, 2026; job Completed, Partial Failures 0, Errors 0, Total 30) |
| Failures | 0 | **0** |
| Parks Asset rows in Dataverse after import | 30 | **30** (Active Parks Assets view, "Rows: 30"; names with `&` imported intact) |

### Run 2: `parks-assets-negative.csv` (8 rows)

| Row | Asset ID | What's wrong | AC | Expected (per requirement) | Actual |
|---|---|---|---|---|---|
| 1 | *(blank)* | Missing Asset ID | AC-05.1 | Rejected | **Imported** (record with blank Asset ID) ❌ |
| 2 | PA-0035 | Missing Asset Name | AC-05.1 | Rejected | **Imported** (Asset Name shows "---") ❌ |
| 3 | PA-0001 | Already exists (from run 1) | AC-05.2 | Rejected, existing asset unchanged | **Rejected**: "Entity Key Asset ID Key violated" ✅; existing PA-0001 still "Lakeview Commons Playground A" ✅ |
| 4 | PA-0031 | First of a duplicate pair in the file | AC-05.2 | Created | **Created** ✅ |
| 5 | PA-0031 | Second of the pair | AC-05.2 | Rejected | **Rejected**: "Entity Key Asset ID Key violated" ✅ |
| 6 | PA-0032 | Valid control row | — | Created | **Created** ✅ |
| 7 | PA-0033 | Condition = "Broken" (not a choice) | AC-05.3 | Rejected and reported | **Imported, listed under Success, and the wizard added "Broken" as a new Condition option** (record shows Condition = Broken) ❌ |
| 8 | PA-0034 | Asset Type = "Swing Set" (not a choice) | AC-05.3 | Rejected and reported | **Imported, listed under Success, and the wizard added "Swing Set" as a new Asset Type option** (record shows Asset Type = Swing Set) ❌ |

**Expected totals:** Success 2, Failures 6.
**Actual totals (Nov 4, 2026, 4:59 pm Pacific):** **Success 6, Failures 2.** The alternate key worked (AC-05.2 passed); required-column and choice validation did not (AC-05.1 and AC-05.3 failed). Defect candidates DEF-01 to DEF-03 in PROGRESS.md; to be logged and triaged in ADO.

**Known risks (to observe, not assume):**
- "Business required" isn't enforced for server-side saves (R-13), so rows 1–2 might import with blank values.
- The wizard's option-set mapping step may ask how to map "Broken" and "Swing Set", and Microsoft Learn says unmapped values are dropped, so rows 7–8 might import with an empty choice (a partial failure or a silent drop).
- **Observed:** the values weren't dropped. The wizard **created new choice options** ("Broken" in Condition, "Swing Set" in Asset Type), which matches Learn's note that the wizard "adds all mapped values" to the app. A data import changed the table's schema.

Any difference between expected and actual becomes a logged finding or defect.

### Run 3: re-import `parks-assets.csv` (AC-05.5)

| Measure | Expected | Actual |
|---|---|---|
| New records created | 0 | **0** (Nov 4, 2026: Success 0, Errors 30, reported by Pan; all key violations expected) ✅ |
| Parks Asset rows in Dataverse | unchanged | *(confirm the view still shows 36 rows)* |
