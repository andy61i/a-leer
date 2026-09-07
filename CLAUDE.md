# CLAUDE.md — ¡A leer!

Guidance for Claude Code when working in this repository.

## What this is

A small web game that helps a young child learn to **read in Spanish** using the
syllabic method (método silábico). A word appears split into color-coded
syllables; the **Pista** button reveals the picture as a 3×3 puzzle (a hint); the
**¡Lo leyó!** button (for the adult) plays a reward sound, awards a star and shows
confetti.

UI language is **Spanish**. The intended user is an adult reading together with a
small child, so keep everything large, calm, and free of time pressure.

## How to run

Just open `index.html` in a browser (double-click). No build step, no server, no
dependencies. It works fully offline.

Optional URL param: `?name=NAME` personalizes the greeting. With no param it
shows "¡Hola!".

## Architecture (important)

- **Single file**: everything (HTML + CSS + JS) lives inline in `index.html`.
  Keep it that way unless explicitly asked otherwise — no external files, no CDN,
  no frameworks, no npm. This is a deliberate constraint (offline, portable,
  trivially hostable on GitHub Pages).
- **Images**: `images/<word>.webp` (also tries `.png/.jpg/.jpeg`, in that order).
  Loaded lazily by `loadImage()`; if a file is missing it falls back to the word's
  **emoji**. So the app always works even before images exist.
- **Audio**: reward/pop/fanfare sounds are **synthesized with the Web Audio API**
  (`tone()`), not audio files.
- **Confetti**: a `<canvas>` particle effect (`confetti()`).
- **Persistence**: `localStorage`, key `aleer_v2`. Shape:
  `{ stars:{word:count}, hints:{word:count}, trophies:[level numbers], vowels:[...] }`.
  If you change this shape, migrate or bump the key — don't silently break saved
  progress. `load()` already migrates from the previous key, which it finds by the
  pattern `aleer_*_v1` rather than by name: the old key carried the child's name
  and this repository is public.

## The level ladder

A level is a **step of difficulty**, never a theme. Each step introduces one
reading rule, and a word only lands on a step once every rule it needs has been
introduced. There are 15 steps and 111 words.

1. vowels + m, p, s, l — open syllables only
2. n, d, t
3. f, b, v
4. c and g before a, o, u (six combinations: ca co cu ga go gu — the longest step)
5. r between vowels vs. r starting a word
6. closed syllables (`pan`, `bar-co`)
7. the doubled letters rr and ll
8. ch and ñ
9. the silent u: que, qui, gue, gui
10. the /x/ sound: j, plus ge and gi
11. the /s/ sound: ce, ci and z
12. the silent h
13. consonant clusters with l and r
14. two vowels side by side — both merged (`au-to`) and split (`an-te-o-jos`)
15. tildes

**Every step must contain an example of every letter and combination its own
description claims.** A step that defers its own novelty to the next one is a bug:
the child then meets the new letter together with another new rule.

Colors are not a theme either — each color word sits on the step its spelling
belongs to: `lila` on 1, `verde` on 6, `amarillo` on 7, `marrón` on 15.

### Rules when editing WORDS

1. **Syllables are pre-split in the data** (the `syl` array). Do not compute them
   at runtime. `syl.join('')` MUST equal `w` exactly. Respect Spanish
   syllabification:
   - digraphs `ch`, `ll`, `rr` are never split (`co-che`, `po-llo`, `pe-rro`);
   - `qu`, `gu`(+e/i) stay with the vowel (`que-so`, `gui-ta-rra`);
   - consonant clusters with l/r are not split (`bl, br, cl, cr, dr, fl, fr, gl,
     gr, pl, pr, tr`): `li-bro`, `tren`;
   - otherwise two consonants split (`bar-co`, `can-to`).
2. `w` stays **lowercase** — it is the image filename and the storage key. The UI
   uppercases syllables via CSS, so don't uppercase the data.
3. Pick the `lvl` by the word's hardest rule, per the ladder above.
4. Add an `em` (emoji) — it is the fallback until the picture exists.
5. `LEVELS` and `TROPHIES` must stay in step with the ladder: one trophy emoji per
   level.

