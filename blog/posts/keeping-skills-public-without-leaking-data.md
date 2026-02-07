---
title: "Keeping Skills Public Without Leaking Personal Data"
date: 2026-02-04
tags: ["AI", "agents", "skills", "automation", "privacy"]
---

I wanted to open-source my OpenClaw skills. Problem: they're full of personal data. Emails, phone numbers, file paths. Here's how I solved it.

## The Problem

My skills contain stuff like this:

```markdown
## Form Filling
- Email: myemail@gmail.com
- Phone: 5551234567
- Resume: /Users/myname/Documents/resume.pdf
```

Useful for me. Dangerous to publish.

## The Solution: Placeholders

Replace personal data with environment-variable-style placeholders:

```markdown
## Form Filling
- Email: ${EMAIL}
- Phone: ${PHONE}
- Resume: ${HOME}/Documents/${RESUME_FILE}
```

Now anyone can fork the skill and fill in their own values.

## The Mapping

I maintain a simple list of replacements:

```python
REPLACEMENTS = [
    # Paths (more specific first)
    (r'/Users/myname/\.openclaw/workspace', '${WORKSPACE}'),
    (r'/Users/myname/Documents/jobs', '${JOBS_DIR}'),
    (r'/Users/myname', '${HOME}'),
    
    # Personal info
    (r'myemail@gmail\.com', '${EMAIL}'),
    (r'5551234567', '${PHONE}'),
    (r'My Full Name', '${FULL_NAME}'),
    
    # File names
    (r'My_Resume\.pdf', '${RESUME_FILE}'),
]
```

**Order matters.** More specific paths first, otherwise `/Users/myname` gets replaced before `/Users/myname/.openclaw/workspace` is matched.

## The Sync Script

One script does everything:

```python
def sync_skills(commit_msg="Update skills"):
    # 1. Pull latest from public repo
    subprocess.run(['git', 'pull'], cwd=REPO)
    
    # 2. Copy local skills
    for skill in os.listdir(SOURCE):
        shutil.copytree(src, dst)
    
    # 3. Sanitize each text file
    for filepath in text_files:
        content = open(filepath).read()
        for pattern, replacement in REPLACEMENTS:
            content = re.sub(pattern, replacement, content)
        open(filepath, 'w').write(content)
    
    # 4. Commit and push
    subprocess.run(['git', 'add', '.'], cwd=REPO)
    subprocess.run(['git', 'commit', '-m', commit_msg], cwd=REPO)
    subprocess.run(['git', 'push'], cwd=REPO)
```

Now I just say "sync skills to repo" and my agent runs it.

## Two Copies, One Truth

**Local skills:** Real paths, real emails. They work.

**Public skills:** Placeholders. They're templates.

The sync script is the bridge. Local is always the source of truth.

## What Gets Sanitized

| Type | Example | Placeholder |
|------|---------|-------------|
| Home directory | `/Users/myname` | `${HOME}` |
| Workspace | `~/.openclaw/workspace` | `${WORKSPACE}` |
| Email | `me@gmail.com` | `${EMAIL}` |
| Phone | `5551234567` | `${PHONE}` |
| Full name | `John Smith` | `${FULL_NAME}` |
| Company | `Acme Corp` | `${CURRENT_COMPANY}` |
| File paths | `/path/to/resume.pdf` | `${JOBS_DIR}/${RESUME_FILE}` |

## Verification

After every sync, I check for leaks:

```bash
grep -r "myname\|5551234567\|myemail" /path/to/repo | grep -v ".git"
```

Should return nothing. If it does, I add the pattern to REPLACEMENTS.

## The Result

- **10 skills** synced to public repo
- **Zero personal data** exposed
- **One command** to publish updates
- **Others can fork** and customize with their own data

## Why Bother?

1. **Portfolio**: Show what you've built
2. **Sharing**: Others can learn from your patterns
3. **Backup**: Public repo = offsite backup
4. **Forcing function**: Knowing it's public makes you write cleaner skills

## The Skill That Syncs Itself

The sync script is itself a skill. And yes, it syncs itself too, with its own paths sanitized.

It's skills all the way down.

---

*Full code: [github.com/rkethamakka/openclaw-skills/tree/main/skill-sync](https://github.com/rkethamakka/openclaw-skills/tree/main/skill-sync)*
