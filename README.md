# Jarkom_Modul5_Lapres_C02

Laporan Resmi Modul 5 Praktikum Jaringan Komputer
## Kelompok C02
* Aulia Ihza Hendradi (05111840000089)
* Excel Deo Cornelius (05111840000117)

## Topologi

rancangan topologi
![1(1)](https://user-images.githubusercontent.com/52096462/103269240-fe70a400-49e7-11eb-8c77-051ebd5a49a9.png)

#### Subnetting dan Routing
- Teknik pembuatan topologi yang digunakan adalah **CIDR**.
- Server **Malang** dan **Mojokerto** menggunakan IP DMZ, sehingga diberikan subnet **10.151.77.72/29**.

![1(4)](https://user-images.githubusercontent.com/52096462/103269233-fc0e4a00-49e7-11eb-9499-3e091a19dcb5.png)

![diagram1 (4)](https://user-images.githubusercontent.com/52096462/103269238-fdd80d80-49e7-11eb-9c37-426eca94caed.jpg)

- Routing pada UML hanya dilakukan di **Surabaya**.
```
route add -net 10.151.77.72 netmask 255.255.255.248 gw 192.168.5.2
route add -net 192.168.4.0 netmask 255.255.255.0 gw 192.168.5.2
route add -net 192.168.0.0 netmask 255.255.254.0 gw 192.168.2.2
```

#### DHCP Server dan DHCP Relay
- Pada **Mojokerto**, diinstal DHCP Server menggunakan perintah **apt-get install isc-dhcp-server**.
- Kemudian dibuka **/etc/dhcp/dhcpd.conf**. Tambahkan:
```
subnet 192.168.4.0 netmask 255.255.255.0 {
    range 192.168.4.2 192.168.4.254;
    option routers 192.168.4.1;
    option broadcast-address 192.168.4.255;
    option domain-name-servers 10.151.77.74;
    default-lease-time 600;
    max-lease-time 7200;
}

subnet 192.168.0.0 netmask 255.255.255.0 {
    range 192.168.0.2 192.168.0.254;
    option routers 192.168.0.1;
    option broadcast-address 192.168.0.255;
    option domain-name-servers 10.151.77.74;
    default-lease-time 600;
    max-lease-time 7200;
}

subnet 10.151.77.72 netmask 255.255.255.248 {

}
```
- Masukan perintah **service isc-dhcp-server restart**.
- Kemudian, install DHCP relay menggunakan **apt-get install isc-dhcp-relay** pada router **Batu** dan **Kediri** dengan isi INTERFACES berupa IP **Mojokerto** (Dalam kasus ini, IP Mojokerto adalah *10.151.77.75*).
- Lakukan **service networking restart** pada **Gresik** dan **Sidoarjo**.

## Soal dan Jawaban

### 1
Agar topologi yang kalian buat dapat mengakses keluar, kalian diminta untuk mengkonfigurasi SURABAYA menggunakan iptables, namun Bibah tidak ingin kalian menggunakan MASQUERADE.

- Di **Surabaya**, diberikan iptables berikut.
```
iptables -t nat -A POSTROUTING -s 192.168.0.0/21 -o eth0 -j SNAT --to-source 10.151.76.38
```

![1](1.png)

### 2
Kalian diminta untuk mendrop semua akses SSH dari luar Topologi (UML) Kalian pada server yang memiliki ip DMZ (DHCP dan DNS SERVER) pada SURABAYA demi menjaga keamanan.

- Di **Surabaya**, diberikan iptables berikut.
```
iptables -A FORWARD --dport 22 -d 10.151.77.72/29 -i eth0 -j DROP
```

![2](2.png)

### 3
Karena tim kalian maksimal terdiri dari 3 orang, Bibah meminta kalian untuk membatasi DHCP dan DNS server hanya boleh menerima maksimal 3 koneksi ICMP secara bersamaan yang berasal dari mana saja menggunakan iptables pada masing masing server, selebihnya akan di DROP.

- Di **Malang** dan **Mojokerto**, diberikan iptables berikut.
```
iptables -A INPUT -p icmp -m connlimit --connlimit-above 3 --connlimit-mask 0 -j DROP
```

![3](3.png)

### Batasi akses ke MALANG yang berasal dari SUBNET SIDOARJO dan SUBNET GRESIK dengan peraturan sebagai berikut:

#### 4
Akses dari subnet SIDOARJO hanya diperbolehkan pada pukul 07.00 - 17.00 pada hari Senin sampai Jumat.

- Pada **Malang**, diberikan iptables berikut.
```
iptables -A INPUT -s 192.168.4.0/24 -m time --timestart 07:00 --timestop 17:00 --weekdays Mon,Tue,Wed,Thu,Fri -j ACCEPT
iptables -A INPUT -s 192.168.4.0/24 -m time --timestart 17:01 --timestop 06:59 -j REJECT
```

#### 5
Akses dari subnet GRESIK hanya diperbolehkan pada pukul 17.00 hingga pukul 07.00 setiap harinya.

- Pada **Malang**, diberikan iptables berikut.
```
iptables -A INPUT -s 192.168.0.0/24 -m time --timestart 07:01 --timestop 16:59 -j REJECT
```

![4](4.png)

![5](5.png)

#### 6
Bibah ingin SURABAYA disetting sehingga setiap request dari client yang mengakses DNS Server akan didistribusikan secara bergantian pada PROBOLINGGO port 80 dan MADIUN port 80.

- Di **Surabaya**, diberikan iptables berikut.
```
iptables -A PREROUTING -t nat -p tcp -d 10.151.77.74 --dport 80 statistic --mode nth --every 2 --packet 0 -j DNAT --to-destination 192.168.1.3:80
iptables -A PREROUTING -t nat -p tcp -d 10.151.77.74 --dport 80 statistic --j DNAT --to-destination 192.168.1.2:80

iptables -t nat -A POSTROUTING -p tcp -d 192.168.1.3 --dport 80 -j SNAT --to-source 10.151.77.74:80
iptables -t nat -A POSTROUTING -p tcp -d 192.168.1.2 --dport 80 -j SNAT --to-source 10.151.77.74:80
```

![6](6.png)

#### 7
Bibah ingin agar semua paket didrop oleh firewall (dalam topologi) tercatat dalam log pada setiap UML yang **memiliki aturan drop**.

```
# Pada iptables nomor 2, di Surabaya tambahkan iptables berikut sebelum iptables tersebut
iptables -A FORWARD --dport 22 -d 10.151.77.72/29 -i eth0 -j LOG --log-prefix "FORWARD:DROPPED: "

# Pada iptables nomor 3, di Malang dan Mojokerto tambahkan iptables berikut sebelum iptables tersebut
iptables -A INPUT -p icmp -m connlimit --connlimit-above 3 --connlimit-mask 0 -j LOG --log-prefix "INPUT:DROPPED: "
```

![7_1](7_1.png)

![7_2](7_2.png)
