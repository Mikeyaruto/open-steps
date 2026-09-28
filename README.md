# Open Steps

[![License: MIT](https://img.shields.io/badge/License-MIT-black.svg)](LICENSE)

**English** · [Español](README.es.md) · [Français](README.fr.md) · [Русский](README.ru.md) · [Українська](README.uk.md) · [한국어](README.ko.md) · [中文](README.zh.md)

**Skills that keep development open to the person running it: the sessions,
the decisions, the next steps, the whole picture, all in plain language.**

Plain-language agent skills, built and measured on Claude Code. They also
install on Codex, Cursor and Gemini CLI.

By [Pavlo Kharmanskyi](https://github.com/kharmanskyi).

I'm not an engineer. I'm a market-led builder: I look for where demand already
exists and the infrastructure doesn't, then build the missing piece. Twenty
years of building web and software products, always from the product side, and
more than 50 developers at my company today.

Apart from the company, I started building a product on my own, just
vibecoding. Partly to stay ahead of where all this is going, partly because I
think the moment has arrived for people like me: you can now imagine a serious
product and build it yourself, with no engineers or very few of them.

Then I ran straight into a wall. The agent does excellent work, then tells me
about it in commit hashes and jargon, and I genuinely cannot tell whether we
are done. Not because the work is unclear. Because nobody taught the agent to
talk to someone who doesn't speak engineering.

So I built this pack. I'm sharing it here for anyone in the same position, and
that's the whole idea behind the name: every step of building with an agent,
kept open to the person doing the building. Right now it changes what the agent
tells you. Where I want to take it next is the work itself, not just the
reporting.

## Before and after

The whole idea in one screen. Work ends, you ask "are we done?".

![The same session reported two ways: a wall of engineering detail, and a short plain-language report with a verdict](assets/before-after.svg)

<details>
<summary>The same thing as text, if the picture does not load</summary>

Without the pack:

> Hotfix deployed: session TTL misconfig in auth middleware caused 401
> cascades after key rotation; patched the refresh path, invalidated stale
> JWTs, redeployed api+web. p95 back to 180ms. Root cause: env drift after
> the 09-14 rollout. Two flaky e2e specs quarantined (known, tracked)…

With it you get this:

> People can sign in again. A bug was logging people out because their
> sessions expired far too early. The fix is live for everyone.
>
> | | |
> |---|---|
> | ✅ | Response times are back to normal |
> | ⚠️ | Until this shipped, people were being logged out over and over. |
>
> | Fully done? | Yes |
> |---|---|
> | **Anything needed from you?** | No |
> | **New debt?** | Two small ones, written down |
> | **Safe to close?** | Yes |

</details>

Same facts. One screen. The bad news gets its own row instead of hiding in
the middle of a paragraph. A second, longer example from a real session lives
in this repository, with notes on what the rewrite changed:
[`skills/os-done-or-not/references/01-prod-promote.md`](skills/os-done-or-not/references/01-prod-promote.md).

## Quick start

The pack installs into Claude Code, Codex, Cursor and Gemini CLI. On Claude
Code a plugin wires the skills and both hooks in one command. On Codex, Cursor
and Gemini CLI one copy command installs the skills, and the hooks take a few
lines of settings per tool, see
[Other agents](#other-agents-codex-cursor-gemini-cli) below. What was run on
each tool, and what comes from its documentation, is in the
[table at the end of this section](#what-was-run-on-each-tool).

### First, for any tool

`git` and `gh` are optional: a couple of the skills read project state through
them, and without those tools more of the output honestly says "not checked".

Clone this repository:

```bash
git clone https://github.com/kharmanskyi/open-steps.git
```

The commands below run from the folder you cloned it into, which now holds
`open-steps/`, not from inside the clone.

On Claude Code, Codex, Cursor and Gemini CLI alike, one piece is worth adding
by hand: the routing block, in the file that tool reads as standing
instructions. Skills are something the model chooses to use. The hooks remind
it; on Claude Code the block makes it a rule. Each tool's section below says
where it goes.
[Why the block matters](docs/claude-md.md).

### Claude Code

Install the pack as a plugin:

```bash
claude plugin marketplace add ./open-steps && claude plugin install open-steps@open-steps
```

That's it. The skills and both hooks are wired for you. Check what you got:

```bash
claude plugin details open-steps
```

Later, to check the whole install rather than just the plugin, run
`/open-steps:os-install-check` in Claude Code. It reports what is wired and
what is not, and says "not checked" where it could not look.

The routing block goes in your own `~/.claude/CLAUDE.md`, where it survives
long conversations. One command, from the same folder, safe to re-run:

```bash
grep -q 'os-done-or-not' ~/.claude/CLAUDE.md 2>/dev/null || cat open-steps/docs/routing-block.md >> ~/.claude/CLAUDE.md
```

To update: `git pull` inside `open-steps/`, then
`claude plugin update open-steps@open-steps`. Both halves matter: the plugin
updates from your clone, not from GitHub, so without the pull "already at the
latest version" is true of the folder and wrong about this repository. And
`update` wants the full plugin@marketplace name, where `uninstall` accepts the
short one. And the update moves files only when the version number changed: a
pull that brings no new version brings nothing to the installed copy, so a
change meant to reach it ships with a version bump and a release. To remove:
`claude plugin uninstall open-steps`, then take the
block back out of your `CLAUDE.md`.

On Claude Code there is also an optional writing style. It is left off by
default because turning it on would replace a style you already chose. Two
lines, in [`docs/output-style.md`](docs/output-style.md). It is a Claude Code
setting, and the pack does not wire it on Codex, Cursor or Gemini CLI today.

### Other agents: Codex, Cursor, Gemini CLI

Codex, Cursor and Gemini CLI read `~/.agents/skills/`, so one command
installs the skills for each of the three. Run it from the folder holding the
clone:

```bash
mkdir -p ~/.agents/skills && cp -R open-steps/skills/os-* ~/.agents/skills/
```

Then the routing block goes into the file each tool reads as standing
instructions. A one-line command for each, safe to re-run, is in
[`docs/other-agents.md`](docs/other-agents.md#the-routing-block).

| Tool | Routing block goes in |
|---|---|
| Codex | `~/.codex/AGENTS.md` |
| Cursor | `AGENTS.md` in the project root |
| Gemini CLI | `~/.gemini/GEMINI.md` |

The hooks are the part that differs per tool. For Codex, the same two scripts
are wired with a short block in `~/.codex/config.toml`; per Codex's hook
documentation it then asks once to trust them. The scripts were checked
against Codex-shaped input by hand; Codex running them in a live session has
not been watched yet. On Cursor CLI both run through `hooks/adapter.sh`, which
wraps them in the JSON Cursor CLI wants. A stop cannot be blocked there, so the
report is asked for as a follow-up message rather than required, and a
headless run (`agent -p`) did not reach the stop hook. On Gemini CLI both run
through the same adapter, and there the stop can refuse, on `AfterAgent`. One
thing to know: its file tool cannot write outside the workspace, so the
request says to save the report with the shell tool, after a headless run
wrote it to Gemini's own temp folder; that sentence went in after the run and
was not itself watched. A contributor ran both hooks live on
Cursor CLI 2026.09.02 and Gemini CLI 0.58.0, on Windows 11; the maintainer has
not reproduced them, and the Cursor desktop app has not been tried.

To check the install on these three, run `bash open-steps/doctor.sh` from the
folder holding the clone. It reads the shared skills folder and each tool's
own, the routing block, and the hook settings of each tool it finds, and says
"not checked" for what it cannot look at. It does not check which event each
hook sits under yet.

To update: `git pull` inside `open-steps/`, then run the copy command again.
It is a copy, so the installed skills stay as they were until you do. To
remove (these steps have not been run yet): delete the `os-*` folders from
`~/.agents/skills/`, take the block out of that tool's instructions file, and
remove the two hook entries from its settings file (`~/.codex/config.toml`,
`~/.cursor/hooks.json` or `~/.gemini/settings.json`).

The commands, the paths, the hook settings for each tool, and what was run
rather than read: [`docs/other-agents.md`](docs/other-agents.md).

### What was run on each tool

Activation is measured on Claude Code so far; runners for the other tools are
open issues:
[#39](https://github.com/kharmanskyi/open-steps/issues/39) Codex,
[#40](https://github.com/kharmanskyi/open-steps/issues/40) Cursor,
[#38](https://github.com/kharmanskyi/open-steps/issues/38) Gemini CLI.

| | Skills install | Routing block | Session-start hook | Stop hook | Switches on by itself | Premortem fresh agent |
|---|---|---|---|---|---|---|
| Claude Code | watched: plugin install from a clean empty account | in place in the measured runs | watched, wired by the plugin; on in the measured runs | watched, wired by the plugin; off in the measured runs | measured, 2026-09-12: Haiku 4.5 85%, Sonnet 5 98%, Opus 5 100% | measured, 2026-09-14: 9 of 9 runs on Sonnet 5, 8 of 9 on Opus 5 |
| Codex | watched: listed on Codex CLI 0.145, 2026-08-25 (OS not recorded) | from Codex's docs | checked by hand on test input | checked by hand on test input | not measured (#39) | watched: did not start in 4 runs, and all 4 wrongly called the review independent; Codex CLI 0.151, Linux |
| Cursor CLI | watched: 2026.09.02, Windows 11 | from Cursor's docs | watched: 2026.09.02, Windows 11 | watched: asks, cannot require; 2026.09.02, Windows 11 | not measured (#40) | not tried |
| Gemini CLI | watched: 0.58.0, Windows 11 | watched in place: 0.58.0, Windows 11; not seen steering a skill | watched: 0.58.0, Windows 11 | watched: refuses on `AfterAgent`; 0.58.0, Windows 11 | not measured (#38) | not tried |

"Watched" on Cursor CLI and Gemini CLI means one contributor's runs on
Windows 11; the maintainer has not reproduced them. "Checked by hand on test
input" means the Codex hook scripts were fed Codex-shaped input
(`hooks/test.sh` CASE 9); Codex running them in a live session has not been
watched. The Codex listing on 2026-08-25 covered the six skills the pack had
then; it (PR #1) and the Codex premortem runs (PR #29) were contributors' runs
too. The Codex
premortem check came before the skill's last two changes. Outside Claude Code
no skill has been seen switching on from a user phrase: `os-done-or-not` ran
on Cursor CLI and Gemini CLI when the stop hook asked for it, and
`os-what-could-go-wrong` ran four times on Codex CLI 0.151.

## The skills

| Skill | What it does | When it fires |
|---|---|---|
| [`os-done-or-not`](skills/os-done-or-not/) | A one-screen report with a verdict: done or not, anything needed from you, any new debt, safe to close | Work wraps up, or you ask how it went |
| [`os-step-by-step`](skills/os-step-by-step/) | Numbered steps a non-technical person can follow. The agent must first try everything itself and ask only for what truly needs you | The agent needs you to run, paste, click, approve or test something |
| [`os-ask-simple`](skills/os-ask-simple/) | The question in plain words, what it costs later, and one marked recommendation | The agent has a question or options for you |
| [`os-what-could-go-wrong`](skills/os-what-could-go-wrong/) | Assumes the decision already failed and works backwards to find out why, in a fresh agent that had no hand in it. Ends on one verdict | Something hard to undo is about to be agreed - a contract, a purchase, a migration, a launch |
| [`os-whats-next`](skills/os-whats-next/) | Merges what is verified and ready, then recommends the next task and says why in plain words | You ask what is left or what to do next |
| [`os-check-work`](skills/os-check-work/) | Does not trust another session's report. Checks every claim against what actually happened, then says what to do about it | Another session says it is done |
| [`os-say-simple`](skills/os-say-simple/) | Rewrites any text in plain words without losing facts or bad news. Give it a number and you get exactly that many points | Any text reads like engineering: a report, a comment, an error, the agent's own answer |
| [`os-big-picture`](skills/os-big-picture/) | Keeps one `BIG-PICTURE.md`: what the product is, every feature with how far it got, which parts nobody uses any more, and what is queued - and offers to open the queue as tickets in a tracker you already use | You ask where the project stands, or a session report was just written |

They work as a loop: `os-whats-next` picks the work, `os-step-by-step` walks
you through your part, `os-done-or-not` reports the result, `os-check-work`
accepts what other sessions did, `os-ask-simple` handles the questions on the
way, `os-what-could-go-wrong` attacks anything hard to undo before it is
agreed, and `os-say-simple` rescues any text that still reads like
engineering.

`os-big-picture` keeps one standing file about the product: what it is, what is
in it and how far each part got, what nobody has touched, and what is queued. It
is written in the owner's words and serves two readers. The person gets the
whole picture without reading code. The agent gets what the code cannot tell
it: what the product is for, who runs it, and what is live rather than merely
present, which is exactly what goes missing after a cleared context or a new
session. The architecture itself stays out, because the agent derives that from
the code, and Claude Code's own guidance keeps `CLAUDE.md` short for the same
reason ([memory docs](https://code.claude.com/docs/en/memory)). The file appears
the first time you ask for it and refreshes after each report, so whatever
either reader sees carries its date. It also closes the loop at the point it
used to break: `os-whats-next` is told to read the backlog **always**, and
until now nothing in the pack wrote one. Two things it will not do: invent a
task, or delete anything.

It measures how old each part is and whether anything still reaches it, in
`scripts/census.sh`, which the hook suite runs against a repository with
forged commit dates: a part touched last week, a quiet one nothing mentions,
and a quiet one the build script does. Quiet code that is still used is
reported as finished, not as rot. On a repository younger than six months the
map says outright that its liveness column cannot mean anything yet, and from
when it will - a young project has no quiet code by definition, and a clean
bill of health it did not earn is the kind of lie this file exists to avoid.

Where the project already has a task tracker connected, it offers to open the
queued items as tickets - it shows the list first, checks each one against
tickets that exist, and creates nothing until you say yes.

Its output has to stay fresh, and a stale map is worse than no map - the pack says so itself about trackers. So the map is
built to age out loud. It states the day it was measured on its first line;
the two columns that come from git are re-measured on every pass and never
read back out of the file; and the one column nothing can measure, how far a
feature got, carries the date of the report it came from. Reports stop, and
the dates stop with them, in the rows themselves rather than in a footnote.
`os-whats-next` does not read the git numbers out of the file at all - it
measures them again for itself - and says the age of the stages out loud when
they fall months behind the newest commit. The refresh happens on every
fold-in, including the sessions that change no row, because those are exactly
the sessions after which the file would quietly be a day older than it claims.

The map is a working note, not part of the product, so it is kept out of the
repository: on the pass that creates it, the file goes into that clone's own
`.git/info/exclude` - never into the shared `.gitignore` - so no `git add -A`
can sweep it into a commit. Want it shared with your team instead?
`git add -f BIG-PICTURE.md` once, and the skill leaves a tracked map alone
from then on.

## Numbers

These numbers are for Claude Code with Claude models. Runners for the other
tools are open issues: [#39](https://github.com/kharmanskyi/open-steps/issues/39) Codex, [#40](https://github.com/kharmanskyi/open-steps/issues/40) Cursor,
[#38](https://github.com/kharmanskyi/open-steps/issues/38) Gemini CLI.

The pack tells the agent to separate what it measured from what it assumed.
Same rule for me.

Twenty-five phrases a person would actually say, three per skill plus one
boundary case, each asked three times, headless, in a working installation, on
three Claude models. The question every time: did the right skill switch on by
itself? Three off-topic questions, each also asked three times, checked the
opposite. Remeasured in full on 2026-09-12, the first sweep with
`os-big-picture` in it.

`os-big-picture` and `os-whats-next` both answer a question about the project
as a whole, so the new skill was the one most likely to take a phrase from the
other. This pass did not see it: nine of nine for each, on every model. Three
runs per phrase is a smoke test, so a later pass still might.

![Activation per skill on Haiku 4.5, Sonnet 5 and Opus 5](assets/activation.svg)

<!-- numbers: score.py writes this table, edit the prose but not these rows -->

Measured on 2026-09-12.

| Skill | Haiku 4.5 | Sonnet 5 | Opus 5 |
|---|---|---|---|
| `os-done-or-not` | 11/12 | 12/12 | 12/12 |
| `os-whats-next` | 9/9 | 9/9 | 9/9 |
| `os-check-work` | 9/9 | 9/9 | 9/9 |
| `os-what-could-go-wrong` | 9/9 | 9/9 | 9/9 |
| `os-big-picture` | 9/9 | 9/9 | 9/9 |
| `os-ask-simple` | 7/9 | 9/9 | 9/9 |
| `os-say-simple` | 6/9 | 9/9 | 9/9 |
| `os-step-by-step` | 4/9 | 8/9 | 9/9 |
| **All 25 phrases** | **85%** | **98%** | **100%** |
| Fired on an off-topic question | 0/9 | 0/9 | 0/9 |

<!-- numbers: end -->

The honest reading, because the misses matter more than the score.

- On Sonnet 5 and Opus 5 this works. Four skills are perfect on every model,
  and Opus missed nothing at all.
- `os-what-could-go-wrong` was named the skill most likely to steal a phrase
  from `os-ask-simple`, so that was measured before it merged: 27/27 on its
  own phrases, `os-ask-simple` did not drop, and off-topic questions still
  leave it silent. The fear did not survive the measurement.
- Sonnet 5 missed one run in seventy-five, on a step-by-step phrase. In the
  previous pass it had dropped two runs of the vaguest phrase ("That's it for
  today. What happened?") to no skill at all; this time that phrase fired
  three of three. Read both as the run-to-run wobble Haiku shows below.
- On Haiku 4.5, two skills are unreliable and a third dropped two runs. No
  off-topic question pulled in a skill this time; one did in the previous
  pass. If you run on the cheapest model, expect to type the skill name
  yourself sometimes.
- Haiku also moves between runs. Four sweeps of the same phrases have put
  `os-step-by-step` at 50%, 33%, 44% and 44%, and false fires at zero, one and
  zero. Three runs per phrase is a smoke test, not a benchmark, and small
  numbers wobble. I would rather say that than quote the friendliest sweep.
- Where Haiku misses, it usually asks a clarifying question first: told "put
  a secret on the server, tell me what to do", it wants to know which server
  and which secret. That is the pack's own earn-the-ask rule; a one-shot test
  scores it as a miss.
- The test set is mine, and it is small. Twenty-five phrases in a repository
  you can read, every one of them scored above, so write better ones and
  re-run it.

Two things earlier rounds cost me, kept here because they are the useful part.
A negation inside a description ("this is NOT the skill for X") is ignored, so
boundaries between overlapping skills get drawn by removing triggers, not by
adding warnings. And a phrase with a false premise ("you said X" at the start
of an empty session) is refused by the model, correctly, so test phrases have
to carry their own context.

Everything is in [`evals/`](evals/), and two files are enough if you just want
to look: [`cases.md`](evals/cases.md) is every phrase we ask,
[`results.md`](evals/results.md) is what came back, phrase by phrase, so every
miss above has a row you can read. The scorer writes that file; I don't type
it. Scoring is a plain script reading tool calls, with no AI judging anything.
Re-run it with `bash evals/run.sh`, or `EVAL_MODEL=opus bash evals/run.sh` for
another model. `EVAL_AGENT` picks the tool: Claude Code's runner ships in
`evals/agents/`, and a runner for another tool follows the contract in
[`evals/README.md`](evals/README.md#measuring-another-agent), "Measuring
another agent".

Also measured, and easy to check yourself: the skill descriptions cost **770
tokens per session**, always on, which Claude Code reports itself with
`claude plugin details open-steps`. That figure was taken before
`os-what-could-go-wrong` was added and has not been retaken; run the command
for the current one. The session-start hook adds its injection
on top, capped by `OPEN_STEPS_MAX_REPORT_LINES`. On Claude Code, installing
works from a clean empty account, with both hooks connected. `claude plugin validate
--strict` passes.

## How the pack is built

Each skill is one folder with one `SKILL.md` inside: a short header, then the
rules. Some also carry a worked example in a `references/` folder. What runs
on your machine is plain shell: the two hooks, short enough to read in a
minute (on Cursor CLI and Gemini CLI through `hooks/adapter.sh`); two small
scripts that `os-big-picture` and `os-what-could-go-wrong` call (the second
through Claude Code's `!` command; in a Codex CLI 0.151 check, made before
that line was changed to call the script, the older line arrived as text and
Codex read the `references/` file instead); and the install
check, `doctor.sh`, when you run it. The skills also have the agent run `git`
and `gh` commands, including merges, as the next paragraph says.

One thing to know before installing: **the pack finishes finished work by
itself.** If a pull request has green checks and an approved review, it gets
verified once more and merged. On Claude Code that happens without a
permission prompt; on Codex, Cursor and Gemini CLI the merge command goes
through that tool's own permission settings, per their documentation. Whatever
unblocks the most goes first. Two things stop a merge: a claim that fails
verification, or a note on the task saying merges happen on command only.
Write that note wherever an orchestrator owns the merge; put the same note in
your standing instructions file (`~/.claude/CLAUDE.md`, `~/.codex/AGENTS.md`,
`~/.gemini/GEMINI.md`, or the project's `AGENTS.md` on Cursor) if you want
merges on command only.

And what a skill may do without asking. On Claude Code a skill can
pre-approve tools for the turn it runs in (the `allowed-tools` field), and
this pack keeps that list to what the skills use: reading its own reports
folder, and writing there for `os-done-or-not`; the `gh pr` calls that read a
pull request (`list`, `view`, `checks`, `diff`); `gh pr merge`, because merging
finished work is the behaviour above; for `os-big-picture`, editing its own
`BIG-PICTURE.md`, its census script, one `git rev-parse --git-dir` call that
finds the repository folder, `gh repo view`, and `gh issue list` and
`gh issue create`, which open tickets after you say yes; the premortem's
prompt script for `os-what-could-go-wrong`; and `doctor.sh` for the install
check command. Other commands, and files outside your project and the reports folder,
go through your own permission settings as usual. Other read-only `git` needs
no entry: Claude Code already treats read-only `git` as read-only. Per their
documentation, Codex, Cursor and Gemini CLI ignore the field and ask the way
they normally do; not tested.

Three decisions shape everything here:

1. **Descriptions are commands, not summaries.** The skill descriptions
   open with "ALWAYS invoke this skill…". With this form the skills switched on
   in 98-100% of the test runs on Sonnet 5 and Opus 5 and 85% on Haiku 4.5 (25
   phrases, Claude Code, 2026-09-12); the evals do not compare it with other wordings. See
   [Numbers](#numbers).
2. **A skill cannot force itself to run.** Anything that must hold in each
   reply lives in the tool's standing instructions file (`CLAUDE.md`,
   `AGENTS.md`, `GEMINI.md`) or, on Claude Code, the output style instead.
   The pack says which layer each piece belongs to.
3. **Measured and assumed stay apart.** A "yes" has to name its proof.
   Anything unchecked says "not checked". This is also why the reports are
   short: the agent stops narrating its checks and states the result.

The plain-language rules borrow from ASD-STE100, the simplified English
written for aerospace manuals: short sentences, active voice, one idea per
sentence. Borrow is the word. Nothing here is certified against the standard.

## Optional pieces and limits

On Claude Code, the [`answer-first`](docs/output-style.md) output style makes
the agent put the answer in the first line and stop narrating its
verification. It is a Claude Code setting; the pack does not wire it on Codex,
Cursor or Gemini CLI.

The routing block goes in the file each tool reads as standing instructions:
`~/.claude/CLAUDE.md` on Claude Code, `~/.codex/AGENTS.md` on Codex, the
project's `AGENTS.md` on Cursor, `~/.gemini/GEMINI.md` on Gemini CLI. Claude
Code also reads a project's `AGENTS.md` when that project has no `CLAUDE.md`
(v2.1.277 and later, per its release notes), so a block added there for Cursor
reaches Claude Code in that project as well.

Two hooks ship with the pack. The Claude Code plugin connects them for you; on
Codex, Cursor and Gemini CLI you wire them by hand, as
[`docs/other-agents.md`](docs/other-agents.md) shows, and on Cursor CLI the
stop hook can ask for a report but cannot require one.
[`session-start.sh`](hooks/session-start.sh) puts the routing table and the
last report in front of a new session, and quietly records what your
repositories looked like at that moment.
[`stop-report.sh`](hooks/stop-report.sh) compares against that when the session
ends and asks for a report if real work landed, which is also how work you
finished inside a single reply still gets one. Neither hook can loop: reports
are written outside your repositories, so writing one changes nothing they
look at. The stop hook is free when it stays quiet; the start hook does add its
injection to your context, capped by the setting below. Their settings:

| Setting | Default | What it does |
|---|---|---|
| `OPEN_STEPS_COOLDOWN` | 900 | seconds of quiet between report requests |
| `OPEN_STEPS_MIN_FILES` | 1 | changed files before a report is asked for |
| `OPEN_STEPS_MAX_REPOS` | 25 | started in a folder of repositories, how many get checked |
| `OPEN_STEPS_DISABLE` | unset | set to anything to switch the stop hook off |
| `OPEN_STEPS_MAX_REPORT_LINES` | 80 | cap on the injected last report |
| `OPEN_STEPS_NO_SESSION_START` | unset | set to anything to switch the start hook off |

Reports are saved outside your repositories, in
`~/.claude/open-steps/reports/<project>/`, so they stay out of your commits
and survive uninstalling the pack. The folder name says Claude, but it is only
a path, and each tool is pointed at it so they share one history. On Gemini
CLI the report has to be saved with the shell tool; a headless run was seen
writing it elsewhere.

And the honest limits. Not every skill has a worked example yet.
`os-whats-next` and `os-check-work` read project state through `git` and `gh`;
without those tools, more of the output says "not checked". The writing style
does not reach subagents, so `os-what-could-go-wrong` carries its rules inside
the handover to its fresh agent, and its plain language rests on
`references/premortem-prompt.md`. In Claude Code the premortem hands its
review to a fresh agent: 9 of 9 runs on Sonnet 5 and 8 of 9 on Opus 5
(2026-09-14). On Codex CLI 0.151 no fresh agent started in four runs and the
agent ran the review itself; that check came before the skill's last two
changes.

## Open source

Free, MIT licensed. Take it, use it at work, change it, fork it.

I keep building this pack for my own work, so it moves on its own. Pull
requests are welcome and I read them; the rules are in
[CONTRIBUTING.md](CONTRIBUTING.md).

If it helped, a star makes it easier for other people to find.

## License

MIT - see [LICENSE](LICENSE). © 2026 Pavlo Kharmanskyi.

Open Steps is an independent open-source project, not affiliated with or
endorsed by the makers of the tools it runs on. Claude and Claude Code are
trademarks of Anthropic. All other trademarks, including Codex, Cursor and
Gemini, are the property of their respective owners.
