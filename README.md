# Jarkom-Modul-3-A04-2022

**Nama Anggota Kelompok:**

- Samuel (5025201187)
- Mohamad Kholid Bughowi (5025201253)
- Gloria Dyah Pramesti (5025201033)

**Prefix IP: 10.1**

## Soal 1

Membuat topologi seperti pada soal dengan WISE sebagai DNS Server, Westalis sebagai DHCP Server, Berlint sebagai Proxy Server.

1. Berikut ss topology yang kami buat
   ![image](https://user-images.githubusercontent.com/49820990/201512418-521ab983-8732-42a7-8de6-0b67cd8ae3a5.png)
2. Kemudian ubah konfigurasi untuk masing-masing node

- Ostania

```
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
	address 10.1.1.1
	netmask 255.255.255.0

auto eth2
iface eth2 inet static
	address 10.1.2.1
	netmask 255.255.255.0

auto eth3
iface eth3 inet static
	address 10.1.3.1
	netmask 255.255.255.0
```

- WISE

```
auto eth0
iface eth0 inet static
	address 10.1.2.2
	netmask 255.255.255.0
	gateway 10.1.2.1
```

- Westalist

```
auto eth0
iface eth0 inet static
	address 10.1.2.4
	netmask 255.255.255.0
	gateway 10.1.2.1
```

- Berlint

```
auto eth0
iface eth0 inet static
	address 10.1.2.3
	netmask 255.255.255.0
	gateway 10.1.2.1
```

- Westalist
```
auto eth0
iface eth0 inet static
	address 10.1.2.4
	netmask 255.255.255.0
	gateway 10.1.2.1
```

- Eden
```
auto eth0
iface eth0 inet static
        address 10.1.3.2
        netmask 255.255.255.0
        gateway 10.1.3.1
```

- NewstonCastle
```
auto eth0
iface eth0 inet static
        address 10.1.3.3
        netmask 255.255.255.0
          gateway 10.1.3.1
```

- KemonoPark
```
auto eth0
iface eth0 inet static
        address 10.1.3.4
        netmask 255.255.255.0
        gateway 10.1.3.1
```

- SSS
```
auto eth0
iface eth0 inet static
        address 10.1.1.2
        netmask 255.255.255.0
        gateway 10.1.1.1
```

- Garden
```
auto eth0
iface eth0 inet static
        address 10.1.1.3
        netmask 255.255.255.0
        gateway 10.1.1.1
```

3. Jalankan command iptables

```bash
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 10.1.0.0/16
```

4. Copy `nameserver 192.168.122.1` ke tiap node

```bash
echo "nameserver 192.168.122.1" > /etc/resolv.conf
```

5. Jadikan WISE sebgagi DNS Server
```bash
apt-get update
apt-get install bind9 -y
```

6. Jadikan Westalist sebagai DHCP
```bash
apt-get update
apt-get install isc-dhcp-server -y

echo ‘
INTERFACES=”eth0”
‘ > /etc/default/isc-dhcp-server
```

7. Jadikan Berlint jadi Proxy
```bash
apt-get update
apt-get install squid -y
```

## Soal 2

Membuat Ostania sebagai DHCP Relay

### Konfigurasi Ostania

1. Install `isc-dhcp-relay` pada Ostania

```bash
apt-get update
apt-get install isc-dhcp-relay -y
```

2. Isi prompt server

```bash
10.1.2.4
```

3. Isi prompt interfaces

```bash
eth1 eth3 eth2
```

4. Restart `isc-dhcp-relay`

```bash
service isc-dhcp-relay restart
```

### Konfigurasi Westalist

1. Install `isc-dhcp-server`

```bash
apt-get update
apt-get install isc-dhcp-server -y
```

## Soal 3, 4, 5, 6

Semua client yang ada HARUS menggunakan konfigurasi IP dari DHCP Server. Client yang melalui Switch1 mendapatkan range IP dari `10.1.1.50` - `10.1.1.88` dan `10.1.1.120` - `10.1.1.155`. Client yang melalui Switch3 mendapatkan range IP dari `10.1.3.10` - `10.1.3.30` dan `10.1.3.60` - `10.1.3.85`. Client mendapatkan DNS dari WISE dan client dapat terhubung dengan internet melalui DNS tersebut. Lama waktu DHCP server meminjamkan alamat IP kepada Client yang melalui Switch1 selama 5 menit sedangkan pada client yang melalui Switch3 selama 10 menit. Dengan waktu maksimal yang dialokasikan untuk peminjaman alamat IP selama 115 menit.

### Konfigurasi Westalist

1. Edit file `/etc/default/isc-dhcp-server`

```bash
nano /etc/default/isc-dhcp-server
```

2. Isi interfaces nya dengan `eth0`

```bash
# On what interfaces should the DHCP server (dhcpd) serve DHCP requests?
# Separate multiple interfaces with spaces, e.g. \"eth0 eth1\".
INTERFACES="eth0"
```

3. Edit file `/etc/dhcp/dhcpd.conf`

```bash
nano /etc/dhcp/dhcpd.conf
```

4. Sesuaikan isi konfigurasinya seperti berikut

```bash
subnet 10.1.1.0 netmask 255.255.255.0 {
        range 10.1.1.50 10.1.1.88;
        range 10.1.1.120 10.1.1.155;
        option routers 10.1.1.1;
        option broadcast-address 10.1.1.255;
        option domain-name-servers 10.1.2.2;
        default-lease-time 300;
        max-lease-time 6900;
}
subnet 10.1.2.0 netmask 255.255.255.0{
}

subnet 10.1.3.0 netmask 255.255.255.0 {
	range 10.1.3.10 10.1.3.30;
	option routers 10.1.3.1;
	option broadcast-address 10.1.3.255;
	option domain-name-servers 10.1.2.2;
	default-lease-time 600;
	max-lease-time 6900;
}
```

5. Restart `isc-dhcp-server`

```bash
service isc-dhcp-server restart
```

### Konfigurasi semua node

1. Buka semua client node
2. Ubah `/etc/network/interfaces` menjadi seperti berikut

```bash
auto eth0
iface eth0 inet dhcp
```

3. Restart node client

### Konfigurasi WISE

1. Install bind9

```
apt-get update
apt-get install bind9 -y
```

2. Edit `/etc/bind/named.conf.options`

```bash
nano /etc/bind/named.conf.options
```

3. Sesuaikan isinya seperti berikut

```bash
options {
    directory \"/var/cache/bind\";

    forwarders {
    8.8.8.8;
    8.8.8.4;
    };

    // dnssec-validation auto;
    allow-query { any; };
    auth-nxdomain no; # conform to RFC1035
    listen-on-v6 { any; };
};
```

4. Bikin folder `/etc/bind/domain`
```bash
mkdir -p /etc/bind/domain
```

5. Edit file `/etc/bind/named.conf.local`
```bash
zone "loid-work.com" {
        type master;
        file "/etc/bind/domain/loid-work.com";
};
zone "franky-work.com" {
        type master;
        file "/etc/bind/domain/franky-work.com";
};
```

6. Copy `/etc/bind/db.local` ke folder tiap domain
```bash
cp /etc/bind/db.local /etc/bind/domain/loid-work.com
cp /etc/bind/db.local /etc/bind/domain/franky-work.com
```

7. Edit file di `/etc/bind/domain/loid-work.com` dan `/etc/bind/domain/franky-work.com`
```bash
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     franky-work.com. root.franky-work.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      franky-work.com.
@       IN      A       10.1.2.2
@       IN      AAAA    ::1 
```

```bash
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     loid-work.com. root.loid-work.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      loid-work.com.
@       IN      A       10.1.2.2
@       IN      AAAA    ::1 
```

8. Restart westails dan ostania
```bash
service isc-dhcp-server restart
service isc-dhcp-relay restart
```


## Soal 7

Menjadikan Eden sebagai server untuk pertukaran informasi dengan alamat IP yang tetap dengan IP `10.1.3.13`

### Konfigurasi Westalist

1. Edit `/etc/dhcp/dhcpd.conf`

```bash
nano /etc/dhcp/dhcpd.conf
```

2. Tambahkan konfigurasi seperti ini

```bash
host Eden {
    hardware ethernet 1e:71:20:29:02:a5;
    fixed-address 10.1.3.13;
}
```

3. Restart dhcp-server

```bash
service isc-dhcp-server restart
```

### Konfigurasi Eden

1. Edit network interfaces dan tambahkan seperti ini

```bash
auto eth0
iface eth0 inet dhcp
hwaddress ether 1e:71:20:29:02:a5
```

2. Restart Eden

## Soal 8

Pada Proxy Server di Berlint, Loid berencana untuk mengatur bagaimana Client dapat mengakses internet. Artinya setiap client harus menggunakan Berlint sebagai HTTP & HTTPS proxy. Adapun kriteria pengaturannya adalah sebagai berikut:

1. Client hanya dapat mengakses internet diluar (selain) hari & jam kerja (senin-jumat 08.00 - 17.00) dan hari libur (dapat mengakses 24 jam penuh)
2. Adapun pada hari dan jam kerja sesuai nomor (1), client hanya dapat mengakses domain loid-work.com dan franky-work.com (IP tujuan domain dibebaskan)
3. Saat akses internet dibuka, client dilarang untuk mengakses web tanpa HTTPS. (Contoh web HTTP: http://example.com)
4. Agar menghemat penggunaan, akses internet dibatasi dengan kecepatan maksimum 128 Kbps pada setiap host (Kbps = kilobit per second; lakukan pengecekan pada tiap host, ketika 2 host akses internet pada saat bersamaan, keduanya mendapatkan speed maksimal yaitu 128 Kbps)
5. Setelah diterapkan, ternyata peraturan nomor (4) mengganggu produktifitas saat hari kerja, dengan demikian pembatasan kecepatan hanya diberlakukan untuk pengaksesan internet pada hari libur

### Konfigurasi Berlint

1. Install squid

```bash
apt-get update
apt-get install libapache2-mod-php7.0 -y
apt-get install squid -y
```

2. Edit `/etc/squid/acl.conf`

```bash
acl WORKING time MTWHF 08:00-17:00
acl WEEKEND time AS 00:00-23:59
```

3. Edit `/etc/squid/work-sites.acl`
```bash
loid-work.com
franky-work.com
```

#### Nomor 1
```bash
include /etc/squid/acl.conf

http_port 8080
visible_hostname Berlint

http_access deny WORKING
http_access allow all
' > /etc/squid/squid.conf
```

#### Nomor 2
```bash
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

#### Nomor 3
```bash
echo '
include /etc/squid/acl.conf

http_port 8080
visible_hostname Berlint

acl SSL_ports port 443
acl WORKSITES dstdomain "/etc/squid/work-sites.acl"
http_access deny !SSL_ports
http_access allow WORKSITES
http_access deny WORKING
http_access allow all
' > /etc/squid/squid.conf
```

#### Nomor 4
```bash
echo ' 
include /etc/squid/acl.conf

http_port 8080
visible_hostname Berlint

acl SSL_ports port 443
acl WORKSITES dstdomain "/etc/squid/work-sites.acl"
# http_access deny !SSL_ports
http_access allow WORKSITES
# http_access deny WORKING
http_access allow all

delay_pools 1
delay_class 1 2
delay_access 1 allow all
delay_parameters 1 none 16000/16000
'  > /etc/squid/squid.conf
```

#### Nomor 5
```bash
echo ' 
include /etc/squid/acl.conf

http_port 8080
visible_hostname Berlint

acl SSL_ports port 443
acl WORKSITES dstdomain "/etc/squid/work-sites.acl"
http_access deny !SSL_ports
http_access allow WORKSITES
http_access deny WORKING
http_access allow all

acl OPEN_TIME time MTWHF
delay_pools 1
delay_class 1 2
delay_access 1 allow !OPEN_TIME
delay_parameters 1 none 16000/16000
'  > /etc/squid/squid.conf
```
