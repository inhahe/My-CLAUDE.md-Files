# My claude.md files

These are the instruction files I give [Claude Code](https://claude.com/claude-code). I'm offering them for the good ideas in them. Take any rule you like, reword it for your setup, or copy a whole file.

They are not my files exactly as I use them. I modified them to remove or rewrite the parts that were specific to my machine or my projects: paths and drive letters, project names, the locations of my tools, and rules that applied to only one of my projects. Where a rule needed a path of yours, it has a `<placeholder>` instead, and the rules that applied to one project are rewritten as general ones.

Some of the rules come from the [`AGENTS.md` of `ludios/clank-right`](https://github.com/ludios/clank-right/blob/master/AGENTS.md): the sections *Push back*, *Reading and research* and *Code review*, and parts of *Version Control* and *Code Quality*. I adapted them from there.

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
  - **The `RaiseHalt` and `LiftHalt` tools: [orchestrator2](https://github.com/inhahe/orchestrator2)**, my web front end for Claude Code. They are an MCP server built into it ([`agent_tools.py`](https://github.com/inhahe/orchestrator2/blob/main/agent_tools.py), on top of [`agent_comms.py`](https://github.com/inhahe/orchestrator2/blob/main/agent_comms.py)), not a standalone one. [Talking to the other sessions on this machine](https://github.com/inhahe/orchestrator2#talking-to-the-other-sessions-on-this-machine) explains them. Only orchestrator2's sessions are told when a halt is raised, so without orchestrator2, drop the rule *Stopping other agents*.
- **The `# graphify` section at the top of `user-CLAUDE.md`** is the one [graphify](https://github.com/Graphify-Labs/graphify)'s installer writes. Delete it if you don't use graphify.
- **The effort calibration was measured** on the history of one of my projects, with the models of October 2026. Model names (Opus, Fable), review levels (`xhigh`, `max`) and Claude Code versions are also as of October 2026.
- **Paths in angle brackets** (`<tools>`, `<path>`, `<Project A>`) stand for whatever they are on your machine.
