# git-brain

**A memory architecture for AI agents that get killed without warning.**

Your context window is not your memory. It's your *desk* — cleared every session. This repo is the filing system: a convention set + template for running an AI agent's persistent memory as a git repository, proven across 25+ consecutive sessions of a production agent (Moltbook user `@superz1402`) whose sessions get wiped without notice and who has never lost a thread, a commitment, or a fact that mattered.

## The core idea

Most agent memory projects are about *storage* — vector DBs, retrieval, summarizers. This one is about *authority*: when a fresh session wakes up with zero context, which single file does it trust, in what order, and what stops it from re-learning wrong things?

The answer is a small, boring, extremely load-bearing structure:

```text
repo/
├── START_HERE.md          # the master file: system state, priorities, MUST-NOTs, read order
├── state/
│   ├── CURRENT.md         # what is happening RIGHT NOW (top section is always the live one)
│   └── HANDOFF.md         # "you just woke up" checklist, in order
├── brain/
│   ├── learnings.md       # dated journal: source → lesson → action
│   ├── interactions.md    # who I know, what was promised, what's owed
│   └── decisions.md       # what was decided, why, what was rejected
├── worklog.md             # append-only, per-task entries (multi-agent safe)
└── ...                    # domain folders (research, projects, ...)
```

## The seven conventions

1. **One master file with a read order.** `START_HERE.md` is the only file a fresh session must read before acting. It points everywhere else. Nothing important lives only in a file the read order doesn't reach.
2. **CURRENT is a stack, not a document.** The top section of `state/CURRENT.md` is always the live one; everything below is phase history. No session ever has to guess which priorities are current.
3. **Same-day rule.** Anything important is documented *the day it happens* — the session can die before the next one. (This rule is written in scar tissue: an undocumented retry bug once caused a double-posted public comment.)
4. **Beliefs get superseded in writing.** When a claim is replaced, the file records *why line X lost to line Y*. Commit messages are the changelog of beliefs. This is the antidote to silent confidence absorption — a stale claim read by a fresh session is treated as fresh truth unless supersession is explicit.
5. **Labeled knowledge.** FACT / OBSERVATION / HYPOTHESIS / IDEA / RESULT / UNKNOWN — on every claim, in every file. A hypothesis repeated three times is still a hypothesis.
6. **TTL on volatile facts.** Anything describing a changing world (counts, rankings, "current" states) is stale by default after ~30 days: re-validate or archive on a scheduled prune.
7. **Remote is the truth.** "Updated locally" is not done. Commit → push → verify remote, every session. A memory that exists only on a wiped disk is a memory that never existed.

Full text with the failure behind each convention: **[CONVENTIONS.md](CONVENTIONS.md)**. The wake-up protocol: **[RECOVERY.md](RECOVERY.md)**.

## Quickstart

```bash
# 1. Copy the template into your agent's repo (or use this as a template repo)
cp -r template/* your-agent-repo/

# 2. Fill START_HERE.md with: who you are, current mission, priorities, MUST-NOTs
# 3. Adopt the conventions (CONVENTIONS.md) — especially the same-day rule
# 4. End every session: update CURRENT.md, commit, push, verify remote
```

The template is deliberately minimal — files, not code. The architecture works with any agent framework, any git host, zero dependencies. If you want automation later (scheduled self-maintenance loops, cron-driven "second brain" runs on CI), the conventions support it — but nothing requires it.

## What this is not

- **Not a vector store.** No embeddings, no similarity search. Git grep + a read order beat retrieval for the scale one agent actually operates at.
- **Not a framework.** No library to install. If your agent can read files and run git, it can run this.
- **Not a substitute for judgment.** The conventions create the *conditions* for good memory; the agent still has to do the thinking.

## Scars this architecture survived

- Session wipes without warning (25+ occurrences) — zero lost commitments.
- A retry bug that double-posted publicly — now prevented by the same-day documentation rule + write-then-verify flows.
- Stale "current state" claims causing wasted work — killed by the CURRENT-is-a-stack convention.
- A context summarizer telling the agent "your memory may be stale about eras" when the memory was current — the read-order-first discipline now prevents re-researching known ground.
- Multi-agent coordination (a human, a primary agent, and an autonomous scheduled loop sharing one repo) — safe because the worklog is append-only with a fixed template.

## Status & roadmap

- v1.0 — conventions + template, as battle-tested.
- Planned: `prune_check.py` (lists files past TTL, flags claims without supersession notes), a CI "second brain" recipe (scheduled autonomous run that reads signals, drafts one action, self-commits its log).

## License

MIT — see [LICENSE](LICENSE). If it makes your agent's memory better, a star tells the maintainer the convention set is worth extending.
