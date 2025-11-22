# bash_h
h - a buggy BASH Command Analyzer

Installation -- source .bash_h into the enviroment

h is a shell command introspection tool that tells you what a command really is, where
it comes from, how it resolves, what scripts or binaries it points to, and what metadata is associated with it.

🚀 Overview
Modern shells resolve commands through a complex chain: - aliases - functions - builtins - PATH binaries -
shell wrappers ( sudo , command , builtin , etc.)
h tries to walk this entire chain recursively, expanding each component and showing: - the real implementation
of the command - where it lives - whether it is shadowed or overridden - whether it is a script or ELF binary -
whether it requires root or has capabilities - which package it came from - any subcommands/functions
being called
All displayed in a clean, readable, syntax‑highlighted format.

✨ Key Features
🔍 Unified Command Introspection
Run:
h ls
and immediately see: - alias expansion (with history resolution) - function definitions (with source file + line
numbers) - builtin vs external detection - full search path lookup - highlighting of overrides/shadowing

🧩 Recursive Analysis
Commands like:
h sudo command builtin type ls | grep txt
are resolved component by component: - splits pipelines / chains safely - follows aliases → functions →
scripts → binaries - traces wrappers ( sudo , env , nice , etc.) - depth‑limited to avoid infinite cycles

1📜 Script Introspection
If a command resolves to a script, h extracts: - shebang type - interpreter path - real file location (following
symlinks) - syntax-highlighted preview of first few lines
Supports: - Bash - POSIX sh - Python - Perl - Ruby - Node / JS - Awk - Sed - And any executable text file with a
#!

⚙️ ELF Binary Analysis
If a command is an ELF binary, h shows: - architecture - whether it’s dynamically or statically linked -
interpreter ( ld-linux ) path - capabilities ( cap_* ) - SUID/SGID flags - owner + permissions - real path
after resolving symlinks

📦 Package Lookup
For binaries installed by system packages: - Debian/Ubuntu: dpkg -S 
h prints the package name, file ownership, and version info when possible.

🎨 Readable, Styled Output
Output uses: - color-coded sections - bold/italic classification - visual indicators for wrappers, pipes,
recursion depth - indentation for nested expansions
Everything is optimized for troubleshooting and learning.

🛠️ Usage
Simple:
h <command> analyzes the command 
h           analyzes the last command ran using fc for history
Examples:
h grep
h "sudo ls | awk '{print $1}'"
h ..
You can also feed history references:
h !!
h !42

🌲 How It Works (High-Level)
1. Parses the input command safely using shell-compatible rules. 
2. Splits pipelines ( | ), logical operators ( && , || ), and lists ( ; ). 
3. For each term, resolves in order: - alias - function - builtin - external binary 
4. Recursively expands nested definitions. 
5. Classifies the resolved file: - script - ELF binary - wrapper
6. Gathers metadata (owner, perms, package, capabilities). 
7. Prints structured results with color and indentation.

✨ Dependencies
Minimal: - Bash 5.0+ - Standard Unix tools: type , command , file , grep , awk , sed - Optional: -
dpkg (Debian/Ubuntu package metadata) - getcap / setcap (capabilities) - readlink -f for robust
path resolution

🧪 Use Cases
• Understanding what actually runs when you type a command
• Detecting command shadowing (alias vs function vs binary)
• Auditing scripts and wrappers in a toolchain
• Debugging PATH problems
• Verifying SUID or capability-based privilege escalation
• Teaching shell behavior to new engineers
