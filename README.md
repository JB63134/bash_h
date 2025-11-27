# h — Bash Help Tool

[![MIT License](https://img.shields.io/badge/license-MIT-green)](LICENSE)
[![Version](https://img.shields.io/badge/version-3.0.0-blue)](https://github.com/yourusername/h-bash-helper/releases)

'h' is a full fledged command resolution engine that unifies help into one shortcut - h      
`h` is a smart Bash helper that analyzes commands, aliases, functions, builtins, and scripts. It shows their location, expands aliases, provides help/man info, and even syntax-highlights functions and scripts.

---

## Features

- Detects Bash **keywords, builtins, aliases, functions, and external commands**  
- Shows **where commands/aliases/functions are defined**  
- Provides help output by iterating through common flags '<command> --help -help -h -?'
- For edge cases, keywords without help info, a brief description is provided.
- As a fallback, h checks if a man page or an info page exists and alerts the user.  
- Syntax-highlights Bash **functions and scripts**  
- Optional **FZF integration** for interactive selection

---

## Installation

Download `.bash_h` and source it in your `.bashrc`:

    echo 'source ~/.bash_h' >> ~/.bashrc
    source ~/.bashrc


h is very BASH specific, and GNU specific. 

Fully Works (native, no changes):
All major Linux distros with GNU userland and Bash ≥ 4.3 such as:  
Debian / Fedora / Arch / openSUSE

Works With Minor Fix (install GNU tools):  
macOS (Homebrew GNU tools + brew bash)  
FreeBSD (pkg GNU tools + bash)

--------------------------------------------------------------------------------------------

History:  

V1.0.0 was a small alias: alias h='eval "$(history -p \!\! | awk '\''{print $1}'\'')" --help'

V2.0.0 was a small function using fc similar to this:

      h() {
          last_cmd=$(fc -ln -1 | awk '{print $1}')
          # Run the last command with --help appended
          eval "$last_cmd --help"
      }
     
V3.0.0
      I decided to rewrite h so that i could get help from multiple sources.  
      Using common flags like '<command> --help', -help, -h, or -?.  
      For builtins and some keywords use 'help "command"'  
      As a fallback, checks if a man page exists and alerts the user.  
      For aliases, functions and scripts - handle displaying contents.  


------------------------------------------------------------------------------------------------------------------



📦 ca — A Buggy (But Ambitious) Bash Command Analyzer

ca is very BASH specific, and Debian specific. Should work on Debian based distributions like Ubuntu, and Linux Mint. 
  

The command resolution engine from my h function became the basis for ca.  
ca is a shell-command introspection tool that tells you what a command really is.   Instead of relying on multiple tools (type, which, command -V, declare, alias, etc.), ca unifies all resolution logic into a single command analysis engine.

🚀 Overview: Modern shells resolve commands through a layered chain: Alias → Function → Builtin → keyword → File in $PATH (Script / Binary).  ca tries to walk this chain recursively, determining the true implementation of any command while providing relevent information.

🧪 Use Cases Understand what actually runs when you type a command.   Audit scripts and wrappers in toolchains.   Debug $PATH problems.   Identify missing dependencies.   Identify SUID / capability-based escalation paths.   Identify aliases and functions that override other commands.   Identify disabled builtins that have been overridden by executables.

✨ Key Features 

🔍Command Resolution: For any given command, ca identifies: aliases, functions, builtins, keywords, external binaries, wrapper scripts, interpreted scripts and follows symlinks to find the exact thing that Bash will execute.

🧭 PATH Inspection: Displays the full resolved path and alerts you of symlinks. Can help you identify $PATH ordering issues, and overridden commands.

🔁 Recursive Analysis: (work in progress) Depth-limited to avoid infinite loops.

📜 Alias Introspection: If a command resolves to an alias, ca shows: alias definition, source file & line number (when available), recursive expansion and can alert you of aliases that shadow other files, etc.

📜 Function Introspection: ca extracts full function definition with a syntax-highlighted preview, and source file & line number (when available). Can alert you of Functions that shadow other files, builtins, etc.

📜 Script Introspection: ca identifies shebang interpreter, real file location (follows symlinks), shows syntax-highlighted preview.  Supports: bash, sh, python, perl, ruby, node, awk, sed, and any #! file.

📜 Builtin Introspection Shows: whether the builtin is enabled, whether it is a core builtin or loadable.

⚙️ ELF Binary Analysis Displays: architecture, dynamic vs static linking, ELF interpreter, (ld-linux) capabilities, SUID/SGID bits, owner & permissions, resolved real path  (follows symlinks), List all dependencies and identify missing dependencies. 

📦 Package Lookup On Debian/Ubuntu/linux-Mint systems: uses dpkg to display the package name, version,  maintainer info, and package description.

🛠️ Installation: Source .bash_ca
 
🛠️ Usage Basic: 

     ca without an argument uses history to lookup the last command ran.
     ca <command> gives you info about a specific command.
     Examples: 
     ca grep  
     ca which
     ca awk
     ca then
     ca 'sudo cp'  # output can get long, i might change this
     Analyze history references:
     ca !42 

💡 Tips Use ca when a command behaves unexpectedly. Use it to debug PATH issues or command conflicts. Use it when aliases or functions override global tools. Use it to audit your environment for security problems Or simply use it to explore Bash internals

