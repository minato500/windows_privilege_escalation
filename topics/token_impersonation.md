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

So if we have service account running with the privileges to impersonate security token it is vulnerable to [juice-potato attack](https://github.com/ohpe/juicy-potato)
