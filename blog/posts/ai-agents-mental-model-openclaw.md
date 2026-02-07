---
title: "Building AI Agents: A Mental Model + OpenClaw"
date: 2026-02-04
tags: ["AI", "agents", "architecture", "OpenClaw"]
---

I've been building AI agents for a while now, and I've developed a mental model that helps me think about architecture decisions. Here's what I've learned.

## Why Build Custom Skills?

**The core insight:** Skills aren't just "things an agent can do". They're **crystallized knowledge** that compounds.

When you build a skill like `job-hunter`:
- First run: The agent fumbles, learns the tracker format, figures out hyperlinks
- Second run: It knows the patterns, avoids mistakes
- Tenth run: It's fast, consistent, reliable

**But here's the key:** If that knowledge lives only in the context window, it dies when the session ends. A skill file **persists** that knowledge:
- Where's the tracker? The exact path.
- What columns? The specific format.
- What's the workflow? Discovery → Cover Letter → Apply → Update

**You're not just automating a task. You're building institutional memory.**

## Why Model Day-to-Day Work as Skills?

Think about what you do daily:
- Check email → triage → respond
- Review PRs → leave comments → approve/request changes
- Track tasks → update status → notify stakeholders

These are **repeatable patterns with domain knowledge**. When you encode them as skills:

1. **Consistency**: Same quality every time, not dependent on energy levels
2. **Delegation**: The agent can work while you sleep, commute, or focus on deep work
3. **Iteration**: Improve the skill once, every future execution benefits
4. **Handoff**: Another agent (or human) can pick up the skill

**The mental model:** You're not training an employee. You're writing a procedure manual that *executes itself*.

## How Multiple Skills Work Together

A single agent can handle multiple skills:
- `job-hunter`: role discovery, tracker updates, cover letters
- `twitter-voice`: brainstorm, draft, post
- `weather`: simple lookup

**This works because:**
- Skills are **independent**. job-hunter doesn't need twitter-voice
- **Shared context**: The agent knows your preferences, timezone, history
- **Small surface area**: Each skill is <500 lines of instructions

**The pattern:**
```
Agent = Core Identity + Context + Σ(Skills)
```

Skills are modular. The agent orchestrates them, deciding *when* to invoke *which* skill based on user intent.

## When to Split into Multiple Agents?

**Stay single-agent when:**
- Skills share context (your preferences, history, relationships)
- Total skill instructions fit comfortably in context (~50K tokens)
- Tasks are sequential, not parallel
- You want conversational continuity

**Split into multi-agent when:**
- **Domain expertise diverges**: A coding agent vs. a research agent have different mental models
- **Context isolation needed**: Agent A shouldn't see Agent B's sensitive data
- **Parallel execution**: You want 5 agents researching 5 companies simultaneously
- **Different models/capabilities**: One agent needs vision, another needs code execution

**The heuristic:** If two "skills" would benefit from *forgetting* each other's context, they should be separate agents.

## Agent Orchestration: When?

Orchestration = one agent coordinating multiple sub-agents.

**You need it when:**
- Task requires **decomposition**: "Research 10 companies" → spawn 10 parallel research agents
- **Handoffs**: Research agent passes to analysis agent passes to report agent
- **Human-in-the-loop checkpoints**: Orchestrator collects results, asks you to approve, then proceeds
- **Resource management**: Can't have 50 agents running; orchestrator queues and prioritizes

**Real example from my work:**
> "1 orchestrator managing 6 domain-expert agents (CI, CD, Vault, Scaffolding, Repository Analysis, Human-in-the-Loop)"

That's orchestration. The orchestrator doesn't *do* CI work. It knows *which agent* handles CI and *when* to invoke it.

## Why Think This Way When Building Agents?

**Because the alternative is chaos.**

Without skill/agent boundaries:
- Every agent becomes a monolith
- Context windows fill with irrelevant instructions
- Errors cascade (one bad skill pollutes everything)
- No reusability

**With clear boundaries:**
- Skills are testable in isolation
- Agents have clear responsibilities
- You can swap implementations (different model, different approach)
- Debugging is tractable

**The software engineering parallel:** This is just good architecture. Single responsibility. Separation of concerns. Loose coupling.

## Context Windows & Boundaries

**Current reality (200K tokens):**
- ~150K words, or ~300 pages
- Sounds huge, but fills fast with:
  - System prompts (~5-10K)
  - Skill instructions (~10-50K for complex skills)
  - Conversation history (~20-50K)
  - Tool outputs (can be huge: docx files, web pages, etc.)

**This forces discipline:**
- Keep skills focused
- Summarize/compress history
- Don't load everything, load what's needed
- Clear stale context

**The 200K constraint is actually healthy.** It prevents lazy "dump everything" architectures.

## What Changes with Million-Token Context?

**The tempting thought:** "I can fit everything! No need for multi-agent!"

**Reality:** Context length ≠ context quality.

**Problems with mega-context:**
1. **Attention degradation**: Models struggle to attend to relevant info in massive contexts ("lost in the middle" problem)
2. **Latency**: More tokens = slower responses
3. **Cost**: Token usage scales linearly (or worse)
4. **Debugging hell**: When something goes wrong, which of the 500K tokens caused it?

**What actually changes:**
- **Longer conversations** without summarization
- **Bigger documents** can be processed whole (full codebases)
- **More examples** for in-context learning
- **Richer memory** within a session

**What doesn't change:**
- Need for clear boundaries
- Value of modular skills
- Benefits of parallel execution
- Importance of isolation

**My prediction:** Million-token contexts will make single-agent more viable for *some* use cases, but the multi-agent pattern will remain superior for:
- Parallel work
- Isolated domains
- Long-running tasks
- Production systems

## The Mental Model

Think of it like this:

```
You (human)
  ↓
Main Agent - your interface, knows your context
  ↓
Skills - modular capabilities (job-hunter, twitter-voice)
  ↓
Sub-agents - for parallel/specialized work
  ↓
Tools - atomic actions (read, write, browser, etc.)
```

**Skills improve the agent. Agent coordinates skills. You improve skills.**

It's a flywheel:
1. You notice a repeated task
2. You build a skill for it
3. Agent executes it better each time
4. You refine the skill
5. Agent gets even better
6. Repeat

## The Punchline

**Building skills isn't about automation. It's about encoding your expertise into something that persists, compounds, and executes without you.**

Each skill you build makes future-you (and future-agents) more capable.

That's the game. 🎯
