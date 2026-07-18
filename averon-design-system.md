# Trigger Design System — Color & Typography

Every value below is computed and contrast-checked from the actual OKLCH values, not eyeballed — see the table in the Color section. This replaces the ad-hoc `oklch(...)` values scattered through v4's CSS with a small set of named tokens, and fixes the font that wasn't actually loading.

**Concept the system is built on:** your own hero line is "on autopilot" — night flight, instrument panels, systems reading out quietly while nobody's watching. That's a more specific idea than "dark mode SaaS," and it's what the palette below is grounded in: not a neutral gray-black with a generic bright accent, but a cockpit-glass black with one instrument-green readout color. Same dark-UI territory you're already in, one degree more specific to Trigger.

---

## Color Palette

| Token | OKLCH | Hex (≈) | Role |
|---|---|---|---|
| `--ink` | `oklch(9% 0.025 258)` | `#000208` | Page background. Deep indigo-black, not neutral gray — this is the one change that makes everything sitting on top of it feel less like the default AI-dark-mode black. |
| `--ink-raised` | `oklch(15% 0.025 258)` | `#050b16` | Card/panel surfaces (automate cards, terminal, pipeline rows). One consistent elevation step, replacing the four or five slightly-different grays currently scattered across `.automate-item-1/2/3`, `.terminal`, `.pipeline-row`, etc. |
| `--ink-line` | `oklch(26% 0.02 258)` | `#1e242e` | All hairline borders and dividers. One value, used everywhere a border currently uses its own one-off gray. |
| `--text-primary` | `oklch(96% 0.006 90)` | `#f3f2ed` | Headings, primary copy. A hair off pure white — softer than `#fff` on a background this dark, less harsh under the glow effects. |
| `--text-secondary` | `oklch(72% 0.02 258)` | `#9da5b1` | Body copy, descriptions, subheadings. |
| `--text-tertiary` | `oklch(64% 0.025 258)` | `#838d9c` | Labels, captions, counters, timestamps, status lines — **this is now the floor.** Nothing in the system should go darker than this for text. It still clears WCAG AA at 6.16:1 on `--ink` and 5.85:1 on `--ink-raised` (both verified below). This single token is the fix for all seven contrast failures in the audit — the footer copy, the "03 Systems" counter, the card numerals, the ticker labels, the terminal's dim text, all of it. |
| `--accent` | `oklch(74% 0.15 168)` | `#00c899` | The one signature color. Used for the italic emphasis word in headings, active/interactive states, the primary glow. Shifted from v4's `#00c66d` toward teal — close enough to keep the brand equity you've already built, distinct enough to not be the generic spring-green every other dark AI site uses. |
| `--accent-dim` | `oklch(38% 0.09 168)` | `#005139` | Large fills only (the split-section background). Never used for text on `--ink`. |
| `--caution` | `oklch(78% 0.13 65)` | `#f0a556` | One deliberate secondary signal color — instrument-panel amber. Used sparingly and only for genuine "attention" moments (e.g. a pending/in-progress state), not decoration. |

**Retired: violet.** In v4, `--violet` only ever appeared inside the CRM pipeline visual and nowhere else on the page — it wasn't a brand color, it was one component's local choice wearing a global variable. The system above is disciplined to two accents (`--accent` = go/active, `--caution` = attention) used consistently everywhere. If the CRM visual still wants a third data category, give it a shade of `--accent` at lower opacity rather than a third hue.

**Verified contrast** (computed against the actual token values, not estimated):

| Pair | Ratio | Requirement | Result |
|---|---|---|---|
| `--text-primary` on `--ink` | 18.43:1 | 4.5:1 | Pass |
| `--text-secondary` on `--ink` | 8.35:1 | 4.5:1 | Pass |
| `--text-tertiary` on `--ink` (the floor) | 6.16:1 | 4.5:1 | Pass |
| `--text-tertiary` on `--ink-raised` (cards) | 5.85:1 | 4.5:1 | Pass |
| `--accent` on `--ink` | 9.60:1 | 3:1 (large text) | Pass |
| `--ink` text on `--accent` fill (buttons) | 9.60:1 | 4.5:1 | Pass |
| `--text-primary` on `--accent-dim` (split section) | 8.51:1 | 4.5:1 | Pass |
| `--caution` on `--ink` | 10.07:1 | 4.5:1 | Pass |

### CSS

