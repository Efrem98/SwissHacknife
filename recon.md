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

Dimmi quando vuoi passare il prossimo walkthrough: **aggiungerò solo ciò che non è già nel file**.
```
