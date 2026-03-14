---
title: "Instalasi Adguard Home untuk Proteksi Perangkat pada Home Network"
date: 2022-08-01T23:34:13+07:00
comments: true
showtoc: true
tocopen: true
ShowReadingTime: true
---

# Pendahuluan
Internet memberikan kemudahan dalam mendapatkan suatu informasi, seperti mendapatkan berita dan berkomunikasi dengan seseorang dari segala penjuru dunia. Pada sisi lain internet seperti pisau jika digunakan dibawah umur secara tidak langsung akan melukai dan membahayakan.
![Gambar pendahuluan ](/img/Instalasi-Adguard-Home-untuk-Proteksi-Perangkat-pada-Home-Network/pendahuluan.png)
Pada gambar diatas banyak sekali iklan yang menyerupai seolah-olah bagian dari konten website. Orang yang awam dalam penggunaan teknologi kemungkinan menjadi sasaran dikarenakan kurangnya pengetahuan dalam berseluncur di dunia internet.
Postingan ini akan menjelaskan bagaimana cara memproteksi jaringan dalam rumah untuk meminimalisir adanya iklan dan memberikan perlindungan tambahan dalam berselancar di internet menggunakan Adguard Home dengan aman.

# Prasyarat
Dalam instalasi Adguard Home saya menggunakan perangkat, yaitu:  
 1. STB HG680P (*openwrt*)
 2. Router (saya menggunakan Mikrotik RB941-2nD-TC)

# Adguard Home
Adguard Home adalah perangkat lunak *open-source* untuk jaringan yang bertujuan untuk memblokir iklan dan pelacakan. Selain itu, untuk mendapatkan kendali mengontrol semua lalu lintas jaringan yang ada di *home network*. Adguard Home menyediakan antarmuka website yang mudah digunakan untuk mengelola lalu lintas jaringan.

## Cara Kerja Adguard Home
Adguard Home bekerja seperti *DNS server* yang mengalihkan iklan dan pelacakan ke lubang hitam dan mencegah perangkat untuk tidak terhubung ke server tersebut.

## Kenapa Adguard Home
Adguard Home memudahkan proteksi tanpa harus membutuhkan konfigurasi pada perangkat *client*. Daripada kita konfigurasi tiap perangkat *client* satu per satu yang  membutuhkan waktu yang lebih banyak alangkah baiknya instalasi Adguard Home maka itu mencakup semuanya.

