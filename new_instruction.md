You are investigating content-validation exceptions in the folder I provide.
Your objective is to analyze all Excel validation reports and all exception categories, but you must work interactively with me and must not invent troubleshooting rules.
1. Initial folder scan
First inspect the entire folder and identify:
all *_results_content_validation_report.xlsx files;
any .sql files;
the worksheets and relevant columns in each Excel report.
Do not modify the original Excel files.
For every Excel report, focus only on rows where:
Status = Different
The important columns include:
Status;
Column C TXID;
Output Details;
Scenario;
any other columns necessary to understand the comparison.
2. Categorize the exceptions
Analyze Output Details for every row where Status = Different.
Group the rows into meaningful exception categories based on the nature of the difference, not merely the complete literal Output Details string.
For example, different TXIDs, UUIDs, IDs, payload values, or other row-specific identifiers should not automatically create separate categories if the underlying exception pattern is the same.
However, do not over-normalize. If two messages appear similar but could represent different technical causes, keep them separate until investigation proves they are the same.
For every category, maintain:
category ID;
category description;
source Excel file(s);
number of affected rows;
Column C TXIDs;
Scenario values;
representative Output Details;
investigation status;
troubleshooting steps already performed;
findings/evidence;
whether all cases have been verified to have the same root cause.
3. SQL / database requirement
The database to investigate is:
cbcc-eu-prodlike
Column C of the Excel report contains the TXID that must be used for database investigation.
Before attempting the first database investigation, check whether an appropriate .sql file exists in the same folder.
If the SQL file does not exist, STOP and ask me to provide the SQL file in the same folder.
Do not invent the SQL.
Once I provide the SQL file:
read and understand it;
explain briefly how the TXID needs to be supplied;
use the SQL as the basis for database investigation.
Database access must be read-only. Never execute INSERT, UPDATE, DELETE, MERGE, DROP, ALTER, TRUNCATE, or other commands that change data or database objects.
4. Investigate ONE category at a time
Do not investigate every category simultaneously.
Select one unresolved exception category and show me:
category ID;
category description;
affected file(s);
number of cases;
TXID list;
example Output Details.
Then query cbcc-eu-prodlike for the category's Column C TXIDs using the supplied SQL.
Summarize the database results and observable similarities/differences.
5. Ask me how to troubleshoot
After retrieving the initial database evidence for that category, STOP and ask me what troubleshooting step should be performed next.
Do not assume the root cause yourself unless I have already taught you an applicable troubleshooting rule earlier in this investigation.
I may give instructions such as:
check a particular table or field;
compare two processing states;
inspect payload content;
check message history;
check timestamps;
identify whether a field was transformed;
run another SQL;
compare with another system/state;
use another diagnostic procedure.
Record my troubleshooting instruction as part of the investigation procedure for that category.
6. Apply each troubleshooting step to EVERY case
This is critical.
When I give you a troubleshooting step for a category, do not test only the sample TXID.
Apply the same check to every TXID belonging to that category.
Then compare the results.
Determine whether all cases exhibit the same technical pattern.
Report something like:
Category CAT-003
Cases checked: 27/27
Same pattern: 25
Different pattern: 2
Never claim that a category has one root cause based only on one or a few samples.
7. Consistency decision
If all cases have the same behavior, tell me:
what evidence is consistent;
how many cases were verified;
your current interpretation;
whether there are any remaining uncertainties.
Then ask me whether this is sufficient to mark the category as confirmed/resolved or whether another troubleshooting step is required.
If any case behaves differently, do NOT force it into the same conclusion.
Show me:
which TXIDs follow the expected pattern;
which TXIDs do not;
the evidence that differs.
Then STOP and ask me for further instructions.
If appropriate, propose splitting the category into subcategories, but do not finalize the split until the evidence supports it.
8. Learn troubleshooting rules carefully
When a category is confirmed by me, record the confirmed diagnostic pattern and troubleshooting procedure.
If a later category appears to match an already-confirmed pattern, you may tell me:
This category appears to match the previously confirmed rule CAT-xxx because ...
You may then apply the already-confirmed checks to all cases.
But still validate all TXIDs before concluding that the new category has the same cause.
Never generalize from similarity alone.
9. Investigation loop
Continue using this loop:
Find unresolved category
→ collect all Column C TXIDs
→ query cbcc-eu-prodlike
→ summarize evidence
→ ask me for troubleshooting step
→ apply the step to ALL cases
→ compare results
→ if inconsistent, ask me
→ if consistent, ask me to confirm resolution
→ record conclusion
→ move to next unresolved category
Continue until every Status = Different row from every Excel report has been assigned to an investigated category.
10. Maintain investigation progress
Maintain a persistent investigation record so progress is not lost.
Recommended structure:
analysis_progress.json
and
content_validation_analysis.md
Store at least:
files scanned;
total Different rows;
categories discovered;
category → TXID mapping;
categories not started;
categories under investigation;
categories confirmed;
categories requiring further instruction;
troubleshooting steps supplied by me;
DB evidence;
exceptions that do not fit their original category.
Update the progress files after every investigation step.
11. Final completeness check
Before declaring the investigation complete, verify mathematically:
Total rows with Status=Different
=
Total rows assigned to all final categories
and every assigned row must have an investigation status.
No TXID or Different row may silently disappear from the analysis.
Finally produce a consolidated report containing, for each Excel file and category:
exception category;
number of cases;
TXIDs;
Scenario;
representative Output Details;
troubleshooting procedure;
DB findings;
confirmed root cause/explanation;
exceptional cases;
final status.
Important operating rule: whenever evidence is insufficient, cases within a category disagree, SQL is missing, or you need domain knowledge from me, STOP AND ASK ME. Do not invent the missing explanation.
