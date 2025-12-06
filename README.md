# h — Bash Help Tool

[![MIT License](https://img.shields.io/badge/license-MIT-green)](LICENSE)
[![Version](https://img.shields.io/badge/version-3.0.20-blue)](https://github.com/JB63134/bash_h/releases)

**hh** is a full fledged command resolution engine that unifies help into one shortcut - **h**    It helps you understand what a command is, and shows available documentation.

I decided to write this version of **h** so that I could get help from multiple sources.  
For binaries '<command> --help', -help, -h, or -?.  
For builtins and some keywords use 'help "command"'  
As a fallback, checks for both man pages and info pages then alerts the user if found.
For aliases, functions and scripts - handle displaying contents.  

## Requires: 

Bash ≥ V4.4 and GNU utils    
Sourced file detection supports debian and fedora / rhel setups 

## Features

* **Syntax-highlighting**:
  
  * 50 line preview of scripts and functions
* **aliases and functions**:

  * show alias definition or function body
  * location (file and line number, or interactive shell)
* **builtins and Keywords**:

  * Display help output - 'help <command>'
  * Internal Descriptions for Edge cases missing help output  
* Inspect **external binaries**:

  * Displays the full resolved path and alerts you of symlinks
  * Display help output by iterating through common flags
  * Tries --help -help -h and -?
* **Interactive search**: using `fzf` for commands (optional)
* Pretty-printed, colorized output (uses `tput` or ANSI colors)

---

## Installation

Clone the repository and source the script in your `.bashrc` or `.bash_profile`:

```bash
git clone https://github.com/JB63134/bash_h.git
source /path/to/ca/.bash_h
```

---

## Usage

```bash
h [command]
```

If no command is provided, `h` will analyze your **most recent command**.

### Options

| Option               | Description                                                        |
| -------------------- | ------------------------------------------------------------------ |
| `-h`, `--help`       | Show help text                                                     |
| `-v`, `--version`    | Show version information                                           |
| `-f`, `--fzf`        | Interactively search for commands (requires `fzf`)                 |


---

## Screenshots / Output Preview

![Aliases](images/alias.png)
![Functions](images/function.png)
![Builtins and Keywords](images/builtin-keyword.png)
![binry](images/awk.png)
![Script](images/script.png)

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
