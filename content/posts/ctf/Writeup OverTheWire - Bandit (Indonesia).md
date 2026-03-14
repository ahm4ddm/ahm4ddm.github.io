---
title: "OverTheWire: Bandit"
date: 2021-01-07T22:09:39+07:00
description: "Writeup OverTheWire - Bandit (Indonesia)"
tags: [writeups, linux, practice]
categories: [security]
comments: true
---

# Daftar isi
[Bandit Level 0](#bandit-level-0)\
[Bandit Level 0 → Level 1 ](#bandit-level-0---level-1)\
[Bandit Level 1 → Level 2 ](#bandit-level-1---level-2)\
[Bandit Level 2 → Level 3 ](#bandit-level-2---level-3)\
[Bandit Level 3 → Level 4 ](#bandit-level-3---level-4)\
[Bandit Level 4 → Level 5 ](#bandit-level-4---level-5)\
[Bandit Level 5 → Level 6 ](#bandit-level-5---level-6)\
[Bandit Level 6 → Level 7 ](#bandit-level-6---level-7)\
[Bandit Level 7 → Level 8 ](#bandit-level-7---level-8)\
[Bandit Level 8 → Level 9 ](#bandit-level-8---level-9)\
[Bandit Level 9 → Level 10 ](#bandit-level-9---level-10)\
[Bandit Level 10 → Level 11 ](#bandit-level-10---level-11)\
[Bandit Level 11 → Level 12 ](#bandit-level-11---level-12)\
[Bandit Level 12 → Level 13 ](#bandit-level-12---level-13)\
[Bandit Level 13 → Level 14 ](#bandit-level-13---level-14)\
[Bandit Level 14 → Level 15 ](#bandit-level-14---level-15)\
[Bandit Level 15 → Level 16 ](#bandit-level-15---level-16)\
[Bandit Level 16 → Level 17 ](#bandit-level-16---level-17)\
[Bandit Level 17 → Level 18 ](#bandit-level-17---level-18)\
[Bandit Level 18 → Level 19 ](#bandit-level-18---level-19)\
[Bandit Level 19 → Level 20 ](#bandit-level-19---level-20)\
[Bandit Level 20 → Level 21 ](#bandit-level-20---level-21)\
[Bandit Level 21 → Level 22 ](#bandit-level-21---level-22)\
[Bandit Level 22 → Level 23 ](#bandit-level-22---level-23)\
[Bandit Level 23 → Level 24 ](#bandit-level-23---level-24)\
[Bandit Level 24 → Level 25 ](#bandit-level-24---level-25)\
[Bandit Level 25 → Level 26 ](#bandit-level-25---level-26)\
[Bandit Level 26 → Level 27 ](#bandit-level-26---level-27)\
[Bandit Level 27 → Level 28 ](#bandit-level-27---level-28)\
[Bandit Level 28 → Level 29 ](#bandit-level-28---level-29)\
[Bandit Level 29 → Level 30 ](#bandit-level-29---level-30)\
[Bandit Level 30 → Level 31 ](#bandit-level-30---level-31)\
[Bandit Level 31 → Level 32 ](#bandit-level-31---level-32)\
[Bandit Level 32 → Level 33 ](#bandit-level-32---level-33)\
[Bandit Level 33 → Level 34 ](#bandit-level-33---level-34)





# Bandit Level 0
## Level Goal
The goal of this level is for you to log into the game using SSH. The
host to which you need to connect is bandit.labs.overthewire.org, on
port 2220. The username is bandit0 and the password is bandit0. Once
logged in, go to the Level 1 page to find out how to beat Level 1.
## Commands you may need to solve this level
ssh
## Solusi
Dari level goal diatas kita harus mengkoneksikan dengan ssh dimana host\
`bandit.labs.overthewire.org` port `2220` lalu login dengan username
`bandit0` dan password `bandit0`
untuk melalukan koneksi ke ssh adalah `ssh username@host -p <[port]>`
```
$ssh bandit0@bandit.labs.overthewire.org -p 2220 
...
...
...
Enjoy your stay!
```

# Bandit Level 0 - Level 1
## Level Goal
The password for the next level is stored in a file called **readme** located in the home directory. Use this password to log into bandit1 using SSH. Whenever you find a password for a level, use SSH (on port 2220) to log into that level and continue the game.
## Commands you may need to solve this level
ls, cd, cat, file, du, find
## Solusi
Dari level goal diatas password disimpan dalam direktori home serta nama file **readme**.
Setelah berhasil mengkoneksikan, kita cek menggunakan ```pwd``` untuk melihat dimana kita berada lalu ```ls``` untuk melihat list direktori maupun file serta terakhir kita ```cat``` untuk mencetak isi file dalam kasus ini yaitu mencetak password.
```
bandit0@bandit:~$ pwd
/home/bandit0
bandit0@bandit:~$ ls
readme
bandit0@bandit:~$ cat readme
boJ9jbbUNNfktd78OOpsqOltutMc3MY1
bandit0@bandit:~$
```
### Password
boJ9jbbUNNfktd78OOpsqOltutMc3MY1


# Bandit Level 1 - Level 2
## Level Goal
The password for the next level is stored in a file called - located in the home directory

## Commands you may need to solve this level
ls, cd, cat, file, du, find

## Solusi
password tersimpan dalam file **-** di direktori home. ketika saya mencoba menjalakan dengan perintah ```file```
```
bandit1@bandit:~$ file -

/dev/stdin: very short file (no magic)
```
tidak menampilkan password. tapi yang menarik adalah file tersebut adalah file stdin.\
untuk mencetak file tersebut gunakan perintah ```./``` yang berati menjalankan suatu file di direktori itu sendiri.
```
bandit1@bandit:~$ file -

/dev/stdin: very short file (no magic)
bandit1@bandit:~$ cat ./-
CV1DtqXWVFXTvM2F0k09SHz0YwRINYA9
bandit1@bandit:~$ 
```

### Password
CV1DtqXWVFXTvM2F0k09SHz0YwRINYA9



# Bandit Level 2 - Level 3
## Level Goal
The password for the next level is stored in a file called spaces in this filename located in the home directory

## Commands you may need to solve this level
ls, cd, cat, file, du, find

## Solusi
Setelah menggunakan ```ls``` ada file space. untuk menjalankannya cukup kita beri tanda petik(') atau dobel petik(").\
 setelah itu kita cek tipe file didapatkan ascii text. langsung kita cetak file untuk mendapatkan password.
 ```
 bandit2@bandit:~$ ls
spaces in this filename
bandit2@bandit:~$ file 'spaces in this filename'
spaces in this filename: ASCII text
bandit2@bandit:~$ cat 'spaces in this filename'
UmHadQclWmgdLOKQ3YNgjWxGoRMb5luK
bandit2@bandit:~$ 
```

### Password
UmHadQclWmgdLOKQ3YNgjWxGoRMb5luK



# Bandit Level 3 - Level 4
## Level Goal
The password for the next level is stored in a hidden file in the inhere directory.

## Commands you may need to solve this level
ls, cd, cat, file, du, find

## Solusi
Setelah masuk kita gunakan perintah ```ls``` untuk list direktori atau file. lalu perintah ```cd``` untuk pindah direktori.
saat kita melihat list tidak ditampilakan adanya file maupun direktori. lalu gunakan opsi ```-l``` untuk mencetak bentuk list dan ```a``` menampilkan file atau direktori tersebunyi.
```
bandit3@bandit:~$ ls
inhere
bandit3@bandit:~$ cd inhere
bandit3@bandit:~/inhere$ ls
bandit3@bandit:~/inhere$ ls -la
total 12
drwxr-xr-x 2 root    root    4096 May  7  2020 .
drwxr-xr-x 3 root    root    4096 May  7  2020 ..
-rw-r----- 1 bandit4 bandit3   33 May  7  2020 .hidden
bandit3@bandit:~/inhere$ file .hidden
.hidden: ASCII text
bandit3@bandit:~/inhere$ cat .hidden
pIwrPrtPN36QITSp3EQaw936yaFoFgAB
bandit3@bandit:~/inhere$ 
```

### Password
pIwrPrtPN36QITSp3EQaw936yaFoFgAB



# Bandit Level 4 - Level 5
## Level Goal
The password for the next level is stored in the only human-readable file in the inhere directory. Tip: if your terminal is messed up, try the “reset” command.

## Commands you may need to solve this level
ls, cd, cat, file, du, find

## Solusi
Setelah masuk dan list file didapatkan banyak file **-file01-09**
level ini seperti level sebelumnya yaitu level 2.
untuk mendapatkan password kita menggunakan perintah ```cat ./-file0*``` tanda ```*``` yang berati semua file01 sampai file09.
```
bandit4@bandit:~/inhere$ ls -la
total 48
drwxr-xr-x 2 root    root    4096 May  7  2020 .
drwxr-xr-x 3 root    root    4096 May  7  2020 ..
-rw-r----- 1 bandit5 bandit4   33 May  7  2020 -file00
-rw-r----- 1 bandit5 bandit4   33 May  7  2020 -file01
-rw-r----- 1 bandit5 bandit4   33 May  7  2020 -file02
-rw-r----- 1 bandit5 bandit4   33 May  7  2020 -file03
-rw-r----- 1 bandit5 bandit4   33 May  7  2020 -file04
-rw-r----- 1 bandit5 bandit4   33 May  7  2020 -file05
-rw-r----- 1 bandit5 bandit4   33 May  7  2020 -file06
-rw-r----- 1 bandit5 bandit4   33 May  7  2020 -file07
-rw-r----- 1 bandit5 bandit4   33 May  7  2020 -file08
-rw-r----- 1 bandit5 bandit4   33 May  7  2020 -file09
bandit4@bandit:~/inhere$ cat ./-file0*
�/`2ғ�%��rL~5�g��� �������p,k�;��r*��	�.!��C��J	�dx,�e�)�#��5��
                                                                       ��p��V�_���ׯ�mm������h!?��r�l$�?h�9('���!y�e�#�x�O��=��ly���~��A�f����-E�{���m�����ܗMkoReBOKuIDDepwhWk7jZC0RTdopnAYKh
�T�?�i��j��îP�F�l�n��J����{��@�e�0$�in=��_b�5FA�P7sz��gN
```

### Password
koReBOKuIDDepwhWk7jZC0RTdopnAYKh



# Bandit Level 5 - Level 6
# Soal
The password for the next level is stored in a file somewhere under the inhere directory and has all of the following properties:

human-readable
1033 bytes in size
not executable

## Commands you may need to solve this level
ls, cd, cat, file, du, find

## Solusi
Ada banyak sekali direktori dan file. tidak mungkin kita cek satu persatu.
disini kita memanfaatkan peintah ```find```
pada Level Goal ada bebrapa ketentuan, kita cukup mencari berdasakan size.
setelah itu saya lihat manual page ```find``` terdapat beberapa opsi yaitu:

>-size n[cwbkMG]
              File uses less than, more than or  exactly  n  units  of  space,
              rounding up.  The following suffixes can be used:

>`b'    for  512-byte blocks (this is the default if no suffix is used)

>`c'    for bytes

>`w'    for two-byte words

>`k'    for kibibytes (KiB, units of 1024 bytes)

>`M'    for mebibytes (MiB, units of 1024 * 1024 = 1048576 bytes)

>`G'    for gibibytes (GiB,  units  of  1024  *  1024  *  1024  = 1073741824 bytes)

Karena bytes kita menggunakan **c**

```
bandit5@bandit:~$ ls
inhere
bandit5@bandit:~$ cd inhere
bandit5@bandit:~/inhere$ find -size 1033c
./maybehere07/.file2
bandit5@bandit:~/inhere$ cat ./maybehere07/.file2
DXjZPULLxYr17uwoI01bNLQbtFemEgo7
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        bandit5@bandit:~/inhere$ 
```

### Password
DXjZPULLxYr17uwoI01bNLQbtFemEgo7



# Bandit Level 6 - Level 7
## Level Goal
The password for the next level is stored somewhere on the server and has all of the following properties:

owned by user bandit7
owned by group bandit6
33 bytes in size

## Commands you may need to solve this level
ls, cd, cat, file, du, find, grep

## Solusi
Karena password disimpan di suatu tempat server kita perlu menggunakan perintah ```find``` pada level sebelumnya yaitu level 6.
```
bandit6@bandit:~$ find / -user bandit7 -group bandit6 -size 33c
find: ‘/root’: Permission denied
find: ‘/home/bandit28-git’: Permission denied
find: ‘/home/bandit30-git’: Permission denied
.
.
.
find: ‘/var/lib/polkit-1’: Permission denied
/var/lib/dpkg/info/bandit7.password
find: ‘/var/log’: Permission denied
find: ‘/var/cache/apt/archives/partial’: Permission denied
find: ‘/var/cache/ldconfig’: Permission denied
```
>/ = berati ditektori root\
>-user = namaUser\
>-group = namaGroup\

didapatkan direktori ```/var/lib/dpkg/info/bandit7.password```

lalu kita cetak didapatkan password
```
bandit6@bandit:~$ cat /var/lib/dpkg/info/bandit7.password
HKBPTKQnIay4Fw76bEy8PVxKEDQRKTzs
```

### Password
HKBPTKQnIay4Fw76bEy8PVxKEDQRKTzs



# Bandit Level 7 - Level 8
## Level Goal
The password for the next level is stored in the file data.txt next to the word millionth

## Commands you may need to solve this level
grep, sort, uniq, strings, base64, tr, tar, gzip, bzip2, xxd

## Solusi 
Setelah masuk, saya mengecek tipe file didapatkan ```data.txt: UTF-8 Unicode text```\
lalu saya cat file terdapat banyal sekali text yang mana membuat laptop tiba-tiba hang (jangan dicoba) :( \
disini kita bisa mengakali untuk mendapatkan password yaitu menggunakan perintah ```grep``` untuk mencari pola dari inputan
```
bandit7@bandit:~$ ls
data.txt
bandit7@bandit:~$ cat data.txt | grep 'millionth'
millionth	cvX2JJa4CFALtqS87jk27qwqGhBM9plV
bandit7@bandit:~$ 
```
perintah ```|``` merupakan pipe digunakan untuk lebih dari 2 perintah.
```
Syntax :

perintah1 | perintah2 | perintah3 | .... | commandN
```


### Password
cvX2JJa4CFALtqS87jk27qwqGhBM9plV



# Bandit Level 8 - Level 9
## Level Goal
The password for the next level is stored in the file data.txt and is the only line of text that occurs only once

## Commands you may need to solve this level
grep, sort, uniq, strings, base64, tr, tar, gzip, bzip2, xxd

## Solusi
setelah saya mencoba mencetak langsung menampilkan banyak sekali text yang mirip password.\
Untuk mendapatkan password yang asli kita gunakan perintah piping ```|``` pada level sebelumnya lalu kita ```sort``` dan ```uniq```
permasalahannya masih banyak text yang bertipe password.\
setelah membaca referensi manual dari ```uniq``` ada salah satu opsi ***-u*** digunakan hanya untuk mencetak baris yang unik.\
setelah dicoba password berhasil didapatkan.
```
bandit8@bandit:~$ cat data.txt | sort | uniq -u
UsvVyFSfZZWbi6wgC7dAFyFuR6jQQUhR
bandit8@bandit:~$ 
```

### Password
UsvVyFSfZZWbi6wgC7dAFyFuR6jQQUhR



# Bandit Level 9 - Level 10
## Level Goal
The password for the next level is stored in the file data.txt in one of the few human-readable strings, preceded by several ‘=’ characters.

## Commands you may need to solve this level
grep, sort, uniq, strings, base64, tr, tar, gzip, bzip2, xxd

## Solusi
Karena tipe file ***data.txt*** merupakan file data jadi untuk menampilkan format untuk bisa dibaca manusia menggunakan perintah ***strings***
Pada Level Goal diatas atas petunjuk bahwa password diawali dengan karakter '=' disini kita bisa menggunakan ***grep*** untuk mendapatkan password.

```
bandit9@bandit:~$ ls
data.txt
bandit9@bandit:~$ file data.txt
data.txt: data
bandit9@bandit:~$ strings data.txt | sort | grep '='
A=|t&E
c^ LAh=3G
=:G e
<I=zsGi
========== password
S=A.H&^
*SF=s
========== the*2i"4
&========== truKLdjsbJ5g7yyJ2X2R0o3a5HQJFuLk
Zdb=
Z)========== is
bandit9@bandit:~$ 

```

### Password
truKLdjsbJ5g7yyJ2X2R0o3a5HQJFuLk



# Bandit Level 10 - Level 11
## Level Goal
The password for the next level is stored in the file data.txt, which contains base64 encoded data

## Commands you may need to solve this level
grep, sort, uniq, strings, base64, tr, tar, gzip, bzip2, xxd

## Solusi
Password diencode base64 kita bisa melakukan decode untuk mendapatkan password
```
bandit10@bandit:~$ file data.txt
data.txt: ASCII text
bandit10@bandit:~$ strings data.txt | base64 -d
The password is IFukwKGsFW8MOq3IRFqrxE1hxTNEbUPR
bandit10@bandit:~$ 
```

### Password
IFukwKGsFW8MOq3IRFqrxE1hxTNEbUPR



# Bandit Level 11 - Level 12
## Level Goal
The password for the next level is stored in the file data.txt, where all lowercase (a-z) and uppercase (A-Z) letters have been rotated by 13 positions

## Commands you may need to solve this level
grep, sort, uniq, strings, base64, tr, tar, gzip, bzip2, xxd

## Solusi
Password terencode rot13.\
inti dari encode rot13 yaitu mengganti setiap huruf ke-13 setelahnya dalam alfabet.\
kita bisa mendecode dengan menggunakan perintah ```tr``` atau bisa memakai tools online.
```
bandit11@bandit:~$ ls
data.txt
bandit11@bandit:~$ file data.txt
data.txt: ASCII text
bandit11@bandit:~$ cat data.txt
Gur cnffjbeq vf 5Gr8L4qetPEsPk8htqjhRK8XSP6x2RHh
bandit11@bandit:~$ tr '[A-Za-z]' '[N-ZA-Mn-za-m]' < data.txt
The password is 5Te8Y4drgCRfCx8ugdwuEX8KFC6k2EUu
bandit11@bandit:~$ 
```

### Password
5Te8Y4drgCRfCx8ugdwuEX8KFC6k2EUu



# Bandit Level 12 - Level 13
## Level Goal
The password for the next level is stored in the file data.txt, which is a hexdump of a file that has been repeatedly compressed. For this level it may be useful to create a directory under /tmp in which you can work using mkdir. For example: mkdir /tmp/myname123. Then copy the datafile using cp, and rename it using mv (read the manpages!)

## Commands you may need to solve this level
grep, sort, uniq, strings, base64, tr, tar, gzip, bzip2, xxd, mkdir, cp, mv, file

## Solusi
buat direktore baru di tmp
```
bandit12@bandit:~$ mkdir /tmp/solusi
bandit12@bandit:~$ cp data.txt /tmp/solusi
bandit12@bandit:~$ cd /tmp/solusi
bandit12@bandit:/tmp/solusi$ ls
data.txt
```
Setelah itu kita cetak didapatkan header kalau data.txt merupakan file binary.\
```
bandit12@bandit:/tmp/solusi$ cat data.txt
00000000: 1f8b 0808 0650 b45e 0203 6461 7461 322e  .....P.^..data2.
00000010: 6269 6e00 013d 02c2 fd42 5a68 3931 4159  bin..=...BZh91AY
```
kita ganti data.txt menjadi data.bin menggunakan ```xxd -r``` untuk convert file menjadi binary
```
bandit12@bandit:/tmp/solusi$ xxd -r data.txt > data.bin
bandit12@bandit:/tmp/solusi$ ls
data.bin  data.txt
bandit12@bandit:/tmp/solusi$ file data.bin
data.bin: gzip compressed data, was "data2.bin", last modified: Thu May  7 18:14:30 2020, max compression, from Unix
bandit12@bandit:/tmp/solusi$ 
```
didapatkan file ***gzip compressed data*** \
kita ganti dan lakukan decompress
```
bandit12@bandit:/tmp/solusi$ mv data.bin data.gz
bandit12@bandit:/tmp/solusi$ gzip -d data.gz
bandit12@bandit:/tmp/solusi$ ls
data  data.txt
bandit12@bandit:/tmp/solusi$ file data
data: bzip2 compressed data, block size = 900k
```
Ternyata didapatkan file ***bzip2 compressed data***\
kita ganti dan lakukan decompress lagi
```
bandit12@bandit:/tmp/solusi$ mv data data.bz2
bandit12@bandit:/tmp/solusi$ bzip2 -d data.bz2
bandit12@bandit:/tmp/solusi$ ls
data  data.txt
bandit12@bandit:/tmp/solusi$ file data
data: gzip compressed data, was "data4.bin", last modified: Thu May  7 18:14:30 2020, max compression, from Unix
```
Didapatkan file ***gzip compressed data*** kita ganti dan lakukan decompress lagi
```
bandit12@bandit:/tmp/solusi$ mv data data.gz
bandit12@bandit:/tmp/solusi$ gzip -d data.gz
bandit12@bandit:/tmp/solusi$ ls
data  data.txt
bandit12@bandit:/tmp/solusi$ file data
data: POSIX tar archive (GNU)
bandit12@bandit:/tmp/solusi$ 
```
didapatkan file ***POSIX tar archive***\
lalu kita decompress lagi 
```
bandit12@bandit:/tmp/solusi$ mv data data.tar
bandit12@bandit:/tmp/solusi$ tar -x -f data.tar
```
dimana: 
>-x = ekstrak file\
>-f = file

setelah kita decompress didapatkan file ***data5.bin*** tapi setelah diidentifikasi ternyata file beripe ***tar***
kita ganti dan lakukan decompress lagi.
```
bandit12@bandit:/tmp/solusi$ ls
data5.bin  data.tar  data.txt
bandit12@bandit:/tmp/solusi$ file data5.bin
data5.bin: POSIX tar archive (GNU)
bandit12@bandit:/tmp/solusi$ mv data5.bin data5.tar
bandit12@bandit:/tmp/solusi$ tar -x -f data5.tar
bandit12@bandit:/tmp/solusi$ ls
data5.tar  data6.bin  data.tar  data.txt
bandit12@bandit:/tmp/solusi$ file data6.bin
data6.bin: bzip2 compressed data, block size = 900k
```
Setelah didecompress didapatkan file ***data6.bin*** yang mana setelah diidentifikasi merupakan file ***bzip2 compressed data***\
kita ganti lalu lakukan decompress
```
bandit12@bandit:/tmp/solusi$ mv data6.bin data6.bz2
bandit12@bandit:/tmp/solusi$ bzip2 -d data6.bz2
bandit12@bandit:/tmp/solusi$ ls
data5.tar  data6  data.tar  data.txt
bandit12@bandit:/tmp/solusi$ file data6
data6: POSIX tar archive (GNU)
```
Didapatkan file ***data6*** yang mana data tersebut ***POSIX tar archive***\
kita ganti lagi lalu lakukan decompress
```
bandit12@bandit:/tmp/solusi$ mv data6 data6.tar
bandit12@bandit:/tmp/solusi$ tar -x -f data6.tar
bandit12@bandit:/tmp/solusi$ ls
data5.tar  data6.tar  data8.bin  data.tar  data.txt
bandit12@bandit:/tmp/solusi$ file data8.bin
data8.bin: gzip compressed data, was "data9.bin", last modified: Thu May  7 18:14:30 2020, max compression, from Unix
```
Didapatkan file ***data8.bin*** setelah dicek file tersebut ***gzip compressed data***\
kita ganti lalu lakukan decompress lagi
```
bandit12@bandit:/tmp/solusi$ mv data8.bin data8.gz
bandit12@bandit:/tmp/solusi$ gzip -d data8.gz
bandit12@bandit:/tmp/solusi$ ls
data5.tar  data6.tar  data8  data.tar  data.txt
bandit12@bandit:/tmp/solusi$ file data8
data8: ASCII text
```
didapatkan file ***data8*** setelah dicek merupakan file text. setelah saya cetak menampilkan password
```
bandit12@bandit:/tmp/solusi$ cat data8
The password is 8ZjyCRiBWFYkneahHwxCv3wb2a1ORpYL
```
Saya menyimpulkan bahwa password dicompress sebanyak 8 kali jadi harap sabar dan teliti untuk level ini:)

### Password
8ZjyCRiBWFYkneahHwxCv3wb2a1ORpYL



# Bandit Level 13 - Level 14
## Level Goal
The password for the next level is stored in /etc/bandit_pass/bandit14 and can only be read by user bandit14. For this level, you don’t get the next password, but you get a private SSH key that can be used to log into the next level. Note: localhost is a hostname that refers to the machine you are working on

## Commands you may need to solve this level
ssh, telnet, nc, openssl, s_client, nmap

## Solusi
karena tidak ada password yang dibutuhkan dan diberi ***sshkey.private*** kita cukup masuk menggunakan ssh untuk mengakses bandit14.
```
bandit13@bandit:~$ ssh bandit14@localhost -i sshkey.private
```
lalu kita cetak password yang ada di ***/etc/bandit_pass/bandit14***
```
bandit14@bandit:~$ cat /etc/bandit_pass/bandit14
4wcYUJFw0k0XLShlDzztnTBHiqxU3b3e
bandit14@bandit:~$ 
```

### Password
4wcYUJFw0k0XLShlDzztnTBHiqxU3b3e



# Bandit Level 14 - Level 15
## Level Goal
The password for the next level can be retrieved by submitting the password of the current level to port 30000 on localhost.

## Commands you may need to solve this level
ssh, telnet, nc, openssl, s_client, nmap

## Solusi
Kita masuk ke bandit14 cek password lalu, kita lakukan perintah ****nc*** digunakan untuk r/w data menggunakan tcp/ip atau bisa digunakan untuk membuat koneksi dengan jaringan lainnya.
```
bandit14@bandit:~$ nc localhost 30000
4wcYUJFw0k0XLShlDzztnTBHiqxU3b3e
Correct!
BfMYroe26WYalil77FoDi9qh59eK5xNr
```
sintaks:
```
 nc <host> <port>
```

### Password
BfMYroe26WYalil77FoDi9qh59eK5xNr



# Bandit Level 15 - Level 16
## Level Goal
The password for the next level can be retrieved by submitting the password of the current level to port 30001 on localhost using SSL encryption.

Helpful note: Getting “HEARTBEATING” and “Read R BLOCK”? Use -ign_eof and read the “CONNECTED COMMANDS” section in the manpage. Next to ‘R’ and ‘Q’, the ‘B’ command also works in this version of that command…

## Commands you may need to solve this level
ssh, telnet, nc, openssl, s_client, nmap

## Solusi
Dari level goal diatas kita kita harus melakukan koneksi dengan menggunakan SSL encryption untuk mendapatkan password pada level berikutnya.\
kita bisa menggunakan ***ncat*** dan opsi ***--ssl*** \
berikut sintaks:
```
ncat --ssl <host> <port>
```
lalu kita masukkan password pada level sebelumnya.
```
bandit15@bandit:~$ ncat --ssl localhost 30001
BfMYroe26WYalil77FoDi9qh59eK5xNr
Correct!
cluFn7wTiGryunymYOu4RcffSxQluehd
```

### Password
cluFn7wTiGryunymYOu4RcffSxQluehd



# Bandit Level 16 - Level 17
## Level Goal
The credentials for the next level can be retrieved by submitting the password of the current level to a port on localhost in the range 31000 to 32000. First find out which of these ports have a server listening on them. Then find out which of those speak SSL and which don’t. There is only 1 server that will give the next credentials, the others will simply send back to you whatever you send to it.

## Commands you may need to solve this level
ssh, telnet, nc, openssl, s_client, nmap

## Solusi
kita disuruh mencari password di server menggunakan host ***localhost*** port ***31000-32000***\
kita bisa menggunakan ***nmap*** untuk mencari port yang aktif
```
bandit16@bandit:~$ nmap - A -p 31000-32000 127.0.0.1
Starting Nmap 7.40 ( https://nmap.org ) at 2021-01-05 14:01 CET
Nmap scan report for localhost (127.0.0.1)
Host is up (0.00036s latency).
.
.
.
31790/tcp open  ssl/unknown
| fingerprint-strings: 
|   FourOhFourRequest, GenericLines, GetRequest, HTTPOptions, Help, Kerberos, LDAPSearchReq, LPDString, RTSPRequest, SIPOptions, SSLSessionReq, TLSSessionReq: 
|_    Wrong! Please enter the correct current password
| ssl-cert: Subject: commonName=localhost
| Subject Alternative Name: DNS:localhost
| Not valid before: 2020-12-03T12:25:02
|_Not valid after:  2021-12-03T12:25:02
|_ssl-date: TLS randomness does not represent time
.
.
.
```
nmap menampilkan beberapa port, disini saya fokus pada port ***31790*** karena ada pesan please correct password.\
lalu saya gunakan perintah ***ncat*** lalu munculah private key
```
bandit16@bandit:~$ ncat --ssl localhost 31790
cluFn7wTiGryunymYOu4RcffSxQluehd
Correct!
-----BEGIN RSA PRIVATE KEY-----
MIIEogIBAAKCAQEAvmOkuifmMg6HL2YPIOjon6iWfbp7c3jx34YkYWqUH57SUdyJ
imZzeyGC0gtZPGujUSxiJSWI/oTqexh+cAMTSMlOJf7+BrJObArnxd9Y7YT2bRPQ
Ja6Lzb558YW3FZl87ORiO+rW4LCDCNd2lUvLE/GL2GWyuKN0K5iCd5TbtJzEkQTu
.
.
.
```
lalu saya copy ssh private key setelah itu saya buat file dan set permissions menjadi r/w
```
bandit16@bandit:mkdir /tmp/solusi17
bandit16@bandit:/tmp/solusi17$ nano bandit.key
bandit16@bandit:/tmp/solusi17$ chmod 600 bandit.key
bandit16@bandit:/tmp/solusi17$ ssh -i bandit.key bandit17@localhost
```
lalu berhasil masuk ke ***bandit17***\
setelah itu saya masuk disana ada file ***passwords.new passwords.old*** setelah dicoba tidak berhasil, lalu saya lihat pada level sebelumnya yaitu 

>The password for the next level is stored in /etc/bandit_pass/bandit14

saya langsung cetak pada folder tersebut.
```
bandit17@bandit:~$ cat /etc/bandit_pass/bandit17
xLYVMN9WE5zQ5vHacb0sZEVqbrp7nBTn
```

### Password
xLYVMN9WE5zQ5vHacb0sZEVqbrp7nBTn



# Bandit Level 17 - Level 18
## Level Goal
There are 2 files in the homedirectory: passwords.old and passwords.new. The password for the next level is in passwords.new and is the only line that has been changed between passwords.old and passwords.new

NOTE: if you have solved this level and see ‘Byebye!’ when trying to log into bandit18, this is related to the next level, bandit19

## Commands you may need to solve this level
cat, grep, ls, diff

## Solusi
Saat masuk ke bandit18 disana ada file ***passwords.new dan passwords.old***\
kita bisa melakukan perbandingan pada file tersebut menggunakan ***diff***
```
bandit17@bandit:~$ diff passwords.old passwords.new
42c42
< w0Yfolrc5bwjS4qw5mq1nnQi6mF03bii
---
> kfBf3eYk5BPBRzwjqutbbfE887SVc5Yd
```
kita coba masuk ke bandit18 menggunakan password diatas ternyata berhasil.

### Password
kfBf3eYk5BPBRzwjqutbbfE887SVc5Yd



# Bandit Level 18 - Level 19
## Level Goal
The password for the next level is stored in a file readme in the homedirectory. Unfortunately, someone has modified .bashrc to log you out when you log in with SSH.

## Commands you may need to solve this level
ssh, ls, cat

## Solusi
Setelah login ke ke ***bandit18*** langsung keluar. kita bisa menggunakan perintah dengan memanfaatkan ssh\
sintaks:
```
ssh user@host <port> <command>
```
```
$ssh bandit18@bandit.labs.overthewire.org -p 2220 whoami
bandit18@bandit.labs.overthewire.org's password: 
bandit18
```
kita coba cek list file atau direktori
```
$ssh bandit18@bandit.labs.overthewire.org -p 2220 ls -la
bandit18@bandit.labs.overthewire.org's password: 
total 24
drwxr-xr-x  2 root     root     4096 May  7  2020 .
drwxr-xr-x 41 root     root     4096 May  7  2020 ..
-rw-r--r--  1 root     root      220 May 15  2017 .bash_logout
-rw-r-----  1 bandit19 bandit18 3549 May  7  2020 .bashrc
-rw-r--r--  1 root     root      675 May 15  2017 .profile
-rw-r-----  1 bandit19 bandit18   33 May  7  2020 readme
```
langsung saja kita cetak file readme untuk mendapatkan password
```
$ssh bandit18@bandit.labs.overthewire.org -p 2220 cat readme
bandit18@bandit.labs.overthewire.org's password: 
IueksS7Ubh8G3DCwVzrTd8rAVOwq3M5x
```

### Password
IueksS7Ubh8G3DCwVzrTd8rAVOwq3M5x



# Bandit Level 19 - Level 20
## Level Goal
To gain access to the next level, you should use the setuid binary in the homedirectory. Execute it without arguments to find out how to use it. The password for this level can be found in the usual place (/etc/bandit_pass), after you have used the setuid binary.

## Solusi
Setelah dicek ada file ***bandit20-do*** dan file merupakan file ELF 
```
bandit20-do: setuid ELF 32-bit LSB executable
```
setelah menjalankan file tersebut ada petunjuk penggunaan
```
bandit19@bandit:~$ ./bandit20-do
Run a command as another user.
  Example: ./bandit20-do id
```
ternyata untuk mendapatkan password kita harus menjalankan file tersebut.
atau biasanya disebut ***setuid*** dimana pengguna diizinkan menjalankan file executable dengan file sistem hak akses.\
langsung saja lakukan cetak password sesudah kita menjalankan file tersebut
```
bandit19@bandit:~$ ./bandit20-do cat /etc/bandit_pass/bandit20
GbKksEFF4yrVs6il55v6gwY5aVje5f0j
```

### Password
GbKksEFF4yrVs6il55v6gwY5aVje5f0j



# Bandit Level 20 - Level 21
## Level Goal
There is a setuid binary in the homedirectory that does the following: it makes a connection to localhost on the port you specify as a commandline argument. It then reads a line of text from the connection and compares it to the password in the previous level (bandit20). If the password is correct, it will transmit the password for the next level (bandit21).

NOTE: Try connecting to your own network daemon to see if it works as you think

## Commands you may need to solve this level
ssh, nc, cat, bash, screen, tmux, Unix ‘job control’ (bg, fg, jobs, &, CTRL-Z, …)

## Solusi
Pada level ini localhost dijadikan connect dan listen.\
kita buat 2 terminal\
Terminal 1
```
bandit20@bandit:~$ nc -lvp 1337
listening on [any] 1337 ...
```
Terminal 2
```
bandit20@bandit:~$ ./suconnect 1337
```
lalu masukkan password level sebelumnya di terminal 1
```
bandit20@bandit:~$ nc -lvp 1337
listening on [any] 1337 ...
connect to [127.0.0.1] from localhost [127.0.0.1] 44134
GbKksEFF4yrVs6il55v6gwY5aVje5f0j
gE269g2h3mw3pwgrj0Ha9Uoqen1c9DGr
```
didapatkan password.


### Password
gE269g2h3mw3pwgrj0Ha9Uoqen1c9DGr



# Bandit Level 21 - Level 22
## Level Goal
A program is running automatically at regular intervals from cron, the time-based job scheduler. Look in /etc/cron.d/ for the configuration and see what command is being executed.

## Commands you may need to solve this level
cron, crontab, crontab(5) (use “man 5 crontab” to access this)

## Solusi
masuk ke direktori 
```
bandit21@bandit:~$ cd /etc/cron.d/
bandit21@bandit:/etc/cron.d$ ls
cronjob_bandit15_root  cronjob_bandit22  cronjob_bandit24
cronjob_bandit17_root  cronjob_bandit23  cronjob_bandit25_root
```
kita cek file didapatkan 
```
bandit21@bandit:/etc/cron.d$ file cronjob_bandit22
cronjob_bandit22: ASCII text
```
lalu kita cetak file didapatkan
```
bandit21@bandit:/etc/cron.d$ cat cronjob_bandit22
@reboot bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
* * * * * bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
```
lalu kita coba lihat isi file tersebut
```
bandit21@bandit:/etc/cron.d$ cat /usr/bin/cronjob_bandit22.sh
#!/bin/bash
chmod 644 /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
cat /etc/bandit_pass/bandit22 > /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
```
kita cetak yang ada di direktori ***tmp*** didapatkan password
```
bandit21@bandit:/etc/cron.d$ cat /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
Yk7owGAcWjwMVRwrTesJEwB7WVOiILLI
```

### Password
Yk7owGAcWjwMVRwrTesJEwB7WVOiILLI



# Bandit Level 22 - Level 23
## Level Goal
A program is running automatically at regular intervals from cron, the time-based job scheduler. Look in /etc/cron.d/ for the configuration and see what command is being executed.

NOTE: Looking at shell scripts written by other people is a very useful skill. The script for this level is intentionally made easy to read. If you are having problems understanding what it does, try executing it to see the debug information it prints.

## Commands you may need to solve this level
cron, crontab, crontab(5) (use “man 5 crontab” to access this)

## Solusi
Hampir sama seperti level sebelumnya kita telusuri satu persatu
```
bandit22@bandit:~$ cd /etc/cron.d
bandit22@bandit:/etc/cron.d$ ls
cronjob_bandit15_root  cronjob_bandit22  cronjob_bandit24
cronjob_bandit17_root  cronjob_bandit23  cronjob_bandit25_root
bandit22@bandit:/etc/cron.d$ file cronjob_bandit23
cronjob_bandit23: ASCII text
bandit22@bandit:/etc/cron.d$ cat cronjob_bandit23
@reboot bandit23 /usr/bin/cronjob_bandit23.sh  &> /dev/null
* * * * * bandit23 /usr/bin/cronjob_bandit23.sh  &> /dev/null
```
Setelah itu kita cetak file didapatkan 
```
bandit22@bandit:/etc/cron.d$ cat /usr/bin/cronjob_bandit23.sh
#!/bin/bash

myname=$(whoami)
mytarget=$(echo I am user $myname | md5sum | cut -d ' ' -f 1)

echo "Copying passwordfile /etc/bandit_pass/$myname to /tmp/$mytarget"

cat /etc/bandit_pass/$myname > /tmp/$mytarget
```
setelah itu kita cek siapa kita 
```
bandit22@bandit:/usr/bin$ whoami
bandit22
```
setelah kita jalankan didapatkan
```
bandit22@bandit:/usr/bin$ ./cronjob_bandit23.sh
Copying passwordfile /etc/bandit_pass/bandit22 to /tmp/8169b67bd894ddbb4412f91573b38db3
```
yang mana kita adalah masuk sebagi ***bandit22***
untuk itu kita salin sedikit code seperti
```
bandit22@bandit:/usr/bin$ bandit22@bandit:/usr/bin$ echo I am user bandit23 | md5sum | cut -d ' ' -f 1
8ca319486bfbbc3663ea0fbe81326349
```
password terletak di direktori ***tmp/8caxxx***\
```
bandit22@bandit:/usr/bin$ cat /tmp/8ca319486bfbbc3663ea0fbe81326349
jc1udXuA1tiHqjIsL8yaapX5XIAI6i0n
```

### Password
jc1udXuA1tiHqjIsL8yaapX5XIAI6i0n



# Bandit Level 23 - Level 24
## Level Goal
A program is running automatically at regular intervals from cron, the time-based job scheduler. Look in /etc/cron.d/ for the configuration and see what command is being executed.

NOTE: This level requires you to create your own first shell-script. This is a very big step and you should be proud of yourself when you beat this level!

NOTE 2: Keep in mind that your shell script is removed once executed, so you may want to keep a copy around…

## Commands you may need to solve this level
cron, crontab, crontab(5) (use “man 5 crontab” to access this)

## Solusi
kita telusuri seperti level sebelumnya didapatkan isi shell script
```
bandit23@bandit:/etc/cron.d$ cat /usr/bin/cronjob_bandit24.sh
#!/bin/bash

myname=$(whoami)

cd /var/spool/$myname
echo "Executing and deleting all scripts in /var/spool/$myname:"
for i in * .*;
do
    if [ "$i" != "." -a "$i" != ".." ];
    then
        echo "Handling $i"
        owner="$(stat --format "%U" ./$i)"
        if [ "${owner}" = "bandit23" ]; then
            timeout -s 9 60 ./$i
        fi
        rm -f ./$i
    fi
done
```
intinya shell tersebut mengekskusi dan menghapus script yang ada di direktori ***/var/spool/bandit24*** \
untuk itu kita buat direktori baru di ***tmp*** dan membuat shell script di ***/var/spool/bandit24***\
yang mana untuk copy password pada ***/etc/bandit_pass/bandit24*** ke direktori ***tmp*** \
kita buat direktori dan atur permissions menjadi r/w
```
bandit23@bandit:/var/spool/bandit24$ mkdir /tmp/sol24
bandit23@bandit:/var/spool/bandit24$ chmod 777 /tmp/sol24
```
script seperti ini
```
bandit23@bandit:/var/spool/bandit24$ touch ban24.sh
bandit23@bandit:/var/spool/bandit24$ cat ban24.sh
#!/bin/bash
cat /etc/bandit_pass/bandit24 > /tmp/sol24/hasil.txt
bandit23@bandit:/var/spool/bandit24$ chmod +x cat.sh
```

kalo kita coba listing direktori default tidak bisa, harus secara eksplisit
```
bandit23@bandit:/tmp$ ls
ls: cannot open directory '.': Permission denied
```
kalo kita eksplisit didapatkan
```
bandit23@bandit:/var/spool/bandit24$ ls /tmp/sol24
hasil.txt
```
langsung kita cetak didapatkan password
```
bandit23@bandit:/var/spool/bandit24$ cat /tmp/sol24/hasil.txt
UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ
```

### Password
UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ



# Bandit Level 24 - Level 25
## Level Goal
A daemon is listening on port 30002 and will give you the password for bandit25 if given the password for bandit24 and a secret numeric 4-digit pincode. There is no way to retrieve the pincode except by going through all of the 10000 combinations, called brute-forcing.

## Solusi
password didapatkan ketika kita berhasil memasukkan pin yang valid. permasalahan kita tidak tau pin berapa yang valid.\
kita bisa lakukan bruteforce untuk level ini\
berikut script
```
bandit24@bandit:/tmp/bandi25$ cat ban25.sh
#!/bin/bash
for i in {0000..9999}
do
    echo "UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ $i"
done
```
lalu kita simpan menjadi ***txt file***
```
bandit24@bandit:/tmp/bandi25$ ./ban25.sh > bf.txt
bandit24@bandit:/tmp/bandi25$ ls
ban25.sh  bf.txt
```
lalu kita lakukan pipe 
```
bandit24@bandit:/tmp/solu24$ cat bf.txt | nc localhost 30002
I am the pincode checker for user bandit25. Please enter the password for user bandit24 and the secret pincode on a single line, separated by a space.
Wrong! Please enter the correct pincode. Try again.
Wrong! Please enter the correct pincode. Try again.
.
.
.
Wrong! Please enter the correct pincode. Try again.
Correct!
The password of user bandit25 is uNG9O58gUE7snukf3bvZ0rxhtnjzSGzG

Exiting.
```
didapatkan password untuk masuk ke bandit25

### Password
uNG9O58gUE7snukf3bvZ0rxhtnjzSGzG



# Bandit Level 25 - Level 26
## Level Goal
Logging in to bandit26 from bandit25 should be fairly easy… The shell for user bandit26 is not /bin/bash, but something else. Find out what it is, how it works and how to break out of it.

## Commands you may need to solve this level
ssh, cat, more, vi, ls, id, pwd

## Solusi
diberikan file ***bandit26.sshkey***\
langsung kita koneksian tiba tiba langsung keluar 
```
bandit25@bandit:~$ ssh -i bandit26.sshkey bandit26@localhost
Connection to localhost closed.
```
kita lihat apa yang terjadi
```
bandit25@bandit:/etc$ cat /etc/passwd | grep 'bandit26'
bandit26:x:11026:11026:bandit level 26:/home/bandit26:/usr/bin/showtext
```
kita cetak untuk informasi detail
```
bandit25@bandit:/etc$ cat /usr/bin/showtext
#!/bin/sh

export TERM=linux

more ~/text.txt
exit 0
```
kita resize pada terminal paling kecil pada saat login ke bandit26 lalu tekan ***v*** lalu tekan ***:***
```
e /etc/bandit_pass/bandit26
```
dimana ***e*** digunakan untuk edit

didapatkan password

### Password
5czgV9L3Xx8JPOyRbXh6lQbmIOWvPT6Z



# Bandit Level 26 - Level 27
## Level Goal
Good job getting a shell! Now hurry and grab the password for bandit27!

## Commands you may need to solve this level
ls

## Solusi 
login pada ***bandit26*** dengan resize terminal 
tekan ***v*** lalu ***:***
```
set shell=/bin/bash
```
lalu tekan ***:*** lagi
```
:shell
bandit26@bandit:~$ 
```
coba kita cat pada ***text.txt***
```
bandit26@bandit:~$ cat text.txt
  _                     _ _ _   ___   __  
 | |                   | (_) | |__ \ / /  
 | |__   __ _ _ __   __| |_| |_   ) / /_  
 | '_ \ / _` | '_ \ / _` | | __| / / '_ \ 
 | |_) | (_| | | | | (_| | | |_ / /| (_) |
 |_.__/ \__,_|_| |_|\__,_|_|\__|____\___/ 
bandit26@bandit:~$ 
```
lalu kita cek file pada ***bandit27-do*** ternyata seperti level sebelumnya (setuid). 
```
bandit26@bandit:~$ ./bandit27-do
Run a command as another user.
  Example: ./bandit27-do id
```
langsung kita cat dengan menjalanakan file tersebut, didapatkan password
```
bandit26@bandit:~$ ./bandit27-do cat /etc/bandit_pass/bandit27
3ba3118a22e93127a4ed485be72ef5ea
```

### Password
3ba3118a22e93127a4ed485be72ef5ea



# Bandit Level 27 - Level 28
## Level Goal
There is a git repository at ssh://bandit27-git@localhost/home/bandit27-git/repo. The password for the user bandit27-git is the same as for the user bandit27.

Clone the repository and find the password for the next level.

## Commands you may need to solve this level
git

## Solusi
Pada level ini kita dituntut untuk bisa menggunakan git\
langsung saja, buat direktori di ***tmp*** lalu lakukan ***git clone*** untuk password memakai password level sebelumnya
```
bandit27@bandit:/tmp/bandi27$ git clone ssh://bandit27-git@localhost/home/bandit27-git/repo
Cloning into 'repo'...
Could not create directory '/home/bandit27/.ssh'.
.
.
```
kita pindah ke direktori dan list direktori didapatkan
```
bandit27@bandit:/tmp/bandi27$ ls
repo
bandit27@bandit:/tmp/bandi27$ cd repo
bandit27@bandit:/tmp/bandi27/repo$ ls
README
``` 
langsung kita cetak file tersebut didapatkan password
```
bandit27@bandit:/tmp/bandi27/repo$ cat README
The password to the next level is: 0ef186ac70e04ea33b4c1853d2526fa2
```

### Password
0ef186ac70e04ea33b4c1853d2526fa2



# Bandit Level 28 - Level 29
## Level Goal
There is a git repository at ssh://bandit28-git@localhost/home/bandit28-git/repo. The password for the user bandit28-git is the same as for the user bandit28.

Clone the repository and find the password for the next level.

## Commands you may need to solve this level
git

## Solusi
Hampir mirip seperti level sebelumnya, tapi pada level ini password tidak ditampilkan 
```
bandit28@bandit:/tmp/bandi28/repo$ cat README.md
# Bandit Notes
Some notes for level29 of bandit.

## credentials

- username: bandit29
- password: xxxxxxxxxx
```
kita bisa cek history dengan perintah ***git log***
```
bandit28@bandit:/tmp/bandi28/repo$ git log
commit edd935d60906b33f0619605abd1689808ccdd5ee
Author: Morla Porla <morla@overthewire.org>
Date:   Thu May 7 20:14:49 2020 +0200

    fix info leak

commit c086d11a00c0648d095d04c089786efef5e01264
Author: Morla Porla <morla@overthewire.org>
Date:   Thu May 7 20:14:49 2020 +0200

    add missing data

commit de2ebe2d5fd1598cd547f4d56247e053be3fdc38
Author: Ben Dover <noone@overthewire.org>
Date:   Thu May 7 20:14:49 2020 +0200

    initial commit of README.md
```
ada beberapa log, disini menurut saya yang mencurigakan adalah ***add missing data***\
langsung saja kita lihat isi commit tersebut dengan perintah 
```
git show <commit>
```
didapatkan password
```
bandit28@bandit:/tmp/bandi28/repo$ git show c086d11a00c0648d095d04c089786efef5e01264
commit c086d11a00c0648d095d04c089786efef5e01264
Author: Morla Porla <morla@overthewire.org>
Date:   Thu May 7 20:14:49 2020 +0200

    add missing data

diff --git a/README.md b/README.md
index 7ba2d2f..3f7cee8 100644
--- a/README.md
+++ b/README.md
@@ -4,5 +4,5 @@ Some notes for level29 of bandit.
 ## credentials
 
 - username: bandit29
-- password: <TBD>
+- password: bbc96594b4e001778eee9975372716b2
```

### Password
bbc96594b4e001778eee9975372716b2



# Bandit Level 29 - Level 30
## Level Goal
There is a git repository at ssh://bandit29-git@localhost/home/bandit29-git/repo. The password for the user bandit29-git is the same as for the user bandit29.

Clone the repository and find the password for the next level.

## Commands you may need to solve this level
git

## Solusi
Sama seperti level sebelumnya, tapi permasalahannya adalah tidak ada password pada commit.\
kita cek pada semua branch dengan opsi ***-a*** untuk menampilkan semua branch
```
bandit29@bandit:/tmp/bandi29/repo$ git branch -a
* master
  remotes/origin/HEAD -> origin/master
  remotes/origin/dev
  remotes/origin/master
  remotes/origin/sploits-dev
```
lalu coba kita ganti branch dengan menggunakan perintah ***checkout***
```
bandit29@bandit:/tmp/bandi29/repo$ git checkout remotes/origin/dev
.
.
.
```
lalu kita lihat semua log 
```
bandit29@bandit:/tmp/bandi29/repo$ git log
commit bc833286fca18a3948aec989f7025e23ffc16c07
Author: Morla Porla <morla@overthewire.org>
Date:   Thu May 7 20:14:52 2020 +0200

    add data needed for development

commit 8e6c203f885bd4cd77602f8b9a9ea479929ffa57
Author: Ben Dover <noone@overthewire.org>
Date:   Thu May 7 20:14:51 2020 +0200

    add gif2ascii

commit 208f463b5b3992906eabf23c562eda3277fea912
Author: Ben Dover <noone@overthewire.org>
Date:   Thu May 7 20:14:51 2020 +0200

    fix username

commit 18a6fd6d5ef7f0874bbdda2fa0d77b3b81fd63f7
Author: Ben Dover <noone@overthewire.org>
Date:   Thu May 7 20:14:51 2020 +0200

    initial commit of README.md
```
disini yang cukup menarik menurut saya yaitu ***add data needed for development*** kita coba lihat isi dari commit tersebut, maka didapatkan password
```
bandit29@bandit:/tmp/bandi29/repo$ git show bc833286fca18a3948aec989f7025e23ffc16c07
commit bc833286fca18a3948aec989f7025e23ffc16c07
Author: Morla Porla <morla@overthewire.org>
Date:   Thu May 7 20:14:52 2020 +0200

    add data needed for development

diff --git a/README.md b/README.md
index 1af21d3..39b87a8 100644
--- a/README.md
+++ b/README.md
@@ -4,5 +4,5 @@ Some notes for bandit30 of bandit.
 ## credentials
 
 - username: bandit30
-- password: <no passwords in production!>
+- password: 5b90576bedb2cc04c86a9e924ce42faf
```

### Password
5b90576bedb2cc04c86a9e924ce42faf



# Bandit Level 30 - Level 31
## Level Goal
There is a git repository at ssh://bandit29-git@localhost/home/bandit29-git/repo. The password for the user bandit29-git is the same as for the user bandit29.

Clone the repository and find the password for the next level.

## Commands you may need to solve this level
git

## Solusi
Hampir sama pada level sebelumnya, tapi permasalahan tidak ada password pada semua branch.
kita bisa menggunakan ***git-show-ref*** untuk list referensi pada lokal repo
```
andit30@bandit:/tmp/bandi30/repo$ git show-ref
3aefa229469b7ba1cc08203e5d8fa299354c496b refs/heads/master
3aefa229469b7ba1cc08203e5d8fa299354c496b refs/remotes/origin/HEAD
3aefa229469b7ba1cc08203e5d8fa299354c496b refs/remotes/origin/master
f17132340e8ee6c159e0a4a6bc6f80e1da3b1aea refs/tags/secret
```
ada beberapa commit, kita cetak yang bawah sendiri, karena itu mencurigakan
```
bandit30@bandit:/tmp/bandi30/repo$ git show f17132340e8ee6c159e0a4a6bc6f80e1da3b1aea
47e603bb428404d265f59c42920d81e5
```
dan itu merupakan password


### Password
47e603bb428404d265f59c42920d81e5



# Bandit Level 31 - Level 32
## Level Goal
There is a git repository at ssh://bandit31-git@localhost/home/bandit31-git/repo. The password for the user bandit31-git is the same as for the user bandit31.

Clone the repository and find the password for the next level.

### Commands you may need to solve this level
git

## Solusi
setelah kita clone repo, kita coba cetak ***README.md*** didapatkan
```
bandit31@bandit:/tmp/bandi31/repo$ cat README.md
This time your task is to push a file to the remote repository.

Details:
    File name: key.txt
    Content: 'May I come in?'
    Branch: master
```
untuk mendapatkan password, kita buat file ***key.txt*** isinya yaitu ***May I come in?*** pada branch ***master***
```
bandit31@bandit:/tmp/bandi31/repo$ nano key.txt
bandit31@bandit:/tmp/bandi31/repo$ git add -f 'key.txt'
bandit31@bandit:/tmp/bandi31/repo$ git commit -m "update key.txt"
bandit31@bandit:/tmp/bandi31/repo$ git push
.
.
.
Total 6 (delta 1), reused 0 (delta 0)
remote: ### Attempting to validate files... ####
remote: 
remote: .oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.
remote: 
remote: Well done! Here is the password for the next level:
remote: 56a9bf19c63d650ce78e6ec0354ee45e
remote: 
.
.
.
```
maka didapatkan password.

### Password
56a9bf19c63d650ce78e6ec0354ee45e



# Bandit Level 32 - Level 33
After all this git stuff its time for another escape. Good luck!

### Commands you may need to solve this level
sh, man

## Solusi
Setelah login dengan ***bandit32*** didapatkan tampilan 
```
WELCOME TO THE UPPERCASE SHELL
>> id
sh: 1: ID: not found
>> sh
sh: 1: SH: not found
```
saya lakukan apapun tidak bisa :(\
lalu melihat pada literatur di internet bahwa untuk escape dari itu kita bisa menggunakan ***$0***  yang mana itu merupakan bash special parameters. [$0 ketika dijabarkan menjadi nama dari sebuah shell atau shell script](https://bash.cyberciti.biz/guide/$0) jadi kita bisa escape dari tampilan UPPERCASE SHELL tadi.\
default dari ***$0*** apabila kita tidak set maka itu menujuk ke shell.
```
$ echo $0
sh
```
langsung saja kita cetak password ***bandit33***
```
$ cat /etc/bandit_pass/bandit33
c9c3199ddf4121b10cf581a98d51caee
```

### Password
c9c3199ddf4121b10cf581a98d51caee



# Bandit Level 33 - Level 34
***At this moment, level 34 does not exist yet.***

Setelah saya masuk dengan ***bandit33*** didapatkan sebagai berikut:
```
bandit33@bandit:~$ cat README.txt
Congratulations on solving the last level of this game!

At this moment, there are no more levels to play in this game. However, we are constantly working
on new levels and will most likely expand this game with more levels soon.
Keep an eye out for an announcement on our usual communication channels!
In the meantime, you could play some of our other wargames.

If you have an idea for an awesome new level, please let us know!
```
>Selamat teman-teman yang sudah sampai sini. semangat terus.

# Catatan
Jika ada yang tidak cocok bisa dilihat [disini](https://github.com/ahm4ddm/OverTheWire/tree/main/Bandit) 
karena disana saya jelaskan satu persatu.\
Terima kasih