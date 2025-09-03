# 🔑 Passwords & Cracking

Appunti e snippet per la fase di **cracking password e gestione hash**.

## 📌 Obiettivi

* Identificare e preparare correttamente gli hash
* Craccare password con **John the Ripper** e **Hashcat**
* Generare e modificare wordlist realistiche
* Eseguire spraying controllato su servizi di rete
* Conoscere l’uso storico delle rainbow tables

---

## 🦾 John the Ripper (Jumbo)

**Perché usarlo:**
È lo strumento “coltellino svizzero” del cracking. Supporta moltissimi formati di hash e include tool per **estrarli** da file reali (archivi, chiavi SSH, database, PDF, ecc.).

**Opzioni principali:**

* `--single` → attacco basato su user/metadati
* `--wordlist` → usa dizionario
* `--rules` → applica regole di trasformazione
* `--mask` → brute force basato su pattern
* `--format` → specifica tipo di hash
* `--session` / `--restore` → gestisce sessioni lunghe
* `--show` → mostra hash già crackati

---

### 📦 Estrarre hash con \*2john

Molti target **non contengono hash diretti**, ma file protetti da password. John include vari tool (o script) che convertono questi file in hash crackabili.

**Comandi comuni:**

```bash
# Unix: unisci passwd e shadow
unshadow /etc/passwd /etc/shadow > shadow.txt

# Archivi compressi
zip2john file.zip > zip.hash
rar2john file.rar > rar.hash
7z2john.pl file.7z > 7z.hash

# Documenti
pdf2john.py file.pdf > pdf.hash
office2john.py file.docx > office.hash

# Database
keepass2john database.kdbx > kdbx.hash

# SSH keys
ssh2john id_rsa > ssh.hash

# Git repositories
git2john .git > git.hash
```

👉 Questi comandi producono un file `.hash` compatibile da passare a `john`.

---

### 🚀 Cracking con John

**Esempi pratici:**

```bash
# Modalità Single
john --single zip.hash --format=zip

# Dizionario + regole
john --wordlist=rockyou.txt --rules zip.hash --format=zip

# Brute force con mask
john zip.hash --mask=?l?l?l?l?d?d --format=zip
```

---

### 💾 Salvataggio e ripresa sessioni

```bash
# Avvia sessione
john --session=myzip --wordlist=rockyou.txt --rules zip.hash --format=zip

# Riprendi sessione
john --restore=myzip

# Vedi password crackate
john --show zip.hash
```
---

### 📦 Estrarre gli hash (tool “*2john*”)

> Generano un *hash file* compatibile.

**Sistemi operativi**

```bash
# Unix /etc/shadow
unshadow /etc/passwd /etc/shadow > shadow.txt
```

**Archivi compressi**

```bash
zip2john file.zip > zip.hash
rar2john file.rar > rar.hash
7z2john.pl file.7z > 7z.hash
```

**Documenti & Office**

```bash
pdf2john.py file.pdf > pdf.hash
office2john.py file.docx > office.hash
```

**Database & applicazioni**

```bash
keepass2john database.kdbx > kdbx.hash
git2john .git > git.hash
```

**Chiavi & sistemi cifrati**

```bash
ssh2john id_rsa > ssh.hash
gpg2john secret.gpg > gpg.hash
bitlocker2john image.vhd > bitlocker.hash
truecrypt2john volume.tc > truecrypt.hash
veracrypt2john volume.vc > veracrypt.hash
androidbackup2john backup.ab > android.hash
```

**Active Directory & Kerberos**

```bash
# Ticket Kerberos
krb5tgs2john file.kirbi > tgs.hash
krb5pa2john file.kirbi > pa.hash
```
### 🔑 Workflow pratico con John (post-estrazione)

**1️⃣ Estrai l’hash con lo strumento giusto**

```bash
zip2john file.zip > zip.hash
rar2john file.rar > rar.hash
pdf2john.py file.pdf > pdf.hash
keepass2john database.kdbx > kdbx.hash
```

**2️⃣ Avvia John sul file .hash**

* Modalità wordlist + rules (la più usata):

```bash
john --wordlist=rockyou.txt --rules zip.hash --format=PKZIP
```

* Modalità mask (pattern mirato):

```bash
john zip.hash --mask=?u?l?l?l?l?d?d --format=PKZIP
```

