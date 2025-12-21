# Bash Help Tool (`h`) Overview

## 1. Purpose


- Shows command resolution order (alias → function → builtin → PATH).  

---

## 2. Core Features

### Command Detection
- Determines whether a command is:
  - Alias
  - Shell function
  - Builtin
  - Keyword
  - External file/script
- Provides context (e.g., file location, line number, symlink targets).

### Help Output
- Shows help from:
  - `help` for builtins/keywords
  - `--help` / `-h` flags for external commands
  - `man` or `info` pages if available

### Script/Function Preview
- Displays the first 50 lines
- Syntax highlighting:
  - Prefers `bat` / `batcat` if installed
  - Otherwise uses embedded Perl for colorized Bash syntax

### Alias Expansion
- Resolves aliases and expands variables like `$HOME`, `$USER`  
- Shows where aliases are defined (interactive shell or sourced file)

### Command Resolution Trace
- `_h_print_trace` shows the order Bash would resolve a command  
- Highlights shadowing (e.g., alias overriding a builtin)

### Dependency Checking
- Required: `grep`, `basename`, `file`, `find`, `sed`, `cut`, `head`, `readlink`, `realpath`, `perl`  
- Optional: `tput`, `fzf`, `bat`, `batcat`  
- Warns or exits if required dependencies are missing

### Sourcing Tree Analysis
- `_h_sourcedtree` recursively detects sourced files from `.bashrc`, `.bash_profile`, `/etc/profile.d`, etc.  
- Handles `source`, `.`, conditional sourcing, array sourcing safely (no arbitrary `eval`)

### Interactive Command Selection
- If `fzf` is installed, `h -f` lets users pick a command interactively

### Color Support
- Detects terminal capabilities via `tput`  
- ANSI color codes used for highlighting output categories: keywords, builtins, aliases, files, scripts, variables, numbers, comments, etc.

---

## 3. Usage Examples

```bash
h                   # Analyze the last executed command
h ls                # Show detailed info about 'ls'
h awk               # Show help, location, and script preview if available
h -f                # Use fzf to select a command interactively
h -t awk            # Show detailed trace of command resolution
h -h                # Show usage
h -v                # Show version info



---

---




# h — Bash Help Tool

[![MIT License](https://img.shields.io/badge/license-MIT-green)](LICENSE)
[![Version](https://img.shields.io/badge/version-3.0.38-blue)](https://github.com/JB63134/bash_h/releases)

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

`fzf` -- for interactive search    
`bat` / `batcat` -- for pretty syntax-highlighting. `h` will fallback to an internal perl script for basic highlighting 

## Features

* **Syntax-highlighting**:
  
  * 50 line preview of scripts and functions, can use `bat` as optional highlighter.
* **Aliases and Functions**:

  * show alias definition or function body
  * location (file and line number, or interactive shell)
* **Builtins and Keywords**:

  * Display help output - 'help 'command''
  * Internal Descriptions for Edge cases missing help output  
* Inspect **External Binaries**:

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
source /path/to/.bash_h
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
| `-t`, `--trace`      | Show detailed trace of command resolution                          |

---

## Screenshots / Output Preview

![binry](images/awk.png)
![Aliases](images/alias.png)
![Functions](images/function.png)
![Builtins and Keywords](images/builtin-keyword.png)
![Script](images/script.png)

--------------------------------------------------------------------------------------------



