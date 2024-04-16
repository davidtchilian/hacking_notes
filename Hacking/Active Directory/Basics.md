
Change the password of another user you have the permissions of changing the password :
```powershell
Set-ADAccountPassword sophie -Reset -NewPassword (Read-Host -AsSecureString -Prompt 'New Password') -Verbose
```

Configuring DNS to see Domain Controller :
```bash
systemd-resolve --interface breachad --set-dns $THMDCIP --set-domain za.tryhackme.com
# THMDCIP contains ip of the domain controller (10.10.200.22)
# za.tryhackme.com is the domain that will be resolved as dns
```
