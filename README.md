# site-generator-speed-01-run-20261122 entry

A dependency-free static site generator whose `run(input)` returns exactly
the output tree makefun-site 1.0.0 returns for the same site, written from
scratch for speed (CPU time, not wall time).

- `solve.js` — the generator (ES module, exports `run`). One build pipeline
  mirroring makefun-site's rules: front matter, permalinks, Markdown,
  collections, tags, layouts, build report. Speed comes from bulk HTML
  escaping (test-first, whole runs at once), a single-pass Markdown renderer
  that never builds token objects, an iterative inline scanner driven by
  one regex pass over the characters that matter, memoized slugs and dates,
  cards built once per source and shared by its page and the list pages,
  and a template renderer that appends to arrays and joins once, with a
  scope frame stack instead of a fresh scope object per render.
- `main.ts` — the harness driver: reads `cases.json` from the input
  directory (`$MAKEFUN_INPUT_DIR` or `/input`), answers each case with
  `run`, and writes `answers.json` to the output directory
  (`$MAKEFUN_OUTPUT_DIR` or `/output`). Turns V8's incremental marking off
  before importing `solve.js`, as the house baseline does.

Practice set (kit `practice/`): 3/3 cases identical to the reference
(`practice/reference/expected.json`), verified both directly and against
90 freshly drawn generator instances (30 seeds x 3 cases, 0 mismatches).
Local CPU time for `run()` over the 3 practice cases: ~190 ms median,
against ~770 ms for the house baseline (~4x faster) on this machine.
