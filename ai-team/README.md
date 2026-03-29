# OpenClaw AI Team Guide

> 构建你的专属AI团队 - Build a Multi-Agent AI Team with OpenClaw

Inspired by **木子不写代码**'s viral tutorial (6,215+ likes). This comprehensive guide teaches you how to build an AI team where one person equals a team.

---

## 🌟 Overview

### What You'll Build

A 3-6 agent team where:
- 🧠 **Main Agent** - Task dispatcher / team manager
- 💻 **Code Agent** - Technical expert / developer  
- 📰 **News Agent** - Information gatherer / research assistant
- (Optional) **Writing Agent** - Content creator
- (Optional) **Review Agent** - Quality assurance

### Why Multi-Agent?

| Problem | Solution |
|---------|----------|
| Single agent gets confused with complex tasks | Split into specialized agents |
| Long context = AI "forgets" | Each agent has focused context |
| Can't do multiple things at once | Agents work in parallel |
| One agent = bottleneck | Team scales with task complexity |

---

## 🚀 Quick Start

### Prerequisites

1. OpenClaw installed (`npm install -g openclaw`)
2. API keys (OpenAI, Claude, or other supported models)
3. At least one messaging channel (Telegram, Discord, etc.)

### 5-Minute Setup

```bash
# 1. Initialize OpenClaw
openclaw onboard

# 2. Create your first agent
openclaw agents add code-agent

# 3. Configure the team in openclaw.json
# (See configuration section below)
```

---

## 📐 Three-Layer Architecture

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

### Layer 1: Global Configuration (全局层)

This is your `openclaw.json` - the system-wide settings that apply to all agents.

**Key Settings:**

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

**What Lives Here:**

| Config | Purpose |
|--------|---------|
| `openclaw.json` | Main gateway config |
| `~/.openclaw/agents/<agentId>/agent/` | Per-agent physical configs |
| `auth-profiles.json` | API keys, credentials |
| `models.json` | Model definitions |

### Layer 2: Agent Workspace (Agent层)

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

**Files Explained:**

| File | Purpose | Example |
|------|---------|---------|
| `SOUL.md` | Agent's personality | "I'm a meticulous coder who prefers clean, tested code" |
| `AGENTS.md` | Team rules & routing | "Route coding tasks to code-agent" |
| `IDENTITY.md` | Name & emoji | "code-agent 🤖" |
| `USER.md` | Your preferences | "Prefer Python over JavaScript" |
| `HEARTBEAT.md` | Periodic tasks | "Check GitHub daily at 9am" |

### Layer 3: Session (Session层)

Ephemeral context - created per conversation, discarded after.

**Session Properties:**
- **Per-task**: New session = fresh context
- **Isolated**: Each agent only sees their own sessions by default
- **Flexible**: Can share via `sessions_spawn` or `sessions_history`

---

## ⚙️ Configuration

### Step 1: Install OpenClaw

```bash
# Install globally via npm
npm install -g openclaw

# Verify installation
openclaw --version
```

### Step 2: Initial Setup

```bash
# Start the onboarding wizard
openclaw onboard
```

This wizard will guide you through:
1. Creating your first agent (main)
2. Configuring your LLM API
3. Setting up a messaging channel
4. Testing the connection

### Step 3: Configure Your LLM

Edit `~/.openclaw/openclaw.json`:

```json
{
  "models": {
    "default": "openai/gpt-4o",
    "fallbacks": [
      "anthropic/claude-3-5-sonnet-20241002",
      "minimax/MiniMax-M2.5"
    ]
  },
  "authProfiles": {
    "openai": {
      "type": "openai",
      "apiKey": "sk-..."
    }
  }
}
```

**Recommended Models by Use Case:**

| Use Case | Primary Model | Fallback |
|---------|--------------|---------|
| General chat | gpt-4o | Claude 3.5 |
| Code writing | claude-3-5-sonnet | gpt-4o |
| Fast responses | Kimi K2.5 | MiniMax M2.5 |
| Long context | gpt-4-turbo | Claude 3.5 |

### Step 4: Add Your Agents

**Option A: Via CLI**

```bash
# Add a code specialist agent
openclaw agents add code-agent
# Follow prompts for name, description, workspace

# Add a news/research agent  
openclaw agents add news-agent
```

**Option B: Via Configuration**

Add directly to `openclaw.json`:

```json
{
  "agents": {
    "main": {
      "enabled": true,
      "workspace": "workspace-main"
    },
    "code-agent": {
      "enabled": true,
      "workspace": "workspace-code"
    },
    "news-agent": {
      "enabled": true,
      "workspace": "workspace-news"
    }
  }
}
```

### Step 5: Create Agent Workspaces

**Main Agent Workspace:**

```bash
mkdir -p ~/.openclaw/workspace-main
```

Create `IDENTITY.md`:
```markdown
# IDENTITY.md - Agent Identity
- **Name:** main
- **Role:** Team lead / task dispatcher
- **Emoji:** 👋
```

Create `SOUL.md`:
```markdown
# SOUL.md - Agent Personality
I am the team's coordinator. I:
- Receive your requests
- Break down complex tasks
- Dispatch to the right specialist agent
- Aggregate results and present them to you

I don't write code or fetch news directly - I delegate to specialists.
```

Create `AGENTS.md`:
```markdown
# AGENTS.md - Team Rules

## Team Members
- **code-agent**: Code writing & review
- **news-agent**: Research & information gathering

## Task Routing
| Request Type | Route To |
|-------------|---------|
| Write code | code-agent |
| Fix bug | code-agent |
| Find info | news-agent |
| Summarize | news-agent |
| General chat | handle myself |

## Collaboration
- Use sessions_send to invoke other agents
- Wait for results before responding
- Never hallucinate - ask if unsure
```