## Progress, locks and the party

- **Level progress** — which words of a level have ever been read. Counted from
  `prog.stars`, lives in storage, drives the progress bar and the locks.
- **The lap** (`circle`) — which words have been read since this lap started.
  Lives only in page memory. It is cleared on three occasions: moving to another
  level, closing a lap, and "Borrar progreso". Missing the second one makes the
  fanfare fire on every word after the lap closes. It deliberately survives a trip
  to the shelf or the start screen and back to the same level (`circleLevel` guards
  that), so a lap in progress is not silently thrown away.
- **The trophy is stored in `markRead`, not on the party screen.** Leaving the game
  before pressing "Siguiente" must not cost the child the level they just finished.
  `showTrophy()` only shows the party; the trophy and the unlocked next level are
  already in storage by then. Accepted limitation: if the child leaves before
  "Siguiente", the party itself is not shown later — the trophy is on the shelf,
  but that one celebration is missed.
- A level is unlocked when the previous one has a trophy. Level 1 is always open.
  The adult gets past a lock by holding a finger on it — a tap never opens it.
- The trophy, the lock and the party screen happen **once** per level. A repeated
  full lap still plays the fanfare on the game screen. That fanfare sounds
  immediately when the lap closes; only its line is delayed, and `renderWord`
  cancels the line so it cannot overwrite the praise of the next word.
- The party waits for "Siguiente": the child reads the last word, looks at the
  picture, and only then the game leaves for the trophy screen.
- On the last level there is no next lock and no "Seguir" button. The
  "you finished the whole game" line appears only when all 15 trophies are in.

## Word sizing

A long word must never push the buttons off the screen. `renderWord()` measures
the width the word needs in em into `--wlen`, and `#word .syl` divides the stage
width by it. The divisor is the **stage** (max 680px), not the viewport — dividing
`vw` lets a long word overflow the stage on a wide screen. The coefficients
(0.70 per letter, 0.62 padding, 0.54 per dot) were calibrated against the real
rendered width; if you change the font, padding or gap, re-measure them.

The rule is scoped to `#word .syl` on purpose. The vowel warm-up uses the same
`.syl` class and sets no `--wlen`; a global rule would collapse those letters to
body-text size.

## Images

Style, prompt, per-level word list and the rule for color cards live in
`images/COMO_AGREGAR_IMAGENES.txt`. Colors get three or four different objects of
that color on a background of the same color — a single object makes the child
name the object instead.

## Deployment

Static site → **GitHub Pages** served from the repo **root** of branch `main`.
`.nojekyll` is included so Pages doesn't run Jekyll. Nothing to build.

## Verification (do this after editing content/logic)

No test framework. Quick checks:

```bash
# JS syntax check (extract inline script, then node --check)
node -e "const fs=require('fs');const h=fs.readFileSync('index.html','utf8');fs.writeFileSync('/tmp/a.js',h.match(/<script>([\s\S]*?)<\/script>/)[1]);"
node --check /tmp/a.js

# data integrity: counts + syllables join back to the word
node -e "const fs=require('fs');const h=fs.readFileSync('index.html','utf8');const W=eval('['+h.match(/const WORDS = \[([\s\S]*?)\];/)[1]+']');console.log('total',W.length);console.log('bad',W.filter(w=>!w.w||!w.syl||!w.em||!w.lvl||w.syl.join('')!==w.w).map(b=>b.w));"
```

For visual and interaction checks, serve the folder (`python3 -m http.server`)
and drive it in a browser — `file://` does not work with browser automation.

## Conventions & constraints

- Keep it a single self-contained `index.html` + `images/`. Offline-first.
- Spanish UI; warm, kid-friendly, no time pressure, large tap targets.
- Code comments, identifiers and log strings in English only.
- This repo is **public** — do not put personal data (real child names, family
  details, location) in the code, README, or commits. The greeting defaults to
  generic "¡Hola!"; personalize only via the `?name=` URL param.
