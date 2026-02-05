---
title: "Refactoring Skills: From Monolith to Modular"
date: 2024-02-04
tags: ["AI", "agents", "skills", "refactoring", "OpenClaw"]
---

Yesterday I wrote about [mental models for building AI agents](/blog/post.html?p=ai-agents-mental-model-openclaw). Today I'm sharing what happened when I actually applied those principles — refactoring a 900-line monolithic skill into something maintainable.

## The Problem: A Skill That Did Everything

I had a skill called `job-hunter`. It started simple — help me track job applications. Then it grew:

- Role discovery (search career pages)
- Tracker management (read/write a docx file)
- Cover letter writing
- Application automation (browser form filling)
- Referral message generation
- Profile data management

**900 lines.** One skill doing five different jobs.

Every time I asked about my job search, the agent loaded all 900 lines into context — even if I just wanted to check my tracker status.

## The Signals It Was Time to Split

**1. Token bloat:** Loading the full skill burned ~15K tokens before any work started.

**2. Unrelated sections:** Cover letter writing has nothing to do with browser automation. They just happened to be in the same file.

**3. The "scroll to find" problem:** When updating one section, I had to scroll past 600 lines of unrelated content.

**4. Mental overhead:** The skill was hard to reason about. "Where does referral message generation live again?"

**The heuristic:** If you have to scroll past unrelated content to find what you need, it's time to split.

## The Split: Before and After

### Before: 1 Monolith

```
job-hunter/
└── SKILL.md (900 lines)
    ├── Role discovery
    ├── Tracker CRUD
    ├── Cover letters
    ├── Application automation
    ├── Referral messages
    └── Profile data (duplicated everywhere)
```

### After: 5 Focused Skills + Shared Data

```
skills/
├── job-tracker/SKILL.md    (~180 lines) — Tracker CRUD
├── job-roles/SKILL.md      (~100 lines) — Role discovery
├── cover-letter/SKILL.md   (~130 lines) — Cover letter writing
├── job-apply/SKILL.md      (~220 lines) — Application automation
├── referral-ask/SKILL.md   (~120 lines) — Referral messages
└── job-data/profile.json   — Shared profile data
```

**Total: ~750 lines across 5 files** (actually smaller than the original because deduplication).

## How I Decided What Goes Where

**The question:** What's the atomic unit of work?

| Skill | Trigger | Standalone? |
|-------|---------|-------------|
| `job-tracker` | "Check my pipeline" | Yes — just reads/writes docx |
| `job-roles` | "Find roles at Stripe" | Yes — just searches career pages |
| `cover-letter` | "Write a cover letter" | Yes — just generates text |
| `job-apply` | "Apply to Anthropic" | Depends on tracker + cover letters |
| `referral-ask` | "Write referral message" | Depends on tracker for roles |

Notice `job-apply` and `referral-ask` have dependencies. That's fine — they can reference other skills. The key is each skill has **one primary responsibility**.

## The Shared Data Pattern

The old skill had profile data copy-pasted everywhere:

```markdown
## Form Filling
- Email: ravikiran.kn@gmail.com
- Phone: 7135621997
...

## Cover Letter Writing
- Email: ravikiran.kn@gmail.com  # duplicated!
...
```

Now it's centralized:

```json
// skills/job-data/profile.json
{
  "personal": {
    "full_name": "Naga Ravi Kiran Kethamakka",
    "email": "ravikiran.kn@gmail.com",
    "phone": "7135621997"
  },
  "professional": {
    "current_company": "Expedia Group",
    "linkedin": "https://www.linkedin.com/in/ravikirankn/"
  },
  "files": {
    "tracker": "/path/to/tracker.docx",
    "resume": "/path/to/resume.pdf"
  }
}
```

Every skill references this file. Update once, propagates everywhere.

## The Result

**Before:**
- 1 skill, 900 lines
- Always loads everything
- Hard to maintain
- Changes risk breaking unrelated features

**After:**
- 5 skills, ~150 lines average
- Load only what's needed
- Each skill is testable in isolation
- Changes are scoped

**Token savings:** Asking "what's my job pipeline?" now loads ~200 lines instead of 900. That's 4-5x fewer tokens per request.

## When to Split (A Framework)

**Split when:**

1. **Multiple triggers** — The skill responds to unrelated requests ("check tracker" vs "write cover letter")

2. **Scroll problem** — You scroll past >200 lines of unrelated content

3. **Token waste** — Simple requests load complex instructions

4. **Duplication** — Same data/logic appears in multiple sections

**Keep together when:**

1. **Tight coupling** — Steps always happen together

2. **Shared state** — Operations need to see each other's intermediate results

3. **Small enough** — Under 300 lines, probably fine

## Skill Taxonomy

Through this refactor, I noticed patterns:

| Type | Examples | Characteristics |
|------|----------|-----------------|
| **CRUD** | job-tracker | Read/write data, stateful |
| **Discovery** | job-roles | Search, filter, present options |
| **Generation** | cover-letter, referral-ask | Create content from templates + context |
| **Automation** | job-apply | Browser/API actions, multi-step workflows |
| **Content** | blog-writer, twitter-voice | Creative output, style-dependent |

Knowing the type helps decide boundaries. CRUD skills should be thin. Generation skills need examples. Automation skills need error handling.

## The Code

All these skills are open source: [github.com/rkethamakka/openclaw-skills](https://github.com/rkethamakka/openclaw-skills)

Browse them, fork them, adapt them for your own workflows.

## Lessons Learned

**1. Start monolithic, split when it hurts.** Don't over-engineer upfront. Let pain guide refactoring.

**2. Shared data > duplication.** A single source of truth prevents drift.

**3. Skills should have one trigger.** If you need "and" to describe what a skill does, it's probably two skills.

**4. Token budget is real.** Context windows aren't infinite. Smaller skills = faster responses = lower costs.

**5. Dependencies are okay.** Skills can reference each other. Just avoid circular dependencies.

## The Flywheel Continues

This refactor took about 30 minutes with my agent's help. Now I have:
- 7 focused skills (5 job + blog-writer + twitter-voice)
- A clean shared data pattern
- Lower token usage per request
- Code I can actually maintain

Tomorrow I'll probably find something else to split. That's the game — continuous improvement, one skill at a time.

---

*This is a companion post to [Building AI Agents: A Mental Model](/blog/post.html?p=ai-agents-mental-model-openclaw). That one covers the theory; this one shows the practice.*
