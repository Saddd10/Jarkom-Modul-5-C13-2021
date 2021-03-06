# Jarkom-Modul-5-C13-2021

## Anggota Kelompok :

| Anggota              | NRP            |
| -------------------- | -------------- |
| M. Auliya Mirzaq R.  | 05111940000065 |
| M. Akmal Joedhiawan  | 05111940000125 |
| M. Arsyad Ardiansyah | 05111940000228 |

## Soal

Setelah kalian mempelajari semua modul yang telah diberikan, Luffy ingin meminta bantuan untuk terakhir kalinya kepada kalian. Dan kalian dengan senang hati mau membantu Luffy.

**(A)**. Tugas pertama kalian yaitu membuat topologi jaringan sesuai dengan rancangan yang diberikan Luffy dibawah ini:

![img](./img/soaltopologi.png)

Keterangan :

- Doriki adalah DNS Server
- Jipangu adalah DHCP Server
- Maingate dan Jorge adalah Web Server
- Jumlah Host pada Blueno adalah 100 host
- Jumlah Host pada Cipher adalah 700 host
- Jumlah Host pada Elena adalah 300 host
- Jumlah Host pada Fukurou adalah 200 host

**(B)**. Karena kalian telah belajar subnetting dan routing, Luffy ingin meminta kalian untuk membuat topologi tersebut menggunakan teknik CIDR atau VLSM. setelah melakukan subnetting,

**(C)**. Kalian juga diharuskan melakukan Routing agar setiap perangkat pada jaringan tersebut dapat terhubung.

**(D)**. Tugas berikutnya adalah memberikan ip pada subnet Blueno, Cipher, Fukurou, dan Elena secara dinamis menggunakan bantuan DHCP server. Kemudian kalian ingat bahwa kalian harus setting DHCP Relay pada router yang menghubungkannya.

1. Agar topologi yang kalian buat dapat mengakses keluar, kalian diminta untuk mengkonfigurasi Foosha menggunakan iptables, tetapi Luffy tidak ingin menggunakan MASQUERADE.
2. Kalian diminta untuk mendrop semua akses HTTP dari luar Topologi kalian pada server yang merupakan DHCP Server dan DNS Server demi menjaga keamanan.
3. Karena kelompok kalian maksimal terdiri dari 3 orang. Luffy meminta kalian untuk membatasi DHCP dan DNS Server hanya boleh menerima maksimal 3 koneksi ICMP secara bersamaan menggunakan iptables, selebihnya didrop.

Kemudian kalian diminta untuk membatasi akses ke Doriki yang berasal dari subnet Blueno, Cipher, Elena dan Fukuro dengan beraturan sebagai berikut

4. Akses dari subnet Blueno dan Cipher hanya diperbolehkan pada pukul 07.00 - 15.00 pada hari Senin sampai Kamis.
5. Akses dari subnet Elena dan Fukuro hanya diperbolehkan pada pukul 15.01 hingga pukul 06.59 setiap harinya.
   Selain itu di reject
6. Karena kita memiliki 2 Web Server, Luffy ingin Guanhao disetting sehingga setiap request dari client yang mengakses DNS Server akan didistribusikan secara bergantian pada Jorge dan Maingate

Luffy berterima kasih pada kalian karena telah membantunya. Luffy juga mengingatkan agar semua aturan iptables harus disimpan pada sistem atau paling tidak kalian menyediakan script sebagai backup.

## Solusi

**(A)** Topologi yang sudah kami buat adalah sebagai berikut.

![img](./img/solusitopologi.png)

**(B)** Untuk pembagian IP pada toplogi di atas kami menggunakan VLSM

untuk pembagian IP akan dilakukan penghitungan kebutuhan IP untuk tiap subnet nya. Hasilnya dapat dilihat pada tabel di bawah :

| Subnet    | Jumlah IP | Netmask |
| --------- | --------- | ------- |
| A1        | 3         | /29     |
| A2        | 101       | /25     |
| A3        | 701       | /22     |
| A4        | 2         | /30     |
| A5        | 2         | /30     |
| A6        | 301       | /23     |
| A7        | 201       | /24     |
| A8        | 3         | /29     |
| **TOTAL** | **1314**  | **/21** |

Berdasarkan total IP dan netmask yang dibutuhkan, maka kita dapat menggunakan netmask /21 untuk memberikan pengalamatan pada subnet.

Setelah menghitung total IP yang dibutuhkan, subnet paling besar yang dibentuk memiliki NID 192.190.0.0 dan netmask /21. Dengan menggunakan NID dan netmask tersebut kita dapat menghitung pembagian alamat IP untuk tiap subnet pada gambar pohon pengalamatan dibawah.

![img](./img/treesubnet.png)

Dari pohon tersebut akan mendapat pembagian alamt IP untuk setiap subnet sebagai berikut:

