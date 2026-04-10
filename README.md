# 🌅 Sunrise Realty — PWA Dashboard v4.1

> A complete Progressive Web App for managing real estate team performance, leads, and daily tracking — powered by Google Sheets as a database.

---

## 📁 Complete File Structure

```
sunrise-realty/
├── index.html        ← Main PWA frontend (single-file app, ~1600 lines)
├── code.gs           ← Google Apps Script backend API (~530 lines)
├── manifest.json     ← PWA manifest (standalone mode, installable)
├── sw.js             ← Service Worker v4 (offline caching, no CDN caching)
├── icon-192.png      ← App icon 192×192px
├── icon-512.png      ← App icon 512×512px
└── README.md         ← This documentation
```

---

## 🗄️ Complete Database Schema (Google Sheets)

### Sheet: `Users`
| Col | Header | Type | Example | Notes |
|-----|--------|------|---------|-------|
| A | UserID | String | `SR1X2Y3Z4` | Auto-generated unique ID |
| B | Username | String | `kunal` | Lowercase, unique login key |
| C | Password | String | `pass1234` | Plain text (sheet is private) |
| D | Role | String | `Admin` / `Agent` | Controls access level |
| E | Designation | String | `Sales` / `Telecaller` / `Digital` | Controls form fields |
| F | FullName | String | `Kunal Mhetre` | Display name |
| G | Phone | String | `9876543210` | 10-digit mobile |
| H | Email | String | `kunal@gmail.com` | Optional |
| I | Status | String | `active` / `locked` | Admin-controlled |
| J | Team | String | `Alpha Team` | Optional team assignment |

### Sheet: `Sheet1` (Daily Tracking)
| Col | Header | Type | Notes |
|-----|--------|------|-------|
| A | Timestamp | String | IST datetime when submitted |
| B | EmployeeName | String | Display name at time of submission |
| C | UserID | String | Unique ID (dedup key) |
| D | Username | String | Login username |
| E | Designation | String | Sales / Telecaller / Digital |
| F | TotalCalls | Number | Sales/Telecaller only |
| G | ConnectedCalls | Number | Sales/Telecaller only |
| H | Visits | Number | Sales/Telecaller only |
| I | Bookings | Number | Sales/Telecaller only |
| J | CreatedPosts | String | Digital only |
| K | PostCounts | String | Digital only |
| L | PostUploads | Number | Digital only |
| M | TargetsCompleted | String | Yes / No |
| N | Email | String | Optional |
| O | PositiveClients | Number | Count |

### Sheet: `Sheet2` (Positive Leads)
| Col | Header | Type | Notes |
|-----|--------|------|-------|
| A | Timestamp | String | IST datetime |
| B | EmployeeName | String | Agent name |
| C | UserID | String | Unique ID |
| D | Username | String | Login username |
| E | ClientName | String | Client full name |
| F | ContactNo | String | 10-digit phone |
| G | Feedback | String | Latest feedback |
| H | Email | String | Agent email |
| I | ScheduledDate | String | YYYY-MM-DD |
| J | ScheduledTime | String | HH:MM |
| K | FollowUpCount | Number | Increments on each follow-up |
| L | FeedbackHistory | JSON String | Array of {ts, text} objects |

### Sheet: `Settings`
| Key | Default Value | Description |
|-----|--------------|-------------|
| Call_Target | 50 | Daily call target per agent |
| Booking_Target | 2 | Daily booking target per agent |
| WA_Template | (empty) | WhatsApp message template |
| SMS_Template | (empty) | SMS message template |
| Email_Template | (empty) | Email body template |
| Email_Subject | (empty) | Email subject template |

### Sheet: `Teams` (auto-created)
| Col | Header | Type | Notes |
|-----|--------|------|-------|
| A | TeamName | String | Unique team name |
| B | Members | JSON | Array of usernames |
| C | Description | String | Optional |
| D | CreatedAt | String | IST datetime |

