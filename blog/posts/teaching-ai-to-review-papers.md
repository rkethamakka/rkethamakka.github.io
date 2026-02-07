---
title: "Teaching AI to Review Academic Papers"
date: 2026-02-07
tags: ["AI", "agents", "peer-review", "ethics", "automation"]
---

I'm a reviewer for 12 academic conferences. Last week, I had four papers waiting for review with deadlines looming. So I did what any engineer with an AI assistant would do: I built a skill for it.

This is the story of teaching an AI to help with peer review, the unexpected technical challenges, and the ethical lines I had to draw.

## The Setup

Microsoft CMT (Conference Management Toolkit) is where academic conferences manage paper submissions. As a reviewer, you log in, download PDFs, read papers, and submit ratings plus detailed feedback. It's manual, time-consuming, and - if you're reviewing across multiple conferences - scattered across different deadlines.

I wanted my AI agent to help. Not to write reviews for me, but to handle the tedious parts: navigating the interface, downloading papers, organizing the workflow, and formatting my feedback consistently.

## The First Problem: Downloads Don't Click

Browser automation sounds simple. Click the download link. Save the file. Done.

Except CMT's download links don't work that way. When you click a PDF link, the browser sees it as navigation to a new URL. My automation tool threw an error: "Download is starting" - and then nothing. The file never appeared.

After debugging, I found the fix: skip the browser for downloads entirely.

```bash
# Extract auth cookies via Chrome DevTools Protocol
# Then use curl with those cookies
curl -s -o paper.pdf \
  -H "Cookie: .AspNetCore.Cookies={SESSION}; .ROLE=Reviewer" \
  "https://cmt3.research.microsoft.com/api/Conference/Files/98"
```

CDP gives you access to the browser's cookie jar. Curl doesn't care about "download behavior" - it just fetches bytes. Problem solved.

This is a pattern I keep rediscovering: when browser automation fights you, drop down a level. Sometimes `curl` beats Playwright.

## Building the Review Workflow

The skill I built tracks papers across all 12 conferences:

- **NEEDS_REVIEW**: Papers assigned, deadline pending
- **DEADLINE_PASSED**: Missed it (conferences move fast)
- **ALREADY_REVIEWED**: Submitted
- **NO_PAPERS**: No assignments yet

For each paper, the workflow is:

1. Scan the summary page for title and abstract
2. Download the PDF (using the curl trick)
3. Convert if needed (some authors submit .doc files - `textutil` handles that on macOS)
4. Read and analyze the paper
5. Fill the rating form (technical quality, novelty, clarity, relevance, significance)
6. Upload a detailed review PDF (some conferences require file attachments)
7. Submit

The AI handles navigation, form-filling, and generating the detailed PDF from my bullet points. I handle the actual judgment calls.

## The Ethics Line

Here's where it gets interesting. Where's the line between "AI-assisted" and "AI-generated" reviews?

My rule: **the AI never decides accept or reject.** It can summarize the paper. It can identify methodology gaps. It can format feedback. But the judgment call - is this contribution worthy of publication? - that's mine.

This matters because peer review is built on trust. Authors submit their work expecting human experts to evaluate it. If I outsource that judgment to a model, I'm breaking that implicit contract.

What the AI does well:
- **Consistency**: Every review follows the same structure (technical analysis, contributions, gaps, recommendations)
- **Thoroughness**: It catches things I might skim over - missing citations, unclear notation, statistical gaps
- **Speed**: Form-filling and PDF generation that used to take 20 minutes now takes 2

What I still do:
- Read the paper (yes, actually read it)
- Decide if the methodology is sound
- Judge novelty against the field I know
- Make the accept/rework/reject call

## Lessons from Four Reviews

**Paper #88: Resume Analysis with NLP**
Complete ML pipeline, real metrics, deployed system. Accept. The AI helped me articulate why the 92.7% accuracy claim was credible (proper train/test split, multiple classifiers compared).

**Paper #131: AI + Blockchain for Mental Health**
Pure concept paper. No implementation, no experiments. Just "we propose combining these buzzwords." Rework. The AI helped structure my feedback diplomatically while being clear: you need to actually build something.

**Paper #181: Student Performance Prediction**
Solid work, but missing cross-validation and baseline comparisons. Accept with notes. The detailed PDF had six specific recommendations for strengthening the paper.

One thing I learned the hard way: **never compare papers to each other in reviews.** Each paper stands on its own merits. Mentioning "Paper #88 had implementation, why don't you?" is unprofessional. The AI initially made this mistake - I caught it and added the rule to my skill.

## The Detailed PDF Pattern

ICCTAC requires uploading a file with each review. Rather than just attaching the paper back, I started generating detailed review PDFs:

1. Skip score repetition (ratings are already on the form)
2. Lead with technical analysis
3. List key contributions (what the paper actually achieves)
4. Enumerate critical gaps with evidence (cite specific sections)
5. Close with numbered recommendations

This structure helps authors understand *why* they got the score they did. It's more work, but it's the kind of feedback I'd want on my own submissions.

## Why This Matters

Peer review is overwhelmed. Conferences get thousands of submissions. Reviewers are volunteers squeezing reviews between their day jobs. The result: rushed reviews, inconsistent feedback, frustrated authors.

AI won't fix the fundamental incentive problems. But it can reduce the friction that makes reviewing painful. If I can get through four papers in an evening instead of a weekend, I'm more likely to accept that fifth review invitation.

The key is keeping humans in the loop for the judgment calls. AI handles the mechanics. Humans handle the meaning.

## The Skill

The `eb1a-judging` skill is now part of my public OpenClaw skills repo. It's specific to CMT, but the patterns generalize:

- Scan across multiple data sources (12 conferences)
- Handle download edge cases (CDP + curl)
- Maintain consistent output format (structured review PDFs)
- Track state (which papers reviewed, which pending)

If you're doing any kind of repeated evaluation work - code reviews, application screening, document analysis - the same skill architecture applies.

---

*Conference papers reviewed: 4. Coffees consumed: too many. Ethical lines crossed: zero.*
