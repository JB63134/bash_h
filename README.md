# h — Bash Help Tool

[![MIT License](https://img.shields.io/badge/license-MIT-green)](LICENSE)
[![Version](https://img.shields.io/badge/version-3.0.2-blue)](https://github.com/JB63134/bash_h/releases)

`h` is a full fledged command resolution engine that unifies help into one shortcut - h   

---

## Features

- Detects Bash **keywords, builtins, aliases, functions, and external commands**  
- Shows **where commands/aliases/functions are defined**  
- Provides help output by iterating through common flags '<command> --help -help -h -?'
- As a fallback, h checks if a man page or an info page exists and alerts the user.  
- Syntax-highlights **functions and scripts**  
- Optional **FZF integration** for interactive selection

---

This has become 2 separate projects as outlined below.


| Feature / Scope                | `h` (Bash help)                          | `ca` (Command Analyzer)                                                                |
| ------------------------------ | ---------------------------------------- | -------------------------------------------------------------------------------------- |
| **Type detection**             | ✅ Keywords, builtins, aliases, functions | ✅ Keywords, builtins, aliases, functions                                               |
| **External commands**          | Path + help/manual, scripts highlighted  | ✅ Path, symbolic links, binary type, ELF headers, SUID/SGID, dependencies, permissions |
| **Scripts / Functions**        | ✅ Shows content with syntax highlighting | ✅ Shows content with syntax highlighting                                               |
| **Permissions / Ownership**    | ❌                                        | ✅ Includes SUID/SGID, owner/group, octal permissions                                   |
| **Shadow / Overrides**         | ❌                                        | ✅ Detects overridden commands, shadowing                                               |
| **Dependencies / Environment** | ❌                                        | ✅ Checks missing deps, sourced files hierarchy                                         |
| **Package info**               | ❌                                        | ✅ Version, maintainer, description                                                     |
| **Interactive search**         | ✅ fzf                                    | ✅ fzf                                                                                  |
| **Focus / Use case**           | Shell-level explanation                  | Deep system/binary inspection                                                          |
| **Ease of use**                | ✅ Tab Completion                         | ✅ Tab Completion                                                                       |




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

