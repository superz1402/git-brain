# CONVENTIONS.md — the seven rules, with the failures behind them

Each convention lists the concrete failure that produced it. None are theoretical.

## 1. One master file with a read order

**Rule.** `START_HERE.md` is the single file every fresh session reads before acting. It contains: who the agent is, current system state, current priorities *in order*, a MUST-NOT list, and the read order for everything else.

**The failure it prevents.** A fresh session that reads "whatever looks relevant" reconstructs its priorities from the newest files — which are often the least important. Worse, it re-asks questions the repo already answers, or rebuilds infrastructure that already works. Both happened before the master file existed.

**In practice.** The MUST-NOT list is as important as the priorities: it is where you put the expensive lessons ("do not rebuild X", "do not contact Y", "never auto-approve Z") so every future session inherits them without paying for them.

## 2. CURRENT is a stack, not a document

**Rule.** `state/CURRENT.md` has one live section at the top. When priorities shift, the old live section moves down (marked historical) and the new one goes on top. Nothing is ever edited in place below the line.

**The failure it prevents.** A single mutable "current status" doc accretes contradictory claims across sessions. A fresh session can't tell which half is real, so it trusts the wrong half.

**In practice.** Date every live section. When you retire one, write one line on *why* it ended (done / superseded / abandoned) — that line is what the next session actually needs.

## 3. The same-day rule

**Rule.** Anything important — a decision, a contact, a bug, a discovery — is documented in the repo the same day it happens, before the session ends.

**The failure it prevents.** Sessions die without warning. An undocumented bug fix means the next session repeats the bug. An undocumented promise means a contact is silently dropped. (Real case: a comment-retry bug went undocumented, and the retry path double-posted a public comment days later.)

**In practice.** Batch at close of session is acceptable; mid-session checkpoints for anything you would be embarrassed to lose. The test: if this session dies in 60 seconds, what would the next session wrongly believe?

## 4. Beliefs get superseded in writing

**Rule.** When new evidence replaces a claim, the file records the supersession: `SUPERSEDED (date): why line X lost to line Y`. Never silently edit the claim away.

**The failure it prevents.** Silent confidence absorption: a fresh session reads the old claim and the new one in different files and treats both as live. Memory systems don't fail from missing data first — they fail from unmarked contradictions.

**In practice.** Git commit messages double as the belief changelog. Write them as claims ("X replaced Y because Z"), not as file lists ("updated some files").

## 5. Labeled knowledge

**Rule.** Every non-trivial claim carries a label: FACT (verified, source known) / OBSERVATION (seen, not verified) / HYPOTHESIS (testable, untested) / IDEA (unjudged) / RESULT (experiment outcome) / UNKNOWN (honest gap).

**The failure it prevents.** In agent memory, rumor becomes fact by repetition. A hypothesis copied across three files reads like an established truth to a fresh session.

**In practice.** Labels cost two words. The rule has a strict corollary: repetition never promotes a label — only a new source or a test does.

## 6. TTL on volatile facts

**Rule.** Any claim about a changing world (counts, rankings, "current" anything) is stale by default after ~30 days. Scheduled prune: re-validate or archive.

**The failure it prevents.** Stale metrics rot into false confidence. A "karma: 12" from three months ago is worse than no data — it drives decisions.

**In practice.** Put `updated: YYYY-MM-DD` on every knowledge file at write time. The prune script (planned: `prune_check.py`) just greps for old dates and unlabeled claims.

## 7. Remote is the truth

**Rule.** A session ends with: docs updated → security scan → commit → push → *verify the remote* (compare local HEAD to remote HEAD). "Updated locally" is never "done".

**The failure it prevents.** The disk wipes. Local-only commits are lost work — and worse, lost *corrections*: the next session inherits the last pushed (possibly wrong) state and re-makes the mistakes.

**In practice.** The verification step matters as much as the push: pushes fail silently (auth expiry, hooks). `git rev-parse HEAD origin/main` after every push.

---

## Multi-agent coordination (append-only worklog)

When more than one writer touches the repo (a primary agent, scheduled CI loops, delegated subagents):

- One shared `worklog.md`. **Append-only.** Never rewrite others' entries.
- Fixed template per entry: task ID, agent name, task, work log bullets, stage summary.
- Before starting work, read the worklog tail — what previous agents did is as important as what the files say.

This is what makes an autonomous scheduled loop (a "second brain" running on CI) safe: it commits its own log entries, and any session can audit exactly what it did and why.
