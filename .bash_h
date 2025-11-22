#-------------------------------------------------------------------------------------------------
# .bash_help
# Function: h
# Purpose: Analyzes a Bash command, alias, builtin, function, or binary
#-------------------------------------------------------------------------------------------------

OLD_PATH="$PATH"

declare -gA __H_SEEN_ALIASES
declare -gA __H_SEEN_COMMANDS
declare -gA __H_SEEN_BUILTINS
declare -gA __H_SHADOW_SEEN
declare -g suppress_builtin_msg=1   # suppress repeated builtin help
complete -F _h_completion h

h() {

    # -------------------------
    # Set path for system commands to be included 
    # -------------------------

    trap 'PATH="$OLD_PATH"' RETURN

    local admin_dirs=(
        "$HOME/.local/bin"
        "$HOME/bin/scripts"
        "$HOME/bin"
        "/usr/local/bin"
        "/usr/local/sbin"
        "/usr/local/games"
        "/usr/bin"
        "/usr/sbin"        
        "/usr/games"
        "/bin"
        "/sbin"   
        )
    
    # Add admin dirs to PATH if missing
    for dir in "${admin_dirs[@]}"; do
        if [[ -d "$dir" && ":$PATH:" != *":$dir:"* ]]; then
            PATH="$PATH:$dir"
        fi
    done

    export PATH
    
    # -------------------------
    # Check dependencies
    # -------------------------
    _check_dependencies needed_deps optional_deps 1  # 1 = exit if required deps missing
   
    # -------------------------
    # LOCAL Variables
    # -------------------------
    local fcmd cmd ctype cmd_path alias_expansion RED GREEN CYAN RESET 
    local depth=${2:-0}
    local cmdline="$*"
    local MAX_DEPTH=8
    local indent=$(printf "%*s" $((depth * 4)) "")
     
    # -------------------------
    # Colors (tput → ANSI → none)
    # -------------------------
    if command -v tput &>/dev/null && [[ $(tput colors 2>/dev/null) -ge 8 ]]; then
        # Preferred: tput colors
        RED=$(tput setaf 1)
        GREEN=$(tput setaf 2)
        CYAN=$(tput setaf 6)
        YELLOW=$(tput setaf 11)
        RESET=$(tput sgr0)
    else
        # Fallback: ANSI escape sequences
        RED='\033[31m'
        GREEN='\033[32m'
        CYAN='\033[36m'
        YELLOW='\033[93m'
        RESET='\033[0m'

        # Verify terminal supports ANSI colors
        if [[ -t 1 ]]; then
            # terminal is interactive — keep ANSI
            :
        else
            # non-interactive — disable colors
            RED=""; GREEN=""; CYAN=""; YELLOW=""; RESET=""
        fi
    fi

    #--------------------------
    # Reset arrays on first run
    #--------------------------
    if (( depth == 0 )); then
        __H_SEEN_ALIASES=()
        __H_SEEN_COMMANDS=()
        __H_SEEN_BUILTINS=()
        __H_SHADOW_SEEN=()
    fi

    # -------------------------
    # Depth guard
    # -------------------------
    (( depth > MAX_DEPTH )) && { 
        printf "%s${RED}Max recursion depth (%d) reached${RESET}\n" "$indent" "$MAX_DEPTH"
        return 1 
    }
    
    # -------------------------
    # Get command or last command
    # -------------------------
    if [[ -n "$1" ]]; then
        read -r -a fcmd <<< "$1"
    else
        read -r -a fcmd <<< "$(fc -ln -1)" || { 
            printf "%s${RED}Failed to get last command${RESET}\n" "$indent"
            return 1
        }
    fi
    cmd="${fcmd[0]}"
    
    #--------------------------
    # Greeting
    #--------------------------
    if (( depth == 0 )); then
        printf "\n├─ h - a buggy BASH Command Analyzer\n"
    fi 
     
    # -------------------------------------------------------------------------
    # handle arguments for h
    # -------------------------------------------------------------------------
    case "$cmd" in
        -h|--help) _h-usage; return 0;;
        -v|--version) _h-ver; return 0;;
        -s|--sourced) _sourced_files; return 0;;
        -a|--alias) printf "\n" && _alias_override; return 0;;
    esac

    # -------------------------------------------------------------------------
    # Split and analyze pipelines, chains, and lists safely
    # -------------------------------------------------------------------------
if [[ "$cmdline" =~ [\|\&\;] ]]; then
    local segments=()
    _split_top_level_commands "$cmdline" segments

    for seg in "${segments[@]}"; do
        # Get individual commands in the segment (e.g., within a pipeline)
        local subcmds=()
        _parse_commands "$seg" subcmds

        for c in "${subcmds[@]}"; do
            printf "%s${CYAN}↳ Subcommand:${RESET} %s\n" "$indent" "$c"
            h "$c" $((depth + 1))
        done
    done
    return 0
