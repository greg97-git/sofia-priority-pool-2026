# Priority Pool Selection Tool — Template Guide

A step-by-step reference for adapting `index.html` to a different teacher's situation. Every customisable section is listed in the order it appears in the file.

---

## 1 — Teacher identity (Header)

Locate the `<header>` block near the top of `<body>` and update the three lines below:

```html
<h1>Sofia Gonzalez-Herrero — LBPSB Priority Pool 2026–27</h1>
<p>Categories: 3403 English Generalist (main) · 3402 Kindergarten (hidden)
   · Priority: 02-118 · Qualifié français: NON</p>
```

| What to change | Notes |
|---|---|
| Teacher full name | Appears in the page title and the export file |
| Category codes + labels | List main category first; others are secondary |
| Priority number | e.g. `02-118` — determines turn order |
| Qualifié français | `OUI` or `NON` |

Also update `<title>` in `<head>` so the browser tab is correct:
```html
<title>Sofia — LBPSB Priority Pool 2026-27</title>
```

---

## 2 — Stats bar (initial counts)

The stats update live as posts are marked Taken, but the **initial hardcoded values** must match the real job list or the page will flash wrong numbers on first load.

```html
<div class="n" id="s-total">46</div>   <!-- total jobs in JOBS array -->
<div class="n" id="s-avail">46</div>   <!-- same as total at start -->
<div class="n" id="s-ge50">30</div>    <!-- jobs where pct >= 50 -->
<div class="n" id="s-e2">8</div>       <!-- E2 jobs only -->
```

---

## 3 — Rules banner

The yellow banner summarises the key rules for this specific teacher. Rewrite it to reflect the new teacher's situation:

```html
<div class="rules-banner">
  <strong>Key rules:</strong>
  ⚠️ <strong>Right of Refusal</strong> — ...
  🔗 <strong>Combinations</strong> — ...
  🌟 <strong>E2 contracts</strong> lead to tenure ...
</div>
```

Typical things to adjust:
- Days-of-experience threshold for E8 combos (the example has 318 days)
- Whether the teacher is qualifié français (changes which categories trigger ROR)

---

## 4 — JOBS array

Each line in `const JOBS = [ ... ]` represents one posting. Every field is required.

```js
{
  code:       "10844",          // LBPSB posting number (string, must be unique)
  cat:        "3403",           // primary category code (string)
  pct:        100,              // position percentage (number, 1–100)
  ctype:      "E2",             // contract type — see table below
  grade:      "Cycle 1-3",      // grade or cycle label shown in the table
  school:     "Christmas Park", // school name
  location:   "Beaconsfield",   // city / area
  start_time: "Late Start",     // "Early Start" | "Late Start" | ""
  desc:       "Real year-round. Bilingual. Drama — all levels ...",
  s:          "2026-07-01",     // start date YYYY-MM-DD
  e:          "2027-06-30",     // end date YYYY-MM-DD or "Open"
  cv:         "",               // combo formula string — see §5; empty if none
  ci:         "",               // combo info/warning — see §5; empty if none
  ror:        true,             // true = post triggers Right of Refusal
},
```

### Contract types

| Code | Meaning | Typical ROR |
|---|---|---|
| E2 | Real year-round | `true` if ≥ 50% |
| E3 | Partial real ≥ 34% | `true` if ≥ 50% |
| E4 | Partial real < 34% | `false` |
| E8 | Replacement | depends on % |

### start_time values

| Value | Meaning |
|---|---|
| `"Early Start"` | School opens before the board-wide start date |
| `"Late Start"` | School opens on or after the board-wide start date |
| `""` | Unknown or not applicable |

### Row colour logic

Rows are coloured automatically based on `cat` and whether a valid combo exists:

| Condition | Background |
|---|---|
| `cv` is non-empty | Yellow (combo available) |
| `cat === "3402"` | Blue |
| `cat === "3403"` | Green |

Add new categories by extending the CSS and the `render()` function:

```css
.row-3405 { background: #fce7f3; }   /* example: pink for Phys Ed */
```

```js
// inside render(), update the baseClass line:
const baseClass = j.cv ? 'row-combo'
  : j.cat === '3402' ? 'row-3402'
  : j.cat === '3405' ? 'row-3405'
  : 'row-3403';
```

Also add a matching badge colour:
```css
.b-3405 { background: #db2777; color: white; }
```

