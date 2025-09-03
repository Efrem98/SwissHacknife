# 🎨 Recon Cheatsheet – Poster Edition

🧭 **Mappa mentale veloce** degli strumenti di **ricognizione**, divisa per protocolli/servizi.  

---

> ⚡ **Scansione Porte & Servizi**
>
> - 🔢 **Nmap** → scansione porte, versioni, NSE  
>   ```bash
>   nmap -sC -sV -p- <target-ip>
>   ```
> - 🚀 **Rustscan** → port scanner ultra-veloce → output a Nmap  
>   ```bash
>   rustscan -a <target-ip> -r 1-65535 -- -sC -sV
>   ```

---

> 🌐 **Web Recon**
>
> - 📂 **Gobuster** → directory/file bruteforce  
> - 🚀 **ffuf** → fuzzing avanzato (dir, file, param, API, subdomains)  
> - 🖼️ **Nikto** → vuln scanner “broad” (config, file sensibili)  
> - 🧭 **WhatWeb** → fingerprint CMS/framework  
> - 🛡️ **WafW00f** → rilevamento WAF  
> - 📦 **davtest** → test upload WebDAV  
>
> ```bash
> gobuster dir -u http://<target-ip>/ -w common.txt
> ffuf -u http://<target-ip>/FUZZ -w common.txt
> nikto -h http://<target-ip>
> whatweb http://<target-ip>/
> wafw00f http://<target-ip>/
> davtest -url http://<target-ip>/
> ```

---

> 🔒 **SMB Enumeration**
>
> - 📦 **Enum4linux** → utenti, share, policy  
> - 📜 **smbmap** → lista share/permessi  
> - 🛠️ **CrackMapExec** → SMB scan + brute credenziali  
>
> ```bash
> enum4linux -a <target-ip>
> smbmap -H <target-ip>
> crackmapexec smb <target-ip> -u '' -p ''
> ```

---

> 📡 **Altri Servizi**
>
> - 📡 **MQTT** (1883) → `mosquitto_sub/pub`  
>   ```bash
>   mosquitto_sub -h <target-ip> -t '#' -v
>   ```
> - 📚 **LDAP** (389) → `ldapsearch`  
>   ```bash
>   ldapsearch -x -H ldap://<target-ip> -s base
>   ```
> - 🛰️ **SNMP** (161/udp) → `snmpwalk`  
>   ```bash
>   snmpwalk -v2c -c public <target-ip>
>   ```
> - ✉️ **SMTP** (25/587) → `smtp-user-enum`  
>   ```bash
>   smtp-user-enum -M VRFY -U users.txt -t <target-ip>
>   ```

---

> 🌍 **DNS & Domain Recon**
>
> - 🔍 **dig/nslookup** → query record singoli  
> - 🌐 **dnsenum** → enum DNS/subdomains  
> - 🌐 **dnsrecon** → bruteforce subdomains, MX, zone transfer  
>
> ```bash
> dig A <domain>
> nslookup <domain>
> dnsenum <domain>
> dnsrecon -d <domain> -t brt -D subdomains.txt
> ```

---

# 🧠 Quick Map

- 🔢 **Tutte le porte** → Nmap / Rustscan  
- 🌐 **Web (80/443)** → Gobuster / ffuf / Nikto / WhatWeb / WafW00f / davtest  
- 🔒 **SMB (139/445)** → Enum4linux / smbmap / CrackMapExec  
- 📡 **MQTT (1883)** → mosquitto_sub/pub  
- 📚 **LDAP (389)** → ldapsearch  
- 🛰️ **SNMP (161/udp)** → snmpwalk  
- ✉️ **SMTP (25/587)** → smtp-user-enum  
- 🌍 **DNS (53)** → dig / nslookup / dnsenum / dnsrecon  

---
