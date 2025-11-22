📦 h — A Buggy (But Ambitious) Bash Command Analyzer

A shell-command introspection tool that tells you what a command really is.

h is a deep-introspection utility for Bash.
Instead of relying on multiple tools (type, which, command -V, compgen, alias, etc.),
h unifies all resolution logic into a single command analysis engine.

It recursively expands aliases, functions, builtins, binaries, scripts, pipelines, lists, and wrappers — producing a clean, readable, syntax-highlighted explanation of what Bash will actually run.

Designed for:

learners exploring shell internals

sysadmins & DevOps engineers

security auditors

developers maintaining complex shell environments

⚠️ Note: This project is experimental. The author calls it “buggy.”
Contributions, issues, and PRs are welcome.

🚀 Overview

Modern shells resolve commands through a layered chain:

Alias → Function → Builtin → File in $PATH → Script / Interpreter → ELF Binary


h tries to walk this chain recursively, determining the true implementation of any command — including nested components in pipelines, functions, and aliases.

It shows:

what Bash actually executes

where it lives

whether it's shadowed or overridden

whether it's a script or ELF binary

permissions, capabilities, shebangs

which package installed it

definitions, file origins, previews, and more

All in a clear, syntax-highlighted display.

✨ Key Features
🔍 Command Resolution

For any given input, h identifies:

aliases

functions

builtins

keywords

external binaries

symlinks / wrapper scripts

interpreted scripts

shadowing / overrides

exact file Bash will execute

🧭 PATH Inspection

Displays the full resolved path

Warns about shadowed commands

Identifies $PATH ordering issues

🔁 Recursive Analysis

(This is currently being improved — alias recursion is known to be buggy.)

Supports recursion into:

pipelines (cmd1 | cmd2)

command lists (cmd1 && cmd2, cmd1; cmd2)

alias expansions

functions containing additional commands

Depth-limited to avoid infinite loops.

📜 Alias Introspection

If a command resolves to an alias, h shows:

alias definition

source file & line number (when available)

recursive expansion

shadowing warnings

📜 Function Introspection

h extracts:

full function definition

file & line number

syntax-highlighted preview

📜 Script Introspection

For scripts, h identifies:

shebang interpreter

interpreter path

real file location (follows symlinks)

script preview

Supports:
bash, sh, python, perl, ruby, node, awk, sed, and any #! file.

📜 Builtin Introspection

Shows:

whether the builtin is enabled

whether it is shadowed

builtin help text

⚙️ ELF Binary Analysis

Displays:

architecture

dynamic vs static linking

ELF interpreter (ld-linux)

capabilities

SUID/SGID bits

owner & permissions

resolved real path

binary help output

📦 Package Lookup

On Debian/Ubuntu systems:

uses dpkg -S

prints package name, version, and file ownership

🛠️ Usage
Basic:
h <command>

Analyze last executed command:
h

Analyze history references:
h !!
h !42

Examples:
h grep
h "sudo ls | awk '{print $1}'"
h ..

🧪 Use Cases

Understand what actually runs when you type a command

Detect command shadowing (alias → function → binary)

Audit scripts and wrappers in toolchains

Debug $PATH problems

Identify SUID / capability-based escalation paths

Teach Bash command resolution to new engineers

Inspect environment for dangerous executables in writable dirs

💡 Tips

Use h when a command behaves unexpectedly

Use it to debug PATH issues or command conflicts

Use it when aliases or functions override global tools

Use it to audit your environment for security problems

Or simply use it to explore Bash internals



