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

## User Enumeration 

```
// Current username in the system and we can verify that we are an system level user or not 
whoami

// we can see the privileges provided for the current user
whoami /priv 

// we can check the group which the current user found and can confirm their presence in any sort of the administrative groups 
whoami /groups

// To list the user in the machines
net user 

// we can see some informations about other users like last password changed, active account 
net user user_name
net user administrator (for admin informations)
```

## Network Enumeration

```
// To get the ip address of the machine and additional information about DNS server, etc
ipconfig

// To get the arp table 
arp -a 

// To get route table 
route print 
```

## Password Hunting 

In the password hunting first we going to search for the password in local system (stored file) or file. First step is somebody will store or make their wifi password same as the work credentials to make easier to remember, this password could be stored in registry 

```
// To search for the password in a word 
findstr /si password *.txt *.init
```

## AV Enumeration

We want to look at the configurations of firewall and Anti-viruses. We look the informations using service query 

```
// showing windows defender configurations
sc query windefend

// to find all the service running on the machine (used to find other anti-viruses)
sc queryex type= service 

// to see the firewall configurations
netsh advfirewall firewall dump

// if above command not worked (this is for the older versions)
netsh firewall show state 

// to see open ports and configuration in firewall
netsh firewall show config
```
