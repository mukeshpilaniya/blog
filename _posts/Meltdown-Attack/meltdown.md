International Institute of Information

Technology(IIIT), Bangalore![](images.001.png)

![](images.002.png)

Meltdown Attack![](images.003.png)

Mentor Prof.Thangaraju B Professor,IIIT-Bangalore

Mukesh Kumar Pilaniya Shreyansh Jain M.Tech 1st Year M.Tech 1st Year

MT2019068 MT2019106

Contents

[1 Introduction](#_page3_x72.00_y245.21) 1

[2 Background](#_page4_x72.00_y126.38) 2

1. [Cache Side Channel and Attacks ](#_page4_x72.00_y205.76). . . . . . . . . . . . . . . . . . . . . . . . 2

[2.1.1 FLUSH+RELOAD attack .](#_page5_x72.00_y406.68) . . . . . . . . . . . . . . . . . . . . . . . 3

2. [Out-Of-Order Execution ](#_page5_x72.00_y532.25). . . . . . . . . . . . . . . . . . . . . . . . . . . . . 3
3. [Address Space Randomization .](#_page7_x72.00_y244.87) . . . . . . . . . . . . . . . . . . . . . . . . . 5

[3 Meltdown and its Components](#_page7_x72.00_y605.80) 5

1. [Transient Instruction .](#_page8_x72.00_y158.47) . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 6
1. [Attack Description ](#_page8_x72.00_y403.09). . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 6

[4 Evaluation](#_page9_x72.00_y72.00) 7

1. [Environment Setting ](#_page9_x72.00_y141.42). . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 7
1. [Code Compilation .](#_page9_x72.00_y484.81) . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 7
1. [Reading from Cache versus Main Memory ](#_page9_x72.00_y620.22). . . . . . . . . . . . . . . . . . . 7
1. [Using Cache as Side-Channel ](#_page11_x72.00_y387.26). . . . . . . . . . . . . . . . . . . . . . . . . . 9
1. [Prepration for Meltdown Attack . ](#_page13_x72.00_y258.59). . . . . . . . . . . . . . . . . . . . . . . . 11
1. [Exception Handling .](#_page15_x72.00_y141.77) . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 13
1. [Out-of-Order Execution ](#_page16_x72.00_y150.91). . . . . . . . . . . . . . . . . . . . . . . . . . . . . 14
1. [Meltdown Attack ](#_page16_x72.00_y551.27). . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 14

[5 Prevention of Meltdown Attack - KAISER Patch ](#_page17_x72.00_y595.11)15 [6 Conclusion](#_page18_x72.00_y291.66) 16

List of Figures

1  [Cache Architecture ](#_page5_x436.13_y273.55). . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 3
1  [Out-Of-Order Execution ](#_page6_x467.85_y473.80). . . . . . . . . . . . . . . . . . . . . . . . . . . . . 4
1  [Environment Setting ](#_page9_x470.67_y426.90). . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 7
1  [Program Illustrating the Timing Dierence of Probing Array . ](#_page10_x523.95_y639.84). . . . . . . . 8
1  [Access Timing of Probing Array .](#_page11_x306.00_y329.36) . . . . . . . . . . . . . . . . . . . . . . . . 9
1  [Program showing Cache is used as a Side Channel . ](#_page12_x306.00_y576.02). . . . . . . . . . . . . . 10
1  [Reading of Secret Value from Cache .](#_page13_x306.00_y200.68) . . . . . . . . . . . . . . . . . . . . . . 11
1  [Program illustrating Meltdown Attack .](#_page14_x306.00_y389.10) . . . . . . . . . . . . . . . . . . . . 12
1  [dmesg command ](#_page15_x523.95_y83.86). . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 13
1  [Exception Handling .](#_page15_x451.95_y528.16) . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 13
1  [Output of Exception Handling Program ](#_page16_x306.00_y93.00). . . . . . . . . . . . . . . . . . . . 14
1  [Out-of-Order Execution ](#_page16_x487.95_y393.29). . . . . . . . . . . . . . . . . . . . . . . . . . . . . 14
1  [Reading Secret Value from Kernel .](#_page17_x451.95_y417.57) . . . . . . . . . . . . . . . . . . . . . . . 15
1  [Output- Program Reading Secret Value from Kernel . .](#_page17_x523.95_y493.08) . . . . . . . . . . . . 15
1  [KAISER PATCH ](#_page18_x523.95_y232.47). . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 16

Abstract

Application security relies on the memory mapping in the system as well as isolation if memory unit and memory management unit is responsible for that like kernel address system space that are marked as not accessible from user space and are marked as priviliged. In this paper, we present Meltdown, Meltdown exploit vulnerabilities of out-of-order execution which allows a user program to read data stored inside the kernel-level memory that are marked as not accessible from user-level program. Such, accessing of data is not allowed by the hardware level protection mechanism implemented in Central Processing Unit(CPU), but the vulnerability exists in the design of these CPU and mostly intel CPU are aected by meltdown. Meltdown breaks all the security guarantees of a system which is not patched by KASLR.

1  Introduction

A operating system provide security to each application using memory isolation technique. Operating system provide guarantee that one application or program can not access other application data without permission of operating system(kernel).Kernel level permission bit is set by hardware level mechanism known as supervisor bit. Supervisor bit provide isolation between kernel address space and user address space. So, the aim is that this bit could only be set when entering into kernel space and it's cleared when switching to user process(mode) space. This hardware feature allows OS to map kernel address space to address space of other user process and it is an very ecient address transitions scehme.(1)(9)

In this paper, we present Meltdown, the attack itself is quite complex, therefore we break it down into quite small steps so that each step is easy to [understand.(1)(9)](#_page18_x72.00_y492.76)

Meltdown does not exploit any kind of software vulnerability i.e it does not use or break internal software system but it is a hardware attack and it works on all the major intel system. The major cause of meltdown attack is out-of-order execution[.(1)(9) ](#_page18_x72.00_y492.76)Out-of-order execution increases CPU eciency and allows CPU to execute instruction faster and, in a second half of the paper we have describe it in short. Through Out-of-Order execution we exploit cache side channel to catch data store in L3 cache. However, out-of- order attacks cache side channel and the result allows an attacker to dump whole kernel memory by reading cache memory in an out-of-order execution [manner.(1)(9)](#_page18_x72.00_y492.76)

Outline

- In this section we will describe about

{ Cache Side channel and attacks { Out-Of-Order Execution

{ Address Spaces Randomization

- Meltdown and Its Components
- Evluation
- Conclusion
2  Background

In this section we describe cache side channel, out-of-order execution and address space randomization.

1. Cache Side Channel and Attacks

Cache based attack on the processor were happening for a number of years, meltdown and spectre are famous known attacks of Cache side channel. Cache side channel attacks are enabled using the micro architecture design of the processor. They are part of hardware design and thus they are very dicult to [defeat.(9)(10)](#_page19_x72.00_y311.60)

In order to speed up memory access and address translation CPU contains the small memory known as cache memory that store frequently used data. Every instruction and every piece of data that require main memory. The CPU retrieves the instruction and data from main memory execute that instruction and store result back in main memory. So, to improve per- formance of accessing main memory a hardware level access is made to various level of cache memory. If the require instruction or data already in cache memory CPU retrieved that data from cache execute it and save back to cache, with the help of various write back policy cache data is written back to main memory eg: - write back and write [through.(1)(4)(9) ](#_page18_x72.00_y623.77)Here, the main point is cache memory is used for storing frequently access data.

The attacker can not directly read data stored in cache memory but this does not mean that there is not information leakage, the cache is much smaller than main memory and nearest to the CPU, so data retrieving from cache memory is much faster than accessing data from main memory. Cache side channel exploit this timing dierence for retrieving data. Dier- ent cache technique has been proposed in the past including Evict+Time, Prime+Probe and [Flush+Reload.(2)](#_page18_x72.00_y531.61)

The next piece of background information required to understand the CPU cache topology of modern processors. Figure [1] shows a generic topology that is common to most of modern CPU's. Modern CPU's generally contain multiple levels of cache memory. In this gure we assume that CPU is dual core and each core contains its own L1 and L2 cache memory and L3 cache is shared in between core0 and core1. We exploiting cache side channel attack in L3 cache only because that is practical to exploit and for side channel attack, we are using Flush+Reload technique because it's works on a single cache line granularity[.(1)(4)(9)](#_page18_x72.00_y623.77)

All the technique works this way: manipulate cache to known state, wait for victim activity and examine what has changed. Program virtual address map to physical address with the help of page table. L1 cache is nearest to the CPU and split between a data and an instruc- tion cache. If the data not found in L1 cache than load instruction passed to the next Cache hierarchy. This is the point where the page table come into play. Page table are used to

![](images.004.png)

Figure 1: Cache Architecture

translate virtual address into a physical address, once's we have physical address CPU can query into L2 cache and if data is not found in L2 cache then Load instruction is passed to L3 cache. L3 is inclusive shared cache between core0 and [core1.(3)(9)](#_page18_x72.00_y584.91)

2.1.1 FLUSH+RELOAD attack

Using Flush+Reload an attacker can exploit last-level shared inclusive cache i.e Level3 cache, an attacker frequently ush a targeted memory location using the clush instruction and thereby measuring the time it takes to reload the data, now the attacker can know whether data was loaded into the cache memory by another application or any current process in the mean[time.(1)(9)](#_page19_x72.00_y311.60)

2. Out-Of-Order Execution

Out-Of-Order execution allows processor to execute the instructions one after the other, the processor uses the resources not to its full extent which makes CPU performance ine- cient.Thus, to improve the eciency of CPU, there are two methods,rst by executing various sub-steps of sequential instructions at the same time(simultaneously) or secondly maybe by executing instructions simultaneously depending on the resources availability. Further im- provement within the CPU can be achieved by Out-of-Order Execution. Out-of-order instruction execution are usually achieved by executing the instruction in an various dierent [form.(4)(9)](#_page19_x72.00_y311.60)

Out-of-order execution is a method or approach that is utilized in high performance micro- processors. Out-Of-Order eciently uses instruction cycles (Instruction Cycles is a process by which a computer system pulls program instruction from the memory which can invoke pre-fetching or pre-processing, also determines what action the instruction requires or what resources it needs and carries out those actions.) and reduces costly delay due to resource unavailability. A processor will execute the instruction order of availability of knowledge or operands or resources rather than original order of the instructions in the program. Through this, the CPU will avoid being idle or free while data is retrieved for the next instruction in advance for a p[rogram.(4)(9)](#_page19_x72.00_y311.60)

Out-of-Order Execution can be regarded as A Room guarded by a Security Ocer.The attacker wants to enter the room to get some secret value but the Security Guard have 2 options either it can allow the attacker to access the data depending on the permissions or it can deny the attacker to access the room's [data.(4)(9)](#_page18_x72.00_y623.77)

![](images.005.png)

Figure 2: Out-Of-Order Execution

In gure 2 the line 3 causes an interrupt because user(attacker) wants to access the kernel data, this line leads CPU to do two things

1)The CPU raises an exception since user level program want to access kernel level data, this causes program or application to either crash if the program doesn't have exception handling mechanism.

2)Mean-while when CPU is busy in permission check the CPU doesn't want the other com- putational parts to be idle since this may degrade the performance or eciency so the CPU execute the adjacent instructions depending on the availability of data operands.

Now the user is either allowed or denied to access the data, but in both cases due to Out- of-Order Execution the adjacent instructions are executed. If the permission is granted then the adjacent instructions are successfully executed but if the permission is denied the

program or application may abruptly end(or may show appropriate message depending on the Exception Handling Mechanism) but in both cases the value from the kernel is fetched by the CPU and stored in a temporary register in Cache Memory. The Cache Memory is not ushed in either case of access permission check, that means the data is still available somewhere in the Cache. So from outside or user point of view the attacker has not accessed the data but from inside it can still access the data through Cache Memory via Timing [Dierence.(1)(4)(9)](#_page18_x72.00_y623.77)

We will show in the Evaluation(Section 5) the proof of Out-of-Order Execution that how the attacker can access the secret data.

3. Address Space Randomization

CPU's support two kind of address spaces so that processes are isolated from each other. Virtual Address Spaces can be dened as virtual addresses that are translated to phys- ical(logical) addresses through page translation tables. Virtual address space is divided into a group of pages that will be mapped to the corresponding logical or physical memory through a multi-level page translation table. The page translation tables are used to dene

the specic mapping virtual to physical address and also protection bit(like dirty bit etc) properties that are used to force privilege checks, like read access, write access, executable or not and user-accessible or not. The currently used translation table is stored in a special CPU register. On each context switch(change of process states), the OS updates this register with corresponding process translation table address so as to implement per-process virtual address spaces.Thus, each process can only refer to data that belongs to its own virtual address space. Every virtual address space is divided into two categories which are User and Kernel parts so that each process gets a user and kernel address space. The currently running application uses the user address space, while the kernel address space would only be accessed if CPU is running(active) and also in privileged mode(mode bit set to 1). This

is done by the operating system which disable the user-accessible property of the current or specic translation tables. The kernel address space does not only have memory mapped for the kernel's own use, but it must also perform operations on the user pages. As a result of this, the whole physical memory is mapped with the k[ernel.(1)(4)(9)](#_page18_x72.00_y623.77)

3  Meltdown and its Components

Meltdown Attack uses aw in most of the modern processors.These aws exists in the CPU's itself i.e it can be regrded as a Hardware Defect rather than a software bug, it allows a user- level program to read data stored inside the kernel memory. We cannot access kernel space due to the hardware protection mechanism, but a defect exists in the design of these CPU's which allows to defeat the hardware protection and thus carry out these types of attacks. Because the defect exists within the hardware,it is very dicult to fundamentally x the problem, unless we change the CPU's in our [computers.(1)(4)(9)](#_page18_x72.00_y623.77)

1. Transient Instruction

Before doing an in-depth analysis of how meltdown takes place, rst we need to understand what is Transient Instruction.

Any Instruction that executes Out-of-order in the program and has measurable side eects

is known as Transient Instruction.

Transient instructions occur all the time, because the CPU runs ahead of the current in- struction all the time to minimize the experienced latency and, thereby, to maximize the performance or eciency.These instructions introduce an exploitable side channel if their operation depend on a secret value. We specialise in addresses that are mapped within the attacker's process, i.e., the user-accessible user space addresses as well as the user-inaccessible which may kernel space addresses or other user's address space.The attacks targeting code that is executed within the context (i.e., address space) of other user processess are also pos- sible, but out of context in this work, because the physical memory (including the memory of other user processes) they are read through the kernel [space.(1)(4)(9)](#_page18_x72.00_y623.77)

2. Attack Description

Meltdown attack can be divided into 3 steps:

1.To know Secret Kernel Address Space The content of an attacker chosen memory location which is stored in kernel space address and which can not be accessed by the at- tacker, is loaded into a register.

2.Out-of-Order Instruction Execution A transient instruction which is an out-of-order instruction accesses the cache line supported by the register.

3.Using Cache as Side Channel to read the secret data The attacker uses Flush+Reload technique (Timing Dierence Attack) to work out the cache line accessed in the previous step and thus the secret stored at the specied memory location.

Since these is only applicable for single memory location if the attacker uses these technique for other locations then it can get the secret from other locations in kernel space as well which may lead to unavoidable conse[quences.(1)(2)(4)(9)](#_page18_x72.00_y623.77)

4  Evaluation

In this section we describe each step that need to done for performing meltdown attack.

1. Environment Setting

For setting up our lab environment we are using 64-bit ubuntu 16.04 LTS in oracle virtual box 6.0, setting related to hardware device is specied in given 3.

![](images.006.png)

Figure 3: Environment Setting

2. Code Compilation

While compiling source code we have to add -march=native ag. while compiling the pro- gram -march=native ag tells the compiler to produce specic code for the local machine. For, example to compile a myprogram.c we are using the following command :

gcc-march=native -o myprogram myprogram.c

3. Reading from Cache versus Main Memory

Cache memory is nearest to CPU so, rst CPU check data in cache, if data is present in cache than it will fetch directly from it and if data is not present than it will fetch from main memory. Fetching data from cache is much faster than fetching data from main memory.

gcc -march=native -o CacheTime CacheTime.c

In the Figure [4 ](#_page10_x523.95_y639.84)at line number 19, rst we have initialized an array of size 10\*PAZESIZE. For nding PAGESIZE run the following command in terminal \getconf PAGESIZE" and put your own PAGESIZE in line 8. After that we ush the array address to make sure that array indexes are not cached and in the next phase, we are accessing index 4 and 7 as shown in line number 25 and 26 so that index 4 and 7 is cached by cache. From line number 29 to 35 we are accessing the array index and measuring the timing using rdtscp time stamp.

![](images.007.png)

Figure 4: Program Illustrating the Timing Dierence of Probing Array

Figure [5 ](#_page11_x306.00_y329.36)illustrate the timing dierence where accessing the array index 3 and 7 is much faster than others.

![](images.008.png)

Figure 5: Access Timing of Probing Array

4. Using Cache as Side-Channel

The objective of this section is extracting a secret value used by the victim function. We are assuming that victim(); function at line 53 uses a secret value dene in line 14, as index to load values from an array and the secret value cannot be accessed from the outside. Our objective is to use side channel to get this secret value. The technique that we are using for retrieving secret value is Flush(line 16)+Reload(line27) Functions.

As Shown in Figure [6 ](#_page12_x306.00_y576.02)at line 14, rst we set one-byte secretValue variable equal to 105.Since for a one-byte secret value there are 256 possibilities so in line 12 we declare array of size 256\*PAGESIZE. We multiply by PAGESIZE because caching is done at a block level, not at a byte level so, if one byte is cached by cpu than adjacent byte will also cached. Since the rst array[0\*PAGESIZE] may also cached by some cache block as a default behavior of cache. Therefore, to make sure array[0\*PAGESIZE] will not cached we are accessing ar- ray[i\*PAGESIZE+DELTA], where DELTA is a constant dene in line number 10.

![](images.009.png)

Figure 6: Program showing Cache is used as a Side Channel

First Flush the entire array using ushSideChannel(); from the cache memory to make sure that array is not cached. After that we invoke the victim(); function, which access of the array element based on the value of secret, that array index value is cached by the cache memory. And the nal step is calling the reloadSideChannel(); function which reload the entire array and measure the time it takes to reload each element. So, if the array index is previously![](images.010.png) cached than it requires less CPU cycle. The output of the program illustrates in Figure [7.](#_page13_x306.00_y200.68)

The output of the program shown in g 6[ b](#_page12_x306.00_y576.02)elow:

Figure 7: Reading of Secret Value from Cache

5. Prepration for Meltdown Attack

For preparing meltdown attack we have to placed secret value in kernel space and we show that how a user-level program can access that data without going into kernel space. To store the Secret value in kernel space we are using kernel Module approach and the code is listed in [8.](#_page14_x306.00_y389.10)

![](images.011.png)

Figure 8: Program illustrating Meltdown Attack

For executing the meltdown attack rst, we need to know address of secret value so, the kernel module saves the address of secret value in kernel buer at line 41. which we will get it using `dmseg` command as shown in Figure [9. ](#_page15_x523.95_y83.86)The next thing is this secret value need to be cached so to achieve this we are creating a le /proc/secret data at line number 46. Which provide a link to communicate a user level program to kernel module. Therefore, when a user-level program read /proc/secret data le then it will invoke read proc() function at line 23. The read proc() function will load the secret value (line 25) which is cached by CPU. read proc() function will not return secret value so it does not leak secret value.

Compile the kernel module make

Install the kernel module

sudo insmod MeltdownKernel.ko Print secret value address dmesg
PAGE16
![](images.012.png)

Figure 9: dmesg command

6. Exception Handling

When user program tries to access kernel memory in Figure 10[ at ](#_page15_x451.95_y528.16)line 23 than memory access violation is triggered and segmentation fault is generated. To avoid segmentation fault, we are using SIGSEGV signal because c does not provide try/catch techniques like java. So to implement try/catch in c we are using sigsetjmp() at line 21 and siglongjmp at line 10.

![](images.013.png)

Figure 10: Exception Handling

The execution of this program is quite complex but let's understand it line by line. First,

we register a SIGSEGV signal handler in line 19 which will invoke catch segv function (line 7). once's the signal handler complete processing it let the program to continue its execution so for that we have to dene a checkpoint that we are achieve by sigsetjmp(buer,1) at line 21. sigsetjmp save the stack context in buer that it latter used by siglongjmp (line 10). siglongjmp rollback the stack context in buer and return the second argument which is 1 so the program execution is start form else part (line 29), output is illustrate in 11.
PAGE18


![](images.014.png)

Figure 11: Output of Exception Handling Program

7. Out-of-Order Execution

Meltdown is a race condition vulnerability, which involves racing between out-of-order exe- cution and access block so, for exploiting meltdown successfully we must have to win race condition. To win race condition we have to keep CPU execution busy somehow and for that we are using assembly level code.

![](images.015.png)

Figure 12: Out-of-Order Execution

The code in Figure [12 ](#_page16_x487.95_y393.29)is simply a loop over 400 times (line 63). In the next line it adds a number 0X141 (321 in decimal) to the eax register to keep rax register busy so that we can win race condition.

8. Meltdown Attack

To make attack more practical and improve eciency of attack we create a score array of size 256. The reason of creating an array of size 256 is that for one byte there is 256 possibilities. Therefore, one element for each possible secret value and we run attack multiple times as shown in Figure 13 at line 92. This step is combination of all step that are describe above, after running multiple times the highest value of score array is our answer. The output of this step is illustrated in Figure 14.
PAGE20

![](images.016.png)

Figure 13: Reading Secret Value from Kernel

![](images.017.png)

Figure 14: Output- Program Reading Secret Value from KernelThe code in MeltdownAttack can only steals a one byte secret from the kernel.

5  Prevention of Meltdown Attack - KAISER Patch

The exploitation in the memory attacks usually requires correct knowledge of addresses of specic data. So in order to carry out these attacks, Address Space Layout Random- ization (ASLR) has been introduced. To protect kernel, KASLR randomizes the osets which means the starting address from where the system is loaded and is changed every time the system is booted , making attacks harder as the attacker now require to guess the situa- tion of kernel data [structures.(1)(4)(9)](#_page18_x72.00_y623.77)[ But,](#_page19_x72.00_y311.60) with side channel, the attacker has to precisely determine the kernel address . A combination of a software bug and the knowledge of the physical addresses can lead to privileged code [execution.(1)(4)(9)](#_page18_x72.00_y623.77)

To Prevent Meltdown KAISER technique can be used more accurately or we can say that it is a counter measure to Meltdown Attack. KAISER hide the kernel space from user space using randomization technique. KAISER allow the kernel to randomize the kernel location at boot time. The Output of same program after applying KAISER patch is illustrated in Figure 15.

![](images.018.png)

Figure 15: KAISER PATCH

6  Conclusion

In this paper we presented Meltdown, a vulnerability or attack to the software system which can read kernel data or secret data from an underprivileged user-level program, since it does not depend on any software vulnerability as well as it is independent of the type of Operating System.

To prevent Meltdown KAISER can be used more accurately we can say that it is a counter- measure to Meltdown Attack, it is a kernel modication to not have the kernel mapped in the user space.This modication to protect side channel attacks breaking KASLR(Kernel Address Space Layout Randomization) but it also prevents Meltdown.

References

1. Zheng Zmy(2019) Meltdown: Reading Kernel Memory from User Space <https://blog.csdn.net/zheng_zmy/article/details/103479066>
1. Wenliang Du, Syracuse University(2018) Meltdown Attack Lab [http://www.cis.syr.edu/~wedu/seed/Labs_16.04/System/Meltdown_Attack/ Meltdown_Attack.pdf](http://www.cis.syr.edu/~wedu/seed/Labs_16.04/System/Meltdown_Attack/Meltdown_Attack.pdf)
1. Areej(2020) - Dierence between l1 l2 and l3 cache memory
1. Moritz Lipp,Michael Schwarz,Daniel Gruss - Metldown paper - <https://arxiv.org/pdf/1801.01207.pdf>
5. Jacek Galowicz - Metldown paper - <https://blog.cyberus-technology.de/posts/2018-01-03-meltdown.html>
5. Jann Horn, Project Zero - Reading privileged memory with a side-channel - [https://googleprojectzero.blogspot.com/2018/01/ reading-privileged-memory-with-side.html](https://googleprojectzero.blogspot.com/2018/01/reading-privileged-memory-with-side.html)
5. Jake Edge - Kernel Address Space Layout Randomization(KASLR) - <https://lwn.net/Articles/569635/>
5. Daniel Gruss,Clementine Maurice, Klaus Wagner, and Stefan Mangard- Flush+Flush:A Fast and Stealthy Cache Attack - <https://gruss.cc/files/flushflush.pdf>
5. Jann Horn, Project Zero - Reading privileged memory with a side-channel <https://cryptome.org/2018/01/spectre-meltdown.pdf>
5. Yinqian Zhang - Cache Side Channels: State of the Art and Research Oppor- tunities

<http://web.cse.ohio-state.edu/~zhang.834/slides/tutorial17.pdf>
PAGE23
