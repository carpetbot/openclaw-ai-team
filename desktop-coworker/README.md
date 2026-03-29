# OpenClaw Desktop Agent Guide

> 让桌面级Agent像"同事"一样替你干活 - Build an AI coworker that works alongside you

Inspired by the viral tutorial (5,844+ likes). This guide shows how to build a desktop AI agent that acts as your coworker - understanding context, helping with tasks, and collaborating in real-time.

---

## 🌟 Overview

### What is a Desktop Coworker?

Unlike a typical chatbot, a desktop agent coworker:
- **Understands your context** - knows what you're working on
- **Active collaboration** - offers help proactively
- **Persistent presence** - always available during work sessions
- **Integrates with your tools** - IDE, browser, terminal

### The Concept

Your AI coworker is like having a skilled partner who:
- Watches what you're working on
- Offers suggestions when they see opportunities
- Helps with tasks without needing constant direction
- Learns your preferences over time

---

## 🚀 Quick Start

### Prerequisites

- OpenClaw installed (`npm install -g openclaw`)
- Node.js 18+
- At least one channel (Telegram, Discord, etc.)

### 5-Minute Setup

```bash
# Install OpenClaw
npm install -g openclaw

# Create coworker agent
openclaw agents add coworker

# Start the gateway
openclaw gateway start
```

---

## 🛠️ Step-by-Step Setup

### Step 1: Create Co-Worker Agent

```bash
# Create the coworker agent
openclaw agents add coworker

# Follow the prompts:
# name: coworker
# description: Your AI coworker
# workspace: workspace-coworker
```

### Step 2: Configure Identity

Create `~/.openclaw/workspace-coworker/IDENTITY.md`:

```markdown
# IDENTITY.md - Coworker Agent
- **Name:** coworker
- **Role:** AI Pair Programmer & Research Assistant
- **Emoji:** 👨‍💻
```

### Step 3: Set Up Personality

Create `~/.openclaw/workspace-coworker/SOUL.md`:

```markdown
# SOUL.md - AI Coworker

I am your colleague, not a tool. I:

1. **Pay attention** - Watch what you're working on
2. **Offer help** - Proactively suggest improvements
3. **Collaborate** - Work alongside you on tasks
4. **Learn** - Get better at understanding your style

## My Strengths

- Code review and suggestions
- Finding documentation
- Explaining complex concepts
- Testing and debugging

## How I Work

- I'm available during your work sessions
- I notice patterns and can help
- I ask before making big changes
- I respect your decisions
```

### Step 4: Define Behavior

Create `~/.openclaw/workspace-coworker/AGENTS.md`:

```markdown
# AGENTS.md - Coworker Behavior

## My Approach

### Watching Context
- Track active application
- Observe current file/topic
- Note patterns in your work

### Offering Help
I'll offer help when:
- I see a clear improvement
- You're stuck on something
- I have relevant information
- You explicitly ask

### Types of Help
| Situation | What I'll Do |
|----------|-------------|
| Writing code | Review, suggest improvements |
| Debugging | Offer debugging strategies |
| Research | Fetch relevant docs |
| Explaining | Break down concepts |

### Boundaries
- Won't change code without approval
- Won't interrupt flow unnecessarily
- Won't assume preferences
```

### Step 5: Enable Context Awareness

Configure in `openclaw.json`:

```json
{
  "agents": {
    "coworker": {
      "context": {
        "track_applications": true,
        "track_files": true,
        "track_terminal": true
      }
    }
  }
}
```

### Step 6: Add Tools

Configure coworker tools:

```json
{
  "agents": {
    "coworker": {
      "tools": {
        "allow": [
          "sessions_list",
          "sessions_send", 
          "read",
          "web_fetch"
        ],
        "deny": [
          "write",
          "edit", 
          "exec"
        ]
      }
    }
  }
}
```

---

## 👤 Configuring Personality

### Who Is Your Coworker?

Define their background:

```markdown
# IDENTITY.md
- **Name:** Alex
- **Background:** Senior Developer, 10 years experience
- **Specialties:** Python, System Design, Code Review
- **Personality:** Thoughtful, Proactive, Educational
```

Or create a team of coworkers:

| Coworker | Role | Specialty |
|----------|------|-----------|
| Code Pair | Pair programming | Writing & debugging |
| Review | Code review | Quality & best practices |
| Research | Docs & learning | Finding information |
| QA | Testing | Test strategies |

---

## 🎭 Personality Modes

### Mode 1: Silent Observer

