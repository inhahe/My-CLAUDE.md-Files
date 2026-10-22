# Rules for every project

The rules for every session, on every account, in every project. Each account's `CLAUDE.md` imports this one file (`@~/claude-global.md`), so a rule written here once reaches all of them. Claude Desktop's Cowork sessions are the exception: they do not follow such imports.

Push back, Reading and research, Code review, and parts of Version Control and Code Quality were adapted from the `AGENTS.md` of [`ludios/clank-right`](https://github.com/ludios/clank-right/blob/master/AGENTS.md).

## Effort & Duration Calibration (measured, not guessed)

- **Your trained instinct estimates task durations in human-developer calendar units, and is consistently wrong by ~1–2 orders of magnitude — because the training data is humans, who code far slower than you.** Do NOT answer "how hard/long is X?" or pace your own work (or decide whether a feature is "worth building now") using that gut instinct. This calibration is *measured* from one project's own history (4640 commits over 46 days, every commit and session-JSONL entry carrying millisecond timestamps, so it's observed, not assumed).
- **Measured throughput:** roughly **one committed, tested, logical change every ~5 minutes of active work** (median commit spacing 4.7 min; ~12.6 commits/active-hour). Concrete anchors: a kernel bug fix *with* root-cause instrumentation + a regression test + three full QEMU boot cycles ≈ **38 min**; a new syscall feature end-to-end ≈ **21 min**; a capability-gated syscall pair ≈ **17 min**; a focused self-test ≈ **7–10 min**.
- **Divide your gut estimate hard. Rough conversion:**

  | Your instinct says… | Reality (active work) | You're off by |
  |---|---|---|
  | "a couple hours" | ~5–20 min | ~10× |
  | "a day or two" | ~20–40 min | ~20–40× |
  | "a week or two" (a whole subsystem) | a few focused active hours, spread over 1–3 calendar days | ~20–40× |
  | "months" / "~a year" (huge external port) | days-to-weeks of active work *if unblocked* | ≥10×; and the real gate is missing **prerequisites/dependencies**, not your effort |

- **Consequences:** When asked how hard/long something is, answer in the *measured* units (minutes-to-hours of active work; at most a handful of calendar days for genuinely large things) — never human-team weeks/months. When self-pacing, treat "big" tasks as cheap enough to **just start** rather than ration or defer — a "large initiative" here is usually a few active hours. What actually stretches wall-clock delivery is **downtime, not effort** (rate-limit windows) — so for "when will it be done?", model the coding as minutes-to-hours and add slack only for possible rate-limit downtime; never inflate the coding itself.

## What I Optimize For: The End Result, Not Time or Risk

- **I am generally NOT concerned with how long you think something will take to implement, test, or debug — nor with whether you consider it "risky." I care about one thing: the best possible end result.** Do not propose, choose, or water down an approach on the grounds that it's slow, laborious, tedious, or a big lift. Effort is not a cost I'm trying to minimize.
- **Never offer a worse solution because the better one is "too much work."** If the right fix is a large refactor, a full rewrite, a deep multi-hour debugging session, an exhaustive test sweep, or reworking something you already built — just do it. Don't ask permission to do the thorough version, and don't present the cheap version as the default with the good one as an optional upgrade.
- **Don't hedge with risk warnings as a reason to avoid the correct approach.** Flagging a genuine tradeoff (data loss, an irreversible action, a user-visible policy change) is useful and I still want to hear it — but "this is risky/ambitious/might take a while" is never on its own a reason to pick the lesser option. Surface the concern if it's real, then proceed with the best approach.
  - **The reason risk doesn't worry me: almost everything here is reversible.** It's all in version control — if an ambitious approach genuinely doesn't work out, we revert it and try a different strategy, having learned something. A failed attempt at the right design costs a revert; shipping a timid design costs us permanently. So bias hard toward attempting the ambitious approach. The only risks that actually warrant caution are the genuinely *irreversible* ones (destroying data, force-pushing shared history, anything with real-world side effects) — those, and only those, are worth stopping to ask about.
