# Natalie's Bar Prep Website

A personal, static MBE (Multistate Bar Exam) practice site Natalie uses while studying for the bar. No build step, no framework, no backend — just hand-edited HTML files committed to GitHub.

- **Repo:** https://github.com/nataliebaldacci/bar-prep-website
- **`index.html`** — landing page linking into the quiz.
- **`mbe-quiz.html`** — the entire quiz app: all questions, styles, and JS live inline in this one file. This is the file you edit ~every session.
- The `.md` (CONTRACTS.md, CRIMINAL PROCEDURE.md, …) and `.pdf` files in the repo are Natalie's study source material — reference, not site code.

## The daily workflow

This is the recurring loop, and it's almost the only thing we do here:

1. Natalie pastes a study outline — notes, rules, and sometimes example questions from material she just studied.
2. You write new **MBE-format multiple-choice questions** from that material and **append** them to the `questions` array in `mbe-quiz.html` (it's no longer grouped by subject after the initial seed — just add at the end).
3. Report the new total, then commit + push when she confirms.

Commit message style: `Add N <topic> questions on <subtopics>` with a short body of what was covered. Get the count with:
```
grep -c "    subject:" mbe-quiz.html
```

## Question schema

Match this object shape exactly (from `mbe-quiz.html`):

```js
{
  subject: "torts",                       // must be a key in SUBJECTS (see below)
  topic: "Negligence · Duty to Rescue",   // "Area · Subtopic", middot separator
  fact: "A pedestrian sees a small child drowning…",   // the hypo
  call: "Will the parents prevail?",       // the question stem
  choices: [ "…", "…", "…", "…" ],         // exactly 4
  correct: 1,                              // 0-indexed (0=A, 1=B, 2=C, 3=D)
  explanation: "The case turns on <strong>duty</strong>…"  // see rules below
}
```

**Subjects** (the `SUBJECTS` map near the top of the `<script>` — don't invent new keys without adding them there too, since the filter UI reads from it):
`torts`, `civpro`, `contracts`, `property`, `criminal`, `crimpro`, `conlaw`.

**Explanation field:** wrap key doctrine names in `<strong>` (and `<em>` for emphasis). Explain why the correct answer wins AND why each distractor fails, referencing distractors by letter — e.g. "(A) is wrong because…". Make sure those letters match the choices' final positions.

## Two rules Natalie has corrected me on — do NOT repeat these mistakes

**1. Answer-choice length parity.** All four choices must be parallel in length, syntactic structure, and doctrinal specificity. The correct answer must NOT be the longest or most detailed — that lets a test-taker spot it without reading. Put the substantive doctrinal reasoning in the `explanation` field, not in the correct choice. Give every distractor its own plausible reason, not a bare "because [conclusion]." After drafting, eyeball the four lengths; if one is dramatically longer/shorter, rewrite all four together. Keep choices within ~10–15 characters of each other. (History: at one point 83% of the bank had the correct answer as the longest choice — she said "I didn't even need to read it.")

**2. Even correct-answer distribution.** Spread the correct answer evenly across A/B/C/D. Do not cluster on one letter (my default tendency is to put it in slot A or B). Pre-assign target positions across a batch before writing the choices so it's balanced. Current bank is roughly even (~A 103 / B 102 / C 105 / D 97) — keep it that way.

Both rules can be audited with a quick Node script: parse the `choices`/`correct` from the file, then check `correctLength − maxDistractorLength` (gap >20 is noticeable, >50 egregious) and the count of `correct` by letter.

## App features (so edits don't break them)

- All styling and logic are inline in `mbe-quiz.html`. Body text uses **Lora**; headings use **Cormorant Garamond**; UI uses **Inter** (Google Fonts, loaded via `<link>`).
- Completed questions are tracked in `localStorage` under key `mbeQuizCompleted_v1`, and there's a subject/“Completed only” filter UI. Adding questions to the array is safe; don't renumber or reorder existing ones in a way that would shift what "completed" refers to.

## Conventions

- Static files only — no install/build/test. To preview, open the HTML in a browser.
- `.gitignore` covers only `.DS_Store`.
- Don't commit/push until Natalie confirms the new questions look right.