fi

    # -------------------------------------------------------------------------
    # Handle h   for some reason h as an argument without - causes problems
    # -------------------------------------------------------------------------
    if [[ "$cmd" == "h" ]]; then
            printf "%s├─ Detected ${CYAN}'%s' ${RESET}\n" "$indent" "$cmd"
            printf "%s    ↳ Showing  ${CYAN}'%s --help'${RESET}:\n\n"  "$indent" "$cmd"
            _h-usage
            export OLD_PATH
            return 0
    fi

    # -------------------------------------------------------------------------
    # Handle "sudo"
    # -------------------------------------------------------------------------
    if [[ "$cmd" == "sudo" ]]; then
        if (( ${#fcmd[@]} == 1 )); then
            printf "%s├─ Detected ${CYAN}'%s' ${RESET}with no command\n" "$indent" "$cmd"
            printf "%s    ↳ Showing  ${CYAN}'%s --help'${RESET}:\n\n"  "$indent" "$cmd"
            sudo --help 2>/dev/null | sed 's/^/    /'
            return 0
        fi

        # Strip 'sudo' and analyze the underlying command
        local next_cmd="${fcmd[@]:1}"
        printf "%s├─ Detected ${CYAN}'sudo'${RESET}, analyzing underlying command:\n" "$indent"
        h "$next_cmd" $((depth + 1)) 
        return 0
    fi
    
    # -------------------------------------------------------------------------
    # Handle keywords and provide help statements
    # -------------------------------------------------------------------------
    if [[ "$cmd" == "if" || "$cmd" == "case" ||  "$cmd" == "for" || "$cmd" == "select" || "$cmd" == "while" || "$cmd" == "until" || "$cmd" == "function" || "$cmd" == "time" || "$cmd" == "{" || "$cmd" == "[[" || "$cmd" == "coproc" ]]; then
    
        printf "%s├─ Detected BASH Keyword ${CYAN}'%s' ${RESET}\n" "$indent" "$cmd"
        printf "%s    ↳ Showing  ${CYAN}'help %s'${RESET}:\n\n" "$indent" "$cmd"
        help "$cmd" 2>/dev/null | sed 's/^/    /'
        return 0
    fi
    
    case "$cmd" in
      then|else|elif|fi|do|done|in|!|])
          description=""
          case "$cmd" in
              then) description="Begins the command block for a true condition." ;;
              else) description="Begins the command block if the condition is false." ;;
              elif) description="Introduces a new condition if the previous one is false." ;;
              fi)   description="Ends an if statement." ;;
              do)   description="Begins the body of a loop or conditional block." ;;
              done) description="Ends the body of a loop or conditional block." ;;
              in)   description="Specifies the list to iterate over in a loop." ;;
              !)    description="Negates the exit status of a command or pipeline." ;;
              ])    description="Ends a conditional test expression." ;;
          esac
          printf "%s├─ Detected BASH Keyword ${CYAN}'%s' ${RESET}\n" "$indent" "$cmd"
          printf "%s    ↳ ${CYAN}%s${RESET} -- %s\n" "$indent" "$cmd" "$description"
          return 0
          ;;
    esac

    if [[ "$cmd" == "esac" ]]; then
        printf "%s├─ Detected BASH Keyword ${CYAN}'%s' ${RESET}\n" "$indent" "$cmd"
           printf "%s    ↳ ${CYAN}%s${RESET} -- Ends a case statement.\n" "$indent" "$cmd"
        return 0
    fi

    if [[ "$cmd" == "}" ]]; then
        printf "%s├─ Detected BASH Keyword ${CYAN}'%s' ${RESET}\n" "$indent" "$cmd"
           printf "%s    ↳ ${CYAN}%s${RESET} -- Ends a group of commands in a block.\n" "$indent" "$cmd"
        return 0
    fi
    
    # -------------------------------------------------------------------------
    # Handle "builtin", "command", "[[", and "wait" 
    # -------------------------------------------------------------------------
    if [[ "$cmd" == "builtin" || "$cmd" == "command" || "$cmd" == "wait" ]]; then    
        if (( ${#fcmd[@]} == 1 )); then
            printf "%s├─ Detected builtin ${CYAN}'%s' ${RESET}with no command\n" "$indent" "$cmd"
            printf "%s    ↳ Showing  ${CYAN}'help %s'${RESET}:\n\n" "$indent" "$cmd"
            help "$cmd" 2>/dev/null | sed 's/^/    /'
            return 0
        fi

        # Strip wrapper and skip any flags (e.g., -n)
        local subcmd=("${fcmd[@]:1}")
        while [[ "${subcmd[0]}" == -* ]]; do
            subcmd=("${subcmd[@]:1}")
        done

        printf "%s├─ Detected ${CYAN}'%s'${RESET} wrapper\n" "$indent" "$cmd"
        printf "%s    ↳ Showing  ${CYAN}'help %s'${RESET}:\n\n" "$indent" "$subcmd"
        help "$subcmd" 2>/dev/null | sed 's/^/    /'
        return 0    
    fi

    # -------------------------------------------------------------------------
    # Detect command type
    # -------------------------------------------------------------------------
    ctype=$(type -t -- "$cmd" 2>/dev/null)
    if [[ -z "$ctype" ]]; then
        printf "%s├─ ${RED}Unknown or invalid command:${CYAN} '%s'${RESET}\n" "$indent" "$cmd"
        printf "%s    ↳ Check your spelling and try again\n\n" "$indent" 
        return 1
    fi

    # -------------------------------------------------------------------------
    # Builtins
    # -------------------------------------------------------------------------
    if [[ "$ctype" == "builtin" ]]; then
        printf "%s├─ ${CYAN}'%s'${RESET} is a shell builtin\n" "$indent" "$cmd"

        # Determine whether builtin is enabled or disabled
        local enabled_state="enabled"
        if enable -p 2>/dev/null | grep -qE "^-n ${cmd}\b"; then
            enabled_state="disabled"
        fi

        # Determine whether builtin is a loadable one (from a .so)
        local origin="Core Bash builtin"
        local builtin_info
        builtin_info="$(enable -p "$cmd" 2>/dev/null)"
        if [[ "$builtin_info" =~ -f[[:space:]]+([^[:space:]]+) ]]; then
            origin="Loadable builtin → ${BASH_REMATCH[1]}"
        fi

        # ── Check if the builtin is shadowed by something else 
        local shadow_msg=""
        local shadow_type=""

        if alias "$cmd" &>/dev/null; then
            shadow_type="alias"
        elif declare -F "$cmd" &>/dev/null; then
            shadow_type="function"
        elif type -P "$cmd" &>/dev/null; then
            shadow_type="PATH command → $(type -P "$cmd")"
        fi

        if [[ -n "$shadow_type" ]]; then
            shadow_msg="${YELLOW}Shadowed by: ${shadow_type}${RESET}"
        fi
        
    # Shadow detection
    if [[ -n "$shadow_type" ]]; then
        shadow_msg="${YELLOW}Shadowed by: ${shadow_type}${RESET}"
        printf "%s    ↳ ${shadow_msg}\n" "$indent"

        # If shadowed by PATH command, follow it
        if [[ "$shadow_type" == PATH* ]]; then
            local shadow_cmd="${shadow_type#PATH command → }"
            printf "%s    ↳ Analyzing shadowing executable: ${CYAN}%s${RESET}\n" "$indent" "$shadow_cmd"
            h "$shadow_cmd" $((depth + 1))
            return 0
        fi
        
        # If shadowed by alias command, follow it
        if [[ "$shadow_type" == alias* ]]; then
            local shadow_cmd="${shadow_type#PATH command → }"
            alias_def=$(alias "$cmd")
            printf "%s    ↳ Showing shadowing alias: ${CYAN}%s${RESET}\n" "$indent" "$alias_def"
            return 0
        fi
        
        # If shadowed by Function command, follow it
        if [[ "$shadow_type" == function* ]]; then
            local shadow_cmd="${shadow_type#PATH command → }"
            printf "%s    ↳ Showing function: ${CYAN}%s${RESET}\n\n"  "$indent" "$shadow_cmd"        
            func_content=$(declare -f "$shadow_cmd")
            _highlight_script "$func_content" "$indent"
            printf "\n%s    ─── End of function '%s' ───\n" "$indent" "$shadow_cmd"
            return 0
        fi
    fi
    
        printf "%s    ↳ Status: Shell builtin is ${CYAN}%s${RESET}\n" "$indent" "$enabled_state"
        printf "%s    ↳ Source:  ${CYAN}%s${RESET}\n" "$indent" "$origin"
        [[ -n "$shadow_msg" ]] && printf "%s    ↳ ${shadow_msg}\n" "$indent"
        printf "%s    ↳ Showing ${CYAN}'help %s'${RESET}:\n\n" "$indent" "$cmd"
        help "$cmd" 2>/dev/null | sed "s/^/$indent        /"
        return 0
    fi
        
    # -------------------------------------------------------------------------
    # Functions
    # -------------------------------------------------------------------------
    if [[ "$ctype" == "function" ]]; then
        #--------------------------- 
        # Enable extdebug temporarily so declare -F includes file/line info
        local was_extdebug=0
        shopt -q extdebug && was_extdebug=1 || shopt -s extdebug
        
        read _ line file <<< "$(declare -F "$cmd")"

        # Restore extdebug state if we enabled it
        (( !was_extdebug )) && shopt -u extdebug
        #---------------------------- 
        
        printf "%s├─ ${CYAN}'%s' ${RESET}is a shell function\n" "$indent" "$cmd"
        printf "%s    ↳ Declared in: ${CYAN}%s (line %s)${RESET}\n" "$indent" "$file" "$line"
        printf "%s    ↳ Showing function: ${CYAN}%s${RESET}\n\n"  "$indent" "$cmd"        
        func_content=$(declare -f "$cmd")
        _highlight_script "$func_content" "$indent"
        printf "\n%s    ─── End of function '%s' ───\n" "$indent" "$cmd"
        
        return 0
    fi
    
    # -------------------------------------------------------------------------
    # Aliases
    # -------------------------------------------------------------------------
    if [[ "$ctype" == "alias" ]]; then
        # Avoid re-analyzing the same alias (prevents infinite loops)
        [[ -n "${__H_SEEN_ALIASES[$cmd]}" ]] && return 0
        __H_SEEN_ALIASES["$cmd"]=1
    
        # Get the alias expansion (strip surrounding quotes)
        raw=$(alias "$cmd")
        alias_expansion=${raw#*=}         # remove 'name='
        alias_expansion=${alias_expansion:1:${#alias_expansion}-2}   # trim outer quotes

        # Example usage in your alias handling:
        alias_expansion=$(_expand_vars "$alias_expansion")
  
        alias_def=$(alias "$cmd")
        printf "%s├─ ${CYAN}'%s' ${RESET}is an alias → resolves to: ${CYAN}%s${RESET}\n" "$indent" "$cmd" "$alias_def"
    
        # Show where alias is defined
        local found_location=""
        local line_number
        
        _sourcedtree
    
        # Enable nullglob temporarily 
        local nullglob_was=0
        shopt -q nullglob && nullglob_was=1 || shopt -s nullglob      
            for file in ${SOURCED_FILES_LIST[@]}; do 
                 [[ -r "$file" ]] || continue
                if grep -qE "^[[:space:]]*alias[[:space:]]+${cmd}=" "$file"; then
                    found_location="$file"
                    line_number=$(grep -nE "^[[:space:]]*alias[[:space:]]+$cmd=" "$file" | head -n1 | cut -d: -f1)
                    printf "%s    ↳ Defined in: ${CYAN}%s (line %s)${RESET}\n" "$indent" "$file" "$line_number"
                    break
                fi
            done  
        # Restore nullglob state if we enabled it
        (( !nullglob_was )) && shopt -u nullglob
        
        if [[ -z "$found_location" ]]; then
            printf "%s    ↳ Defined manually in shell, or a custom location.\n" "$indent"
        fi     
    
        # ------------------------------------------------------------------------- 
        # Handle alias expansion with chain/pipeline parsing
        # ------------------------------------------------------------------------- 
        if [[ "$alias_expansion" =~ [\|\&\;] ]]; then
            local segments=()
            _split_top_level_commands "$alias_expansion" segments

            for seg in "${segments[@]}"; do
                local subcmds=()
                _parse_commands "$seg" subcmds

                for c in "${subcmds[@]}"; do
                    printf "%s${CYAN}↳ Alias subcommand:${RESET} %s\n" "$indent" "$c"
                    h "$c" $((depth + 1))
                done
            done
            return 0
        else
            # No chain/pipeline — analyze normally
            h "$alias_expansion" $((depth + 1))
            return 0
        fi
    fi

    # -------------------------------------------------------------------------
    # External commands
    # -------------------------------------------------------------------------
    cmd_path=$(command -v "$cmd") || { 
        printf "%s${RED}Command not found: '%s'${RESET}\n" "$indent" "$cmd"
        return 1
    }
    local cmd_real formatted_type             
    cmd_real=$(readlink -f "$cmd_path" 2>/dev/null || echo "$cmd_path")
    file_info=$(file -b "$cmd_real")

    # Replace commas with newlines for clarity, but keep first line aligned
    formatted_type=$(printf "%s" "$file_info" | sed -E $'s/, interpreter/\\\ninterpreter/g; s/, version/\\\nversion/g; s/, dynamically/\\\ndynamically/g; s/, BuildID/\\\nBuildID/g; s/, for /\\\nfor /g')
        
    printf "%s├─ ${CYAN}'%s' ${RESET}is an external command\n" "$indent" "$cmd"
    printf "%s    ↳ Path: ${CYAN}%s${RESET}\n" "$indent" "$cmd_path"    
  
    if [[ "$cmd_real" != "$cmd_path" ]]; then # && \
        printf "%s    ↳ ${YELLOW}Symbolic link ${RESET}to: ${YELLOW}%s${RESET}\n" "$indent" "$cmd_real"
        cmd_path="$cmd_real"
    fi

    # -------------------------- 
    # Root privilege detection (enhanced)
    # --------------------------- 
    local requires_root=0
    local root_reason=""

    # 1. Check for setuid root
    if [[ -u "$cmd_path" && $(stat -c '%U' "$cmd_path" 2>/dev/null) == "root" ]]; then
        requires_root=1
        root_reason="setuid root binary"

    # 2. Check capabilities with getcap
    elif command -v getcap &>/dev/null; then
        local caps
        caps=$(getcap "$cmd_path" 2>/dev/null)
        if [[ "$caps" =~ (cap_sys_admin|cap_dac_override|cap_net_admin) ]]; then
            requires_root=1
            root_reason="binary grants elevated capabilities (getcap)"
        fi
    fi

    # 3. Check capabilities using capsh if available
    if (( !requires_root )) && command -v capsh &>/dev/null; then(
        local cap_info
        cap_info=$(capsh --print 2>/dev/null)
    
        if [[ "$cap_info" =~ "Current:" ]]; then
            if [[ "$cap_info" =~ (cap_sys_admin|cap_dac_override|cap_net_admin) ]]; then
                requires_root=1
                root_reason="binary requires elevated capabilities (capsh)"
            fi
        fi)
    fi

    # 4. Known admin commands
    if (( !requires_root )); then
        local root_cmds=(mount umount reboot shutdown halt ifconfig iptables ip systemctl journalctl modprobe insmod rmmod fdisk mkfs losetup parted fsck useradd userdel passwd chown chmod service sysctl visudo fstrim swapoff swapon update-grub grub-installg disk parted blkid update-initramfs)
        for rcmd in "${root_cmds[@]}"; do
            if [[ "$cmd" == "$rcmd" ]]; then
                requires_root=1
                root_reason="known administrative command"
                break
            fi
        done
    fi

    # 5. Display result
    if (( requires_root )); then
        printf "%s    ↳ Requires root privileges: ${YELLOW}Yes (%s)${RESET}\n" "$indent" "$root_reason"
    else
        printf "%s    ↳ Requires root privileges: ${CYAN}No${RESET}\n" "$indent"
    fi

    # -------------------------- 
    # Detect Executable Type
    # -------------------------- 
    if grep -qi 'ELF' <<< "$file_info"; then
        if grep -qi 'statically linked' <<< "$file_info"; then
            printf "%s    ↳ Executable Type: ELF binary - Statically linked\n" "$indent"
            while IFS= read -r line; do
                printf "%s    ⎟    ${CYAN}%s${RESET}\n" "$indent" "$line"
            done <<< "$formatted_type"
        else
            printf "%s    ↳ Executable Type: ELF binary - Dynamically linked\n" "$indent"  
            while IFS= read -r line; do
                printf "%s    ⎟    ${CYAN}%s${RESET}\n" "$indent" "$line"
            done <<< "$formatted_type"  
        fi

    elif grep -qi 'script\|text executable' <<< "$file_info"; then
        printf "%s    ↳ Executable Type: ${CYAN}Script / Text executable${RESET}\n" "$indent"

        local first_line interp
        first_line=$(head -n1 "$cmd_path")

        if [[ "$first_line" =~ ^#! ]]; then
            interp="${first_line#\#!}"        # remove shebang (#!)
            interp="${interp#"${interp%%[![:space:]]*}"}"  # trim leading whitespace
            while IFS= read -r line; do
                printf "%s    ⎟    ${CYAN}%s${RESET}\n" "$indent" "$line"
            done <<< "$formatted_type"       
        fi
        printf "%s    ↳ Interpreter: ${CYAN}%s${RESET}\n" "$indent" "$interp"
    else
        printf "%s    ↳ Executable Type: Unknown\n" "$indent"
        while IFS= read -r line; do
            printf "%s    ⎟    ${CYAN}%s${RESET}\n" "$indent" "$line"
        done <<< "$formatted_type"
        printf "%s    ⎟       ${YELLOW}%s${RESET}\n" "$indent" "$file_info"
    fi
   
    # Get dependencies
    deps=$(ldd "$cmd_path" 2>/dev/null | sed 's/^[[:space:]]*//')
    if [[ -n "$deps" ]]; then
        printf "%s    ↳ Dependencies:\n" "$indent" 
        local unmet_found=0
        while IFS= read -r line; do
            if [[ "$line" =~ "not found" ]]; then
                unmet_found=1
                printf "%s    ⎟    ${RED}%s${RESET}\n" "$indent" "$line"
            else
                printf "%s    ⎟    ${CYAN}%s${RESET}\n" "$indent" "$line"
            fi
        done <<< "$deps"
        (( unmet_found )) && printf "%s        ↳ ${RED}⚠ Some dependencies are missing!${RESET}\n" "$indent"
    fi

    # Package info---------------
    local pkg_name
    local pkg_lines=()

    if command -v dpkg &>/dev/null; then
        pkg_name=$(dpkg -S "$cmd_path" 2>/dev/null | cut -d: -f1 | head -n1)

        if [[ -n "$pkg_name" ]]; then
            # Read apt show output line by line
            while IFS= read -r line; do
                pkg_lines+=("$line")
            done < <(apt show "$pkg_name" 2>/dev/null | grep -E 'Package:|Version:|Maintainer:|Description:'| awk -F': ' '{print $2}')

            # Display each line with custom formatting
            printf "%s    ↳ Package Info:\n" "$indent"
            printf "%s    ⎟    Package: ${CYAN}%s${RESET}\n" "$indent" "${pkg_lines[0]}"
            printf "%s    ⎟    Version: ${CYAN}%s${RESET}\n" "$indent" "${pkg_lines[1]}"
            printf "%s    ⎟    Maintainer: ${CYAN}%s${RESET}\n" "$indent" "${pkg_lines[2]}"
            printf "%s    ⎟    Description: ${CYAN}%s${RESET}\n" "$indent" "${pkg_lines[3]}"
        fi
    fi

    # -----------------------------
    # Get and display file permissions
    if [[ -x "$cmd_path" ]]; then
        local perms octal owner_grp owner_perm group_perm other_perm
        # Using stat for symbolic and octal
        if stat --version &>/dev/null; then
            perms=$(stat -c "%A" "$cmd_path")      # symbolic: -rwxr-xr-x
            octal=$(stat -c "%a" "$cmd_path")      # numeric: 755
            owner_grp=$(stat -c "%U:%G" "$cmd_path") # owner:group
            owner_perm=$(stat -c "%A" "$cmd_path" | cut -c2-4)   # owner bits
            group_perm=$(stat -c "%A" "$cmd_path" | cut -c5-7)   # group bits
            other_perm=$(stat -c "%A" "$cmd_path" | cut -c8-10)  # others bits
        fi

        printf "%s    ↳ Permissions: ${CYAN}%s${RESET} (octal: ${CYAN}%s${RESET})\n" "$indent" "$perms" "$octal"
        printf "%s    ⎟    ↳ Owner/Group: ${CYAN}%s${RESET}\n" "$indent" "$owner_grp"
        printf "%s    ⎟    ↳ Owner: ${CYAN}%s${RESET}  Group: ${CYAN}%s${RESET}  Others: ${CYAN}%s${RESET}\n" \
            "$indent" "$owner_perm" "$group_perm" "$other_perm"
    fi
   
    # Show script contents if text executable
    if [[ "$file_info" == *"script"* || "$file_info" == *"text executable"* ]]; then
        printf "%s    ↳ Showing script ${CYAN}'%s'${RESET}:\n\n" "$indent" "$cmd"
        if [[ -r "$cmd_path" ]]; then
            _highlight_script "$cmd_path" "$indent"
            printf "\n\n%s    ─── End of script '%s' ───\n" "$indent" "$cmd"
        fi
        return 0
    fi
    
    # Show help for external commands 
    local output cmdname hout
    local output_shown=0 
    local help_flags=(--help -help -h "-?") 
    cmdname=$(basename "$cmd_path")

    for flag in "${help_flags[@]}"; do
        if output=$("$cmd_path" "$flag" 2>&1); then
            if [[ -n "$output" ]]; then
                printf "%s    ↳ Showing${CYAN} '%s %s'${RESET}:\n\n" "$indent" "$cmdname" "$flag"
                printf "%s\n\n" "$output" | sed "s/^/$indent         /"
                output_shown=1
                break
            fi
        fi
    done

    # Fallback if no help output was shown
    if (( output_shown == 0 )); then
        local cmd_only="${cmd_path##*/}"
        if command -v man &>/dev/null && man -w "$cmd_only" &>/dev/null; then
            printf "%s    ↳ Manual available via${CYAN} 'man %s'${RESET}\n" "$indent" "$cmd_only"
        elif command -v info &>/dev/null && info "$cmd_only" &>/dev/null; then
            printf "%s    ↳ Info page available via ${CYAN}'info %s'${RESET}\n" "$indent" "$cmd_only"
        else 
            hout=$(help "$cmd" 2>&1)
            # Filter known useless help output
            if [[ -n "$hout" && ! "$hout" =~ [Nn]o[[:space:]]help[[:space:]]topics ]]; then
                printf "%s    ↳ Showing${CYAN} 'help %s'${RESET}:\n\n" "$indent" "$cmd"
                printf "%s\n" "$hout" | sed "s/^/$indent        /"
            else
                printf "%s    ↳ ${RED}No help found for ${CYAN}'%s'${RESET}\n" "$indent" "$cmd_only"
            fi
        fi
    fi

    local next_cmd=("${fcmd[@]:1}")
    if (( ${#next_cmd[@]} > 0 )); then
        h "${next_cmd[*]}" $((depth + 1))
    fi
    
    export OLD_PATH 
    return 0
}

#-------------------------------------------------------------------------------------------------
# Function: _h-usage
# Purpose : Display usage information and examples for the `h` command analyzer.
#-------------------------------------------------------------------------------------------------
_h-usage() {
    cat <<'EOF'

     Usage:  h [command]  
     Analyzes a Bash command, alias, builtin, function, or binary — showing where it’s defined,
     how it expands, and what help or documentation is available.

     Options:
           -h --help or h      Show this help text.
           -v --version        Show version information.
           -s --sourced        List all sourced files in the enviroment.
           -a --alias          List all aliases that override a command in the enviroment.

     Examples:
           h                   # Automatically analyzes your most recent command
           h sudo              # Analyzes 'sudo'
           h 'sudo rm'         # Ignores sudo and Analyzes rm
           h ls                # Is 'ls' a builtin, alias, function, script or binary?

     Features:
       • Handles builtins, aliases, functions, and external commands
       • Detects symbolic links and script file contents
       • Shows --help/-h/-? output or points to man/info pages
  
EOF
}

#-------------------------------------------------------------------------------------------------
# Function: _h-ver
# Purpose : Display version information.
#-------------------------------------------------------------------------------------------------
_h-ver() {
    cat <<'EOF'

     h v3.0.9 — Bash Command Analyzer
     Author : John Blair
     Updated: 2025-11-10  
          
     MIT License

     Copyright (c) 2025 John Blair

     Permission is hereby granted, free of charge, to any person obtaining a copy
     of this software and associated documentation files (the "Software"), to deal
     in the Software without restriction, including without limitation the rights
     to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
     copies of the Software, and to permit persons to whom the Software is
     furnished to do so, subject to the following conditions:

     The above copyright notice and this permission notice shall be included in all
     copies or substantial portions of the Software.

     THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
     IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
     FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
     AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
     LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
     OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
     SOFTWARE.

EOF
}

#--------------------------------------------------------------------------------------------
# Function: _split_top_level_commands
# Purpose : Split a Bash command line into top-level segments
#--------------------------------------------------------------------------------------------
_split_top_level_commands() {
    local input="$1"
    local -n out="$2"
    out=()

    local len=${#input}
    local i=0
    local seg=""
    local c next

    # parser state
    local in_single=0 in_double=0 in_backtick=0
    local paren=0 brace=0 bracket=0
    local dollar_paren=0

    while (( i < len )); do
        c="${input:i:1}"
        next="${input:i+1:1}"

        # -----------------------------
        # QUOTE HANDLING
        # -----------------------------
        if (( in_single )); then
            seg+="$c"
            [[ $c == "'" ]] && in_single=0
            ((i++)); continue
        fi
        if (( in_double )); then
            seg+="$c"
            [[ $c == '"' ]] && in_double=0
            ((i++)); continue
        fi
        if (( in_backtick )); then
            seg+="$c"
            [[ $c == '`' ]] && in_backtick=0
            ((i++)); continue
        fi

        # Enter quotes
        case "$c" in
            "'") in_single=1; seg+="$c"; ((i++)); continue;;
            '"') in_double=1; seg+="$c"; ((i++)); continue;;
            '`') in_backtick=1; seg+="$c"; ((i++)); continue;;
        esac

        # -----------------------------
        # STRUCTURAL NESTING
        # -----------------------------
        case "$c" in
            '(')
                seg+="$c"
                ((paren++))
                # detect $(...)
                if [[ "${input:i-1:1}" == '$' ]]; then ((dollar_paren++)); fi
                ((i++)); continue;;
            ')')
                seg+="$c"
                if (( dollar_paren > 0 )); then
                    ((dollar_paren--))
                else
                    ((paren--))
                fi
                ((i++)); continue;;

            '{') seg+="$c"; ((brace++)); ((i++)); continue;;
            '}') seg+="$c"; ((brace--)); ((i++)); continue;;
            '[') seg+="$c"; ((bracket++)); ((i++)); continue;;
            ']') seg+="$c"; ((bracket--)); ((i++)); continue;;
        esac

        # -----------------------------
        # TOP LEVEL OPERATORS
        # -----------------------------
        # Only if ALL nesting == 0
        if (( paren==0 && brace==0 && bracket==0 && !in_single && !in_double && !in_backtick )); then

            # Operators: || && | ; &
            # Identify multi-char first
            if [[ "$c$next" =~ ^(\|\||&&|;;|;&)$ ]]; then
                out+=("$(printf '%s' "$seg" | sed 's/[[:space:]]*$//')")
                out+=("$c$next")
                seg=""
                ((i+=2)); continue
            fi

            case "$c" in
                '|'|';'|'&')
                    out+=("$(printf '%s' "$seg" | sed 's/[[:space:]]*$//')")
                    out+=("$c")
                    seg=""
                    ((i++)); continue;;
            esac
        fi

        # default → accumulate
        seg+="$c"
        ((i++))
    done

    # final segment
    if [[ -n "$seg" ]]; then
        out+=("$(printf '%s' "$seg" | sed 's/[[:space:]]*$//')")
    fi
}


#--------------------------------------------------------------------------------------------
# Function: _parse_commands
# Purpose : Parse a Bash segment into actual executed commands using DEBUG trap
#--------------------------------------------------------------------------------------------
_parse_commands() {
    local cmdline="$1"
    local depth="${2:-0}"
    [[ -z "$cmdline" ]] && return

    local segments=()
    local seg=""
    local in_single=0 in_double=0
    local in_backtick=0
    local paren=0 brace=0 bracket=0
    local i=0 len=${#cmdline} c next

    # First, split by top-level operators
    while (( i < len )); do
        c="${cmdline:i:1}"
        next="${cmdline:i+1:1}"

        # Handle quotes
        if (( in_single )); then
            seg+="$c"
            [[ $c == "'" ]] && in_single=0
            ((i++)); continue
        fi
        if (( in_double )); then
            seg+="$c"
            [[ $c == '"' ]] && in_double=0
            ((i++)); continue
        fi
        if (( in_backtick )); then
            seg+="$c"
            [[ $c == '`' ]] && in_backtick=0
            ((i++)); continue
        fi

        case "$c" in
            "'") in_single=1; seg+="$c"; ((i++)); continue;;
            '"') in_double=1; seg+="$c"; ((i++)); continue;;
            '`') in_backtick=1; seg+="$c"; ((i++)); continue;;
            '(') ((paren++)); seg+="$c"; ((i++)); continue;;
            ')') ((paren--)); seg+="$c"; ((i++)); continue;;
            '{') ((brace++)); seg+="$c"; ((i++)); continue;;
            '}') ((brace--)); seg+="$c"; ((i++)); continue;;
            '[') ((bracket++)); seg+="$c"; ((i++)); continue;;
            ']') ((bracket--)); seg+="$c"; ((i++)); continue;;
        esac

        # Top-level operator detection
        if (( paren==0 && brace==0 && bracket==0 && !in_single && !in_double && !in_backtick )); then
            if [[ "$c$next" =~ ^(\|\||&&)$ ]]; then
                [[ -n "$seg" ]] && segments+=("$seg")
                segments+=("$c$next")
                seg=""
                ((i+=2))
                continue
            fi
            case "$c" in
                '|'|';'|'&')
                    [[ -n "$seg" ]] && segments+=("$seg")
                    segments+=("$c")
                    seg=""
                    ((i++))
                    continue;;
            esac
        fi

        # default → accumulate
        seg+="$c"
        ((i++))
    done
    [[ -n "$seg" ]] && segments+=("$seg")

    # Now analyze each segment
    for s in "${segments[@]}"; do
        # Operators
        if [[ "$s" =~ ^(\|\||&&|;|\|)$ ]]; then
            printf "Operator detected: %s\n" "$s"
            continue
        fi

        # Split segment into command + args respecting quotes
        local tokens=()
        local token=""
        local in_single=0 in_double=0
        local j=0 len_seg=${#s} char
        while (( j < len_seg )); do
            char="${s:j:1}"

            if (( in_single )); then
                token+="$char"
                [[ $char == "'" ]] && in_single=0
                ((j++)); continue
            fi
            if (( in_double )); then
                token+="$char"
                [[ $char == '"' ]] && in_double=0
                ((j++)); continue
            fi

            case "$char" in
                "'") in_single=1; token+="$char";;
                '"') in_double=1; token+="$char";;
                ' ')
                    if [[ -n "$token" ]]; then
                        tokens+=("$token")
                        token=""
                    fi
                    ;;
                *) token+="$char";;
            esac
            ((j++))
        done
        [[ -n "$token" ]] && tokens+=("$token")

        # Analyze first token as command
        local cmd_name="${tokens[0]}"
        local args=("${tokens[@]:1}")

        # Skip already seen
        if [[ -n "${__H_SEEN_ALIASES[$cmd_name]}" ]]; then
            printf "Alias/function %s already analyzed, skipping recursion.\n" "$cmd_name"
            continue
        fi

        if alias "$cmd_name" &>/dev/null; then
            __H_SEEN_ALIASES[$cmd_name]=1
            local expansion
            expansion=$(alias "$cmd_name" | sed -E "s/^alias $cmd_name='(.*)'$/\1/")
            printf "Alias %s → %s\n" "$cmd_name" "$expansion"
            _parse_commands "$expansion" $((depth+1))
        elif declare -f "$cmd_name" &>/dev/null; then
            __H_SEEN_ALIASES[$cmd_name]=1
            printf "Function %s detected\n" "$cmd_name"
            local func_body
            func_body=$(declare -f "$cmd_name" | tail -n +2)
            _parse_commands "$func_body" $((depth+1))
        else
            printf "Command %s detected with arguments: %s\n" "$cmd_name" "${args[*]}"
        fi
    done
}

