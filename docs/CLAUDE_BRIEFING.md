# Empathy Scheduler — Claude Project Briefing
## Version: v25.27 | Checkpoint: 2026-03-19

---

## CLAUDE BEHAVIOR RULES

- **Always respond in English** regardless of what language the user writes in
- **Never show code** unless it requires manual execution by the user (e.g. SQL commands to run in Supabase)
- **Version naming**: `v25.X` format — file saved as `empathy_v25.XX.html`
- **Loading spinner** always shows version: `טוען נתונים... (vXX.XX)`
- **Version bump**: increment minor number each deploy. Major bump (v26.0) only after testing confirms stability
- **Don't code until told to** — collect all change requests first, then ask for approval

### Version Size Rules
- **Small** (1–3 focused fixes) → code immediately, one version
- **Medium** (4–8 changes, low risk) → one version
- **Large** (many changes, risky rewrites) → split into 2–3 versions, each independently testable
- Never bundle untested large changes together

### Deployment Process
1. User downloads file from Claude
2. User uploads to GitHub as `index.html` in repo `moca44/patient-visit-scheduler`
3. GitHub Pages serves at `https://moca44.github.io/patient-visit-scheduler/`
4. Hard refresh: **Ctrl+Shift+R**

---

## PROJECT OVERVIEW

**App:** Empathy Scheduler — patient visit coordination system  
**Type:** Single HTML file, RTL Hebrew, ES5 JavaScript  
**Hosting:** GitHub Pages (free)  
**Database:** Supabase (PostgreSQL, Frankfurt region)  
**Author:** Moshe Carmeli `<moshe.carmeli@gmail.com>`  
**License:** CC BY-NC 4.0  
**Built with:** Claude.ai (Anthropic)

**Use case:** A family managing visits to a home-care patient. Admin sets time windows, users book visits.

---

## TECHNICAL ARCHITECTURE

### File Structure
- Single `index.html` file
- **Script block 1** (lines ~580–860): Supabase REST helpers + DB layer (`DB` object)
- **Script block 2** (lines ~862–end): App IIFE — all UI, logic, event listeners

### Key Global Variables
```javascript
cfg          // system config {patient, defCap, defDur, maxWl, sessionTimeout, warningPercent, 
             //                debugMode, bookingDays, reportPageSize, reportPrintSize}
weekDayWins  // {0..6: [{id, start, cap, dur, maxWl}]} — weekly template
_editWeekDayWins  // working copy for template editor (never affects calendar until saved)
slotsDB      // {"YYYY-MM-DD_winId": {cap, bk:[], wl:[]}}
dayWinsDB    // {"YYYY-MM-DD": [{id, start, cap, dur}]} — per-day overrides
users        // [{id, name, phone, email, pwHash, isAdmin, isSA, deletable, blocked, status}]
bookings     // [{user, cnt, id, ds, twId, status:'ok'}]
wlist        // [{user, cnt, id, ds, twId}]
lg           // activity log array
cu           // current user object
sa           // current SA object
_sysMode     // 'maintenance' | 'active'
```

### DB Layer (direct REST fetch — no Supabase JS client)
```javascript
DB.loadAll()          // runs initDB() first, then loads everything
DB.initDB()           // seeds defaults on first run if tables empty
DB.saveCfg()
DB.loadCfg()
DB.saveUser(u)
DB.loadUsers()
DB.deleteUser(id)
DB.saveBooking(entry)
DB.deleteBooking(id)
DB.saveWlist(entry)
DB.deleteWlist(id)
DB.saveSlot(dstr,wid)
DB.loadSlots()
DB.saveDayWins(dstr)
DB.loadDayWins()
DB.saveLog(msg,type)
DB.loadLog()
DB.saveWeekDayWins()
DB.loadWeekDayWins()
```

### Supabase Connection
```javascript
const SUPA_URL = 'https://yulsydrkqgehqoopuopb.supabase.co';
const SUPA_KEY = 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...'; // anon key
```
Region: Frankfurt. RLS enabled with `allow_all` policies on all tables.

---

## SUPABASE SCHEMA

### Tables
| Table | Key Columns |
|-------|-------------|
| `cfg` | id(1), patient, contact, def_cap, def_dur, max_wl, sys_mode, session_timeout, warning_percent, debug_mode, booking_days, report_page_size, report_print_size, template_updated_at |
| `users` | id, name, phone, email, pw_hash, is_admin, is_sa, deletable, blocked, status, contact |
| `week_day_wins` | id, dow, start_time, cap, dur, max_wl, sort_order |
| `slots_db` | id(date_wid), date_str, win_id, cap |
| `bookings` | id, user_id, user_name, user_email, date_str, win_id, cnt, status |
| `wlist` | id, user_id, user_name, user_email, date_str, win_id, cnt |
| `day_wins_db` | id, date_str, win_id, start_time, cap, dur, sort_order |
| `activity_log` | id, message, log_type, created_at |

