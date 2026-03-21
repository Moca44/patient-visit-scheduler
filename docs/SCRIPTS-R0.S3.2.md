# Empathy Scheduler — Session Scripts
## Version: R0.S3.2 | Generated: Saturday, March 22, 2026 — Start of Session 3

---

## HELP / COMMANDS
If user says: "פקודות" / "תהליכים" / "מה אתה יכול לעשות" / "עזרה" / "commands" / "help"
→ Reply with this list:

| Script | Purpose |
|--------|---------|
| CHECKPOINT | Create a session checkpoint — verify docs, update versions, generate files, optional archive |
| VERIFY DOCS | Check all uploaded documents are the latest version — warn and regenerate if not |

---

## SCRIPT 1 — CHECKPOINT (יצירת נקודת ביקורת)

Run at end of session or mid-session to reduce Claude context load.

### Step 1 — Verify documents
- Ask user to upload all current session documents
- Run VERIFY DOCS sub-process (see Script 2)
- Confirm all docs are current before proceeding

### Step 2 — Define new version numbers
Current naming convention: `RELEASE.SESSION.DOC_VERSION`
- RELEASE = 0 (unchanged until public release)
- SESSION = current session number (e.g. S3)
- DOC_VERSION = 0 for new session, increment for updates within session
- Example: `R0.S3.0`

Ask user to confirm: "We are ending Session X. New doc version will be R0.SX.0 — confirm?"

### Step 3 — Update and regenerate all documents
Regenerate with new version number in filename AND internal header:
- `CLAUDE_BRIEFING-R0.S3.0.md`
- `PENDING_ITEMS_vXX_XX-R0.S3.0.docx`
- `PENDING_ITEMS-R0.S3.0.md`
- `SESSION_HANDOVER_CHECKLIST-R0.S3.0.docx`
- `SESSION_TRANSITION_GUIDE-R0.S3.0.docx`
- `SRS_Empathy_Scheduler_vXX_XX-R0.S3.0.docx`
- `ROADMAP_Empathy_Scheduler-R0.S3.0.docx`
- `SCRIPTS-R0.S3.0.md` (this file)
- `DOC_INDEX-R0.S3.0.docx`

### Step 4 — Update HTML version number
Remind user: "Current app file is empathy_vXX.XX.html — rename to index.html before uploading to GitHub root."

### Step 5 — Archive decision
Ask user: **"האם ליצור ארכיון לסשן זה? (כן רק במעבר סשן משמעותי)"**

If YES → archive the CURRENT (new) session docs — they represent the start state of this session:

  **What to archive:** The newly generated R0.SX.0 files (not the old ones)
  **Why:** Archive = entry state of session X = exit state of session X-1. This captures every change made.

  **Archive structure** (2 items only):
  - `DOC_INDEX-R0.SX.0.docx` — open file, readable directly on GitHub
  - `ARCHIVE-R0.SX.zip` — all other files packed together

  **Why this structure:** Index is visible without opening ZIP. ZIP avoids recursive filename update problem.

  **Files to include in ZIP:**
  1. CLAUDE_BRIEFING-R0.SX.0.md
  2. PENDING_ITEMS_vXX_XX-R0.SX.0.docx
  3. PENDING_ITEMS-R0.SX.0.md
  4. SESSION_HANDOVER_CHECKLIST-R0.SX.0.docx
  5. SCRIPTS-R0.SX.Y.md (latest .Y version)
  6. SESSION_TRANSITION_GUIDE-R0.SX.0.docx
  7. SRS_Empathy_Scheduler_vXX_XX-R0.SX.0.docx
  8. ROADMAP_Empathy_Scheduler-R0.SX.0.docx

  **Steps:**
  1. Download all new files from Claude
  2. Create ZIP of files 1-8 above, rename to: `ARCHIVE-R0.SX.zip`
  3. Go to github.com/moca44/patient-visit-scheduler → docs folder
  4. Click Add file → Create new file
  5. Type in name field: `archive/R0.S3/.gitkeep` (replace X with session number)
  6. Click Commit changes
  7. Go to `/docs/archive/R0.S3/` → Add file → Upload files
  8. Upload ONLY: `DOC_INDEX-R0.SX.0.docx` and `ARCHIVE-R0.SX.zip`
  9. Click Commit changes

If NO → skip archive, continue to Step 6.

### ⚠️ ZIP Download Note
When downloading multiple files from Claude as a ZIP:
- Claude cannot control the ZIP filename — it downloads as a generic name
- IMMEDIATELY after downloading, rename the ZIP to: `ARCHIVE-R0.SX.zip` (use correct session number)
- Then extract and upload contents to GitHub archive subfolder
- Never upload the ZIP itself to GitHub — GitHub does not extract ZIP files automatically

### Step 6 — Upload to GitHub
Remind user to upload all regenerated docs to `/docs` folder on GitHub:
- Go to: github.com/moca44/patient-visit-scheduler → docs folder
- Add file → Upload files
- Upload all files listed in Step 3
- Commit changes
- Then upload index.html to repo ROOT (not docs)

### Step 7 — Opening prompt for next session
Tell user to paste this at the start of Session X+1:

---
*"This is Session [N]. Please read all uploaded documents including development agreements.
Confirm: session number, app version, doc version, and first task.
Respond in English."*
---

---

## SCRIPT 2 — VERIFY DOCS (בדיקת מסמכים)

Run at start of checkpoint OR anytime user wants to verify document currency.

### Process
1. Ask user to upload all current documents
2. For each document — check internal header for version/generated timestamp
3. Compare against expected current version (from CLAUDE_BRIEFING or user confirmation)
4. For each document output one of:
   - ✅ `FILENAME` — current (R0.SX.X confirmed)
   - ⚠️ `FILENAME` — version mismatch or missing timestamp — regenerating...
5. Regenerate any outdated documents with correct version number
6. Deliver updated files and confirm completion

---

## VERSION HISTORY
| Version | Session | Notes |
|---------|---------|-------|
| R0.S2.0 | Session 2 | Initial scripts defined — timestamp naming, checkpoint process, archive rules |
| R0.S3.0 | Session 3 | New naming convention: RELEASE.SESSION.DOC_VERSION replaces timestamp |
