# Jarkom_Modul5_Lapres_C02

Laporan Resmi Modul 5 Praktikum Jaringan Komputer
## Kelompok C02
* Aulia Ihza Hendradi (05111840000089)
* Excel Deo Cornelius (05111840000117)

## Topologi

**(A)** Membuat topologi jaringan sesuai dengan rancangan yang diberikan pada gambar di bawah ini:

![1(1)](https://user-images.githubusercontent.com/52096462/103269240-fe70a400-49e7-11eb-8c77-051ebd5a49a9.png)

Keterangan:

* **SURABAYA** diberikan IP Tuntap

* **MALANG** merupakan **DNS Server** diberikan **IP DMZ** : `10.151.77.106`

* **MOJOKERTO** merupakan **DHCP Server** diberikan **IP DMZ** : `10.151.77.107`

* **MADIUN** dan **PROBOLINGGO** merupakan **WEB Server**

* **Setiap Server** diberikan memory sebesar **128M**

* **Client** dan **Router** diberikan memori sebesar **96M**

* Jumlah host pada subnet **SIDOARJO 200 Host**

* Jumlah host pada subnet **GRESIK 210 Host**

**(B)** Melakukan subnetting dengan VLSM:

![1(4)](https://user-images.githubusercontent.com/52096462/103269233-fc0e4a00-49e7-11eb-9499-3e091a19dcb5.png)

Pembuatan Tree:

![diagram1 (4)](https://user-images.githubusercontent.com/52096462/103269238-fdd80d80-49e7-11eb-9c37-426eca94caed.jpg)

 ________ ___________ _________ 
| Subnet |    Host   | Length  |
|--------|-----------|---------|
| A1     | SERVER    | 29      |
| A2     |  201      | 24      |
| A3     | 2         | 30      |
| A4     | 2         | 30      |
| A5     | 211       | 24      |
| A6     |  3        | 30      | 
|Total   | 419       | 23      |
--------------------------------
 ________ ___________ _________ ______________ ________________ ______________ 
| Subnet | Jumlah IP | Submask |      NID     |     Netmask    | Broadcast ID |
|--------|-----------|---------|--------------|----------------|--------------|
| A1     | SERVER    | 29      |10.151.77.104 |255.255.255.248 |10.151.77.111 |
| A2     |  200      | 24      |192.168.1.0   |255.255.255.0   |192.168.1.255 |
| A3     | 2         | 30      |192.168.0.0   |255.255.255.252 |192.168.0.3   |
| A4     | 2         | 30      |192.168.0.4   |255.255.255.252 |192.168.0.7   |
| A5     | 210       | 24      |192.168.2.0   |255.255.255.0   |192.168.2.255 |
| A6     | SERVER    | 29      |192.168.0.8   |255.255.255.248 |192.168.0.15  |
-------------------------------------------------------------------------------

**(C)** Melakukan Routing

Sintaks untuk **topo.sh**

```
# Switch
uml_switch -unix switch1 > /dev/null < /dev/null &
uml_switch -unix switch2 > /dev/null < /dev/null &
uml_switch -unix switch3 > /dev/null < /dev/null &

# Router
xterm -T SURABAYA -e linux ubd0=SURABAYA,jarkom umid=SURABAYA eth0=tuntap,,,10.151.76.53 eth1=daemon,,,switch1 eth2=daemon,,,switch3 eth3=daemon,,,switch2 mem=256M &

# Server switch 2
xterm -T MALANG -e linux ubd0=MALANG,jarkom umid=MALANG eth0=daemon,,,switch2 mem=160M &
xterm -T MOJOKERTO -e linux ubd0=MOJOKERTO,jarkom umid=MOJOKERTO eth0=daemon,,,switch2 mem=128M &
xterm -T TUBAN -e linux ubd0=TUBAN,jarkom umid=TUBAN eth0=daemon,,,switch2 mem=128M &

# Klien switch 1
xterm -T SIDOARJO -e linux ubd0=SIDOARJO,jarkom umid=SIDOARJO eth0=daemon,,,switch1 mem=64M &
xterm -T GRESIK -e linux ubd0=GRESIK,jarkom umid=GRESIK eth0=daemon,,,switch1 mem=64M &

# Klien switch 3
xterm -T BANYUWANGI -e linux ubd0=BANYUWANGI,jarkom umid=BANYUWANGI eth0=daemon,,,switch3 mem=64M &
xterm -T MADIUN -e linux ubd0=MADIUN,jarkom umid=MADIUN eth0=daemon,,,switch3 mem=64M &
```
![Img](https://github.com/riclown/Jarkom_modul5_praktikum_C12/blob/main/img/topo.jpg)

Lalu  jalankan `bash topo.sh` pada *putty* dan masukkan *username* dan *password* default. Pada router **SURABAYA**, **BATU**, **KEDIRI**  lakukan setting `sysctl` dengan mengetikkan perintah `nano /etc/sysctl.conf`, dan tambahkan `net.ipv4.conf.all.accept_source_route = 1`.

Lalu setting IP pada setiap *interfaces* uml dengan mengetikkan `nano /etc/network/interfaces` sebagai berikut:

**SURABAYA**

```
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
address 10.151.76.54
netmask 255.255.255.252
gateway 10.151.76.53

auto eth1 #switch 5
iface eth1 inet static
address   192.168.0.1
netmask 255.255.255.252

auto eth2 #switch 6
iface eth2 inet static
address 192.168.0.5
netmask 255.255.255.252

```

**BATU**

```
auto lo
iface lo inet loopback

auto eth0 #switch 5
iface eth0 inet static
address 192.168.0.2
netmask 255.255.255.252 
gateway 192.168.0.1

auto eth1 #switch 3
iface eth1 inet static
address 192.168.1.1
netmask 255.255.255.0

auto eth2 #switch 2
iface eth2 inet static
address 10.151.77.105
netmask 255.255.255.248

```

**KEDIRI**

```
auto lo
iface lo inet loopback

auto eth0 #switch 6
iface eth0 inet static
address 192.168.0.6
netmask 255.255.255.252
gateway 192.168.0.5

auto eth1 #switch 4
iface eth1 inet static
address 192.168.2.1
netmask 255.255.255.0

auto eth2 #switch 1
iface eth2 inet static
address 192.168.0.9
netmask 255.255.255.248
```

**MALANG**

```
auto lo
iface lo inet loopback

auto eth0 #switch 2
iface eth0 inet static
address 10.151.77.106
netmask 255.255.255.248
gateway 10.151.77.105
```

**MOJOKERTO**

```
auto lo
iface lo inet loopback

auto eth0 #switch 2
iface eth0 inet static
address 10.151.77.107
netmask 255.255.255.248
gateway 10.151.77.105
```

**PROBOLINGGO**

```
auto lo
iface lo inet loopback

auto eth0 #switch 1
iface eth0 inet static
address 192.168.0.10
netmask 255.255.255.248
gateway 192.168.0.9
```

**MADIUN**

```
auto lo
iface lo inet loopback

auto eth0 #switch 1
iface eth0 inet static
address 192.168.0.11
netmask 255.255.255.248
gateway 192.168.0.9
```

**SIDOARJO**

```
auto lo
iface lo inet loopback

auto eth0 #switch 3
iface eth0 inet static
address 192.168.1.2
netmask 255.255.255.0
gateway 192.168.1.1
```

**GRESIK**

```
auto lo
iface lo inet loopback

auto eth0 #switch 4
iface eth0 inet static
address 192.168.2.2
netmask 255.255.255.0
gateway 192.168.2.1
```

Lakukan proses routing dari **SURABAYA** ke subnet yang tidak berhubungan secara langsung dengan membuat script yang bernama `rute.sh` di **SURABAYA**, dan masukkan sintaks berikut:

```
route add -net 10.151.77.104 netmask 255.255.255.248 gw 192.168.0.2 #A1
route add -net 192.168.1.0 netmask 255.255.255.0 gw 192.168.0.2 #A2
route add -net 192.168.2.0 netmask 255.255.255.0 gw 192.168.0.6 #A5
route add -net 192.168.0.8 netmask 255.255.255.248 gw 192.168.0.6 #A6
```

Jalannkan script `rute.sh` di **SURABAYA** dan restart network dengan mengetikkan `service networking restart` di setiap UML. Untuk menguji koneksi antara subnet, terlebih dahulu diuji coba dengan mengetikkan `iptables –t nat –A POSTROUTING –o eth0 –j MASQUERADE –s 192.168.0.0/16` pada router **SURABAYA**. IP Tables dapat dimasukkan ke dalam *script*, dalam hal ini, nama script berupa `table1.sh`.

**(D)** Memberikan IP pada subnet **SIDOARJO** dan **GRESIK** secara dinamis menggunakan DHCP SERVER

* Pada **SURABAYA**, **BATU**, **KEDIRI** lakukan instalasi **ISC-DHCP-Relay**. Lakukan perintah `apt-get update` pada ketiga uml tersebut dan masukkan *syntax* `apt-get install isc-dhcp-relay -y`, setelah instalasi selesai, masukkan syntax `nano /etc/default/isc-dhcp-relay`. Sesuaikan `isc-dhcp-relay` seperti pada gambar berikut:

![Img](img/sbydhcp.jpg)

![Img](img/kdrdhcp.jpg)

![Img](img/batudhcp.jpg)

Lakukan restart dhcp-server dengan `service isc-dhcp-relay restart`.

* Pada **MOJOKERTO** lakukan instalasi **ISC-DHCP-Server**. Lakukan perintah `apt-get update` pada ketiga uml tersebut dan masukkan *syntax* `apt-get install isc-dhcp-server -y`, setelah instalasi selesai, masukkan syntax `nano /etc/default/isc-dhcp-server`. Sesuaikan `isc-dhcp-server` seperti pada gambar berikut:

![Img](img/mojodhcp.jpg)

Berikutnya masukkan *syntax* `nano /etc/dhcp/dhcpd.conf` dan lakukan konfigurasi:

```
subnet 10.151.77.104 netmask 255.255.255.248 {  
    option routers 10.151.77.107; 
}
    
subnet 192.168.1.0 netmask 255.255.255.0 {
    option routers 10.151.77.105;
    option broadcast-address 10.151.77.111;
    option domain-name-servers 10.151.77.106;
}

subnet 192.168.2.0 netmask 255.255.255.0 {
    option routers 10.151.77.105;
    option broadcast-address 10.151.77.111;
    option domain-name-servers 10.151.77.106;
}
```

Lakukan restart dhcp-server dengan `service isc-dhcp-server restart`.

Tambahkan pada interfaces setiap klien (**SIDOARJO** dan **GRESIK**):

```
auto eth0
iface eth0 inet dhcp
```

Restart **SIDOARJO** dan **GRESIK** dengan `service networking restart`.

*Notes: Jika sebelumnya terdapat syntax iptables lain di suatu uml, masukkan syntax*

```
iptables -F
iptables -t nat -F
```


## Soal 1

Masukkan *syntax* iptables berikut pada **SURABAYA** :

```
iptables -t nat -A POSTROUTING -s 192.168.0.0/16 -o eth0 -j SNAT --to-source 10.151.76.54
```

Lakukan ping keluar, contohnya di **SURABAYA** dan **MOJOKERTO**

![Img](img/1a.jpg)

![Img](img/1b.jpg)

## Soal 2

Masukkan *syntax* iptables berikut pada **SURABAYA** :

```
iptables -A FORWARD -d 10.151.77.104/29 -i eth0 -p tcp --dport 22 -j DROP
```

Install **netcat** pada **MALANG** dengan `apt-get update` dan `apt-get install netcat -y`.

Masukkan `nc -l -p <port>` pada uml **MALANG** dan `nc <ip_malang> <port>` pada putty atau wsl.

* Uji 1 `port 23`: (Hanya untuk testing)

![Img](img/2.1.jpg)

* Uji 2 `port 22`

![Img](img/2.4.jpg)

## Soal 3

Masukkan *syntax* iptables pada **MALANG** dan **MOJOKERTO**:

```
iptables -A INPUT -p icmp -m connlimit --connlimit-above 3 --connlimit-mask 0 -j DROP
```

Lakukan ping ke **MALANG** atau **MOJOKERTO** dari 4 uml yang berbeda, dalam hal ini yaitu **KEDIRI**, **GRESIK**, **BATU**, **SIDOARJO** melakukan `ping 10.151.77.106`, hasilnya salah satu uml (**BATU**) ter-drop.

![Img](img/3.8.jpg)

## Soal 4

Masukkan *syntax* iptables pada **MALANG**:

```
iptables -A INPUT -s 192.168.1.0/24 -m time --timestart 07:00 --timestop 17:00 --weekdays Mon,Tue,Wed,Thu,Fri -j ACCEPT
iptables -A INPUT -s 192.168.1.0/24 -j REJECT
```

Lakukan ping ke **MALANG** dari subnet **SIDOARJO** dengan `ping 10.151.77.106` . Berdasarkan soal "hanya diperbolehkan pada pukul 07.00 - 17.00 pada hari Senin sampai Jumat", maka:

![Img](img/4.2.jpg)

## Soal 5

Masukkan *syntax* iptables pada **MALANG**:

```
iptables -A INPUT -s 192.168.2.0/24 -m time --timestart 07:00 --timestop 17:00 -j REJECT
```

Lakukan ping ke **MALANG** dari subnet **GRESIK** dengan `ping 10.151.77.106` . Berdasarkan soal "hanya diperbolehkan pada pukul 17.00 hingga pukul 07.00 setiap harinya", maka:

![Img](img/5.2.jpg)

## Soal 6

Masukkan *syntax* iptables pada **SURABAYA**:

```
iptables -A PREROUTING -t nat -p tcp -d 10.151.77.106 --dport 80 -m statistic --mode nth --every 2 --packet 0 -j DNAT --to-destination 192.168.0.10:80
iptables -A PREROUTING -t nat -p tcp -d 10.151.77.106 --dport 80 -j DNAT --to-destination 192.168.0.11:80
iptables -t nat -A POSTROUTING -p tcp -d 192.168.0.10 --dport 80 -j SNAT --to-source 10.151.77.106:80
iptables -t nat -A POSTROUTING -p tcp -d 192.168.0.11 --dport 80 -j SNAT --to-source 10.151.77.106:80
```
Install **netcat** pada **PROBOLINGGO** dan **MADIUN** dengan `apt-get update` dan `apt-get install netcat -y`.

Masukkan `nc -l -p <port>` pada uml **PROBOLINGGO** & **MADIUN** dan `nc <ip_malang> <port>` pada putty atau wsl.

![Img](img/6.2.jpg)

## Soal 7

Masukkan *syntax* iptables pada **SURABAYA**:

```
iptables -A FORWARD -d 10.151.77.104/29 -i eth0 -p tcp --dport 22 -j LOG --log-prefix "FORWARD TCP-DROPPED: "
iptables -A FORWARD -d 10.151.77.104/29 -i eth0 -p tcp --dport 22 -j DROP #surabaya
```

Lakukan hal yang sama seperti nomor 2.  Masukkan `nc -l -p <port>` pada uml **MALANG** dan `nc <ip_malang> <port>` pada putty atau wsl. Hasil catatan log dapat dilihat di uml **SURABAYA** atau mengetik `tail /var/log/messages`.

![Img](img/7.1.jpg)

Masukkan *syntax* iptables pada **MALANG** dan **MOJOKERTO**:
```
iptables -A INPUT -p icmp -m connlimit --connlimit-above 3 --connlimit-mask 0 -j LOG --log-prefix "Connection Limit --> DROPPED : "
iptables -A INPUT -p icmp -m connlimit --connlimit-above 3 --connlimit-mask 0 -j DROP
```
Lakukan hal yang sama seperti nomor 3. Lakukan ping ke **MALANG** dari 4 uml yang berbeda, dalam hal ini yaitu **KEDIRI**, **GRESIK**, **BATU**, **SIDOARJO** melakukan `ping 10.151.77.106`, hasilnya salah satu uml (**GRESIK**) ter-drop. Hasil catatan log dapat dilihat di uml **MALANG** atau mengetik `tail /var/log/messages`.

![Img](img/7.4.jpg)