### SQL Commands Run (for reference)
```sql
ALTER TABLE cfg ADD COLUMN session_timeout INTEGER DEFAULT 15;
ALTER TABLE cfg ADD COLUMN warning_percent INTEGER DEFAULT 80;
ALTER TABLE cfg ADD COLUMN debug_mode BOOLEAN DEFAULT false;
ALTER TABLE cfg ADD COLUMN booking_days INTEGER DEFAULT 14;
ALTER TABLE cfg ADD COLUMN report_page_size INTEGER DEFAULT 15;
ALTER TABLE cfg ADD COLUMN report_print_size INTEGER DEFAULT 40;
ALTER TABLE cfg ADD COLUMN template_updated_at TIMESTAMPTZ DEFAULT NOW();
```

---

## CURRENT STATE

### Last Stable Tested Version
**v25.24** — panels don't close during polling

### Latest Version (UNTESTED)
**v25.27** — major update, needs full testing before v26.0 bump

### What v25.27 Contains (vs v25.24)
- `_editWeekDayWins` working copy — template changes don't affect calendar until saved
- Template conflict detection via `template_updated_at`
- `initDB()` — auto-seeds DB on first run
- Maintenance mode: blocks user login, allows registration with message
- Case insensitive email (lowercase everywhere)
- `bookingDays` constant (7–31) replaces hardcoded 14
- `reportPageSize` + `reportPrintSize` constants saved to DB
- Reports: HTML `<table>` with borders, vertical headers, pagination, print CSS
- Button flow: "בצע רישום" → "צור משתמש נוסף" (resets form fully)
- Panel stays open when switching tabs (polling fix)
- Timeout warning: splits hours/minutes from cfg

---

## PENDING ITEMS (not yet coded)

### Bugs
1. Time picker in template — show "09:00" as text, show two dropdowns only on click
2. `cancelDayEdit` removed from `switchTab` — verify calendar day edit still cancels properly
3. SA display in login screen after SA change — verify shows new SA email
4. `beforeunload` status update — verify works on tab close
5. Log cleanup — auto-delete entries older than 1000 records

### Features
6. Report row highlighting — amber left border on changed rows
7. Custom confirm modal used everywhere (replace all native `confirm()` calls)
8. Tab dirty indicator — amber border on tabs with unsaved changes
9. `doSaveSettings` — show "צור משתמש נוסף" button properly after save
10. Realtime subscriptions — polling currently every 30s, Supabase Realtime not yet wired
11. `report_page_size` / `report_print_size` — verify saved/loaded from DB correctly
12. Print — test Ctrl+P output looks correct

### Architecture / Future
13. Version 26.0 — major bump after v25.27 is tested and stable
14. SRS document update — after v26.0 is stable
15. Admin Guide + User Guide update
16. README update with v26.0 changelog

---

## ADMIN ROLES

| Role | Access |
|------|--------|
| SA (Super Admin) | Full access, can change SA email, reset system |
| Admin | Manage users, calendar, settings (not SA functions) |
| User | Book visits, manage own profile |

**SA default:** `admin@family.com` / id: `sa` / `deletable: false`  
All other users: `deletable: true`  
Admin cannot delete themselves

---

## PASSWORD HASHING
```javascript
function H(pw){
  var h=5381;
  for(var i=0;i<pw.length;i++){h=((h<<5)+h)^pw.charCodeAt(i);h=h>>>0}
  return h.toString(16)+'_'+btoa(pw.split('').reverse().join('')).slice(0,10)
}
```
Requirements: 8+ chars, uppercase, lowercase, special character.

---

## SYSTEM MODES
- **maintenance** (default) — only admins can log in; users can register but not enter
- **active** — all users can log in and book visits

---

## KEY UI SCREENS
| Screen ID | Description |
|-----------|-------------|
| `vLoading` | Spinner shown on startup |
| `vLogin` | Login / register |
| `vUser` | User calendar view |
| `vProfile` | User profile edit |
| `vAdmin` | Admin panel (tabs: dashboard, calendar, template, users, reports, constants) |
| `vSetPw` | Set password (first login or reset) |
| `vReg` | New user registration |

### Admin Tabs
| Tab | ID | Content |
|-----|----|---------|
| Dashboard | tab0/tp0 | Overview stats |
| Calendar | tab1/tp1 | 14-day calendar + day edit |
| Template | tab2/tp2 | Weekly window template |
| Users | tab3/tp3 | User management |
| Reports | tab5/tp5 | 5 reports with print |
| Constants | tab4/tp4 | System settings |

---

## GITHUB REPO
- **URL:** `https://github.com/moca44/patient-visit-scheduler`
- **Branch:** `main`
- **File:** `index.html`
- **Docs folder:** `/docs` (SRS, Admin Guide, User Guide)
- **License:** `LICENSE` (CC BY-NC 4.0)

## DEBUG ENVIRONMENT

Minimum 5 browser windows/tabs needed:
1. **Claude** — code changes and debugging
2. **GitHub** — upload index.html, manage /docs
3. **Supabase** — Table Editor, SQL Editor, check data
4. **App** — https://moca44.github.io/patient-visit-scheduler/
5. **Download** - Temporary storage, renaming to index.html
