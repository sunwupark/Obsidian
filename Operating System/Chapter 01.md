
## Operating System

- **Software that manages a computer’s hardware**
- intermediate between computer use and computer hardware

### **CHAPTER OBJECTIVES**

- Describe the general organization of a computer system and the role of interrupts.
- Describe the components in a modern multiprocessor computer system.
- Illustrate the transition from user mode to kernel mode.
- Discuss how operating systems are used in various computing environ- ments.
- Provide examples of free and open-source operating systems.

## 1.1 What Operating Systems do

Four Componenets of Computer System

1. hardware
    1. CPU, memory, input/output devices - basic computing resources
2. operating system
3. application programs
    1. spreadsheets, compiler, web browser - which these resources are used to solve problems
4. user

Operating System - government: performs no useful function by itself.

**⇒ PROVIDES ENVIRONMENT other programs can do work!!!**

## 1.1.1 Views

- USERS VIEW
    
    - user to monopolize its resources
    - maximize the work that user is performing!
    
    ![[스크린샷 2024-05-19 오전 2.09.20.png]]
    
## Operating System is control program.
- manages execution of user programs to prevent errors and improper use of the computer.
- kernel - one program running at all times on the computer
- system programs: associated with OS but are not necessarily part of the kernel
- application programs: all the programs not assoicated with OS


## 1.2 Computer-System Organization
- [[device driver]]: understands device controller and provides the rest of OS with uniform interface to the device

	CPU and device controllers execute in parallel => competing for memory cycles.

![[스크린샷 2024-05-19 오전 2.18.31.png]]

### 1.2.1 Interrupts
- How controller informs device driver when operation has finished??
- Interruptes handled quickly => table of pointers to interrupt routines => interrupt vector
	interrupt -> save current state -> restore the state before returning => interrupt handled => saved return address is loaded to PC => resumes

"Interrupt-request line" 

![[스크린샷 2024-05-19 오전 2.40.12.png]]

### 1.2.2 Storage Structure
- CPU can load instructions only from memory
- any progrmas must first be loaded into memory to run

- Main Memory commonly in DRAM(Dynamic random-access memory)
- bootstramp program: first program to run on computer => loads OS
- EEPROM - firmware 저장(잘 수정되지 않는 곳), static programs
- basic unit of computer storage => bit
- all forms of memory provide an array of bytes

Instrucitons
- load instruction: moves a byte or word from main memory to an internal register within the CPU
- store instruction: moves the content of a register to main memory

But memory is too small to store and volatile => Secondary Storage, tertiary storage
1. HDD (hard-disk drives)
2. NVM(Non volatile memory devices)
![[스크린샷 2024-05-19 오전 2.54.29.png]]

=> top four levels => constructed using semi-conductor memory
NVM - flash memory (smartphones, tablets) & long-term storage on laptops, desktops and servers(SSD)

when used for bulk data movement => DMA(Direct Memory Access=)
- only one interrupt is generated per block => tell device driver that completed

## 1.3 Computer-System Architecture
A multiprocessor system is a computer system with multiple processors. 
These processors share the computer bus and sometimes the clock, memory, and peripheral devices. 
The main advantage of such systems is increased throughput, which means more work can be done in less time. 
However, the speed-up ratio is not linear with the number of processors because of overhead and contention for shared resources.

There are two main types of multiprocessor systems: 
symmetric multiprocessing (SMP) and multicore systems. 

![[스크린샷 2024-05-19 오전 3.06.59.png]]
SMP systems have multiple peer CPUs that can perform all tasks. 
Multicore systems have multiple cores on a single chip. 

Multicore systems are more efficient than SMP systems because on-chip communication is faster than between-chip communication. Additionally, multicore systems use less power than SMP systems.


Most archi- tectures adopt this approach, combining local and shared caches, where local, lower-level caches are generally smaller and faster than higher-level shared


![[스크린샷 2024-05-19 오전 3.10.54.png]]

DEFINITIONS OF COMPUTER SYSTEM COMPONENTS
- CPU — The hardware that executes instructions.
- Processor—AphysicalchipthatcontainsoneormoreCPUs.
- Core — The basic computation unit of the CPU.
- Multicore — Including multiple computing cores on the same CPU.
- Multiprocessor — Including multiple processors.

Non-uniform memory access (NUMA) is an alternative approach to adding more processors to a system. In NUMA systems, each CPU has its own local memory that it can access quickly. All CPUs share one physical address space and are connected by a shared system interconnect. This allows NUMA systems to scale better than traditional systems by reducing contention for the system bus.

However, there is a performance penalty when a CPU needs to access memory from another CPU. Operating systems can minimize this penalty through scheduling and memory management. **NUMA systems are becoming increasingly popular for servers and high-performance computing because they can handle a large number of processors.**

## 1.3.3 Clustered Systems

A clustered system is a group of computers working together as a single unit. They are different from multiprocessor systems because they are individual systems connected together. Clustered systems are used to provide high-availability service, which means that the service will continue to function even if one or more systems fails. This is achieved by adding redundancy to the system.

There are two ways to structure a clustered system: asymmetrically and symmetrically. Asymmetric clustering uses a hot-standby mode, where one machine monitors another machine. If the active machine fails, the hot-standby machine takes over. 
Symmetric clustering uses all of the available computers in the cluster.

Clusters can also be used to provide high-performance computing by running applications concurrently on all computers. This requires the application to be written specifically to take advantage of the cluster.

To provide this shared access, the system must also supply access control and locking to ensure that no conflicting operations occur. This function, commonly known as a distributed lock manager (DLM), is included in some cluster technology.

Other forms of clusters include parallel clusters and clusters over a wide-area network. Parallel clusters allow multiple computers to access the same data on shared storage. Clustering technology is changing rapidly, and some clusters can support thousands of systems.