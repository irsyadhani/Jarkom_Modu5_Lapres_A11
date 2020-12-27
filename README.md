# Jarkom_Modu5_Lapres_A11
Kelompok A11:
* _Irsyadhani Dwi Shubhi (0511184000022)_
* _Anggun Wahyuni (05111840000154)_

----------------------------------------------------------------
## Soal
  * [Soal A](#soal-a)
  * [Soal B](#soal-b)
  * [Soal C](#soal-c)
  * [Soal D](#soal-d)
  * [Soal 1](#soal-1)
  * [Soal 2](#soal-2)
  * [Soal 3](#soal-3)
  * [Soal 4](#soal-4)
  * [Soal 5](#soal-5)
  * [Soal 6](#soal-6)
  * [Soal 7](#soal-7)
----------------------------------------------------------------
#### Soal A:
Tugas pertama kalian yaitu membuat topologi jaringan sesuai dengan rancangan yang diberikan.

_**Penyelesaian:**_
* Menentukan jumlah subnet pada topologi

![alt text](/img/a_topologi.jpg)
* Menentukan jumlah alamat IP yang dibutuhkan oleh tiap subnet dan lakukan labelling netmask berdasarkan jumlah IP yang dibutuhkan

![alt text](/img/a_jumlahIP.jpg)
* Menghitung pembagian IP berdasarkan NID dan netmask menggunakan pohon serta melakukan subnetting

![alt text](/img/a_subnet_tree.jpg)

![alt text](/img/a_perhitungan_subnet_tree.jpg)
* Dari pohon tersebut akan mendapat pembagian IP sebagai berikut

![alt text](/img/a_subnet_server.jpg)

![alt text](/img/a_tabel_subnet.jpg)

#
#### Soal B:
Membuat topologi tersebut menggunakan teknik **CIDR** atau **VLSM**.

_**Penyelesaian:**_

Kelompok A11 membuat topologi menggunakan teknik **VLSM**.
* Setting IP pada setiap UML dengan mengetikkan `nano /etc/network/interfaces`
```
# SERVER
# MALANG
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
address 10.151.73.98
netmask 255.255.255.248
gateway 10.151.73.97

# MOJOKERTO
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
address 10.151.73.99
netmask 255.255.255.248
gateway 10.151.73.97

# PROBOLINGGO
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
address 192.168.0.11
netmask 255.255.255.248
gateway 192.168.0.9

# MADIUN
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
address 192.168.0.10
netmask 255.255.255.248
gateway 192.168.0.9

# ROUTER
# SURABAYA
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
address 10.151.72.50
netmask 255.255.255.252
gateway 10.151.72.49
auto eth1
iface eth1 inet static
address 192.168.0.1
netmask 255.255.255.252

auto eth2
iface eth2 inet static
address 192.168.0.5
netmask 255.255.255.252

# BATU
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
address 192.168.0.2
netmask 255.255.255.252
gateway 192.168.0.1

auto eth1
iface eth1 inet static
address 192.168.1.1
netmask 255.255.255.0

auto eth2
iface eth2 inet static
address 10.151.73.97
netmask 255.255.255.248

# KEDIRI
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
address 192.168.0.6
netmask 255.255.255.252
gateway 192.168.0.5

auto eth1
iface eth1 inet static
address 192.168.3.1
netmask 255.255.255.0

auto eth2
iface eth2 inet static
address 192.168.0.9
netmask 255.255.255.248
```
#
#### Soal C:
Melakukan routing agar setiap perangkat pada jaringan tersebut dapat terhubung.

_**Penyelesaian:**_
* Membuat `route.sh` pada setiap router
```
# SURABAYA
route add -net 192.168.1.0 netmask 255.255.255.0 gw 192.168.0.2
route add -net 10.151.73.96 netmask 255.255.255.248 gw 192.168.0.2
route add -net 192.168.3.0 netmask 255.255.255.0 gw 192.168.0.6
route add -net 192.168.0.8 netmask 255.255.255.248 gw 192.168.0.6

# BATU
route add -net 0.0.0.0 netmask 0.0.0.0 gw 192.168.0.1

# KEDIRI
route add -net 0.0.0.0 netmask 0.0.0.0 gw 192.168.0.5
```
![alt text](/img/c_routing.jpg)
#
#### Soal D:
Memberikan ip pada subnet **SIDOARJO** dan **GRESIK** secara dinamis menggunakan bantuan DHCP SERVER (Selain subnet tersebut menggunakan ip static). Kemudian, setting DHCP RELAY pada router yang menghubungkannya.

_**Penyelesaian:**_
**Konfigurasi DHCP Server MOJOKERTO**

* tambahkan `interfaces=”eth0”` pada etc/default/isc-dhcp-server
![alt text](/img/d_1.jpg)
* Buka `nano /etc/dhcp/dhcpd.conf` Lalu tambahkan script ini di file konfigurasi DHCP MOJOKERTO
```
subnet 192.168.1.0 netmask 255.255.255.0 {
	range 192.168.1.2 192.168.1.202;
	option routers 192.168.1.1;
	option broadcast-address 192.168.1.255;
	option domain-name-servers 10.151.73.98, 202.46.129.2;
	default-lease-time 600;
    	max-lease-time 7200;
}
subnet 192.168.3.0 netmask 255.255.255.0 {
	range 192.168.3.2 192.168.3.212;
	option routers 192.168.3.1;
	option broadcast-address 192.168.3.255;
	option domain-name-servers 10.151.73.98, 202.46.129.2;
	default-lease-time 600;
    	max-lease-time 7200;
}
subnet 10.151.73.96 netmask 255.255.255.248 {
	option routers 10.151.73.97;
	option broadcast-address 10.151.73.103;
}
```
* Restart service isc-dhcp-server dengan perintah
```
service isc-dhcp-server restart
```
**Konfigurasi DHCP Relay BATU**
* atur ip address DNS pada relay server
![alt text](/img/d_sda_1.jpg)
* aktifkan baris `net.ipv4.conf.all.accept_source_rote = 0` dan ubah nilainya menjadi 1 pada `/etc/sysctl.conf`
* periksa dengan `sysctl -p` kemudian restart server
* install paket `isc-dhcp-relay`
`apt-get install isc-dhcp-relay -y`
* masukkan IP server DHCP, yaitu IP Mojokerto
* masukkan interface yang terhubung dengan layanan DHCP
![alt text](/img/d_sda_2.jpg)

**Konfigurasi DHCP Client SIDOARJO**
* buka `/etc/network/interfaces` dan tambahkan
`auto eth0
iface eth0 inet dhcp`
* restart network

**Konfigurasi DHCP Relay KEDIRI** 
* Melakukan hal yang sama seperti konfigurasi DHCP Relay pada BATU
![alt text](/img/d_gsk_1.jpg)

![alt text](/img/d_gsk_2.jpg)

**Konfigurasi DHCP Client GRESIK**
* Melakukan hal yang sama seperti konfigurasi DHCP Client pada SIDOARJO, sehinggga gresik mendapatkan IP sebagai berikut

![alt text](/img/d_gsk_3.jpg)

Gresik dan Sidoarjo mendapatkan pembagian IP sebagai berikut
![alt text](/img/D_testing.PNG)

#
#### Soal 1:
Agar topologi yang kalian buat dapat mengakses keluar, kalian diminta untuk mengkonfigurasi **SURABAYA** menggunakan iptables, namun Bibah tidak ingin kalian menggunakan MASQUERADE.

_**Penyelesaian:**_
* Pada UML SURABAYA memasukkan perintah sebagai berikut:
```
iptables -t nat -A POSTROUTING -s 192.168.0.0/22 -o eth0 -j SNAT --to-source 10.151.72.50
```
![alt text](/img/1.jpg)
#
#### Soal 2:
Mendrop semua akses SSH dari luar Topologi (UML) Kalian pada server yang memiliki ip DMZ (DHCP dan DNS SERVER) pada **SURABAYA** demi menjaga keamanan.

_**Penyelesaian:**_
* Pada UML SURABAYA memasukkan perintah sebagai berikut:
```
iptables -A FORWARD -d 10.151.73.96/29 -i eth0 -p tcp --dport 22 -j DROP
```
![alt text](/img/2.jpg)

Hasil testing dilakukan dengan mengetikkan ``nc 10.151.73.98 22`` pada terminal. Testing pertama dilakukan sebelum memasukkan perintah di iptables menunjukkan bahwa akses SSH berhasil. Testing kedua dilakukan setelah memasukkan perintah untuk mendrop semua akses SSH menunjukkan "connection timed out"
![alt text](/img/2_testing.PNG)

#
#### Soal 3:
Membatasi DHCP dan DNS server hanya boleh menerima maksimal 3 koneksi ICMP secara bersamaan yang berasal dari mana saja menggunakan **iptables pada masing masing server**, selebihnya akan di DROP.

_**Penyelesaian:**_
* Pada UML MOJOKERTO dan MALANG memasukkan perintah sebagai berikut:
```
iptables -A INPUT -p icmp -m connlimit --connlimit-above 3 --connlimit-mask 0 -j DROP
```
![alt text](/img/3_1.jpg)
![alt text](/img/3_2.jpg)

_**Hasil:**_
* UML KEDIRI, SIDOARJO dan BATU berhasil di routing ketiganya
* Sedangkan UML ke-4 yaitu UML MADIUN tidak berhasil melakukan routing

![alt text](/img/3_hasil.jpg)
#
#### Soal 4:
Membatasi akses ke **MALANG** yang berasal dari SUBNET SIDOARJO dan SUBNET GRESIK dengan peraturan sebagai berikut:
* Akses dari subnet SIDOARJO hanya diperbolehkan pada pukul 07.00 - 17.00 pada hari Senin
sampai Jumat

Selain itu paket akan di REJECT.

_**Penyelesaian:**_
* Pada UML MALANG memasukkan perintah sebagai berikut:
```
iptables -A INPUT -s 192.168.1.0/24 -m time --timestart 07:00 --timestop 17:00 --weekdays Mon,Tue,Wed,Thu,Fri -j ACCEPT

iptables -A INPUT -s 192.168.1.0/24 -j REJECT
```
![alt text](/img/4.jpg)

_**Hasil:**_
* Set waktu adalah pukul 01:54 di kedua UML
* Menjalankan perintah iptables di UML MALANG
* Menjalankan routing pada UML SIDOARJO
* Maka routing tidak berhasil

![alt text](/img/4_hasil_1.jpg)

* Set waktu adalah pukul 08:00 di kedua UML
* Menjalankan perintah iptables di UML MALANG
* Menjalankan routing pada UML SIDOARJO
* Maka routing berhasil

![alt text](/img/4_hasil_2.jpg)
#
#### Soal 5:
Membatasi akses ke **MALANG** yang berasal dari SUBNET SIDOARJO dan SUBNET GRESIK dengan peraturan sebagai berikut:
* Akses dari subnet GRESIK hanya diperbolehkan pada pukul 17.00 hingga pukul 07.00 setiap
harinya.

Selain itu paket akan di REJECT.

_**Penyelesaian:**_
* Pada UML MALANG memasukkan perintah sebagai berikut:
```
iptables -A INPUT -s 192.168.3.0/24 -m time --timestart 07:00 --timestop 17:00 -j REJECT
```
![alt text](/img/5.jpg)

_**Hasil:**_
* Set waktu adalah pukul 08:00 di kedua UML
* Menjalankan perintah iptables di UML MALANG
* Menjalankan routing pada UML GRESIK
* Maka routing tidak berhasil


![alt text](/img/5_hasil_1.jpg)

* Set waktu adalah pukul 18:00 di kedua UML
* Menjalankan perintah iptables di UML MALANG
* Menjalankan routing pada UML GRESIK
* Maka routing berhasil

![alt text](/img/5_hasil_2.jpg)
#
#### Soal 6:
Karena kita memiliki 2 buah **WEB Server**, **SURABAYA** disetting sehingga setiap
request dari client yang mengakses **DNS Server** akan didistribusikan **secara bergantian** pada
**PROBOLINGGO** port 80 dan **MADIUN** port 80.


_**Penyelesaian:**_
* Pada UML SURABAYA memasukkan perintah sebagai berikut:
```
iptables -t nat -A PREROUTING -p tcp -d 10.151.73.98 -m statistic --mode nth --every 2 --packet 0 -j DNAT --to-destination 192.168.0.11:80
iptables -t nat -A PREROUTING -p tcp -d 10.151.73.98 -j DNAT --to-destination 192.168.0.10:80
iptables -t nat -A POSTROUTING -p tcp -d 192.168.0.11 --dport 80 -j SNAT --to-source 10.151.73.98
iptables -t nat -A POSTROUTING -p tcp -d 192.168.0.10 --dport 80 -j SNAT --to-source 10.151.73.98
```
![alt text](/img/6.jpg)

Hasil testingnya adalah sebagai berikut :
![alt text](/img/6_testing.PNG)

#
#### Soal 7:
Semua paket didrop oleh firewall (dalam topologi) tercatat dalam log pada setiap
UML yang memiliki aturan drop.

_**Penyelesaian:**_
* Pada UML yang mengandung perintah iptables `DROP` yaitu pada UML MALANG, MOJOKERTO, dan SURABAYA dimasukkan perintah sebagai berikut:
```
iptables -N LOGGING
iptables -A INPUT -j LOGGING
iptables -A OUTPUT -j LOGGING
iptables -A LOGGING -j LOG --log-prefix "IPTables-Dropped: " --log-level 4
iptables -A LOGGING -j DROP
```
![alt text](/img/7_surabaya.jpg)
![alt text](/img/7_mojokerto.jpg)
![alt text](/img/7_malang.jpg)
![alt text](/img/7_testing.png)
