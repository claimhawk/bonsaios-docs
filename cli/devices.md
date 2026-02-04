---
summary: "CLI reference for `bonsaios devices` (device pairing + token rotation/revocation)"
read_when:
  - You are approving device pairing requests
  - You need to rotate or revoke device tokens
title: "devices"
---

# `bonsaios devices`

Manage device pairing requests and device-scoped tokens.

## Commands

### `bonsaios devices list`

List pending pairing requests and paired devices.

```
bonsaios devices list
bonsaios devices list --json
```

### `bonsaios devices approve <requestId>`

Approve a pending device pairing request.

```
bonsaios devices approve <requestId>
```

### `bonsaios devices reject <requestId>`

Reject a pending device pairing request.

```
bonsaios devices reject <requestId>
```

### `bonsaios devices rotate --device <id> --role <role> [--scope <scope...>]`

Rotate a device token for a specific role (optionally updating scopes).

```
bonsaios devices rotate --device <deviceId> --role operator --scope operator.read --scope operator.write
```

### `bonsaios devices revoke --device <id> --role <role>`

Revoke a device token for a specific role.

```
bonsaios devices revoke --device <deviceId> --role node
```

## Common options

- `--url <url>`: Gateway WebSocket URL (defaults to `gateway.remote.url` when configured).
- `--token <token>`: Gateway token (if required).
- `--password <password>`: Gateway password (password auth).
- `--timeout <ms>`: RPC timeout.
- `--json`: JSON output (recommended for scripting).

## Notes

- Token rotation returns a new token (sensitive). Treat it like a secret.
- These commands require `operator.pairing` (or `operator.admin`) scope.
