---
title: MeltDown Attack
publish: true
---

# International Institute of Information Technology(IIIT), Bangalore

![](RackMultipart20201215-4-102lhkx_html_8331d2c89349cba4.jpg)

# Meltdown Attack

Mentor

Prof.Thangaraju B

Professor,IIIT-Bangalore

| Mukesh Kumar Pilaniya | Shreyansh Jain |
| --- | --- |
| M.Tech 1st Year | M.Tech 1st Year |
| MT2019068 | MT2019106 |

Contents

| [1](#page4) | [Introduction](#page4) | 1 |
| --- | --- | --- |
| [2](#page5) | [Background](#page5) | 2 |
|
 | [2.1](#page5) | [Cache Side Channel and Attacks](#page5). . . . . . . . . . . . . . . . . . . . . . . . | 2 |
|
 |
 | [2.1.1 FLUSH+RELOAD attack](#page6). . . . . . . . . . . . . . . . . . . . . . . . | 3 |
|
 | [2.2](#page6) | [Out-Of-Order Execution](#page6). . . . . . . . . . . . . . . . . . . . . . . . . . . . . | 3 |
|
 | [2.3](#page8) | [Address Space Randomization](#page8). . . . . . . . . . . . . . . . . . . . . . . . . . | 5 |
| [3](#page8) | [Meltdown and its Components](#page8) | 5 |
|
 | [3.1](#page9) | [Transient Instruction](#page9). . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . | 6 |
|
 | [3.2](#page9) | [Attack Description](#page9). . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . | 6 |
| [4](#page10) | [Evaluation](#page10) | 7 |
|
 | [4.1](#page10) | [Environment Setting](#page10). . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . | 7 |
|
 | [4.2](#page10) | [Code Compilation](#page10). . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . | 7 |
|
 | [4.3](#page10) | [Reading from Cache versus Main Memory](#page10). . . . . . . . . . . . . . . . . . . | 7 |
|
 | [4.4](#page12) | [Using Cache as Side-Channel](#page12). . . . . . . . . . . . . . . . . . . . . . . . . . | 9 |
|
 | [4.5](#page14) | [Prepration for Meltdown Attack](#page14). . . . . . . . . . . . . . . . . . . . . . . . . | 11 |
|
 | [4.6](#page16) | [Exception Handling](#page16). . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . | 13 |
|
 | [4.7](#page17) | [Out-of-Order Execution](#page17). . . . . . . . . . . . . . . . . . . . . . . . . . . . . | 14 |
|
 | [4.8](#page17) | [Meltdown Attack](#page17). . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . | 14 |
| [5](#page18) | [Prevention of Meltdown Attack - KAISER Patch](#page18) | 15 |
| [6](#page19) | [Conclusion](#page19) | 16 |

List of Figures

| [1](#page6) | [Cache Architecture](#page6). . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . | 3 |
| --- | --- | --- |
| [2](#page7) | [Out-Of-Order Execution](#page7). . . . . . . . . . . . . . . . . . . . . . . . . . . . . | 4 |
| [3](#page10) | [Environment Setting](#page10). . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . | 7 |
| [4](#page11) | [Program Illustrating the Timing Di erence of Probing Array](#page11). . . . . . . . . | 8 |
| [5](#page12) | [Access Timing of Probing Array](#page12). . . . . . . . . . . . . . . . . . . . . . . . . | 9 |

- [Program showing Cache is used as a Side Channel](#page13). . . . . . . . . . . . . . . 10

[7 Reading of Secret Value from Cache](#page14). . . . . . . . . . . . . . . . . . . . . . . 11

| [8](#page15) | [Program illustrating Meltdown Attack](#page15). . . . . . . . . . . . . . . . . . . . . | 12 |
| --- | --- | --- |
| [9](#page16) | [dmesg command](#page16). . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . | 13 |
| [10](#page16) | [Exception Handling](#page16). . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . | 13 |
| [11](#page17) | [Output of Exception Handling Program](#page17). . . . . . . . . . . . . . . . . . . . | 14 |
| [12](#page17) | [Out-of-Order Execution](#page17). . . . . . . . . . . . . . . . . . . . . . . . . . . . . | 14 |

1. [Reading Secret Value from Kernel](#page18). . . . . . . . . . . . . . . . . . . . . . . . 15

1. [Output- Program Reading Secret Value from Kernel](#page18). . . . . . . . . . . . . . 15

1. [KAISERPATCH](#page19)................................. 16

Abstract

Application security relies on the memory mapping in the system as well as isolation if memory unit and memory management unit is responsible for that like kernel address system space that are marked as not accessible from user space and are marked as priviliged. In this paper, we present Meltdown, Meltdown exploit vulnerabilities of out-of-order execution which allows a user program to read data stored inside the kernel-level memory that are marked as not accessible from user-level program.

Such, accessing of data is not allowed by the hardware level protection mechanism implemented in Central Processing Unit(CPU), but the vulnerability exists in the design of these CPU and mostly intel CPU are a ected by meltdown. Meltdown breaks all the security guarantees of a system which is not patched by KASLR.

- Introduction

A operating system provide security to each application using memory isolation technique. Operating system provide guarantee that one application or program can not access other application data without permission of operating system(kernel).Kernel level permission bit is set by hardware level mechanism known as supervisor bit. Supervisor bit provide isolation between kernel address space and user address space. So, the aim is that this bit could only be set when entering into kernel space and it&#39;s cleared when switching to user process(mode) space. This hardware feature allows OS to map kernel address space to address space of other user process and it is an very e cient address transitions [scehme.(1)(9)](#page20)

In this paper, we present Meltdown, the attack itself is quite complex, therefore we break it down into quite small steps so that each step is easy to [understand.(1)(9)](#page20)

Meltdown does not exploit any kind of software vulnerability i.e it does not use or break internal software system but it is a hardware attack and it works on all the major intel system. The major cause of meltdown attack is out-of-order execution[.(1)(9)](#page20)

Out-of-order execution increases CPU e ciency and allows CPU to execute instruction faster and, in a second half of the paper we have describe it in short. Through Out-of-Order execution we exploit cache side channel to catch data store in L3 cache. However, out-of-order attacks cache side channel and the result allows an attacker to dump whole kernel memory by reading cache memory in an out-of-order execution [manner.(1)(9)](#page20)

Outline

In this section we will describe about { Cache Side channel and attacks { Out-Of-Order Execution

{ Address Spaces Randomization Meltdown and Its Components

1

Evluation Conclusion

- Background

In this section we describe cache side channel, out-of-order execution and address space randomization.

2.1 Cache Side Channel and Attacks

Cache based attack on the processor were happening for a number of years, meltdown and spectre are famous known attacks of Cache side channel. Cache side channel attacks are enabled using the micro architecture design of the processor. They are part of hardware design and thus they are very di cult to [defeat.(9)(10)](#page20)

In order to speed up memory access and address translation CPU contains the small memory known as cache memory that store frequently used data. Every instruction and every piece of data that require main memory. The CPU retrieves the instruction and data from main memory execute that instruction and store result back in main memory. So, to improve per-formance of accessing main memory a hardware level access is made to various level of cache memory. If the require instruction or data already in cache memory CPU retrieved that data from cache execute it and save back to cache, with the help of various write back policy cache data is written back to main memory eg: - write back and write [through.(1)(4)(9)](#page20) Here, the main point is cache memory is used for storing frequently access data.

The attacker can not directly read data stored in cache memory but this does not mean that there is not information leakage, the cache is much smaller than main memory and nearest to the CPU, so data retrieving from cache memory is much faster than accessing data from main memory. Cache side channel exploit this timing di erence for retrieving data. Di er-ent cache technique has been proposed in the past including Evict+Time, Prime+Probe and [Flush+Reload.(2)](#page19)

The next piece of background information required to understand the CPU cache topology of modern processors. Figure [1] shows a generic topology that is common to most of modern CPU&#39;s. Modern CPU&#39;s generally contain multiple levels of cache memory. In this gure we assume that CPU is dual core and each core contains its own L1 and L2 cache memory and L3 cache is shared in between core0 and core1. We exploiting cache side channel attack in L3 cache only because that is practical to exploit and for side channel attack, we are using Flush+Reload technique because it&#39;s works on a single cache line [granularity.(1)(4)(9)](#page20)

All the technique works this way: manipulate cache to known state, wait for victim activity and examine what has changed. Program virtual address map to physical address with the help of page table. L1 cache is nearest to the CPU and split between a data and an instruc-tion cache. If the data not found in L1 cache than load instruction passed to the next Cache hierarchy. This is the point where the page table come into play. Page table are used to

2

![](RackMultipart20201215-4-102lhkx_html_e94a3decdc860892.gif)

Figure 1: Cache Architecture

translate virtual address into a physical address, once&#39;s we have physical address CPU can query into L2 cache and if data is not found in L2 cache then Load instruction is passed to L3 cache. L3 is inclusive shared cache between core0 and [core1.(3)(9)](#page20)

2.1.1 FLUSH+RELOAD attack

Using Flush+Reload an attacker can exploit last-level shared inclusive cache i.e Level3 cache, an attacker frequently ush a targeted memory location using the cl ush instruction and thereby measuring the time it takes to reload the data, now the attacker can know whether data was loaded into the cache memory by another application or any current process in the [meantime.(1)(9)](#page20)

2.2 Out-Of-Order Execution

Out-Of-Order execution allows processor to execute the instructions one after the other,

the processor uses the resources not to its full extent which makes CPU performance ine -

cient.Thus, to improve the e ciency of CPU, there are two methods, rst by executing various

sub-steps of sequential instructions at the same time(simultaneously) or secondly maybe by

executing instructions simultaneously depending on the resources availability. Further im-

provement within the CPU can be achieved by Out-of-Order Execution. Out-of-order

instruction execution are usually achieved by executing the instruction in an various di erent

[form.(4)(9)](#page20)

Out-of-order execution is a method or approach that is utilized in high performance micro-

3

processors. Out-Of-Order e ciently uses instruction cycles (Instruction Cycles is a process by which a computer system pulls program instruction from the memory which can invoke pre-fetching or pre-processing, also determines what action the instruction requires or what resources it needs and carries out those actions.) and reduces costly delay due to resource unavailability. A processor will execute the instruction order of availability of knowledge or operands or resources rather than original order of the instructions in the program. Through this, the CPU will avoid being idle or free while data is retrieved for the next instruction in advance for a [program.(4)(9)](#page20)

Out-of-Order Execution can be regarded as A Room guarded by a Security O cer.The attacker wants to enter the room to get some secret value but the Security Guard have 2 options either it can allow the attacker to access the data depending on the permissions or it can deny the attacker to access the room&#39;s [data.(4)(9)](#page20)

![](RackMultipart20201215-4-102lhkx_html_e810b60fa21fb9c0.jpg)

Figure 2: Out-Of-Order Execution

In gure 2 the line 3 causes an interrupt because user(attacker) wants to access the kernel data, this line leads CPU to do two things

1)The CPU raises an exception since user level program want to access kernel level data, this causes program or application to either crash if the program doesn&#39;t have exception handling mechanism.

2)Mean-while when CPU is busy in permission check the CPU doesn&#39;t want the other com-putational parts to be idle since this may degrade the performance or e ciency so the CPU execute the adjacent instructions depending on the availability of data operands.

Now the user is either allowed or denied to access the data, but in both cases due to Out-of-Order Execution the adjacent instructions are executed. If the permission is granted then the adjacent instructions are successfully executed but if the permission is denied the

4

program or application may abruptly end(or may show appropriate message depending on the Exception Handling Mechanism) but in both cases the value from the kernel is fetched by the CPU and stored in a temporary register in Cache Memory. The Cache Memory is not ushed in either case of access permission check, that means the data is still available somewhere in the Cache. So from outside or user point of view the attacker has not accessed the data but from inside it can still access the data through Cache Memory via Timing Di [erence.(1)(4)(9)](#page20)

We will show in the Evaluation(Section 5) the proof of Out-of-Order Execution that how the attacker can access the secret data.

2.3 Address Space Randomization

CPU&#39;s support two kind of address spaces so that processes are isolated from each other. Virtual Address Spaces can be de ned as virtual addresses that are translated to phys-ical(logical) addresses through page translation tables. Virtual address space is divided into a group of pages that will be mapped to the corresponding logical or physical memory through a multi-level page translation table. The page translation tables are used to de ne the speci c mapping virtual to physical address and also protection bit(like dirty bit etc) properties that are used to force privilege checks, like read access, write access, executable or not and user-accessible or not. The currently used translation table is stored in a special CPU register. On each context switch(change of process states), the OS updates this register with corresponding process translation table address so as to implement per-process virtual address spaces.Thus, each process can only refer to data that belongs to its own virtual address space. Every virtual address space is divided into two categories which are User and Kernel parts so that each process gets a user and kernel address space. The currently running application uses the user address space, while the kernel address space would only be accessed if CPU is running(active) and also in privileged mode(mode bit set to 1). This is done by the operating system which disable the user-accessible property of the current or speci c translation tables. The kernel address space does not only have memory mapped for the kernel&#39;s own use, but it must also perform operations on the user pages. As a result of this, the whole physical memory is mapped with the [kernel.(1)(4)(9)](#page20)

- Meltdown and its Components

Meltdown Attack uses aw in most of the modern processors.These aws exists in the CPU&#39;s itself i.e it can be regrded as a Hardware Defect rather than a software bug, it allows a user-level program to read data stored inside the kernel memory. We cannot access kernel space

5

due to the hardware protection mechanism, but a defect exists in the design of these CPU&#39;s which allows to defeat the hardware protection and thus carry out these types of attacks. Because the defect exists within the hardware,it is very di cult to fundamentally x the problem, unless we change the CPU&#39;s in our [computers.(1)(4)(9)](#page20)

3.1 Transient Instruction

Before doing an in-depth analysis of how meltdown takes place, rst we need to understand what is Transient Instruction.

Any Instruction that executes Out-of-order in the program and has measurable side e ects is known as Transient Instruction.

Transient instructions occur all the time, because the CPU runs ahead of the current in-struction all the time to minimize the experienced latency and, thereby, to maximize the performance or e ciency.These instructions introduce an exploitable side channel if their operation depend on a secret value. We specialise in addresses that are mapped within the attacker&#39;s process, i.e., the user-accessible user space addresses as well as the user-inaccessible which may kernel space addresses or other user&#39;s address space.The attacks targeting code that is executed within the context (i.e., address space) of other user processess are also pos-sible, but out of context in this work, because the physical memory (including the memory of other user processes) they are read through the kernel [space.(1)(4)(9)](#page20)

3.2 Attack Description

Meltdown attack can be divided into 3 steps:

1.To know Secret Kernel Address Space The content of an attacker chosen memory location which is stored in kernel space address and which can not be accessed by the at-tacker, is loaded into a register.

2.Out-of-Order Instruction Execution A transient instruction which is an out-of-order instruction accesses the cache line supported by the register.

3.Using Cache as Side Channel to read the secret data The attacker uses Flush+Reload technique (Timing Di erence Attack) to work out the cache line accessed in the previous step and thus the secret stored at the speci ed memory location.

Since these is only applicable for single memory location if the attacker uses these technique for other locations then it can get the secret from other locations in kernel space as well which may lead to unavoidable [consequences.(1)(2)(4)(9)](#page20)

6

- Evaluation

In this section we describe each step that need to done for performing meltdown attack.

4.1 Environment Setting

For setting up our lab environment we are using 64-bit ubuntu 16.04 LTS in oracle virtual box 6.0, setting related to hardware device is speci ed in given [3.](#page10)

![](RackMultipart20201215-4-102lhkx_html_cf798081f3ce0fab.jpg)

Figure 3: Environment Setting

4.2 Code Compilation

While compiling source code we have to add -march=native ag. while compiling the pro-gram -march=native ag tells the compiler to produce speci c code for the local machine. For, example to compile a myprogram.c we are using the following command :

gcc-march=native -o myprogram myprogram.c

4.3 Reading from Cache versus Main Memory

Cache memory is nearest to CPU so, rst CPU check data in cache, if data is present in cache than it will fetch directly from it and if data is not present than it will fetch from main memory. Fetching data from cache is much faster than fetching data from main memory.

7

gcc -march=native -o CacheTime CacheTime.c

In the Figure [4](#page11)at line number 19, rst we have initialized an array of size 10\*PAZESIZE. For nding PAGESIZE run the following command in terminal \getconf PAGESIZE&quot; and put your own PAGESIZE in line 8. After that we ush the array address to make sure that array indexes are not cached and in the next phase, we are accessing index 4 and 7 as shown in line number 25 and 26 so that index 4 and 7 is cached by cache. From line number 29 to 35 we are accessing the array index and measuring the timing using rdtscp time stamp.

![](RackMultipart20201215-4-102lhkx_html_ebe4995c33765571.jpg)

Figure 4: Program Illustrating the Timing Di erence of Probing Array

8

Figure [5](#page12)illustrate the timing di erence where accessing the array index 3 and 7 is much faster than others.

![](RackMultipart20201215-4-102lhkx_html_475c3f6b475c3414.jpg)

Figure 5: Access Timing of Probing Array

4.4 Using Cache as Side-Channel

The objective of this section is extracting a secret value used by the victim function. We are assuming that victim(); function at line 53 uses a secret value de ne in line 14, as index to load values from an array and the secret value cannot be accessed from the outside. Our objective is to use side channel to get this secret value. The technique that we are using for retrieving secret value is Flush(line 16)+Reload(line27) Functions.

As Shown in Figure [6](#page13)at line 14, rst we set one-byte secretValue variable equal to 105.Since for a one-byte secret value there are 256 possibilities so in line 12 we declare array of size 256\*PAGESIZE. We multiply by PAGESIZE because caching is done at a block level, not at a byte level so, if one byte is cached by cpu than adjacent byte will also cached. Since the rst array[0\*PAGESIZE] may also cached by some cache block as a default behavior of cache. Therefore, to make sure array[0\*PAGESIZE] will not cached we are accessing ar-ray[i\*PAGESIZE+DELTA], where DELTA is a constant de ne in line number 10.

9

![](RackMultipart20201215-4-102lhkx_html_d739b94fdf40f8e.gif)

Figure 6: Program showing Cache is used as a Side Channel

First Flush the entire array using ushSideChannel(); from the cache memory to make sure that array is not cached. After that we invoke the victim(); function, which access of the array element based on the value of secret, that array index value is cached by the cache memory. And the nal step is calling the reloadSideChannel(); function which reload the

10

entire array and measure the time it takes to reload each element. So, if the array index is previously cached than it requires less CPU cycle. The output of the program illustrates in Figure [7.](#page14)

The output of the program shown ing [6](#page13)below:

![](RackMultipart20201215-4-102lhkx_html_5608a2d38d44d4ef.jpg)

Figure 7: Reading of Secret Value from Cache

4.5 Prepration for Meltdown Attack

For preparing meltdown attack we have to placed secret value in kernel space and we show that how a user-level program can access that data without going into kernel space. To store the Secret value in kernel space we are using kernel Module approach and the code is listed in [8.](#page15)

11

![](RackMultipart20201215-4-102lhkx_html_fca9efad5a458686.gif)

Figure 8: Program illustrating Meltdown Attack

For executing the meltdown attack rst, we need to know address of secret value so, the kernel module saves the address of secret value in kernel bu er at line 41. which we will get it using &#39;dmseg&#39; command as shown in Figure [9.](#page16)The next thing is this secret value need to be cached so to achieve this we are creating a le /proc/secret data at line number 46. Which provide a link to communicate a user level program to kernel module. Therefore, when a user-level program read /proc/secret data le then it will invoke read proc() function at line

1. The read proc() function will load the secret value (line 25) which is cached by CPU. read proc() function will not return secret value so it does not leak secret value.

Compile the kernel module make

Install the kernel module sudo insmod MeltdownKernel.ko

Print secret value address dmesg

12

![](RackMultipart20201215-4-102lhkx_html_30a33eb5da2304.gif)

Figure 9: dmesg command

4.6 Exception Handling

When user program tries to access kernel memory in Figure [10](#page16)at line 23 than memory access violation is triggered and segmentation fault is generated. To avoid segmentation fault, we are using SIGSEGV signal because c does not provide try/catch techniques like java. So to implement try/catch in c we are using sigsetjmp() at line 21 and siglongjmp at line 10.

![](RackMultipart20201215-4-102lhkx_html_467d1da6ea67c736.jpg)

Figure 10: Exception Handling

The execution of this program is quite complex but let&#39;s understand it line by line. First, we register a SIGSEGV signal handler in line 19 which will invoke catch segv function (line 7). once&#39;s the signal handler complete processing it let the program to continue its execution so for that we have to de ne a checkpoint that we are achieve by sigsetjmp(bu er,1) at line

1. sigsetjmp save the stack context in bu er that it latter used by siglongjmp (line 10). siglongjmp rollback the stack context in bu er and return the second argument which is 1 so the program execution is start form else part (line 29), output is illustrate in [11.](#page17)

13

![](RackMultipart20201215-4-102lhkx_html_a6be912a04650f3d.gif)

Figure 11: Output of Exception Handling Program

4.7 Out-of-Order Execution

Meltdown is a race condition vulnerability, which involves racing between out-of-order exe-cution and access block so, for exploiting meltdown successfully we must have to win race condition. To win race condition we have to keep CPU execution busy somehow and for that we are using assembly level code.

![](RackMultipart20201215-4-102lhkx_html_6cc07cc9c906d759.jpg)

Figure 12: Out-of-Order Execution

The code in Figure [12](#page17)is simply a loop over 400 times (line 63). In the next line it adds a number 0X141 (321 in decimal) to the eax register to keep rax register busy so that we can win race condition.

4.8 Meltdown Attack

To make attack more practical and improve e ciency of attack we create a score array of size

1. The reason of creating an array of size 256 is that for one byte there is 256 possibilities. Therefore, one element for each possible secret value and we run attack multiple times as shown in Figure 13 at line 92. This step is combination of all step that are describe above, after running multiple times the highest value of score array is our answer. The output of this step is illustrated in Figure 14.

14

![](RackMultipart20201215-4-102lhkx_html_35c2a25c4def5984.gif)

Figure 13: Reading Secret Value from Kernel

![](RackMultipart20201215-4-102lhkx_html_991b017e8c448709.jpg)

Figure 14: Output- Program Reading Secret Value from Kernel

The code in MeltdownAttack can only steals a one byte secret from the kernel.

- Prevention of Meltdown Attack - KAISER Patch

The exploitation in the memory attacks usually requires correct knowledge of addresses of speci c data. So in order to carry out these attacks, Address Space Layout Random-ization (ASLR) has been introduced. To protect kernel, KASLR randomizes the o sets which means the starting address from where the system is loaded and is changed every time

15

the system is booted , making attacks harder as the attacker now require to guess the situa-tion of kernel data [structures.(1)(4)(9)](#page20)But, with side channel, the attacker has to precisely determine the kernel address . A combination of a software bug and the knowledge of the physical addresses can lead to privileged code [execution.(1)(4)(9)](#page20)

To Prevent Meltdown KAISER technique can be used more accurately or we can say that it is a counter measure to Meltdown Attack. KAISER hide the kernel space from user space using randomization technique. KAISER allow the kernel to randomize the kernel location at boot time. The Output of same program after applying KAISER patch is illustrated in Figure 15.

![](RackMultipart20201215-4-102lhkx_html_a0039141753d2ec7.jpg)

Figure 15: KAISER PATCH

- Conclusion

In this paper we presented Meltdown, a vulnerability or attack to the software system which can read kernel data or secret data from an underprivileged user-level program, since it does not depend on any software vulnerability as well as it is independent of the type of Operating System.

To prevent Meltdown KAISER can be used more accurately we can say that it is a counter-measure to Meltdown Attack, it is a kernel modi cation to not have the kernel mapped in the user space.This modi cation to protect side channel attacks breaking KASLR(Kernel Address Space Layout Randomization) but it also prevents Meltdown.

References

1. Zheng Zmy(2019) Meltdown: Reading Kernel Memory from User Space [https://blog.csdn.net/zheng\_zmy/article/details/103479066](https://blog.csdn.net/zheng_zmy/article/details/103479066)

1. Wenliang Du, Syracuse University(2018) Meltdown Attack Lab [http://www.cis.syr.edu/](http://www.cis.syr.edu/~wedu/seed/Labs_16.04/System/Meltdown_Attack/Meltdown_Attack.pdf)[
## ~
wedu/seed/Labs\_16.04/System/Meltdown\_Attack/](http://www.cis.syr.edu/~wedu/seed/Labs_16.04/System/Meltdown_Attack/Meltdown_Attack.pdf)[Meltdown\_Attack.pdf](http://www.cis.syr.edu/~wedu/seed/Labs_16.04/System/Meltdown_Attack/Meltdown_Attack.pdf)

1. Areej(2020) - Di erence between l1 l2 and l3 cache memory

1. Moritz Lipp,Michael Schwarz,Daniel Gruss - Metldown paper - [https://arxiv.org/pdf/1801.01207.pdf](https://arxiv.org/pdf/1801.01207.pdf)

16

1. Jacek Galowicz - Metldown paper - [https://blog.cyberus-technology.de/posts/2018-01-03-meltdown.html](https://blog.cyberus-technology.de/posts/2018-01-03-meltdown.html)

1. Jann Horn, Project Zero - Reading privileged memory with a

side-channel - [https://googleprojectzero.blogspot.com/2018/01/](https://googleprojectzero.blogspot.com/2018/01/reading-privileged-memory-with-side.html)[reading-privileged-memory-with-side.html](https://googleprojectzero.blogspot.com/2018/01/reading-privileged-memory-with-side.html)

1. Jake Edge - Kernel Address Space Layout Randomization(KASLR) - [https://lwn.net/Articles/569635/](https://lwn.net/Articles/569635/)

1. Daniel Gruss,Clementine Maurice, Klaus Wagner, and Stefan Mangard-Flush+Flush:A Fast and Stealthy Cache Attack - [https://gruss.cc/files/flushflush.pdf](https://gruss.cc/files/flushflush.pdf)

1. Jann Horn, Project Zero - Reading privileged memory with a side-channel [https://cryptome.org/2018/01/spectre-meltdown.pdf](https://cryptome.org/2018/01/spectre-meltdown.pdf)

1. Yinqian Zhang - Cache Side Channels: State of the Art and Research Oppor-tunities [http://web.cse.ohio-state.edu/](http://web.cse.ohio-state.edu/~zhang.834/slides/tutorial17.pdf)[
# ~
zhang.834/slides/tutorial17.pdf](http://web.cse.ohio-state.edu/~zhang.834/slides/tutorial17.pdf)

17
