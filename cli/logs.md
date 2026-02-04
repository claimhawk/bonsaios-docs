---
summary: "CLI reference for `bonsaios logs` (tail gateway logs via RPC)"
read_when:
  - You need to tail Gateway logs remotely (without SSH)
  - You want JSON log lines for tooling
title: "logs"
---

# `bonsaios logs`

Tail Gateway file logs over RPC (works in remote mode).

Related:

- Logging overview: [Logging](/logging)

## Examples

```bash
bonsaios logs
bonsaios logs --follow
bonsaios logs --json
bonsaios logs --limit 500
```