# Instalasi Adguard Home 
Saya menggunakan *STB* HG680P yang sudah *openwrt*. Pada repository [Adguard Home](https://github.com/AdguardTeam/AdGuardHome/releases) kali ini menggunakan versi *AdGuard Home v0.107.6* pilih *AdGuard_xxx_versiArsitektur* sesuaikan dengan tipe arsitektur. Untuk HG680P pilih arsitektur *ARM64*. 
![Gambar pemilihan arsitektur](/img/Instalasi-Adguard-Home-untuk-Proteksi-Perangkat-pada-Home-Network/pemilihan-arsitektur.png)
 
 1. Lakukan update dan upgrade package
 ```
 opkg update || opkg upgrade || opkg install wget || opkg install sudo ca-certificates 
 ca-bundle bind-tools
 ```
 ![Gambar update dan upgrade package](/img/Instalasi-Adguard-Home-untuk-Proteksi-Perangkat-pada-Home-Network/update-dan-upgrade-package.png)

 2. Buat direktori baru bernama *opt*
 ```
 mkdir /opt/
 ```

 3. Download file Adguard Home
 ```
 wget -c https://github.com/AdguardTeam/AdGuardHome/releases/download/v0.107.6/AdGuardHome_linux_arm64.tar.gz
 ```
![Gambar download adguard home](/img/Instalasi-Adguard-Home-untuk-Proteksi-Perangkat-pada-Home-Network/download-adguardhome.png)

 4. Lakukan ekstrak pada file *tar*
 ```
 tar xvfz AdGuardHome_linux_arm64.tar.gz
 ```
 ![Gambar ekstrak file](/img/Instalasi-Adguard-Home-untuk-Proteksi-Perangkat-pada-Home-Network/ekstrak-file.png)

 5. Pindahkan direktori Adguard Home di *opt*
 ```
 mv AdGuardHome /opt/
 ```

 # Instalasi Adguard Home Luci
  1. Download package local openwrt (ipk) [luci-app-adguard home](https://github.com/rufengsuixing/luci-app-adguardhome)
  ```
  wget -c https://github.com/rufengsuixing/luci-app-adguardhome/releases/download/1.8-11/luci-app-adguardhome_1.8-11_all.ipk
  ```
  ![Gambar download ipk luci](/img/Instalasi-Adguard-Home-untuk-Proteksi-Perangkat-pada-Home-Network/download-ipk-luci.png)

  2. Lalu lakukan instalasi package dengan perintah
  ```
  opkg install luci-app-adguardhome_1.8-11_all.ipk
  ```
  ![Gambar install luci adguardhome](/img/Instalasi-Adguard-Home-untuk-Proteksi-Perangkat-pada-Home-Network/install-luci-adguardhome.png)

  Dari tahap sini pemasangan luci AdGuardHome berhasil.
  ![Gambar status install luci adguard home](/img/Instalasi-Adguard-Home-untuk-Proteksi-Perangkat-pada-Home-Network/status-luci.png)

  # Setup Adguard Home Luci
 ![Gambar status install luci adguard home](/img/Instalasi-Adguard-Home-untuk-Proteksi-Perangkat-pada-Home-Network/setup-luci-adguardhome.png)
 Pada saat konfigurasi di Luci kita harus mengaktifkan terlebih dahulu. Jika didapatkan pesan “AdGuardHome RUNNING Not Redirect” adalah wajar karena kita belum konfigurasi pada Adguard Home. Perlu diperhatikan pada nilai “Redirect” lakukan set nilai yaitu **redirect 53 port to AdGuardHome** agar *traffic* data masuk di Adguard Home. Lalu pada nilai “Bin Path” isi dengan lokasi binary Adguard Home. Nilai “Config Path” biarkan default saja. Terakhir nilai “Work Dir” isi dengan direktori Adguard Home.

 # Setup Adguard Home *Web UI*
 Untuk konfigurasi Adguard Home *Web UI* buka dengan *iprouter:3000* karena nilai default dari konfigurasi *adguard home*.

![Gambar setup luci web-ui adguard home](/img/Instalasi-Adguard-Home-untuk-Proteksi-Perangkat-pada-Home-Network/setup-agh-webui.png)

Lalu ubah nilai “Listen Interface” pada *admin interface* menjadi **8080** dan “Listen Interface pada *DNS Server* menjadi **5353**. Lalu buat username dan password. Kembali ke *luci adguard home* pada nilai browser management ubah port menjadi 8080.
![Gambar ganti port luci adguard home](/img/Instalasi-Adguard-Home-untuk-Proteksi-Perangkat-pada-Home-Network/change-port-agh-webui.png)

# Mengalihkan semua traffic ke Adguard Home
Berfungsi untuk mengalihkan semua permintaan dari pengguna yang terhubung di jaringan  ke Adguard Home. Untuk konfigurasinya pada *sidebar menu luci* pilih *network* lalu klik *firewall*.
![Gambar setup traffic](/img/Instalasi-Adguard-Home-untuk-Proteksi-Perangkat-pada-Home-Network/setup-tf.png)

Gunakan *command* berikut:
```
iptables -t nat -A PREROUTING -i br-lan -p udp --dport 53 -j DNAT --to 192.168.1.1:5353
iptables -t nat -A PREROUTING -i br-lan -p tcp --dport 53 -j DNAT --to 192.168.1.1:5353
```

Jika memiliki *router* Mikrotik berikut *command* (opsional):
```
add chain=dstnat action=dst-nat to-addresses=<<Alamat IP AdGuardHome>> to-ports=<<Port AdGuardDNS pada Konfigurasi Dasar>> protocol=tcp dst-port=53 
add chain=dstnat action=dst-nat to-addresses=<<Alamat IP AdGuardHome>> to-ports=<<Port AdGuardDNS pada Konfigurasi Dasar>> protocol=udp dst-port=53
```
![Gambar setup traffic mikrotik](/img/Instalasi-Adguard-Home-untuk-Proteksi-Perangkat-pada-Home-Network/setup-tf-mikrotik.png)

Untuk memastikan apakah traffic berhasil dialihkan apa tidak, bisa masuk ke halaman admin Adguard Home.
![Gambar status adguard home web-ui](/img/Instalasi-Adguard-Home-untuk-Proteksi-Perangkat-pada-Home-Network/status-agh-webui.png)
Kalau *DNS Querie*s sudah ada nilainya berati traffic sudah berhasil dialihkan.

# Konfigurasi Adguard Home
Pada menu *general settings*:
 1. **Block domains using filters and hosts files** digunakan untuk block iklan.
Atur interval waktu sesuai dengan kebutuhan.
 2. **Use AdGuard browsing security web service** digunakan untuk safe browsing (proteksi malware, situs berbahaya, dsb).
 3. **Use AdGuard parental control web service** digunakan untuk mengontrol anak-anak ketika mengakses internet (proteksi situs dewasa).
 4. **Use safe search** digunakan untuk proteksi mesin pencarian dari hasil pencarian yang mengandung konten yang tidak sewajarnya.
![Gambar fitur adguard home](/img/Instalasi-Adguard-Home-untuk-Proteksi-Perangkat-pada-Home-Network/fitur-agh.png)

Ada satu fitur lagi di dalam Adguard Home, yaitu menonaktifkan layanan pada aplikasi. maksudnya adalah ketika pengguna membuka suatu aplikasi yang sudah kita *blacklist* maka permintaan pengguna ke server yang dituju tidak akan sampai.

Jika dirasa ingin menambah domains atau manual blocking untuk block domain tertentu bisa menggunakan cara berikut:
Masuk menu **Filters -> Custom Filtering Rules** lalu masukkan alamat domain yang ingin diblock.

Perlu diingat untuk konfigurasi *log* memerlukan penyimpanan yang lebih besar (bisa menggunakan penyimpanan eksternal). jika ingin melihat *log* bisa menggunakan rentang maksimal satu hari dan jika dirasa tidak membutuhkan *log* sama sekali bisa tidak dicentang pada *Enable log*. 

# Test Adguard Home
Saya ambil contoh dari website penyedia berita yaitu Tempo. Contoh ini diambil sebelum instalasi Adguard Home.
![Berita sebelum adguard home](/img/Instalasi-Adguard-Home-untuk-Proteksi-Perangkat-pada-Home-Network/berita-sebelum-agh.png)

Terlihat banyak sekali iklan yang muncul sampai memenuhi konten utama.
Setelah dilakukan instalasi dan konfigurasi Adguard Home
![Berita setelah adguard home](/img/Instalasi-Adguard-Home-untuk-Proteksi-Perangkat-pada-Home-Network/berita-sesudah-agh.png)
Terlihat bahwa iklan tidak muncul.

# Penutup
Jika Anda administrator jaringan direkomendasikan untuk melakukan proteksi jaringan mengingat bahwa kemudahan dalam mengakses internet tidak menutup kemungkinan berdampak pada keamanan internet. Adguard Home memiliki banyak fitur untuk proteksi jaringan seperti *adblocker*. Konfigurasi Adguard Home sudah mencakup di semua jaringan tanpa perlu konfigurasi tiap perangkat.