### Sheet: `AuditLogs` (auto-created)
| Col | Header | Notes |
|-----|--------|-------|
| A | Timestamp | IST datetime |
| B | Actor | Username who performed action |
| C | Action | Action code (LOGIN_SUCCESS, USER_ADDED, etc.) |
| D | Detail | Additional context |

**Audit Action Codes:**
- `LOGIN_SUCCESS`, `LOGIN_FAILED`, `LOGIN_BLOCKED`
- `DAILY_SUBMIT`, `LEAD_SUBMIT`
- `FEEDBACK_UPDATE`, `FOLLOW_UP`
- `USER_ADDED`, `USER_UPDATED`, `USER_LOCKED`, `USER_UNLOCKED`, `USER_REMOVED`
- `FORCE_LOGOUT`, `TEAM_CREATED`, `TEAM_UPDATED`, `TEAM_DELETED`
- `SETTINGS_UPDATED`, `BACKUP_CREATED`

### Sheet: `Sessions` (auto-created)
| Col | Header | Notes |
|-----|--------|-------|
| A | Username | Login username |
| B | Status | `active` / `force_logout` / `logged_out` |
| C | UpdatedAt | Last update timestamp |

### Sheet: `Backups` (auto-created)
| Col | Header | Notes |
|-----|--------|-------|
| A | BackupID | Unique ID (e.g. BK1X2Y3Z) |
| B | Timestamp | When backup was created |
| C | DataType | Always "FULL" |
| D | Data | JSON snapshot of all sheets |

---

## 🚀 Deployment Steps

### Step 1 — Google Apps Script
1. Open your Google Sheet → **Extensions → Apps Script**
2. Delete existing code, paste entire `code.gs`
3. Save (`Ctrl+S`)
4. Click **Deploy → New Deployment**
5. Type: **Web App** | Execute as: **Me** | Access: **Anyone**
6. Click **Deploy** → Copy the URL

### Step 2 — Update API URL
In `index.html`, line ~475, update:
```javascript
const API = "https://script.google.com/macros/s/YOUR_ID/exec";
```

### Step 3 — Update Users Sheet Headers
Add row 1 headers to Users sheet:
```
UserID | Username | Password | Role | Designation | FullName | Phone | Email | Status | Team
```

### Step 4 — Host the PWA
| Option | Steps | Cost |
|--------|-------|------|
| **GitHub Pages** | Push to repo → Settings → Pages | Free |
| **Netlify** | Drag folder at netlify.com | Free |
| **Vercel** | `npx vercel` in folder | Free |
| **Live Server** | VS Code extension | Local only |

> ⚠️ PWA features (installability, service worker) require **HTTPS**. GitHub Pages and Netlify both provide HTTPS automatically.

---

## 👤 Roles & Permissions

| Feature | Agent | Admin |
|---------|:-----:|:-----:|
| View own dashboard | ✅ | ✅ |
| Submit daily report | ✅ | ✅ |
| Add positive leads (multi-client) | ✅ | ✅ |
| Schedule leads with date & time | ✅ | ✅ |
| Add follow-ups to clients | ✅ | ✅ |
| Update client feedback | ✅ | ✅ |
| View feedback history | ✅ | ✅ |
| View all team data | ❌ | ✅ |
| Filter by designation | ❌ | ✅ |
| Filter by team | ❌ | ✅ |
| Ranked performance table | ❌ | ✅ |
| Performance chart | ❌ | ✅ |
| Export Excel / PDF | ❌ | ✅ |
| User management | ❌ | ✅ |
| Create/manage teams | ❌ | ✅ |
| Audit log | ❌ | ✅ |
| Backup system | ❌ | ✅ |
| Settings & templates | ❌ | ✅ |
| Force logout others | ❌ | ✅ |
| Lock/unlock accounts | ❌ | ✅ |

---

## ✨ Features v4.1

