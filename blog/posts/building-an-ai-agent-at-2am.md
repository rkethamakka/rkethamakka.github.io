---
title: "Building an AI Agent at 2 AM: A Late Night Experiment"
date: 2024-02-04
tags: ["AI", "agents", "OpenClaw", "automation"]
---

It's 2 AM and I just shipped a job hunting AI agent. Let me tell you how that happened.

## The Setup

I've been playing with [OpenClaw](https://github.com/openclaw/openclaw), an open-source framework for building AI agents. Think of it as a way to give Claude (or other LLMs) actual tools — file access, web browsing, shell commands, messaging — and let it do real work.

Tonight I decided to build something practical: an AI assistant to help with my job search.

## What We Built

### 1. Job Hunter Skill

The core of the system is a "skill" — a structured way to teach the agent how to do something specific. My job-hunter skill can:

- **Discover roles** at target companies (filtering for Staff/Senior SWE positions)
- **Track applications** in a Google Drive-synced document with clickable hyperlinks
- **Write cover letters** using existing letters as templates
- **Auto-apply** to roles (with human confirmation before submitting)

The tracker now has 36 companies and 75 role hyperlinks, all added programmatically.

### 2. This Website

Yep, this blog you're reading was also built tonight. The agent:

- Extracted text from my PDF resume
- Converted it to a modern HTML page with responsive design
- Deployed it to GitHub Pages
- Set up this markdown-based blog system

All through conversation. I'd say "build me a resume site" and it would figure out the steps, execute them, and handle the edge cases.

### 3. Twitter Integration

We also set up a Twitter presence for sharing learnings and connecting with other engineers. The agent can draft tweets, suggest engagement opportunities, and help maintain a consistent voice.

## The Interesting Parts

**Natural language → Real actions.** I didn't write code tonight. I described what I wanted, and the agent translated that into file operations, API calls, and browser automation. When something broke (and things did break), we debugged together.

**Iterative refinement.** The first version of the tracker had the wrong columns. The first cover letter was too generic. The first website upload corrupted the HTML. Each time, we identified the issue and fixed it. The agent learned from mistakes within the session.

**Compound capabilities.** The job-hunter skill combines web scraping, document manipulation (python-docx for hyperlinks), file management, and browser automation. None of these are impressive alone, but together they create something useful.

## What I Learned

1. **AI agents are force multipliers, not replacements.** I still made all the decisions. The agent executed. This is the right balance for now.

2. **Skills > Prompts.** A well-structured skill file beats a clever prompt. It gives the agent clear procedures, constraints, and context.

3. **Trust is earned incrementally.** The agent asks before sending emails or making external changes. It shows me screenshots before submitting applications. This builds confidence.

4. **Late night coding hits different with an AI pair.** There's something surreal about having a conversation that results in real infrastructure. It's like pair programming, but your partner never gets tired and has read all the documentation.

## What's Next

- Write more blog posts (the agent can help draft these too)
- Actually apply to some of these 75 roles
- Explore more automation possibilities
- Maybe teach the agent to write better tweets than I can

## Try It Yourself

If you're curious about building AI agents, check out:
- [OpenClaw](https://github.com/openclaw/openclaw) - The framework I used
- [Claude](https://www.anthropic.com/) - The LLM behind this

The barrier to entry is lower than you think. And if you're job hunting, maybe an agent can help with that too.

---

*Built at 2 AM, deployed at 3 AM. No regrets.*
