
[HackTricks PowerView/SharpView](https://book.hacktricks.xyz/windows-hardening/basic-powershell-for-pentesters/powerview)
## Quick enum

```powershell
Get-NetDomain #Basic domain info
#User info
Get-NetUser -UACFilter NOT_ACCOUNTDISABLE | select samaccountname, description, pwdlastset, logoncount, badpwdcount #Basic user enabled info
Get-NetUser -LDAPFilter '(sidHistory=*)' #Find users with sidHistory set
Get-NetUser -PreauthNotRequired #ASREPRoastable users
Get-NetUser -SPN #Kerberoastable users
#Groups info
Get-NetGroup | select samaccountname, admincount, description
Get-DomainObjectAcl -SearchBase 'CN=AdminSDHolder,CN=System,DC=EGOTISTICAL-BANK,DC=local' | %{ $_.SecurityIdentifier } | Convert-SidToName #Get AdminSDHolders
#Computers
Get-NetComputer | select samaccountname, operatingsystem
Get-NetComputer -Unconstrainusered | select samaccountname #DCs always appear but aren't useful for privesc
Get-NetComputer -TrustedToAuth | select samaccountname #Find computers with Constrained Delegation
Get-DomainGroup -AdminCount | Get-DomainGroupMember -Recurse | ?{$_.MemberName -like '*$'} #Find any machine accounts in privileged groups
#Shares
Find-DomainShare -CheckShareAccess #Search readable shares
#Domain trusts
Get-NetDomainTrust #Get all domain trusts (parent, children and external)
Get-NetForestDomain | Get-NetDomainTrust #Enumerate all the trusts of all the domains found
#LHF
#Check if any user passwords are set
$FormatEnumerationLimit=-1;Get-DomainUser -LDAPFilter '(userPassword=*)' -Properties samaccountname,memberof,userPassword | % {Add-Member -InputObject $_ NoteProperty 'Password' "$([System.Text.Encoding]::ASCII.GetString($_.userPassword))" -PassThru} | fl
#Asks DC for all computers, and asks every compute if it has admin access (very noisy). You need RCP and SMB ports opened.
Find-LocalAdminAccess
#Get members from Domain Admins (default) and a list of computers and check if any of the users is logged in any machine running Get-NetSession/Get-NetLoggedon on each host. If -Checkaccess, then it also check for LocalAdmin access in the hosts.
Invoke-UserHunter -CheckAccess
#Find interesting ACLs
Invoke-ACLScanner -ResolveGUIDs | select IdentityReferenceName, ObjectDN, ActiveDirectoryRights | fl
```
