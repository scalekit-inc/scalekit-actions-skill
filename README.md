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

## Usage

In Claude Code, use the `/test-tool` skill:

```
/test-tool generate-link <connection_name> <identifier>
/test-tool execute-tool <tool_name> <connection_name> <identifier> '<tool_input_json>'
/test-tool get-tool --tool-name <tool_name>
/test-tool get-tool --page-size 10
```

### Examples

```
/test-tool generate-link googledocs-abc123 alice
/test-tool execute-tool googledocs_create_document googledocs-abc123 alice '{"title": "My Doc"}'
/test-tool get-tool --tool-name slack_send_message
```
