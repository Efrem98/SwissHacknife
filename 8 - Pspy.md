# 👀 pspy – Process Spy

Appunti e snippet per usare **pspy** nella fase di **Privilege Escalation** su Linux.
Ti permette di monitorare in tempo reale i processi e gli script che girano sul sistema **senza permessi root**.

---

## 📌 Obiettivi

* Individuare **cronjob sospetti** o script automatizzati.
* Rilevare **processi lanciati come root** o altri utenti privilegiati.
* Intercettare **comandi sensibili** e file accessibili che possono portare ad escalation.
* Capire cosa accade “dietro le quinte” mentre sei dentro la macchina.

---

## 📥 Download & Trasferimento

### 🔎 Scegli la versione giusta

* `pspy64` → sistemi **x86\_64** (64 bit).
* `pspy32` → sistemi **x86** (32 bit).
* Varianti `s` (es. `pspy64s`) = **static build** (non richiede librerie esterne).

### 💾 Scaricare direttamente sulla macchina target

```bash
wget -O /tmp/pspy64 https://github.com/DominicBreuker/pspy/releases/download/v1.2.1/pspy64
chmod +x /tmp/pspy64
```

### 📤 Trasferire da attaccante a target

```bash
# Con scp
scp pspy64 user@target:/tmp/pspy64
ssh user@target 'chmod +x /tmp/pspy64'
```

---

## ⚙️ Esecuzione Base

### Run standard

```bash
./pspy64
```

### Modalità più utili

* `-i <ms>` → intervallo di polling (default 100ms).
* `-p` → mostra i comandi dei processi.
* `-f` → monitora anche eventi filesystem.
* `-d <dir>` → osserva directory specifiche (ripetibile).
* `-c` → output colorato.

---

## 📋 Esempi pratici

```bash
# Profilo classico: processi + file system, refresh ogni 1s
./pspy64 -pf -i 1000

# Focus su cron e /tmp
./pspy64 -pf -i 750 -d /etc/cron.d -d /var/spool/cron -d /tmp

# Solo output root o cronjob
./pspy64 -pf -i 1000 | grep -E 'root|/etc/cron'
```

---

## 🔍 Cosa osservare

* **Cronjob di root** → script in `/etc/cron.*` o `/var/spool/cron`.
* **Script world-writable** → se vedi uno script che gira come root e risiede in `/tmp` o con permessi larghi, bingo.
* **Processi con interpreti** (bash, python, perl) → spesso vulnerabili a *path hijacking*.
* **Servizi con sudo** senza password → facili da abusare.

---

## 🛡️ OPSEC & Pulizia

* Esegui da `/dev/shm` o `/tmp` per lasciare meno tracce.
* Dopo l’uso:

```bash
shred -u /tmp/pspy64   # oppure rm -f /tmp/pspy64
```

* Usa la versione statica (`pspy64s`) se mancano librerie.

---
