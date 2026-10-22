# Reference Codebases

- **<Program> source**: `<path>` — full source of <Program>. Useful for understanding how it implements features that <Project A> mirrors.

# <Project A> (`<path>`)

<What it is, in a line: language, framework, how big.>

- **Architecture and features:** `<Project A>/design.md`. Read it before a change whose effects leave the file you are editing.
- **User-facing document:** `<Project A>/REFERENCE.md` — see *User-Facing Documentation* in the global rules. Observable changes (CLI flags, defaults, slash commands, keybindings, toolbar content, styling, startup behaviour, how it responds to events) go in it in the same commit.

# <Project B> (`<path>`)

**This is the one I actively use.** <What it is, and how to run it: `python server.py` from the directory you want it to work in, say.>

- **Architecture and features:** `<Project B>/design.md`, which carries the file layout and every key flow. Read it before a change whose effects leave the file you are editing.

# <Project C> (`<path>`)

<What it is.>

**`<Project C>/README.md` is its user-facing document** — see *User-Facing Documentation* in the global rules.

# <Project D> (`<path>`)

- **Project-specific instructions:** `<Project D>/CLAUDE.md` (versioning policy, release script). Loaded automatically when working in that directory.
- **Architecture and features:** `<Project D>/design.md`.

# Global rules

They are in `~/claude-global.md`, which each account's own `CLAUDE.md` imports, so they reach every project on every drive, these included.
