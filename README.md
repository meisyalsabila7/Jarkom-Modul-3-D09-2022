# Jarkom-Modul-3-D09-2022

## Anggota Kelompok D09
| No | Nama | NRP |
| ------ | ------ | ----- |
| 1 | Monica Narda Davita | 5025201009 |
| 2 | Alya Shofarizqi Inayah | 5025201113 |
| 3 | Meisya Salsabila Indrijo Putri | 5025201114 |

## Topologi
![topologi](https://user-images.githubusercontent.com/96837287/201667955-ba5d5b9d-97e9-488b-aa00-0ec0d80f8073.jpeg)

## Konfigurasi
- Ostania
- SSS
- Garden
- Wise
- Berlint
- Westalis
- Eden
- NewstonCastle
- KemonoPark

## Soal Nomor 1
> Loid bersama Franky berencana membuat peta tersebut dengan kriteria `WISE sebagai DNS Server`, `Westalis sebagai DHCP Server`, `Berlint sebagai Proxy Server`.

### Pengerjaan
Setelah Wise dikonfigurasikan, untuk membuat Wise menjadi DNS Server perlu untuk membuat file dengan isi seperti dibawah ini. Untuk menjalankannya gunakan command `bash WISE-DNS.sh`

```

```
![A 1 1](https://user-images.githubusercontent.com/96837287/201670268-f3b75cea-9888-4542-ad21-97d5c165371b.jpg)

Untuk membuat node Westalis menjadi DHCP Server, perlu mengonfigurasikan file dengan isi seperti dibawah ini dan jangan lupa untuk mengubah config interface di isc-dhcp-server menjadi `eth0`. Lalu, jalankan dengan command `bash westalis-dhcp.sh`

```
echo -e '
INTERFACES="eth0"
' > /etc/default/isc-dhcp-server
```

Selanjutnya, untuk node Berlint sebagai Proxy Server dibutuhkan file konfigurasi yang berisi seperti dibawah ini. Dan menjalankannya dengan menggunakan command `bash Berlint-Proxy.sh`

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

![berlint proxy](https://user-images.githubusercontent.com/96837287/201672102-5180a733-d850-4e45-ab9c-10dc9808f6ad.jpg)

## Soal Nomor 2
`Ostania sebagai DHCP Relay`

### Pengerjaan
Pada soal nomor 2 yakni perintah untuk kriteria Ostania sebagai DHCP relay, dapat mengubah config pada relay /etc/default/isc-dhcp-relay menjadi seperti di bawah ini. Sebelumnya, Install relay pada Ostania `apt-get install isc-dhcp-relay -y`. Setelah itu untuk menjalankannya, Enter/kosongi semua permintaan dan gunakan command `bash Ostania-dhcprelay.sh`
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
service isc-dhcp-relay restart
```

![235 westalis](https://user-images.githubusercontent.com/96837287/201677037-8ff4dc1d-a420-438a-938b-9eebf008cb7f.jpg)

Karena untuk nomor 2, 3, dan 5 saling terhubung, dan sehubungan dengan waktu yang amat terbatas maka untuk konfigurasinya dijadikan satu file yang sama. Untuk menjalankannya dapat menggunakan command `bash nomor235-westalis` pada client westalis.

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
```
![235-westalis](https://user-images.githubusercontent.com/96837287/201677698-478ff20a-7fad-4d84-b7a6-e975d47fefb6.jpg)

## Soal Nomor 3
> Client yang melalui `Switch 1` mendapatkan range IP dari `[prefix IP].1.50 - [prefix IP].1.88` dan `[prefix IP].1.120 - [prefix IP].1.155`.

### Pengerjaan

Dapat dilihat pada file konfigurasi `nomor235-westalis.sh`
```
range 192.189.2.10 192.189.2.30;
range 192.189.2.60 192.189.2.85;
```

## Soal Nomor 4
> Client yang melalui `Switch 3` mendapatkan range IP dari `[prefix IP].3.10 - [prefix IP].3.30` dan `[prefix IP].3.60 - [prefix IP].3.85`.

### Pengerjaan

### Dokumentasi


## Soal Nomor 5
> Client mendapatkan `DNS dari WISE` dan client dapat terhubung dengan internet melalui DNS tersebut.

### Pengerjaan
pada configurasi wise, tambahkan line seperti ini pada directory `/var/cache/bind`
```
forwarders {
    192.168.122.1;
};
```

## Soal Nomor 6
> Lama waktu DHCP server meminjamkan alamat IP kepada Client yang melalui `Switch 1` selama 5 menit sedangkan pada client yang melalui `Switch 3` selama 10 menit. Dengan waktu maksimal yang dialokasikan untuk peminjaman alamat IP selama 115 menit.

### Pengerjaan

### Dokumentasi


## Soal Nomor 7
> Loid dan Franky berencana menjadikan `Eden sebagai server` untuk pertukaran informasi dengan alamat IP yang tetap dengan `IP [prefix IP].3.13`.

### Pengerjaan

### Dokumentasi


## Soal Nomor 8
> Client hanya dapat mengakses internet diluar (selain) hari & jam kerja (senin-jumat 08.00 - 17.00) dan hari libur (dapat mengakses 24 jam penuh).

### Pengerjaan

### Dokumentasi


## Soal Nomor 9
> Adapun pada hari dan jam kerja sesuai nomor (1), client hanya dapat mengakses domain loid-work.com dan franky-work.com (IP tujuan domain dibebaskan).

### Pengerjaan

### Dokumentasi


## Soal Nomor 10
> Saat akses internet dibuka, client dilarang untuk mengakses web tanpa HTTPS. (Contoh web HTTP: http://example.com).

### Pengerjaan

### Dokumentasi


## Soal Nomor 11
> Agar menghemat penggunaan, akses internet dibatasi dengan kecepatan maksimum 128 Kbps pada setiap host (Kbps = kilobit per second; lakukan pengecekan pada tiap host, ketika 2 host akses internet pada saat bersamaan, keduanya mendapatkan speed maksimal yaitu 128 Kbps)

### Pengerjaan

### Dokumentasi


## Soal Nomor 12
> Setelah diterapkan, ternyata peraturan nomor (4) mengganggu produktifitas saat hari kerja, dengan demikian pembatasan kecepatan hanya diberlakukan untuk pengaksesan internet pada hari libur

### Pengerjaan

### Dokumentasi


## Kendala
- waktu pengerjaan praktikum sangat terbatas