**Code Agent Workspace:**

```bash
mkdir -p ~/.openclaw/workspace-code
```

Create `IDENTITY.md`:
```markdown
# IDENTITY.md - Code Agent
- **Name:** code-agent
- **Role:** Technical specialist
- **Emoji:** 🤖
```

Create `SOUL.md`:
```markdown
# SOUL.md - Code Agent
I am a meticulous code reviewer. I:
- Write clean, well-commented code
- Prefer Python, TypeScript
- Always test my code before delivering
- Explain my choices clearly
```

**News Agent Workspace:**

```bash
mkdir -p ~/.openclaw/workspace-news
```

Create `IDENTITY.md`:
```markdown
# IDENTITY.md - News Agent
- **Name:** news-agent
- **Role:** Information specialist  
- **Emoji:** 📰
```

Create `SOUL.md`:
```markdown
# SOUL.md - News Agent
I find and summarize information. I:
- Search the web for relevant info
- Summarize in bullet points
- Filter out noise and ads
- Cite sources
```

### Step 6: Configure Tool Permissions

Edit `openclaw.json` to control inter-agent communication:

```json
{
  "tools": {
    "agentToAgent": {
      "enabled": true,
      "allow": ["main", "code-agent", "news-agent"]
    },
    "sessions": {
      "visibility": "self"
    }
  },
  "security": {
    "deny": []
  }
}
```

### Step 7: Start the Gateway

```bash
# Start the gateway
openclaw gateway start

# Or run in background
openclaw gateway start --background
```

**Check Status:**

```bash
openclaw gateway status
```

Expected output:
```
Gateway: running on port 19889
Agents: main, code-agent, news-agent
Channel: telegram (connected)
```

---

## 🔄 Workflows

### Pattern 1: Simple Relay

```
User → Main → Specialist → Result → Main → User
```

**Use case**: One-off tasks that don't need parallelism

```
User: "Write a Python function"

Main receives request → 
  Main calls code-agent → 
  Code agent writes function → 
  Code returns to Main → 
  Main delivers to User
```

### Pattern 2: Parallel Execution

```
User → Main → [Agent A] ──┬──→ Main → Result
                         [Agent B] ──↗
```

**Use case**: Multiple independent tasks

```
User: "Search for AI news AND write code"

Main receives request →
  Main calls code-agent (async) →
  Main calls news-agent (async) →
  Both run in parallel →
  Results combined by Main →
  Delivered to User
```

### Pattern 3: Chain

```
User → Main → Agent A → Agent B → Agent C → Result → Main → User
```

**Use case**: Multi-step workflows

```
User: "Research topic, write article, publish"

Main receives request →
  Main calls news-agent (research) →
  News returns summary →
  Main calls writer-agent (write) →
  Writer returns draft →
  Main calls review-agent (review) →
  Review returns final →
  Main delivers to User
```

### Calling Another Agent

```python
# Using sessions_send (fire and forget, waits for response)
sessions_send(
    agentId="code-agent",
    task="Write a Python function to calculate fibonacci",
    timeout=120
)
```

```python
# Using sessions_spawn (create sub-agent)
sessions_spawn(
    agentId="temp-coder",
    task="Fix this bug in 10 lines",
    runtime="subagent"
)
```

---

## 🔐 Security & Permissions

### Agent Communication

```json
{
  "tools": {
    "agentToAgent": {
      "enabled": true,
      "allow": ["main", "code-agent", "news-agent"]
    }
  }
}
```

### Tool Permissions

Control what each agent can do. **Deny > Allow** priority.

```json
{
  "id": "news-agent",
  "tools": {
    "allow": ["sessions_list", "sessions_send", "read"],
    "deny": ["write", "edit", "exec", "apply_patch", "bash"]
  }
}
```

### Session Visibility

```json
{
  "tools": {
    "sessions": {
      "visibility": "self"  // or "all" for full visibility
    }
  }
}
```

---

## 🧪 Testing Your Team

### Test 1: Main Agent Responds

Send a message to your main agent:
```
Hi! What can you do?
```

Expected: Main explains their role and team.

### Test 2: Code Agent Works

```
Write a Python function to calculate fibonacci numbers
```

Expected: Main dispatches → Code Agent writes → Result returned.

### Test 3: News Agent Works

```
What's the latest news about AI agents?
```

Expected: Main dispatches → News Agent researches → Summary returned.

---

## 📋 Directory Structure

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

## 🔧 Troubleshooting

### "Agent not found"

Check that agentId is lowercase in all configs:
```json
{
  "agentId": "code-agent"  // ✓ lowercase
}
```

### "Tool not available"

Ensure tool permissions are set:
```json
{
  "tools": {
    "sessions_send": { "enabled": true }
  }
}
```

### "API key invalid"

Check your auth-profiles.json has valid keys.

---

## 🎯 Use Cases

### 1. Research Team
- News Agent scrapes → Code Agent processes → Main Agent summarizes → You get a daily brief

### 2. Development Team  
- You describe feature → Code Agent writes → Review Agent checks → Main Agent reports status

### 3. Content Team
- Main Agent gets topic → Writing Agent creates → Review Agent edits → Ready to publish

---

## 🔗 Original Sources

- **YouTube**: [我用 OpenClaw 搭了一支 AI 团队](https://www.youtube.com/watch?v=d25fcWem0ME)
- **Bilibili**: [BV1GNcXz9E91](https://www.bilibili.com/video/BV1GNcXz9E91/)
- **Blog (详细)**: [OpenClaw多智能体机制](https://www.cnblogs.com/softlin/p/19694335)

---

## 🤝 Contributing

Found something missing or have improvements? Open a PR!

---

## 📝 License

MIT License - Built with love by carpetbot