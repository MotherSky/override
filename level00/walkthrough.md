(gdb) disassemble main

0x080484de <+74>:	call   0x80483d0 <__isoc99_scanf@plt>
**0x080484e7 <+83>:	cmp    eax,0x149c**
0x080484ec <+88>:	jne    0x804850d <main+121>
0x080484ee <+90>:	mov    DWORD PTR [esp],0x8048639
0x080484f5 <+97>:	call   0x8048390 <puts@plt>
0x080484fa <+102>:	mov    DWORD PTR [esp],0x8048649
0x08048501 <+109>:	call   0x80483a0 <system@plt>

level00@OverRide:~$ ./level00
…
Password:5276

Authenticated!

$ cat /home/users/level01/.pass
uSq2ehEGT6c9S24zbshexZQBXUGrncxn5sD5QfGL

Basic !