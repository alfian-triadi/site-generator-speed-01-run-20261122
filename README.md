# Fast makefun-site — site-generator-speed-01

[![make.fun](https://make.fun/badge/site-generator-speed-01-run-20261122.svg)](https://make.fun/c/site-generator-speed-01-run-20261122)
[![rank](https://make.fun/badge/site-generator-speed-01-run-20261122/alfian-triadi/site-generator-speed-01-run-20261122/rank.svg)](https://make.fun/c/site-generator-speed-01-run-20261122)
[![reward](https://make.fun/badge/site-generator-speed-01-run-20261122/alfian-triadi/site-generator-speed-01-run-20261122/reward.svg)](https://make.fun/c/site-generator-speed-01-run-20261122)

A faster drop-in for [makefun-site 1.0.0](https://make.fun/c/site-generator-speed-01-run-20261122), built for the make.fun competition ["Build a static site faster, 22 November 2026"](https://make.fun/c/site-generator-speed-01-run-20261122).

`run(input)` in `solve.js` returns, for every case's input, exactly the answer makefun-site 1.0.0 returns — the output tree as a JSON object of every path and its text, byte for byte — while running about 3x faster than the house baseline on the practice set (warmed `run()` over the 3 practice cases: ~210 ms vs ~620 ms for the baseline on this machine; whole-program CPU ~1.08 s vs ~1.64 s).

## How it is faster

The reference pipeline is reimplemented with the same semantics and the hot paths rewritten:

- **Chunked HTML escaping** — one native scan, returning the string untouched when it holds nothing to escape.
- **Range-based inline Markdown** — no per-character regex, no string slice per nested span; strong/emphasis/link bodies render in place.
- **Single-pass Markdown rendering** — blocks render straight to output, no token objects; per-line block tests gated on the first character.
- **Scan-based front matter** — lines walked by index, the body taken as one slice instead of splitting the whole file.
- **ASCII fast path for slugs** — NFKD normalisation and combining-mark removal skipped when the text is pure ASCII (exact: NFKD is the identity there).
- **Array-joined template rendering** over a scope stack, with one vars/loop pair reused per `for` node and the root scope shared instead of spread per render.
- **Word counts cached** on each source instead of recomputed per card.

Nothing else changes: same front-matter rules, same template dialect, same slug rule, same report. `main.ts` is the kit's driver unchanged; it reads `/input/cases.json`, answers each case with `run`, and writes `/output/answers.json`.

## Layout

- `main.ts` — the program the harness runs.
- `solve.js` — dependency-free ES module; `run(input)` is the entry point.
- `package.json` — `{ "type": "module" }`.

## License

MIT — see [LICENSE](LICENSE).
