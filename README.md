# CHERI exercise

This is a small exercise to get started with CHERI (on RISC-V, QEMU emulation). 

## Recommended setup

While QEMU with CHERI-RISC-V should run on most Linux/Unix/Mac platforms, we recommend using Ubuntu 18.04 - if needed you can do this using a VM (for example from https://www.osboxes.org/ubuntu/#ubuntu-1804-vbox). Note that the tools take a while to build (several hours depending on CPU etc), so plan in some time to wait for the compilation to finish.

## Resources

The following resources by the CHERI team from Cambridge are useful:

 * The getting started guide, including installation instructions for the emulator etc: https://ctsrd-cheri.github.io/cheri-exercises/cover/index.html 
 * The `cheribuild` tool: https://github.com/CTSRD-CHERI/cheribuild.git
 * How to copy files in/out of QEMU to the host is documented here: https://github.com/CTSRD-CHERI/cheri-exercises/pull/26
   Essentially, you can simply use `mount_smbfs -I 10.0.2.4 -N //10.0.2.4/source_root /mnt` in the QEMU guest, which will mount the CHERI base directory of the host on `/mnt`.

Also, if you ever need to exit QEMU: press `Ctrl-a` then release and press `x`   

## Task

We will use a (slightly modified) exercise from https://github.com/CTSRD-CHERI/cheri-exercises/

 * Fork this repository here (*not* the CHERI exercise one) - we expect you to add your solutions in this README.md where it says *INSERT SOLUTION HERE*. Please make sure you do reasonable commits and commit messages. You can also use other features of Github e.g. issues.
 
 * Assuming that you have installed CHERI-RISC-V in `~/cheri`, make sure your forked repo is cloned to `~/cheri/riscv-exercise`
 
 * Compile `buffer-overflow.c` to a RISC-V binary `buffer-overflow-hybrid` in hybrid capability mode (`riscv64-hybrid`). You can use the `ccc` script from `task/tools` (see the exercise docs for details) for that. What is the full commandline for compilation? 
 
---
### Solution 1

Assuming that the `SYSROOT` environment variable is set for the hybrid mode, the `ccc` script can be run to produce the RISC-V binary. The `SYSROOT` can be set as follows:

```bash
SYSROOT=~/cheri/output/sdk/sysroot-riscv64-hybrid; export SYSROOT
```
The `ccc` script can be run from the task directory `~/cheri/riscv-exercise/task` with the following command:

```bash
./tools/ccc riscv64-hybrid ~/cheri/riscv-exercise/task/buffer-overflow.c -o buffer-overflow-hybrid
```

Note that the command also includes the required output file name of the resulting RISC-V binary. The script then runs the full command line for compilation:

```bash
Running: /home/osboxes/cheri/output/sdk/bin/clang -target riscv64-unknown-freebsd -march=rv64gcxcheri -mabi=lp64d -mno-relax --sysroot=/home/osboxes/cheri/output/sdk/sysroot-riscv64-hybrid -g -O2 -fuse-ld=lld -Wall -Wcheri /home/osboxes/cheri/riscv-exercise/task/buffer-overflow.c -o buffer-overflow-hybrid
```

---
 
 * There is a security flaw in `buffer-overflow.c`. Briefly explain what the flaw is: 
 
---
### Solution 2

From inspection of the code, there are no boundary checks on the second input argument (the one after the binary file's name), potentially causing a buffer overflow security flaw when it is copied into the buffer.

Two local variables are defined which are allocated memory on the stack; a character, and a character array `buffer` of 16 bytes. The second input argument from the command line is copied into the buffer as a string. C language strings are null terminated so the allocated buffer space can only store a maximum of 15 characters. The `strcpy` function however does not perform any checks on the length of the string, meaning that strings with more than 15 characters will be written into  memory locations outside the buffer. If the number of characters is 16, the 16th chracter will be written into the memory allocated for the null character (In this program it is overwritten with a 0 - null character). If the number of characters is 17 or more, they will be written into adjacent memory locations outside the allocated buffer size. For 64 bit alignment, the `char c` could be stored 8 bytes/8 characters away from the buffer, meaning that the character c could be overwritten when the input string length is 24 characters long.

---
 
 * Start CHERI-RISC-V in QEMU, copy `buffer-overflow-hybrid` to the QEMU guest, and run it with a commandline argument that triggers the mentioned security flaw to overwrite the variable `c` with an attacker-controlled value. Give all the commands you have to run (assuming CHERI is in `~/cheri` and cheribuild in `~/cheribuild`):
 
  ```
  INSERT SOLUTION HERE
  ```
  
 * Now, compile the same program in pure capability mode (`riscv64-purecap`) to `buffer-overflow-purecap`. What happens when you run this program in QEMU with the same input that triggered the flaw in `buffer-overflow-hybrid`? Explain why this happens!

 ```
 INSERT SOLUTION HERE
 ```
