# Jarkom-Modul-3-A04-2022

**Nama Anggota Kelompok:**

- Samuel (5025201187)
- Mohamad Kholid Bughowi (5025201253)
- Gloria Dyah Pramesti (5025201033)

**Prefix IP: 10.1**

## Soal 1

Membuat topologi seperti pada soal dengan WISE sebagai DNS Server, Westalis sebagai DHCP Server, Berlint sebagai Proxy Server.

1. Berikut ss topology yang kami buat
   ![image](https://user-images.githubusercontent.com/49820990/201507126-659d1f94-60a3-49ec-a1cf-b476816d4f88.png)
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

## Soal 2

Membuat Ostania sebagai DHCP Relay

## Soal 3

Semua client yang ada HARUS menggunakan konfigurasi IP dari DHCP Server. Client yang melalui Switch1 mendapatkan range IP dari `10.1.1.50` - `10.1.1.88` dan `10.1.1.120` - `10.1.1.155`

## Soal 4

Client yang melalui Switch3 mendapatkan range IP dari `10.1.3.10` - `10.1.3.30` dan `10.1.3.60` - `10.1.3.85`

## Soal 5

Client mendapatkan DNS dari WISE dan client dapat terhubung dengan internet melalui DNS tersebut.

## Soal 6

Lama waktu DHCP server meminjamkan alamat IP kepada Client yang melalui Switch1 selama 5 menit sedangkan pada client yang melalui Switch3 selama 10 menit. Dengan waktu maksimal yang dialokasikan untuk peminjaman alamat IP selama 115 menit.

## Soal 7

Menjadikan Eden sebagai server untuk pertukaran informasi dengan alamat IP yang tetap dengan IP `10.1.3.13`

## Soal 8

Pada Proxy Server di Berlint, Loid berencana untuk mengatur bagaimana Client dapat mengakses internet. Artinya setiap client harus menggunakan Berlint sebagai HTTP & HTTPS proxy. Adapun kriteria pengaturannya adalah sebagai berikut:

1. Client hanya dapat mengakses internet diluar (selain) hari & jam kerja (senin-jumat 08.00 - 17.00) dan hari libur (dapat mengakses 24 jam penuh)
2. Adapun pada hari dan jam kerja sesuai nomor (1), client hanya dapat mengakses domain loid-work.com dan franky-work.com (IP tujuan domain dibebaskan)
3. Saat akses internet dibuka, client dilarang untuk mengakses web tanpa HTTPS. (Contoh web HTTP: http://example.com)
4. Agar menghemat penggunaan, akses internet dibatasi dengan kecepatan maksimum 128 Kbps pada setiap host (Kbps = kilobit per second; lakukan pengecekan pada tiap host, ketika 2 host akses internet pada saat bersamaan, keduanya mendapatkan speed maksimal yaitu 128 Kbps)
5. Setelah diterapkan, ternyata peraturan nomor (4) mengganggu produktifitas saat hari kerja, dengan demikian pembatasan kecepatan hanya diberlakukan untuk pengaksesan internet pada hari libur
