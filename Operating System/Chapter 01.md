
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
## **I/O 수행 과정**
1. I/O 활동을 시작하기 위해 device driver는 device controller에 적절한 레지스터를 로드함
2. device controller는 레지스터의 콘텐츠를 확인하고 무슨 액션을 취할지 결정함
3. 컨트롤러는 디바이스 → 로컬 버퍼로 데이터 옮기기 시작
4. 데이터 다 옮긴 후 디바이스 컨트롤러는 디바이스 드라이버에게 끝났다고 알림
5. 디바이스 드라이버는 OS의 다른 부분들에게 컨트롤 넘김
    1. 이 때 데이터나 데이터 포인터를 리턴할 수 있음 (operation이 read인경우처럼)

⇒ 이 때, 컨트롤러는 디바이스 드라이버한테 operation 끝났다고 어케 알림? ⇒ via **interrupt !**

- 하드웨어 interrupt는 주로 **system bus**를 통해 일어남

**Interrupt Implementation**

- CPU는 instruction executions 끝날때마다 감지하는 **interrupt-request line**을 가짐
- 여기에 시그널이 있다? → interrupt number 읽음 → interrupt vector의 인덱스로 씀 → interrupt-handler routine으로 점프 → 인덱스랑 연관된 주소에 있는 execution 시작 → handler는 바뀌는 상태들 저장
- CPU들은 보통 interrupt request line 두 . 개가짐
    1. nonmaskable interrupt
        1. 복구 불가한 메모리 에러 같은 애들에 사용
    2. maskable interrupt
        1. 방해되면 안되는 critical instruction 돌리기 전에 CPU가 끌 수 있는 애들
- **interrupt chaining**
    - interrupt vector의 각 엘리먼트가 list of interrupt handlers를 포인팅 하는 구조

### 1.2.2 Storage Structure
- CPU can load instructions only from memory
- any programs must first be loaded into memory to run

- Main Memory commonly in DRAM(Dynamic random-access memory)
- bootstramp program: first program to run on computer => loads OS
	- volatile메모리에 올릴 수 없음
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
- CPU Register, Cache => SRAM
- main memory => DRAM => 지속적으로 refresh refresh하는동안 데이터 못쓴다

=> top four levels => constructed using semi-conductor memory
NVM - flash memory (smartphones, tablets) & long-term storage on laptops, desktops and servers(SSD)

when used for bulk data movement => DMA(Direct Memory Access=)
- only one interrupt is generated per block => tell device driver that completed

**폰노이만 구조**
- memory에서 instruction fetch해오고 그걸 instruction registor에 저장
- instruction은 디코드, 실행. 필요시 실행 결과 다시 메모리에 저장
- 포인투:
    - 메모리는 메모리 어드레스의 나열만 쭉 보고, 그게 어떻게 생성됐는지, 뭘 위한건지는 몰라도 됨
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

Most architectures adopt this approach, combining local and shared caches, where local, lower-level caches are generally smaller and faster than higher-level shared

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

There are two ways to structure a clustered system: asymmetrically and symmetrically. 
Asymmetric clustering uses a hot-standby mode, where one machine monitors another machine. If the active machine fails, the hot-standby machine takes over. 
Symmetric clustering uses all of the available computers in the cluster.

Clusters can also be used to provide high-performance computing by running applications concurrently on all computers. This requires the application to be written specifically to take advantage of the cluster.

To provide this shared access, the system must also supply access control and locking to ensure that no conflicting operations occur. This function, commonly known as a distributed lock manager (DLM), is included in some cluster technology.

Other forms of clusters include parallel clusters and clusters over a wide-area network. Parallel clusters allow multiple computers to access the same data on shared storage. Clustering technology is changing rapidly, and some clusters can support thousands of systems.

## 1.4 Operating system Operations
The bootstrap program's main function is to locate and load the operating system kernel into memory. Once loaded, the kernel provides system services.

Additional system programs, like systemd on Linux, are loaded at boot time to become daemons and run continuously alongside the kernel.

When the system finishes booting, it goes into an idle state waiting for events to happen. These events can be hardware interrupts (introduced earlier) or software-generated interrupts (traps/exceptions). Traps can be caused by errors or user program requests for system services via system calls.

### 1.4.1 Multiprogramming and Multitasking

Multiprogramming and multitasking are techniques that allow a computer system to run multiple programs at the same time. This improves CPU utilization and keeps users satisfied by ensuring the CPU is always busy.

