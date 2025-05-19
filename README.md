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

getent passwd               # alle nutzer anzeigen lassen

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

chgrp web-admins /srv/www   (web-admins als Eingetümer von /srv/www mit Vollzugriff)

------------------------------------------------------------

🧱 RAID mit mdadm

apt install mdadm

mdadm -C   md127      -l1             -n2 /dev/sd[b-c]                  -x1 /dev/sdd
           legende     raid level       wieviel platten ohne spare            spare


lsblk (alle festplatten anzeigen lassen)

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

------------------------------------------------------------------------------------
*KEY ERSTELLT* 
apt install bind9
tsig-keyg
tsig-keygen VOGEL >vogel.fliegt
cp VOGEL /etc/dhcp/             (DHCP = dhcp)
cp VOGEL /etc/bind/             (DNS = bind)


*DHCP SERVER EINRICHTEN SCHRITTANLEITUNG*   
(prüfen wenn problem bei Namensauflösung)

1) nano /etc/dhcp/dhcpd.conf

ddns-update-style standard;
default-lease-time 600;
max-lease-time 7200;
subnet 172.25.200.0 netmask 255.255.255.0 {
    include "/etc/dhcp/vogel.key";
    range 172.25.200.10 172.25.200.100;
    option routers 172.25.200.1;
    option domain-name-servers 172.25.200.1;
    option domain-name "vogel.fliegt";
    zone vogel.fliegt. {
    primary 172.25.200.1;
    key VOGEL;
    }
    zone 200.25.172.in-addr-arpa. {
    primary 172.25.200.1;
    key VOGEL;
    }
}

2) nano /etc/default/isc-dhcp-server

INTERFACESv4 = "enp0s8"

MASQUERADE AKTIVIEREN auf router (Client maskieren damit wir ins Internet können)

nano /etc/sysctl.conf
>net.ipv4.ip_forward=1 (entkommentieren)

apt install iptables

iptables -t nat -A POSTROUTING -o enp0s3 -j MASQUERADE
(enp0s8 geht zum client enps03 geht ins internet)


nano /etc/rc.local

>#!/bin/bash
>iptables -t nat -A POSTROUTING -o enp0s3 -j MASQUERADE  (um den Traffic auch wieder zurückzubekommen (tcp))

ls -l /etc/rc.local

chmod a+x /etc/rc.local  (chmod 755 /etc/rc.local)

. /etc/rc.local

*NAMESERVER EINRICHTEN SCHRITTANLEITUNG*

1) nano /etc/bind/named.conf.options

forwarders {        (default is kommentiert also entkommentieren)
8.8.8.8;
};

2) nano /etc/bind/named.conf.local   (inkludiere die key datei in )

include "/etc/bind/vogel.key";     (nutzung der key Datei)

# key "VOGEL" {              (alternative zum include)
#    algorithm HMAC-SHA256;
#    secret

zone "vogel.fliegt" {
    type master;
    file "vogel.fwd";
    allow-update { key VOGEL; }; 
    };

zone "200.25.172.in-addr-arpa" {
    type master;
    file "vogel.rev";
    allow-update { key VOGEL; };
    };


less /etc/bind/named.conf.options     (directory um zu schauen wo der speicherpfad ist)

cp /etc/bind/db.empty /var/cache/bind/vogel.fwd
cp /etc/bind/db.empty /var/cache/bind/vogel.rev

nano /var/cache/bind/vogel.fwd

$TTL 86400
@    IN    SOA    ns1.vogel.fliegt. root (
                    1         ;Serial
                    604800    ;Refresh
                    86400     ;Retry
                    2419200   ;Expire
                    86400 )   ; Negative Cache TTL

     IN      NS     ns1.vogel.fliegt.
ns1  IN      A        172.25.200.1


nano /var/cache/bind/schoen.rev

$TTL 86400
@    IN    SOA      ns1.vogel.fliegt. root (
                    1         ;Serial
                    604800    ;Refresh
                    86400     ;Retry
                    2419200   ;Expire
                    86400 )   ; Negative Cache TTL

     IN      NS     ns1.vogel.fliegt.
1    IN      PTR    ns1.vogel.fliegt.

systemctl restart named.service
systemctl restart isc-dhcp-server.service


Um im eigenen router zu schauen ob die daten weitergeleitet werden muss man :
nano /etc/resolv.conf
>domain mai.baum
>search mai.baum
>nameserver 172.18.19.1

------------------------------------------------------------



