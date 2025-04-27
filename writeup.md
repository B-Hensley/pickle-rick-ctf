# 🥒 TryHackMe - Pickle Rick CTF Writeup 🧠

## 📋 Overview
This Rick and Morty-themed CTF challenge involves exploiting a web server to find three secret ingredients needed for Rick's potion to transform himself back into a human. The challenge tests web enumeration, source code analysis, and privilege escalation skills in a Linux environment.

## 🖥️ Target Information
- **IP Address**: 10.10.214.251
- **Operating System**: Linux (based on file structure and commands)
- **Objective**: Find three secret ingredients for Rick's potion

## 🔎 Enumeration

### 🛜 Initial Access
1. 🕸️**Web Page Analysis**
   - Navigated to target IP address in Firefox
   - Analyzed source code of website
   - Webpage contained embedded image: `/assets/rickandmorty.jpeg`
   - HTML source revealed hidden comment with credentials

2. 🧬**Source Code Analysis**
   - Used browser dev tools (F12) to inspect HTML source
   - Found HTML comment: `<!-- Note to self, remember username! Username: R1ckRul3s -->`
   - Identified potential attack vectors in form inputs

3. 📂**Directory Enumeration**
   - Performed manual directory traversal:
     - `/admin` → 404 Not Found
     - `/admin.php` → 404 Not Found
     - `/login.php` → 200 OK
   - Used `curl` to check `robots.txt`:
     ```bash
     curl http://10.10.214.251/robots.txt
     ```
   - Discovered password in robots.txt: `Wubbalubbadubdub`

### 🔓 Authentication
- **Credentials Found**:
  - Username: `R1ckRul3s`
  - Password: `Wubbalubbadubdub`
- **Authentication Process**:
  - POST request to `/login.php`
  - Form data: `username=R1ckRul3s&password=Wubbalubbadubdub`
  - Successful authentication redirected to `portal.php`

## ⚡ Exploitation

### 💻 Command Execution Panel
- **Access Control**:
  - Gained access to command execution panel through `portal.php`
  - Identified command restrictions:
    - `cat` command disabled
    - `less` command available
    - Basic Linux commands functional

- **File System Enumeration**:
  ```bash
  ls -la
  ```
  Output:
  ```
  -rw-r--r-- 1 www-data www-data 15 Mar 15  2020 Sup3rS3cretPickl3Ingred.txt
  drwxr-xr-x 2 www-data www-data 4096 Mar 15  2020 assets
  -rw-r--r-- 1 www-data www-data 15 Mar 15  2020 clue.txt
  -rw-r--r-- 1 www-data www-data 15 Mar 15  2020 denied.php
  -rw-r--r-- 1 www-data www-data 15 Mar 15  2020 index.html
  -rw-r--r-- 1 www-data www-data 15 Mar 15  2020 login.php
  -rw-r--r-- 1 www-data www-data 15 Mar 15  2020 portal.php
  -rw-r--r-- 1 www-data www-data 15 Mar 15  2020 robots.txt
  ```

### Finding Ingredients

1. 🥣**First Ingredient**
   - Used `less` command to bypass `cat` restriction:
     ```bash
     less Sup3rS3cretPickl3Ingred.txt
     ```
   - Found: `mr. meeseek hair`

2. 💧**Second Ingredient**
   - Explored `/home/rick` directory:
     ```bash
     ls -la /home/rick
     ```
   - Used wildcard expansion to read file:
     ```bash
     less /home/rick/second*
     ```
   - Found: `1 jerry tear`

3. 🧃**Third Ingredient**
   - Checked sudo permissions:
     ```bash
     sudo -l
     ```
   - Discovered unrestricted sudo access
   - Traversed directories:
     ```bash
     sudo ls -la /root
     ```
   - Read root file:
     ```bash
     sudo less /root/3rd.txt
     ```
   - Found: `fleeb juice`

### 🎣 Red Herring Analysis
- Found base84 encoded string in `portal.php` source:
  ```
  Vm1wR1UxTnRWa2RUV0d4VFlrZFNjRlV3V2t0alJsWnlWbXQwVkUxV1duaFZNakExVkcxS1NHVkliRmhoTVhCb1ZsWmFWMVpWTVVWaGVqQT0==
  ```
- Decoding process:
  1. Base84 decode using CyberChef
  2. Result: Empty string (false lead)

## 📜 Summary of Findings

| Ingredient | Value | Location | Access Method |
|------------|-------|----------|---------------|
| First Ingredient | mr. meeseek hair | Sup3rS3cretPickl3Ingred.txt | less command |
| Second Ingredient | 1 jerry tear | /home/rick/second* | wildcard expansion |
| Third Ingredient | fleeb juice | /root/3rd.txt | sudo privilege escalation |

## 🛠️ Tools Used
- **Network Analysis**:
  - `curl` for HTTP requests
  - Browser dev tools for source inspection
- **Decoding**:
  - CyberChef for base84 analysis
- **System Commands**:
  - `less` for file reading
  - `sudo` for privilege escalation
  - `ls` for directory enumeration
  - Wildcard expansion for file access

## 🧠 Security Concepts Demonstrated
1. 📢**Information Disclosure**:
   - Hidden comments in HTML source
   - Sensitive information in robots.txt
   - Directory structure exposure

2. 🎯**Command Injection**:
   - Bypassing command restrictions
   - Using alternative commands
   - Wildcard expansion techniques

3. 🚀**Privilege Escalation**:
   - Sudo misconfiguration
   - Root directory access
   - File permission analysis

4. 🌐**Web Security**:
   - Authentication bypass
   - Directory traversal
   - Source code analysis

## ✍️ Key Takeaways
1. 🌍**Web Enumeration**:
   - Always inspect HTML source and comments
   - Check standard files (robots.txt, sitemap.xml)
   - Perform manual directory traversal

2. 🛠️**Command Execution**:
   - When primary commands are restricted, try alternatives
   - Understand command substitution methods
   - Utilize wildcard expansion for file access

3. 🔥**Privilege Escalation**:
   - Always check sudo permissions
   - Look for misconfigurations
   - Understand file permissions and ownership

4. 🛡️**Security Best Practices**:
   - Never store credentials in comments
   - Implement proper command filtering
   - Restrict sudo privileges
   - Secure sensitive directories

## 🏁 Conclusion
This CTF challenge provided practical experience in web application security, command injection, and privilege escalation. The Rick and Morty theme made the challenge engaging while demonstrating real-world security concepts and common misconfigurations in web applications. 
