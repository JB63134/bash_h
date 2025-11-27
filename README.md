# h — Bash Help Tool

[![MIT License](https://img.shields.io/badge/license-MIT-green)](LICENSE)
[![Version](https://img.shields.io/badge/version-3.0.0-blue)](https://github.com/yourusername/h-bash-helper/releases)

`h` is a full fledged command resolution engine that unifies help into one shortcut - h   

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

