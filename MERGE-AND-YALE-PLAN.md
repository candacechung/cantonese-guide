# Plan — Merge into one clean page + standardize romanization to Yale

> **STATUS: ✅ Executed 2026-06-24.** Both Part A (merge) and Part B (Yale standardization)
> were applied to `index.html`. See `FIXES.md` → "Merge into one clean page + Yale
> standardization (executed)" for the full record of changes and verification.

## Context / why
`index.html` (and `README.md`) are each built from **~10+ complete, standalone HTML
documents concatenated end-to-end** — every lesson has its own `<!DOCTYPE>`, `<html>`,
`<head>`, `<style>`, and `<body>`. Browsers tolerate this (they merge it all into one DOM,
so the page does scroll through every section), but the markup is **invalid**, the CSS and
the `speak()` function are **duplicated many times**, and some element IDs are **reused**
with different meanings. The romanization is also **inconsistent** (mixed Yale tone marks,
bare syllables, and a few Jyutping-style spellings).

Goal: one valid, maintainable HTML5 page with consistent **Yale** romanization throughout.

---

## Part A — Merge into a single clean page

**Target structure**
- One `<!DOCTYPE html>` → one `<html>` → one `<head>` (one consolidated `<style>`) → one
  `<body>`.
- Each lesson becomes a `<section id="...">` inside a single shared layout.
- One shared `speak()` and one copy of each interactive script (quiz, recorder, stars).
- A sticky top **nav menu** linking to each section (`#numbers`, `#dim-sum`, `#kitchen`,
  `#grammar`, `#navigation`, `#hiking`, `#weather`, `#voice-lab`, `#quiz`, …).

**Steps**
1. **Inventory** every section currently in `index.html` (numbers, dim sum, essential
   characters, five elements, stroke-order/draw pad, voice lab, kitchen/food, grammar,
   daily life, navigation, pictographs, hiking, weather, scored quiz, Yale guide).
2. **Build a skeleton**: one `<head>` with a unified stylesheet driven by CSS variables
   (`--red`, `--dark`, etc., already used in later sections — promote these to the top).
3. **Migrate** each lesson's body into a `<section>`, dropping the per-document
   `<!DOCTYPE>/<html>/<head>/<body>` wrappers.
4. **Reconcile CSS collisions** — the same class names are defined differently across
   sections (`.container`, `.card`, `.grid`, `.char`, `h1`, `h2`). Either (a) scope them
   per section (e.g., `#kitchen .card { … }`) or (b) agree one shared definition. Scoping
   per-section is the safer, lower-risk choice.
5. **De-duplicate JS** — keep a single `speak()`; ensure the quiz, recorder, and star
   functions exist once. **Fix duplicate IDs** (e.g., `#quiz-result` is used by both the
   Mini Quiz and the scored Quiz with different styling; `#status` likewise) — rename so
   every ID is unique.
6. **Add navigation** — a sticky header with anchor links; optional "back to top" button.
7. **Test** each section after migration (audio, quiz scoring, recorder, stars).

**Risks to watch**
- Class collisions and duplicate IDs (above) are the main source of post-merge bugs.
- Multiple `speak()` definitions today are identical, so collapsing them is safe.
- `README.md` is a near-duplicate (older) copy — decide whether to delete it or convert it
  into a real text README once `index.html` is the single source of truth.

---

## Part B — Standardize romanization to Yale

Decision (confirmed): use **Yale**, not Jyutping — easier for English-speaking beginners.
A Yale primer now lives in `Yale-Romanization.md`, and an in-app guide was added to
`index.html`.

**Steps**
1. **Audit** all romanization strings (mostly in `.pinyin`, `.jyutping`, and inline text).
   Flag inconsistencies:
   - Bare syllables missing tone marks: `Hou-sek`, `Zoi-gin`, `Neih hou`, `Yat`, `Yih`…
   - Jyutping-style `z`/`c` spellings that Yale writes with `j`/`ch`:
     - 再見 `Zoi-gin` → **`joi gin`**
     - 豬肉 `Zyū Yuhk` → **`jyū yuhk`**
   - Rename the `.jyutping` CSS class used in the Pictograph section to `.yale` (or a
     neutral `.romanization`) to avoid mislabeling.
2. **Build a master table** mapping every character/phrase in the app → its correct Yale
   form **with tone marks** (accents + low-tone `h`). Verify against a Cantonese reference
   (e.g., CantoDict / a native speaker) — do not guess tones.
3. **Apply** the corrected Yale consistently across all sections (do this *after* Part A so
   there is only one copy of each word to edit, not 2+ duplicated copies).
4. **Verify** a sample of high-frequency words by ear using the in-app `zh-HK` audio.

---

## Recommended sequencing
1. **Part A first** (merge) — collapses duplicated content so each word exists once.
2. **Part B second** (Yale pass) over the single merged file.
3. Then revisit `README.md` (delete or convert to a true README).

## End-to-end verification
- Open `index.html` in Chrome/Edge; confirm the nav jumps to each section.
- Click characters/Listen buttons → audio plays (needs a `zh-HK` voice installed).
- Mini Quiz: `Hou-sek` is accepted (already fixed).
- Scored Quiz: streak counts; **best streak persists** after refresh.
- Kitchen: starring an item **persists** after refresh.
- Voice Lab: record → playback works; saved blob uses the recorder's real MIME type.
- Spot-check romanization against `Yale-Romanization.md`.
