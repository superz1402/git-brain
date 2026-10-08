# RECOVERY.md — the wake-up protocol

You are a fresh session. Your predecessor died mid-sentence, and the last thing it did may or may not have been pushed. This checklist is the order of operations. Do not skip, do not reorder.

## 0. Ground rule

Do not restart. Do not re-ask questions the repo answers. Do not rebuild what production already proves works. Your predecessor was not stupid; assume their state is *recoverable*, not wrong.

## 1. Read the master file
`START_HERE.md` — system state, live priorities, MUST-NOT list. If it's missing or >30 days stale, reconstruct cautiously from `state/CURRENT.md` + `git log` and *rewrite it first*.

## 2. Check what actually happened after the last handoff
```bash
git log --oneline -30          # commits after the handoff was written
git fetch origin && git status -sb   # is local behind/ahead of remote?
```
An unpushed local commit means your predecessor died before closing — treat its claims as *unfinished*, verify anything it says was done.

## 3. Read the live state
- `state/HANDOFF.md` — the situation snapshot + recovery checklist
- `state/CURRENT.md` — top section only (it's a stack; lower sections are history)
- `worklog.md` — the last 1-2 entries: what was in flight when it died

## 4. Verify environment
Credentials/tokens present and working? (Expired tokens are the #1 cause of "silent" close failures — which is why Convention 7 requires verifying the remote, not just pushing.)

## 5. Check the outside world
If your agent has inbound channels (notifications, DMs, mentions): check them *before* producing anything. Someone may have answered the thing you were about to re-ask.

## 6. Continue the queue
Pick up `state/CURRENT.md`'s live priorities from the top. If an item needs context the repo lacks, that's a finding — document the gap, don't improvise the fact.

## 7. Close the loop (every session, no exceptions)
1. Update docs (CURRENT.md at minimum; HANDOFF.md snapshot if the situation changed)
2. Security scan (no secrets in diff — ever)
3. Commit with a claim-style message
4. Push
5. **Verify remote**: `git rev-parse HEAD origin/main` must match
6. Update the worklog with the template entry

---

## Failure modes this protocol has survived

| Failure | The tell | The counter |
|---|---|---|
| Session died mid-task | Unpushed commits, stale CURRENT | Step 2: treat predecessor claims as unfinished |
| Context summarizer hallucinated state | "Your memory may be stale about X" when X was current | Step 1: read the domain files *before* re-researching |
| Push "succeeded" but didn't | Local-only belief of completion | Step 7.5: rev-parse both, compare |
| Priorities reconstructed wrong | Session works on last-touched, not top-priority | Steps 1+3: master file + CURRENT stack, in that order |
| Duplicate public action | Fresh session re-asks an answered question | Step 5: check inbound before producing |
