---
name: guided-review
description: Walks me through the current change in small, plain-language bites, like a guided tour. It opens with a short summary of the goal/problem, then for each bite says what the code concretely is/does before explaining why, shows one small snippet at a time, and asks "OK, more detail, or change requests?" — OK moves on, a request for more detail explains again more simply with a concrete example, and a change request makes the edit (re-verifying it with scoped checks) before continuing. Use for a guided, interactive review of uncommitted changes or of a specific commit, especially when I want it explained in plain terms and small steps rather than dumped on me.
disable-model-invocation: true
---

Give me a guided tour of the current change so I can review it without
navigating or decoding it myself. Bias hard towards **small bites and plain
words** — see below. When in doubt, split one more time and simplify one more
notch.

## Setup

Figure out what's under review:
- **Default:** the current uncommitted changes (`git status`, `git diff` for
  unstaged, `git diff --cached` for staged).
- If I name a commit or range (e.g. `HEAD`, a SHA, `main..HEAD`): tour that instead
  (`git show <ref>` / `git diff <range>`).

Before touring anything, give me a **short, plain-language overview**: what
problem or goal this change is solving, in terms someone outside the codebase
would follow — one short paragraph, no jargon yet. Skip this only if the change
is trivial enough that it would be redundant (e.g. a one-line typo fix).

## Triage: spend my attention where the risk is

Before the tour, sort the changed files into two groups:
- **Needs your eyes:** business logic, security/permissions, data changes
  (migrations, deletes, money), public interfaces, anything surprising or
  where you made an assumption I didn't explicitly ask for.
- **Mechanical:** wiring, imports, renames, DTOs/boilerplate, config, generated
  code, tests that just mirror the implementation.

Show both lists with a one-line reason per file. Only the first group gets
the full stop-by-stop tour. The mechanical group gets **one** summary stop at
the end: one line per file, and I can pull any of them into a full tour.
When unsure which group a file belongs in, put it in "needs your eyes".
Skip triage for small changes (roughly 3 files or fewer) - just tour them.

Then decide a sensible **reading order** for the "needs your eyes" group -
most important first (tests that show the intended behavior, then
core/interface, then callers). Tell me the order and how many stops in one
line, then start.

## Sectioning: keep every stop small

A "stop" is **one small idea with one small snippet** — not a file, not a
whole method if that method does more than one thing. Rules of thumb:
- **One code block per stop.** Never two separate snippets (e.g. a template
  change *and* a script change) in the same stop — that's two stops.
- If a snippet would run past roughly 10–15 lines, or covers more than one
  behavior change, split it into more stops instead of showing it all at once.
- A method with two unrelated things happening in it (e.g. a reordering *and*
  a new branch *and* a new piece of state) is two or three stops, not one.
- Simple, single-purpose, short changes (a one-line fix, a renamed variable)
  can stay one stop — don't split trivia into ceremony.
- When genuinely unsure whether to split: split. An extra "OK" from me costs
  nothing; a wall of text costs me re-reading it.

## Per stop

For each stop, in order — **always in this order, every single stop**:

1. **Orient first, in plain words, before any "why".** One or two short
   sentences: what this piece of code concretely *is* or *does* when the app
   runs — not yet why it was written this way. E.g. "This is the page text
   shown when someone is locked out" — not "Because the controller passes a
   flag...". If I have to ask "worum geht's?" to get this, that's the skill
   failing, not me asking for something extra — do it unprompted, every time.
2. **Show the one small snippet** for just this stop (see Sectioning above).
3. **Then explain the why** — a few short sentences on why *this* stop was
   written this way, in plain language. Flag anything non-obvious or risky.
   Prefer describing what something does over just naming it; when a
   technical term is unavoidable, explain what it does in the same breath
   instead of dropping the bare term and moving on. Short sentences over long
   ones. No stacked technical vocabulary without unpacking it first.
4. **Ask: "OK, more detail, or change requests?"** Then **stop and wait.** Read
   my reply as one of these — and when unsure, treat it as a question, not
   approval:
   - **Explicit approval** (e.g. "ok", "next", "looks good", "passt", "weiter")
     → *only now* move to the next stop.
   - **A request for more detail** (e.g. "erklär's einfacher", "mit Beispiel",
     "versteh ich nicht") → explain the *same* stop again, even more simply: a
     concrete small example with real-ish values, or a plain analogy, rather
     than repeating the same abstract phrasing. Then ask again. Not approval.
   - **A sharp/technical follow-up** → answer at that level, don't retreat into
     the beginner framing once I've shown I'm following the detail. Match the
     depth I'm actually operating at, stop by stop — it can go both ways
     across the tour.
   - **Change request** → make the edit (see below), show exactly what
     changed, then **stay on this stop**, re-present it, and ask again —
     until I explicitly approve.

## Change requests and re-verification

After a change request that could plausibly affect behavior or types,
re-verify it — scoped and fast, never a full-project run mid-review: the
formatter in changed-files-only mode, a type-check scoped to just the touched
file(s) if the tooling supports that, and just the test file(s) covering the
changed code. Skip re-verification for purely cosmetic edits (comment
wording, whitespace) — it'd just be noise. A full suite run, if warranted,
belongs at the end of the tour or when I ask for one, not after every edit.

## When the tour is done

Once every stop has been reviewed (and any change requests applied), propose a
**commit message** for the whole reviewed change:
- as a code block so I can copy it,
- in the style of the existing history (`git log --oneline -10`), else Conventional Commits,
- reflecting what we actually ended up with, including any edits made during the tour.

I always commit it myself — don't ask whether to commit, don't commit yourself.

## Rules

- **Small bites, plain words, every stop, no exceptions.** When in doubt, err
  smaller and simpler — that's the whole point of this skill.
- **Advance only on an explicit go-ahead for the current stop.** Never move on
  by yourself; a question or a request for more detail is not approval.
- Always re-state which stop is current after answering, so we never lose our
  place. If my reply is ambiguous, ask "ready for the next stop?" instead of assuming.
- This is **review only** — don't commit, and don't ask whether to.
- If there's nothing to review, say so.
