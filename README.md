<h1 align="center">Honey for Claude</h1>

<p align="center">
  <em>Write less code. Say less about it.</em>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/skills-14-111111?style=flat-square" alt="14 skills">
  <img src="https://img.shields.io/badge/subagents-3-111111?style=flat-square" alt="3 subagents">
  <img src="https://img.shields.io/badge/hooks-3-111111?style=flat-square" alt="3 hooks">
  <img src="https://img.shields.io/badge/output%20tokens-−15%25-111111?style=flat-square" alt="minus 15 percent output tokens">
  <img src="https://img.shields.io/badge/license-MIT-111111?style=flat-square" alt="MIT license">
</p>

<p align="center">
  <a href="#install">Install</a> ·
  <a href="#what-runs-where">What runs where</a> ·
  <a href="#skills">Skills</a> ·
  <a href="#eson">ESON</a> ·
  <a href="#numbers">Numbers</a> ·
  <a href="#how-this-differs-from-upstream">Differences</a>
</p>

---

**[Honey (I Shrunk the AI)](https://github.com/Green-PT/honey-for-devs)** by
[Green-PT](https://github.com/Green-PT), repackaged so it installs as a marketplace in the
Claude app — web, desktop, mobile and Cowork.

Upstream doesn't add there. The app reads `.agents/plugins/marketplace.json`, and upstream's
copy points at a directory holding only the Codex build: no plugin manifest, no agents, no
hooks. This repo fixes the packaging and nothing else.

Skill and agent content is unchanged — 14/14 skill bodies, 3/3 agent bodies and 16 code
files verified byte-identical against upstream.

Using Claude Code in a terminal? Install upstream. It works there and covers every other
platform besides.

---

## Why

Agents are paid by the token, and most tokens are waste. Not wrong output — *surplus*
output. A helper class where a one-liner would do. Three paragraphs explaining code that
reads fine on its own. A tool result pasted in full when a summary and a hash would carry
the same meaning.

Honey attacks that surplus on three independent levers:

1. **Less code.** Walk the ladder, stop at the first rung that holds — does it need to
   exist → is it already in the repo → stdlib → language native → existing dependency →
   one line → minimum block.
2. **Less prose.** No wind-up, no hedging, no narrating code that already reads clearly.
   Answer first.
3. **Denser agent-to-agent handoffs.** When the reader is a program, hand it the most
   token-efficient format it parses losslessly.

It isn't a mode you invoke. It's a writing style the agent applies reflexively, so it costs
no reasoning tokens to decide whether to apply it.

Intensity: `lite` keeps the explanation, `full` is the default, `ultra` is answer-only.
Never simplified away: input validation, error handling, auth, accessibility, and anything
you explicitly asked for.

---

## Before / after

You ask for a debounce helper. Your agent writes a class, adds a config object, wires up
cancel/flush methods, and explains the event loop.

With Honey:

```js
// honey: setTimeout is the debounce
const debounce = (fn, ms) => (...a) => (clearTimeout(fn.t), fn.t = setTimeout(() => fn(...a), ms));
```

One line, one `honey:` marker naming the shortcut, no essay attached.

---

## Install

**Settings → Plugins → `+` → Add marketplace**

```
S1lverElixir/honey-for-claude
```

Then **Sync → Install**.

Already added a marketplace named `honey`? Remove it first (⋮ → Remove) or the app serves
you a cached copy.

Verify: type `/` in a chat. Fourteen `honey-*` commands should appear.

---

## What runs where

| | Chat — web, desktop, mobile | Cowork |
|---|:---:|:---:|
| 14 skills | ✅ | ✅ |
| 3 subagents | — | ✅ |
| 3 hooks | — | ✅ |

Hooks and subagents grey out in plain chat. That's an Anthropic platform limit, not a
packaging bug. Every skill works everywhere, including the full `honey` core.

---

## Skills

### Core

| Command | What it does |
|---|---|
| `/honey` | The three levers. YAGNI and stdlib-first for code, terse prose for everything else |
| `/honey-chat` | The prose core with no tool dependencies — paste into a Claude Style or Project instructions |

### Code

| Command | What it does |
|---|---|
| `/honey-review` | Reviews a diff for what Honey would cut: speculative generality, hand-rolled stdlib, single-caller abstractions, dead code |
| `/honey-debt` | Harvests every `honey:` comment into a ledger, so deliberate shortcuts get tracked instead of quietly going permanent |
| `/honey-design` | Same pixels, fewer tokens. For landing pages and UI where polish *is* the spec — trims how the design is written, never how it looks |
| `/honey-memory` | Sets up a committed `PROJECT.md` so agents stop rediscovering the same facts every cold session |
| `/honey-compress` | Rewrites `CLAUDE.md`-style context files tersely, cutting input tokens every session |

### Bulk data

| Command | What it does |
|---|---|
| `/honey-ccr` | Compress-Cache-Retrieve for huge repetitive arrays — logs, scans, time series. Keeps an informative sample, caches the rest behind a retrievable hash |
| `/honey-px` | Reads dense read-only text as rendered PNG pages; image tokens scale with pixels, not characters. Lossy on exact strings — never for files you'll edit |
| `/honey-loop` | Cost discipline for recurring `/loop` runs, where waste multiplies by iteration count |

### Orchestration and reporting

| Command | What it does |
|---|---|
| `/honey-hive` | When to delegate to read-only subagents instead of working inline |
| `/honey-superpowers` | Stacks Honey onto Superpowers-style workflows so dispatched subagents inherit the levers |
| `/honey-gain` | Reports the committed benchmark scoreboard from `bench/` |
| `/honey-eco` | Session output tokens and CO₂ via the committed EcoLogits port — needs shell, so Cowork only |

---

## ESON

A wire format for agent-to-agent payloads. Repeated record keys are emitted once, declared
row counts catch truncated messages, JSON-compatible cells keep their types.

Measured against compact JSON across five handoff documents, `o200k` tokenizer:

| Format | Valid JSON? | vs compact JSON |
|---|:--:|---:|
| JSON (pretty) — the unprompted default | yes | **+55%** |
| JSON (compact) | yes | 0% |
| TOON | no | −20% |
| JSON (columnar) | yes | −22% |
| **ESON** | no | **−28%** |

Comprehension ties JSON at 100% on key-lookup, column-match, nested-cell and nested-array
access. Positional access and in-context counting fail across *all* formats — a model
limit, not ESON's.

Spec: [`eso/SPEC.md`](eso/SPEC.md) · Codec: [`eso/index.js`](eso/index.js) ·
CLI: `node bin/eso.js encode|decode|crush`

**CCR** ([`eso/ccr.js`](eso/ccr.js)) covers the other case: arrays too big to read, too
uniform to matter. Keeps endpoints, anomalies and head/tail, drops the redundant middle to
a local cache, leaves a hash you can expand.

---

## Input precompression

The three levers cut **output**. There is symmetric waste on the **input** side — filler,
pleasantries, sentences the prompt repeats to itself.

[`hooks/precompress.js`](hooks/precompress.js) is a deterministic, **no-model** compressor
that strips them before the prompt reaches the LLM. Code, paths, URLs, double-quoted
strings and numbers pass through verbatim — it never touches a token you'd need exact.

```bash
printf '%s' 'Hi! Could you please write a function `add(a, b)` that returns their sum? Thanks so much in advance!' \
  | node hooks/precompress-cli.js
# write a function `add(a, b)` that returns their sum? in advance!
```

Upstream keeps this as a **measured negative result**, and so do I. On a hand-written
verbose corpus it cuts −16.5% median. On **266 real prompts from 35 actual sessions** it
cuts 2.5% total, median 0% — 219 of those 266 compress to nothing, because real prompts are
already terse. Deterministic compression can't catch *reworded* restatement; that needs a
model, so this is the ceiling, not a tuning gap.

The honest conclusion from upstream: **the prompt is the wrong target.** Real input volume
in agentic coding is tool output — CCR's domain — and re-pasted context across turns, not
human pleasantries. It ships as a CLI filter you can reach for, never wired always-on.

---

## Subagents and hooks

Cowork only.

| Subagent | Role |
|---|---|
| `hive-scout` | Read-only code locator. Returns a map of hits, not a tour |
| `hive-reviewer` | Reviews a diff, returns id-keyed findings as data, not a write-up |
| `hive-builder` | Surgical change, max 2 files, returns a change-manifest |

| Hook | Fires |
|---|---|
| `SessionStart` | Re-activates Honey each session until you switch it off |
| `SubagentStart` | Injects the Honey directive into every spawned subagent |
| `PostToolUse` | Compresses bulky Bash output before it reaches context |

### Carbon badge

[`hooks/statusline.js`](hooks/statusline.js) renders a 🍯 badge with a live CO₂ estimate for
the session and the CO₂/$ saved against a no-Honey baseline. The numbers come from the
committed EcoLogits port in [`hooks/eco.js`](hooks/eco.js) — no network, no API key.

The saved figure is a **modelled counterfactual**, not a measurement, and it always carries
the bench stamp it came from. `/honey-eco` expands the badge into a full breakdown.

### honey-usage

[`bin/usage.js`](bin/usage.js) reads the session data your coding agents already write to
disk and reports **actual** token usage — tokens, approximate USD, served CO₂ — per app and
model. Zero dependencies, no network, nothing leaves your machine.

| App | Source |
|---|---|
| `claude` (Claude Code) | `$CLAUDE_CONFIG_DIR` or `~/.claude` — `projects/**/*.jsonl` |
| `codex` (Codex CLI) | `$CODEX_HOME` or `~/.codex` — `sessions/**/*.jsonl` |
| `opencode` | `($XDG_DATA_HOME` or `~/.local/share)/opencode/opencode.db` |

```bash
node bin/usage.js
```

---

## Numbers

From [`bench/results/combined.md`](bench/results/combined.md) — 23 tasks, `claude-opus-4-8`,
345 generations.

| Variant | Tests pass | Judge ±sd | Output tokens |
|---|---|---|---|
| baseline | 97% | 94 ±7 | 90,795 |
| **honey** | **100%** | 93 ±6 | **77,098 · −15%** |

On self-contained code tasks the gap widens to **−49%** — 8,126 tokens against 15,996.

The authors' own caveat, kept here: **quality is a tie, not a gain.** Token savings are
real; a quality improvement is not claimed. Every figure is a paired per-task median with a
p-value, and `(ns)` results are ties, not wins. Method:
[`bench/METHODOLOGY.md`](bench/METHODOLOGY.md).

---

## How this differs from upstream

| | Upstream | Here |
|---|---|---|
| Skills | 14 | 14 — bodies byte-identical |
| Subagents | 3 | 3 — bodies byte-identical |
| Code: hooks, ESON, bin | — | 16 files byte-identical |
| Version | 1.3.1 | 1.3.1, kept in sync |
| Cursor · Windsurf · Cline · Kiro · Hermes · OpenClaw · Codex | yes | removed |
| CI workflows, test suite, npm manifest | yes | removed |

Only skill frontmatter changed: characters the Claude.ai marketplace validator rejects —
emoji, `≤`, em dashes — replaced with ASCII. Instruction text untouched.

Want Cursor, Gemini CLI, Hermes or the one-line installer?
[Upstream](https://github.com/Green-PT/honey-for-devs) ships all of it.

---

## Updating

The version deliberately tracks upstream (`1.3.1`) instead of bumping on every change, so
the app may not refresh on its own. Force it: **Settings → Plugins → Honey → ⋮ → Update**.

---

## License

MIT, same as upstream. See [`LICENSE`](LICENSE) and [`NOTICE`](NOTICE).

Every idea, skill and benchmark here is
**[Green-PT](https://github.com/Green-PT)**'s work. This repo only rearranges the packaging.
If Honey earns its place in your setup, star
[the original](https://github.com/Green-PT/honey-for-devs).

The benchmarks compare Honey against
[Ponytail](https://github.com/DietrichGebert/ponytail) and
[Caveman](https://github.com/JuliusBrussee/caveman). Both are worth your time.