In multiprogramming, the operating system keeps several programs in memory and switches between them whenever one program needs to wait for I/O. This is similar to how a lawyer might juggle multiple cases to avoid being idle.

Multitasking is an extension of multiprogramming where the CPU switches between processes more frequently, giving the user the illusion that multiple programs are running concurrently. This is useful because user input and output (like typing) happen much slower than computer operations.

Both multiprogramming and multitasking require memory management to keep track of multiple programs in memory and CPU scheduling to decide which process should run next. Additionally, these systems need to protect resources from being improperly used by different programs.

### 1.4.2 Dual Mode and Multimode Operation
1. user mode and kernel mode
	1. mode bit is added to hardware to indicate current mode
		1. kernel - 0/user -1
	![[스크린샷 2024-05-19 오후 3.26.15.png]]

System calls are a way for user programs to request services from the operating system. When a system call is made, the program switches to kernel mode and the operating system fulfills the request.

Hardware protection detects errors that violate modes, such as a user program trying to execute an illegal instruction. In such cases, the hardware traps to the operating system, which can terminate the program abnormally.

### 1.4.3
The operating system needs to ensure it maintains control over the CPU to prevent user programs from entering infinite loops and never relinquishing control.

One way to achieve this is through timers. Timers can be set to interrupt the computer after a specific period, giving the OS a chance to intervene. These interrupts can be fixed or variable.

Before giving control to a user program, the OS sets the timer to interrupt. If the timer triggers, control is transferred back to the OS, which can decide how to handle the situation (treat it as an error or give the program more time).

Since modifying the timer can impact the OS's control, these instructions are privileged and can only be executed in kernel mode.

## 1.5 
An operating system acts as a resource manager for various resources like CPU, memory, storage and I/O devices.

Process management is an important aspect of resource management. A process is an instance of a program in execution. It needs resources like CPU time, memory, files and I/O devices to function. The OS allocates these resources to the process when it is running.

A program itself is not a process. A program is passive whereas a process is active. A process can be single-threaded or multi-threaded. A single threaded process executes instructions sequentially. A multithreaded process has multiple threads and can execute instructions concurrently.

The OS is responsible for various activities related to process management including creating and deleting processes, scheduling them on CPUs, suspending and resuming them, providing mechanisms for synchronization and communication between processes.

Memory management is another important aspect of resource management. The OS is responsible for keeping track of which parts of memory are being used, allocating and deallocating memory space, and deciding which processes or data to move in and out of memory. Memory management techniques are covered in Chapter 9 and 10.

Another important resource is storage. The operating system provides a logical view of information storage through files. A file is a collection of related information defined by its creator. The OS manages mass storage media and devices to implement the concept of a file. Files can be organized into directories for easier use. The OS is responsible for creating and deleting files and directories, manipulating them, mapping them to storage, and backing them up. File management techniques are covered in Chapter 13, 14 and 15.

The OS also plays a role in managing secondary storage devices like hard disk drives and solid state drives. This includes mounting and unmounting devices, managing free space, allocating storage, scheduling disk access, and partitioning the storage. Efficient management of secondary storage is important because the speed of the entire computer system can be impacted by the speed of these devices. Tertiary storage devices like tape drives and optical drives are slower and cheaper than secondary storage and are used for backups and archival purposes. The OS may or may not play a role in managing tertiary storage. Techniques for managing both secondary and tertiary storage are covered in Chapter 11.

Caching is a technique to improve computer performance by keeping frequently used information in a faster storage location. There are different levels of cache in a computer system, organized as a hierarchy.

- Registers: Fastest type of storage, managed by compiler/programmer
- Cache: Faster than main memory, managed by hardware
- Main memory: Managed by OS
- Secondary storage (like hard drives): Managed by OS
- Tertiary storage (like tape drives): May or may not be managed by OS

The size and replacement policy of a cache impact its performance.

In a storage hierarchy, data can exist in multiple levels at the same time. The OS needs to ensure data consistency when multiple processes or processors access the same data. This is especially important in multi-tasking and multi-processor environments.

I/O system management hides the specifics of hardware devices from the user and the OS itself. It consists of:

- Memory management component (buffering, caching, spooling)
- General device driver interface
- Specific device drivers

An operating system needs to protect resources from unauthorized access. This is achieved through protection mechanisms that control access to files, memory segments, CPU and other resources.

Protection mechanisms also improve reliability by detecting errors at the interfaces between subsystems.

Security is another aspect that is becoming increasingly important. Security mechanisms protect the system from external and internal attacks like viruses, worms, denial-of-service attacks, identity theft and theft of service.

