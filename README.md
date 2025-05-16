# 🦈 Projet Wireshark - Analyse réseau sur VMs Linux

## 🎯 Objectif du projet

Découvrir l’analyse réseau avec **Wireshark** et **tshark** sous **Linux**, en capturant, filtrant, et analysant différents types de paquets réseau (ARP, TCP, UDP, DNS, etc.). Ce projet permet aussi de mieux comprendre les **couches du modèle OSI** et le fonctionnement des **protocoles réseau**.

---

## 🧰 Présentation de Wireshark

**Wireshark** est un outil graphique de capture réseau qui permet :
- de surveiller les paquets en temps réel,
- de les filtrer selon différents critères,
- de décoder leur contenu selon les couches du modèle OSI (Ethernet, IP, TCP...).

### 🧪 tshark (version en ligne de commande)

**tshark** est la version terminal de Wireshark. Il permet :
- de capturer des paquets directement en ligne de commande,
- d’automatiser les analyses réseau via des scripts.

---

## ❓ Réponses aux questions du sujet

### 📌 Quelle est la différence entre une trame et un paquet ?
- **Trame** = niveau **liaison (couche 2)** → elle contient les **adresses MAC**, utilisée dans les réseaux Ethernet.
- **Paquet** = niveau **réseau (couche 3)** → il contient les **adresses IP**, utile pour le routage.

### 📌 Qu’est-ce que le format pcap / pcapng ?
- Ce sont des **formats de fichiers de capture** utilisés par Wireshark et tshark.
- `.pcap` est l’ancien format, `.pcapng` est plus récent (supporte plus d'infos : noms d'interfaces, commentaires, etc.).

---

## 🖥️ Environnement utilisé

- 2 VMs sous **Debian** : une en **serveur** (avec services installés), une en **client**.
- Réseau configuré en **NAT** ou **Bridge** pour la capture réseau.

---

## ⚙️ Étapes de capture Wireshark

### 1. 🧱 Installation

```bash
sudo apt update
sudo apt install wireshark tshark -y
````

Autoriser l'utilisateur courant :

```bash
sudo usermod -aG wireshark $USER
```

Puis redémarrer la session ou faire :

```bash
newgrp wireshark
```

### 2. 📡 Lancer Wireshark (ou tshark)

```bash
sudo wireshark &
```

OU pour la ligne de commande :

```bash
sudo tshark
```

---

## 🧪 Captures demandées

### 🔎 Captures à faire avec Wireshark

Filtrer les paquets :

* ARP → `arp`
* TCP → `tcp`
* UDP → `udp`

Exemple d’analyse de trame TCP :

* **MAC source** : `08:00:27:ab:cd:ef`
* **IP source** : `192.168.1.10`
* **MAC destination** : `08:00:27:12:34:56`
* **IP destination** : `192.168.1.1`

---

## 🧬 Analyse OSI

Avec un paquet TCP :

1. **Couche 1** : Physique (non visible)
2. **Couche 2** : Liaison (Ethernet → adresses MAC)
3. **Couche 3** : Réseau (IP → adresses IP)
4. **Couche 4** : Transport (TCP ou UDP)
5. **Couche 7** : Application (HTTP, DNS, FTP...)

---

## 🔓 Exemples de protocoles à capturer

### ➤ ARP

Filtre : `arp`
Fonction : Résolution d’adresse IP en adresse MAC sur le LAN.

### ➤ TCP / UDP

Filtre : `tcp` ou `udp`
Fonction : Transport de données fiable (TCP) ou rapide mais sans vérification (UDP).

### ➤ DNS / mDNS

Filtre : `dns` ou `mdns`
Fonction DNS : Résolution de noms de domaines ([www.google.fr](http://www.google.fr) → IP)
Fonction mDNS : DNS local sans serveur (utilisé par exemple avec `.local`)

### ➤ DHCP

Filtre : `bootp`
Fonction : Attribution automatique d’IP par le serveur.

### ➤ FTP (non sécurisé)

Filtre : `ftp`
📌 Observation : les **identifiants de connexion (login/mot de passe)** peuvent être vus en clair dans les paquets → dangereux.

### ➤ TLS / HTTPS / SSL

Filtres :

* TLS : `tls`
* HTTPS : `http && tls`
* SSL : `ssl`

📌 Observation : les données sont **chiffrées**, on ne peut pas lire les identifiants → sécurisé.

---

## 📥 tshark – Capture par script

### ➤ Capture simple

```bash
sudo tshark -i eth0 -a duration:30 -w capture.pcapng
```

* `-i eth0` : interface réseau
* `-a duration:30` : capture pendant 30 secondes
* `-w` : écrit dans un fichier

### ➤ Capture filtrée (ex. : DNS uniquement)

```bash
sudo tshark -i eth0 -Y dns -w dns_only.pcapng
```

### ➤ Export texte lisible

```bash
tshark -r capture.pcapng -V > details.txt
```

---

## 🔁 Analyse hexadécimale

Outils en ligne pour analyser les paquets en hexadécimal :

* [https://hpd.gasmi.net/](https://hpd.gasmi.net/)
* [https://www.binaryhexconverter.com/](https://www.binaryhexconverter.com/)
* [https://www.base64decode.org/](https://www.base64decode.org/)
* [https://www.dcode.fr/md5-hash](https://www.dcode.fr/md5-hash)

---

## 🔄 Schéma de connexion TCP (3-way handshake)

```
Client ------ SYN ------> Serveur
Client <----- SYN-ACK --- Serveur
Client ------ ACK ------> Serveur
```

Ce mécanisme permet de **synchroniser** les deux machines avant d’échanger des données.

---

## 🔍 Utilisation des filtres Wireshark

Filtres d'affichage :

* `ip.addr == 192.168.1.10`
* `tcp.port == 80`
* `udp contains "DHCP"`
* `http.request.method == "GET"`

Ces filtres permettent de **trouver plus vite les trames utiles** dans la masse capturée.

---

## ✅ Conclusion

Ce projet m’a permis de :

* comprendre la **différence entre trame, paquet, segment**,
* observer le **fonctionnement des protocoles TCP/IP**,
* capturer et filtrer du trafic avec **Wireshark** et **tshark**,
* repérer des **données sensibles** transmises sans chiffrement (FTP),
* mettre en place une **analyse réseau automatisée sous Linux**
