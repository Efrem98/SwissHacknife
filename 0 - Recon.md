# 🔍 Recon & Discovery

Appunti e snippet per la fase di **ricognizione**.

## 📌 Obiettivi
- Identificare host e servizi
- Capire la superficie di attacco
- Preparare la fase di exploit

---

## 🌐 Nmap

**Perché usarlo:**  
Nmap è lo strumento principale per la scansione delle porte e l’identificazione dei servizi.

**Opzioni principali:**
- `-sC` → esegue gli script NSE di default (info sui servizi comuni)
- `-sV` → rileva la versione dei servizi
- `-p-` → scansiona **tutte** le porte (0–65535)
- `-Pn` → salta il ping discovery (utile se ICMP è filtrato)
- `-oN` → salva output in formato leggibile (Normal)
- `-oA` → salva output in 3 formati (Normal, Grepable, XML)

### 🔹 Comandi diretti (solo output su CLI)
```bash
# Scansione completa di tutte le porte + service/version
nmap -sC -sV -p- <target-ip>

# Scansione veloce delle porte più comuni
nmap -sC -sV <target-ip>

# Quando il ping è bloccato (host considerato "up")
nmap -sC -sV -Pn <target-ip>
````

### 💾 Comandi con salvataggio su file

```bash
# Output leggibile
nmap -sC -sV -p- -oN nmap_full.txt <target-ip>

# Output in 3 formati: <prefix>.nmap / .gnmap / .xml
nmap -sC -sV -p- -oA nmap_scan <target-ip>
```

---

## 📂 Gobuster (Directory Bruteforce)

**Perché usarlo:**
Enumera directory e file nascosti su webserver per allargare la superficie d’attacco.

**Opzioni principali:**

* `dir` → modalità directory/file discovery
* `-u` → URL target (es. `http://<target-ip>/`)
* `-w` → wordlist (es. `common.txt`)
* `-x` → estensioni da provare (es. `php,txt,html`)
* `-t` → numero thread (default 10; aumentarlo con cautela)

### 🔹 Comandi diretti (solo output su CLI)

```bash
# Ricerca directory base
gobuster dir -u http://<target-ip>/ -w /usr/share/wordlists/dirb/common.txt

# Ricerca includendo estensioni PHP e TXT
gobuster dir -u http://<target-ip>/ -w /usr/share/wordlists/dirb/common.txt -x php,txt

# Ricerca con la migliore cartella per trovare cartelle 
gobuster dir -u http://<target-ip>/ -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,txt

# Aumentare i thread per velocizzare (attenzione ai falsi negativi)
gobuster dir -u http://<target-ip>/ -w /usr/share/wordlists/dirb/common.txt -t 50
```

### 💾 Comandi con salvataggio su file

```bash
gobuster dir -u http://<target-ip>/ -w /usr/share/wordlists/dirb/common.txt -o gobuster.txt
```

---

## 🖼️ Nikto (Web Vulnerability Scan)

**Perché usarlo:**
Scanner web “broad” per individuare misconfig, file sensibili e vulnerabilità comuni.

**Opzioni principali:**

* `-h` → host/URL target
* `-p` → porta (se diversa da default)
* `-o` → salva output su file
* `-ssl` → forza SSL/TLS se serve

### 🔹 Comandi diretti (solo output su CLI)

```bash
# Scan base
nikto -h http://<target-ip>

# Specifica porta non standard
nikto -h http://<target-ip> -p 8080

# Forza SSL/TLS
nikto -h <target-ip> -ssl
```

### 💾 Comandi con salvataggio su file

```bash
nikto -h http://<target-ip> -o nikto.txt
```

---

## 📦 Enum4linux (SMB Enumeration)

**Perché usarlo:**
Enumera informazioni su condivisioni **SMB**, utenti e policy quando le porte **139/445** sono aperte.

**Opzioni principali:**

* `-a` → modalità “all” (raccolta completa)
* `-U` → enumerazione utenti
* `-S` → enumerazione share
* `-P` → policy password

### 🔹 Comandi diretti (solo output su CLI)

```bash
# Enumerazione completa
enum4linux -a <target-ip>

# Solo utenti
enum4linux -U <target-ip>

# Solo condivisioni
enum4linux -S <target-ip>
```

