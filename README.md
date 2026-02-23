# scalekit-actions-skill

A Claude Code skill for testing and building workflows with Scalekit Connect tools.

## Install

```
/plugin marketplace add scalekit-inc/scalekit-actions-skill
/plugin install scalekit-actions-skill@scalekit-inc
```

Add your credentials to `.env`:

```
TOOL_ENV_URL=https://your-env.scalekit.dev
TOOL_CLIENT_ID=skc_your_client_id
TOOL_CLIENT_SECRET=your_client_secret
```

---

## Examples

**Generate an auth link for a user:**
```
/test-tool generate-link googledrive-abc123 alice
```

**Execute a tool:**
```
/test-tool execute-tool googledrive_search_files googledrive-abc123 alice '{"query": "name contains '\''report'\''"}'
```

**Fetch a tool's schema:**
```
/test-tool get-tool --tool-name googledrive_search_files
```

**List all available tools:**
```
/test-tool get-tool --page-size 10
```
