# G-Sheet-Automation
Google Sheet to Gmail Automation

Overview

This n8n workflow reads student/course data from a Google Sheet, formats
each row into a clean email, and sends the result through Gmail.

The workflow is designed for a simple end-to-end automation:

Manual Trigger → Google Sheets → Edit Fields → Gmail

Workflow

Node                                Purpose

When clicking 'Execute            Starts the workflow manually from
workflow'                         n8n.

Get row(s) in sheet             Reads rows from the configured
Google Sheet.

Edit Fields                     Creates the email subject and
formats the sheet data into a
readable message body.

Data Expected from Google Sheets

The workflow expects the sheet to contain these columns:

Name

Course

Status

The current workflow references the Name column as Name (with a
trailing space), so make sure the column header matches exactly or
update the expression in the Edit Fields node.

Email Format

Subject

Data Summary from Google Sheet

Body

Hi All,

Here is the latest data from the sheet:

- Name: {{ Name }}
- Course: {{ Course }}
- Status: {{ Status }}

Regards,
Explore AI

The values are populated dynamically from each Google Sheets row.

Setup

1. Import the workflow

Import the supplied Google Sheet Automation.json workflow into your
n8n instance.

2. Configure Google Sheets credentials

Open Get row(s) in sheet and select a Google Sheets OAuth2
credential that has access to the source spreadsheet.

The imported workflow is configured to read from the Google Sheet
referenced in the workflow JSON. For another spreadsheet, replace the
document and sheet selection with your own.

3. Verify the sheet

Make sure the sheet contains the required headers:

Name | Course | Status

Add at least one data row for testing.

4. Configure Gmail credentials

Open Send a message and select the Gmail OAuth2 credential you want
to use.

The current workflow sends to:

example@gmail.com

Change the Send To field if you want to deliver the email to another
address.

5. Test the workflow

Click Execute Workflow.

The workflow should:

Start from the Manual Trigger.

Retrieve rows from Google Sheets.

Create emailSubject and emailBody fields.

Send the formatted email through Gmail.

Important: Empty Sheet Handling

The current imported workflow does not contain an explicit
empty-sheet check. If the Google Sheets node returns no items, the
downstream nodes normally have no item to process, so Gmail will not
receive a message. However, this behavior is implicit rather than an
explicit validation step.

For a robust production workflow, add an IF node after Get row(s)
in sheet:

Manual Trigger
      ↓
Google Sheets
      ↓
IF: Rows exist?
   ├── Yes → Edit Fields → Gmail
   └── No  → Stop

This makes the requirement explicit and prevents accidental blank emails
if the workflow is later modified.

Troubleshooting

Email fields are empty

Check that the Google Sheet column names exactly match the expressions
used in Edit Fields.

In particular, the current workflow uses:

$json['Name ']
$json.Course
$json.Status

If the sheet header is Name without the trailing space, change the
first expression to:

$json.Name

Google Sheets cannot read the spreadsheet

Verify that:

The Google Sheets credential is connected.

The authenticated Google account has access to the spreadsheet.

The selected spreadsheet and sheet are correct.

Gmail does not send the message

Verify that:

The Gmail credential is connected.

The destination address is correct.

Gmail authorization has been completed for the n8n instance.

Security

Do not publish workflow exports containing credentials or sensitive
configuration.

The workflow JSON contains credential references and a destination email
address. When sharing the workflow publicly, remove or replace sensitive
information and reconnect credentials after importing.

Customization Ideas

You can extend this workflow by:

Replacing the Manual Trigger with a Schedule Trigger.

Sending one summary email containing all rows instead of one email
per row.

Adding an IF node to filter rows by status.

Adding an HTML email template for improved formatting.

Adding a timestamp to the subject.

Adding an error-handling path for failed Gmail deliveries.

Marking processed rows in Google Sheets to prevent duplicate emails.

Expected Result

After a successful execution, the chosen Gmail recipient receives a
readable email containing the values from the Google Sheet rather than a
raw JSON/data dump.

Workflow File

The workflow used for this README is the supplied Google Sheet
Automation n8n workflow.
