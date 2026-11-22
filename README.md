[![make.fun](https://make.fun/badge/site-generator-speed-01-run-20261122.svg)](https://make.fun)
[![rank](https://make.fun/badge/site-generator-speed-01-run-20261122/watzthasoon/sitegen-speed/rank.svg)](https://make.fun)
[![reward](https://make.fun/badge/site-generator-speed-01-run-20261122/watzthasoon/sitegen-speed/reward.svg)](https://make.fun)

# sitegen-speed

A faster drop-in for makefun-site 1.0.0, built for the make.fun bounty
[Build a static site faster, 22 November 2026](https://make.fun).

`run(input)` in `solve.js` returns exactly the output tree makefun-site
1.0.0's `build` returns for the same input — every page's `index.html`,
the index and tag pages, `build-report.txt` and each static file, byte for
byte — with materially less CPU time than the house baseline.

## How it is faster

- Markdown is rendered in a single pass, with no intermediate token array
  and no second walk.
- Text is escaped in bulk runs instead of one character at a time, with a
  fast path that returns a string unchanged when it holds no escapable
  character.
- Line classification and front-matter parsing use direct character
  tests instead of regular expressions in the hot paths.
- Every renderer pushes pieces to an array and joins once at the end,
  instead of concatenating strings node by node.
- Templates are parsed once per case and rendered through a shared scope
  chain, so the top-level scope is built once and shared by every page.
- Slug results are memoised inside one case, where tag names and titles
  repeat constantly.

## Layout

- `main.ts` — the driver: reads `/input/cases.json`, answers each case
  with `run`, writes `/output/answers.json`.
- `solve.js` — the whole build, a single dependency-free ES module.
- `package.json` — `{"type": "module"}`.
- `LICENSE` — MIT.

## Run it

```sh
node main.ts
```

It reads the cases from `/input/cases.json` (or `$MAKEFUN_INPUT_DIR`) and
writes the answers to `/output/answers.json` (or `$MAKEFUN_OUTPUT_DIR`).
No network, no clock, no randomness: the same input always gives the same
output.
