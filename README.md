# note
ref_link:http://lists.kernelnewbies.org/pipermail/kernelnewbies/2013-July/008598.html

*The following approach was successfully tested in Linux kernel 3.5.7 version sources for x86 32-bit architecture.*

*Here are the steps to create our own system call in the existing kernel sources. The paths given below are relative paths from /usr/src/linux.*

####1. Generally, add the function (system call) definition in kernel/sys.c file.

/* this is the implementation of our system call */
asmlinkage long sys_helloworld(void) {
       printk(KERN_EMERG "Hello, world!\n");
return 0;
}

####2. Add the function prototype in the header file include/linux/syscalls.h

asmlinkage long sys_helloworld(void);

####3. Create an entry in system call table in the
file arch/x86/syscalls/syscall_32.tbl

350     i386    helloworld             sys_helloworld

Note: In my case already table had 349 offsets, so I added it as 350.

####4. If we want to create our own kernel images, then change the EXTRAVERSION in the main Makefile available at /usr/src/linux

EXTRAVERSION = .ownsyscall

####5. Then, build the modules from the main directory with the following.

make menuconfig  --- Just save and exit.
make modules
make modules_install
make install

####6. Now, reboot with our own image.

####7. Write a simple C application for calling the our own system call.

#include <stdio.h>

/* sys_helloworld 350 */
int main ()
{
syscall(350); /* 350 is our system calls offset number */
return 0;
}

I hope, you understand it clearly and it helps you. Based on this, you can
practice the second approach. It needs of creating our own directory and
files (C, Makefile, Kconfig) and modifications required in architecture
specific Kconfig.

Regards,
Srinivas.