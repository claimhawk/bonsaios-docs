---
summary: "CLI reference for `bonsaios agents` (list/add/delete/set identity)"
read_when:
  - You want multiple isolated agents (workspaces + routing + auth)
title: "agents"
---

# `bonsaios agents`

Manage isolated agents (workspaces + auth + routing).

Related:

- Multi-agent routing: [Multi-Agent Routing](/concepts/multi-agent)
- Agent workspace: [Agent workspace](/concepts/agent-workspace)

## Examples

```bash
bonsaios agents list
bonsaios agents add work --workspace ~/.bonsaios/workspace-work
bonsaios agents set-identity --workspace ~/.bonsaios/workspace --from-identity
bonsaios agents set-identity --agent main --avatar avatars/bonsaios.png
bonsaios agents delete work
```

## Identity files

Each agent workspace can include an `IDENTITY.md` at the workspace root:

- Example path: `~/.bonsaios/workspace/IDENTITY.md`
- `set-identity --from-identity` reads from the workspace root (or an explicit `--identity-file`)

Avatar paths resolve relative to the workspace root.

## Set identity

`set-identity` writes fields into `agents.list[].identity`:

- `name`
- `theme`
- `emoji`
- `avatar` (workspace-relative path, http(s) URL, or data URI)

Load from `IDENTITY.md`:

```bash
bonsaios agents set-identity --workspace ~/.bonsaios/workspace --from-identity
```

Override fields explicitly:

```bash
bonsaios agents set-identity --agent main --name "BonsaiOS" --emoji "🌳" --avatar avatars/bonsaios.png
```

Config sample:

```json5
{
  agents: {
    list: [
      {
        id: "main",
        identity: {
          name: "BonsaiOS",
          theme: "space bonsai",
          emoji: "🌳",
          avatar: "avatars/bonsaios.png",
        },
      },
    ],
  },
}
```
