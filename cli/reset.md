---
summary: "CLI reference for `bonsaios reset` (reset local state/config)"
read_when:
  - You want to wipe local state while keeping the CLI installed
  - You want a dry-run of what would be removed
title: "reset"
---

# `bonsaios reset`

Reset local config/state (keeps the CLI installed).

```bash
bonsaios reset
bonsaios reset --dry-run
bonsaios reset --scope config+creds+sessions --yes --non-interactive
```
