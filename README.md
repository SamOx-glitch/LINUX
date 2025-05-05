# LINUX

DHCP Protokoll (vergibt IP adressen)
ip = 4 bit
MAC = 6 bit
Allgemeiner Boradcast 255.255.255.255
Zielport udp 67
Absendeport 68
MAC-Broadcast ff:ff:ff:ff:ff:ff 

Client -> Server
Discover -> Offer -> Request -> Acknowledge
   C          S        C            S



-------------------------------------------------
Linux Befehle
-------------------------------------------------

nano /etc/services (tcp & udp Protokoll)

ip a (zeigt alle ip & die MAC an)

nano /etc/network/interfaces   (Bauen eines NIC in den switch "spring" ein (IP-172.20.50.1/24))
>auto enp0s8
>iface enp0s8 inet static
>address 172.20.50.1/24


--------------------------------------------------
DHCP Server einrichten
--------------------------------------------------
apt install isc-dhcp-server

nano /etc/dhcp/dhcpd.conf
>ddns-updates off;
>default-lease-time 1200;
>max-lease-time 1800;
>subnet 172.20.50.0 netmask 255.255.255.0 {
>        range 172.20.50.11 172.20.50.100;
>        option routers 172.20.50.1;
>        option damin-name-servers 8.8.8.8;
>}


nano /etc/default/isc-dhcp-server
>ipv4 = enp0s8

systemctl restart isc-dhcp-server.service

ss -una


