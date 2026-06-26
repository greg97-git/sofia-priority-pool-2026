# LBPSB Priority Pool — Project Context
> Reference document for Claude sessions working on Sofia's priority pool planning tool.
> Last updated: June 2026

---

## What This Is

The Lester B. Pearson School Board (LBPSB) runs an annual **Priority Pool Selection** process where teachers without a permanent contract pick available posts in strict seniority order. This document explains the rules, Sofia's specific situation, and the tools built to help her prepare.

---

## The Selection Process

### Seniority Format
Teachers are ranked by a priority number in `YY-DDD` format:
- `YY` = last two digits of hire year
- `DDD` = day of the year they were hired
- **Lower number = more senior = picks earlier**
- Example: `02-118` = hired in 2002, day 118 of that year

### How Selection Day Works
1. Teachers are called in seniority order
2. When it's your turn, you can pick any available eligible post (see eligibility below)
3. Once picked, the post is removed from the pool
4. You can also pick a **combination** of posts (see combination rules below)
5. If you pass on an eligible post ≥50%, you must formally invoke **Right of Refusal**

---

## Sofia's Profile

| Field | Value |
|---|---|
| Name | Sofia Gonzalez-Herrero |
| Priority number | 02-118 |
| French-qualified | NON (not French-qualified) |
| Main (visible) category | **3403** — English Generalist Cycle 1-3 |
| Hidden category | **3402** — Kindergarten |
| Days of experience | ~318 days |
| Teachers ahead of her | 115 total; 63 with 3402 or 3403 |

**Probability context (as of June 2026):** Sofia is roughly #64 among competing teachers for 3402/3403 posts. There are 45 total 3402/3403 posts (21 that are ≥50%), meaning supply runs out about 19 spots before her turn. Estimated ~10–15% chance of landing a post ≥50%.

---

## Category Codes

| Code | Subject |
|---|---|
| 3402 | Kindergarten |
| 3403 | English Generalist Cycle 1-3 |
| 3400 | English (general) |
| 3404 | French |
| 3405 | Physical Education |
| 3406 | Art |
| 3438 | Other subjects |

### Visible vs. Hidden Categories
- A teacher's **visible** category is their primary/main teaching field.
- A teacher's **hidden** category is a secondary qualification — they can still pick posts in this category, but competitors in that category are prioritized first.
- Sofia's visible: 3403. Hidden: 3402. She is eligible for posts in **either** category.
- When parsing a job list, include posts where the category code matches any of the teacher's categories (visible or hidden).

---

## Contract Types

| Code | Name | Description |
|---|---|---|
| E2 | Real year-round | Best contract. Runs July 1 – June 30. Leads to tenure. Prioritize these. |
| E3 | Partial real | Partial-time with a guaranteed end date. Better than E8. |
| E4 | Partial real <34% | Same as E3 but very small percentage. |
| E8 | Replacement | No guaranteed end date — can end early if the permanent teacher returns. Riskiest. |

---

## Right of Refusal

If a post **≥50% in the teacher's eligible category** is available at their turn, they cannot silently skip it. They must either:
1. **Claim it**, or
2. **Formally invoke Right of Refusal** — which means declaring they refuse it on the record. The post stays in the pool for the next teacher.

Failing to formally refuse when a qualifying post is available has administrative consequences. This is why ≥50% posts are flagged with `ror: true` in the tool.

---

## Combination Rules

A teacher can combine **two or more posts** into a single contract. Key rules:

1. **The largest single post must be in the teacher's eligible category** (3402 or 3403 for Sofia). The other posts can be in any category.
2. **Experience thresholds:**
   - E8 combinations: need **100 days** of experience (Sofia has ~318 ✓)
   - E3 combinations that create a 100% vacancy: need **200 days** of experience (Sofia has ~318 ✓)
3. **Invalid combo example:** If Sofia is combining posts and a 3405 Phys Ed post is larger than her 3403 post, the combination is invalid — the majority must be in her category.
4. Posts flagged `cv` in the tool have confirmed valid combo partners listed. Posts flagged `ci` have potential combos that need percentage verification before committing.

### Confirmed Combo Opportunities for Sofia

| Anchor Post | Anchor % | Partners | Combined Total | Type |
|---|---|---|---|---|
| 10765 Verdun (3403) | 55% | 6088 (3400 10%) + 10766 (3405 10%) + 10767 (3406 9%) | **84%** | E3 |
| 6754 St. John Fisher Jr (3402) | 60% | 5652 (3404 20%) | **80%** | E8 |
| 1404 Allion (3403) | 50% | 1404-French (3404 50%) | **100%** | E8 |
| 3473 Clearpoint (3403) | 50% | 3473-French (3404 50%) | **100%** | E8 |
| 8615 Birchwood (3403) | 50% | 6301 (3404 20%) | **70%** | E3 |
| 7263 Beacon Hill (3402) | 20% | 1238 (3403 5%) + 10792 (3406 8%) | **33%** | E8 |
| 10780 St. Charles (3403) | 20% | 7337 (3402 10%) | **30%** | E4+E8 |

