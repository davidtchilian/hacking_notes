
```bash
python3 -c 'import pty;pty.spawn("/bin/bash")' OR /usr/bin/script -qc /bin/bash /dev/null
Ctrl-Z

# On attacker machine
stty raw -echo; fg

# In reverse shell
reset
export SHELL=bash
export TERM=xterm-256color

# stty -a to check for cols and rows on your machine
stty rows <num> columns <cols>
```
