
📦 h — A Buggy (But Ambitious) Bash Command Analyzer

h is a deep-introspection utility for Bash.

A shell-command introspection tool that tells you what a command really is. Instead of relying on multiple tools (type, which, command -V, compgen, alias, etc.), h unifies all resolution logic into a single command analysis engine.

🚀 Overview Modern shells resolve commands through a layered chain: Alias → Function → Builtin → File in $PATH → Script / Interpreter → ELF Binary h tries to walk this chain recursively, determining the true implementation of any command while providing relevent information and any available help.

✨ Key Features 

🔍Command Resolution: For any given command, h identifies: aliases, functions, builtins, keywords, external binaries, symlinks / wrapper scripts, interpreted scripts, and shadowing to find the exact thing that Bash will execute.

🧭 PATH Inspection: Displays the full resolved path. Warns about shadowed commands and can help you identify $PATH ordering issues

🔁 Recursive Analysis: (work in progress) Depth-limited to avoid infinite loops.

📜 Alias Introspection: If a command resolves to an alias, h shows: alias definition source file & line number (when available) recursive expansion and can alert you of aliases shadowing other files, builtins, etc.

📜 Function Introspection h extracts: full function definition file & line number (when available) syntax-highlighted preview

📜 Script Introspection For scripts, h identifies: shebang interpreter interpreter path real file location (follows symlinks) syntax-highlighted preview Supports: bash, sh, python, perl, ruby, node, awk, sed, and any #! file.

📜 Builtin Introspection Shows: whether the builtin is enabled, whether it is shadowed, and the  builtin 'help <command>'

⚙️ ELF Binary Analysis Displays: architecture, dynamic vs static linking, ELF interpreter, (ld-linux) capabilities, SUID/SGID bits, owner & permissions, resolved real path, binary help output '<command> --help -help -h -?'

📦 Package Lookup On Debian/Ubuntu systems: uses dpkg and apt to display the package name, version,  maintainer info, and package description.

🛠️ Usage Basic:
h  
Analyze history references: 
h !! 
h !42 
Examples: 
h grep  
h ..

🧪 Use Cases Understand what actually runs when you type a command. Detect command shadowing (alias → function → binary). Audit scripts and wrappers in toolchains. Debug $PATH problems. Identify SUID / capability-based escalation paths.

💡 Tips Use h when a command behaves unexpectedly. Use it to debug PATH issues or command conflicts. Use it when aliases or functions override global tools. Use it to audit your environment for security problems Or simply use it to explore Bash internals



But thats all a bunch of crap, this is really an over ambitious help tool: 

V1.0.0 was a small alias: alias h='eval "$(history -p \!\! | awk '\''{print $1}'\'')" --help'

V2.0.0 was a small function using fc similar to this:
      h() {
          last_cmd=$(fc -ln -1 | awk '{print $1}')
          # Run the last command with --help appended
          eval "$last_cmd --help"
      }
     
V3.0.0
      I decided to rewrite h so that i could get help from multiple sources
      common flags like '<command> --help', -help, -h, or -?
      along with 'help <command>' for builtins and some keywords
      as a fallback, it checks if a man page exists and alerts the user.
      For aliases, functions and scripts - handle displaying contents.
      Then feature creep got away from me and Now I have this 1200+ line mess in BASH
      Plus! the parser, and pipeline handling are vibe coded, (chatgpt sucks)
      this is just a side project for me, not sure if it will ever work right,
      but someone might find this useful and decide to fork it.
