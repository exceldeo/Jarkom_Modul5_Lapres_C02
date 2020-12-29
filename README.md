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

* **MOJOKERTO** + **MALANG** merupakan **DHCP Server** diberikan **IP DMZ** 

* **MADIUN** dan **PROBOLINGGO** merupakan **WEB Server**

* **Setiap Server** diberikan memory sebesar **128M**

* **Client** dan **Router** diberikan memori sebesar **96M**

* Jumlah klien pada subnet **SIDOARJO 200 Host**

* Jumlah klien pada subnet **GRESIK 210 Host**

**(B)** Melakukan subnetting dengan VLSM:

![1(4)](https://user-images.githubusercontent.com/52096462/103269233-fc0e4a00-49e7-11eb-9499-3e091a19dcb5.png)

Pembuatan Tree:

![diagram1 (4)](https://user-images.githubusercontent.com/52096462/103269238-fdd80d80-49e7-11eb-9c37-426eca94caed.jpg)

![tabel](https://user-images.githubusercontent.com/52096462/103274774-113da580-49f5-11eb-8121-d3ec6af2773c.png)


**(C)** Melakukan Routing

Sintaks untuk **topo.sh**

![4](https://user-images.githubusercontent.com/52096462/103274813-2d414700-49f5-11eb-89bf-76124d4738b9.png)

Lalu  jalankan `bash topo.sh` pada *putty* dan masukkan *username* dan *password* default. Pada router **SURABAYA**, **BATU**, **KEDIRI**  lakukan setting `sysctl` dengan mengetikkan perintah `nano /etc/sysctl.conf`, dan tambahkan `net.ipv4.conf.all.accept_source_route = 1`.

Lalu setting IP pada setiap *interfaces* uml dengan mengetikkan `nano /etc/network/interfaces` sebagai berikut:

**SURABAYA**

![5](https://user-images.githubusercontent.com/52096462/103274817-2f0b0a80-49f5-11eb-8fae-dd8519023634.png)

**BATU**

![6](https://user-images.githubusercontent.com/52096462/103274819-2f0b0a80-49f5-11eb-93e6-d17822dc7bac.png)

**KEDIRI**

![7](https://user-images.githubusercontent.com/52096462/103274820-2fa3a100-49f5-11eb-988f-f5f228018217.png)

**MALANG**

![8](https://user-images.githubusercontent.com/52096462/103274821-303c3780-49f5-11eb-92fc-f116abb76560.png)

**MOJOKERTO**

![9](https://user-images.githubusercontent.com/52096462/103274982-9fb22700-49f5-11eb-966e-b5377892c075.png)

**PROBOLINGGO**


![10](https://user-images.githubusercontent.com/52096462/103274983-a04abd80-49f5-11eb-9ff2-f8d437b5ef2b.png)

**MADIUN**

![11](https://user-images.githubusercontent.com/52096462/103274984-a0e35400-49f5-11eb-91ea-cb083b2be9f8.png)

**SIDOARJO**


![12](https://user-images.githubusercontent.com/52096462/103274985-a17bea80-49f5-11eb-869a-803463ef078d.png)

**GRESIK**


![13](https://user-images.githubusercontent.com/52096462/103274987-a17bea80-49f5-11eb-9f9e-53cdd75aecd9.png)

Lakukan proses routing dari **SURABAYA** ke subnet yang tidak berhubungan secara langsung dengan membuat script yang bernama `roxxx.sh` di **SURABAYA**, dan masukkan sintaks berikut:

![rute sh](https://user-images.githubusercontent.com/52096462/103275166-20712300-49f6-11eb-9b44-bc6ca462d739.png)

Jalannkan script `roxxx.sh` di **SURABAYA** dan restart network dengan mengetikkan `service networking restart` di setiap UML. Untuk menguji koneksi antara subnet, terlebih dahulu diuji coba dengan mengetikkan `iptables –t nat –A POSTROUTING –o eth0 –j MASQUERADE –s 192.168.0.0/16` pada router **SURABAYA**. IP Tables dapat dimasukkan ke dalam *script*, dalam hal ini, nama script berupa `table1.sh`.

**(D)** Memberikan IP pada subnet **SIDOARJO** dan **GRESIK** secara dinamis menggunakan DHCP SERVER

* Pada **SURABAYA**, **BATU**, **KEDIRI** lakukan instalasi **ISC-DHCP-Relay**. Lakukan perintah `apt-get update` pada ketiga uml tersebut dan masukkan *syntax* `apt-get install isc-dhcp-relay -y`, setelah instalasi selesai, masukkan syntax `nano /etc/default/isc-dhcp-relay`. Sesuaikan `isc-dhcp-relay` seperti pada gambar berikut:

![14](https://user-images.githubusercontent.com/52096462/103274988-a2148100-49f5-11eb-8c05-d3cd100135c2.png)
![15](https://user-images.githubusercontent.com/52096462/103274990-a2ad1780-49f5-11eb-9190-b5458552fade.png)
![16](https://user-images.githubusercontent.com/52096462/103274992-a345ae00-49f5-11eb-9a69-b6c5fe6749cf.png)

Lakukan restart dhcp-server dengan `service isc-dhcp-relay restart`.

* Pada **MOJOKERTO** lakukan instalasi **ISC-DHCP-Server**. Lakukan perintah `apt-get update` pada ketiga uml tersebut dan masukkan *syntax* `apt-get install isc-dhcp-server -y`, setelah instalasi selesai, masukkan syntax `nano /etc/default/isc-dhcp-server`. Sesuaikan `isc-dhcp-server` seperti pada gambar berikut:

![17](https://user-images.githubusercontent.com/52096462/103274994-a345ae00-49f5-11eb-91ce-61a21ae7d6ca.png)

Berikutnya masukkan *syntax* `nano /etc/dhcp/dhcpd.conf` dan lakukan konfigurasi:

![17gantinyateksdibawahnya](https://user-images.githubusercontent.com/52096462/103276058-25cf6d00-49f8-11eb-95b5-7efa112dd6d2.PNG)

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

![syntax](https://user-images.githubusercontent.com/52096462/103276278-ac844a00-49f8-11eb-96ec-bc9a2a00dbe7.PNG)

![1](https://user-images.githubusercontent.com/52096462/103276270-aaba8680-49f8-11eb-877e-33762e988e69.PNG)

Lakukan ping keluar, contohnya di **SURABAYA** dan **MOJOKERTO**

![2](https://user-images.githubusercontent.com/52096462/103276275-abebb380-49f8-11eb-9ddb-c52a7e70136e.PNG)

## Soal 2

Masukkan *syntax* iptables berikut pada **SURABAYA** :

![syntax](https://user-images.githubusercontent.com/52096462/103276314-c6259180-49f8-11eb-9be5-ff5112805132.PNG)

Install **netcat** pada **MALANG** dengan `apt-get update` dan `apt-get install netcat -y`.

Masukkan `nc -l -p <port>` pada uml **MALANG** dan `nc <ip_malang> <port>` pada putty atau wsl.

* Uji 1 `port 23`: (Hanya untuk testing)

![1](https://user-images.githubusercontent.com/52096462/103276316-c6be2800-49f8-11eb-8330-53f52e3a8678.PNG)

* Uji 2 `port 22`

![2](https://user-images.githubusercontent.com/52096462/103276319-c756be80-49f8-11eb-954a-cbd12a2781cb.PNG)

## Soal 3

Masukkan *syntax* iptables pada **MALANG** dan **MOJOKERTO**:

![syntax](https://user-images.githubusercontent.com/52096462/103276407-fec56b00-49f8-11eb-86ca-acd0647ea1a7.PNG)

Lakukan ping ke **MALANG** atau **MOJOKERTO** dari 4 uml yang berbeda, dalam hal ini yaitu **KEDIRI**, **GRESIK**, **BATU**, **SIDOARJO** melakukan ping ke **MOJOKERTO** dengan `ping 10.151.77.27`, hasilnya salah satu uml (**GRESIK**) ter-drop.

![1](https://user-images.githubusercontent.com/52096462/103276400-fc631100-49f8-11eb-86ce-7023b6ede83d.PNG)
![2](https://user-images.githubusercontent.com/52096462/103276402-fd943e00-49f8-11eb-8339-8c0a49df3e0c.PNG)
![3](https://user-images.githubusercontent.com/52096462/103276404-fe2cd480-49f8-11eb-81d0-e8b75fad1b51.PNG)
![4yg kedrop](https://user-images.githubusercontent.com/52096462/103276405-fec56b00-49f8-11eb-8fc4-ab168f07000f.PNG)

## Soal 4

Masukkan *syntax* iptables pada **MALANG**:

![syntax](https://user-images.githubusercontent.com/52096462/103276908-51ebed80-49fa-11eb-9bbe-c40eea208c92.PNG)

Lakukan ping ke **MALANG** dari subnet **SIDOARJO** dengan `ping 10.151.77.26` . Berdasarkan soal "hanya diperbolehkan pada pukul 07.00 - 17.00 pada hari Senin sampai Jumat", maka:

![1](https://user-images.githubusercontent.com/52096462/103276907-50bac080-49fa-11eb-8e17-1bff7ae03d0e.PNG)

## Soal 5

Masukkan *syntax* iptables pada **MALANG**:

![syntax](https://user-images.githubusercontent.com/52096462/103276969-7cd64180-49fa-11eb-97fb-cde5a3742f5b.PNG)


Lakukan ping ke **MALANG** dari subnet **GRESIK** dengan `ping 10.151.77.26` . Berdasarkan soal "hanya diperbolehkan pada pukul 17.00 hingga pukul 07.00 setiap harinya", maka:

![1](https://user-images.githubusercontent.com/52096462/103276970-7d6ed800-49fa-11eb-83e4-60eaa1b40495.PNG)

## Soal 6

Masukkan *syntax* iptables pada **SURABAYA**:

![syntax](https://user-images.githubusercontent.com/52096462/103277007-94adc580-49fa-11eb-95d9-0f4f686bf0c2.PNG)

Install **netcat** pada **PROBOLINGGO** dan **MADIUN** dengan `apt-get update` dan `apt-get install netcat -y`.

Masukkan `nc -l -p <port>` pada uml **PROBOLINGGO** & **MADIUN** dan `nc <ip_malang> <port>` pada Gresik.

![1](https://user-images.githubusercontent.com/52096462/103277003-937c9880-49fa-11eb-9e70-40f1cd16fad4.PNG)

## Soal 7

Masukkan *syntax* iptables pada **SURABAYA**:

![syntax1](https://user-images.githubusercontent.com/52096462/103277039-ad1de000-49fa-11eb-9d28-431a481615e2.PNG)

Lakukan hal yang sama seperti nomor 2.  Masukkan `nc -l -p <port>` pada uml **MALANG** dan `nc <ip_malang> <port>` pada putty atau wsl. Hasil catatan log dapat dilihat di uml **SURABAYA** atau mengetik `tail /var/log/messages`.

![1](https://user-images.githubusercontent.com/52096462/103277035-aa22ef80-49fa-11eb-99a1-06dbb9326234.PNG)

Masukkan *syntax* iptables pada **MALANG** dan **MOJOKERTO**:

![syntax2](https://user-images.githubusercontent.com/52096462/103277041-adb67680-49fa-11eb-8cd6-cb3ad0d27658.PNG)

Lakukan hal yang sama seperti nomor 3. Lakukan ping ke **MOJOKERTO** dari 4 uml yang berbeda, dalam hal ini yaitu **KEDIRI**, **GRESIK**, **BATU**, **SIDOARJO** melakukan ping ke **MOJOKERTO** dengan`ping 10.151.77.27`, hasilnya salah satu uml (**GRESIK**) ter-drop. Hasil catatan log dapat dilihat di uml **MOJOKERTO** atau mengetik `tail /var/log/messages`.

![2](https://user-images.githubusercontent.com/52096462/103277037-abecb300-49fa-11eb-99a8-b2490b5fbbfb.PNG)
