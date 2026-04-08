# COACH App — Claude Code Context

## About the user
- **Name:** Sprite, 34F, wellness competitor
- **Goals:** Fat loss (starting ~137 lbs, goal ~125 lbs), muscle building, managing psoriasis + psoriatic arthritis
- **Daily nutrition targets:** ~1,250 cal / 130g protein
- **Workout split:** Mon=Push, Tue=Glute/Ham, Wed=Pull, Thu=Core, Fri=Quad/Calf, Sat=Cardio, Sun=Rest

---

## What this app is
Single-file vanilla JS/HTML PWA — everything lives in `index.html`. No frameworks, no build tools. All data stored in `localStorage`. Anthropic API integrated for AI coaching features.

**File:** `index.html` (~5,000+ lines)

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
1. **Today** — Morning check-in (mood/energy/sleep + pre-gym checklist) + Night close-out (mood/win/better + habits + body check-in)
2. **Train** — Workout day selector (6 days + rest), exercise blocks with sets/reps/weight, YouTube links, cardio section, AI workout note after saving
3. **Eat** — Food log with AI analysis, diet adherence AI card, water cup tracker (16 cups), daily targets + totals, saved recipes with quick-log
4. **Stats** — Weight trend chart, this week's training checklist, current plan card, personal records, cardio history
5. **Chat** — Direct coach chat with Claude (persistent conversation)
6. **Journal** — Daily entries with mood, tags, win highlights
7. **More** — Settings (API key, goals), recipes, weekly coach report

---

## Key functions
| Function | Purpose |
|---|---|
| `saveMorning()` | Saves morning check-in, calls `generateMorningFocus()` + `checkMilestones()` |
| `saveNight()` | Saves night check-in + habits, saves `lastNightCal`/`lastNightProtein`, calls `checkMilestones()` |
| `saveWorkout()` | Saves workout, calls `generateWorkoutNote()`, `checkNewPR()`, `checkMilestones()` |
| `generateMorningFocus(forceRefresh)` | Haiku AI call, cached per day |
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
| `getTodayPlannedWorkout()` | Returns today's workout type string |

---

## Features already built
- Full visual overhaul (gradient cards, glassy nav, stacked tab bar)
- Morning AI Focus card (haiku, cached per day, personalized)
- Welcome overlay with personalized AI morning greeting
- Goodnight AI message
- Smarter AI prompts — every AI call includes live context (mood, energy, sleep, nutrition totals, weight trend)
- Food trigger flagging — ⚑ button, orange when flagged, feeds into AI reports
- Quick-log from saved recipes
- Milestone badges — 7-day streak, 30-day streak, 5 lbs lost, 10 workouts, 50 workouts
- PR celebration toast
- `buildTodayContext()` reusable context helper
- Diet adherence AI card in Eat tab
- Water cup tracker (16 cups) in Eat tab
- Streamlined Morning: 3 fields only + pre-gym checklist + 1 save button
- Streamlined Night: 3 fields + habits + collapsed body check-in + 1 "Close Out" button
- Rest day fix: shows recovery card + "Save Rest Day" button

---

## Coding conventions
- **No frameworks** — vanilla JS and HTML only
- **Single file** — all CSS, JS, and HTML stays in `index.html`
- **CSS variables** — always use the design tokens above, never hardcode colors
- **localStorage** — all persistence goes here, no backend
- **Mobile-first** — max-width 480px, touch-friendly tap targets
- **AI calls** — always include `buildTodayContext()` in prompts for personalization
- **Haiku for speed** — use `claude-haiku-4-5-20251001` for quick/cheap calls, Sonnet for complex ones
