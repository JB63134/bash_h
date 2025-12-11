# h — Bash Help Tool

[![MIT License](https://img.shields.io/badge/license-MIT-green)](LICENSE)
[![Version](https://img.shields.io/badge/version-3.0.32-blue)](https://github.com/JB63134/bash_h/releases)

**h** is a full fledged command resolution engine that unifies help into one shortcut - **h**    
It helps you understand what a command is, and shows available documentation.
  
**h** started out as an alias h='eval "$(history -p !! | awk '''{print $1}''')" --help'
  
I decided to write this version of **h** so that I could get help from multiple sources.  
For binaries '<command> --help', -help, -h, or -?.  
For builtins and some keywords use 'help "command"'  
As a fallback, checks for both man pages and info pages then alerts the user if found        
For aliases, functions and scripts - handle displaying contents.  

## Requirements 

Bash ≥ V4.4 and GNU utils    
Sourced file detection supports debian and fedora / rhel setups 

## Optional dependencies

fzf -- for interactive search
bat / batcat -- for pretty syntax-highlighting. h will fallback to an internal perl script for syntax-highlighting 

## Features

* **Syntax-highlighting**:
  
  * 50 line preview of scripts and functions, can use bat as optional highlighter.
* **aliases and functions**:

  * show alias definition or function body
  * location (file and line number, or interactive shell)
* **builtins and Keywords**:

  * Display help output - 'help 'command''
  * Internal Descriptions for Edge cases missing help output  
* Inspect **external binaries**:

  * Displays the full resolved path and alerts you of symlinks
  * Display help output by iterating through common flags
  * Tries --help -help -h and -?
* **Interactive search**: using `fzf` for commands (optional)
* Supports TAB completion
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

![binry](images/awk.png)
![Aliases](images/alias.png)
![Functions](images/function.png)
![Builtins and Keywords](images/builtin-keyword.png)
![Script](images/script.png)

--------------------------------------------------------------------------------------------
