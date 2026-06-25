# Fix Log — Cantonese Guide

## 2026-06-24 — Mini Quiz "Delicious" answer never accepted

### Summary
The **🧠 Mini Quiz** at the top of `index.html` asks *"What is the Cantonese word for
'Delicious'?"* with three choices: **Haa Gau**, **Hou-sek**, **Zoi-gin**. The correct
choice is **Hou-sek** (好食), but clicking it was always marked wrong.

### Root cause
In the `checkQuiz()` function (`index.html`), the comparison string did not match any
button's value:

- Each button calls `checkQuiz('Haa Gau' | 'Hou-sek' | 'Zoi-gin')`.
- The function checked `if (answer === 'Hou-lek')` — a value **no button ever sends**.

Because `'Hou-lek'` could never equal any answer passed in, the success branch was
unreachable and every selection (including the correct `Hou-sek`) fell through to the
"Try again" branch. As a secondary issue, the reward audio played `好叻` (*hóu lēk*,
"clever"), not the word being taught (`好食`, *hóu sihk*, "delicious").

### Fix
In `checkQuiz()` (`index.html`):

| Before | After |
| --- | --- |
| `if (answer === 'Hou-lek')` | `if (answer === 'Hou-sek')` |
| `speak('好叻')` | `speak('好食')` |

The "Try again" message also now includes a small hint (`Hint: it starts with 'Hou'.`).

### How it was verified
- Confirmed the three quiz buttons send `'Haa Gau'`, `'Hou-sek'`, `'Zoi-gin'`.
- Confirmed `'Hou-sek'` now matches the success branch (green "✅ Correct!" + plays `好食`).
- Confirmed the other two buttons still show the red "❌ Try again" message.
- Manual test: open `index.html` in a browser, click **Hou-sek**, confirm green message + audio.

---

## 2026-06-24 — Reachability correction (no code change)
Earlier I claimed browsers stop at the first `</html>` and hide later sections. **That was
wrong.** Per the HTML spec's error-recovery rules, the browser parses the whole file into a
single DOM (ignoring the duplicate `<!DOCTYPE>/<html>/<head>/<body>` tags) and renders every
section as one long scroll — which is what the live site shows. The concatenated-documents
structure is still *invalid markup* worth cleaning up, but it does **not** hide content.

---

## 2026-06-24 — Yale guide, persistence, and audio-format fixes

### 1. In-app Yale Romanization guide (new learning content)
Added a **"🗣️ How to Read the Sounds (Yale Romanization)"** section near the top of
`index.html` (first section, before *Counting Numbers*), styled to match the site:

- A short **"This course uses Yale Romanization"** note, plus a **`zh-HK` audio note**
  (see item 4 below).
- **Table 1 — The 6 Cantonese Tones in Yale** (tone, name, how Yale marks it, example).
- **Table 2 — Yale vs. Jyutping** (reference only; the course always uses Yale).
- New CSS: `.romanization-note`, `.audio-note`, `.yale-table`, `.yale-eg`.

The full primer was also saved as a standalone note: **`Yale-Romanization.md`**.

### 2. Audio format fix (#3) — MediaRecorder no longer mislabeled as WAV
In the Voice Lab recorder (`index.html`), the recording blob was created as
`{ type: 'audio/wav' }`, but `MediaRecorder` does not produce WAV (it outputs webm/ogg).
Changed to use the recorder's real format:

```js
audioBlob = new Blob(audioChunks, { type: mediaRecorder.mimeType || 'audio/webm' });
```

### 3. localStorage persistence (#5)
- **Best Streak** (scored Quiz) now persists across refreshes:
  - Loaded on start: `parseInt(localStorage.getItem('cantonese-best-streak'), 10) || 0`
    and shown immediately in the scoreboard.
  - Saved whenever a new best is reached (`cantonese-best-streak`).
