# refive blog-to-LinkedIn automation

An n8n workflow that turns a refive blog article into six LinkedIn drafts, three each for Mitul K. Jain and Willi. Google Sheets holds the research and the human approval queue.

## Files

- [n8n workflow](refive-blog-to-linkedin.json)
- [Google Sheets workbook](google-sheets-template.xlsx)
- [Sample output](sample-output.csv)

## Setup

1. Import the XLSX into Google Sheets as a new spreadsheet. Keep the tab names and headers. For a clean test, clear rows 2–7 of the approval queue in your working copy.
2. Import the JSON using n8n's **Import from File**. The export uses Google Sheets node 4.7 and Gemini node 1.2 and starts inactive.
3. In each of the six Google Sheets nodes, select your OAuth credential and replace `YOUR_GOOGLE_SPREADSHEET_ID` in **Document → By ID**. Each sheet is selected by name.
4. Select your Gemini credential in all three Gemini nodes. If the supplied model is unavailable, choose an available text model and verify its response structure. The article and reference context are sent to Gemini.
5. Test the RSS branch against a working copy. Check that it appends six new `Pending` rows, one per founder and weekday combination, with draft text and review fields populated.
6. Review the draft against the article. Enter the final copy in `Approved_Text`, set `Approval_Status` to `Approved`, and run **Check Approvals Manually**. Confirm it retrieves approved rows and skips pending rows.

## Workflow and workbook

The RSS trigger polls the refive feed hourly when active. It extracts article text, then Gemini classifies it using `ICP_Segments`, `Lifecycle_Stages` and `Strategic_Priorities`. The strategic priorities are labelled hypothetical. Gemini drafts Monday, Wednesday and Friday posts using the two profiles in `Founder_Voices`. A second Gemini pass checks claims, repetition and voice before the drafts are appended to `LinkedIn_Approval_Queue` for human review. The workflow stops at draft storage or approval retrieval. It has no publishing integration.

[n8n import and export](https://docs.n8n.io/workflows/export-import/) · [Google Sheets operations in n8n](https://docs.n8n.io/integrations/builtin/app-nodes/n8n-nodes-base.googlesheets/sheet-operations/)
