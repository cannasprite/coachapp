# COACH App — Claude Code Context

## About the user
- **Name:** Sprite, 34F, wellness competitor
- **Goals:** Fat loss (starting ~137 lbs, goal ~125 lbs), muscle building, managing psoriasis + psoriatic arthritis
- **Daily nutrition targets:** ~1,250 cal / 130g protein
- **Workout split:** Mon=Push, Tue=Glute/Ham, Wed=Pull, Thu=Core, Fri=Quad/Calf, Sat=Cardio, Sun=Rest

---

## What this app is
Single-file vanilla JS/HTML PWA — everything lives in `index.html`. No frameworks, no build tools. All data stored in `localStorage`. Anthropic API integrated for AI coaching features.

**File:** `index.html` (~5,900 lines)

---

## Tech stack
- **Fonts:** Google Fonts — Syne (headings) + Instrument Sans (body)
- **CSS:** Custom properties for theming, no framework
- **API:** Anthropic — `claude-sonnet-4-20250514` (complex) + `claude-haiku-4-5-20251001` (fast/cheap)
- **Storage:** localStorage only

---

## Design system (dark theme)
```css
--bg: #0a0a0a
--surface: #141414
--surface2: #1f1f1f
--border: rgba(255,255,255,0.08)
--border-accent: rgba(184,255,87,0.2)
--text: #f0f0f0
--muted: #888
--accent: #b8ff57      /* lime green — primary action color */
--night: #7b6fff       /* purple — night/sleep related */
--pink: #ff7eb3        /* pink — body/health related */
--warn: #ff9f43        /* orange — warnings/flags */
--glass: rgba(255,255,255,0.03)
```
- Cards: `linear-gradient(160deg, #1e1e1e 0%, #181818 100%)` with subtle inset shadow
- Nav: `backdrop-filter: blur(20px)` glass effect
- Tab bar: pill-style card, stacked icon + label tabs
- Max-width: 480px centered

---

## App structure (tabs)
1. **Home** (`tab-today`) — AI Daily Focus (data-driven, yesterday-aware), 3-icon check-in row (☀️🌙⚖️ with dot indicators), glance grid (Body/Goals/Check-in)
2. **Check-in** (`tab-checkin`) — 3 subtabs: Morning (mood/energy/sleep), Night (close-out + habits), Physical (weight/body). Each has an inline 📓 journal drop-down per section.
3. **Body** (`tab-body`) — 2 subtabs: Train (workout day selector, exercise blocks, cardio with 📷 photo scan), Eat (food log, AI analysis, water tracker, recipes)
4. **Progress** (`tab-progress`) — PRs with 30-day delta, weight trend chart, this week grid, workout volume chart (8 weeks), training streak, cal/protein 30-day chart, sleep/mood 14-day chart, cardio history, current plan
5. **Goals** (`tab-goals`) — Goal tracking
6. **Chat** (`tab-chat`) — Direct coach chat with Claude (persistent conversation)
7. **Settings** (`tab-more`) — API key, display name, goal weight, recipes, weekly coach report

---

## Key functions
| Function | Purpose |
|---|---|
| `saveMorning()` | Saves morning check-in, calls `generateMorningFocus()` + `checkMilestones()` |
| `saveNight()` | Saves night check-in + habits, calls `checkMilestones()` |
| `savePhysical()` | Saves physical check-in (weight, body feel, photo flag) |
| `saveWorkout()` | Saves workout, calls `generateWorkoutNote()`, `checkNewPR()`, `checkMilestones()` |
| `generateDailyFocus()` | Haiku AI call with yesterday's data + 7-day nutrition avg, cached per day |
| `generateMorningFocus()` | Haiku AI call for morning focus card, cached per day |
| `generateDietAdherence()` | Diet feedback with sleep/workout/flagged food context |
| `generateCoachReport()` | Weekly report with avg cal/protein, weight trend, flagged foods, mood notes |
| `logFood()` | Logs food with remaining budget context |
| `sendMsg()` | Chat with live today context in system prompt |
| `showBadgeToast(msg)` | Shared toast for badges + PRs |
| `checkMilestones()` | Checks all badge conditions |
| `checkNewPR(exercise, weight)` | Checks and celebrates new PRs |
| `toggleFoodFlag(idx)` | Toggles orange flag on food entry |
| `quickLogRecipe(idx)` | One-tap log from saved recipe |
| `buildTodayContext()` | Builds context string from current form state |
| `renderProgress()` | Renders all Progress tab sections (PRs, charts, streaks, cardio) |
| `claudeVisionCall(model, system, text, base64, mimeType)` | Claude API call with image content for photo scanning |
| `cardioScanPhoto(exerciseName)` | Opens camera, sends to Claude Vision, pre-fills cardio fields |
| `ciToggleJournal(section)` | Toggles inline journal drop-down per check-in section |
| `ciSaveJournal(section)` | Saves journal note to `coach_journal_{section}_{date}` |
| `homeUpdateCheckinStatus()` | Updates ci-dot indicators on Home check-in row |

---

## Features already built
- Full visual overhaul (gradient cards, glassy nav, stacked tab bar)
- Morning AI Focus card (haiku, cached per day, personalized)
- Daily Focus card on Home (haiku, data-driven: yesterday's mood/energy/workout + 7-day nutrition averages)
- Welcome overlay with personalized AI morning greeting
- Goodnight AI message
- Smarter AI prompts — every AI call includes live context (mood, energy, sleep, nutrition totals, weight trend)
- Food trigger flagging — ⚑ button, orange when flagged, feeds into AI reports
- Quick-log from saved recipes
- Milestone badges — 7-day streak, 30-day streak, 5 lbs lost, 10 workouts, 50 workouts
- PR celebration toast with 30-day delta in Progress tab
- `buildTodayContext()` reusable context helper
- Diet adherence AI card in Eat tab
- Water cup tracker (16 cups) in Eat tab
- Streamlined Morning: 3 fields only + pre-gym checklist + 1 save button
- Streamlined Night: 3 fields + habits + collapsed body check-in + 1 "Close Out" button
- Rest day fix: shows recovery card + "Save Rest Day" button
- Inline journal drop-downs per check-in section (Morning/Night/Physical), with tag pills
- Compact 3-icon check-in row on Home with dot indicators (filled = done)
- Progress tab: weight trend, week grid, workout volume chart, training streak, cal/protein chart, sleep/mood chart, cardio history with summary
- Cardio photo scan: 📷 button opens camera, Claude Vision (Haiku) extracts duration/notes and pre-fills fields

---

## Coding conventions
- **No frameworks** — vanilla JS and HTML only
- **Single file** — all CSS, JS, and HTML stays in `index.html`
- **CSS variables** — always use the design tokens above, never hardcode colors
- **localStorage** — all persistence goes here, no backend
- **Mobile-first** — max-width 480px, touch-friendly tap targets
- **AI calls** — always include `buildTodayContext()` in prompts for personalization
- **Haiku for speed** — use `claude-haiku-4-5-20251001` for quick/cheap calls, Sonnet for complex ones
