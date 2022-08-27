(gdb) disassemble main

0x080488c1 <+103>:	call   0x8048530 <__isoc99_scanf@plt> //with %d
0x080488ca <+112>:	mov    DWORD PTR [esp+0x4],0x1337d00d
0x080488d2 <+120>:	mov    DWORD PTR [esp],eax // entred password
0x080488d5 <+123>:	call   0x8048747 <test>

the program ask for a password then subtract it from 0x1337d00d, if the result is between 0x1 and 0x9 or 0x10(16) and 0x15(21), 
it will be used as XOR input in decrypt function to reverse a static string, if the result equals "Congratulations!", /bin/sh will be executed

We found the wanted diff at 18.

level03@OverRide:~$ ./level03
Password:322424827
$ ls
ls: cannot open directory .: Permission denied
$ cat /home/users/level04/.pass
kgv3tkEb9h2mLkRsPkXRfc2mHbjMxQzvb2FrgKkf
$
