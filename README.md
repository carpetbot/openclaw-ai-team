# OpenClaw AI Team: The Complete Guide

> 构建你的专属AI团队 | Build Your Own AI Team with OpenClaw

Inspired by **木子不写代码**'s viral tutorial (6,215+ likes). This comprehensive guide breaks down how to build a multi-agent AI team that works while you sleep.

## 🌟 Overview

This guide teaches you how to build an AI team using OpenClaw - where one person = a team. Each AI agent has specific responsibilities, works independently, and collaborates through established protocols.

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
# (See AGENTS.md for configuration)
```

---

## 📚 Guide Structure

| File | Description |
|------|-------------|
| [ARCHITECTURE.md](ARCHITECTURE.md) | OpenClaw's three-layer architecture explained |
| [SETUP.md](SETUP.md) | Step-by-step installation guide |
| [AGENTS.md](AGENTS.md) | Agent configurations & code samples |
| [WORKFLOWS.md](WORKFLOWS.md) | Collaboration patterns |
| [TOOLS.md](TOOLS.md) | Tool permission configs |
| [PROMPTS.md](PROMPTS.md) | SOUL.md prompt templates |
| [SECURITY.md](SECURITY.md) | Permission boundaries |
| [EXAMPLES.md](EXAMPLES.md) | Real-world examples |

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