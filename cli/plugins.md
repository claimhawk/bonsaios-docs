---
summary: "CLI reference for `bonsaios plugins` (list, install, enable/disable, doctor)"
read_when:
  - You want to install or manage in-process Gateway plugins
  - You want to debug plugin load failures
title: "plugins"
---

# `bonsaios plugins`

Manage Gateway plugins/extensions (loaded in-process).

Related:

- Plugin system: [Plugins](/plugin)
- Plugin manifest + schema: [Plugin manifest](/plugins/manifest)
- Security hardening: [Security](/gateway/security)

## Commands

```bash
bonsaios plugins list
bonsaios plugins info <id>
bonsaios plugins enable <id>
bonsaios plugins disable <id>
bonsaios plugins doctor
bonsaios plugins update <id>
bonsaios plugins update --all
```

Bundled plugins ship with BonsaiOS but start disabled. Use `plugins enable` to
activate them.

All plugins must ship a `bonsaios.plugin.json` file with an inline JSON Schema
(`configSchema`, even if empty). Missing/invalid manifests or schemas prevent
the plugin from loading and fail config validation.

### Install

```bash
bonsaios plugins install <path-or-spec>
```

Security note: treat plugin installs like running code. Prefer pinned versions.

Supported archives: `.zip`, `.tgz`, `.tar.gz`, `.tar`.

Use `--link` to avoid copying a local directory (adds to `plugins.load.paths`):

```bash
bonsaios plugins install -l ./my-plugin
```

### Update

```bash
bonsaios plugins update <id>
bonsaios plugins update --all
bonsaios plugins update <id> --dry-run
```

Updates only apply to plugins installed from npm (tracked in `plugins.installs`).
