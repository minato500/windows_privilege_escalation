# GetSystem

We got a shell in the windows 7 using metasploit and there is buildin tool called getsystem

- getsystem is used after you already have a Meterpreter session on a target machine. Its purpose is to elevate privileges to SYSTEM (the highest privilege level on Windows)
- It attempts multiple privilege escalation techniques to impersonate the NT AUTHORITY\SYSTEM account

```
// making privilege escalation 
getsystem 

// to check the usage 
getsystem -h 
Usage: getsystem [options]
Attempt to elevate your privilege to that of local system.
OPTIONS:
-h        Help Banner.
-t   The technique to use. (Default to '0').
0 : All techniques available
1 : Service - Named Pipe Impersonation (In Memory/Admin)
2 : Service - Named Pipe Impersonation (Dropper/Admin)
3 : Service - Token Duplication (In Memory/Admin)
```

1. Named Pipe Impersonation

- Windows allows processes to communicate using named pipes (special files for inter-process communication)
- A high-privileged process (like SYSTEM) might connect to a pipe
- If the attacker (low-privileged user) creates a pipe first, and a SYSTEM process connects, the attacker can impersonate the token of that SYSTEM process
- Meterpreter sets up a malicious named pipe and tricks Windows into connecting with SYSTEM rights → attacker steals that identity
- Works if you have SeImpersonatePrivilege (common in service accounts) and Fails if impersonation rights are restricted or patched

2. Service-based Escalation

- On Windows, services often run with SYSTEM privileges.
- If an attacker can create or modify a service, they can make it run malicious code (like Meterpreter)
- getsystem tries to install a temporary service that runs as SYSTEM → executes payload → deletes the service
- Works if you have rights to create/manage services and Needs Administrator-level access at least (not just a normal user)

3. Token Duplication

- In Windows, when users or services log in, they are assigned tokens that define their privileges
- If SYSTEM or Administrator tokens exist in memory, a lower-privileged attacker may be able to duplicate one of those tokens
- This is often called token impersonation or token theft
- After duplicating a SYSTEM token, Meterpreter can "impersonate" SYSTEM without directly running code as SYSTEM
- Works if tokens are available in the session and doesn’t work if no privileged tokens exist in memory

Resource for [getsystem](https://www.cobaltstrike.com/blog/what-happens-when-i-type-getsystem)

