---
title: "45 Applications, One Pipeline: Automating the Job Hunt"
date: 2026-02-07
tags: ["career", "automation", "job-search", "AI", "agents"]
---

I've applied to 45 companies in the past month. Not by grinding through Lever forms at midnight. By building a system.

This is what happens when a platform engineer treats job searching like an engineering problem.

## The Scale

Principal/Staff software engineering roles. Companies ranging from FAANG to growth-stage startups. Each one needs:

- Role discovery (finding the right positions on career pages)
- Application materials (tailored cover letters, sometimes custom answers)
- Submission (navigating Greenhouse, Lever, Workday, or custom ATSs)
- Referral outreach (finding connections, crafting messages)
- Status tracking (where is each application in the pipeline?)

Multiply that by 45 companies. Some with multiple roles. That's easily 100+ hours of busywork if you do it manually.

## The Skills

I split the problem into five focused skills:

**job-tracker**: The source of truth. A Google Doc with every company, status, referrer, notes. Standardized from 21 ad-hoc statuses down to 13 meaningful ones:

- Search Roles, Roles Found
- Referral Requested, Referral Confirmed
- Ready to Apply, Applied
- Recruiter Call Done, Interview: [Date], In Loop: [Dates]
- Rejected, Stalled, Limited Roles, Closed

When I ask "what's my pipeline?" I get an instant rollup. Not a pile of browser tabs.

**job-roles**: Career page crawler. Point it at a company, it finds matching roles. Snap had 5 relevant positions. Figma had 4. Plaid had 3. It filters by level (L6/L7), location (Seattle/remote), and keywords (platform, distributed systems, AI).

**cover-letter**: Tailored letter generator. Feed it a job description and it writes a cover letter matching my experience to their requirements. Not generic fluff, specific examples. The Salesforce letter mentions my API gateway work. The Microsoft letter mentions distributed systems scale.

**job-apply**: Browser automation for form submission. Navigate to application page, fill fields, upload resume. Most ATSs follow patterns: personal info, work authorization, resume upload. Automate the pattern, handle exceptions manually.

**referral-ask**: Message generator for LinkedIn and email outreach. Given a role and a potential referrer, it crafts a personalized ask. Not "can you refer me" spam, but context about the role, why I'm interested, what I'd bring.

## What Actually Happens

Here's a typical session:

> "Add Snap to the tracker and find roles"

The agent searches Snap's career page, finds 5 relevant positions (Principal SWE, Staff SWE, etc.), adds them to the tracker with links and salary ranges.

> "Write cover letters and apply to the L7 roles"

Cover letters generated, saved to Google Drive. Browser opens Workday, creates account, fills application forms, uploads resume and cover letter. Three applications submitted.

> "Who do I know at Figma?"

LinkedIn search for 2nd-degree connections at Figma in engineering. Find Owais Ahmed, 2nd degree, Seattle. Generate a referral request message.

Total time: maybe 20 minutes. Manual version: 3+ hours.

## The Referral Network Effect

Cold applications have ~2% response rates. Referrals: 10-20x better. So I built referral hunting into the workflow.

For each company:
1. Search LinkedIn for 1st and 2nd degree connections
2. Filter by relevance (engineering, Seattle, senior+)
3. Generate personalized outreach
4. Track status (requested, confirmed, not responsive)

Current referral coverage:
- 15 companies: referral requested
- 6 companies: recruiter call done
- 9 companies: applied (mostly with referrals)

The tracker knows who I've reached out to, so I don't accidentally spam the same person twice.

## Cover Letter Economics

I used to spend 30-45 minutes per cover letter. Researching the company, finding angles, writing drafts. Now:

1. Paste job description
2. AI identifies key requirements
3. Matches to my experience (from a profile.json with past projects)
4. Generates letter with specific examples
5. I review and adjust tone

Time: 5 minutes including review. Quality: honestly better than my manual versions. The AI is relentless about specificity. It won't write "I have extensive experience" when it can write "I led the API Gateway platform serving 10K TPS across 50 teams."

I've generated 12+ tailored cover letters this week. Each one references specific projects from my background that match the role requirements.

## ATS Archaeology

Every company uses a different ATS with different quirks:

- **Lever**: Clean, predictable. Usually works smoothly.
- **Greenhouse**: Needs careful handling of the "How did you hear about us" dropdown.
- **Workday**: Account creation flow, security questions, email verification.
- **Apple Jobs**: Custom system, requires Apple ID, different form for each role.
- **Custom ATSs**: Manual fallback. Some things aren't worth automating.

The skill has a "capabilities" section for each ATS family. What's automatable, what's not. Resume uploads on Lever sometimes need manual file picker interaction since browser automation can't fully handle OS-level dialogs.

## The Dashboard

45 companies. Here's the current state:

| Status | Count |
|--------|-------|
| In Loop | 1 (Expedia: Feb 16-17) |
| Interview Scheduled | 1 (OpenAI: Feb 10) |
| Recruiter Call Done | 6 |
| Applied | 9 |
| Referral Requested | 15 |
| Ready to Apply | 2 |
| Roles Found | 5 |
| Search Roles | 1 |
| Rejected/Stalled/Closed | 5 |

One question surfaces these numbers: "What's my pipeline status?" The tracker aggregates, the AI summarizes.

## What I Still Do Manually

Not everything should be automated:

- **Actual conversations**: Recruiter calls, interviews. Obviously.
- **Judgment calls**: Is this role actually a fit? Does this company's culture match?
- **Relationship building**: Referral requests start with automation, but real connections require human follow-up
- **Salary negotiation**: When I get there, that's all me

The automation handles volume. The human handles nuance.

## The Uncomfortable Truth

Job searching sucks. It's repetitive, demoralizing, and weirdly time-consuming for what should be simple transactions. "Here's my resume. Want to talk?"

But treating it like an engineering problem helps. Not because it makes rejection hurt less. It doesn't. Because it removes the friction that makes the process feel endless.

When applying to one company takes 20 minutes instead of 2 hours, you can apply to more companies. More applications mean more shots. More shots mean better odds.

## Building the System

The skills are in my public repo (sanitized, of course, no personal data). But the pattern is more important than the code:

1. **Centralize state**: One tracker, one source of truth
2. **Standardize statuses**: If you have 21 different ways to say "in progress," you have chaos
3. **Automate the repetitive**: Form-filling, status updates, document generation
4. **Keep humans on judgment**: Which roles to pursue, how to present yourself, when to follow up

If you're job hunting, you don't need my exact skills. But you probably need some version of this system. Spreadsheet discipline, templated outreach, tracked follow-ups.

The job market is brutal right now. Might as well bring engineering discipline to it.

---

*45 companies tracked. 12+ cover letters generated. 1 offer pending. The grind continues.*