Server untereinander verbinden und steuern

ssh =secure shell



ssh niklas@ns1 (ssh <nutzername>@nameserver)
ssh lale@ns1   (ein passwort muss bestehen (passwd lale))

less .ssh/known_hosts (auf client "root" alle keys)

ssh-keygen (auf client) (key erstellen)

ls -la .ssh/ (keys einsehen)

ssh-copy-id -i .ssh/eid niklas@www

ssh-keygen -t rsa -f .ssh/eid

ssh niklas@www (um mich auf dem zweiten server anzumelden, da er bei" ssh niklas@ns1 " automatisch den ersten erstellten key verwendet)

(dsa,ecdsa,ed25519,rsa = sind alles verschiedene verschlüsselungen)
ssh-keygen -t dsa -f .ssh/mpwd
ssh-keygen -t ecdsa
ssh-keygen -t ed25519
ssh-keygen -t rsa 


ssh-agent = keys verwalten 
(
ssh-add .ssh/id_rsa
ssh-add .ssh/id_dsa
ssh-add .ssh/id_ecdsa
ssh-add .ssh/id_ed25519
fügt den key dem keyagent hinzu 
)

ssh-copy-id -i .ssh/id_rsa lina@ns1
ssh-copy-id -i .ssh/id_dsa lina@ns1
ssh-copy-id -i .ssh/id_ecdsa lina@ns1
ssh-copy-id -i .ssh/id_ed25519 lina@ns1

dann login 

auf server verzeichnis erstellen auf einem server auf das ich das backup schicke


mkdir /var/backups/debxfc-home
nano /etc/group
chmod 770 /var/backups/debxfc-home
chgrp backup /var/backups/debxfc-home

