# RunAs 

RunAs command allow us to run command as somebody else 

```
// To look for stored credentials on the machine 
cmdkey /list 

// nmap result 
PORT    STATE  SERVICE  VERSION 
21/tcp  open   ftp      Microsoft ftpd 
23/tcp  open   telnet? 
80/tcp  open   http?
```

We moving to the website in the port 80 but nothing interesting, we see ftp port is open and anonymous login is allowed here. 

```
ftp> ls 
Backups
Engineer 

// for effectively transfering the file 
ftp> binary 
ftp> cd Backups 
ftp> ls 
backup.mdb 
ftp> get backup.mdb 
ftp> cd Engineer 
Access Control.zip 
ftp> get "Access Control.zip"

```

Resource for [runas](https://learn.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/cc771525(v=ws.11))

