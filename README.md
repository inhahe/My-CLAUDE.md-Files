# My claude.md files

These are the instruction files I give [Claude Code](https://claude.com/claude-code). I'm offering them for the good ideas in them. Take any rule you like, reword it for your setup, or copy a whole file.

They are not my files exactly as I use them. I modified them to remove or rewrite the parts that were specific to my machine or my projects: paths and drive letters, project names, the locations of my tools, and rules that applied to only one of my projects. Where a rule needed a path of yours, it has a `<placeholder>` instead, and the rules that applied to one project are rewritten as general ones.

Some of the rules come from the [`AGENTS.md` of `ludios/clank-right`](https://github.com/ludios/clank-right/blob/master/AGENTS.md): the sections *Push back*, *Reading and research* and *Code review*, and parts of *Version Control* and *Code Quality*. I adapted them from there.

## My favorite parts

If you take only a few things from these files, take these. They have been the most beneficial and made the most difference for me:

- **The code reviews** (*Code review* in `claude-global.md`). Every batch of commits is reviewed by a fresh Claude session, under Opus, before it is pushed or reported as done. A batch where a missed bug is expensive (concurrency, security boundaries, parsers of untrusted input, anything that deletes the user's data, and the like) gets a second review under Fable. A review typically reports 9 to 15 findings (median 12; Opus more, Fable fewer), and most of them, about 85%, turn out to be real issues that you otherwise might not find out about until much later.
- **The feature suggestions** (the last rule of *Push back* in `claude-global.md`). The agent tells me about features a program ought to have whenever it notices one, even outside the task at hand.
- **The simplification suggestions** (the rule before it in *Push back*). The agent tells me about code that could be shorter, a mechanism that could go, or two things that could be one, even outside the task at hand.

## The files

| File | Where it goes | What it holds |
|---|---|---|
| [`claude-global.md`](claude-global.md) | `~/claude-global.md` | Most of the rules: calibrating effort estimates, optimizing for the end result, pushing back, keeping a `design.md`, research, subagents, the text that Windows tools mangle, cleaning up build output, version control, code review, code quality, and bug tracking. |
| [`user-CLAUDE.md`](user-CLAUDE.md) | `~/.claude/CLAUDE.md` | The user-level file Claude Code loads in every session. It imports `claude-global.md` with `@~/claude-global.md`, registers the [graphify](https://github.com/Graphify-Labs/graphify) skill, and ends with lessons that agents learned the hard way. |
| [`workspace-CLAUDE.md`](workspace-CLAUDE.md) | `CLAUDE.md` in the folder that holds your projects | A template that tells an agent where each project's design document, user-facing document and own instructions are. Fill in the `<placeholders>`. |

## Why there are two files instead of one

I use more than one Claude account, and each has its own config folder with its own `CLAUDE.md`. The rules all accounts share live in one file, `claude-global.md`, which each account's `CLAUDE.md` imports. A rule I write there once reaches every account. Each account's own file keeps only what belongs to that account.

## Before you copy them

- **They are written for Windows,** where Claude Code's Bash tool is Git Bash (MSYS). The section *Text that tools mangle* and most of the curated lessons are about that. On macOS or Linux you can skip them.
- **Some rules refer to two tools of mine.** They are in repositories of their own:
  - **The curator: [agent-knowledge](https://github.com/inhahe/agent-knowledge).** Every night it mines your Claude Code transcripts for lessons with a local LLM: corrections you made, tool traps, mistakes agents made and fixed. Every morning a headless Claude session turns the lessons that keep recurring into rules, within a size budget. It writes them into the *Lessons agents learned* section at the end of each `CLAUDE.md`, and touches nothing else in the file. It runs on Windows and needs an NVIDIA GPU with about 20 GB of free VRAM.
  - **The `RaiseHalt` and `LiftHalt` tools: [agent-comms](https://github.com/inhahe/agent-comms).** It lets the Claude Code sessions on one machine halt each other, across accounts (and, under orchestrator2, address and message each other too). A halt asks every session in a repository, or on the machine, to stop taking new work. It also makes long jobs that run `python -m agent_comms check-halt` refuse to start. It stays up until somebody lifts it. `python -m agent_comms install` gives an account the two tools, and a hook that tells each session about a halt when its next prompt arrives. My [orchestrator2](https://github.com/inhahe/orchestrator2), a web front end for Claude Code, uses it for every session it runs, and tells them at once, idle or not.
- **The `# graphify` section at the top of `user-CLAUDE.md`** is the one [graphify](https://github.com/Graphify-Labs/graphify)'s installer writes. Delete it if you don't use graphify. But graphify seems like a great tool, and its README undersells what it can do.
- **The effort calibration was measured** on the history of one of my projects, with the models of October 2026. Model names (Opus, Fable), review levels (`xhigh`, `max`) and Claude Code versions are also as of October 2026.
- **Paths in angle brackets** (`<tools>`, `<path>`, `<Project A>`) stand for whatever they are on your machine.
