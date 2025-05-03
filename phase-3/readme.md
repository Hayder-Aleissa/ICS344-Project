# Phase 3 – Securing the FTP Service (ICS 344)

> Harden an FTP server running on a vulnerable VM, demonstrate the original exploit, apply mitigations, and verify that the attack can no longer succeed.

---

## 1  Overview

This mini-project documents how we blocked a Metasploit `SITE CPFR/CPTO` copy exploit against an FTP service that hosts our web root.  
The defence relies on:

* Switching from **ProFTPd + mod_copy** to **vsftpd**  
* Denying dangerous `SITE` commands with `cmds_denied`  
* Mounting the web-root as **root-owned and non-writable** for regular FTP users :contentReference[oaicite:1]{index=1}

<!-- 📸 Place your main diagram here -->
---

## 2  Before – Vulnerable State

* Service: **ProFTPd 1.3.5 with mod_copy**  
* `SITE CPFR` + `CPTO` let an attacker copy `/tmp/rev.php` into **/var/www/html**.  
* Metasploit’s `proftpd_modcopy_exec` module opens a reverse shell on success. :contentReference[oaicite:3]{index=3}

<!-- 📸 Screenshot: metasploit exploit succeeds -->
![Image](https://github.com/user-attachments/assets/59154339-933d-4537-b855-2976746d6b25)
![Image](https://github.com/user-attachments/assets/03f353ab-5c67-4de1-9e8c-2dac764381f2)
---

## 3  Applying the Defence

### 3.1  Reconfigure FTP

```ini
# /etc/vsftpd.conf
cmds_denied=CPFR,CPTO
chroot_local_user=YES
allow_writeable_chroot=NO
```
Restart vsftpd after saving the file.
Change ownership of the web root to root (chown -R root:root /var/www/html).

### 3.2 Why it works
cmds_denied blocks the copy commands outright.

Even if an attacker uploads rev.php to /tmp, they cannot move it into the web tree because they lack write permission. 

<!-- 📸 Screenshot: vsftpd config or terminal showing chown -->
![Image](https://github.com/user-attachments/assets/13dcfe96-0f0c-44eb-bc35-b1765ee4519a)
![Image](https://github.com/user-attachments/assets/1f2fb9c5-0d6a-42f6-a58e-1b34a9ce8d63)

## 4 After – Hardened State
The same Metasploit module now aborts; payload never reaches /var/www/html.

vsftpd responds 500 Unknown SITE command or 550 ... not allowed. 

<!-- 📸 Screenshot: exploit aborted / rev.php missing -->
![Image](https://github.com/user-attachments/assets/7b921a8f-579d-4053-93c5-c839b04eb4e3)

## 5 Proving that attack blocked 
attacker tries the old exploit → fails
victim log shows denied command
![Image](https://github.com/user-attachments/assets/416337bb-1d9d-4ddb-af8f-f80b597cd244)

## 6 Comparison Table
| Metric                                                   | Before (Vulnerable)                                   | After (Hardened)                                   |
|----------------------------------------------------------|-------------------------------------------------------|----------------------------------------------------|
| **FTP service**                                          | `ProFTPd 1.3.5` + `mod_copy`                           | `vsftpd` (secure)                                  |
| **Dangerous SITE commands** (`CPFR`, `CPTO`)             | **Allowed** – server replies `250 Copy successful`    | **Denied** – server replies `500 Unknown SITE command` |
| **Web-root writeability**                                | Writable by FTP user → attacker can drop `rev.php`    | Owned by `root`; regular users **read-only**       |
| **Metasploit module result**                             | Exploit runs and opens a reverse shell                | Exploit fails; no payload delivered                |

## 7 How to Reproduce
```
# 1  Spin up attacker (Kali) and victim (Ubuntu) VMs
# 2  On victim BEFORE hardening:
sudo apt install proftpd-mod-copy
# ... run exploit from attacker; observe shell

# 3  Apply the defence steps (section 3), switch to vsftpd
# 4  Run the exploit again; observe failure
```

## 8 Credits
Course: ICS 344 – Security
Author: Faisal Alhamdi
Tools: Metasploit, vsftpd, VirtualBox/VMware
