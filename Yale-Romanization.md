# Yale Romanization — A Quick Guide

**Yale** is a system for writing Cantonese sounds using the Latin alphabet. It was
developed at Yale University (1970s, by Gerard Kok and Parker Po-fei Huang) to teach
Cantonese to English speakers. That's why it suits this app: it's designed to be **read
intuitively by people who already know English spelling**, rather than to be maximally
precise or compact.

## The three parts of a syllable

A Cantonese syllable in Yale = **initial + final + tone**.

Example: 好 = `hóu` → `h` (initial) + `ou` (final) + the rising tone mark `´`.

### 1. Initials (the starting consonant)
Mostly read like English: `b, p, m, f, d, t, n, l, g, k, h, s, j, ch, y, w …`
- `j` sounds like English "j" (e.g., 知 *jī*)
- `ch` like English "ch"

### 2. Finals (vowel + ending)
Read close to English vowels: `aa, a, e, i, o, u, eu, yu`, optionally closed by
`-i, -u, -m, -n, -ng, -p, -t, -k`.
- 心 = `sām` (heart), 山 = `sāan` (mountain), 水 = `séui` (water)

### 3. Tones — the distinctive feature
Cantonese has 6 tones. Yale marks them **two ways combined**: an **accent mark** over the
vowel for the pitch contour, and an **`h` after the vowel** to signal the *low* register.

| Tone | Name | How Yale shows it | Example |
|------|------|-------------------|---------|
| 1 | High | macron `ā` (or high-falling `à`) | 詩 `sī` |
| 2 | Mid-rising | acute accent `á` | 史 `sí` |
| 3 | Mid-level | (no mark) | 試 `si` |
| 4 | Low-falling | grave accent **+ `h`** → `àh` | 時 `sìh` |
| 5 | Low-rising | acute accent **+ `h`** → `áh` | 市 `síh` |
| 6 | Low-level | **`h`** only | 是 `sih` |

So words like 你好 = **Néih hóu** and 多謝 = **Dōjeh** — the `h` isn't pronounced, it's a
tone signal.

## Yale vs. Jyutping (why this app uses Yale)

| | **Yale** | **Jyutping** |
|---|---|---|
| Tones | accent marks + silent `h` | a **number** 1–6 after the syllable |
| 好 (good) | `hóu` | `hou2` |
| 食 (eat) | `sihk` | `sik6` |
| Reads like English? | Yes, fairly intuitive | Less so (`j` = English "y", plus `z`, `c`) |
| Standard today? | Older, common in textbooks | Modern academic/official standard |

**Why Yale fits beginners:** the spelling maps onto English reading habits, and tones
appear as accent marks *on the word* rather than as trailing numbers — so a learner sees
`hóu sihk` and can sound it out, instead of decoding `hou2 sik6`. The trade-off is that
the silent `h` and accent marks can confuse people at first, and Jyutping is what most
modern dictionaries/apps use.

## Note for this app
The vocabulary currently mixes styles — some entries have proper Yale tone marks
(e.g., `Gāi Yuhk`, `Néih`) and others are bare syllables with no diacritics
(`Hou-sek`, `Zoi-gin`). Standardizing on Yale means carrying the marks and the low-tone
`h` consistently — e.g., **好食 = `hóu sihk`**, and **再見 = `joi gin`** (Yale uses `j`,
not `z`). See `MERGE-AND-YALE-PLAN.md` for the standardization plan.