### 💾 Comando con salvataggio su file

```bash
enum4linux -a <target-ip> | tee enum4linux.txt
```
Perfetto 👍 allora da **Bugged** devi aggiungere solo due cose nuove al tuo `recon.md`:

---

## ⚡ Rustscan (Fast Port Scan → Nmap)

**Perché usarlo:**
Rustscan è uno scanner multithread molto veloce, usato per scoprire le porte aperte e passarle direttamente a **Nmap** per il service/version detection. Utile quando vuoi ridurre i tempi di scansione completi rispetto a Nmap puro.

**Opzioni principali:**

* `-a` → target (IP o CIDR)
* `-r` → range di porte (es. `1-65535`)
* `--` → tutto ciò che segue viene passato a Nmap (es. `-sC -sV`)

### 🔹 Comandi diretti (solo output su CLI)

```bash
# Scan completo di tutte le porte e passaggio risultati a Nmap
rustscan -a <target-ip> -r 1-65535 -- -sC -sV

# Solo discovery porte (senza nmap post-scan)
rustscan -a <target-ip> -r 1-65535
```

### 💾 Comandi con salvataggio su file

```bash
rustscan -a <target-ip> -r 1-65535 -- -sC -sV | tee rustscan_nmap.txt
```

---

## 📡 MQTT (Mosquitto Enumeration)

**Perché usarlo:**
Quando Nmap o Rustscan mostrano una porta legata al protocollo **MQTT** (tipicamente `1883`), è utile testare la possibilità di sottoscriversi o pubblicare su canali senza autenticazione.
Gli strumenti più comuni sono i client **Mosquitto**: `mosquitto_sub` (subscribe) e `mosquitto_pub` (publish).

**Opzioni principali (mosquitto\_sub):**

* `-h` → host
* `-t` → topic (canale a cui iscriversi)
* `-v` → mostra anche il topic nei messaggi

**Opzioni principali (mosquitto\_pub):**

* `-h` → host
* `-t` → topic
* `-m` → messaggio da inviare

### 🔹 Comandi diretti (solo output su CLI)

```bash
# Subscribe a tutti i topic
mosquitto_sub -h <target-ip> -t '#' -v

# Subscribe a un topic specifico
mosquitto_sub -h <target-ip> -t 'devices/sensor1' -v

# Pubblica un messaggio su un topic
mosquitto_pub -h <target-ip> -t 'devices/sensor1' -m 'test'
```
## 🚀 ffuf (Fast Web Fuzzer)

**Perché usarlo:**
**ffuf** è uno strumento molto veloce e versatile per il **fuzzing web**.
Si usa per trovare directory/file nascosti, endpoint API, sottodomini o parametri vulnerabili.
È considerato l’evoluzione di Gobuster, con più opzioni e velocità superiore.

**Opzioni principali:**

* `-u` → URL target con il placeholder `FUZZ`
* `-w` → wordlist
* `-e` → estensioni da provare (es. `.php,.txt`)
* `-recursion` → segue directory trovate e continua lo scan
* `-t` → thread (default 40, può essere aumentato)
* `-mc` → match code di risposta HTTP (es. `200,403`)
* `-fs` → filter size (filtra risposte di una certa lunghezza)

### 🔹 Comandi diretti (solo output su CLI)

```bash
# Enumerazione directory base
ffuf -u http://<target-ip>/FUZZ -w /usr/share/wordlists/dirb/common.txt

# Enumerazione con estensioni
ffuf -u http://<target-ip>/FUZZ -w /usr/share/wordlists/dirb/common.txt -e .php,.txt

# Filtra le risposte che hanno sempre la stessa size (es. 4243 bytes)
ffuf -u http://<target-ip>/FUZZ -w /usr/share/wordlists/dirb/common.txt -fs 4243

# Fuzzing su parametri GET
ffuf -u http://<target-ip>/index.php?FUZZ=test -w /usr/share/wordlists/params.txt
```

### 💾 Comandi con salvataggio su file

```bash
# Salvataggio in JSON
ffuf -u http://<target-ip>/FUZZ -w /usr/share/wordlists/dirb/common.txt -o ffuf.json -of json

# Salvataggio in formato CSV
ffuf -u http://<target-ip>/FUZZ -w /usr/share/wordlists/dirb/common.txt -o ffuf.csv -of csv
```