#-------------------------------------------------------------------------------------------------
# Function: _h_completion
# Purpose : Enable tab completion for entering commands 
#-------------------------------------------------------------------------------------------------
_h_completion() {
    local cur="${COMP_WORDS[COMP_CWORD]}"
    local commands

    # Collect possible completions: aliases, functions, builtins, and executables in PATH
    commands=$(compgen -A function -A alias -A builtin -A command -- "$cur")

    # Return completions
    COMPREPLY=( $(compgen -W "$commands" -- "$cur") )
}

#-------------------------------------------------------------------------------------------------
# Function: _expand_vars
# Purpose : expand variables for aliases so $HOME resolves to /home/jb/ 
#-------------------------------------------------------------------------------------------------
_expand_vars() {
    local input="$1"
    # Only expand variables like $HOME, $USER, etc. Use 'echo' with eval but in quotes to avoid execution.
    # This does NOT run any commands; it only expands env vars.
    printf '%s' "$(eval "echo \"$input\"")"
}

#-------------------------------------------------------------------------------------------------
# Funtion: _sourcedtree
# Purpose: Recursively scan .bashrc for ALL files that are sourced. including conditionals.
#-------------------------------------------------------------------------------------------------
# Global array to track all sourced files
declare -gA SOURCED_FILES_MAP=()   # associative array to avoid duplicates
declare -ga SOURCED_FILES_LIST=()  # optional list preserving order

