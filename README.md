📦 h — A Buggy (But Ambitious) Bash Command Analyzer

A shell-command introspection tool that tells you what a command really is.

h is a deep-introspection utility for Bash.
Instead of relying on multiple tools (type, which, command -V, compgen, alias, etc.),
h unifies all resolution logic into a single command analysis engine.

It Detects aliases, functions, builtins, keywords, binaries, scripts, pipelines, chained commands, and wrappers — producing a clean, readable, explanation of what Bash will actually run.

Designed for:
learners exploring shell internals
sysadmins & DevOps engineers
security auditors
developers maintaining complex shell environments

🚀 Overview
Modern shells resolve commands through a layered chain:
Alias → Function → Builtin → File in $PATH → Script / Interpreter → ELF Binary
h tries to walk this chain recursively, determining the true implementation of any command — including nested components in pipelines, chains, and aliases.

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
Supports recursion into:
pipelines (cmd1 | cmd2)
command chains (cmd1 && cmd2, cmd1; cmd2)
alias expansions
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
file & line number (when available)
syntax-highlighted preview

📜 Script Introspection
For scripts, h identifies:
shebang interpreter
interpreter path
real file location (follows symlinks)
syntax-highlighted preview
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