- **Favourites (⭐)** in the Kitchen now persist:
  - `toggleStar()` saves/removes the item (keyed by its character) under
    `cantonese-favourites`.
  - On `DOMContentLoaded`, previously starred items are re-highlighted.
  - Helpers added: `loadStars()`, `starId()` (reads the card's `.char`).

### 4. `zh-HK` voice note (#5)
Added an **audio note** under the page title explaining that the "Listen"/tap-to-hear audio
uses the browser's built-in Cantonese (`zh-HK`) voice, and that a device may need a
Cantonese voice installed if nothing is heard.

### How it was verified
- Re-read each edited region; confirmed unique anchors and intended replacements.
- Logic check: best-streak and favourites read/write the documented `localStorage` keys and
  restore on load; recorder blob uses `mediaRecorder.mimeType`.
- Manual test (recommended): open `index.html`, star a Kitchen item and build a quiz streak,
  refresh → both persist; record in Voice Lab → playback works.

### Not changed (planned separately at the time)
- **#1 Merge into one clean page** and the **Yale romanization standardization** are
  scoped in **`MERGE-AND-YALE-PLAN.md`**. ➡️ **Now executed — see the next entry.**
- `README.md` (an older near-duplicate of the page) is untouched.

---

## 2026-06-24 — Merge into one clean page + Yale standardization (executed)

Executed **`MERGE-AND-YALE-PLAN.md`** (both Part A and Part B). `index.html` was rebuilt
from ~12 concatenated standalone HTML documents into **one valid HTML5 page**.

### Part A — Merge (structure)
- **One document:** a single `<!DOCTYPE>`, `<html>`, `<head>`, `<body>`, one consolidated
  `<style>`, and **one** `<script>` at the end of `<body>`. (Verified: each of these appears
  exactly once.)
- **12 sections** as `<section id="…">`: `home`, `characters`, `draw`, `voice`, `kitchen`,
  `grammar`, `daily`, `navigation`, `pictographs`, `hiking`, `weather`, `quiz`.
- **Sticky nav bar** with anchor links to every section (12 links ↔ 12 ids, verified 1:1).
- **CSS collisions reconciled:** shared base/component styles defined once; each section sets
  a per-section theme via an inline `--accent` CSS variable (used for the card shadow, `h1`
  colour, and `h2` pill). Kitchen food-group header colours (`.meat-h` … `.staple-h`) are
  defined after the base `h2` rule so they still win.
- **Duplicate ID fixed:** the Mini Quiz `#quiz-result` clashed with the scored quiz's
  `#quiz-result`. The Mini Quiz now uses `#mini-quiz-result`, and its handler was renamed
  `checkQuiz → checkMiniQuiz` (old name fully removed).
- **Mislabeled class renamed:** the Pictograph romanization span `.jyutping` → `.romanization`
  (it shows Yale, not Jyutping).
- **De-duplicated JS:** a single `speak()` plus one copy each of the Word-of-the-Moment,
  canvas drawing, voice recorder, ⭐ favourites, and scored-quiz logic. The earlier fixes
  (audio MIME type, best-streak + favourites persistence) are preserved.
- **Staple Foods** header switched from the (mislabeled) `dessert-h` purple to a new
  `staple-h` colour; the previously-undefined `--staple` variable is now defined.

### Part B — Yale romanization standardization
All romanization was converted to consistent **Yale with tone marks**. Highlights:
- **Jyutping `z`/`c` → Yale `j`/`ch`:** 豬肉 `Zyū → jyū`, 再見 `Zoi-gin → joi gin`,
  左 `Zó → jó`, 中 `Zung → jūng`, 主食 `Zyú → jyú`.
- **Spelling/tone corrections:** 蔬菜 `Sor Choi → sō choi`, 西 `Saai → sāi`, 東 `Dung → dūng`,
  北 `Bak → bāk`, 南 `Naam → nàahm`, 紅蘿蔔 `Hùng → hùhng`, 樓梯 `Làu → làuh`,
  電纜 `Lám → láahm`, 行慢啲 `Hàhng → hàahng`, 返屋企 `Uk → ūk`.
- **Tone marks added** to previously bare syllables (numbers `Yat → yāt`, greetings
  `Neih hou → néih hóu`, dim sum, essential characters, etc.).
- **Syllabic finals:** 五 `ńgh`, 唔 `m̀h` (e.g. 唔該 `m̀h gōi`, 唔係 `m̀h haih`).
- **Mini Quiz** options/answer now read `hā gáau` / `hóu sihk` / `joi gin`; the correct check
  compares against `hóu sihk`.
- **Note:** loanword transliterations (e.g. 雲呢拿 “vanilla” `wàhn nēi láh`) are approximate;
  a native-speaker review of edge-case tones is still advisable.

### How it was verified
- Structural greps: exactly **1** each of `<!DOCTYPE`, `<html`, `<head>`, `<body>`, `</html>`,
  `function speak`, `<script>`; **1** `#quiz-result` and **1** `#mini-quiz-result`.
- **0** remaining `checkQuiz(` refs, **0** `jyutping` refs, **0** leftover Jyutping `z`-spellings.
- Nav anchors and section ids match 1:1 (12 ↔ 12).
- **Manual test still recommended:** open `index.html` in Chrome/Edge, click through the nav,
  test audio, the Mini Quiz (`hóu sihk` = correct), the scored quiz streak (persists on
  refresh), Kitchen ⭐ (persists on refresh), the draw pad, and the Voice Lab recorder.

### Still open
- `README.md` remains an older near-duplicate; converting it to a true text README (or
  deleting it now that `index.html` is the single source of truth) is a suggested follow-up.

---

## 2026-06-24 — Mobile/layout polish + content updates

1. **Mobile-friendly nav.** The sticky nav no longer wraps; on narrow screens it becomes a
   single horizontally-scrollable row with the scrollbar hidden (`flex-wrap: nowrap;
   overflow-x: auto;` + `scrollbar-width: none` / `::-webkit-scrollbar { display:none }`,
   and `flex: 0 0 auto` on the pills). All anchors stay reachable by swiping the bar.
2. **Yale tables relocated.** The two Yale Romanization tables (the 6-tone table and the
   Yale-vs-Jyutping reference) moved from **Home → Voice Lab**. The short Home note now points
   readers to the Voice Lab guide.
3. **Radicals subsection added** to Characters: "🧩 Common Radicals by Category (部首)" with
   two groups — **Body & Person** (亻 person, 扌 hand, 口 mouth, 忄 heart, 目 eye, 足 foot) and
   **Nature** (氵 water, 木 wood, 火 fire, 艹 grass, 日 sun). Each card plays an example word.
4. **Sections merged.** "Draw It" is now a subsection inside **Characters & Writing**; the
   separate `#draw` section and its nav anchor were removed (11 sections / 11 nav anchors,
   verified balanced).

Verification: greps confirm 11 `<section>` opens = 11 closes, 11 nav anchors, 0 orphan
`#draw` refs, both Yale tables now sit after the Voice Lab heading, and Home (Counting
Numbers / Dim Sum / Mini Quiz) is intact. Visual/runtime test still recommended on a phone.

---

## 2026-06-25 — Radicals rework + three new modules

1. **Word Elements & Their Radicals (rebuilt).** The radicals subsection in *Characters &
   Writing* now follows the original element-card layout. Each of the 11 cards
   (金 木 水 火 土 人 手 口 心 目 足) shows, in order: the **original word** (with Yale reading) →
   **meaning** → **radical** (as a `radical-tag`) → a **larger example word**.
   - **Click behaviour:** the original word and the example word are clickable and call
     `speak()`; the radical is intentionally *not* pronounced (no handler on it). Card-level
     `onclick` was removed so tapping the radical does nothing.
   - **Bigger example word:** new `.example-word` style (2.8rem, clickable, hover scale).
2. **Three new lesson modules added** (sections + nav anchors, between Daily Life and
   Navigation), each in the existing card / Yale / `speak()` pattern:
   - **💰 Money & Shopping** (`#money`) — asking the price (幾多錢, 蚊, 毫子, 找錢), buying &
     bargaining (買, 賣, 平, 貴, 平啲啦), plus a market mini-dialogue.
   - **🕐 Time & Days** (`#time`) — telling time (而家幾點, 點鐘, 半), parts of the day
     (朝早, 晏晝, 夜晚), today/tomorrow/yesterday, and all 7 weekdays (星期一–日).
   - **🚍 Getting Around** (`#transport`) — modes (巴士, 港鐵, 的士, 小巴, 渡輪, 電車), riding &
     asking (八達通, 上車, 落車, 車站, 去邊度), plus a taxi mini-dialogue.

Verification: 14 `<section>` opens = 14 closes = 14 nav anchors; `#money`/`#time`/`#transport`
ids and anchors present; 11 `.example-word` cards; single valid document (one each of
DOCTYPE/html/head/body). Yale tones for the new vocab are my best effort — a native-speaker
spot-check is still advisable. Visual/runtime test on a phone recommended.

---

## 2026-06-25 — Removed the Kitchen ⭐ favourites feature

The ⭐ favourites toggle in the Kitchen section was no longer working (and had no `.star`
CSS, so toggling produced no visible change), so it was removed to avoid confusion:
- Deleted all **29 `<span class="star">⭐</span>`** spans from the food cards.
- Deleted the favourites JavaScript (`toggleStar`, `loadStars`, `starId`, the `STAR_KEY`
  localStorage writes, and the `DOMContentLoaded` restore).
- Updated the Kitchen subtitle to drop the "Tap the ⭐ to track your progress" line.

Verification: 0 remaining `star` / `toggleStar` / `cantonese-favourites` references; food
cards intact; document still valid. (The `cantonese-favourites` localStorage key, if any user
already had one, is now simply unused/harmless.)

---

## 2026-06-25 — Radical sizing + new Tenses & Aspect section

1. **Word Elements sizing.** In "Word Elements & Their Radicals", the original element
   character (金 木 水 火 土 人 手 口 心 目 足) was reduced from `2.4rem → 1.6rem`, and the
   **radical pill** was enlarged — scoped with `.card:has(.example-word) .radical-tag
   { font-size: 1.15rem; padding: 4px 12px; }` so the Five Elements cards above are unaffected.
   (Uses the CSS `:has()` selector — supported in all current browsers.)
2. **New section: ⏳ Verb Time — Tenses & Aspect** (`#tenses`, added between Grammar and
   Daily Life, with a nav link). Explains that Cantonese verbs don't conjugate; you add one
   particle to mark time:
   - **Future 會 (wúih)** before the verb — 我會去 (I will go).
   - **Past/Done 咗 (jó)** after the verb — 我去咗 (I went / have gone).
   - **Now 緊 (gán)** after the verb — 我食緊飯 (I am eating).
   - **Experience 過 (gwo)** after the verb — 我去過 (I have been there before).
   Each with tap-to-hear example cards, plus a "Quick Recap" box.

Verification: 15 `<section>` opens = 15 closes = 15 nav anchors; `#tenses` id + anchor present;
11 word-element chars now `1.6rem` (0 left at `2.4rem`); scoped radical rule present; single
valid document. Yale tones for the new examples are best-effort; native spot-check advisable.

---

## 2026-06-25 — Standardized click-to-speak; removed Listen/發音 buttons

Home and Characters already let you tap the Chinese word to hear it; the rest of the site
used "Listen 🔊" / "發音 🔊" buttons. Standardized the whole site on tap-the-word:

- **Removed all 125 word buttons** (`listen-btn` ×120, `btn-speak` ×5) via a single regex
  pass, plus their now-unused CSS rules. Kept the Recording Challenge, scored-quiz, and Home
  interactive buttons (Random Word, Mini-Quiz options, Radical hints).
- **One delegated click handler** makes every Chinese-word element speak its own text:
  `document.querySelectorAll('.char, .char-big, .char-hero, .big-char, .speakable')` →
  `speak(el.textContent.trim())`. Multi-character phrases (e.g. 我會去, 幾多錢?) read in full.
- **Discoverability/affordance:** added pointer cursor + a hover highlight (color + slight
  scale) to those word classes, and updated the Home note to "Tap any Chinese word to hear it."
- **Nav location words** (呢度 / 嗰度 / 邊度?) were bare spans, so they got a `speakable` class
  to join the handler.

Verification: 0 `listen-btn`/`btn-speak` refs and 0 "Listen/發音" text remain; delegated
handler + affordance CSS present; 3 `speakable` nav words; 15 sections balanced; document
still valid; recording/quiz/Home buttons intact.

Note: the Voice Lab "Recording Challenge" copy still says "Listen to a character above / match
the Listen button," which is now stale (that section was trimmed to just the Yale tables +
recorder). Left as-is for now — easy follow-up if you want it reworded.

