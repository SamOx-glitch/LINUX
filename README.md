linux_notes = """
🐧 Linux Grundlagen & Netzwerk

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

🌐 Netzwerk konfigurieren (statische IP)

/etc/network/interfaces:

auto enp0s8
iface enp0s8 inet static
    address 172.20.50.1/24

------------------------------------------------------------

🧱 RAID mit mdadm

apt install mdadm

mdadm -C /dev/md127 -l1 -n2 /dev/sdb /dev/sdc -x1 /dev/sdd

mkfs.ext4 /dev/md127

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

------------------------------------------------------------

🧪 Bash-Skript: Warnung bei voller Partition

Datei: hd.sh

#!/bin/bash
HD=$(df -h | grep sda1 | tr -s " " | cut -d " " -f5 | cut -d% -f1)

if [ "$HD" -gt 5 ]; then
    echo "WARNUNG: Die Partition /dev/sda1 ist zu $HD% voll!" | mail -s "HD-Warning" user@example.com
fi

chmod +x hd.sh
./hd.sh
"""

# Save to txt file
file_path = "/mnt/data/Linux_Grundlagen_Notizen.txt"
with open(file_path, "w") as file:
    file.write(linux_notes)

file_path

# Die bisherigen Notizen werden um zusätzliche sinnvolle Bereiche erweitert.

additional_notes = """
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
"""

# Datei um neue Inhalte ergänzen
file_path = "/mnt/data/Linux_Grundlagen_Notizen_erweitert.txt"
with open(file_path, "a") as file:
    file.write(additional_notes)

file_path
