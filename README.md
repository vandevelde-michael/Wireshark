
# Documentation Projet Wireshark

## Partie 1 – Découverte de Wireshark

### Qu'est-ce que Wireshark ?
Wireshark est un analyseur de paquets réseau open-source. Il permet de capturer, analyser et interpréter le trafic réseau sur une interface donnée. Il fonctionne en mode graphique, et son pendant en ligne de commande est `tshark`.

### Trame vs Paquet
- **Trame** : unité de donnée de la couche liaison (niveau 2 OSI), contenant les adresses MAC.
- **Paquet** : unité de donnée de la couche réseau (niveau 3 OSI), contenant les adresses IP.

Format pcap/pcapng
- **pcap** : format standard de capture de paquets.
- **pcapng** : format plus récent et plus complet, supporte des métadonnées supplémentaires.

Installation de Wireshark
```bash
sudo apt update
sudo apt install wireshark
sudo wireshark
```
*Lancer en root permet d'accéder aux interfaces réseau.*

Captures de paquets
Interface utilisée : ens33 (interface NAT dans les VM)

Filtres d'affichage :
- ARP : `arp`
- UDP : `udp`
- TCP : `tcp`

Analyse de trames
Pour chaque capture, identifier les éléments suivants :
- Adresse MAC source/destination
- Adresse IP source/destination
- Ports (UDP/TCP)

Découverte d'autres paquets
Lors des captures, on observe aussi :
- DNS : `dns`
- mDNS : `mdns`
- ICMP : `icmp`
- HTTP/HTTPS : `http`, `tls`

Spécifications des paquets
  ARP
- Aucune couche transport
- Utilise le broadcast
- Permet d'associer une adresse IP à une adresse MAC

  UDP
- Protocole non connecté
- Léger, sans contrôle d'erreur

  TCP
- Protocole connecté
- Utilise la connexion en 3 étapes : SYN, SYN-ACK, ACK

 Diagramme TCP
```
Client   ------------------->  Serveur : SYN
Client   <-------------------  Serveur : SYN-ACK
Client   ------------------->  Serveur : ACK
```

 Utilisation des filtres Wireshark
Filtres d'affichage utiles :
- Par protocole : `arp`, `tcp`, `udp`, `dns`, `tls`
- Par IP : `ip.addr == 192.168.1.10`
- Par port : `tcp.port == 80`

 Partie 2 – Capture sur réseau local

Configuration : VM client et VM serveur en NAT.

  Protocoles à tester
| Protocole | Action/Test | Filtre Wireshark |
|----------|-------------|------------------|
| ARP | `ping` local | `arp` |
| DHCP | Démarrage VM sans IP | `bootp` |
| DNS | `dig` ou `nslookup` | `dns` |
| mDNS | `avahi-daemon` actif | `mdns` |
| FTP | Serveur vsftpd | `ftp` |
| SMB | Partage Windows | `smb` |
| SSL | Navigation HTTPS | `ssl` |
| HTTPS | `curl https://...` | `tls` |
| TLSv1.2 | Accès site sécurisé | `tls` |

 Sauvegarde des paquets
- Utiliser "Fichier > Enregistrer sous" au format `.pcapng`
- Donner un nom correspondant : `capture_ftp.pcapng`, etc.

 Interprétation FTP vs SSL
- En FTP sans TLS : les mots de passe sont visibles en clair dans Wireshark.
- En HTTPS/SSL : les données sont chiffrées, les mots de passe sont illisibles.

 Partie 3 – tshark

 Installation
```bash
sudo apt install tshark
```

Commandes utiles
- Capture UDP :
```bash
sudo tshark -i ens33 -f "udp" -w udp_capture.pcap
```
- Afficher noms DNS :
```bash
sudo tshark -i ens33 -Y dns -T fields -e dns.qry.name
```
- Capture filtrée FTP :
```bash
sudo tshark -i ens33 -Y ftp -w ftp_capture.pcap
```

  Options utiles
| Option | Description |
|--------|-------------|
| `-i` | Interface |
| `-f` | Filtre de capture (niveau bas) |
| `-Y` | Filtre d'affichage (niveau haut) |
| `-w` | Sauvegarde dans un fichier .pcap |
| `-T` | Type de sortie (text, fields...) |
| `-e` | Champ à afficher |

---

## Conclusion
Ce projet a permis de mettre en pratique les compétences d'analyse réseau avec Wireshark et tshark. En comparant les protocoles, leurs fonctionnements, et les données transportées, on comprend l'importance de la capture réseau pour la sécurité et le diagnostic.