- **Don't pad your responses with time estimates or effort disclaimers.** Skip "this will take a while," "this is a big undertaking," "for now I'll just…" I don't need the schedule; I need the work done properly.
- **Corollary:** thoroughness beats speed every time. Prefer the complete fix over the quick one, full test coverage over spot checks, and real root-cause analysis over a plausible guess — even when the thorough path is far longer.

## Push back

- **If something I asked for, or the way I'm thinking about a problem, looks wrong, say so — even to the point of stopping before doing it.** This is real work, not an eval: a request carried out faithfully on a mistaken premise is worse than one questioned. Explain what you think is off and what you would do instead.
- **Don't assume everything that already exists is wanted.** Some of it is leftovers. Treat the existing implementation as revisable: if redoing a part makes the design better or the code shorter, propose it, or do it when it is clearly within the task.
- **Always tell me about opportunities for simplification you notice** — code that could be shorter, a mechanism that could go, two things that could be one — even when they are outside the task. I would rather hear about one too many than miss one.
- **Don't be shy about suggesting features a program ought to have or would benefit from.** Working in a program, you see what it lacks: something users of a program like it would expect, an option that would end a recurring chore, a diagnostic or error message that would have made the bug you just chased obvious, a workflow it makes harder than it needs to be. Say so, even when it is outside the task and nobody asked: name the feature and what it would give me. Build it when I ask, or when it is clearly part of the task; otherwise the suggestion is the deliverable. As with simplifications, I would rather turn down an idea than never hear it.

## design.md (every project)

- **Keep a `design.md` in every project.** It records the project's features and architecture. Whenever features or architecture change, update `design.md` in the same change so it stays current. If a project has no `design.md` yet, create one the first time you touch its features or architecture.
- **Check `design.md` before making non-local changes.** Whenever you add or change a feature whose effects might not be confined to the part of the code you're editing (anything touching shared state, interfaces, data formats, cross-module behavior, etc.), read `design.md` first to find the other parts of the system that might need to change too, then evaluate each of them and update whatever is actually affected.

### Exception — a project whose own documents already do this job

A project may already carry the same information in a set of specialised documents (a spec that is the authority where documents disagree, a feature inventory, subsystem deep-dives, a log of design decisions, a live roadmap). There, one more whole-system file, *rewritten in place* on every feature, would conflict constantly between agents working the tree in parallel. Such a project's own `CLAUDE.md` says so and names the documents that fill the role.

**The "check before non-local changes" half of the rule still applies in full** — read those documents before a change whose effects leave the file you're editing. What is waived is only the "keep a `design.md` and update it in the same change" half. Keep that project's `README.md` as the short **as-built** architecture overview: what exists and how the pieces fit, updated when a *subsystem-level* fact changes, not once per feature.

## Reading and research

- **Read the part of a file you need.** Search first (the Grep tool) and read the rest only once you know you need it; a whole large file read to answer one question is usage spent for nothing.
- **When you need outside information, keep searching until you have it.** One query that returns nothing useful is not an answer: rephrase, quote exact phrases, combine terms, and search where the knowledge lives — `site:github.com` for code and issues, `site:news.ycombinator.com` and `site:reddit.com` for experience reports. A page that will not fetch can usually be had another way (the raw file of a GitHub page through `curl`, say).

## Subagents / Parallelism

- **Never launch subagents (the Agent/Task tool) in parallel — and prefer not to use them at all for multi-part work.** Running multiple agents concurrently burns through Anthropic usage limits dramatically faster (each agent is its own token-hungry context), and in practice hits the limit *sooner* than just doing the same work serially in the main thread. Parallel fan-out feels faster but is a false economy here. If a task has several independent parts, do them one at a time in the main conversation rather than spawning agents. Only use a subagent when it genuinely saves the main thread's context (e.g. a big read-only exploration whose raw output you don't need to keep), and even then launch at most one at a time.
- **A `claude -p` session you start — the code review below, say — draws on the same usage, and counts as an agent for this rule:** one at a time.