rsync -azv  /home/* undine@www:/var/backups/debxfc-home/    (Homeverzeichnis als backup an undine ins verzeichnis schicken)

backup wieder herstellen
rsync -azv undine@www:/var/backups/debxfc-home/ /home/      (Backup von verzeichnis zu Homeverzeichnis schicken)

azv = archiv zip visible
rsync = rekursiv syncronisation
       
------------------------------------------------------------
Backup
crontab -e  #benutzer crontab anpassen

0    */3    *    *    1-5    rsync -a /home/* /vvar/backups/home/ <e/ >/dev/null 2>&1

crontab -l  #benutzer crontab auflisten
crontab -r  # benutzer crontab löschen




------------------------------------------------------------
Archivierung

guip, bzip2, xz = compression Software

tar = tape archiver
ar - archive
t - tape

tar [-]<options>

c = create
f = file/archivname
x = extract 
t = table (inhalt Archiv)
z = gzip
j = bzip2
J = xz

du = disc used (summary, human readeble)
du -sh /usr/

cat = anschauen
-------------------------------------------------

ls -l

tar czf home.tar.gz /hom/

tar cjf home.tar.bz2 /home/

tar czf usr.tar.gz /usr/

tar cJf var.tar.xz /var/

tar xf ~/var.tar.xz  (die ~ tilde steht fürs Home directory)

file var.tar.gz  (um zu sehen was für einen Dateityp und was in der Datei drin ist)


rm /var/cache/man/sv/index.db   (datei löschen)
ls -l /var/cache/man/sv/        (datei sichten)
cd /                             (home verzeichnis)
tar xf /root/var.tar.gz (option 1)
tar xf ~/var-tar-gz   (option 2)

tar xf /root/var.tar.gz var/cache/man/sv/index.de  (wiederherstellen)

ls- l /var/cache/man/sv/

-------------------------------------------------

Verschlüsselung

gpg = gnu pgp
pgp = pretty good privacy

gpg = asynchrone Verschlüsselung
1. public key
2. privat key (secret key)
zusammen ist es ein schlüsselpaar = key-pair
(werden immer zusammen erzeugt)

GPG-VERSCHLÜSSELUNG – SCHRITT-FÜR-SCHRITT-ANLEITUNG

Ziel:
Eine Datei so verschlüsseln, dass nur eine bestimmte Person sie mit ihrem privaten Schlüssel lesen kann.

============================================================
1. Schlüsselpaar erstellen
------------------------------------------------------------
Befehl:
gpg --full-generate-key

Erklärung:
Erstellt ein neues Schlüsselpaar (privat + öffentlich). Du wirst nach Name, E-Mail, Ablaufdatum und Passwort gefragt. Der private Schlüssel bleibt geheim bei dir, der öffentliche wird später weitergegeben.

Public-key wird hier erzeugt
============================================================
2. Public Key exportieren
------------------------------------------------------------
Befehl:
gpg --export -a -o NAME.key

Erklärung:
Exportiert deinen öffentlichen Schlüssel im ASCII-Format in eine Datei (z. B. Friedrich.key), die du an andere weitergeben kannst, damit sie dir verschlüsselte Nachrichten senden können.
-a ASCII code
-o directory
============================================================
3. Schlüssel übergeben / kopieren
------------------------------------------------------------
Befehl:
cp NAME.key /tmp/

Erklärung:
Kopiert die Datei mit deinem öffentlichen Schlüssel in ein gemeinsames oder temporäres Verzeichnis (z. B. /tmp), damit ein anderer Nutzer sie importieren kann.

============================================================
4. Datei zum Verschlüsseln vorbereiten (Optional)
------------------------------------------------------------
Befehl:
echo "Geheime Nachricht" > datei.txt

nano datei.txt (um datei mit text zu füllen)

Erklärung:
Erstellt die Datei, die du verschlüsseln möchtest. Du kannst den Befehl auch durch einen Texteditor ersetzen oder eine bestehende Datei verwenden.

============================================================
5. Public Key des Empfängers importieren (davor muss ich auf dem neuen nutzer anmelden)
------------------------------------------------------------
Befehl:
gpg --import NAME.key

Erklärung:
Importiert den öffentlichen Schlüssel des Empfängers in dein GPG-Schlüsselbund, damit du Dateien für ihn verschlüsseln kannst.

============================================================
6. Datei verschlüsseln
------------------------------------------------------------
Befehl:
gpg -e -r "Name" datei.txt
gpg -e datei.txt

Erklärung:
Verschlüsselt die Datei „datei.txt“ für den Empfänger mit dem Namen „Name“ (so wie im Schlüssel angegeben). Das Ergebnis ist die Datei „datei.txt.gpg“, die nur der Empfänger mit seinem privaten Schlüssel entschlüsseln kann.
🔸 -e: encrypt
🔸 -r: Empfänger
============================================================
7. Datei weitergeben / kopieren
------------------------------------------------------------
Befehl:
cp datei.txt.gpg /tmp/

Erklärung:
Kopiert die verschlüsselte Datei in ein temporäres Verzeichnis oder einen Speicherort, auf den der Empfänger zugreifen kann.

============================================================
8. Datei holen als Empfänger
------------------------------------------------------------
Befehl:
cp /tmp/datei.txt.gpg .

Erklärung:
Der Empfänger kopiert die verschlüsselte Datei in sein eigenes Arbeitsverzeichnis.

============================================================
9. Datei entschlüsseln
------------------------------------------------------------
Befehl:
gpg -d datei.txt.gpg

Erklärung:
Der Empfänger entschlüsselt die Datei mit seinem privaten Schlüssel. Der Inhalt wird angezeigt oder (mit zusätzlicher Option) in eine neue Datei geschrieben.



niklas
1gpg --full-gen-key 
1gpg --export -a -o niklas.key 
1cp niklas.key /tmp/
3cp /tmp/dassi.txt.gpg .
3gpg -d dassi.txt.gpg

dassi
2gpg --import /tmp/niklas.key
2nano dassi.txt
2gpg -e dassi.txt   (public key eingeben)
2cp dassi.txt.gpg /tmp/

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
NOTIZEN
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

main configuration /etc/bind/
data bases         /var/cache/bind/
cd /etc/bind
less named.conf 
less named.conf.options

Diese Adressen sind in RFC 1918, Address Allocation for Private Internets definiert. Sie können diese privaten Adressen, die auch als 1918-Adressen bezeichnet werden, für Systeme in lokalen Netzwerken innerhalb eines Firmen-Intranets verwenden. Diese privaten Adressen sind jedoch im Internet nicht gültig.

less named.conf.local

hostname = rechnername

DHCP-Server                            DNS-Server
Clients <hostname>                  rev.
            <IP>                    <host-ip>    IN    PTR
                                    fwd.           IN    A

TSIG = Transaction signature

router 
tsig-keygen SCHOEN (key generieren)
tsig-keygen SCHOEN >schoen.key (key in datei umleiten)
                                less schoen.key (um den key zu sehen)
                                ls -l schoen.key (um die berechtigungen zu sehen)
                                cp schoen.key /etc/dhcp/
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

