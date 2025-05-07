linux_notes = """
🐧 Linux Grundlagen & Netzwerk

Legende:
NIC = Network Interface Card (Netzwerkkarte)
IP-Adresse = z.B. 192.168.1.10/24
Subnetzmaske = 255.255.255.0
Net-ID = Netzwerkanteil einer IP-Adresse

Beispiel:

    [ Rechner 1 ]
    +---------------------+
    | NIC 1               |
    | IP: 192.168.1.10    | <---+
    | Subnetz: /24        |     |
    +---------------------+     |
                                | gemeinsames
                                | Subnetz: 192.168.1.0/24
                                |
    +---------------------+     |
    | NIC 2               |     |
    | IP: 192.168.1.11    | <---+
    | Subnetz: /24        |
    +---------------------+
    [ Rechner 2 ]

Jede IP-Adresse im Subnetz 192.168.1.0/24 hat die:
- Net-ID: 192.168.1.0
- Host-ID: Der letzte Teil (z. B. .10, .11, …)

Maximale Hosts pro /24-Netz: 254
  (von .1 bis .254 – .0 = Netzadresse, .255 = Broadcast)

Hinweis:
- Alle Geräte im selben Subnetz können direkt miteinander kommunizieren.
- Unterschiedliche Subnetze benötigen ein Gateway/Router zur Kommunikation.

  
Zusammenfassung:
----------------------------------------------------
IP-Adresse:     192.168.1.10/25
Subnetzmaske:   255.255.255.128 (/25)
Invert-Maske:   0.0.0.127
Net-ID:         192.168.1.0
Broadcast:      192.168.1.127
Hostbereich:    192.168.1.1 – 192.168.1.126 (Range)
----------------------------------------------------

📡 DHCP-Protokoll

- Zweck: Automatische Vergabe von IP-Adressen
- IP-Adressen: 4 Byte  
- MAC-Adressen: 6 Byte
- Broadcast-IP: 255.255.255.255
- Broadcast-MAC: ff:ff:ff:ff:ff:ff
- Zielport (UDP): 67  
- Quellport (UDP): 68

Ablauf:

Client        Server
  | Discover  |
  |---------->|
  |           | Offer
  |<----------|
  | Request   |
  |---------->|
  |           | Acknowledge
  |<----------|

Client        Server
  | Entdecken  |
  |---------->|
  |           | Angebot
  |<----------|
  | Anfrage   |
  |---------->|
  |           | Anerkennung
  |<----------|
------------------------------------------------------------

🔧 Grundlegende Linux-Befehle

📂 Navigation im Dateisystem

pwd         # aktuelles Verzeichnis anzeigen
cd          # Verzeichnis wechseln
cd ~        # Home-Verzeichnis
cd /        # Root-Verzeichnis
cd ..       # Ein Verzeichnis höher

🗃️ Dateien und Verzeichnisse anzeigen

ls          # Inhalte auflisten
ls -l       # Details anzeigen
ls -la      # auch versteckte Dateien anzeigen

📄 Dateien erstellen und anzeigen

touch datei.txt        # Leere Datei erstellen
cat datei.txt          # Inhalt anzeigen
more datei.txt         # Seitenweise anzeigen
less datei.txt         # Interaktiv anzeigen
nano datei.txt         # Bearbeiten im Editor

📁 Verzeichnisse erstellen & löschen

mkdir ordner           # Neues Verzeichnis
rmdir leererOrdner     # Leeres Verzeichnis löschen
rm -r ordner           # Ordner inkl. Inhalt löschen

🔁 Dateien verschieben, kopieren, löschen

mv quelle ziel         # Verschieben oder umbenennen
cp quelle ziel         # Datei kopieren
cp -r ordner ziel      # Ordner rekursiv kopieren
rm datei.txt           # Datei löschen

------------------------------------------------------------

👤 Benutzerverwaltung

id                          # Benutzer-ID anzeigen
sudo <befehl>               # Als Admin ausführen
su                          # Benutzer wechseln (root)
exit                        # Sitzung beenden
hostname                    # Hostname anzeigen
hostnamectl set-hostname NEU # Hostname dauerhaft setzen

useradd -m benutzer         # Benutzer mit Home-Verzeichnis
passwd benutzer             # Passwort setzen
userdel benutzer            # Benutzer löschen
userdel -r benutzer         # inkl. Home-Verzeichnis löschen

------------------------------------------------------------

⏻ Systemsteuerung

shutdown -h now     # Sofort herunterfahren
shutdown -h +10     # In 10 Minuten herunterfahren
shutdown -r now     # Sofort neustarten
shutdown -r +5      # In 5 Minuten neustarten

------------------------------------------------------------

💾 Speicher prüfen

free           # RAM-Nutzung
free -h        # besser lesbar (MB/GB)

------------------------------------------------------------

🗂️ FHS – Dateisystemstruktur

/ - Root-Dateisystem
/bin - Systemprogramme
/boot - Bootloader & Kernel
/dev - Geräte
/etc - Konfigurationsdateien
/home - Benutzerverzeichnisse
/lib - Bibliotheken
/mnt - Temporäre Mountpoints
/proc - Virtuelles Dateisystem
/root - root's Home
/sbin - Admin-Programme
/tmp - Temporäre Dateien
/usr - Benutzerprogramme
/var - Logs, Caches

------------------------------------------------------------
Verzeichnis erstellen

mkdir -p /daten/innen   (parent verzeichnis erstellen)


------------------------------------------------------------
mount (verzeichnis einem device zuordnen)
nano /etc/fstab

/dev/VG/lv1    /daten/innen    ext4         defaults    0    0
/dev/VG/lv2    /daten/aussen    ext4        defaults    0    0
festplatte       verzeichnis    format    standard einstellungen

mount -a (die änderungen montieren/übernehmen)

------------------------------------------------------------
verzeichnis einer gruppe zuordnen

chgrp intern /daten/innen
chgrp extern /daten/aussen

------------------------------------------------------------
nano /etc/default/useradd   (bash shell zuordnen als default)

nano /etc/group             (User hinzufügen)

d------------------------------------------------------------
🧱 RAID mit mdadm

apt install mdadm

mdadm -C /dev/md127      -l1             -n2 /dev/sd[b-c]         -x1 /dev/sdd
           legende     raid level       wieviel platten ohne spare            spare


-Logisches Volume Managment

1.pvcreate     /dev/md127                                      (Platten Volumen erstellen)
         device = md127
2.vgcreate        VG                    /dev/md127             (Volumen Gruppe erstellen)
        Volumen gruppen name           device   

3.lvcreate -n <name>   -L<Größe>   <Volume Gruppe>             (Logisches Volume erstellen)
            name      M,G,T     name von Volume Gruppe

lvdisplay    (Status des Volumes anzeigen)

ls -l /dev/VG     (device mapper)

-Formatieren (Formatsystem/speichersystem-ext4ls)

mkfs.ext4 /dev/md127   (wenn ich den RAID formatieren möchte)
*bei der RAID formatierung wird das logische Volumen zurückgesetzt)

mfks.ext4 /dev/VG/l1   (wenn ich das Volumen formatieren möchte)

------------------------------------------------------------

🌐 Netzwerk konfigurieren (statische IP)

/etc/network/interfaces:

>auto enp0s8
>iface enp0s8 inet static
>    address 172.20.50.1/24

ifup enp0s8

------------------------------------------------------------
📡 DHCP-Server einrichten

apt install isc-dhcp-server

/etc/dhcp/dhcpd.conf:

ddns-updates off;
default-lease-time 1200;
max-lease-time 1800;
subnet 172.20.50.0 netmask 255.255.255.0 {
    range 172.20.50.11 172.20.50.100;
    option routers 172.20.50.1;
    option domain-name-servers 8.8.8.8;
}

/etc/default/isc-dhcp-server:

INTERFACESv4="enp0s8"

systemctl restart isc-dhcp-server.service
ss -una


auf router 

nano /etc/sysctl.conf
>net.ipv4.ip_forward=1 (entkommentieren)

apt install iptables

iptables -t nat -A POSTROUTING -o enp0s3 -j MASQUERADE
(enp0s8 geht zum client enps03 geht ins internet)


nano /etc/rc.local

>#!/bin/bash
>iptables -t nat -A POSTROUTING -o enp0s3 -j MASQUERADE  (um den Traffic auch wieder zurückzubekommen (tcp))

ls -l /etc/rc.local

chmod a+x /etc/rc.local

. /etc/rc.local

------------------------------------------------------------
systemctl restart NetworkManager.service (wenn man google per ping nicht erreicht auf der VM (nicht auf dem router))
------------------------------------------------------------
DNS

ROOT-Server

A fully qualified domain name (FQDN), sometimes also called an absolute domain name, is a domain name that specifies its exact location in the tree hierarchy of the Domain Name System (DNS). 

  www.  google . de.   = fqdn
1.top-level-domain  gov,com,edu,org | an,at,de,tv 
2.sub-level-domain google,t-online,1&1 (alles nach dem ersten punkt und vor dem letzten)
3.Hostname         www , hostname (alles vor dem ersten punkt)

------------------------------------------------------------
Domains

dig  (domain information grabber)

dig t-online.de 

SOA = start of authority

A  = address (ipv4)
NS = Nameserver
MX = mailexchanger
AAAA = ipv6
PTR = 

host www.vodafone.de (forward auflösung) zeigt ip an 

host 139.7.147.41 (reverse auflösung) zeigt die domain an 

SOA-Datensatz

ttl = time to live

<domain>.       ttl        <class>          <typ>        <(Master)Name-Server>                <e-mail-admin>
t-online.de     86400         IN            SOA          dns00.dns.t-ipnet.de.        dns.telekom.de.     (

2025050600  ; serial number      (letzte DNS bearbeitung)
     10800  ; refresh time       (
      1800  ; retry time         (
   3024000  ; expire time        (ablaufdatum)
      3600  ; neg.ttl            (wenn nach einem Rechner gefragt wurde der nicht in der Domain ist wir diese anfrage für eine stunde im Cache gespeichert)
)
                            IN                NS        dns00.dns.t-ipnet.de.
                            IN                NS        dns182.t-online.de.
dns                         IN                A           194.2.16.170
www                         IN                A            12.144.33.8

less named.conf
less nameld.conf.default-zones
less /usr/share/dns/root.hints
less db.local


------------------------------------------------------------

🧰 Paketverwaltung (Debian/Ubuntu – apt)

apt update                 # Paketquellen aktualisieren
apt upgrade                # Alle Pakete aktualisieren
apt install <paket>        # Paket installieren
apt remove <paket>         # Paket deinstallieren
apt search <begriff>       # Paket suchen
apt show <paket>           # Paketinformationen anzeigen
apt autoremove             # Nicht mehr benötigte Pakete entfernen

dpkg -l                    # Liste installierter Pakete
dpkg -i paket.deb          # .deb-Paket installieren
dpkg -r paket              # .deb-Paket entfernen

------------------------------------------------------------

📦 Archivdateien & Komprimierung

tar -cvf archiv.tar verzeichnis/     # Archiv erstellen
tar -xvf archiv.tar                  # Archiv entpacken
tar -czvf archiv.tar.gz verzeichnis/ # Archiv + Komprimierung
tar -xzvf archiv.tar.gz              # Entpacken

gzip datei.txt                       # Datei komprimieren
gunzip datei.txt.gz                  # Datei dekomprimieren

zip -r archiv.zip verzeichnis/      # ZIP erstellen
unzip archiv.zip                    # ZIP entpacken

------------------------------------------------------------

🔒 Rechte & Eigentümer

ls -l                               # Zeigt Berechtigungen
chmod 755 datei.sh                  # Rechte ändern (z. B. rwxr-xr-x)
chown benutzer:gruppe datei.txt     # Eigentümer ändern

chmod +x datei.sh                   # Datei ausführbar machen

------------------------------------------------------------

📈 Prozesse & Dienste überwachen

top                                 # Prozesse in Echtzeit
htop                                # (besseres top, ggf. installieren)
ps aux                              # Alle laufenden Prozesse
kill <PID>                          # Prozess beenden
killall <prozessname>              # Alle gleichnamigen Prozesse beenden

systemctl status <dienst>          # Status eines Dienstes
systemctl start <dienst>           # Dienst starten
systemctl stop <dienst>            # Dienst stoppen
systemctl enable <dienst>          # Autostart aktivieren
systemctl disable <dienst>         # Autostart deaktivieren
systemctl restart <dienst>         # Dienst neustarten

journalctl -xe                     # Systemd-Logs anzeigen

------------------------------------------------------------

🌐 Netzwerkanalyse & Tools

ip a                               # Netzwerkinterfaces & IP-Adressen
ip r                               # Routing-Tabelle
ping <ziel>                        # Erreichbarkeit testen
traceroute <ziel>                 # Route zum Ziel anzeigen
nslookup <domain>                 # DNS-Abfrage
dig <domain>                      # DNS-Abfrage (detaillierter)
netstat -tuln                     # Offene Ports (älteres Tool)
ss -tuln                          # Offene Ports (neuer, moderner)

nmap <ziel>                       # Portscan (ggf. installieren)

------------------------------------------------------------
🧪 Bash-Skript: Warnung bei voller Partition
------------------------------------------------------------

Datei: hd.sh

nano /hd.sh

#!/bin/bash
HD=$(df -h | grep sda1 | tr -s " " | cut -d " " -f5 | cut -d% -f1)

if [ "$HD" -gt 5 ]; then
    echo "WARNUNG: Die Partition /dev/sda1 ist zu $HD% voll!" | mail -s "HD-Warning" user@example.com
fi

chmod +x hd.sh

./hd.sh

------------------------------------------------------------
# 🧪 Bash: Beispiele mit if, else, elif, while
------------------------------------------------------------

# Beispiel 1: Einfache Bedingung
#!/bin/bash

echo "Gib eine Zahl ein:"
read zahl

if [ "$zahl" -gt 10 ]; then
    echo "Die Zahl ist größer als 10"
else
    echo "Die Zahl ist 10 oder kleiner"
fi

------------------------------------------------------------
# Beispiel 2: Mehrere Bedingungen mit elif
------------------------------------------------------------
#!/bin/bash

echo "Wie ist das Wetter? (sonnig/regnerisch/bewölkt)"
read wetter

if [ "$wetter" = "sonnig" ]; then
    echo "Geh nach draußen ☀️"
elif [ "$wetter" = "regnerisch" ]; then
    echo "Nimm einen Schirm ☔"
elif [ "$wetter" = "bewölkt" ]; then
    echo "Vielleicht wird's noch schön ⛅"
else
    echo "Ungültige Eingabe"
fi


------------------------------------------------------------
# Beispiel 3: Benutzer abfragen
------------------------------------------------------------
#!/bin/bash

echo "Benutzername:"
read name

if id "$name" &>/dev/null; then
    echo "Benutzer $name existiert"
else
    echo "Benutzer $name existiert NICHT"
fi

------------------------------------------------------------
# Beispiel 4: While-Schleife mit if
------------------------------------------------------------
#!/bin/bash

count=1

while [ $count -le 5 ]; do
    if [ $((count % 2)) -eq 0 ]; then
        echo "$count ist gerade"
    else
        echo "$count ist ungerade"
    fi
    count=$((count + 1))
done


------------------------------------------------------------
# Beispiel 5: Datei prüfen
------------------------------------------------------------
#!/bin/bash

echo "Pfad zur Datei eingeben:"
read dateipfad

if [ -f "$dateipfad" ]; then
    echo "Datei existiert: $dateipfad"
else
    echo "Datei existiert nicht"
fi

------------------------------------------------------------

