# OpenClaw Architecture: Three-Layer Model

> Understanding how OpenClaw works under the hood

## The Three Layers

OpenClaw follows a three-layer architecture that separates concerns and enables clean multi-agent collaboration:

```
┌─────────────────────────────────────────┐
│  Layer 3: Session (对话线程)            │  ← Ephemeral, per-task context
├─────────────────────────────────────────┤
│  Layer 2: Agent (工作区)               │  ← Persistent identity & memory  
├─────────────────────────────────────────┤
│  Layer 1: Global (全局配置)            │  ← System-wide settings
└─────────────────────────────────────────┘
```

---

## Layer 1: Global Configuration (全局层)

This is your `openclaw.json` - the system-wide settings that apply to all agents.

### Key Settings

```json
{
  "gateway": {
    "port": 19889,
    "host": "0.0.0.0"
  },
  "models": {
    "default": "openai/gpt-4o",
    "fallbacks": ["anthropic/claude-3-5-sonnet-20241002"]
  },
  "tools": {
    "agentToAgent": {
      "enabled": true,
      "allow": ["main", "code-agent", "news-agent"]
    },
    "sessions": {
      "visibility": "self"
    }
  }
}
```

### What Lives Here

| Config | Purpose |
|--------|---------|
| `openclaw.json` | Main gateway config |
| `~/.openclaw/agents/<agentId>/agent/` | Per-agent physical configs |
| `auth-profiles.json` | API keys, credentials |
| `models.json` | Model definitions |

---

## Layer 2: Agent Workspace (Agent层)

Each agent gets their own "brain" - a workspace directory where their identity, memory, and instructions live.

```
~/.openclaw/workspace-<name>/
├── AGENTS.md       # Behavior rules, workflows
├── SOUL.md        # Identity & personality
├── IDENTITY.md    # Name, emoji, avatar
├── USER.md        # Your preferences
├── TOOLS.md       # Local tool configs
├── HEARTBEAT.md  # Scheduled tasks
├── MEMORY.md     # Long-term memory
└── skills/       # Installed skills
```

### Files Explained

| File | Purpose | Example |
|------|---------|---------|
| `SOUL.md` | Agent's personality | "I'm a meticulous coder who prefers clean, tested code" |
| `AGENTS.md` | Team rules & routing | "Route coding tasks to code-agent" |
| `IDENTITY.md` | Name & emoji | "code-agent 🤖" |
| `USER.md` | Your preferences | "Prefer Python over JavaScript" |
| `HEARTBEAT.md` | Periodic tasks | "Check GitHub daily at 9am" |

---

## Layer 3: Session (Session层)

Ephemeral context - created per conversation, discarded after.

### Session Properties

- **Per-task**: New session = fresh context
- **Isolated**: Each agent only sees their own sessions by default
- **Flexible**: Can share via `sessions_spawn` or `sessions_history`

```python
# Session lifecycle
user message → new session created → agent processes → response delivered → session archived
```

---

## How Agents Communicate

### Method 1: Fixed Division (固定分工)

Each agent has a fixed role. Main dispatches to the right specialist.

```
User → Main → "Write code" → Code Agent → Return code → Main → User
```

### Method 2: Sub-Agent (临时子代理)

Main spawns a temporary agent for a specific task.

```python
# Main spawns a sub-agent for one-off task
sessions_spawn(
  agentId="temp-coder",
  task="Fix this bug in 10 lines",
  runtime="subagent"
)
```

### Communication Protocol

| Tool | Use |
|------|-----|
| `sessions_send` | Call another agent directly |
| `sessions_spawn` | Create temporary sub-agent |
| `sessions_history` | Read past conversations |
| `sessions_list` | See all agent's sessions |

---

## Directory Structure Deep Dive

```
~/.openclaw/
├── openclaw.json              # Global config
├── agents/
│   ├── main/
│   │   └── agent/
│   │       ├── auth-profiles.json
│   │       └── models.json
│   ├── code-agent/
│   │   └── agent/
│   │       ├── auth-profiles.json
│   │       └── models.json
│   └── news-agent/
│       └── agent/
│           ├── auth-profiles.json
│           └── models.json
├── workspace-main/           # Main's brain
│   ├── AGENTS.md
│   ├── SOUL.md
│   └── IDENTITY.md
├── workspace-code/          # Code Agent's brain
│   ├── AGENTS.md
│   ├── SOUL.md
│   └── IDENTITY.md
└── workspace-news/         # News Agent's brain
    ├── AGENTS.md
    ├── SOUL.md
    └── IDENTITY.md
```

---

## Key Concepts

### 1. agentId
Unique identifier (lowercase!) for each agent. Used for routing.

```json
{
  "agentId": "code-agent"  // ✓ correct
  "agentId": "CodeAgent"  // ✗ will cause routing issues
}
```

### 2. Workspace Separation
Never share workspaces between agents - causes context pollution!

```bash
# ✗ BAD - shared workspace
workspace: ~/.openclaw/workspace-shared/

# ✓ GOOD - separate workspaces  
workspace: ~/.openclaw/workspace-code/
workspace: ~/.openclaw/workspace-news/
```

### 3. Tool Permissions
Control what each agent can do. **Deny > Allow** priority.

```json
{
  "id": "news-agent",
  "tools": {
    "allow": ["sessions_list", "sessions_send", "read"],
    "deny": ["write", "edit", "exec", "bash"]
  }
}
```

---

## Next Steps

Now that you understand the architecture:
1. [SETUP.md](SETUP.md) - Set up your first agent
2. [AGENTS.md](AGENTS.md) - Configure your team
3. [WORKFLOWS.md](WORKFLOWS.md) - Define collaboration patterns