---

## 5 — Combination posts (cv and ci fields)

### cv — confirmed valid combo

Use this when the combination is verified and meets the majority rule.

Format: `"[CODE] ([CAT], [PCT]%) = [TOTAL]% total"`

Examples:
```
"6301 (3404, 20%) = 70% total"
"1238 (3403 5%) + 10792 (3406 8%) = 33% total [exclude 1722]"
"1404-French (3404, 50%) = 100% total"
```

The app auto-expands category codes to their names (e.g. `3404` → `3404 (French)`).  
Leave `cv: ""` if no valid combo exists.

### ci — combo info / warning

Use this for unverified combos or combos that are explicitly invalid.

Examples:
```
"Combo with 1620 (3404) - verify % first"
"10799 (3406) is 60% — combo invalid"
"Combo 10347 (3404), 10771 (3405) - verify %"
```

### Majority rule reminder

The largest single post in any combination **must** be in the teacher's own category (e.g. 3402 or 3403). Combinations that would flip the majority to another category are invalid.

---

## 6 — COMBOS array (bottom panel cards)

Each entry creates a card in the "Valid Combination Opportunities" section. Only include confirmed, valid combos here.

```js
{
  title:  "Birchwood — Real Partial 50% + French 20%",
  anchor: "8615",          // code of the MAJORITY post (must exist in JOBS)
  total:  70,              // combined percentage
  type:   "E3",            // label shown on the card
  parts: [
    { code: "8615", cat: "3403", pct: 50, label: "3403 English Generalist Cycle 3 (majority ✓)" },
    { code: "6301", cat: "3404", pct: 20, label: "3404 French" },
  ],
  note: "70% real partial at Birchwood (French Plus).",
},
```

Clicking a combo card scrolls to the anchor row in the table and highlights it.

---

## 7 — Category code reference (LBPSB)

| Code | Name |
|---|---|
| 3400 | Elementary Resource (remedial) |
| 3401 | Secondary Resource |
| 3402 | Kindergarten |
| 3403 | English Generalist (Cycle 1–3) |
| 3404 | French (Français langue 2e prim.) |
| 3405 | Physical Education |
| 3406 | Art |
| 3438 | Confirm with current year's job list |
| 3432 | Confirm with current year's job list |

---

## 8 — Category filter options

Update the filter dropdown in `<div class="controls">` to match the new teacher's categories:

```html
<select id="f-cat" onchange="applyFilters()">
  <option value="">All</option>
  <option value="3402">3402 Kindergarten</option>
  <option value="3403">3403 English Generalist</option>
</select>
```

---

## 9 — localStorage keys

The app saves row order, taken marks, and column order under these keys:

| Key | What it stores |
|---|---|
| `sofia2026_order` | Preference ranking (array of codes) |
| `sofia2026_taken` | Posts marked as taken |
| `sofia2026_colorder` | Column arrangement |

**Rename these keys** for each new teacher so separate sessions don't share state:

```js
const LS_ORDER = 'marie2027_order';
const LS_TAKEN = 'marie2027_taken';
const LS_COLS  = 'marie2027_colorder';
```

---

## 10 — Minimal complete example

A stripped-down job entry covering the most common case (a straightforward E3 partial real):

```js
{
  code:       "99999",
  cat:        "3403",
  pct:        60,
  ctype:      "E3",
  grade:      "Cycle 1-3",
  school:     "Lakeshore Elementary",
  location:   "Dorval",
  start_time: "Early Start",
  desc:       "Partial real (60%). Bilingual. French Immersion programme.",
  s:          "2026-08-25",
  e:          "2027-06-30",
  cv:         "",
  ci:         "",
  ror:        true,
},
```

---

## 11 — Checklist before sharing

- [ ] Teacher name updated in `<h1>`, `<p>`, and `<title>`
- [ ] Stats bar initial counts match the actual JOBS array length
- [ ] Rules banner reflects this teacher's specific obligations
- [ ] All JOBS entries have unique `code` values
- [ ] All `anchor` codes in COMBOS exist in JOBS
- [ ] `cv` fields use the correct majority-post code
- [ ] localStorage keys renamed to avoid cross-session conflicts
- [ ] Row colour CSS added for any new category codes
- [ ] Badge colour CSS added for any new category codes
- [ ] Filter dropdown options updated
