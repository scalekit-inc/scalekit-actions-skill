# scalekit-actions-skill

A Claude Code skill for testing and building workflows with Scalekit Connect tools.

## What it does

Adds a `/test-tool` command to Claude Code that lets you:
- Generate authorization links for connected accounts
- Execute Scalekit tools on behalf of users
- Fetch tool metadata and schemas
- Build and test multi-step workflows using the Scalekit Connect SDK

## Setup

### 1. Install dependencies

```bash
pip install scalekit-sdk python-dotenv
# or
uv add scalekit-sdk python-dotenv
```

### 2. Configure credentials

```bash
cp .env.example .env
```

Fill in your Scalekit environment credentials in `.env`:

```
TOOL_ENV_URL=https://your-env.scalekit.dev
TOOL_CLIENT_ID=skc_your_client_id
TOOL_CLIENT_SECRET=your_client_secret
```

### 3. Open Claude Code in this directory

The `/test-tool` skill is automatically available once you open Claude Code in this repo.

---

## Usage

### generate-link

Check if a user's connected account is active and generate an authorization link if needed.

```
/test-tool generate-link <connection_name> <identifier>
```

**Example — account already active:**
```
/test-tool generate-link googledrive-vjmIFg7f claude
```

```
🚀 Scalekit Connect CLI
   Env URL: https://kindle-dev.scalekit.cloud
   Client ID: skc_5381...
   Operation: Generate Auth Link

   Connection: googledrive-vjmIFg7f
   Identifier: claude

   Connected Account ID: ca_113453695454151836
   Status: ACTIVE

✅ googledrive-vjmIFg7f is already connected and active!
```

**Example — account not yet authorized:**
```
/test-tool generate-link googledocs Jiten
```

```
🚀 Scalekit Connect CLI
   Env URL: https://kindle-dev.scalekit.cloud
   Operation: Generate Auth Link

   Connection: googledocs
   Identifier: Jiten
   Status: PENDING_AUTH

⚠️  googledocs is not connected (status: PENDING_AUTH)

🔗 Click the link to authorize googledocs:
   https://your-env.scalekit.dev/magicLink/fa138abe-b4a6-4ac7-9d9e-9d813e770eb2_o
```

---

### execute-tool

Execute a Scalekit tool on behalf of a user. If the account is not yet authorized, a magic link is generated automatically.

```
/test-tool execute-tool <tool_name> <connection_name> <identifier> '<tool_input_json>'
```

**Example — search Google Drive:**
```
/test-tool execute-tool googledrive_search_files googledrive-vjmIFg7f claude '{"query": "name contains '\''SDK TEST'\''", "page_size": 3}'
```

```
🚀 Scalekit Connect CLI
   Env URL: https://kindle-dev.scalekit.cloud
   Operation: Execute Tool

   Tool: googledrive_search_files
   Connection: googledrive-vjmIFg7f
   Identifier: claude
   Input: {
     "query": "name contains 'SDK TEST'",
     "page_size": 3
   }

   Connected Account ID: ca_113453695454151836
   Status: ACTIVE

🔧 Executing tool: googledrive_search_files

✅ Result:
{
  "files": [
    {
      "mimeType": "application/vnd.google-apps.document",
      "kind": "drive#file",
      "id": "1Q3eS7fplF5ZiT5lvbkmcbzrMVxV8QkqMT9avMAzy2LI",
      "name": "SDK TEST"
    }
  ],
  "kind": "drive#fileList",
  "incompleteSearch": false
}
```

**Parameters:**
| Parameter | Value |
|-----------|-------|
| operation | execute-tool |
| tool_name | googledrive_search_files |
| connection_name | googledrive-vjmIFg7f |
| identifier | claude |
| tool_input | `{"query": "name contains 'SDK TEST'", "page_size": 3}` |

---

### get-tool

Fetch tool metadata, input schema, and API details from the Scalekit tools registry.

```
/test-tool get-tool [--tool-name <name>] [--provider <provider>] [--page-size <n>] [--page-token <token>]
```

All flags are optional. Omitting `--tool-name` returns all tools (paginated).

**Example — fetch a specific tool schema:**
```
/test-tool get-tool --tool-name googledrive_search_files
```

```json
{
  "total_size": 1,
  "tool_names": ["googledrive_search_files"],
  "tools": [
    {
      "id": "tol_113451701398144156",
      "provider": "GOOGLEDRIVE",
      "definition": {
        "name": "googledrive_search_files",
        "display_name": "Search Drive Files",
        "description": "Search for files and folders in Google Drive using query filters like name, type, owner, and parent folder.",
        "input_schema": {
          "type": "object",
          "properties": {
            "query": {
              "description": "Drive search query string",
              "type": ["string", "null"],
              "display_properties": {
                "placeholder": "name contains 'report'",
                "help_link": "https://developers.google.com/drive/api/guides/search-files"
              }
            },
            "page_size": {
              "description": "Number of files to return per page",
              "type": ["integer", "null"],
              "default": 100
            },
            "order_by": {
              "description": "Sort order for results",
              "type": ["string", "null"],
              "display_properties": { "placeholder": "modifiedTime desc" }
            }
          }
        },
        "rest_api_info": {
          "method": "GET",
          "base_url": "https://www.googleapis.com",
          "path_template": "/drive/v3/files"
        }
      }
    }
  ]
}
```

**Example — list all tools (paginated):**
```
/test-tool get-tool --page-size 3
```

```json
{
  "next_page_token": ">:WyJ0b2xfMTEzNjczMDU5NDk5NTA3ODMwIl0=",
  "total_size": 189,
  "tool_names": [
    "todoist_task_create",
    "todoist_project_get",
    "todoist_project_update"
  ]
}
```

Use `next_page_token` to fetch the next page:
```
/test-tool get-tool --page-size 3 --page-token ">:WyJ0b2xfMTEzNjczMDU5NDk5NTA3ODMwIl0="
```

---

## Building workflows

This skill is designed to be used by Claude Code as a coding agent. Claude can chain multiple tool calls together to accomplish multi-step tasks. For example:

1. `/test-tool execute-tool googledocs_create_document ...` → get `document_id`
2. `/test-tool execute-tool googledocs_update_document ...` with `document_id` → insert text and formatting
3. `/test-tool execute-tool googledocs_read_document ...` → verify the content

Claude uses the output of each step as input to the next, building up a complete workflow interactively.
