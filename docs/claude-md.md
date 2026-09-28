# Add this to your CLAUDE.md

**The pack does not fully work without this step.** No installer edits your
`CLAUDE.md` - that file is yours - so the block gets added once, by you.

From the clone, one command adds it (and refuses to add it twice):

```bash
grep -q 'os-done-or-not' ~/.claude/CLAUDE.md 2>/dev/null || cat docs/routing-block.md >> ~/.claude/CLAUDE.md
```

Or paste it by hand - near the top matters, earlier instructions carry more
weight than later ones. The block lives in
[`docs/routing-block.md`](routing-block.md); copy it from there, so there is
one copy of it to keep current.

On another agent the same block goes in that agent's own instructions file
instead. [`docs/other-agents.md`](other-agents.md) has the file and the command
for each, and the hooks below are a separate matter on those tools.

## Why this is required and not a nicety

A skill is **model-invoked**: the agent decides whether to load it. Two of the
moments the block names are ones the agent will not notice on its own.

- Asking you to do something does not feel like a task to the agent, so
  `os-step-by-step` gets skipped and you get a wall of commands instead.
- Asking you a technical question feels like ordinary conversation, so
  `os-ask-simple` gets skipped and you get jargon with no recommendation.

The skill descriptions are written in a directive form ("ALWAYS invoke this
skill…"). On Claude Code (2026-09-12) they switched on in 98-100% of the test
runs on Sonnet 5 and Opus 5 and 85% on Haiku 4.5; the evals do not compare this form with other
wordings. The block adds the part a description cannot: an obligation, and
a table that says which moment maps to which skill. It is also re-injected
after the conversation is compacted, so it survives long sessions.

## Optional: the status line

Independent of the skills, this one line per reply removes the most common
confusion - not knowing whether the agent has finished.

```markdown
## End every reply with a status line

Finish every response with exactly one of these, on its own last line, in the
language of the conversation:

- ✅ **Done.** - finished and verified.
- ⏸ **Waiting on you.** - blocked on me; name what is needed in ten words or fewer.
- ⏳ **Still working.** - more steps are coming.
- ⚠️ **Done, with a caveat.** - finished, but something is unverified or risky.

Never claim ✅ for anything not actually verified - use ⚠️ instead.
```

This belongs in `CLAUDE.md` rather than in a skill for the same reason: it has to
hold for every reply, and a skill cannot guarantee that.

## The session-start hook does not replace this block

The plugin already wires a SessionStart hook (`hooks/session-start.sh`): at
the start of every session it injects the same routing table plus the last
session's report, so a new session begins oriented instead of re-exploring
the repository. Nothing to add by hand - installing the plugin turned it on.

Keep the block anyway. The hook speaks once, at the start; the `CLAUDE.md`
block is re-injected after the conversation is compacted, so in a long
session it is the copy that survives. They say the same thing on purpose.

To switch the hook off: `export OPEN_STEPS_NO_SESSION_START=1`.
