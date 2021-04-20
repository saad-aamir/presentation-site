---
title: "Explanation"
date: 2021-04-19T21:48:30+05:00
featured_image: "/images/lol4.jpg"
summary: "Building 64-bit Operating System"
draft: false
---

## Tools:
* VS Code
* Docker
* Qemu

## Introduction:
An x86 compatible Operating System which is multiboot2 compliant is made. It is written using C proramming language and assembly language for the hardware portion.

## Development:
Installed Qemu to emulate the Operating System and Docker to have a reproducable build enviroment. Then, installed nasm which is used to compile the assembly code and grub package to form the final iso file. For this purpose,create a folder by the name __buildenv__ and create a __Dockerfile__ inside it and copy the following code inside it.
{{< highlight html >}}
FROM randomdude/gcc-cross-x86_64-elf

RUN apt-get update 
RUN apt-get upgrade -y
RUN apt-get install -y nasm
RUN apt-get install -y xorriso
RUN apt-get install -y grub-pc-bin
RUN apt-get install -y grub-common

VOLUME /root/env
WORKDIR /root/env
{{< /highlight >}}

Ran the following command in the termnal to build a docker image:
{{< highlight html >}}
docker build buildenv -t myos-buildenv
{{< /highlight >}}

The image took around 10 minutes to build.

Now, opened an instance of this image which is known as a *container* by running one of the following commads based on the PC's Operating System:
{{< highlight html >}}
Windows: docker run --rm -it -v "%cd%":/root/env myos-buildenv
Linux or MacOS: docker run --rm -it -v $pwd:/root/env myos-buildenv
{{< /highlight >}}

Now, entered a virtual linux machine.

## Code:
After sucessfully entering the virtual linux machine with access to all the required tools, we started coding the Operating system.

Then, created folders in the folowing way:
{{< highlight html >}}
 >src
    > impl
      > x86_64
        > boot
           - header.asm
           - main.asm
           - main64.asm
        - print.c
      > kernel
        - main.c
    > intf
      - print.h

 >targets 
    > x86_64
       - linker.ld
       > iso
           > boot
              > grub 
                  - grub.cfg
           - .gitignore

 - MakeFile
{{< /highlight >}}

The general purpose of each of the folder is as follows:
* **src:** To hold all our source code.
* **impl:** Implmentation folder to hold all our implementation files.
* **x86_64:** To contain our x86 assembly code which will be the entry point toour operating system.
* **boot:** For integration with multiboot2.
* **iso:** This wil contain the grub configuration and the final produced iso file.
* **grub:** To hold th grub configuration file.
* **kernel:** For writing C code

The purpose of each of the files is as follows:
### header.asm
Made a __*header.asm*__ file inside the boot folder contains data to be included in our OS binaries. This data is necessary so that the bootloader can understand and locate our OS.

You can find the code [here](https://github.com/saad-aamir/os-assignment/blob/master/src/impl/x86_64/boot/header.asm).

### main.asm
Made a __*main.asm*__ file inside the boot folder which is the entry point into our OS. It also containts the necssary requirements to make a 64-bit OS.

You can find the code [here](https://github.com/saad-aamir/os-assignment/blob/master/src/impl/x86_64/boot/main.asm).

### main64.asm
Made a __*main64.asm*__ file inside the boot folder to make the long mode label.

You can find the code [here](https://github.com/saad-aamir/os-assignment/blob/master/src/impl/x86_64/boot/main64.asm).
### linker.ld
Made a __*linker.ld*__ file inside *targets/x86_64* which describes how to link our OS together.

You can find the code [here](https://github.com/saad-aamir/os-assignment/blob/master/targets/x86_64/linker.ld).

### grub.cfg
Make a __*grubcfg*__ inside *targets/x86_64/iso/boot/grub* . This creates an iso file out of our OS kernel binary.

You can find the code [here](https://github.com/saad-aamir/os-assignment/blob/master/targets/x86_64/iso/boot/grub/grub.cfg).

### .gitignore
If you're using git, make a __*.gitignore*__ file inside targets/x86_64/iso and put the following code inside:
{{< highlight html >}}
boot/kernel.bin
{{< /highlight >}}

### MakeFile
It contains the commands for bulding the Operating System. It helps to organise all the build commands and makes sure that only modified files get rebuilt.

You can find the code [here](https://github.com/saad-aamir/os-assignment/blob/master/Makefile).

### main.c 
To create the function __*kernel_main*__ which is then called in __*main64.asm*__ to display output.

You can find the code [here](https://github.com/saad-aamir/os-assignment/blob/master/src/impl/kernel/main.c).

### print.h
Created a __*print.h*__ file inside src/intf which contains the printing interface.

You can find the code [here](https://github.com/saad-aamir/os-assignment/blob/master/src/intf/print.h).

### print.c
Created a __*print.c*__ file inside src/impl/x86_64. The data inside this file will be used to keep track of the rows, colmns and colors. This file contains all the required functions which are used for printing which are called when writing code for printing.

You can find the code [here](https://github.com/saad-aamir/os-assignment/blob/master/src/impl/x86_64/print.c).

__*NOW*__, ran the following command to build for x86:
{{< highlight html >}}
make build-x86_64
{{< /highlight >}}
__Exit__ the docker container.

__*FINALLY*__ ran the following command to emulate the Operating System in Qemu:
{{< highlight html >}}
qemu-system-x86_64 -cdrom dist/x86_64/kernel.iso
{{< /highlight >}}


## Errors encountred
* "**Hardware assisted virtualization and data execution protection must be enabled in the BIOS**" when running Docker.

This error was resolved by rebooting the PC and going into bios setup to turn virualizatin seting "ON".

* {{< figure src="/images/error.jpeg" alt="" title="">}}

This error occured while emulating the image. It was **resolved** by closing the qemu window.


## OUTPUT


{{< figure src="/lol.png" alt="here" title="">}}

__*Zoomed*__,

{{< figure src="/zoom.png" alt="here" title="">}}

