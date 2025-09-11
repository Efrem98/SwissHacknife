# 🗺️ Pentest Roadmap – Da A a Z  

Percorso step-by-step per guidare un **penetration test** (o un CTF come HackTheBox / TryHackMe) dall’inizio alla fine.  
Non è una guida esaustiva, ma una **mappa mentale** che rimanda ai file dedicati per i dettagli e i comandi copia-incolla.  

---

## 1️⃣ Recon & Discovery
🔍 Identifica **host attivi, porte e servizi**.  
- Scansioni complete e veloci.  
- Fingerprinting dei servizi e banner grabbing.  
👉 Vai a: [0 - Recon.md](./0%20-%20Recon.md)  

---

## 2️⃣ Enumeration & Service Analysis
📡 Approfondisci i servizi scoperti.  
- HTTP/HTTPS → directory brute-force, CMS detection.  
- SMB, LDAP, DNS → enum e dump.  
👉 Vai a: [1 - Web.md](./1%20-%20Web.md), [4 - Wordpress.md](./4%20-%20Wordpress.md)  

---

## 3️⃣ Credential Attacks & Password Cracking
🔑 Trova credenziali o sfrutta password deboli.  
- Brute-force login (Hydra, CME, ecc.).  
- Cracking hash (John, Hashcat, wordlist mangling).  
👉 Vai a: [2 - Passwords.md](./2%20-%20Passwords.md)  

---

## 4️⃣ Exploitation
💥 Sfrutta vulnerabilità note o zero-day (solo lab).  
- Exploit pubblici (searchsploit, PoC).  
- Metasploit Framework (payload, handler, msfvenom).  
👉 Vai a: [11 - Metasploit.md](./11%20-%20Metasploit.md)  

---

## 5️⃣ Access & Shells
🖥️ Ottieni e stabilizza una shell.  
- Reverse / Bind shell.  
- TTY upgrade e stabilizzazione.  
👉 Vai a: [5 - Shells.md](./5%20-%20Shells.md)  

---

## 6️⃣ Privilege Escalation
📈 Escalation da utente a root/Administrator.  
- Linux: SUID, Cron, Kernel exploits.  
- Windows: Services, Token abuse, DLL hijacking.  
👉 Vai a: [6 - Privilege Escalation.md](./6%20-%20Privilege%20Escalation.md)  
👉 Tools: [7 - linPEAS.md](./7%20-%20linPEAS.md), [8 - Pspy.md](./8%20-%20Pspy.md)  

---

## 7️⃣ Post-Exploitation
🎯 Consolidare l’accesso e raccogliere prove.  
- Evidence gathering (flag, secrets).  
- Persistence (solo lab).  
- Lateral movement (solo lab).  
👉 Vai a: [9 - Post Exploitation.md](./9%20-%20Post%20Exploitation.md)  

---

## 8️⃣ File Transfer & Utility
📂 Trasferisci/exfiltra file e usa tool extra.  
- `wget`, `curl`, `scp`, `certutil`.  
- Script di supporto e tool minori.  
👉 Vai a: [3 - File-Transfer.md](./3%20-%20File-Transfer.md), [10 - Misc & Utility.md](./10%20-%20Misc%20&%20Utility.md)  

---

## 9️⃣ Reporting & Wrap-up
📝 Documenta tutto ciò che hai fatto.  
- Screenshot, note, flag raccolte.  
- Evidenze di vulnerabilità e exploit.  
- Checklist finale: **ho raggiunto tutti gli obiettivi?**

---

⚡ **Nota bene:**  
- Ogni fase punta a un file dedicato con **comandi pratici e spiegazioni**.  
- Usa questa roadmap come **indice veloce** e come **percorso di riferimento**.  