**3️⃣ Mostra la password trovata**

```bash
john --show zip.hash
```

👉 Output tipico:

```
file.zip/password123
```
---

## ⚡ Hashcat

**Perché usarlo:**
Il cracking più veloce su GPU. Ideale per attacchi **mask** e **dizionari+regole**.

**Opzioni principali:**

* `-m` → modalità hash (es. `1000` per NTLM, `1800` per sha512crypt)
* `-a` → attacco (0=wordlist, 3=mask, 6/7=ibridi)
* `-r` → regole
* `--status` → mostra progressi
* `--show` → mostra hash risolti

### 🔹 Comandi diretti (solo output su CLI)

```bash
# NTLM con wordlist+rules
hashcat -m 1000 -a 0 ntlm.txt rockyou.txt -r rules/best64.rule

# WPA2 handshake convertito a .hc22000
hashcat -m 22000 -a 3 wifi.hc22000 '?d?d?d?d?d?d?d?d'

# Bcrypt (lento → ottimizza wordlist)
hashcat -m 3200 -a 0 bcrypt.txt rockyou.txt -r rules/dive.rule
```

### 💾 Comandi con salvataggio su file

```bash
hashcat -m 1000 -a 0 ntlm.txt rockyou.txt -r rules/best64.rule -o cracked_ntlm.txt
```

---

## 🧬 Wordlist Mangling & Generazione

**Perché usarlo:**
Trasforma dizionari standard (es. `rockyou.txt`) in liste più realistiche e mirate al target.

**Strumenti utili:**

* `cewl` → estrae parole da siti web
* `cupp` → profiling interattivo
* `rsmangler` → varianti (leet, anni, delimitatori)
* `crunch` / `maskprocessor` → generazione pattern
* `pipal` / `PACK` → analisi statistiche per creare maschere

### 🔹 Comandi diretti

```bash
# Cewl: estrai parole da un sito
cewl -d 2 -m 5 -w cewl.txt https://target.tld

# Rsmangler
rsmangler --file base.txt --output mangled.txt --leetspeak --years 1990-2025

# Crunch: 8 caratteri alfanumerici
crunch 8 8 abcdefghijklmnopqrstuvwxyz0123456789 -o crunch.txt

# Analisi password già crackate
pipal cracked.txt > stats.txt
```

---

## 🌐 Password Spraying

**Perché usarlo:**
Testa **una password comune su tanti utenti** evitando lockout. Da usare solo in scenari autorizzati.

**Opzioni principali:**

* Limitare i tentativi → 1 password/utente ogni 30–60 minuti
* Usare sleep/jitter tra batch
* Applicare a SSH, SMB, HTTP, Kerberos

### 🔹 Comandi diretti

```bash
# SSH (Hydra)
hydra -L users.txt -p Estate2025 ssh://10.10.10.10 -t 4

# SMB (CrackMapExec)
cme smb 10.10.10.0/24 -u users.txt -p "Password123"

# Kerberos (kerbrute)
echo Password2025! | kerbrute passwordspray -d corp.local --dc 10.10.10.5 users.txt
```

---

## 🌈 Rainbow Tables

**Perché usarle:**
Oggi poco diffuse (hash salati le rendono inutili), ma storicamente utili per **LM/NTLM/MD5**.

**Strumenti tipici:**

* `rcracki_mt` → cracking con rainbow tables
* `ophcrack` → GUI per LM/NTLM legacy

### 🔹 Comandi diretti

```bash
# Genera tabella rainbow
rtgen md5 loweralpha 1 8 0 2400 8000000 0
rtsort .

# Usa la tabella
rcracki_mt . -h <hash>
```

### 📡 Wi-Fi WPA2/PMKID

**Perché usarlo:**
Il cracking di handshake Wi-Fi (WPA2/WPA3-PMKID) è un classico in CTF.
Serve convertire il capture `.pcapng` in formato compatibile prima di passarlo a John o Hashcat.

```bash
# Conversione handshake/PMKID
hcxpcapngtool capture.pcapng -o wifi.hc22000

# Cracking con John (OpenCL se supportato)
john wifi.hc22000 --wordlist=rockyou.txt --format=wpapbkdf2

# In alternativa con Hashcat
hashcat -m 22000 -a 0 wifi.hc22000 rockyou.txt
```
