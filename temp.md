- Shows command resolution order (alias → function → builtin → PATH).  
---
##  Features

### Command Detection
- Determines whether a command is:
  - Alias
  - Shell function
  - Builtin
  - Keyword
  - External file/script
- Provides context (e.g., file location, line number, symlink targets).

---

## Features

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

### Interactive Command Selection
- If `fzf` is installed, `h -f` lets users pick a command interactively

### Command Resolution Trace
- `_h_print_trace` shows the order Bash would resolve a command  
- Highlights shadowing (e.g., alias overriding a builtin)

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
h                   # Analyze the last executed command
h ls                # Show detailed info about 'ls'
h awk               # Show help, location, and script preview if available
h -f                # Use fzf to select a command interactively
h -t awk            # Shows command resolution order (alias → function → builtin → PATH).  
h -h                # Show usage
h -v                # Show version info
```

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