![img](./img/ipsubnet.png)

**(C)** Konfigurasi untuk routing dapat dilihat di bawah ini:

Tapi sebelum melakukan routing, harus terlebih dahulu untuk melakukan konfigurasi IP sesuai dengan yang dihitung sebelumnya

**Konfigurasi IP:**

**Router:**

- **Foosha**

  ```
  auto lo
  iface lo inet loopback

  auto eth0
  iface eth0 inet static
  address 192.168.122.46
  netmask 255.255.255.0
  gateway 192.168.122.1

  auto eth1
  iface eth1 inet static
  address 192.190.0.9
  netmask 255.255.255.252

  auto eth2
  iface eth2 inet static
  address 192.190.0.5
  netmask 255.255.255.252
  ```

- **Water7**

  ```
  auto lo
  iface lo inet loopback

  auto eth0
  iface eth0 inet static
  address 192.190.0.6
  netmask 255.255.255.252
  gateway 192.190.0.5

  auto eth1
  iface eth1 inet static
  address 192.190.0.17
  netmask 255.255.255.248

  auto eth2
  iface eth2 inet static
  address 192.190.0.129
  netmask 255.255.255.128

  auto eth3
  iface eth3 inet static
  address 192.190.4.1
  netmask 255.255.252.0
  ```

- **Guanhao**

  ```
  auto lo
  iface lo inet loopback

  auto eth0
  iface eth0 inet static
  address 192.190.0.10
  netmask 255.255.255.252
  gateway 192.190.0.9

  auto eth1
  iface eth1 inet static
  address 192.190.0.33
  netmask 255.255.255.248

  auto eth2
  iface eth2 inet static
  address 192.190.2.1
  netmask 255.255.254.0

  auto eth3
  iface eth3 inet static
  address 192.190.1.1
  netmask 255.255.255.0
  ```

**Server:**

- **Doriki**

  ```
  auto lo
  iface lo inet loopback

  auto eth0
  iface eth0 inet static
  address 192.190.0.18
  netmask 255.255.255.248
  gateway 192.190.0.17
  ```

- **Jipangu**

  ```
  auto lo
  iface lo inet loopback

  auto eth0
  iface eth0 inet static
  address 192.190.0.19
  netmask 255.255.255.248
  gateway 192.190.0.17
  ```

- **Maingate**

  ```
  auto lo
  iface lo inet loopback

  auto eth0
  iface eth0 inet static
  address 192.190.0.35
  netmask 255.255.255.248
  gateway 192.190.0.33
  ```

- **Jorge**

  ```
  auto lo
  iface lo inet loopback

  auto eth0
  iface eth0 inet static
  address 192.190.0.34
  netmask 255.255.255.248
  gateway 192.190.0.33
  ```

**Client:**

- **Blueno(100Host)**

  ```
  auto eth0
  iface eth0 inet dhcp
  ```

- **Chiper(700Host)**

  ```
  auto eth0
  iface eth0 inet dhcp
  ```

- **Elena(300Host)**

  ```
  auto eth0
  iface eth0 inet dhcp
  ```

- **Fukurou(200Host)**

  ```
  auto eth0
  iface eth0 inet dhcp
  ```

**Konfigurasi Routing:**

- **Foosha**

  ```
  route add -net 192.190.0.16 netmask 255.255.255.248 gw 192.190.0.6
  route add -net 192.190.0.128 netmask 255.255.255.128 gw 192.190.0.6
  route add -net 192.190.4.0 netmask 255.255.252.0 gw 192.190.0.6
  route add -net 192.190.2.0 netmask 255.255.254.0 gw 192.190.0.10
  route add -net 192.190.1.0 netmask 255.255.255.0 gw 192.190.0.10
  route add -net 192.190.0.32 netmask 255.255.255.248 gw 192.190.0.10
  ```

Lalu untuk mengecek apakah konfigurasi dari routing sebelumnya sudah berhasil dapat dicek menggunakan perintah :

```
route -n
```

Lalu jika berhasil akan tampil konfigurasi routing yang sudah diatur sebagai berikut:

![img](./img/c-1.png)

Kita juga bisa cek koneksi antara perangkat dengan melakukan ping, misalnya dari `Doriki` ke `Foosha` sebagai berikut:

![img](./img/c-2.png)

**(D)** Kemudian memberikan ip pada subnet Blueno, Cipher, Fukurou, dan Elena secara dinamis menggunakan bantuan DHCP server, untuk caranya bisa dilihat di bawah:



### 1. Konfigurasi Iptables tanpa MASQUERADE

Untuk menghubungkan topologi dengan jaringan luar dibutuhkan konfigurasi `iptables`, namun pada soal ini tidak menggunakan `masquerade`. Untuk konfigurasinya dapat dilihat di bawah ini:

