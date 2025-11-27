# h — Bash Help Tool

[![MIT License](https://img.shields.io/badge/license-MIT-green)](LICENSE)
[![Version](https://img.shields.io/badge/version-3.0.0-blue)](https://github.com/yourusername/h-bash-helper/releases)

`h` is a smart Bash helper that analyzes commands, aliases, functions, builtins, and scripts. It shows their location, expands aliases, provides help/man info, and even syntax-highlights functions and scripts.

---

## Features

- Detects Bash **keywords, builtins, aliases, functions, and external commands**  
- Shows **where commands/aliases/functions are defined**  
- Expands **aliases** and **environment variables**  
- Displays `--help` or points to `man`/`info` pages  
- Syntax-highlights Bash **functions and scripts**  
- Optional **FZF integration** for interactive selection  
- Depth-safe analysis to prevent recursion loops  

---

## Installation

Download `.bash_h` and source it in your `.bashrc`:

```bash
# Source in your shell
echo 'source ~/.bash_h' >> ~/.bashrc
source ~/.bashrc
