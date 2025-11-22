bash_h
h - a buggy BASH Command Analyzer

h is a shell command introspection tool that tells you what a command really is, 
Instead of relying on multiple tools (type, which, command -V, compgen, alias, etc.), 
the h function unifies all resolution logic and recursively analyzes commands, pipelines, 
functions, aliases, builtins, binaries, scripts, shebangs, permissions, shadowing, 
PATH issues, and more.

It is intended for: - Sysadmins & DevOps engineers - Security auditors - Developers maintaining complex shell environments - Learners exploring Bash internals

🚀 Overview Modern shells resolve commands through a complex chain: - aliases - functions - builtins - PATH binaries - shell wrappers ( sudo , command , builtin , etc.) h tries to walk this entire chain recursively, expanding each component and showing: - the real implementation of the command - where it lives - whether it is shadowed or overridden - whether it is a script or ELF binary - whether it requires root or has capabilities - which package it came from - any subcommands/functions being called All displayed in a clean, readable, syntax‑highlighted format.

✨ Key Features 

🔍 Command Resolution
For any given input, h determines exactly what Bash would execute: 
Alias - Function - Builtin - Keyword - Binary (with full path) - Shell script or interpreted script - Symlink chain resolution
builtin vs external detection
highlighting of overrides/shadowing

🧭 PATH Inspection
• Shows the exact file found by Bash
• Warns about shadowed commands (e.g., local ls hiding /bin/ls)
• Can highlight PATH misconfigurations

🔁 Recursive Analysis     *** need help here *** my recursive logic for aliases is messed up... 
If the command contains: 
Pipelines (cmd1 | cmd2)  
Lists (cmd1 && cmd2, cmd1; cmd2) 
Aliases expanding into other commands 
Functions containing additional commands
h tries to recursively analyzes each component.
depth‑limited to avoid infinite cycles

📜 Alias Introspection If a command resolves to an alias, h extracts: 
alias definition,
real file location and line number

📜 Function Introspection If a command resolves to a function, h extracts: 
function definition,
real file location and line number
syntax-highlighted preview of the function 

📜 Script Introspection If a command resolves to a script, h extracts: 
Shebang type 
Interpreter path 
real file location (following symlinks) 
syntax-highlighted preview of the script 
Supports: Bash - POSIX sh - Python - Perl - Ruby - Node / JS - Awk - Sed
And any executable text file with a #!

📜 Builtin Introspection If a command resolves to a Builtin, h extracts: 
Whether the builtin is enabled
if the builtin is overriden by an executable
and shows help for the builtin 

⚙️ ELF Binary Analysis If a command is an ELF binary, h shows: 
Architecture 
Whether it’s dynamically or statically linked 
Interpreter ( ld-linux ) path 
Capabilities ( cap_* ) 
SUID/SGID flags 
Owner + permissions 
Real path after resolving symlinks
and shows help for the elf 

📦 Package Lookup For binaries installed by system packages: 
Debian/Ubuntu: dpkg -S 
h prints the package name, file ownership, and version info when possible.

🛠️ Usage Simple: 
h <command>        analyzes the command 
h                  analyzes the last command ran using fc for history 
Examples: 
h grep 
h "sudo ls | awk '{print $1}'" 
h .. 
h
You can also feed history references: 
h !! 
h !42

🧪 Use Cases 
• Understanding what actually runs when you type a command 
• Detecting command shadowing (alias vs function vs binary) 
• Auditing scripts and wrappers in a toolchain 
• Debugging PATH problems 
• Verifying SUID or capability-based privilege escalation 
• Teaching shell behavior to new engineers

Tips
    • Use h when a command behaves “wrong” or unexpectedly
    • Use it to debug $PATH ordering issues
    • Use it when aliases or functions interfere with global commands
    • Use it to audit your environment for dangerous executables in writable directories
    • Or use it to just get help for a command






























