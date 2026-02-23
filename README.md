# scalekit-actions-skill

Scalekit actions playground in your Claude Code.

Use this skill in Claude Code or other coding agents to quickly add Scalekit Connect tools — generate auth links, execute tools on behalf of users, and build multi-step workflows without leaving your editor.

## Get started

**1. Install the plugin**
```
/plugin marketplace add scalekit-inc/scalekit-actions-skill
/plugin install scalekit-actions-skill@scalekit-inc
```

**2. Add your credentials to `.env`**
```
TOOL_ENV_URL=https://your-env.scalekit.dev
TOOL_CLIENT_ID=skc_your_client_id
TOOL_CLIENT_SECRET=your_client_secret
```

**3. Just ask**

Open Claude Code and ask it to execute any Scalekit tool, generate an auth link, or build a workflow. The skill handles the rest.
