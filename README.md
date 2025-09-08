<h1 align="center">☣️⚙️ All Will Be One ☠️🔥</h1>

<p align="center">
  <img src="https://media1.giphy.com/media/v1.Y2lkPTc5MGI3NjExemlta3NqaHU2YWR4cXprOXJvYnU0NWdxd2dsdDZlbXF4ZjVpajNuYyZlcD12MV9pbnRlcm5hbF9naWZfYnlfaWQmY3Q9Zw/iBc5SoLxZEfkXGNZ8h/giphy.gif" width="600"/>
</p>

> Repository personale con appunti, cheat sheet e comandi pronti per **HackTheBox**, **TryHackMe** e laboratori CTF.  
> ⚠️ **Nota bene:** Tutto il materiale qui è pensato per **ambienti controllati e legali**. Non usare mai queste informazioni su sistemi reali senza autorizzazione.

---

## 🎯 Obiettivi

* Avere una **raccolta centralizzata** di comandi utili già pronti (copia-incolla).
* Organizzare i tool e gli snippet in modo chiaro per **categorie**.
* Creare una vera e propria **miniera di riferimento rapido** durante i lab.
* Usare il repo come **playbook personale** in continua evoluzione.

---

## 📂 Struttura del Playbook

### 0️⃣ Recon & Discovery

* [0 - Recon.md](./0%20-%20Recon.md) → Guida completa alla fase di ricognizione (Nmap, Rustscan, Gobuster, ffuf, Nikto, WhatWeb, WafW00f, davtest, Enum4linux, smbmap, CrackMapExec, MQTT, LDAP, SNMP, SMTP, DNS).

---

### 1️⃣ Web Exploitation

* [1 - Web.md](./1%20-%20Web.md) → Tecniche e tool per l’exploitation web (sqlmap, XSStrike, LFISuite, weevely3, WPScan, Droopescan, Joomscan, ysoserial, PHPGGC, tplmap, Commix, jwt_tool, NoSQLMap, Burp Suite).

---

### 2️⃣ Passwords & Cracking

* [2 - Passwords.md](./2%20-%20Passwords.md) → Sezione dedicata al cracking password (John, Hashcat, cewl, cupp, rsmangler, crunch, pipal, Hydra, CrackMapExec, kerbrute, rainbow tables).

---

### 3️⃣ File Transfer

* [3 - File-Transfer.md](./3%20-%20File-Transfer.md) → Guida completa ai metodi di trasferimento file (SCP, Python HTTP server, Netcat, Curl/Wget, FTP, SMB, Base64).

---

### 4️⃣ WordPress

* [4 - Wordpress.md](./4%20-%20Wordpress.md) → WPScan, plugin/theme enum, brute force, shell upload.

---

### 5️⃣ Shells

* [5 - Shells.md](./5%20-%20Shells.md) → Reverse shells, bind shells, web shells, stabilizzazione TTY + link utili (PentestMonkey, SecLists, GTFOBins).

---

### 6️⃣ Privilege Escalation

* [6 - Privilege Escalation.md](./6%20-%20Privilege%20Escalation.md) → Tecniche di escalation su **Linux** (SUID, cronjob, capabilities, kernel exploits) e **Windows** (Services, Registry, Token abuse, DLL hijacking) + risorse utili (GTFOBins, LOLBAS, HackTricks, PayloadsAllTheThings).

---

## 🚀 Prossimi step

* Creare sezioni dedicate a **Post-Exploitation** e **Persistence**.
* Integrare esempi pratici e workflow completi (es. *da Recon → Exploit → PrivEsc*).
* Aggiungere riferimenti rapidi a wordlist, tool custom e script personali.