```css
:root {
  /* Surfaces */
  --ink: oklch(9% 0.025 258);
  --ink-raised: oklch(15% 0.025 258);
  --ink-line: oklch(26% 0.02 258);

  /* Text */
  --text-primary: oklch(96% 0.006 90);
  --text-secondary: oklch(72% 0.02 258);
  --text-tertiary: oklch(64% 0.025 258); /* floor — never go darker than this for text */

  /* Accent */
  --accent: oklch(74% 0.15 168);
  --accent-dim: oklch(38% 0.09 168);
  --accent-glow: oklch(80% 0.13 168 / 0.45);
  --caution: oklch(78% 0.13 65);

  --ease-out-expo: cubic-bezier(0.16, 1, 0.3, 1);
}
```

---

## Typography

**Recommendation: keep Instrument Serif, replace Satoshi with Instrument Sans.**

The core problem in v4 isn't the pairing — Instrument Serif + Satoshi is a genuinely good, deliberate choice — it's that Satoshi is a Fontshare-exclusive font being requested through Google's API, where it doesn't exist. Two ways to fix it:

**Option A — recommended.** Swap Satoshi for **Instrument Sans**. It's designed by the same foundry as Instrument Serif, explicitly as a companion face for the same "Instrument" brand system — arguably a *more* considered pairing than the original, since both halves come from one type family instead of two unrelated ones. It's a confirmed, verified Google Fonts family, so this is a one-line fix with no self-hosting required.

```html
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Instrument+Serif:ital@0;1&family=Instrument+Sans:wght@400;500;700&display=swap" rel="stylesheet">
```

One trade-off: Instrument Sans' weight axis tops out at 700 (Bold), where Satoshi went to 900 (Black). v4 only used 900 in two places — `.nav-logo` and `.footer-logo` — so drop those to `font-weight: 700`. Everything else in the original CSS already uses 400/500/700, unaffected.

**Option B — if you want to keep Satoshi specifically.** Self-host it: download the woff2 files from Fontshare (fontshare.com/fonts/satoshi) and serve them yourself with `@font-face`, or load them from Fontshare's own CSS API instead of Google's. More setup, but keeps Satoshi's exact letterforms.

### Type scale

| Role | Family | Size | Weight | Tracking | Line-height |
|---|---|---|---|---|---|
| Hero H1 | Instrument Serif | `clamp(5rem, 13vw, 12rem)` desktop / `clamp(3rem, 14vw, 6rem)` mobile* | 400 | `-0.04em` | 0.9 |
| Section H2 | Instrument Serif | `clamp(2.5rem, 4–8vw, 4–8rem)` per section | 400 | `-0.03em` | 1–1.1 |
| Card H3 | Instrument Serif | `clamp(2rem, 3.5vw, 3rem)` | 400 | `-0.02em` | 1.1 |
| Display numerals (ticker) | Instrument Serif | `4rem` | 400 | `-0.03em` | 1 |
| Body / descriptions | Instrument Sans | `1rem` | 400 | normal | 1.7 |
| Wordmark (nav/footer logo) | Instrument Sans | `1.2rem` | **700** (was 900) | `-0.04em` | 1 |
| Buttons / CTAs | Instrument Sans | `0.85–1.1rem` | 700 | normal | 1 |
| Labels / eyebrows / captions | Instrument Sans | `0.7–0.85rem` | 400–500 | `0.08–0.15em`, uppercase | 1 |
| Terminal / code | `'SF Mono', 'Fira Code', monospace` (unchanged) | `0.8rem` | 400 | normal | 2 |

*mobile minimum lowered from `3.5rem` → `3rem` — see the Layout fix prompt, this is what resolves the 320px wrap bug.

### CSS

```css
:root {
  --font-display: 'Instrument Serif', Georgia, serif;
  --font-body: 'Instrument Sans', -apple-system, sans-serif;
}

body { font-family: var(--font-body); }
.hero h1,
.automate-header h2,
.split-left-content h2,
.final-cta h2,
.automate-item h3,
.kinetic-text,
.ticker-value,
.automate-item-num { font-family: var(--font-display); }

.nav-logo, .footer-logo { font-weight: 700; } /* was 900 — see note above */
```

Replace every hardcoded `font-family: 'Instrument Serif', serif;` and `font-family: 'Satoshi', sans-serif;` in the stylesheet with `var(--font-display)` / `var(--font-body)` — that's what makes this an actual system instead of two more magic strings to keep in sync by hand.

---

Use this file as the source of truth when applying the fix prompts — each one references these exact token names and values.
