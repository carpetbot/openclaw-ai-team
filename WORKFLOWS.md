# Workflows & Collaboration Patterns

> How to make your agents work together effectively

## Basic Workflow Patterns

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

---

## Implementation

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

### Parallel Execution

```python
# Run agents in parallel using async
async def handle_parallel_request(task):
    # Fire both calls
    code_task = sessions_send(agentId="code-agent", task=f"Task: {task}")
    news_task = sessions_send(agentId="news-agent", task=f"Research: {task}")
    
    # Wait for both
    code_result = await code_task
    news_result = await news_task
    
    # Combine results
    return combine(code_result, news_result)
```

---

## Common Workflows

### Daily Standup Workflow

```
9:00 AM: HEARTBEAT triggers

→ Main checks GitHub notifications
→ Main checks email/messages  
→ Main compiles status
→ Main sends daily brief to user
```

### Research Workflow

```
User: "Tell me about [topic]"

→ Main → News Agent: "Research [topic]"
→ News returns summary with sources
→ Main formats for user
→ User receives brief
```

### Code Review Workflow

```
User: "Review my code"

→ Main → Code Agent: "Review this code"
→ Code reviews and provides feedback
→ Main formats as review comments
→ User receives review
```

### Multi-Step Content Creation

```
User: "Create post about [topic]"

→ Main → Research: "Gather info on [topic]"
→ Writer → "Create draft based on research"
→ Review → "Improve and finalize"
→ Main → Deliver final post
```

---

## Advanced Patterns

### Conditional Routing

```python
# Route based on task type
def route_task(request):
    if "code" in request.lower() or "function" in request.lower():
        return sessions_send(agentId="code-agent", task=request)
    elif "search" in request.lower() or "info" in request.lower():
        return sessions_send(agentId="news-agent", task=request)
    else:
        # Handle directly
        return handle_directly(request)
```

### Fallback Patterns

```python
try:
    result = sessions_send(agentId="preferred-agent", task=task)
except Exception as e:
    # Fallback to secondary agent
    result = sessions_send(agentId="fallback-agent", task=task)
```

### Retry Patterns

```python
def call_with_retry(agent_id, task, max_retries=3):
    for attempt in range(max_retries):
        try:
            return sessions_send(agentId=agent_id, task=task)
        except Exception as e:
            if attempt == max_retries - 1:
                raise e
            # Wait before retry
            time.sleep(2 ** attempt)
```

---

## Best Practices

### 1. Clear Task Definitions

```python
# ✗ Vague
sessions_send(agentId="code-agent", task="work on this")

# ✓ Specific  
sessions_send(agentId="code-agent", task="""
Write a Python function that:
1. Takes a list of numbers
2. Returns the average
3. Handles empty lists gracefully

Example input: [1, 2, 3, 4, 5]
Expected output: 3.0
""")
```

### 2. Timeout Settings

```python
# Short task
sessions_send(agentId="code-agent", task="Quick fix", timeout=60)

# Complex task
sessions_send(agentId="news-agent", task="Research topic", timeout=180)
```

### 3. Error Handling

```python
try:
    result = sessions_send(agentId="code-agent", task=task)
except:
    # Report failure clearly
    return "That agent is having trouble. Let me try another approach."
```

---

## Monitoring

### Check Agent Status

```bash
# List all agents
openclaw agents list

# Check specific agent
openclaw agents status code-agent
```

### View Logs

```bash
# View gateway logs
openclaw gateway logs

# View agent conversation
sessions_history agentId="code-agent"
```

---

## Examples

### Example 1: Code + Review

**User**: "Write a Python web scraper"

```
Main receives → Code Agent writes scraper → 
Main reviews → Review Agent checks → 
Main delivers
```

### Example 2: Research + Write + Publish

**User**: "Create blog post about AI trends"

```
Main receives → News Agent researches → 
Writer Agent drafts → Review Agent edits →
Main delivers → User approves → Published
```

### Example 3: Multi-Tool Pipeline

**User**: "Analyze stock data"

```
Main receives → News Agent gets data → 
Code Agent analyzes → Review Agent validates →
Main presents results with charts
```

---

## Next Steps

- [PROMPTS.md](PROMPTS.md) - Customize prompts for each agent
- [EXAMPLES.md](EXAMPLES.md) - Real-world usage examples