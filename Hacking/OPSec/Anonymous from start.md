
- Buy the computer on a platform that accepts Monero
- Deliver it to an abandonned address and pick it up
- Full wipe it and delete everything on it
- Install Tails on a flash drive and use it to boot
- Spoof your mac address (automatic on Tails for each session)
	1. `ip link show` # usually eth0 will be your interface
	2. `ip link set dev eth0 down` # Turn off your interface
	3. `ip link set dev interface address XX:XX:XX:XX:XX:XX` # Change your mac address
	4. `ip link set dev eth0 up` # Restart your interface
- Spoof your IP
	1. VPN (OpenVPN)
	2. Tor (Tor Browser)
	3. Proxy (Proxy chains) 
```
# edit /etc/proxychains4.conf and setup proxychains
# set dynamic_chain and comment strict_chain enable proxy_dns
# at the end :
# [ProxyList]
# socks4  127.0.0.1 9050
# socks5  127.0.0.1 

service tor start
proxychains firefox www.duckduckgo.com
```

- Machine separation
	1. Virtual machines
	2. Bouncing server (no care cloud provider)

- Public wifi
- DNS Tunneling
	- dnscat2 tunnel commands through dns and execute commands on a victim machine
