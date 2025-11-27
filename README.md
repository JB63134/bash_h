📦 h — A Bash help tool

h is a full fledged command resolution engine that unifies help into one shortcut - h.    
Provides help output by iterating through common flags '<command> --help -help -h -?'  
Provides help for builtins and keywords via help e.g 'help exit'  
For edge cases, keywords without help info, a brief description is provided.  
and as a backup, h checks if a man page or an info page exists and alerts the user.  

h is very BASH specific, and GNU specific. 

Fully Works (native, no changes):
All major Linux distros with GNU userland and Bash ≥ 4.3 such as:  
Debian / Ubuntu / Mint 
Fedora / RHEL / Rocky
Arch / Manjaro / Endeavour
openSUSE

Works With Minor Fix (install GNU tools):  
macOS (Homebrew GNU tools + brew bash)  
FreeBSD (pkg GNU tools + bash)

    22:21:55 Wed Nov 26: ~ $ cat crap
    sandy poop...

    22:22:00 Wed Nov 26: ~ $ h

    ├─ h - a BASH help tool
    ├─ 'cat' is an external command
        ↳ Path: /usr/bin/cat
        ↳ Showing 'cat --help':

             Usage: /usr/bin/cat [OPTION]... [FILE]...
             Concatenate FILE(s) to standard output.
         
             With no FILE, or when FILE is -, read standard input.
         
               -A, --show-all           equivalent to -vET
               -b, --number-nonblank    number nonempty output lines, overrides -n
               -e                       equivalent to -vE
               -E, --show-ends          display $ at end of each line
               -n, --number             number all output lines
               -s, --squeeze-blank      suppress repeated empty output lines
               -t                       equivalent to -vT
               -T, --show-tabs          display TAB characters as ^I
               -u                       (ignored)
               -v, --show-nonprinting   use ^ and M- notation, except for LFD and TAB
                   --help        display this help and exit
                   --version     output version information and exit
         
             Examples:
               /usr/bin/cat f - g  Output f's contents, then standard input, then g's contents.
               /usr/bin/cat        Copy standard input to standard output.
         
             GNU coreutils online help: <https://www.gnu.org/software/coreutils/>
             Full documentation <https://www.gnu.org/software/coreutils/cat>
             or available locally via: info '(coreutils) cat invocation'
         

    22:22:02 Wed Nov 26: ~ $ 

    
    22:01:10 Wed Nov 26: ~ $ h awk

    ├─ h - a BASH help tool
    ├─ 'awk' is an external command
        ↳ Path: /usr/bin/awk
        ↳ Symbolic link to: /usr/bin/mawk
        ↳ Showing 'mawk --help':

             Usage: mawk [Options] [Program] [file ...]
         
             Program:
                 The -f option value is the name of a file containing program text.
                 If no -f option is given, a "--" ends option processing; the following
                 parameters are the program text.
         
             Options:
                 -f program-file  Program  text is read from file instead of from the
                                  command-line.  Multiple -f options are accepted.
                 -F value         sets the field separator, FS, to value.
                 -v var=value     assigns value to program variable var.
                 --               unambiguous end of options.
         
                 Implementation-specific options are prefixed with "-W".  They can be
                 abbreviated:
         
                 -W version       show version information and exit.
                 -W dump          show assembler-like listing of program and exit.
                 -W help          show this message and exit.
                 -W interactive   set unbuffered output, line-buffered input.
                 -W exec file     use file as program as well as last option.
                 -W posix         stricter POSIX checking.
                 -W random=number set initial random seed.
                 -W sprintf=number adjust size of sprintf buffer.
                 -W traditional   pre-POSIX 2001.
                 -W usage         show this message and exit.

    22:15:57 Wed Nov 26: ~ $ h then

    ├─ h - a BASH help tool
    ├─ Detected BASH Keyword 'then' 
        ↳ then -- Begins the command block for a true condition.

    22:16:02 Wed Nov 26: ~ $ h exit

    ├─ h - a BASH help tool
    ├─ 'exit' is a shell builtin
        ↳ Showing 'help exit':

            exit: exit [n]
                Exit the shell.
            
                Exits the shell with a status of N.  If N is omitted, the exit status
                is that of the last command executed.




--------------------------------------------------------------------------------------------
history:  

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