**Perché usarlo:**
Se vuoi trovare sottodomini tipo `admin.example.com`, `dev.example.com` usando una wordlist (bruteforce DNS via host header), ffuf è ottimo: veloce e flessibile. Utile quando i passivi non restituiscono tutto e vuoi testare nomi “comuni” o personalizzati.

**Opzioni principali (per subdomain fuzzing):**

* `-u` → URL con `FUZZ` placeholder (solitamente `http://example.com/` o `http://FUZZ.example.com/`)
* `-w` → wordlist di subdomains (es. `subdomains-top1million-5000.txt`)
* `-H` → header custom (per il `Host:` quando necessario)
* `-mc` → match http code (es. `200,301,302,403`)
* `-fs` / `-fc` → filter by size/code per ridurre rumore
* `-t` → thread

### 🔹 Esempi pratici (solo output su CLI)

```bash
# Metodo A: host header (molto usato se il server risponde sempre su IP principale)
ffuf -u http://example.com/ -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-5000.txt \
     -H "Host: FUZZ.example.com" -mc 200,301,302,403 -t 50

# Metodo B: direct FUZZ nel subdomain (se DNS risolve i nomi testati — richiede risoluzione)
ffuf -u http://FUZZ.example.com/ -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-5000.txt \
     -mc 200,301,302,403 -t 50

# Metodo C: salva output in JSON per post-processing
ffuf -u http://example.com/ -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-5000.txt \
     -H "Host: FUZZ.example.com" -mc 200,301,302,403 -o ffuf_subs.json -of json
```

### 🔧 Note utili / Best-practice

* Se usi il **Host header** (`-H "Host: FUZZ.domain.tld"`) non è necessario che il nome sia risolvibile via DNS: il server risponderà comunque (utile per virtualhosts).
* Se vuoi verificare la **risoluzione DNS reale** dei sottodomini trovati (Metodo B con `FUZZ.domain`), combina ffuf con `massdns`/`dnsx`/`dig` per confermare gli A/CAA/CNAME:

  ```bash
  cat ffuf_subdomains.txt | dnsx -a -resp
  ```
* Filtra i falsi positivi con `-fs` (filter size) o `-fc` (filter code). Esempio: se la pagina 404 ha 512 bytes, usa `-fs 512` per rimuoverla.
* Per liste grandi, valuta un prefilter con `massdns` per evitare richieste HTTP su nomi non risolvibili (risparmi tempo).

## 📂 davtest (WebDAV Enumeration)

**Perché usarlo:**
`davtest` serve a verificare se un server **WebDAV** consente l’upload di file e quali estensioni sono accettate. È utile quando Nmap rivela il modulo WebDAV attivo su Apache/IIS.

**Opzioni principali:**

* `-url` → URL del target (root del servizio WebDAV)
* `-sendbd` → prova a inviare backdoor di test
* `-move` → prova a spostare file (utile su alcuni setup)

### 🔹 Comandi diretti (solo output su CLI)

```bash
# Test base su WebDAV
davtest -url http://<target-ip>/

# Test con invio di backdoor di esempio
davtest -url http://<target-ip>/ -sendbd
```

### 💾 Comando con salvataggio su file

```bash
davtest -url http://<target-ip>/ | tee davtest.txt
```

## 🌍 WhatWeb / WafW00f (Web Fingerprinting)

**Perché usarli:**

* **WhatWeb** → identifica tecnologie del sito (CMS, framework, linguaggi).
* **WafW00f** → rileva la presenza di Web Application Firewall (WAF).

### 🔹 Comandi diretti

```bash
# Fingerprinting rapido
whatweb http://<target-ip>/

# Fingerprint dettagliato con aggressività alta
whatweb -a 3 http://<target-ip>/

# Rileva WAF
wafw00f http://<target-ip>/
```

### 💾 Salvataggio su file

```bash
whatweb http://<target-ip>/ -a 3 | tee whatweb.txt
wafw00f http://<target-ip>/ | tee wafw00f.txt
```

---

## 🌐 DNS Enumeration (dig / nslookup / dnsenum / dnsrecon)

**Perché usarli:**
Scoperta di sottodomini, record DNS, informazioni su mailserver o configurazioni nascoste.

### 🔹 Comandi diretti