## Tooling

- **If a tool you don't have would let you do the job better, get it — don't silently settle for a worse alternative.** When the best approach needs a program, library, CLI, package, or MCP/tool integration that isn't currently available, do NOT quietly fall back to an inferior workaround. Instead either (a) install it yourself when that's safe and self-contained (e.g. a `pip`/`npm`/`winget` package, a small CLI), then use it, or (b) if installing it needs my decision — it's heavyweight, system-wide, requires admin/credentials, has licensing implications, or you're unsure — pause and ask me first, explaining what the tool is and why it's the better path. The point: reach for the *right* tool and tell me about it, rather than hiding a compromise. Always mention when you've installed something new.

## Local Dev Tools

- **`cdb` (Windows Console Debugger) IS installed and on PATH — don't conclude it's missing.** The real, self-contained debugger (cdb.exe + all its DLLs/extensions) lives in `<tools>\dbg\`. It is reachable two ways: a bash/MSYS shim at `~/bin/cdb` (found by `command -v cdb` / `which cdb` in the bash tool) and a Windows shim `<tools>\cdb.cmd` (found by `where cdb` in cmd/PowerShell). Both exec `<tools>\dbg\cdb.exe`. Note: bash does NOT resolve the `.cmd` shim (MSYS ignores `.cmd` on PATH) and `where cdb.exe` won't match either shim — so probe with `command -v cdb` or just run `cdb -version`. Do not scatter cdb's DLLs into `<tools>` (other tools there, e.g. Procmon/Process Explorer, could pick up its `dbghelp.dll`/`symsrv.dll`).

## Killing processes

- **Never do a blanket kill of Python processes.** Do NOT run `taskkill /IM python.exe`, `killall python`, `pkill -f python`, `Stop-Process -Name python`, or anything else that kills Python by image/process name. I always have useful Python processes running — including the backends that serve my Claude sessions — so a blanket kill takes down my own tooling (possibly the very session you're running in) and loses live work.
- **Kill only the exact process you started, by PID.** If you need to stop something you launched, capture its PID and kill that one PID. If you didn't start it, don't kill it — ask me first.
- **This applies to any shared runtime, not just Python.** Same rule for blanket kills of `node`, `dotnet`, `java`, etc.: target a specific PID, never a process name.

## Text that tools mangle (Windows: the Bash tool is Git Bash / MSYS)

Found by searching every past session for "mangled". Each of these has recurred, several of them a dozen times or more, and most produced a result that looked real (a "0 warnings", a "0.0 GB", an "anchor not found") rather than an error.

- **Anything containing a backslash goes into a file through the Write or Edit tool, never through the shell: no `sed`, no `echo` or `printf`, no `python -c`, and no Python heredoc doing `str.replace`.** Through the Bash tool, `\\` often arrives as `\`, even inside quoted heredocs (`<<'EOF'`, `python - <<'PYEOF'`). What survives is then read as an escape: `C:\vendor` becomes `C:` + a vertical tab + `endor`, `\fonts` a form feed, `\a` a BEL byte, `\1` a 0x01 byte, and `\n` a real line break inside a C++/Rust/C# string literal. A change too large for the Edit tool is a Python script written with the Write tool and run as a file. Write Windows paths with forward slashes (`C:/My Projects/...`); in generated code, build a backslash as `chr(92)`; after any scripted write, check the file for stray control bytes.
- **`$` inside `powershell -Command "..."` sent from bash is expanded by bash first:** `$_` has become `extglob.`, and `$PID` and `$env:X` are replaced before PowerShell sees them. Use the PowerShell tool, or a `.ps1` written with the Write tool and run with `-File`. `wsl -- bash -c '...'` mangles `$` and `>` the same way: use `wsl -- bash -s <<'EOF'` or a script file.
- **Git Bash rewrites anything that looks like a POSIX path when it calls a Windows program.** `cmd /c` becomes `C:/`, so cmd runs nothing and a "build" silently does nothing; paths inside `cmd //c "..."` get mangled too, as do flags like `taskkill /PID` and `tasklist /FI`, `git show rev:path` (it becomes `rev\path` with `;`), regex arguments, and `/usr/bin/...` handed to WSL. Prefix the command with `MSYS2_ARG_CONV_EXCL='*'`, or run native commands through the PowerShell tool or a `.bat` file.
- **`/tmp` is two different places.** In bash it is `C:/Users/<you>/AppData/Local/Temp`; Windows Python and PowerShell read `/tmp` as `\tmp` on the current drive. Hand files between them by full Windows path.
- **Python on Windows defaults to cp1252.** Pass `encoding="utf-8"` to every `open()`, `read_text()`, `write_text()` and `subprocess.run(..., text=True)`, and set `PYTHONIOENCODING=utf-8` before printing non-ASCII (otherwise `UnicodeEncodeError`). Work on diffs and binary data as `bytes`, not `str`: round-tripping through `str` has mangled em dashes.
- **Python text-mode writes turn `\n` into `\r\n` on Windows,** silently converting a whole LF file to CRLF. Use `newline=""` or write bytes.
- **The Windows console garbles UTF-8** (code page 437/1252): keep `.bat` files, and anything they print, ASCII. The Grep tool can also *display* an em dash wrongly; check with the Read tool before calling a file corrupted.
- **Commit messages:** backticks and `$(...)` inside `git commit -m "..."` run as commands. Use `git commit -F - <<'EOF'`.
- **`sed` replacements:** `\` and `&` are special there, and a damaged replacement is usually still valid `sed`. Never put a Windows path, or anything else with a backslash, in a `sed` expression. Use the Edit tool; for a replace too large for it, a Python script written with the Write tool (not a heredoc: see the backslash rule) that replaces literally and checks how many places it changed.
- **Markdown in replies eats `_` and `*`:** `n_j`, `a_(j+1)` and `x*y` lose characters or turn italic. Put such text in backticks or a code block.
- **A surprising result after text went through a shell: suspect the shell first.** Syntax checks don't catch mangling (`bash -n` accepts a line continuation that became a literal `n`); run the code, or look at the bytes.

## Edits by search-and-replace

- **Before replacing, check that the anchor matched exactly as many places as you meant — usually one — and stop if it did not.** A scripted replace that matches a second place, or different text than intended, lands the wrong edit silently.
- **Do not write a comment explaining a trap in the same pass as the code it describes.** Write it, run something (a test, a gate, a build), then read it back as a reader rather than as its author.

## Build Artifacts — Clean Up After Yourself

- **Disk space is a real, finite constraint, and build output is the thing that eats it.** Rust `target/` directories in particular run to tens of gigabytes each. Once, one project's various worktrees had accumulated ~300 GB of them and filled the drive. Assume nobody else is watching this — if you created build output, you are responsible for removing it.
- **Never create a second build directory that outlives the command that needed it.** If you need an isolated build (a `--target-dir` separate from the main one so a clippy or fmt run doesn't invalidate the primary cache), **delete it as soon as that run is finished** — in the same task, not "later." A scratch target dir named for a one-off job (`target-lint`, `target-check`, `target-fmt-c`, `target-reintro-c`) is a 10–40 GB leak the moment the job ends. Prefer reusing the project's existing target dir over creating a new one; only split when a genuine cache conflict forces it, and then clean up immediately.
- **Delete the build output of any scratch worktree when you finish with it.** A temporary worktree (bisect, straddle test, experiment) gets its own `target/`. Removing the worktree without removing its `target/` leaves the biggest part behind.
- **When you finish a task, the only `target*` directory left behind should be the project's single canonical one.** Check with `ls -d */target*` (or the platform equivalent) at the end of a work session and remove anything that isn't the canonical cache.
- **Deleting build output is always safe** — it is regenerable by definition, it is gitignored, and it contains no source. Do not ask permission to delete a `target/`, `build/`, `obj/`, `bin/`, `node_modules/`, `__pycache__/`, or `.venv/` you created. **Do** check first that no build is currently running against it (e.g. no `cargo`/`rustc`/`cl.exe` processes alive), since deleting a cache mid-build produces confusing errors for whoever is building.
- **On Windows, delete large trees with `rd /s /q` from a real `cmd`, not `rm -rf` through MSYS** — `rm -rf` on a multi-gigabyte `target/` takes minutes to hours. Note that MSYS mangles Windows paths passed to `cmd //c`; putting the commands in a `.bat` file and running that avoids the quoting problem entirely.

