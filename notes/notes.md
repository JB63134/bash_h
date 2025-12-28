---
pop doesn't exist as a command on my system  
pop --help -help -h "-?" all fail  
but   
Bash’s help performs prefix matching, so help pop resolves to popd.    
---

does fedora duplicate every binary in the file system? mixing sbin with bin? 
debian, root only, sbin.
fedora, 
    \u251c\u2500 fstrim -- command resolution order:
    \u239f
    \u251c\u2500 Alias?
    \u239f    \u21b3 fstrim - not found
    \u239f   
    \u251c\u2500 Function?
    \u239f    \u21b3 fstrim - not found
    \u239f   
    \u251c\u2500 Builtin?
    \u239f    \u21b3 fstrim - not found
    \u239f   
    \u251c\u2500 Keyword?
    \u239f    \u21b3 fstrim - not found
    \u239f   
    \u251c\u2500 $PATH in order
    \u239f    \u21b3 /sbin                    fstrim - found
    \u239f    \u21b3 /usr/games       
    \u239f    \u21b3 /usr/sbin                fstrim - found [shadowed]
    \u239f    \u21b3 /usr/local/games       
    \u239f    \u21b3 /usr/local/sbin         
    \u239f    \u21b3 /bin                     fstrim - found [shadowed]
    \u239f    \u21b3 /home/jb/.local/bin      
    \u239f    \u21b3 /home/jb/bin             
    \u239f    \u21b3 /usr/local/bin           
    \u239f    \u21b3 /usr/bin                 fstrim - found [shadowed]
    \u239f 
    \u251c\u2500 Bash resolution target:
    \u239f    \u21b3 Executed: /sbin/fstrim  -  Symlink to [ /usr/bin/fstrim ]
    \u239f 
    \u251c\u2500 Kernel execution target:
    \u239f    \u21b3 Symlink \u2192 /usr/bin/fstrim
    \u239f    \u21b3 ELF interpreter: /lib64/ld-linux-x86-64.so.2

