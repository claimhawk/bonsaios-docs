---
summary: "Run the ACP bridge for IDE integrations"
read_when:
  - Setting up ACP-based IDE integrations
  - Debugging ACP session routing to the Gateway
title: "acp"
---

# acp

Run the ACP (Agent Client Protocol) bridge that talks to a BonsaiOS Gateway.

This command speaks ACP over stdio for IDEs and forwards prompts to the Gateway
over WebSocket. It keeps ACP sessions mapped to Gateway session keys.

## Usage

```bash
bonsaios acp

# Remote Gateway
bonsaios acp --url wss://gateway-host:18789 --token <token>

# Attach to an existing session key
bonsaios acp --session agent:main:main

# Attach by label (must already exist)
bonsaios acp --session-label "support inbox"

# Reset the session key before the first prompt
bonsaios acp --session agent:main:main --reset-session
```

## ACP client (debug)

Use the built-in ACP client to sanity-check the bridge without an IDE.
It spawns the ACP bridge and lets you type prompts interactively.

```bash
bonsaios acp client

# Point the spawned bridge at a remote Gateway
bonsaios acp client --server-args --url wss://gateway-host:18789 --token <token>

# Override the server command (default: bonsaios)
bonsaios acp client --server "node" --server-args bonsaios.mjs acp --url ws://127.0.0.1:19001
```

## How to use this

Use ACP when an IDE (or other client) speaks Agent Client Protocol and you want
it to drive a BonsaiOS Gateway session.

1. Ensure the Gateway is running (local or remote).
2. Configure the Gateway target (config or flags).
3. Point your IDE to run `bonsaios acp` over stdio.

Example config (persisted):

```bash
bonsaios config set gateway.remote.url wss://gateway-host:18789
bonsaios config set gateway.remote.token <token>
```

Example direct run (no config write):

```bash
bonsaios acp --url wss://gateway-host:18789 --token <token>
```

## Selecting agents

ACP does not pick agents directly. It routes by the Gateway session key.

Use agent-scoped session keys to target a specific agent:

```bash
bonsaios acp --session agent:main:main
bonsaios acp --session agent:design:main
bonsaios acp --session agent:qa:bug-123
```

Each ACP session maps to a single Gateway session key. One agent can have many
sessions; ACP defaults to an isolated `acp:<uuid>` session unless you override
the key or label.

## Zed editor setup

Add a custom ACP agent in `~/.config/zed/settings.json` (or use Zed’s Settings UI):

```json
{
  "agent_servers": {
    "BonsaiOS ACP": {
      "type": "custom",
      "command": "bonsaios",
      "args": ["acp"],
      "env": {}
    }
  }
}
```

To target a specific Gateway or agent:

```json
{
  "agent_servers": {
    "BonsaiOS ACP": {
      "type": "custom",
      "command": "bonsaios",
      "args": [
        "acp",
        "--url",
        "wss://gateway-host:18789",
        "--token",
        "<token>",
        "--session",
        "agent:design:main"
      ],
      "env": {}
    }
  }
}
```

In Zed, open the Agent panel and select “BonsaiOS ACP” to start a thread.

## Session mapping

By default, ACP sessions get an isolated Gateway session key with an `acp:` prefix.
To reuse a known session, pass a session key or label:

- `--session <key>`: use a specific Gateway session key.
- `--session-label <label>`: resolve an existing session by label.
- `--reset-session`: mint a fresh session id for that key (same key, new transcript).

If your ACP client supports metadata, you can override per session:

```json
{
  "_meta": {
    "sessionKey": "agent:main:main",
    "sessionLabel": "support inbox",
    "resetSession": true
  }
}
```

Learn more about session keys at [/concepts/session](/concepts/session).

## Options

- `--url <url>`: Gateway WebSocket URL (defaults to gateway.remote.url when configured).
- `--token <token>`: Gateway auth token.
- `--password <password>`: Gateway auth password.
- `--session <key>`: default session key.
- `--session-label <label>`: default session label to resolve.
- `--require-existing`: fail if the session key/label does not exist.
- `--reset-session`: reset the session key before first use.
- `--no-prefix-cwd`: do not prefix prompts with the working directory.
- `--verbose, -v`: verbose logging to stderr.

### `acp client` options

- `--cwd <dir>`: working directory for the ACP session.
- `--server <command>`: ACP server command (default: `bonsaios`).
- `--server-args <args...>`: extra arguments passed to the ACP server.
- `--server-verbose`: enable verbose logging on the ACP server.
- `--verbose, -v`: verbose client logging.
