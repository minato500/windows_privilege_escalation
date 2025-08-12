# Initial Enumeration

Enumeration is done after the initial access to get additional information about the target system like (system, user, network, password hunting, and av). We have five stages of hacking, In which first-three are scanning, enumeration and exploitation. Our final goal is to get the system administration access

After getting low privileged access using the meterpeter, call the shell for the effective use to navigate and command execution. 

## System Enumeration

```
shell

// The get the system information like hostname, os version and manufacturer informations 
systeminfo

// Effective usage for needed information
systeminfo | findstr /B /C:"OS Name" /C:"OS Version" /C:"System Type"
```

By using this we could found the architecture (x86 or x64) and can make the payload according to it. The os version contains the information about the kernel which would lead to kernel exploit later 

```
// getting a hostname information
hostname

// to get the information that system we are running on and to see the patch details 
wmic qfe

// logical disk 
wmic logicaldisk 
```
