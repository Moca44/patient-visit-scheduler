# Project Checkpoint Guide
## For Claude + Project Owner

---

## WHAT IS A CHECKPOINT?

A checkpoint is a snapshot of project state created before:
- Usage limit reset
- Long break between sessions
- Major version milestone
- Before a risky large change

It lets you resume work in a new Claude session with full context — as if the conversation never ended.

---

## WHEN TO CREATE A CHECKPOINT

**Triggers:**
- Usage approaching limit (warn at ~85%)
- End of a productive session with many changes
- Before a major version bump
- Before a large risky refactor
- After a stable tested version

**Rule of thumb:** If losing the conversation history would cost more than 30 minutes to reconstruct — create a checkpoint.

---

## WHAT A CHECKPOINT CONTAINS

### 1. Claude Behavior Rules
- Response language
- Version naming convention
- File naming convention
- How many changes per version (small/medium/large)
- What NOT to do (e.g. don't show code unless for manual execution)
- Deployment process step by step

### 2. Project Overview
- What the app does, who uses it
- Technology stack
- Hosting method
- Repository URL

### 3. Technical Architecture
- Key variables and their purpose
- Main functions and what they do
- File/code structure
- External services (DB, APIs) with connection details

### 4. Database Schema
- All tables and key columns
- Any SQL commands run manually (ALTER TABLE etc.)
- Important constraints or policies

### 5. Current State
- Last **tested** stable version
- Latest version (may be untested)
- What changed between them

### 6. Pending Items
- Known bugs (numbered)
- Features requested but not coded
- Architecture improvements for future
- Documents that need updating

### 7. Decisions Made
- Design decisions and why (e.g. "we chose CC BY-NC license because...")
- Conventions agreed on (e.g. "always respond in English")
- Things explicitly rejected and why

---

## HOW TO USE THE CHECKPOINT

**At start of new session:**
1. Open a new Claude conversation
2. Paste the entire briefing document as your first message
3. Add: *"This is our project briefing. Please confirm you understand the current state and behavior rules before we continue."*
4. Claude will confirm and you can resume immediately

**The briefing replaces hours of context-rebuilding.**

---

## CHECKPOINT CREATION PROCESS

### Step 1 — Decide to checkpoint
When usage reaches ~85%, or naturally at end of session.

### Step 2 — Finalize current version
- Ensure latest code compiles (syntax check)
- Note whether it's tested or untested
- Don't bump major version unless tested

### Step 3 — Ask Claude to generate:
1. **CLAUDE_BRIEFING.md** — full technical briefing (see template below)
2. **PENDING_ITEMS.md** — optional separate list if long
3. **CHANGELOG entry** — what changed in this session

### Step 4 — Save files
- Download from Claude
- Upload to `/docs` folder in GitHub repo
- Keep locally as backup

### Step 5 — Note what NOT to continue
Write a one-line note: *"Do not bump to v26.0 until v25.27 is tested."*

---

## BRIEFING DOCUMENT TEMPLATE

```markdown
# [Project Name] — Claude Project Briefing
## Version: vX.XX | Checkpoint: YYYY-MM-DD

---
## CLAUDE BEHAVIOR RULES
[List all agreed rules]

---
## PROJECT OVERVIEW
[What it does, who uses it, tech stack]

---
## TECHNICAL ARCHITECTURE
[Key variables, functions, file structure]

---
## DATABASE SCHEMA
[Tables, columns, SQL commands run]

---
## CURRENT STATE
- Last tested stable: vX.XX
- Latest (untested): vX.XX
- What changed between them

---
## PENDING ITEMS
[Numbered list of bugs and features]

---
## DECISIONS MADE
[Design choices and rationale]
```

---

## TIPS FOR INEXPERIENCED USERS

**You don't need to understand the code** to create a good checkpoint. You just need to tell Claude:

> *"We're approaching the usage limit. Please create a checkpoint briefing document that summarizes everything a new Claude instance would need to continue this project."*

Claude will generate it. You save it. Next session — paste it in.

**Think of it like a handover note** when a colleague takes over your work.

---

## GENERAL ADVICE FOR LONG AI PROJECTS

1. **Checkpoint often** — don't wait for the limit
2. **Test before bumping major version** — untested code is a liability
3. **One thing at a time** — small versions are easier to debug
4. **Keep a running list of pending items** — paste it to Claude at session start
5. **Save every working version** — keep file named with version number
6. **Document SQL changes** — manual DB changes are easy to forget
7. **Note what was rejected** — saves time re-discussing the same ideas

---

*This guide was created during development of Empathy Scheduler (v25.27) as a reusable template for any long-running Claude project.*
