
## Enumerate shares

```bash
smbclient -L <IP>
```

Shares with `$` have a password

## Connect to share

```bash
smbclient \\\\IP\\\SHARE_NAME
```

## Basic commands 

`help`, `get`, `mget`, `put`, `mput`

Other commands also work, it is similar to Linux bash commands. (mv, ls etc)

Download all files from a folder : 
```
mask ""
recurse ON
prompt OFF
cd 'path\to\remote\dir'
lcd '~/path/to/download/to/'
mget *
```
