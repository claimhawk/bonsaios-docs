---
summary: "Top-level overview of BonsaiOS — the agentic operating system for building projects"
read_when:
  - Introducing BonsaiOS to newcomers
title: "BonsaiOS"
---

# BonsaiOS 🌳

> _Stop chatting. Start shipping._

<p align="center">
    <img
        src="/assets/bonsai-logo.png"
        alt="BonsaiOS"
        width="120"
    />
</p>

<p align="center">
  <strong>The agentic operating system for building projects.</strong><br />
  Kanban boards. Autonomous agents. Real deployments.
</p>

<p align="center">
  <a href="https://github.com/claimhawk/bonsaios-docs">GitHub</a> ·
  <a href="https://github.com/claimhawk/bonsaios-docs/releases">Releases</a> ·
  <a href="/">Docs</a> ·
  <a href="/start/getting-started">Get Started</a>
</p>

## What is BonsaiOS?

BonsaiOS is an agentic operating system that replaces endless chat threads with **project-based kanban boards**. Instead of talking _at_ your AI agent in an infinite conversation, you communicate through **stories** on a board — just like a real engineering team.

Write a story. The agent researches it, builds a plan, and presents it for your approval. Accept the plan, and the agent writes the code, runs the tests, and deploys — all autonomously.

**It's OpenClaw meets project management.**

## How it works

```
You write a story on the board
        │
        ▼
  ┌───────────────────────────┐
  │     Backlog               │  Agent picks up the story
  │     → Research phase      │  Explores codebase, produces research doc
  │     → You review          │  Accept or reject with feedback
  └───────────┬───────────────┘
              │
              ▼
  ┌───────────────────────────┐
  │     In Progress           │  Agent writes code in isolated worktree
  │     → Implementation      │  Uses git branch, runs tests
  │     → Moves to Review     │  Signals completion
  └───────────┬───────────────┘
              │
              ▼
  ┌───────────────────────────┐
  │     Review                │  You verify the work
  │     → Accept → Done       │  Ship it
  │     → Reject → Rework     │  Agent fixes based on your feedback
  └───────────────────────────┘
```

Each story lives in its own **isolated context** — its own git branch, worktree, research documents, and implementation logs. No more scrolling through thousands of messages to find what the agent did.

## Key concepts

- **Stories, not chats** — Define work as tickets on a kanban board. Each story has intent, acceptance criteria, artifacts, and comments.
- **Autonomous agents** — Agents pick up stories, research the codebase, plan the approach, and implement the solution without constant hand-holding.
- **Human-gated transitions** — You stay in control. Agents can't move work forward without your approval at key checkpoints.
- **Isolated contexts** — Every story gets its own git branch and worktree. No cross-contamination between tasks.
- **Artifacts** — Research plans, implementation logs, and other documents are attached to stories as reviewable artifacts.

## The dashboard

The dashboard is the control center for your projects. It includes the kanban board, chat, agent sessions, cron jobs, and configuration.

Local default: http://127.0.0.1:18789/

Click any story card to see its full details — intent, artifacts, agent session logs, and comments. Drag cards between columns to manage workflow.

## Quick start

Runtime requirement: **Node ≥ 22**.

```bash
# Install globally
npm install -g bonsaios@latest

# Onboard and start the gateway
bonsaios onboard --install-daemon

# Open the dashboard
open http://127.0.0.1:18789/
```

The gateway runs as a background service. The dashboard opens in your browser where you can create your first project board and start writing stories.

- **New install from zero:** [Getting Started](/start/getting-started)
- **Guided setup (recommended):** [Wizard](/start/wizard) (`bonsaios onboard`)

## Built on OpenClaw

BonsaiOS is built on top of [OpenClaw](https://github.com/openclaw/openclaw), inheriting its messaging infrastructure. All OpenClaw features are available — WhatsApp, Telegram, Discord, iMessage, and plugin channels. BonsaiOS adds the project management layer on top.

### Messaging channels

- 📱 **WhatsApp** — Via WhatsApp Web / Baileys
- ✈️ **Telegram** — DMs + groups via Bot API
- 🎮 **Discord** — DMs + guild channels
- 💬 **iMessage** — macOS local integration
- 🧩 **Plugins** — Mattermost, Matrix, and more

### Platform support

- 🖥️ **macOS app** — Menu bar companion with voice wake
- 📱 **iOS & Android** — Mobile nodes with Canvas surfaces
- 🌐 **WebChat** — Browser-based dashboard and chat
- 🔧 **CLI** — Full command-line control (`bonsaios ...`)

## Architecture

```
  ┌───────────────────────────┐
  │       Dashboard           │  Board + Chat + Config
  │     (Browser UI)          │
  └───────────┬───────────────┘
              │ WebSocket
              ▼
  ┌───────────────────────────┐
  │          Gateway          │  ws://127.0.0.1:18789
  │                           │
  │  Board ←→ Cron ←→ Agents  │  5-minute orchestrator cycle
  │  Queue ←→ Workers          │  Isolated agent sessions
  │                           │
  │  Channels (WhatsApp, etc) │  Messaging bridge
  └───────────────────────────┘
              │
              ├─ Pi agent (RPC)
              ├─ CLI
              ├─ Mobile nodes
              └─ Canvas host
```

The **Gateway** is the single long-running process that manages everything — the board, agent orchestration, channel connections, and the WebSocket control plane.

## Network model

- **One Gateway per host (recommended)**: owns the WhatsApp Web session and board state. For isolation, run multiple gateways with separate profiles and ports.
- **Loopback-first**: Gateway defaults to `ws://127.0.0.1:18789` with token auth.
- **Remote access**: SSH tunnel or Tailnet; see [Remote access](/gateway/remote).

## From source (development)

```bash
git clone https://github.com/claimhawk/bonsaios-docs.git
cd bonsaios
pnpm install
pnpm ui:build
pnpm build
bonsaios onboard --install-daemon
```