_sourcedtree() {
    SOURCED_FILES_MAP=()
    SOURCED_FILES_LIST=()
    local file files depth max_depth
    depth=0
    max_depth="${MAX_DEPTH:-5}"
    
    shopt -s nullglob
    files=(~/.bash_profile ~/.bash_login ~/.profile ~/.bashrc ~/.bash_aliases ~/.bash_functions ~/.bash_exports ~/.bash_local ~/.local/share/bash-completion/bash_completion /etc/profile /etc/bash.bashrc /etc/bashrc /etc/profile.d/ /usr/share/bash-completion/bash_completion ~/.config/bash/bashrc ~/.bash/bashrc ~/.dotfiles/bash/ ~/.dotfiles/bashrc ~/.oh-my-bash/oh-my-bash.sh ~/.bash_it/bash_it.sh ~/.conda/etc/profile.d/conda.sh ~/.config ~/.bash_env /etc/profile.d/*.sh $BASH_ENV) 
    shopt -u nullglob

    for file in "${files[@]}"; do
        _sourcedtree_single "$file" "$depth" "$max_depth"
    done
}

    #-----------------------------------------------------------
    # Internal recursive function
_sourcedtree_single() {
    local file="${1:-$HOME/.bashrc}"
    local depth="${2:-0}"
    local max_depth="${3:-5}"
    local indent="" i expanded src

    (( depth > max_depth )) && return

    for ((i = 0; i < depth; i++)); do indent+="  "; done

    expanded=$(_expand_path "$file")

    # Skip duplicates
    [[ -n ${SOURCED_FILES_MAP["$expanded"]} ]] && return

    # count “garbage” paths
    if [[ ! -f "$expanded" || "$expanded" =~ [\$\*\?\[\]] ]]; then
        return
    fi
    
    SOURCED_FILES_MAP["$expanded"]=1
    SOURCED_FILES_LIST+=("$expanded")

    # Extract sourced files
    while IFS= read -r src; do
        [[ $src =~ ^[[:space:]]*# ]] && continue
        [[ $src =~ (^|[[:space:]])(source|\.)[[:space:]]+([^#;]+) ]] || continue
        src="${BASH_REMATCH[3]}"
        src=$(_expand_path "$src")
        _sourcedtree_single "$src" $((depth + 1)) "$max_depth"
    done < <(grep -E '^\s*(\.|source)\s+' "$expanded" 2>/dev/null)
}

    #-----------------------------------------------------------
    # Helper: _expand_path
_expand_path() {
    local p="$1"

    # Strip quotes and whitespace
    p="${p//\"/}"
    p="${p//\'/}"
    p="${p//;/}"
    p="${p##*( )}"
    p="${p%%*( )}"

    # Tilde expansion
    [[ "$p" == "~"* ]] && p="${p/#\~/$HOME}"

    # Replace known environment variables
    [[ -n "$HOME" ]] && p="${p//\$HOME/$HOME}"
    [[ -n "$BASH_ENV" ]] && p="${p//\$BASH_ENV/$BASH_ENV}"

    # Convert to absolute path if possible
    if command -v realpath &>/dev/null && [[ -e "$p" ]]; then
        p=$(realpath "$p" 2>/dev/null || echo "$p")
    fi

    printf '%s\n' "$p"
}

#-------------------------------------------------------------------------------------------------
# Funtion: _sourced_files
# Purpose: display ALL files that are sourced.
#-------------------------------------------------------------------------------------------------
_sourced_files() {
    local garbage_count=${#garbage[@]}
    _sourcedtree
    
    printf "%s├─ Searching for files that have been sourced into the enviroment automatically\n" 
    printf "    ↳ Discovered the following files:\n\n" 
    for file in "${SOURCED_FILES_LIST[@]}"; do
        printf "      ${CYAN}%s${RESET}\n" "$file" 
    done
    printf "\n      End of list\n"
}        

#-------------------------------------------------------------------------------------------------
# Function: _highlight_script   # _highlight_script "$path-to-script" "<blankspaces>"
# Purpose: Modular syntax highlighting with indentation based on string in $2
#
#        how to add patterns and colors for highlighting
#
#  Example usage: To highlight the word forecast in bright magenta:
#  HIGHLIGHT_PATTERNS+=('\bforecast\b') # your_regex_here
#  HIGHLIGHT_COLORS+=('\x1b[95m')   # bright magenta - choose the ANSI color
#
#  Make sure that HIGHLIGHT_PATTERNS and HIGHLIGHT_COLORS remain aligned by index 
#  (pattern i uses color i).
#
#  Multiple new entries: Highlight 'forecast' and 'radar'
#  HIGHLIGHT_PATTERNS+=('\bforecast\b' '\bradar\b')
#  HIGHLIGHT_COLORS+=('\x1b[95m' '\x1b[92m')  # bright magenta & bright green
#-------------------------------------------------------------------------------------------------
_highlight_script() {
    local input="$1"
    local indent="${2:-}"
    local highlighted
    local use_color=1
    local esc=$'\x1b'

    # Disable color if stdout is not a terminal
    [[ ! -t 1 ]] && use_color=0

    # Read content safely
    if [[ -f "$input" ]]; then
        highlighted=$(<"$input")
    else
        highlighted="$input"
    fi

    # Highlight patterns
    declare -a HIGHLIGHT_PATTERNS=(
        '#.*'                                 # comments
        '"[^"]*"|'\''[^'\'']*'\'''            # strings
        '\$\([^)]*\)|`[^`]*`'                 # subshells
        '\b(local|declare|export|typeset)\b'  # declarations
        '\b(if|then|else|elif|fi|for|while|do|done|until|select|case|esac|break|continue)\b' # control
        '\b(function|return|exit|trap|shift|read|mapfile|set|unset)\b' # builtins
        '\b(printf|echo|mkdir|find|grep|sed|awk|cut|sort|head|tail|xargs|cat|touch|chmod|chown|curl|wget)\b' # commands
        '\$[A-Za-z0-9_@#*!?_-]+'             # variables
        '\b[0-9]+(\.[0-9]+)?\b|0x[0-9A-Fa-f]+' # numbers
        '\[\[|\]\]|\(\(|\)\)|\(|\)|\{|\}'     # brackets/parens/braces
    )

    declare -a HIGHLIGHT_COLORS=(
        "${esc}[36m"    # comments — cyan
        "${esc}[96m"    # strings — bright cyan
        "${esc}[35m"    # subshells — magenta
        "${esc}[32m"    # declarations — green
        "${esc}[93m"    # control — yellow
        "${esc}[32m"    # builtins — green
        "${esc}[35m"    # common commands — magenta
        "${esc}[33m"    # variables — yellow
        "${esc}[34m"    # numbers — blue
        "${esc}[37m"    # brackets — gray
    )

    # Disable colors if needed
    if (( use_color == 0 )); then
        for i in "${!HIGHLIGHT_COLORS[@]}"; do
            HIGHLIGHT_COLORS[$i]=""
        done
    fi

    # Apply highlighting patterns
    for i in "${!HIGHLIGHT_PATTERNS[@]}"; do
        local pattern="${HIGHLIGHT_PATTERNS[$i]}"
        local color="${HIGHLIGHT_COLORS[$i]}"
        highlighted=$(echo "$highlighted" | sed -E "s/($pattern)/${color}\1${esc}[0m/g")
    done

    # Print with indentation
    printf "%s\n" "$highlighted" | sed "s/^/$indent    /"
}

#-------------------------------------------------------------------------------------------------
# Function: _check_dependencies
# Purpose : Check core and optional dependencies, warn user, optionally exit on missing core deps
# Usage   : _check_dependencies needed_deps optional_deps [EXIT_ON_MISSING] 0or1
#-------------------------------------------------------------------------------------------------

needed_deps=(grep sed head readlink file ldd getcap)
optional_deps=(dpkg apt find realpath cut awk xargs tput)

_check_dependencies() {
    local -n required="$1"    # Required dependencies (pass by name)
    local -n optional="$2"    # Optional dependencies (pass by name)
    local exit_on_missing="${3:-1}"  # Default: exit if required deps missing

    local missing_required=()
    local missing_optional=()

    # Check required dependencies
    for cmd in "${required[@]}"; do
        command -v "$cmd" >/dev/null 2>&1 || missing_required+=("$cmd")
    done

    # Check optional dependencies
    for cmd in "${optional[@]}"; do
        command -v "$cmd" >/dev/null 2>&1 || missing_optional+=("$cmd")
    done

    # Report missing required deps
    if (( ${#missing_required[@]} )); then
        printf "${RED}Warning: Missing REQUIRED tools: %s${RESET}\n" "${missing_required[*]}"
        printf "Some core features of the script may not work!\n"
        (( exit_on_missing )) && { printf "Exiting due to missing core dependencies.\n"; exit 1; }
    fi

    # Report missing optional deps
    if (( ${#missing_optional[@]} )); then
        printf "${YELLOW}Note: Missing optional tools: %s${RESET}\n" "${missing_optional[*]}"
        printf "Some optional features may not work.\n"
    fi

}

#-------------------------------------------------------------------------------------------------
# Function: _alias_override                         
# Purpose : list all aliases that override a command
#-------------------------------------------------------------------------------------------------
_alias_override() { 
    local alias cmd list kind 
    local message=()
    local flag=0
    
    printf "%s├─ Examining aliases:\n" "$indent"
    
    while IFS= read -r; do
        alias=${REPLY#alias }
        cmd=${alias%%=*}

        mapfile -t list < <(type -at "$cmd")
    
        for kind in "${list[@]}"; do
            case "$kind" in
	        builtin)
                printf "%s    ↳ Alias ${YELLOW}%s${RESET} overrides a builtin of the same name.\n" "$indent" "$cmd"
                flag=1
                break
                ;;
                file)
                printf "%s    ↳ Alias ${YELLOW}%s${RESET} overrides a command of the same name.\n" "$indent" "$cmd"
                flag=1
                break
                ;;
            esac
        done
    done < <(alias)
    
    if [[ -z "$flag" ]]; then
        printf "%s    ↳ ${CYAN}No command or builtin is overridden.${RESET}\n" "$indent"
    fi
}

#-------------------------------------------------------------------------------------------------

#-------------------------------------------------------------------------------------------------

#-------------------------------------------------------------------------------------------------