### New in this version
| Feature | Description |
|---------|-------------|
| **Unique UserID** | Auto-generated `SR` prefix ID (e.g. `SR1X2Y3`) — prevents duplicate records |
| **Feedback History** | Full history of all feedback versions stored as JSON per client |
| **Follow-Up Counter** | Track follow-up calls per positive client with count display |
| **Schedule Date + Time** | Per-client scheduling with both date AND time pickers |
| **Export Excel** | Exports daily + leads data as `.xlsx` using SheetJS |
| **Export PDF** | Opens print-ready HTML report in new tab |
| **Backup System** | Full JSON snapshot of all sheets stored in Backups sheet |
| **Pull-to-Refresh** | Native mobile swipe-down gesture triggers data sync |
| **Refresh Button** | Manual refresh in nav with spinning animation |
| **Full Sync Time** | Shows `HH:MM:SS AM/PM` with live ticking clock |
| **Audit Log (mobile)** | Audit tab visible in mobile bottom navigation |
| **Teams (mobile)** | Teams tab visible in mobile bottom navigation |
| **Proper Padding** | 16px–28px horizontal padding on all screen sizes |
| **Range+ Fix** | Toggle logic fixed — pill turns gold when active, de-selects cleanly |
| **Submit button fix** | Buttons always visible above bottom nav bar |
| **Card overflow fix** | Lead cards constrained with `min-width:0` and `word-break` |
| **Whitespace handling** | All inputs trimmed globally in `code.gs` via `trim()` |
| **Data dedup by UserID** | Dashboard aggregates by UserID first, then username |
| **Team ranking** | Teams section shows members sorted by bookings |

### Core Features
- **Auto-sync every 5 seconds** — live data for admin
- **Heartbeat every 30s** — detects force-logout
- **PWA installable** — Add to Home Screen (Android + iOS)
- **Offline support** — local files cached by service worker
- **System fonts** — San Francisco (iOS) / Roboto (Android)
- **Button ripple effects** — visual feedback on all buttons
- **Animated loader** — branded ring + dots on startup
- **Smooth section transitions** with CSS animations

---

## 🐛 All Bugs Fixed

| Bug | Fix Applied |
|-----|------------|
| `classList` null error | All DOM queries wrapped in null checks |
| Tailwind CDN CORS in SW | SW skips all cross-origin requests |
| `setHeader` not a function | Removed — Apps Script doesn't support it |
| Duplicate records | Aggregation keyed by `UserID` (unique), not display name |
| Refresh icon invisible on mobile | Kept visible in nav, plus pull-to-refresh |
| Content flush against screen edges | `--page-pad` variable (16px mobile, 28px desktop) |
| Submit buttons hidden behind tab bar | `min-height:48px` + proper bottom padding `calc(tab+safe+20px)` |
| Lead cards overflowing | `width:100%;min-width:0;word-break:break-word` on cards |
| Range+ toggle broken | Replaced with `isRangeOpen` boolean state |
| Teams/Audit missing on mobile | Added dedicated tabs in bottom nav bar |
| No full sync timestamp | `fmtTime()` with `HH:MM:SS AM/PM` format + live clock |
| Extra whitespace in data | Global `trim()` on all inputs in both frontend and backend |

---

## 📱 PWA Installation

**Android (Chrome):**
1. Open app in Chrome
2. Tap 3-dot menu → "Add to Home Screen"
3. Confirm — app appears on home screen

**iOS (Safari):**
1. Open app in Safari
2. Tap Share button → "Add to Home Screen"
3. Confirm — works in standalone mode

---

## 🔧 Message Template Placeholders

| Placeholder | Replaced With |
|-------------|--------------|
| `{name}` | Client's name |
| `{phone}` | Client's phone number |
| `{agent}` | Agent's name |

---

## 📊 Data Flow

```
Agent fills form (PWA)
      ↓
index.html → POST to Apps Script API
      ↓
code.gs writes row to Google Sheet
      ↓
5-second auto-sync fetches all data
      ↓
Admin sees live dashboard
```

---

*Built for Sunrise Realty, Pune — v4.1 © 2026*
