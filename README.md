# Jarkom-Modul-3-D09-2022

## Anggota Kelompok D09
| No | Nama | NRP |
| ------ | ------ | ----- |
| 1 | Monica Narda Davita | 5025201009 |
| 2 | Alya Shofarizqi Inayah | 5025201113 |
| 3 | Meisya Salsabila Indrijo Putri | 5025201114 |

## Topologi
![topologi](https://user-images.githubusercontent.com/96837287/201667955-ba5d5b9d-97e9-488b-aa00-0ec0d80f8073.jpeg)

## SOAL 1:
### Deskripsi:
Loid bersama Franky berencana membuat peta tersebut dengan kriteria WISE sebagai DNS Server, Westalis sebagai DHCP Server, Berlint sebagai Proxy Server

### Jawaban:
* **Konfigurasi DNS Server: WISE**\
Ada pada file WISE-DNS.sh di root node WISE
```
echo ' zone "wise.D09.com" {
	type master;
	file "/etc/bind/wise/wise.D09.com";
}; ' > /etc/bind/named.conf.local

mkdir /etc/bind/wise

cp /etc/bind/db.local /etc/bind/wise/wise.D09.com

echo -e '
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     wise.D09.com. root.wise.D09.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      wise.D09.com.
@       IN      A       192.189.3.3     ; IP WISE
@       IN      AAAA    ::1
' > /etc/bind/wise/wise.D09.com

service bind9 restart
```

* **Konfigurasi DHCP Server: Westalis**\
Ada pada file westalis-dhcp.sh di root node Westalis
```
echo -e '
INTERFACES="eth0"
' > /etc/default/isc-dhcp-server
```

* **Konfigurasi Proxy Server: Berlint**\
Ada pada file Berlint-Proxy.sh di root node Berlint
```
mv /etc/squid/squid.conf /etc/squid/squid.conf.bak

echo -e '
http_port 8080
visible_hostname Berlint
' > /etc/squid/squid.conf

service squid start
service squid restart
service squid status
```

## SOAL 2
### Deskripsi
Ostania sebagai DHCP Relay

### Jawaban
* **Konfigurasi DHCP Relay: Ostania**\
Ada pada file Ostania-dhcprelay.sh di root node Ostania
```
echo -e '
# Defaults for isc-dhcp-relay initscript
# sourced by /etc/init.d/isc-dhcp-relay
# installed at /etc/default/isc-dhcp-relay by the maintainer scripts

#
# This is a POSIX shell fragment
#

# What servers should the DHCP relay forward requests to?
SERVERS="192.189.3.4"

# On what interfaces should the DHCP relay (dhrelay) serve DHCP requests?
INTERFACES="eth1 eth2 eth3"

# Additional options that are passed to the DHCP relay daemon?
OPTIONS=""
' > /etc/default/isc-dhcp-relay
```

## SOAL 3, 4, dan 6
### Deskripsi
- Soal 3: Client yang melalui Switch1 mendapatkan range IP dari [prefix IP].1.50 - [prefix IP].1.88 dan [prefix IP].1.120 - [prefix IP].1.155
- Soal 4: Client yang melalui Switch3 mendapatkan range IP dari [prefix IP].2.10 - [prefix IP].2.30 dan [prefix IP].2.60 - [prefix IP].2.85
- Soal 6: Lama waktu DHCP server meminjamkan alamat IP kepada Client yang melalui Switch1 selama 5 menit sedangkan pada client yang melalui Switch3 selama 10 menit. Dengan waktu maksimal yang dialokasikan untuk peminjaman alamat IP selama 115 menit.

### Jawaban
* **Konfigurasi pada Westalis: nomor235-westalis.sh**\
```
echo -e '
ddns-update-style none;

option domain-name "example.org";
option domain-name-servers ns1.example.org, ns2.example.org;

default-lease-time 600;
max-lease-time 7200;
log-facility local7;
subnet 192.189.2.0 netmask 255.255.255.0{
    range 192.189.2.10 192.189.2.30;
    range 192.189.2.60 192.189.2.85;
    option routers 192.189.2.1;
    option broadcast-address 192.189.2.255;
    option domain-name-servers 192.189.3.3;
    default-lease-time 600;
    max-lease-time 6900;
}
subnet 192.189.1.0 netmask 255.255.255.0{
    range 192.189.1.50 192.189.1.88;
    range 192.189.1.120 192.189.1.155;
    option routers 192.189.1.1;
    option broadcast-address 192.189.1.255;
    option domain-name-servers 192.189.3.3;
    default-lease-time 300;
    max-lease-time 6900;
}
subnet 192.189.3.0 netmask 255.255.255.0{
        option routers 192.189.3.1;
}
' > /etc/dhcp/dhcpd.conf

service isc-dhcp-server restart
service isc-dhcp-server status
```

**Jawaban Soal 3 & 6 - SSS: nomor1-SSS.sh**\
Jangan lupa untuk merestart node SSS sebelum menjalankan script berikut
```
echo -e '
#auto eth0
#iface eth0 inet static
#       address 192.189.1.2
#       netmask 255.255.255.0
#       gateway 192.189.2.1

auto eth0
iface eth0 inet dhcp
' > /etc/network/interfaces

ifconfig eth0
```
![nomor3&6-SSS](https://github.com/meisyalsabila7/Jarkom-Modul-3-D09-2022/blob/main/aset/3%20sss.jpeg?raw=true)

**Jawaban Soal 3 & 6 - Garden: nomor1-Garden.sh**\
Jangan lupa untuk merestart node Garden sebelum menjalankan script berikut
```
echo -e '
#auto eth0
#iface eth0 inet static
#       address 192.189.1.2
#       netmask 255.255.255.0
#       gateway 192.189.2.1

auto eth0
iface eth0 inet dhcp
' > /etc/network/interfaces

ifconfig eth0
```
![nomor3&6-Garden](https://github.com/meisyalsabila7/Jarkom-Modul-3-D09-2022/blob/main/aset/3%20garden.jpeg?raw=true)

**Jawaban Soal 4 & 6 - Eden: nomor1-Eden.sh**\
Jangan lupa untuk merestart node Eden sebelum menjalankan script berikut
```
echo -e '
#auto eth0
#iface eth0 inet static
#       address 192.189.2.2
#       netmask 255.255.255.0
#       gateway 192.189.2.1

auto eth0
iface eth0 inet dhcp
' > /etc/network/interfaces
ifconfig eth0
```
![nomor4&6-Eden](https://github.com/meisyalsabila7/Jarkom-Modul-3-D09-2022/blob/main/aset/WhatsApp%20Image%202022-11-14%20at%208.41.58%20PM.jpeg?raw=true)

**Jawaban Soal 4 & 6 - NewstonCastle: nomor1-NewstonCastle.sh**\
Jangan lupa untuk merestart node NewstonCastle sebelum menjalankan script berikut
```
echo -e '
#auto eth0
#iface eth0 inet static
#       address 192.189.2.3
#       netmask 255.255.255.0
#       gateway 192.189.2.1

auto eth0
iface eth0 inet dhcp
' > /etc/network/interfaces

ifconfig eth0
```
![nomor4&6-NewstonCastle](https://github.com/meisyalsabila7/Jarkom-Modul-3-D09-2022/blob/main/aset/WhatsApp%20Image%202022-11-14%20at%208.42.23%20PM.jpeg?raw=true)

**Jawaban Soal 4 & 6 - KemonoPark: nomor1-KemonoPark.sh**\
Jangan lupa untuk merestart node KemonoPark sebelum menjalankan script berikut
```
echo -e '
#auto eth0
#iface eth0 inet static
#       address 192.189.2.3
#       netmask 255.255.255.0
#       gateway 192.189.2.1

auto eth0
iface eth0 inet dhcp
' > /etc/network/interfaces

ifconfig eth0
```
![nomor4&6-KemonoPark](https://github.com/meisyalsabila7/Jarkom-Modul-3-D09-2022/blob/main/aset/WhatsApp%20Image%202022-11-14%20at%208.42.39%20PM.jpeg?raw=true)

## SOAL 5
### Deskripsi
Client mendapatkan DNS dari WISE dan client dapat terhubung dengan internet melalui DNS tersebut.

### Jawaban
* **Konfigurasi WISE: nomor4-wise.sh**\
```
echo -e '
options {
        directory "/var/cache/bind";
        forwarders {
                192.168.122.1;
        };
         //dnssec-validation auto;

        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
        allow-query{any;};
}; 
'> /etc/bind/named.conf.options 

service bind9 restart;
```

* **Konfigurasi SSS: nomor4-SSS.sh**\
Jangan lupa untuk merestart node SSS sebelum menjalankan script berikut
```
cat /etc/resolv.conf
ping google.com -c 3
```
![nomor5-SSS](https://github.com/meisyalsabila7/Jarkom-Modul-3-D09-2022/blob/main/aset/WhatsApp%20Image%202022-11-14%20at%209.02.23%20PM.jpeg?raw=true)

* **Konfigurasi Garden: nomor4-Garden.sh**\
Jangan lupa untuk merestart node Garden sebelum menjalankan script berikut
```
cat /etc/resolv.conf
ping google.com -c 3
```
![nomor5-Garden](https://github.com/meisyalsabila7/Jarkom-Modul-3-D09-2022/blob/main/aset/WhatsApp%20Image%202022-11-14%20at%209.02.50%20PM.jpeg?raw=true)

* **Konfigurasi Eden: nomor4-Eden.sh**\
Jangan lupa untuk merestart node Eden sebelum menjalankan script berikut
```
cat /etc/resolv.conf
ping google.com -c 3
```
![nomor5-Eden](https://github.com/meisyalsabila7/Jarkom-Modul-3-D09-2022/blob/main/aset/WhatsApp%20Image%202022-11-14%20at%208.57.53%20PM.jpeg?raw=true)

* **Konfigurasi NewstonCastle: nomor4-NewstonCastle.sh**\
Jangan lupa untuk merestart node NewstonCastle sebelum menjalankan script berikut
```
cat /etc/resolv.conf
ping google.com -c 3
```
![nomor5-NewstonCastle](https://github.com/meisyalsabila7/Jarkom-Modul-3-D09-2022/blob/main/aset/WhatsApp%20Image%202022-11-14%20at%208.58.18%20PM.jpeg?raw=true)

* **Konfigurasi KemonoPark: nomor4-KemonoPark.sh**\
Jangan lupa untuk merestart node KemonoPark sebelum menjalankan script berikut
```
cat /etc/resolv.conf
ping google.com -c 3
```
![nomor5-KemonoPark](https://github.com/meisyalsabila7/Jarkom-Modul-3-D09-2022/blob/main/aset/WhatsApp%20Image%202022-11-14%20at%208.58.39%20PM.jpeg?raw=true)

## SOAL 7
### Deskripsi
Loid dan Franky berencana menjadikan Eden sebagai server untuk pertukaran informasi dengan alamat IP yang tetap dengan IP [prefix IP].2.13
### Jawaban
* **Konfigurasi Westalis**\
Tambahkan script berikut di bagian paling bawah pada file `/etc/dhcp/dhcpd.conf'
```
host Eden {
    hardware ethernet 'hwaddress_milik_Eden';
    fixed-address 192.189.2.13;
}
```

lalu restart server DHCP dengan command 'service isc-dhcp-server restart'

* **Konfigurasi Eden**\
Tambahkan script berikut di bagian paling bawah pada file `/etc/network/interfaces'
```
hwaddress ether 'hwaddress_milik_Eden'
```

lalu restart server DHCP dengan command 'service isc-dhcp-server restart'. Kemudian, restart node Eden. Lalu, ketikkan command 'ifconfig eth0' dan copy hwaddress-nya, dalam kasus ini hwaddress eden adalah 76:82:7c:75:46:2b

Salin hwaddress itu pada /etc/dhcp/dhcpd.conf (node Westalis) bagian 'hwaddress_milik_Eden' tanpa tanda kutip. Begitu pula pada /etc/network/interfaces (milik node Eden).
![nomor7-Eden](https://github.com/meisyalsabila7/Jarkom-Modul-3-D09-2022/blob/main/aset/WhatsApp%20Image%202022-11-14%20at%209.11.15%20PM.jpeg?raw=true)


## Soal Nomor 8
> Client hanya dapat mengakses internet diluar (selain) hari & jam kerja (senin-jumat 08.00 - 17.00) dan hari libur (dapat mengakses 24 jam penuh).

### Pengerjaan
```
echo '
acl WORKING time MTWHF 08:00-17:00

' > /etc/squid/acl.conf 

echo '
include /etc/squid/acl.conf

http_port 8080
http_access deny WORKING
http_access allow all 
visible_hostname Berlint

' > /etc/squid/squid.conf

service squid restart
```
### Dokumentasi


## Soal Nomor 9
> Adapun pada hari dan jam kerja sesuai nomor (1), client hanya dapat mengakses domain loid-work.com dan franky-work.com (IP tujuan domain dibebaskan).

### Pengerjaan
```
echo '
liod-work.com
franky-work.com

' > /etc/squid/work-sites.acl

echo '
include /etc/squid/acl.conf

http_port 8080
visible_hostname Berlint

acl WORKSITES dstdomain "/etc/squid/work-sites.acl"
http_access allow WORKSITES
http_access deny WORKING
http_access allow all

' > /etc/squid/squid.conf
```
### Dokumentasi


## Soal Nomor 10
> Saat akses internet dibuka, client dilarang untuk mengakses web tanpa HTTPS. (Contoh web HTTP: http://example.com).

### Pengerjaan
```
echo '
liod-work.com
franky-work.com

' > /etc/squid/work-sites.acl

echo '
include /etc/squid/acl.conf

http_port 8080
visible_hostname Berlint

acl WORKSITES dstdomain "/etc/squid/work-sites.acl"
http_access allow WORKSITES
http_access deny WORKING
http_access allow all
http_access deny !SSL_ports

' > /etc/squid/squid.conf
```
### Dokumentasi


## Soal Nomor 11
> Agar menghemat penggunaan, akses internet dibatasi dengan kecepatan maksimum 128 Kbps pada setiap host (Kbps = kilobit per second; lakukan pengecekan pada tiap host, ketika 2 host akses internet pada saat bersamaan, keduanya mendapatkan speed maksimal yaitu 128 Kbps)

### Pengerjaan
```
echo '
include /etc/squid/acl.conf

http_port 8080
visible_hostname Berlint

acl SSL_ports port 443
acl WORKSITES dstdomain "/etc/squid/work-sites.acl"
#http_access deny !SSL_ports
http_access allow WORKSITES
#http_access deny WORKING
http_access allow all

delay_pools 1
delay_class 1 2
delay_access 1 allow all
delay_parameters 1 none 16000/16000
' > /etc/squid/squid.conf
service squid restart
```
### Dokumentasi


## Soal Nomor 12
> Setelah diterapkan, ternyata peraturan nomor (4) mengganggu produktifitas saat hari kerja, dengan demikian pembatasan kecepatan hanya diberlakukan untuk pengaksesan internet pada hari libur

### Pengerjaan
```
echo '
include /etc/squid/acl.conf

http_port 8080
visible_hostname Berlint

acl SSL_ports port 443
acl WORKSITES dstdomain "/etc/squid/work-sites.acl"
#http_access deny !SSL_ports
http_access allow WORKSITES
#http_access deny WORKING
http_access allow all

acl OPEN_TIME time MTWHF
delay_pools 1
delay_class 1 2
delay_access 1 allow !OPEN_TIME
delay_parameters 1 none 16000/16000
' > /etc/squid/squid.conf
service squid restart
```
### Dokumentasi


## Kendala
- waktu pengerjaan praktikum sangat terbatas
- sering gagal untuk membuat static IP untuk Eden (Nomor 7)