**Watch out:** Some posts have invalid combos nearby. E.g., 7263 Beacon Hill — do NOT include 1722 (3405 22%) because it would exceed 20% and flip the majority. Always check that the 3402/3403 post remains the single largest piece.

---

## Parsing the Job List PDF

When a new JOBLIST PDF is provided, here is how to identify eligible posts for Sofia:

1. **Filter by category code:** Include any post with code 3402 or 3403 (her visible and hidden categories).
2. **Extract per post:**
   - Post code (numeric ID)
   - Category code
   - Percentage (%)
   - Contract type (E2/E3/E4/E8)
   - Grade/Cycle description
   - School name
   - Location/borough
   - Start time (Early Start / Late Start — refers to school calendar zone)
   - Start and end dates
   - Post description (includes language program info: Bilingual, French Plus, French Immersion, etc.)
3. **Flag ROR:** Mark `ror: true` for any post ≥50%.
4. **Identify combos:** Look for posts at the same school where the 3402/3403 post is the largest piece. Check surrounding posts (often the French half of a bilingual post has the same code with a "-French" suffix or appears adjacent in the list).
5. **Flag potential combos for verification:** If a combo looks possible but percentages need confirming, add a `ci` (combo investigate) note rather than asserting it as valid.

### School Name Aliases
The job list and school info sheets sometimes use slightly different names. Common ones encountered:
- "St. Charles" ↔ "St-Charles"
- "Allion Elementary" ↔ "Allion"
- "Verdun Elementary" ↔ "Verdun"

---

## The Tool

### Files
| File | Description |
|---|---|
| `index.html` | The full single-file web app (HTML + CSS + JS) |
| `SofiaPool2026.xlsx` | Excel version with all eligible posts, color-coded |

### GitHub Repo
`github.com/greg97-git/sofia-priority-pool-2026`
Live at: `https://greg97-git.github.io/sofia-priority-pool-2026/`

### App Architecture (index.html)
- **No framework, no build step.** Plain HTML + CSS + Vanilla JS, single file.
- All job data is in a `JOBS` array (hardcoded in the `<script>` block).
- All combo data is in a `COMBOS` array.
- State is managed with two JS objects: `displayOrder` (array of codes in preference order) and `taken` (object of taken codes).

### localStorage Keys
| Key | Value |
|---|---|
| `sofia2026_order` | JSON array of job codes in her preference order |
| `sofia2026_taken` | JSON object of taken/claimed post codes |

On load, the app merges saved state with the current JOBS array: saved order is preserved, any new jobs not in the saved order are appended at the bottom sorted by default (E2→E3→E4→E8, pct desc).

### Key Fields in the JOBS Array
```js
{
  code: "10765",       // Post code from job list
  cat: "3403",         // Category code
  pct: 55,             // Percentage (integer)
  ctype: "E3",         // Contract type
  grade: "Cycle 1-3",  // Grade/cycle description
  school: "Verdun",    // School name
  location: "Verdun",  // Borough/city
  start_time: "Early Start", // or "Late Start" or ""
  desc: "Partial real (55%). Bilingual.", // Free-text description
  s: "2026-08-25",     // Start date
  e: "2027-06-30",     // End date ("Open" for E8)
  cv: "6088 (3400 10%) + ...", // Confirmed valid combo string (empty if none)
  ci: "",              // Combo to investigate (needs % verification)
  ror: true            // Right of Refusal applies (pct >= 50)
}
```

### Update Workflow (When New Job List Arrives)
1. User uploads the new JOBLIST PDF to Claude
2. Claude parses it using pdfplumber (Python) or text extraction
3. Claude identifies all 3402/3403 posts, flags ROR, identifies combos
4. Claude updates the `JOBS` array in `index.html` and the `COMBOS` array
5. User saves the updated `index.html`, pushes to GitHub
6. GitHub Pages redeploys in ~60 seconds
7. Sofia's localStorage preference order is preserved; new jobs appear at the bottom

---

## Source Documents
- **PriorityList2026.pdf** — 14-page list of all priority pool teachers ranked by seniority. Sofia is on page 10 at rank 02-118. Used to count competitors ahead of her.
- **JOBLIST2026V1.pdf** — 44-page job vacancy list. All 3402/3403 eligible posts were extracted from this.
- **TEMPLATE-Priority Pool line up 2026.xlsx** — Original Excel template provided by LBPSB. Has 4 sheets: "Sofia 2026", "Schoolslocationprincipals", "Availability after formspool", "Essential Information Needed". The school info sheet (principals, locations) was referenced when building the Excel deliverable.
