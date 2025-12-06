# h — Bash Help Tool

[![MIT License](https://img.shields.io/badge/license-MIT-green)](LICENSE)
[![Version](https://img.shields.io/badge/version-3.0.14-blue)](https://github.com/JB63134/bash_h/releases)

`h` is a full fledged command resolution engine that unifies help into one shortcut - h   `h` is a powerful Bash CLI helper that analyzes commands, functions, aliases, builtins, keywords, and external binaries. It helps you understand what a command does, where it is defined, and shows available documentation.

---

## Features

* Analyze **builtins, aliases, keywords, functions, and external commands**.
* Shows **where a command is defined** (files, line numbers, or shell).
* Displays **alias expansions** and function contents.
* Provides **help output** or points to `man` / `info` pages.
* Syntax-highlighted preview for functions and scripts (`batcat` for enhanced highlighting with fallback to `perl`).
* Integrates with **fzf** for interactive command search.
* Automatic detection of commonly used **admin paths**.

---
## Usage

```bash
h [command]       # Analyze the given command
h                 # Analyze your most‑recent command
h -f              # Launch interactive search using fzf
h -h | --help     # Show usage instructions
h -v | --version  # Show version info
```
---

## Examples

```bash
04:12:55 Tue Dec 02: ~ $ h awk

╔══════════════════════════════════════════════╗
║   h – Bash Help Tool                         ║
╚══════════════════════════════════════════════╝
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
         

```

```bash
04:13:01 Tue Dec 02: ~ $ h l

╔══════════════════════════════════════════════╗
║   h – Bash Help Tool                         ║
╚══════════════════════════════════════════════╝
├─ 'l' is an alias → resolves to: alias l='ls -CF'
    ↳ Defined in: /home/jb/.bash_aliases (line 13)

```

```bash
04:22:41 Tue Dec 02: ~ $ h o

╔══════════════════════════════════════════════╗
║   h – Bash Help Tool                         ║
╚══════════════════════════════════════════════╝
├─ 'o' is a shell function
    ↳ Declared in: /home/jb/.bash_functions (line 67)
    ↳ Showing function: o

    o () 
    { 
        for arg in "$@";
        do
            setsid xdg-open "$arg" > /dev/null 2>&1 < /dev/null &
        done
    }

    ─── End of function 'o' ───

```

---

## Dependencies

**Required**:

* `grep`, `basename`, `file`, `find`, `sed`, `cut`, `head`, `readlink`, `realpath`, `perl`

**Optional (enhanced experience)**:

* `tput` – colorful output
* `fzf` – interactive search
* `man`, `info` – manual/info pages
* `batcat` – syntax highlighting
---

## Installation

Download `.bash_h` and source it in your `.bashrc`:

    echo 'source ~/.bash_h' >> ~/.bashrc
    source ~/.bashrc


h is very BASH specific, and GNU specific.   
Compatability table:

| System                | Works? | Notes                                     |
| --------------------- | ------ | ----------------------------------------- |
| Ubuntu / Debian       | ✅      | Fully supported                           |
| Fedora / RHEL / Rocky | ✅      | Fully supported                           |
| Arch / Manjaro        | ✅      | Fully supported                           |
| Gentoo                | ✅      | Fully supported                           |
| openSUSE              | ✅      | Fully supported                           |
| macOS                 | ⚠️     | Needs GNU utilities installed             |
| FreeBSD / OpenBSD     | ⚠️     | Needs Bash + GNU utils                    |
| Alpine                | ❌      | Unless user installs bash + GNU coreutils |
| BusyBox environments  | ❌      | Too limited                               |
| Dash, ksh, zsh        | ❌      | Script is bash-only                       |


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
      As a fallback, checks for both man pages and info pages then alerts the user if found.
      For aliases, functions and scripts - handle displaying contents.  


------------------------------------------------------------------------------------------------------------------
