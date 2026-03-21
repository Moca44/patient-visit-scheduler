# Empathy Scheduler — Pending Items
## Updated: 2026-03-21 | Based on v25.27 | Doc: R0.S3.0 | Generated: Saturday, March 22, 2026 — Start of Session 3

---

## VERSIONING CONVENTION (updated)

- **D-0.x.xx** — development / family beta testing (current)
- **1.x.xx** — first public release
- Format: `RELEASE.VERSION.UPDATE`
- Dev versions prefix: `D-`
- Current version: **D-0.25.27** (untested)
- Last tested stable: **D-0.25.24**

---

## BUGS (fix in next session)

### Critical
1. **Duplicate SA login** — SA can log in from two devices simultaneously. Second login should be blocked: "משתמש זה כבר מחובר ממכשיר אחר"
2. **Wrong password shows no error** — SA login with wrong password shows no error message, button appears unresponsive

### High Priority
3. **Panel stuck after user creation** — after creating a user, "חזרה" button does nothing, panel stays open. `renderAdminUsers()` skips reset when panel is open
4. **"ביטול שינויים" in template does nothing** — button doesn't restore from snapshot or clear `_editWeekDayWins`
5. **Dirty flag not cleared on revert** — if user changes a value then changes it back to original, dirty flag stays on
6. **Time picker order wrong** — hours on right, minutes on left (RTL issue). Should be HH on left, MM on right matching normal clock display `HH:MM`

### Medium Priority
7. **Template changes affect calendar** — `_editWeekDayWins` fix may not be fully working, needs verification
8. **Changed row highlight missing** — amber left border on modified rows not rendering in calendar day edit
9. **+/- buttons in day calendar have no label** — unclear what they control (capacity?). Also missing equivalent for waitlist (ממתינים)
10. **Time display in template** — still shows two separate dropdowns instead of "09:00" text with click-to-edit

### Low Priority
11. **Stale active users** — users with `status='active'` remain after closing tab without logout. `beforeunload` may not fire reliably
12. **`g` not accessible from console** — debug difficulty. Expose as `window._g` when `debugMode=true`

---

## FEATURES (plan for future versions)

### Next Version Priority
1. **Version check + force reload** — store `APP_VERSION` in `cfg.app_version`. Polling checks every 30s. If mismatch → show message + force reload after 3s. Prevents old version damaging new DB schema
2. **Overlap validation in template** — prevent adding windows with overlapping times. Show error if overlap detected
3. **Single-level UNDO in template edit** — save snapshot before each window change (add/delete/time). One "בטל פעולה אחרונה" button restores last state. Stack clears on save/cancel
4. **Factory reset** — "החזר להגדרות יצרן" button in template tab (and possibly constants tab). Requires confirmation. Reads from `cfg_factory` table

### Architecture
5. **`cfg_factory` table** — stores hardcoded factory defaults. Used for factory reset and as reference in constants tab display
6. **Polling interval as constant** — add `pollingInterval` to settings (range 10–120s, default 30s)
7. **Debug helpers** — expose `window._g = g` when `debugMode=true`

### UI Improvements
8. **Tab dirty indicator** — amber border on tabs with unsaved changes (tab1=calendar, tab2=template)
9. **Row highlight for changes** — amber left border on modified window rows
10. **Confirm modal everywhere** — replace all native `confirm()` calls with custom styled modal
11. **Report row highlighting** — visual distinction for changed rows

### Future / Nice to Have
12. **UNDO for saved settings** — restore `weekDayWins` to state before last "שמור הגדרות" (uses existing `_prevWeekDayWins`)
13. **Realtime subscriptions** — replace polling with Supabase Realtime when stable
14. **Log auto-cleanup** — keep only last 1000 entries

---

## SQL TO RUN AT START OF NEXT SESSION

```sql
-- Already run (for reference)
ALTER TABLE cfg ADD COLUMN session_timeout INTEGER DEFAULT 15;
ALTER TABLE cfg ADD COLUMN warning_percent INTEGER DEFAULT 80;
ALTER TABLE cfg ADD COLUMN debug_mode BOOLEAN DEFAULT false;
ALTER TABLE cfg ADD COLUMN booking_days INTEGER DEFAULT 14;
ALTER TABLE cfg ADD COLUMN report_page_size INTEGER DEFAULT 15;
ALTER TABLE cfg ADD COLUMN report_print_size INTEGER DEFAULT 40;
ALTER TABLE cfg ADD COLUMN template_updated_at TIMESTAMPTZ DEFAULT NOW();

-- Run at start of NEXT session
CREATE TABLE cfg_factory AS SELECT * FROM cfg WHERE id = 1;
ALTER TABLE cfg ADD COLUMN app_version TEXT DEFAULT 'D-0.25.27';
ALTER TABLE cfg ADD COLUMN polling_interval INTEGER DEFAULT 30;
```

---

## DOCUMENTS TO UPDATE

- [ ] CLAUDE_BRIEFING.md — update with new versioning convention + pending items
- [ ] SRS_Empathy_Scheduler.docx — after v26.0 is stable
- [ ] ADMIN_GUIDE.docx — after v26.0 is stable
- [ ] USER_GUIDE.docx — after v26.0 is stable
- [ ] README.md — after v26.0 is stable

---

## DEBUG ENVIRONMENT (5 windows)

1. **Claude** — code changes and debugging
2. **GitHub** — upload index.html, manage /docs
3. **Supabase** — Table Editor, SQL Editor, check data
4. **App** — https://moca44.github.io/patient-visit-scheduler/
5. **Downloads** — temporary storage, rename file to index.html before upload

---

## USEFUL DEBUG COMMANDS

```javascript
// Force close stuck user panel
document.getElementById('aUserNewPanel').style.display='none';
document.getElementById('aUsersListView').style.display='block';

// Reset all active users (run in Supabase SQL Editor)
UPDATE users SET status = 'inactive' WHERE status IN ('active', 'active_admin');

// Reset specific user by email
UPDATE users SET status = 'inactive' WHERE email = 'user@example.com';

// Reset SA password
UPDATE users SET pw_hash = null WHERE id = 'sa';

// Delete all non-SA users
DELETE FROM users WHERE id != 'sa';
```
