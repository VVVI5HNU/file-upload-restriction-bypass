# File Upload Bypass via `.htaccess` Overwrite

> ⚠️ **Disclaimer**  
> This repository is intended strictly for **educational, defensive, and authorized security testing purposes**.  
> Test only applications you own or have explicit permission to assess.  
> Certain exploit payloads are **intentionally redacted** to prevent misuse.

---

## 📌 Overview

This repository documents a **file upload bypass technique** where an attacker is able to:

- Upload arbitrary file types
- Access uploaded files
- Initially receive **403 Forbidden** or similar errors when attempting execution
- Bypass execution restrictions by abusing server-side configuration handling

This technique is commonly observed in **traditional Apache-based web applications** that rely on `.htaccess` for access control and execution rules.

---

## 🧠 Background: Why `.htaccess` Matters

In many traditional web applications, the `.htaccess` file controls:

- File execution permissions
- Allowed file extensions
- MIME type handling
- Access control rules
- Directory-level security policies

If a file upload functionality allows **server configuration files** to be uploaded into a web-accessible directory, the application may become vulnerable to **configuration overwrite attacks**.

---

## 🔍 Vulnerability Scenario

A typical vulnerable setup looks like this:

- The application allows file uploads
- Uploaded files are stored inside the web root
- Uploaded files are accessible via direct URL
- Execution of certain file types is blocked (e.g., returns `403 Forbidden`)
- The upload directory processes `.htaccess` files

Even if accessing a configuration file directly returns `403 Forbidden`, **Apache may still apply the rules internally**.

---

## 🧪 High-Level Attack Flow (Educational)

> ⚠️ This section explains practical demonstration.  

1. Identify a file upload functionality in the application
2. Upload a benign file and determine:
   - Storage location
   - Direct accessibility via browser
3. Check whether the upload directory processes `.htaccess` rules
4. Overwrite `.htaccess` configuration file by uploading new `.htaccess` file with own rule
   👉 **[Click here to get the php allowed `.htaccess`](https://github.com/VVVI5HNU/file-upload-restriction-bypass/blob/main/htaccess.txt)**
5. Capture the upload request using Burpsuite
6. In Burpsuite request change filname of htaccess.txt to .htaccess
7. Upload the file successfully
8. Observe that:
   - Direct access may return `403 Forbidden`
   - Server behavior changes for subsequent uploads
9. Upload another file type (php) now permitted by the overwritten configuration
10. Capture the request and change the content in file to : `<?php echo shell_exec('whoami'); ?>` or any executable command and observe the response
11. Validate if you can see output of command in burpsuite response

---

## 🔎 Important Observation

Even if:
- The `.htaccess` file cannot be accessed directly
- The browser shows `403 Forbidden`

The **configuration rules may still be applied by the server**, affecting how other files in the same directory are handled.

---

## 🚫 Redacted Exploitation Details

Details related to:
- Command execution
- Shell access
- System interaction

---

## 💥 Security Impact

If this issue is successfully exploited, it may lead to:

- File upload restriction bypass
- Unauthorized file execution
- Server-side code execution (context dependent)
- Complete compromise of application integrity

---

## 🛡 Mitigation & Prevention

To prevent this issue:

- Block upload of configuration files (`.htaccess`, `web.config`)
- Store uploaded files outside the web root
- Disable per-directory overrides (`AllowOverride None`)
- Enforce strict file extension and MIME validation
- Apply least-privilege permissions on upload directories

---
