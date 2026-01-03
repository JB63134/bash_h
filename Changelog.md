# `bash_h` Changelog

## V3.1.1
- trap for path restore fixed

## V3.1.0
- Major refactor
- UI/UX improvements
- Argument handling rewritten with graceful error paths
- Detect scripts that support help flags and display help automatically fallback to source
- Trace mode now recognizes and reports disabled builtins
- Removed greeting banner

## V3.0.55
- ui/ux stuff
- testing on fedora - path manipulation bug fixed

## V3.0.52
- Minor UI/UX updates to keep consistent with `bash_ca`

## V3.0.51
- UI/UX improvements
- Trace mode fixes:
  - Shadowed functions reliably found
  - Depth count for recursion control fixed

## V3.0.47
- UI/UX improvements
- Trace mode fixes for shadowed builtins

## V3.0.45
- New **Command Resolution Trace**:
  - Shows Bash command resolution order
  - Highlights shadowing (e.g., alias overriding a builtin)  
- Example: `h --trace awk`

## V3.0.40
- PATH code refinement

## V3.0.38
- Code cleanup
- UI/UX improvements

## V3.0.37
- Code cleanup
- Sourced file detection guarded to one execution per invocation
- Fixed possible expansion issues

## V3.0.35
- Fixed alias expansion issues
- Code cleanup (ShellCheck passed)

## V3.0.32
- UI fixes

## V3.0.30
- Clarified keyword definitions and fixed usage

## V3.0.28
- Added script info
- Multiple UI updates

## V3.0.24
- UI fix: Confirmation displayed when a symlink was followed for a script

## V3.0.23
- Added function to check help output:
  - Identify binaries that return help text with non-zero exit codes
  - Fallback to `man` and `info` pages

## V3.0.21
- Fixed variable expansion issues (ShellCheck passed)

## V3.0.20
- Major overhaul of sourced file detection:
  - Handles nested sourced files and conditional sourcing
  - Follows Bash internal logic for startup files
  - Detects RHEL/Fedora and adds `profile.d/*sh` for interactive non-login shells

## V3.0.14
- Fixed bug in dependency check

## V3.0.12
- Code cleanup (ShellCheck passed)

## V3.0.10
- Code cleanup and UI bug fixes
- Added `batcat` syntax highlighting

## V3.0.8
- Code cleanup and UI bug fixes
- Consolidated PATH manipulation code
- PATH restoration handled by new function with a trap

## V3.0.2
- Switched highlighting to Perl heredoc instead of sed
- Enhanced `<TAB>` completion and PATH detection

## V3.0.0
- Complete rewrite for multi-source help:
  - Supports common flags: `--help`, `-help`, `-h`, `-?`
  - Builtins/keywords: uses `help "command"`
  - Falls back to `man` and `info` pages if available
  - Displays contents for aliases, functions, and scripts

## V2.0.0
- Small function using `fc` to append `--help` to last executed command
```bash
h() {
    last_cmd=$(fc -ln -1 | awk '{print $1}')
    eval "$last_cmd --help"
}
```

## V1.0.0

* Original small alias:

```bash
alias h='eval "$(history -p !! | awk '\''{print $1}'\'') --help"'
```
