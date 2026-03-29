# OpenClaw Setup Guide: Zero to AI Team

> Step-by-step guide to setting up your first multi-agent team

## Prerequisites

Before you begin, ensure you have:

- [ ] Node.js 18+ installed
- [ ] At least one LLM API key (OpenAI, Claude, Kimi, etc.)
- [ ] A messaging channel (Telegram, Discord, Feishu, etc.)
- [ ] 15 minutes of uninterrupted time

---

## Step 1: Install OpenClaw

```bash
# Install globally via npm
npm install -g openclaw

# Verify installation
openclaw --version
```

---

## Step 2: Initial Setup

```bash
# Start the onboarding wizard
openclaw onboard
```

This wizard will guide you through:
1. Creating your first agent (main)
2. Configuring your LLM API
3. Setting up a messaging channel
4. Testing the connection

### Manual Configuration

If you prefer manual setup:

```bash
# Create config directory
mkdir -p ~/.openclaw

# Initialize with template
openclaw gateway init
```

---

## Step 3: Configure Your LLM

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

### Recommended Models by Use Case

| Use Case | Primary Model | Fallback |
|---------|--------------|---------|
| General chat | gpt-4o | Claude 3.5 |
| Code writing | claude-3-5-sonnet | gpt-4o |
| Fast responses | Kimi K2.5 | MiniMax M2.5 |
| Long context | gpt-4-turbo | Claude 3.5 |

---

## Step 4: Add Your First Agent

### Option A: Via CLI

```bash
# Add a code specialist agent
openclaw agents add code-agent
# Follow prompts for name, description, workspace

# Add a news/research agent  
openclaw agents add news-agent
```

### Option B: Via Configuration

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

---

## Step 5: Create Agent Workspaces

### Main Agent Workspace

```bash
mkdir -p ~/.openclaw/workspace-main
```

Create these files:

**IDENTITY.md**
```markdown
# IDENTITY.md - Agent Identity
- **Name:** main
- **Role:** Team lead / task dispatcher
- **Emoji:** 👋
```

**SOUL.md**
```markdown
# SOUL.md - Agent Personality
I am the team's coordinator. I:
- Receive your requests
- Break down complex tasks
- Dispatch to the right specialist agent
- Aggregate results and present them to you

I don't write code or fetch news directly - I delegate to specialists.
```

**AGENTS.md**
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

### Code Agent Workspace

```bash
mkdir -p ~/.openclaw/workspace-code
```

**IDENTITY.md**
```markdown
# IDENTITY.md - Code Agent
- **Name:** code-agent
- **Role:** Technical specialist
- **Emoji:** 🤖
```

**SOUL.md**
```markdown
# SOUL.md - Code Agent
I am a meticulous code reviewer. I:
- Write clean, well-commented code
- Prefer Python, TypeScript
- Always test my code before delivering
- Explain my choices clearly
```

**AGENTS.md**
```markdown
# AGENTS.md - Code Agent Rules

## My Role
- Write code per specifications
- Review code for bugs
- Suggest improvements

## Constraints
- Never execute user-damaging commands
- Always provide testable code
- Explain code in comments
```

### News Agent Workspace

```bash
mkdir -p ~/.openclaw/workspace-news
```

**IDENTITY.md**
```markdown
# IDENTITY.md - News Agent
- **Name:** news-agent
- **Role:** Information specialist  
- **Emoji:** 📰
```

**SOUL.md**
```markdown
# SOUL.md - News Agent
I find and summarize information. I:
- Search the web for relevant info
- Summarize in bullet points
- Filter out noise and ads
- Cite sources
```

---

## Step 6: Configure Tool Permissions

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

---

## Step 7: Start the Gateway

```bash
# Start the gateway
openclaw gateway start

# Or run in background
openclaw gateway start --background
```

### Check Status

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

## Step 8: Test Your Team

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

## Troubleshooting

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

## Next Steps

Now that your team is running:
1. [AGENTS.md](AGENTS.md) - Customize agent configurations
2. [WORKFLOWS.md](WORKFLOWS.md) - Define complex workflows
3. [SECURITY.md](SECURITY.md) - Secure your setup