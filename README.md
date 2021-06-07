# Operating Systems (CSC 355)

> Subject:  Add System Call (Final term project)
>

## Table of Content

* [Team Member Names](#Team)
* [Virtual Machine Settings](#Settings)
* [Steps of how adding the system call](#Steps)
  * [**Section 1** (Preparation)](#Section-1)
  * [**Section 2** (Creation)](#Section-2)
  * [**Section 3** (Installation)](#Section-3)
  * [**Section 4** (Result)](#Section-4)
* [**References** (Result)](#References)



## Team Member Names <a name="Team"></a>

* Hossam Mahmoud Metwally.

* Youssef Hussien Elsayed.

* Omamia Magdy Saqr.

  


## Virtual Machine Settings <a name="Settings"></a>

|       |       |
| :---- | :---: |
|  Number of cores  |  **2**  |
|  The capacity of memory  |  **4G**  |
|  The kernel version  |  **5.8.1**  |



## Steps of how adding the system call <a name="Steps "></a>

### **Section 1** (Preparation) <a name="Section-1"></a>

*In this section, we will download all necessary tools to add a basic system call to the Linux kernel and run it.*

**1.1** - Fully update operating system.

```shell
$ sudo apt update && sudo apt upgrade -y
```

**1.2** - Download and install the essential packages to compile kernels.

```shell
$ sudo apt install build-essential libncurses-dev libssl-dev libelf-dev bison flex -y
```

**1.3** - Clean up installed packages.

```shell
$ sudo apt clean && sudo apt autoremove -y
```

**1.4** - Download the source code of the latest stable version of the Linux kernel `version 5.8.1` .

```shell
$ wget -P ~/ https://cdn.kernel.org/pub/linux/kernel/v5.x/linux-5.8.1.tar.xz
```

**1.5** - Unpack the source code. 

```shell
$ tar -xvf ~/linux-5.8.1.tar.xz -C ~/
```

**1.6** - Reboot computer.



------



### **Section 2** (Creation) <a name="Section-2"></a>

*In this section, we will write a basic system call in C and integrate it into the new kernel.*

**2.1** - Change working directory to the root directory.

```shell
$ cd ~/linux-5.8.1/
```

**2.2** - Create the home directory of system call.

```shell
$ mkdir hello
```

**2.3** - Create a C file for system call.

```shell
$ nano hello/hello.c
```

Then, we will write the following code in it.

```c
#include <linux/kernel.h>
#include <linux/syscalls.h>

SYSCALL_DEFINE0(hello)
{
    printk("Hello in our project.\n\n");
    printk("Names:-\n");
    printk("1-Hossam Mahmoud Metwally.\n");
    printk("2-Yousif Hussein Alsayed.\n");
    printk("3-Omaima Magdy Saqr.\n");

    return 0;
}
```

![screen](https://github.com/Hossam44/OperatingSystems-AddSystemCall/blob/main/screens/1.PNG?raw=true)

**2.4** - Create a Makefile for system call.

```shell
$ nano hello/Makefile
```

Then, we will write the following code in it.

```makefile
obj-y := hello.o
```

![screen](https://github.com/Hossam44/OperatingSystems-AddSystemCall/blob/main/screens/2.PNG?raw=true)

**2.5** - Add the home directory of system call to the main Makefile of the kernel.

Open the Makefile.

```shell
$ nano Makefile
```

Add the home directory of system call.

```shell
kernel/ certs/ mm/ fs/ ipc/ security/ crypto/ block/ hello/
```

**2.6** - Add a function prototype for system call to the header file of system calls.

Open the header file.

```shell
$ nano include/linux/syscalls.h
```

write the code above `#endif`.

```shell
$ asmlinkage long sys_hello(void);
```

Then, save it.

![screen](https://github.com/Hossam44/OperatingSystems-AddSystemCall/blob/main/screens/3.PNG?raw=true)

**2.7** - Add system call to the kernel's system call table.

Open the table.

```shell
$ nano arch/x86/entry/syscalls/syscall_64.tbl
```

Add the following code at the end of this section.

```
440     common  hello                    sys_hello
```

Then, save it.

![screen](https://github.com/Hossam44/OperatingSystems-AddSystemCall/blob/main/screens/4.PNG?raw=true)



------



### **Section 3** (Installation) <a name="Section-3"></a>

*In this section, we will install the new kernel.*

**3.1** - Configure the kernel.

```shell
$ make menuconfig
```

Make no changes to keep it in default settings.

Then, save it.

![screen](https://github.com/Hossam44/OperatingSystems-AddSystemCall/blob/main/screens/5.PNG?raw=true)

**3.2** - Find out how many logical cores.

```shell
$ nproc
```

**3.3** - Compile the kernel's source code with 2 core.

```shell
$ make -j2
```

![screen](https://github.com/Hossam44/OperatingSystems-AddSystemCall/blob/main/screens/6.png?raw=true)

**3.4** - Prepare the installer.

```shell
$ sudo make modules_install -j2
```

**3.5** - Install the kernel.

```shell
$ sudo make install -j2
```

**3.6** - Update the bootloader of the operating system with the new kernel.

```shell
$ sudo update-grub
```

**3.7** - Reboot computer.



------



### **Section 4** (Result) <a name="Section-4"></a>

*In this section, we will write a C program to check whether system call works or not.*

**4.1** - Check the version now.

```shell
$ uname -r
```

```
5.8.1
```

**4.2** - Change working directory to home directory.

```shell
$ cd ~
```

**4.3** - Create a C file to generate a report of the success or failure of system call.

```shell
$ nano project.c
```

Write the following code in it.

```c
#include <linux/kernel.h>
#include <sys/syscall.h>
#include <stdio.h>
#include <unistd.h>
#include <string.h>
#include <errno.h>

#define __NR_hello 440

long hello_syscall(void)
{
    return syscall(__NR_hello);
}

int main(int argc, char *argv[])
{
    long activity;
    activity = hello_syscall();

    if(activity < 0)
        perror("Your system call faild.");
    else
        printf("Your system call is added\n");

    return 0;
}
```

Then, save.

**4.4** - Compile the C file you just created.

```shell
$ gcc -o project project.c
```

Then, run the C file compiled.

```shell
$ ./project
```

If it displays the following, everything is working as intended.

```
Your system call is added
```

Then, check the last line of the `dmesg` output.

```shell
$ dmesg
```

![screen](https://github.com/Hossam44/OperatingSystems-AddSystemCall/blob/main/screens/7.PNG?raw=true)

At the bottom, we see.

![screen](https://github.com/Hossam44/OperatingSystems-AddSystemCall/blob/main/screens/8.PNG?raw=true)





## References <a name="References"></a>

* https://dev.to/omergulen/how-to-add-system-call-syscall-to-the-kernel-compile-and-test-it-3e6p
* https://www.cyberciti.biz/tips/compiling-linux-kernel-26.html
* https://medium.com/anubhav-shrimal/adding-a-hello-world-system-call-to-linux-kernel-dad32875872
