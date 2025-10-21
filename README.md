The error pipeline - write error file to sldb pipeline processes error records from other pipelines by creating unique identifiers, mapping error details to a structured format, and writing the results to JSON files. It captures pipeline execution information including error details, warnings, and payload data for logging and debugging purposes.

Snap Summary:

CR Pipeline Identifier : generates a unique pipeline identifier by combining the pipeline UUID with the current timestamp, and captures the input processing time in a formatted date-time string.
Map Error Records - EMail : maps error record details to structured fields including pipeline name, project path, snap label, pipeline start time, warning messages, original payload, error information, and reason codes.
JSON Formatter: formats the mapped data into JSON structure with the pipeline identifier as a header field.
File Writer: writes the formatted JSON data to individual files named with the pipeline identifier and overwrites existing files with the same name.

*******

The Error Handling, mapping and send email pipeline handles error notifications and reporting for data export/import operations. It processes different types of errors (Upload, Export, Import) through multiple segments, routes them appropriately, and sends email notifications with detailed information. For import errors with failures, it generates Excel attachments containing error details before sending notifications.

Snap Summary:

Set Upload Flag : Sets the error_type field to 'Upload' for upload error processing.
Union: Combines data streams from multiple segments .
Router: Routes data to different output views based on error_type .
Map Email Fields : Maps source and target information from pipeline parameters to email template fields for upload errors.
Email Sender: Sends failure notification emails for upload errors with detailed source/target information.
Exit Upload Error : Terminates the pipeline with "Upload failed" message.
Set Export Flag : Sets the error_type field to 'Export' for export error processing.
Set Import Flag : Sets the error_type field to 'Import' for import error processing.
Copy: Duplicates the data stream to allow parallel processing for attachment creation and email preparation.
Mapper3 : Maps detailed export/import information from the original data structure to email template fields.
Merge Summary Attachment : Performs inner join to merge email content with attachment information.
Email Sender: Sends notification emails for import errors with Excel attachments containing failure details.
Delete Attachment : Removes the temporary attachment file after email is sent.
Exit Import Error : Terminates the pipeline with "Import complete but with failures" message.
Split Errors : Extracts error details from the Results array for attachment creation.
Excel Formatter: Formats the error data into Excel format with 'Error' sheet name.
Create Attachment : Writes the formatted error data to 'errors.xls' file, overwriting if exists.
Tail: Processes the file creation result and passes filename information to the join.
Map Email Fields : Maps source and target information from pipeline parameters to email template fields for import errors.
Email Sender: Sends failure notification emails for export errors with detailed information.
Exit Export Error : Terminates the pipeline with "Export failed" message.

*******

The write error log to sldb and send email pipeline processes error records from pipeline executions and handles them through two parallel paths: writing error details to a JSON file and sending email notifications to the team. It captures comprehensive error information including pipeline metadata, error details, and stack traces for both logging and alerting purposes.

Snap Summary:

Copy: Duplicates the input error records to send them to both processing segments simultaneously.
Map Error Records - File Write : Maps error record fields including pipeline name, project path, pipeline runtime ID, snap label, start time, error details, reason, resolution, stack trace, and payload for file output.
JSON Formatter: Formats the mapped error data into JSON format for structured file storage.
File Writer: Writes the formatted JSON error data to a file named "errors.json", overwriting any existing file.
Map Error Records - EMail : Maps the same error record fields as the file write mapper but prepares them for email notification formatting.
Email Sender: Sends HTML-formatted email notifications to rdill@snaplogic.com with comprehensive error details including pipeline information, error messages, reasons, resolutions, and stack traces.

*******

The Simple Error Handler pipeline formats JSON data and writes it to a file for error logging purposes. It takes input data, formats it as JSON with a custom header containing error information, and then writes the formatted content to a JSON file with overwrite capability.

Snap Summary:

JSON Formatter: formats input data as JSON and adds a header with a content-location field that generates an error filename using pipeline UUID, snap count, and snap label information.
File Writer: writes the formatted JSON content to a file named using the pipeline UUID with 'errorlog.json' suffix, overwriting any existing file with the same name.