**Node Foosha:**
Pada node `Foosha` silahkan untuk menjalankan perintah berikut:
```
iptables -t nat -A POSTROUTING -s 192.190.0.0/21 -o eth0 -j SNAT --to-source 192.168.122.46
```
Keterangan:

```-t nat```: Menggunakan tabel NAT karena akan mengubah alamat asal dari paket

```-A POSTROUTING```: Menggunakan chain POSTROUTING karena mengubah asal paket setelah routing

```-s 192.190.0.0/21```: Mendifinisikan alamat asal dari paket yaitu semua alamat IP dari subnet 192.190.0.0/21

```-o eth0```: Paket keluar dari eth0 Foosha

```-j SNAT```: Menggunakan target SNAT untuk mengubah source atau alamat asal dari paket

```--to-s (ip eth0)```: Mendefinisikan IP source, di mana digunakan eth0 Foosha dengan rentang IP 192.168.122.0 sampai 192.168.122.255

### 2. Drop akses di luar topologi pada DHCP dan DNS server

Untuk melakukan drop akses dari luar topologi ke DHCP dan DNS server bisa dengan menjalankan perintah berikut:

```
iptables -A FORWARD -d 192.190.0.16/29 -i eth0 -p tcp --dport 80 -j DROP
```

Keterangan:

```-A FORWARD```: Menggunakan chain FORWARD

```-p tcp```: Mendefinisikan protokol yang digunakan, yaitu tcp

```--dport 80```: Mendefinisikan port yang digunakan, yaitu 80 (HTTP)

```-d 192.190.0.16/29```: Mendefinisikan alamat tujuan dari paket (DHCP dan DNS SERVER ) berada pada subnet 192.190.0.16/29

```-i eth0```: Paket masuk dari eth0 Foosha

```-j DROP```: Paket di-drop

Untuk melakukan pengecekan dapat mengikuti langkah berikut:

1. Ping pada node Doriki/Jipangu dengan perintah ```ping monta.if.its.ac.id```