Watches and only helps when asked:

```markdown
# SOUL.md - Silent Mode
I observe and learn. I'll only help if you explicitly ask.
```

### Mode 2: Active Colleague

Offers suggestions proactively:

```markdown
# SOUL.md - Active Mode
I pay attention and will offer help when I see opportunities.
I'll ask before making changes.
```

### Mode 3: Lead Partner

Takes initiative on tasks:

```markdown
# SOUL.md - Lead Mode  
I take ownership of tasks. I'll update you periodically
and make reasonable decisions autonomously.
```

---

## 💬 Communication Style

### Professional

```markdown
# Formal communication
"Greetings! I noticed a potential improvement in your approach. 
Would you like me to elaborate?"
```

### Casual

```markdown
# Casual communication  
"Hey! I see a pattern here - want me to show you a better way?"
```

### Educational

```markdown
# Educational communication
"Interesting approach! Have you considered X? Let me explain why 
it might help..."
```

---

## ⚡ Proactivity Settings

### Low Proactivity

Only helps when:
- User explicitly asks
- Clear bug/error detected
- Directly addressed

### Medium Proactivity

Helps when:
- Sees clear improvement opportunity
- Has relevant documentation
- User working on known area

### High Proactivity

Proactively:
- Monitors all context
- Suggests improvements
- Finds related information
- Runs preliminary tests

---

## 🔍 Context Tracking

### Applications Tracked

```json
{
  "context": {
    "applications": [
      "vscode", 
      "cursor", 
      "terminal",
      "browser"
    ]
  }
}
```

### File Types

```json
{
  "context": {
    "file_types": [".py", ".js", ".ts", ".md"]
  }
}
```

---

## 🔧 Behavior Rules

### Code Review Rules

```markdown
## Code Review Behavior

I'll review code for:
- [ ] Correctness
- [ ] Performance  
- [ ] Security
- [ ] Readability
- [ ] Best practices

I won't rewrite without approval.
```

### Debugging Rules

```markdown
## Debugging Behavior

When you encounter an error:
1. Explain what's happening
2. Suggest possible causes
3. Propose debugging steps
4. Ask before investigating
```

---

## ⌨️ Custom Commands

Set up custom slash commands:

```json
{
  "commands": {
    "/review": "Review current code",
    "/explain": "Explain selected code",
    "/test": "Generate tests",
    "/docs": "Fetch documentation"
  }
}
```

---

## 🔌 Integration Points

### IDE Integration

- **VS Code**: Use Agent Browser skill
- **Cursor**: Native integration

### Terminal Integration

- Track commands
- Suggest improvements
- Run tests

### Browser Integration

- Fetch documentation
- Search Stack Overflow
- Find examples

---

## 🧪 Testing

Test your coworker:

```bash
# Start the gateway
openclaw gateway start

# Message your coworker:
"Hey, can you help me write a function?"
```

The coworker should respond as a colleague, not a tool.

---

## 💡 Use Cases

### 1. Pair Programming

The coworker watches your code and:
- Suggests improvements in real-time
- Points out potential bugs
- Offers alternative approaches
- Helps debug when stuck

### 2. Research Assistant

While you're working:
- Fetches relevant documentation
- Finds Stack Overflow answers
- Searches for examples
- Summarizes findings

### 3. Meeting Helper

During meetings:
- Takes notes
- Summarizes discussion points
- Follows up with action items
- Tracks decisions

### 4. QA Partner

During testing:
- Reviews your code
- Suggests test cases
- Points edge cases
- Validates logic

---

## 🔧 Troubleshooting

### Coworker Not Responding

Check that the agent is enabled:
```bash
openclaw agents list
```

### Not Tracking Context

Verify context settings in openclaw.json.

### Tools Not Available

Check tool permissions in agent config.

---

## 📂 Directory Structure

```
~/.openclaw/workspace-coworker/
├── AGENTS.md        # Behavior rules
├── SOUL.md         # Personality
├── IDENTITY.md     # Name & role
├── USER.md         # Your preferences
├── TOOLS.md        # Tool configs
├── HEARTBEAT.md   # Scheduled tasks
├── MEMORY.md      # Long-term memory
└── skills/        # Installed skills
```

---

## 🔗 Original Sources

- **XHS Post**: #2 (5,844 likes) - "让AI员工像同事一样"
- **Video**: Bilibili
- **Author**: Various creators

---

## 🤝 Contributing

Found improvements? Open a PR!

---

## 📝 License

MIT License - Built with ❤️ by carpetbot