---

## 2026-06-25 — Reworded recorder, more click-to-speak, punctuation not spoken

1. **Recording Challenge reworded** (Voice Lab) — no longer references "the character above"
   or "the Listen button" (both gone); now: tap any word on the site to hear it → record →
   play back and compare.
2. **Click-to-speak extended to phrases/sentences.** Wrapped the Chinese (only — not the
   romanization) in `<span class="speakable">` so the existing delegated handler picks them up:
   - **Sentence Builder Challenge** (好食, 唔好食, 係魚, 唔係魚)
   - **Pro Tip: Existence Questions** (好唔好, 有無, 我有魚, 我無錢, 有無叉燒包)
   - **"The 6 Cantonese Tones in Yale"** example words (詩, 史, 試, 時, 市, 是 — Han char only,
     romanization left unclickable)
   - **Mini Dialogue: Taking a Taxi** (我想去中環, 好,上車啦, 呢度落車)
3. **Punctuation no longer pronounced.** `speak()` now strips symbols a TTS voice may read
   aloud — `/ ／ \ | ~ ^ * _ # @ & < > = + ( ) （ ） [ ] { } 「 」 『 』` — before speaking
   (collapsing leftover spaces). Commas/periods/question marks are kept (they only affect
   prosody, not pronounced as words). This means slashes can stay in the text (e.g. radical
   forms 火 / 灬, meanings like Gold / Metal) for readability without being spoken.

Verification: 21 `.speakable` spans total; `speak()` cleaning present; recorder old copy gone;
15 sections balanced; single valid document.

**Follow-up (same day):** extended click-to-speak to the **Tenses "Quick Recap"** (each marker
會/咗/緊/過 and its example 我會去 / 我去咗 / 我食緊飯 / 我去過) and the **Money "Mini Dialogue:
At the Market"** (呢個幾多錢, 十蚊, 平啲得唔得). The recap examples keep their inner `<b>` emphasis
inside the `.speakable` wrapper (textContent still reads the full phrase).
