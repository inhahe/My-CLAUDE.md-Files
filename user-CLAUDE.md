# graphify
- **graphify** (`~/.claude/skills/graphify/SKILL.md`) - any input to knowledge graph. Trigger: `/graphify`
When the user types `/graphify`, invoke the Skill tool with `skill: "graphify"` before doing anything else.

# Rules for every project

They live in `~/claude-global.md`, which the line below imports and every account shares: edit them there. This file keeps only what belongs to this account, and the curator's section at the end.

@~/claude-global.md

# Lessons agents learned (maintained automatically)

Kept by a morning curator, which distills these from lessons that recurred across separate sessions. It is rewritten every morning, so do not edit it by hand: to remove a rule for good, tell any agent "drop the curated rule about ...".

- **The Bash tool resets the working directory to the project root after every call.** A `cd` never carries over to the next call: begin each command with `cd "<dir>" &&`, or use absolute paths. Quote a path that contains a space, or the `cd` lands on the part before the space.
- **Never wait by sleeping, and background anything that may take minutes.** The harness blocks a Bash call that sleeps to wait for something (`sleep 40; tail log`), and a call that outlives its timeout — two minutes by default, ten at most — is moved to the background mid-run. Start the work itself with `run_in_background: true` and let the completion notification wake you.
- **A pipe or a trailing `echo` discards the exit status you care about.** `cmd | grep …` reports the filter's status and `cmd; echo done` reports `echo`'s, and so does the background-task completion notification — it has reported `exit code 0` for a failed build and for a failed `git push`. Redirect to a file, capture `$?` on its own line, then search the file. A pipe also buffers, and so does Python the moment its stdout is a pipe or a file: the log stays empty until the run exits, so a working run looks like a hang — run `python -u` or set `PYTHONUNBUFFERED=1`.
- **`TaskStop` kills the task's shell, not its descendants, and runs no `trap`.** It has left an inner `bash`, a QEMU and a lock file behind, which then raced the next run: kill the survivors by PID.
- **A Bash call that walks a big tree is killed at the timeout, and its answer is lost.** Do not run `grep -r` from a repo root, `find` without `-maxdepth`, `du -sh` on a build directory, `git log -S`/`-G` over all history, or a shell loop that spawns a process per file, symbol or branch. Use the Grep tool, `git ls-files`, `df`, `git log -- <path>`, or a single Python pass.
- **Three `grep` traps in Git Bash.** `-P` does not work at all — it rejects the locale, so use `-E`, `awk` or Python. `grep -c` exits 1 on a count of zero, firing `||` branches and aborting `set -e` scripts. Build and test logs carry NUL bytes, so pass `-a` or grep reports only that the binary file matches; and `| head` truncates rather than counts.
- **When a scripted replace or an Edit matches nothing, re-read the file instead of trying variants of the pattern.** On Windows the cause is usually concrete: the file is CRLF and your pattern is LF, a formatter has reflowed the anchor, or the shell ate a backslash. Match what the bytes actually say. Never build a `sed` line range from a command substitution or arithmetic: when the inner `grep -n` finds nothing the range becomes a `sed` command and the call errors. Use the Read tool with `offset`, or `awk 'NR>=a && NR<=b'`.
- **A string a Windows tool printed rarely compares equal.** `$(...)` in Git Bash carries the `\r` of CRLF output: pipe it through `tr -d '\r'`. And `git worktree list` and `os.path.relpath` mix `/` with `\`, so normalize both sides or match `[\\/]`. Each mismatch is silent: an empty result reads as a clean one.
- **Git Bash has no `bc` and no `/usr/bin/time`:** neither ships with Git for Windows. Use `$(( ))` for integer arithmetic and `awk` for anything fractional, and time a command with PowerShell's `Measure-Command`.
- **Never edit a file a running job is reading.** A suite's verdict becomes meaningless, and a shell reads its script incrementally from a byte offset, so changing its length makes the running shell resume mid-token. Wait for the job, or run a copy.
- **Never `git stash` in a tree another session may be using.** The stash list belongs to the repository, not the worktree: your `pop` can drag in a peer's entry, and your `push` can carry their uncommitted work away. Save your own with `git diff > patch`, or commit on top of `HEAD`.
- **Prove a new regression test fails without the fix.** Put the bug back, watch the test fail with the assertion you expect, then restore the fix. A test written against already-fixed code passes whatever happens and guards nothing.
