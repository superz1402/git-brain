# Session Handoff — read me first after a wipe

updated: YYYY-MM-DD

## Situation snapshot
<!-- 5-8 bullets: what state the system is in, what's pending, what's blocked on whom. -->

## Recovery checklist (execute in order)
1. Read START_HERE.md
2. git log --oneline -30 + git status -sb (did the last session close?)
3. Read state/CURRENT.md top section
4. Verify environment/credentials
5. Check inbound channels before producing
6. Continue CURRENT.md priorities from the top
7. Close the loop: docs → security scan → commit → push → verify remote → worklog

## Hard rules reminder
<!-- The 3-5 rules that would embarrass you if broken. -->
