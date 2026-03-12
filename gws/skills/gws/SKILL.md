---
name: gws
description: Use Google Workspace CLI to interact with Gmail, Calendar, Drive, Sheets, Docs, Tasks, and other Google services
when_to_use: When the user asks to read/send email, check calendar, manage Drive files, read/write spreadsheets, manage tasks, or interact with any Google Workspace service. Trigger on mentions of "gmail", "email", "calendar", "drive", "sheets", "docs", "tasks", "google workspace", "gws"
when_not_to_use: When using MCP-based Google Calendar or Gmail tools (those are separate integrations). Use this skill for direct CLI access, bulk operations, or services not covered by MCP tools.
---

# Google Workspace CLI (gws)

CLI tool at `/Users/bitchbot/.nvm/versions/node/v25.8.0/bin/gws` for interacting with Google Workspace APIs. Authenticated as stephenhamilton@covertauto.com via OAuth2.

## Core Command Pattern

```bash
gws <service> <resource> <method> --params '<JSON>' [--json '<JSON>'] [--format <fmt>]
```

## Services

| Service | Description |
|---------|-------------|
| gmail | Send, read, manage email |
| calendar | Events, calendars, free/busy |
| drive | Files, folders, shared drives |
| sheets | Read/write spreadsheets |
| docs | Read/write Google Docs |
| tasks | Task lists and tasks |
| people | Contacts and profiles |
| chat | Chat spaces and messages |
| slides | Presentations |
| forms | Google Forms |
| admin-reports | Audit logs, usage reports |

## Gmail

```bash
# List unread inbox
gws gmail +triage

# Send email
gws gmail +send --to "user@example.com" --subject "Subject" --body "Body text"

# Reply to a message
gws gmail +reply --message-id <id> --body "Reply text"

# Reply all
gws gmail +reply-all --message-id <id> --body "Reply text"

# Forward
gws gmail +forward --message-id <id> --to "user@example.com"

# List messages with query
gws gmail users messages list --params '{"userId": "me", "q": "is:unread from:someone@example.com", "maxResults": 10}'

# Read a specific message
gws gmail users messages get --params '{"userId": "me", "id": "<messageId>", "format": "full"}'

# Watch for new emails (streaming)
gws gmail +watch
```

## Calendar

```bash
# View upcoming agenda
gws calendar +agenda

# Create event
gws calendar +insert --summary "Meeting" --start "2026-03-12T10:00:00" --end "2026-03-12T11:00:00"

# List events
gws calendar events list --params '{"calendarId": "primary", "timeMin": "2026-03-11T00:00:00Z", "maxResults": 10, "singleEvents": true, "orderBy": "startTime"}'

# Get free/busy
gws calendar freebusy query --json '{"timeMin": "2026-03-11T00:00:00Z", "timeMax": "2026-03-12T00:00:00Z", "items": [{"id": "primary"}]}'
```

## Drive

```bash
# List files
gws drive files list --params '{"pageSize": 10, "q": "name contains '\''report'\''"}'

# Upload file
gws drive +upload --file /path/to/file.pdf

# Download file
gws drive files get --params '{"fileId": "<id>", "alt": "media"}' --output /path/to/save

# Search files
gws drive files list --params '{"q": "mimeType='\''application/vnd.google-apps.spreadsheet'\'' and name contains '\''sales'\''", "pageSize": 20}'
```

## Sheets

```bash
# Read spreadsheet
gws sheets spreadsheets get --params '{"spreadsheetId": "<id>"}'

# Read range
gws sheets spreadsheets values get --params '{"spreadsheetId": "<id>", "range": "Sheet1!A1:D10"}'

# Write range
gws sheets spreadsheets values update --params '{"spreadsheetId": "<id>", "range": "Sheet1!A1", "valueInputOption": "USER_ENTERED"}' --json '{"values": [["A", "B"], ["1", "2"]]}'
```

## Tasks

```bash
# List task lists
gws tasks tasklists list

# List tasks in a list
gws tasks tasks list --params '{"tasklist": "<tasklistId>"}'

# Create task
gws tasks tasks insert --params '{"tasklist": "<tasklistId>"}' --json '{"title": "New task", "due": "2026-03-15T00:00:00Z"}'
```

## Useful Flags

- `--format table` -- human-readable table output
- `--format csv` -- CSV output for processing
- `--page-all` -- auto-paginate results (NDJSON, one page per line)
- `--page-limit <N>` -- max pages (default 10)
- `--dry-run` -- validate without sending
- `gws schema <service.resource.method>` -- show API schema for any endpoint

## Error Handling

- Auth expired: run `gws auth login` interactively to re-authenticate
- 403 errors: check if the API is enabled in the GCP project
- Rate limits: use `--page-delay` flag for bulk operations

## Security

- Authenticated as stephenhamilton@covertauto.com
- Never expose OAuth tokens in output
- Confirm before sending emails or modifying shared resources