An operating system typically maintains a list of users with unique identifiers. Sometimes, groups are defined to allow or disallow certain access to a set of users.

Virtualization is a technology that abstracts the hardware of a single computer into multiple execution environments. This creates an illusion of multiple private computers running on the same hardware. Virtualization allows running multiple operating systems on a single machine.

## 1.8 distributed system

A distributed system is a collection of separate computers networked together to provide users with access to shared resources. This improves computation speed, functionality, data availability, and reliability.

There are two main approaches to network access in operating systems:

- Generalize network access as a form of file access, hiding the networking details.
- Make users specifically invoke network functions.

Networks are communication paths between systems and can be local (LAN), wide (WAN) or metro (MAN). They can be wired or wireless and use various media to transmit data.

A network operating system allows file sharing and communication between processes on different machines. A distributed operating system provides an illusion of a single operating system controlling the entire network.

## 1.9 Kernel Data Structures
1. List, Stacks, Queues
2. Trees
3. Hash Functions and MAps
4. Bitmaps

## 1.10 Computing Environments
Operating systems are used in various computing environments.

Traditional computing environments include:

- Typical office environment: Networked PCs with servers for file and print services. Remote access and portability are achieved through laptops and mobile devices.
    
- Home environment: Home computers with faster internet connections allowing them to serve web pages and run networks. Firewalls are used for protection.
    

In the past, computing resources were scarce and systems were either batch (processing jobs in bulk) or interactive (waiting for user input). Time-sharing systems were used to optimize resource use but today this technique is applied on various systems where multiple processes share CPU time.

### 1.10.2
Mobile computing refers to computing on mobile devices like smartphones and tablets. These devices are portable and lightweight and are increasingly offering features comparable to traditional computers. They are used for a variety of tasks including email, web browsing, music/video playing, and even high-definition video recording and editing. Mobile devices often have features like GPS for location-based services, accelerometers for detecting orientation and movement, and gyroscopes. These features are used in various applications including navigation and augmented reality. Mobile devices typically use Wi-Fi or cellular data networks to access online services. Their memory and processing power are generally less than traditional computers. The two dominant mobile operating systems are Apple iOS and Google Android.

Client-server computing is a type of distributed system where server systems respond to requests from client systems. There are two main types of server systems:

- Compute servers: These servers perform actions based on client requests and send results back. An example is a database server.
- File servers: These servers provide a file system interface for clients to create, update, read and delete files. An example is a web server delivering files to web browsers.

### 1.10.3
Mobile computing refers to computing on portable devices like smartphones and tablets. These devices offer features comparable to traditional computers including music/video playing, photo taking, and video editing. They also have unique features like GPS for navigation and accelerometers for motion detection used in augmented reality applications. Mobile devices typically use Wi-Fi or cellular data for internet access and have limitations in storage and processing power compared to traditional computers. The two dominant mobile operating systems are Apple iOS and Google Android.

Client-server computing is a network architecture where servers respond to requests from clients. There are two main types of servers:

- Compute servers: Perform actions based on client requests and send results back. An example is a database server.
- File servers: Provide a file system interface for clients to manage files. An example is a web server delivering files to web browsers.

### 1.10.4
There are four main types of distributed systems:

- Client-server: Server systems respond to requests from client systems. There are two main types of servers:
    
    - Compute servers: Perform actions based on client requests and send results back. An example is a database server.
    - File servers: Provide a file system interface for clients to manage files. An example is a web server delivering files to web browsers.
- Peer-to-peer (P2P): In this model, all nodes are considered peers and can act as either a client or a server. Peers can discover services provided by other peers and communicate directly.
    
- Cloud computing: Delivers computing, storage, and even applications as a service across a network. There are different types of cloud computing including public cloud, private cloud, hybrid cloud, software as a service (SaaS), platform as a service (PaaS), and infrastructure as a service (IaaS).

### 1.10.5

Embedded systems are very common and can be found in many devices from cars to appliances. They typically have limited functionality and user interface and focus on monitoring and controlling hardware.

There are three types of embedded systems:

- General-purpose computers running standard operating systems like Linux.
- Hardware devices with a special-purpose embedded OS.
- Hardware devices with application-specific integrated circuits (ASICs) that don't use an operating system.

Embedded systems often use real-time operating systems which have strict time requirements. These systems are used in control devices and need to respond to sensor data within a specific time frame. Examples of real-time systems include scientific experiments, medical imaging, industrial control and weapon systems.

