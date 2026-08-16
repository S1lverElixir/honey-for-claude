# Honey for the Claude app

**Honey (I Shrunk the AI)**, repackaged so it installs as a marketplace in the
**Claude app** — web, desktop, mobile and Cowork.

Upstream: [Green-PT/honey-for-devs](https://github.com/Green-PT/honey-for-devs) · Author: **Green-PT** · MIT

---

## Why this fork exists

The upstream repository does not add as a marketplace in the Claude app. The app reads
`.agents/plugins/marketplace.json`, which pointed at a directory containing only the
Codex build — no plugin manifest, no agents, no hooks.

This repo fixes the packaging. **Skill and agent content is unchanged** — verified
byte-for-byte against upstream (14/14 skill bodies identical, 3/3 agents identical,
16 code files identical).

Using **Claude Code** in a terminal? Install upstream instead — it works there and
supports every other platform too.

---

## Install

**Settings → Plugins → `+` → Add marketplace**

```
S1lverElixir/honey
```

→ **Sync** → **Install**

Already added a marketplace named `honey`? Remove it first (⋮ → Remove), or the app
serves a cached copy.

Verify: type `/` in a chat — 14 `honey-*` commands should appear.

---

## What runs where

| | Chat (web / desktop / mobile) | Cowork |
|---|:---:|:---:|
| 14 skills | ✅ | ✅ |
| 3 subagents | ▫️ | ✅ |
| 3 hooks | ▫️ | ✅ |

Hooks and subagents are greyed out in plain chat — that is an Anthropic platform limit,
not a packaging problem. Every skill, including the full `honey` core, works everywhere.

---

## The three levers

Honey is not a mode you invoke. It is a writing style the agent applies reflexively.

1. **Less code** — most code needn't exist. Walk the ladder and stop at the first rung
   that works: does it need to exist → is it already in the repo → stdlib → language
   native → existing dependency → one line → minimum block.
2. **Less prose** — no wind-up, no hedging, no narrating readable code. Answer first.
3. **Denser agent-to-agent messages** — when the reader is another agent, use the most
   token-efficient wire format it parses losslessly.

Intensity: `lite` keeps explanations, `full` is the default, `ultra` is answer-only.
Safety-critical paths — input validation, error handling, auth, accessibility — are
never simplified away.

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
| `/honey-debt` | Harvests every `honey:` comment into a ledger, so deliberate shortcuts get tracked instead of rotting |
| `/honey-design` | Same pixels, fewer tokens. For landing pages and UI, where polish *is* the spec — trims how the design is written, never how it looks |
| `/honey-memory` | Sets up a committed `PROJECT.md` so agents stop rediscovering the same facts every cold session |
| `/honey-compress` | Rewrites `CLAUDE.md`-style context files tersely, cutting input tokens every session |

### Bulk data

| Command | What it does |
|---|---|
| `/honey-ccr` | **Compress-Cache-Retrieve** for huge repetitive arrays (logs, scans, time series). Keeps an informative sample, caches the rest behind a retrievable hash |
| `/honey-px` | Reads dense read-only text as rendered PNG pages — image tokens scale with pixels, not characters. Lossy on exact strings, never for files you'll edit |
| `/honey-loop` | Cost discipline for recurring `/loop` runs, where waste multiplies by iteration count |

### Orchestration & reporting

| Command | What it does |
|---|---|
| `/honey-hive` | When to delegate to read-only subagents instead of working inline |
| `/honey-superpowers` | Stacks Honey onto Superpowers-style workflows so dispatched subagents inherit the levers |
| `/honey-gain` | Reports the committed benchmark scoreboard from `bench/` |
| `/honey-eco` | Session output tokens and CO₂ via the committed EcoLogits port (needs shell — Cowork only) |

---

## ESON — Efficient Structured Object Notation

A wire format for agent-to-agent payloads. Same data, fewer tokens:

```
JSON   {"rows":[{"id":1,"name":"alpha","v":10},{"id":2,"name":"beta","v":20}]}
ESON   42% smaller, round-trips losslessly
```

Spec: [`eso/SPEC.md`](eso/SPEC.md) · Codec: [`eso/index.js`](eso/index.js) ·
CLI: `node bin/eso.js encode|decode|crush`

**CCR** ([`eso/ccr.js`](eso/ccr.js)) handles the other case — arrays too big to read but
too uniform to matter. It keeps endpoints, anomalies and head/tail, drops the redundant
middle to a local cache, and leaves a hash you can expand.

---

## Subagents & hooks (Cowork)

| Subagent | Role |
|---|---|
| `hive-scout` | Read-only code locator. Returns a map of hits, not a tour |
| `hive-reviewer` | Reviews a diff, returns id-keyed findings as data, not a write-up |
| `hive-builder` | Surgical change, max 2 files, returns a change-manifest |

| Hook | Fires |
|---|---|
| `SessionStart` | Re-activates Honey each session until you turn it off |
| `SubagentStart` | Injects the Honey directive into every spawned subagent |
| `PostToolUse` | Compresses bulky Bash output before it enters context |

Also included: [`hooks/statusline.js`](hooks/statusline.js) — a 🍯 badge with live CO₂,
and [`bin/usage.js`](bin/usage.js) — actual token usage across your coding agents.

---

## Numbers

From [`bench/results/combined.md`](bench/results/combined.md) — 23 tasks, `claude-opus-4-8`:

| Variant | Tests pass | Judge | Output tokens |
|---|---|---|---|
| baseline | 97% | 94 | 90,795 |
| **honey** | **100%** | 93 | **77,098 (−15%)** |

On self-contained code tasks the gap widens to **−49%** (8,126 vs 15,996 tokens).

The authors' own caveat, kept here: **quality is a tie, not a gain.** The token savings
are real; a quality improvement is not claimed. Every figure is a paired per-task median
with a p-value — see [`bench/METHODOLOGY.md`](bench/METHODOLOGY.md).

---

## How this differs from upstream

| | Upstream | Here |
|---|---|---|
| Skills | 14 | 14, **bodies byte-identical** |
| Subagents | 3 | 3, **bodies byte-identical** |
| Code (hooks, ESON, bin) | — | **16 files byte-identical** |
| Version | 1.3.1 | **1.3.1**, kept in sync |
| Cursor / Windsurf / Cline / Kiro / Hermes / OpenClaw / Codex files | yes | removed |
| CI workflows, test suite, npm manifest | yes | removed |

Only skill frontmatter was touched: characters the Claude.ai marketplace validator
rejects (emoji, `≤`, em dashes) were replaced with ASCII. Instruction text is untouched.

Need Cursor, Gemini CLI, Hermes or the one-line installer?
[Upstream](https://github.com/Green-PT/honey-for-devs) has all of it.

---

## Updating

The version deliberately tracks upstream (`1.3.1`) rather than bumping on every change,
so the app may not auto-refresh. Force it with **Settings → Plugins → Honey → ⋮ → Update**.

---

## Credits

All substance is the work of **[Green-PT](https://github.com/Green-PT)**. This repo only
re-lays-out the packaging. If it's useful, star
[the original](https://github.com/Green-PT/honey-for-devs).

The benchmarks compare Honey against
[Ponytail](https://github.com/DietrichGebert/ponytail) and
[Caveman](https://github.com/JuliusBrussee/caveman) — both worth a look.