![2a](https://user-images.githubusercontent.com/73766214/145679993-38e4b898-b7c3-4932-8fbb-911dc796fcea.jpg)

![2b](https://user-images.githubusercontent.com/73766214/145679996-4b80ccc9-e6e7-4a71-8168-12c96564d209.jpg)

### 3. Membatasi DHCP dan DNS Server hanya boleh menerima maksimal 3 koneksi ICMP secara bersamaan 

Untuk membatasi akses yang boleh diterima oleh DHCP dan DNS Server maksimal 3 bisa dengan menjalankan perintah berikut:

```
iptables -A INPUT -p icmp -m connlimit --connlimit-above 3 --connlimit-mask 0 -j DROP
```

Keterangan:

```-A INPUT```: Menggunakan chain INPUT

```-p icmp```: Mendefinisikan protokol yang digunakan, yaitu ICMP (ping)

```-m connlimit```: Menggunakan rule connection limit

```--connlimit-above 3```: Limit yang ditangkap paket adalah di atas 3

```--connlimit-mask 0```: Hanya memperbolehkan 3 koneksi setiap subnet dalam satu waktu

```-j DROP```: Paket di-drop


Untuk melakukan pengecekan dapat mengikuti langkah berikut:

1. Masuk ke 4 node berbeda
2. Ping ke arah Jipangu/Doriki secara bersamaan

![3a](https://user-images.githubusercontent.com/73766214/145672904-d6608381-76f9-4664-bb64-feb2dc663a70.jpg)

![3b](https://user-images.githubusercontent.com/73766214/145672919-ff3913bc-e317-45ae-814c-2ef566b29d9d.jpg)

![3c](https://user-images.githubusercontent.com/73766214/145672922-e58c748d-1137-4357-b990-e22846ab1ea2.jpg)

![3d](https://user-images.githubusercontent.com/73766214/145672927-921dd4aa-720a-4290-b4a3-ad53cebe14a3.jpg)

Terlihat dari node Blueno, Cipher dan Elena dapat melakukan ping ke Doriki/Jipangu, tetapi Fukurou tidak bisa melakukan ping karena maksimal 3 koneksi ke Doriki/Jipangu secara bersamaan.

### 4. Akses dari subnet Blueno dan Cipher ke Doriki hanya diperbolehkan pada pukul 07.00 - 15.00 pada hari Senin sampai Kamis.

Untuk membuat Subnet Bluerno dan Cipher hanya diperbolehkan pada pukul 07.00 - 15.00 pada hari Senin sampai Kamis bisa dengan menjalankan perintah berikut:

#### Cipher

```
iptables -A INPUT -s 192.190.4.0/22 -d 192.190.0.16/29 -m time --timestart 07:00 --timestop 15:00 --weekdays Mon,Tue,Wed,Thu -j ACCEPT
iptables -A INPUT -s 192.190.4.0/22 -j REJECT
```

#### Blueno

```
iptables -A INPUT -s 192.190.0.128/25 -d 192.190.0.16/29 -m time --timestart 07:00 --timestop 15:00 --weekdays Mon,Tue,Wed,Thu -j ACCEPT
iptables -A INPUT -s 192.190.0.128/25 -j REJECT
```

Keterangan:

```A INPUT```: Menggunakan chain INPUT

```s 192.190.0.128/25```: Mendifinisikan alamat asal dari paket yaitu IP dari subnet Blueno

```s 192.190.4.0/22```: Mendifinisikan alamat asal dari paket yaitu IP dari subnet Chiper

```d 192.190.0.16/29```: Mendifinisikan alamat tujuan dari paket yaitu IP dari subnet Doriki

```m time```: Menggunakan rule time

```-timestart 07:00```: Mendefinisikan waktu mulai yaitu 07:00

```-timestop 15:00```: Mendefinisikan waktu berhenti yaitu 15:00

```--weekdays Mon,Tue,Wed,Thu```: Mendefinisikan hari yaitu Senin hingga Kamis

```-j ACCEPT```: Paket di-accept

```-j REJECT```: Paket ditolak

Apabila kita melakukan ping dari Blueno/Cipher ke Doriki di luar jam tersebut maka tidak bisa terkoneksi.

![4a](https://user-images.githubusercontent.com/73766214/145673036-ea1eb406-f2f6-4aaa-a31c-e3d59e9b8580.jpg)

![4b](https://user-images.githubusercontent.com/73766214/145673040-c4d8f98a-cb7d-4ab4-95ef-c94b637e0f71.jpg)

### 5. Akses dari subnet Elena dan Fukuro hanya diperbolehkan pada pukul 15.01 hingga pukul 06.59 setiap harinya.

Untuk membuat subnet Elena dan Fukuro hanya diperbolehkan pada pukul 15.01 - 06.59 setiap hari bisa dengan menjalankan perintah berikut:

#### Elena

```
iptables -A INPUT -s 192.190.2.0/23 -d 192.190.0.16/29 -m time --timestart 15:01 --timestop 06:59 -j ACCEPT
iptables -A INPUT -s 192.190.2.0/23 -j REJECT
```

#### Fukuro

```
iptables -A INPUT -s 192.190.1.0/24 -d 192.190.0.16/29 -m time --timestart 15:01 --timestop 06:59 -j ACCEPT
iptables -A INPUT -s 192.190.1.0/24 -j REJECT
```

Keterangan:

```A INPUT``` : Menggunakan chain INPUT

```s 192.190.2.0/23``` : Mendifinisikan alamat asal dari paket yaitu IP dari subnet Elena

```s 192.190.1.0/24``` : Mendifinisikan alamat asal dari paket yaitu IP dari subnet Fukurou

```m time``` : Menggunakan rule time

```-timestart 15:01``` : Mendefinisikan waktu mulai yaitu 07:00

```-timestop 06:59``` : Mendefinisikan waktu berhenti yaitu 15:00

```-j ACCEPT``` : Paket di-accept

```-j REJECT``` : Paket ditolak

Apabila kita melakukan ping dari Elena/Fukurou ke Doriki di luar jam tersebut maka tidak bisa terkoneksi.

![5a](https://user-images.githubusercontent.com/73766214/145673061-fe407c06-e10c-456f-a5b0-a21e1719578d.jpg)

![5b](https://user-images.githubusercontent.com/73766214/145673064-05eea834-8028-44d7-8d71-c26798c5025f.jpg)

### 6. Guanhao disetting sehingga setiap request dari client yang mengakses DNS Server akan didistribusikan secara bergantian pada Jorge dan Maingate

Untuk bisa menjalankan soal diatas maka perlu dijalankan script berikut ini di route Guanhao:

```
iptables -t nat -A PREROUTING -d 192.190.0.16 -p tcp -m statistic --mode nth --every 2 --packet 0 -j DNAT --to-destination 192.190.0.34
iptables -t nat -A PREROUTING -d 192.190.0.16 -p tcp -j DNAT --to-destination 192.190.0.35
iptables -t nat -A POSTROUTING -p tcp -d 192.190.0.34 -j SNAT --to-source 192.190.0.16
iptables -t nat -A POSTROUTING -p tcp -d 192.190.0.35 -j SNAT --to-source 192.190.0.16
```

1. Install web server pada Maingate dan Jorge dengan command ```apt-get update``` dan ```apt-get install apache2 -y``` 
2. Isi file ```index.html``` pada folder ```/var/www/html/```
3. Coba di salah satu klien dengan perintah ```curl 192.190.0.16``` yaitu merupakan subnet DNS.


