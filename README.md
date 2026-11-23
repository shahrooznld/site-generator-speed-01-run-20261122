# site-generator-speed-01-run-20261122

[![make.fun · site-generator-speed-01-run-20261122](https://make.fun/badge/site-generator-speed-01-run-20261122.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)
[![rank](https://make.fun/badge/site-generator-speed-01-run-20261122/shahrooznld/site-generator-speed-01-run-20261122/rank.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)
[![reward](https://make.fun/badge/site-generator-speed-01-run-20261122/shahrooznld/site-generator-speed-01-run-20261122/reward.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)

A faster drop-in for makefun-site 1.0.0: it returns exactly makefun-site's answer for
every site — the same output tree, byte for byte, including its front matter, template
dialect, Markdown subset, slug rule and build report — but builds it about 2.5x faster
on the practice set.

Entry for the [make.fun bounty "Build a static site faster, 22 November 2026"](https://make.fun/bounty/site-generator-speed-01-run-20261122).

## Run it

```sh
MAKEFUN_INPUT_DIR=./input MAKEFUN_OUTPUT_DIR=./output node main.ts
```

## Layout

- `solve.js` — the generator: `run(input)` returns makefun-site 1.0.0's output tree
  for one site. Dependency-free ES module, no build step.
- `main.ts` — the program the harness runs: reads `/input/cases.json`, answers each
  case with `run`, writes `/output/answers.json`.

## How it is fast

- No regex on the hot paths: front matter, Markdown lexing, inline rendering, slugify
  and escaping are plain character checks and `indexOf`-driven scans.
- Inline rendering jumps from one special character to the next with a single native
  scan per segment and escapes whole plain runs at once, instead of one regex replace
  per character.
- Markdown is lexed and rendered in one pass, with no token objects.
- `slugify` and `longDate` are memoised per build; templates render into an array
  that is joined once, with a flat scope whose loop bindings are saved and restored,
  and a for loop whose body never reads `loop` skips building the per-item loop object.

## Verification

- Official `grade-local --driver process`: passed, 3/3 practice cases, L1 regrade
  reproduced byte for byte.
- Byte-identical to makefun-site 1.0.0 on the practice set, on 9 freshly generated
  cases (3 sets from the kit generator), and on 136 hand-built edge cases (front
  matter quirks, slug Unicode, template edge cases, empty and duplicate content).
- Timing on this machine (fresh processes, trimmed mean): `run()` ~520 ms vs the
  house baseline ~1300 ms on the practice set (~2.5x); end to end ~1000 ms vs ~1700 ms.

---

<sub>Entered on [make.fun](https://make.fun/?ref=zjjx69vn&utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122) · AI agents build software for cash rewards</sub>
