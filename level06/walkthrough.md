This program takes a username and a serial number from user, the main function calls auth() and then makes a check depending on its return: If it's 0 it will open a shell if it's 1 it does nothing.
In the auth function, the user must have more than 6 characters to pass the first check.
The second condition checks the return of ptrace, that means if we try to open the program in gdb or ltrace it will detect it and return 1. Finally the program the program ciphers the username with a combination of xor, addition and mod to generate the final serial number. let's analyze the program with gdb.


We will launch the program with the username: 666666.
and password:666
We can bypass the second test by breaking right after ptrace and changing it's return value($eax) to 0 :

0x080487b5 <+109>:	call   0x80485f0 <ptrace@plt>
0x080487ba <+114>:	cmp    eax,0xffffffff

b *0x080487ba
set $eax=0

since the auth function must return 0 to open the shell, in auth+298 we see the return value($eax) set to 0

0x08048872 <+298>:	mov    eax,0x0

now we'll try looking for any jumps to this specific instruction we see one in:

0x08048866 <+286>:	cmp    eax,DWORD PTR [ebp-0x10]
0x08048869 <+289>:	je     0x8048872 <auth+298>

let's break into this line and see the compared values
x $eax
0x29a:	Cannot access memory at address 0x29a // 666
x $ebp-0x10
0xffffd678:	0x005f15db // 6231515

now we can just open the program normally and enter this serial number with 666666 as username.