The rules below come from a clean-up that found 38 GB of build output and IDE caches in my projects folder and beside it, most of it in projects nobody had touched for months. They are about output nobody will use again: duplicates, scratch, and the leftovers of dormant projects. **An active project's build cache is not such output and stays** — the `target/` of a project rebuilt many times a day, say, is what keeps those rebuilds incremental; emptying it only buys a full rebuild.

- **Copying a project copies its build output and IDE caches.** Seven numbered copies of one experiment (`x\2`, `x\3`, `x\4`, …) held 5 GB, almost all of it build output, and a `backup` folder, a copy of a project kept inside the project, carried 1.7 GB of `Obj` folders. To keep an old state, commit or tag it. If a folder copy is really needed, copy it without the output (`git archive`, or `robocopy` with `/XD .vs x64 Obj Debug Release build`), and never inside the project it copies.
- **Visual Studio's `.vs` folder grows to gigabytes of IntelliSense databases** (up to 1.8 GB per project). When a project goes quiet, delete the databases inside it (`*.db`, `*.vsidx`, `ipch\`, `FileContentIndex\`), not the whole folder: it also holds the user's breakpoints, open tabs and `launch.vs.json`.
- **Give a new project git and a `.gitignore` for its build folders before its first build.** Without them nothing records which folders are output, so nothing gets cleaned: a group of experiments, none of them under git, held about 15 GB of it.
- **Keep build directories inside the project.** Blender's `make.bat` builds beside the source tree, which put a 12 GB build in a folder of its own next to the checkout, where nothing tied it to the project. When a tool insists on building elsewhere, write down where, in the project's notes.
- **Anything large you leave outside a project gets a `README.txt` beside it** saying what it is, what made it, and when it can go. A 70 GB VM disk with no `.vmx` and no note took an investigation to identify.
- **Git LFS keeps every large file twice on NTFS:** checked out, and again in `.git\lfs\objects` (Blender's prebuilt libraries: 6.5 GB plus 6.1 GB). Initialize only the submodules the build needs, and run `git lfs prune` after updating.
- **Python environments and downloaded models count too** (one project's `.venv` was 6.4 GB). Use one venv per project, not per experiment, and keep a `requirements.txt` (or `pip freeze` output) so a dormant project's venv can be deleted and recreated.
- **Generated data a session produces is its own to clean up:** renders, exports, sweep results and the scratch files behind them go in one output folder per project, the scratch is deleted once the result exists, and the final report names anything over about 500 MB left behind. This is about a task's own products, not the project's build cache (above), which stays.

## Stopping other agents

- **When you need every agent on this machine to stop, raise a halt rather than filing a request or asking the operator to relay the message.** Use the `RaiseHalt` tool; lift it with `LiftHalt` as soon as the reason has passed.

*(Deliberately one line. Everything else about cross-agent comms lives in tool descriptions, which are present every turn, rather than here — an instruction file is read once at session start and thereafter competes with everything else in it. A filed bug once sat unread in the receiving agent's own working tree for two days and still cost it a 7783-second job.)*

## Version Control

- **Commit to git freely whenever it makes sense.** You don't need to ask before committing — create commits at natural checkpoints (a feature done, a known-good buildable/tested state, before a risky change). Scope commits sensibly and keep build/render artifacts and secrets out of history.
- **Several changes asked for are several commits,** unless they are entwined, so each can be reviewed and reverted on its own.
- **Stage what you changed, by path. Never `git commit -a` or `git add -A`/`git add .`** — someone else may be working in the same tree (me, another session), and a blanket stage commits their half-done work under your name.
- **Pushing is per-project. By default, do NOT `git push`** — for most projects, pushing to a remote is the user's action, so never run `git push` unless explicitly told to in that moment. When told, push straight from the project's working repository, not through a mirror copy.
  - **A project's own `CLAUDE.md` may grant standing permission to push.** One of mine does: its agents are autonomous and often run while I'm away, so unpushed work is work at risk. There, push after every completed task, or at minimum every few meaningful commits — your own branch; never force-push `main`.

## Code review

- **Every batch of commits gets an independent review before it is handed on** — before you push it, or report the work as done. Per batch, not per commit: every review pays a fixed cost — reading the guidelines and the code around the change — and reviewing commit by commit pays it again and again for what one review of the batch covers once. A project's own CLAUDE.md may say what its batch is.
- **A batch that changes no code — only documentation or data — is not reviewed:** the review would cost as much as any other and find little.
- **The command**, started in the background (`run_in_background: true`) from the tree that holds the commits:

  ```
  MSYS2_ARG_CONV_EXCL='*' "${CLAUDE_CODE_EXECPATH:-claude}" -p --no-session-persistence --model opus "/code-review xhigh commits <base>..<tip>"
  ```

  `--no-session-persistence` keeps the review from saving a session: it runs in the reviewed project's directory, so a saved one turns up among that project's sessions, as a session nobody started. The findings are in its output; redirect that to a file. Without `MSYS2_ARG_CONV_EXCL='*'`, Git Bash rewrites `/code-review` into a Windows path (`C:/Program Files/Git/code-review ...`), which reaches the session as plain text instead of the command. In PowerShell, which rewrites nothing: `& ($env:CLAUDE_CODE_EXECPATH ?? 'claude') -p ...`. `CLAUDE_CODE_EXECPATH` names the Claude Code build running your own session; the `claude` on PATH can lag behind it — on 2026-10-10 it was 2.1.232 while sessions ran 2.1.280 — and what each level does differs between builds. `/code-review` (alias `/review`) takes a level and a target. The target's text reaches the reviewer as written, so a commit or a range can be named; with none, it reviews the diff from the upstream branch plus anything uncommitted. The level also sets how hard the reviewer thinks, so `--effort` adds nothing.
- **The level.** In 2.1.280, for a Fable or Opus reviewer: **`xhigh`** runs a configuration Anthropic tuned by measurement — one agent works through ten review angles itself, keeps uncertain findings instead of verifying them, then sweeps once more for gaps; at most 15 findings. **`max`** hands the ten angles to ten finder subagents and every candidate to a verifier subagent of its own, then sweeps; at most 15 findings, for far more usage. `low`, `medium` and `high` are lighter passes. **`xhigh` is the default.** Use **`max`** where a missed bug is expensive — kernel code, `unsafe`, concurrency and locking, security boundaries and capabilities, parsers of untrusted input, anything that persists or deletes the user's data, crypto — or for a large, subtle batch. The level the code was *written* at does not set the level it is *reviewed* at: what a review adds is independence, a fresh context free of the author's assumptions.
- **The model.** The routine reviewer is **`opus`** (`--model`, by alias so it follows new versions). A batch where a miss is expensive — the list under the level — also gets **a second review by `fable`** at `xhigh`: one after the other, never both at once, since every `claude -p` session draws on the same usage limit as your own work. Measured on 2026-10-10, on one batch both reviewed at `xhigh`: Opus found 12 issues, 5 of them real ones Fable missed, for 43% of Fable's usage; Fable found 8, 2 of them real ones Opus missed, among them the most important of all. A fresh context is most of what a review adds, even from the author's own model; a second model is worth its price where a miss is expensive.
- **When Fable's share has run out, the batch keeps its Opus review alone.** Fable is included in the plan, but it uses the weekly allowance about twice as fast per token as Opus 5.1 (faster still than Opus 5.5), and it may take at most half of the allowance. Past that, a Fable review stops with "Fable limit reached", or asks to continue on usage credits, which a `claude -p` session cannot agree to: say in the follow-up commit's message that the second review could not run.
- **Reviewers read the working tree:** run the review in a tree nobody is editing, and leave it alone until the review is in.
- **Findings come from fallible machines eager to find issues.** Check each against the code before acting on it. Fix what is really worth fixing, in one or more follow-up commits, preferring fixes that don't add code we don't need, and say briefly why the rest didn't warrant a change.
- **Don't send the follow-up fixes through another review,** unless the follow-up grew into something substantial of its own.
- **The follow-up commit's message says** which models reviewed, at which levels, what was fixed, and what was dismissed and why.

## Code Quality

- **Always do the proper fix.** Never put off a correct fix in favor of a quick hack "for now." Quick fixes accumulate as tech debt and create bugs that are harder to diagnose later. If a fix requires a large refactor, do the refactor.
- **No dangling references.** Never store references or pointers to elements of a container (like `std::vector`) across call boundaries where the container could reallocate. Store stable identifiers (IDs, keys) and look them up when needed.
- **Think about invariants, and make them hold out loud:** assert them, or return a domain-specific error, wherever breaking one would otherwise go on and misbehave quietly.
- **A function's doc comment says what each argument and the return value really represent** — what the caller means by them, not their types, which the signature already shows — unless that is obvious.
- **A comment describes the code at its own level of abstraction:** why it is the way it is, and the constraints it keeps. It doesn't narrate other modules' internals or the code's own history: what it used to do and how a bug was found belong in the commit message, and the comment keeps only the constraint that still holds.
- **Evict code nothing uses, and generalize where it makes the result better.** Whoever maintains this reads it with a limited context; every dead function and every near-duplicate is something they must read and rule out.

## User-Facing Documentation

- **If a project has a user-facing document, keep it current in the same change.** Update it when adding a feature significant enough to warrant its own bullet point or section, or when changing or removing a feature already described there. Do *not* update it for minor fixes, internal refactors, or enhancements below the level of detail it already carries.
- **Which file it is differs per project**, and the project's own notes say so — one project's is `README.md`, another's `REFERENCE.md`. If a project has both a `README.md` and a deeper reference, the one to keep current is whichever the user reads, not whichever is easiest to edit.

## Bug Tracking

- **Document all bugs you notice.** Whenever you observe test failures, skipped tests, regressions, or any other bugs — whether you're fixing them or not — record them. Report them to the user and, if a project has a bug-tracking file or issue tracker, log them there. Don't silently ignore failures in test output just because they aren't the focus of the current task.
- **Keep a running list of unsolved bugs and technical debt in a markdown file.** Every project should have an `.md` file (e.g. `bugs.md`, `tech-debt.md`, or a combined `known-issues.md`) where unsolved bugs and accumulated technical debt are tracked. Each entry should have enough context to act on later: what the bug or debt is, where in the code it lives, how to reproduce it (for bugs), and what the proper fix looks like (for debt). If the project doesn't have such a file yet, create one when you first need to log something. **Ideally, though, bugs and tech debt are fixed immediately as they're discovered** — the tracking file is a fallback for when something genuinely can't be addressed in the current task, not a place to defer work that should be done now.

## Curated lessons

- **The last section of each account's `CLAUDE.md`, and of the `CLAUDE.md` in my projects folder, "Lessons agents learned (maintained automatically)", is rewritten every morning by a curator: never edit it by hand.** When I say "drop the curated rule about X", ask what the curator changed, or ask to undo it, follow the curator's `CURATED-RULES.md`.
