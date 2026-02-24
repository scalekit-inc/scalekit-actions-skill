---
description: Test Scalekit tool execution — generate auth links, execute tools, or fetch tool metadata via the Connect SDK
argument-hint: <generate-link|execute-tool|get-tool> [args...]
allowed-tools: [Bash]
---

# Scalekit Tool Tester

Test Scalekit tools using the Connect SDK via `connect.py`.

**Arguments:** $ARGUMENTS

---

## Operations

### generate-link
Usage: `generate-link <connection_name> <identifier>`

Checks the connected account status and generates an authorization link if the account is not yet active.

### execute-tool
Usage: `execute-tool <tool_name> <connection_name> <identifier> <tool_input_json>`

Executes a tool on behalf of a user. Prompts for authorization if the account is not connected.

### get-tool
Usage: `get-tool [--tool-name <name>] [--provider <provider>] [--page-size <n>] [--page-token <token>]`

Fetches tool metadata from the Scalekit tools API and prints the raw JSON response.
All arguments are optional — omitting `--tool-name` returns all tools (paginated).

---

## Your task

Parse `$ARGUMENTS` to determine the operation and run the appropriate command using the Bash tool.

**Working directory:** Always run from the project root (where `connect.py` lives).

**How to invoke `connect.py`:** Check which runner is available on the current machine by running `which uv` — if found, prefix commands with `uv run python`; otherwise fall back to `python3` (or `python`). Do this check once before running any commands.

### Before running any operation — check for credentials

Check if `.env` exists in the working directory and contains all three required variables:
- `TOOL_ENV_URL`
- `TOOL_CLIENT_ID`
- `TOOL_CLIENT_SECRET`

If `.env` is missing or any of these variables are absent or empty, ask the developer for them before proceeding:

> "To connect to Scalekit, I need your environment details. Please provide:
> - **Environment URL** (e.g. `https://your-env.scalekit.dev`)
> - **Client ID** (e.g. `skc_...`)
> - **Client Secret**"

Once provided, create or update `.env` with the values:

```
TOOL_ENV_URL=<value>
TOOL_CLIENT_ID=<value>
TOOL_CLIENT_SECRET=<value>
```

Then proceed with the operation.

### If operation is `generate-link`:

Extract `connection_name` and `identifier` from arguments, then run:
```
<runner> connect.py --generate-link --connection-name <connection_name> --identifier <identifier>
```

### If operation is `execute-tool`:

Extract `tool_name`, `connection_name`, `identifier`, and `tool_input` (JSON) from arguments, then run:
```
<runner> connect.py --execute-tool --tool-name <tool_name> --connection-name <connection_name> --identifier <identifier> --tool-input '<tool_input_json>'
```

If `tool_input` is not provided in the arguments, ask the user what inputs the tool needs before running.

### If operation is `get-tool`:

Extract any of the optional flags from the arguments and run:
```
<runner> connect.py --get-tool [--tool-name <name>] [--provider <provider>] [--page-size <n>] [--page-token <token>]
```

Only include flags that were provided in the arguments. All flags are optional.

### After running:

Show the output clearly. If a magic link was generated, highlight it so the user can click it easily.

Then display the exact command that was run in a code block (with all values filled in, no placeholders). Label it **"Command run:"** — this is used for documentation.

Then display the parameters used in a structured block. Label it **"Parameters:"** — list each param as a key-value pair. For example:

**Parameters:**
| Parameter | Value |
|-----------|-------|
| operation | execute-tool |
| tool_name | googledocs_create_document |
| connection_name | googledocs-ci3BvjJC |
| identifier | Jiten |
| tool_input | `{"title": "My Doc"}` |

Always include all parameters that were passed, even optional ones. Omit parameters that were not used.

#### Workflow context:

This skill is used by Claude as a coding agent to test tools while helping developers build workflows. After running a tool:
- Use the result to inform the next step in the workflow
- Chain multiple tool calls together to accomplish a goal (e.g. create doc → insert text → update formatting → read doc)
- Use IDs and values from previous responses as inputs to subsequent calls
- No need to output Python code snippets — focus on executing and using results to drive the workflow forward