```bash
# Risoluzione A record
dig A <target-domain>

# Risoluzione MX (mail server)
dig MX <target-domain>

# Query diretta
nslookup <target-domain>

# Enumerazione con dnsenum
dnsenum <target-domain>

# Enumerazione con dnsrecon (bruteforce subdomains)
dnsrecon -d <target-domain> -t brt -D /usr/share/wordlists/subdomains-top1million-5000.txt
```

### 💾 Salvataggio su file

```bash
dnsenum <target-domain> | tee dnsenum.txt
dnsrecon -d <target-domain> -t brt -D /usr/share/wordlists/subdomains-top1million-5000.txt -o dnsrecon.txt
```

---

## 📦 SMB (smbmap / CrackMapExec)

**Perché usarli:**
Alternative moderne a Enum4linux per enumerare SMB shares e utenti.

### 🔹 Comandi diretti

```bash
# Lista share con smbmap
smbmap -H <target-ip>

# Lista share con autenticazione guest
smbmap -H <target-ip> -u guest

# CrackMapExec SMB scan
crackmapexec smb <target-ip> -u '' -p ''
```

### 💾 Salvataggio su file

```bash
smbmap -H <target-ip> -u guest | tee smbmap.txt
crackmapexec smb <target-ip> -u '' -p '' | tee cme_smb.txt
```

---

## 📑 LDAP (ldapsearch)

**Perché usarlo:**
Se la porta 389 (LDAP) è aperta → enumerare directory services.

### 🔹 Comandi diretti

```bash
# Query anonima base
ldapsearch -x -H ldap://<target-ip> -s base

# Dump completo con filtro utenti
ldapsearch -x -H ldap://<target-ip> -b "dc=example,dc=com" "(objectClass=person)"
```

### 💾 Salvataggio su file

```bash
ldapsearch -x -H ldap://<target-ip> -s base | tee ldap.txt
```

---

## 📡 SNMP (snmpwalk)

**Perché usarlo:**
Enumerazione dispositivi con SNMP (porta 161/udp).

### 🔹 Comandi diretti

```bash
# Enumerazione base con community "public"
snmpwalk -v2c -c public <target-ip>

# Enumerazione sistema
snmpwalk -v2c -c public <target-ip> 1.3.6.1.2.1.1.1
```

### 💾 Salvataggio su file

```bash
snmpwalk -v2c -c public <target-ip> | tee snmp.txt
```

---

## 📧 SMTP (smtp-user-enum)

**Perché usarlo:**
Verifica la presenza di utenti validi su un server SMTP (porta 25/587).

### 🔹 Comandi diretti

```bash
# Singolo utente
smtp-user-enum -M VRFY -u admin -t <target-ip>

# Lista utenti da wordlist
smtp-user-enum -M VRFY -U /usr/share/wordlists/names.txt -t <target-ip>
```

### 💾 Salvataggio su file

```bash
smtp-user-enum -M VRFY -U /usr/share/wordlists/names.txt -t <target-ip> | tee smtp_enum.txt
```
# 🔎 Sublist3r (Subdomain enumeration — *sublister*)

**Perché usarlo:**
Sublist3r è uno strumento rapido per enumerare sottodomini sfruttando motori di ricerca e vari servizi pubblici (Google, Bing, Yahoo, Twitter, VirusTotal, ecc.). Buono come primo step per raccogliere una lista “di partenza” da poi integrare con wordlist bruteforce o strumenti più completi (amass, subfinder, assetfinder).

**Opzioni principali:**

* `-d` → dominio target
* `-o` → salva output su file
* `-t` → numero di thread
* `-v` → verbose

### 🔹 Comandi diretti (solo output su CLI)

```bash
# Enum base
sublist3r -d example.com

# Con più thread (velocizza)
sublist3r -d example.com -t 50

# Salva i risultati su file
sublist3r -d example.com -o subdomains_example.txt

# Verbose + salvataggio
sublist3r -d example.com -t 50 -v -o subdomains_example.txt
```

### 💾 Comandi con salvataggio su file

```bash
# Salva in output e poi rimuovi duplicati (utile prima di passare a ffuf/amass)
sublist3r -d example.com -o subdomains_raw.txt
sort -u subdomains_raw.txt > subdomains_example.txt
```

