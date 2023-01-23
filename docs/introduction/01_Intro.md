# 1. Introduction to Virtualization

## 1.1. What is Virtualization?

>In computing, virtualization or virtualisation (sometimes abbreviated v12n, a numeronym) is the act of creating a virtual (rather than actual) version of something at the same abstraction level, including virtual computer hardware platforms, storage devices, and computer network resources.

In simple terms Virtualization in computing often refers to the abstraction of some physical component into a logical objects.

By virtualizing an object, you can obtain some greater measure of utility from the resource the object provides. For example,virtual LANs (local area networks), or VLANs, provide greater network per-
formance and improved manageability by being separated from the physical hardware. Likewise, storage area networks (SANs) provide greater flexibility,improved availability, and more efficient use of storage resources by abstracting the physical devices into logical objects that can be quickly and easily manipulated. Our focus, however, will be on the virtualization of entire computers.

Virtualization began in the 1960s, as a method of logically dividing the system resources provided by mainframe computers between different applications. Both MIT & IBM started working on Virtualization in 1960. An early and successful example is IBM CP/CMS. The control program CP provided each user with a simulated stand-alone System/360 computer. Since then, the meaning of the term has broadened

### 1.1.1 Approaches to Virtualization 

```move```
As far as the fundamental virtualization technologies are concerned, there are three major virtualization approaches (Kolyshkin, 2006):

- emulation - operating systems that support the underlying platform are being emulated, either by emulating the processor capabilities, or by passing code to the physical CPU on the host system. The emulated environments are usually of low performance compared to other approaches.

- paravirtualization - Para-virtualization on the other hand is run on a hypervisor - also known as a monitor - which represents a thin layer between the host and the guest operating system. While the performance of para-virtualization is generally better than that of emulation, it has the disadvantage that it requires modifications to the guest operating systems in order for them to operate properly. 

- operating system-level virtualization - virtualization enables kernel virtualization and therefore it is possible to run multiple isolated environments in parallel on a single host operating system. 


## 1.2. Computing resources

Computing resources can be broadly categorized into the following:

- Primary memory
- Persistent storage
- CPU
- Network
- I/O devices

We can have a virtulized computing resources as mentioned in the following table

| Physical resource         | Virtualization     | Remarks |
|--------------|-----------|------------|
| CPU | vCPU      | Virtualized CPU entities        |
| Memory | Memory  |  |
| Persistent Storage | Storage virtualization  | SAN/NAS  |
| Networks | Network virtualizations  |  NFVi/SDNs |

However, in this course, we are much more interested in the full-system virtualization

## 1.3. Need for Virtualization

Virtualization was born because of the identified issue of low utilization of resources. When we virtualize an environment we can have better efficiency. Moreover, Virtual machines can be easily migrated to other physical machines, which increases the flexibility of managing server infrastructure.

This approach can potentially reduce investment in server computers and reduce energy consumption, a key issue in large server centers.

The need for distributed applications also motivates developers of virtualization solutions to create and destroy virtual machines with little overhead. 

Another benefit is that in providing convenient access to several different operating system environments on a single computer, virtualization can be used to provide multiple operating system types on one physical architecture.

## 1.4. Hardware virtualization

According to Popek and Goldberg, a VMM needs to exhibit three properties in order to correctly satisfy their definition:

1. Fidelity - The environment it creates for the VM is essentially identical to the original (hardware) physical machine
2. Isolation or Safety - The VMM must have complete control of the system resources
3. Performance - There should be little or no difference in performance between the VM and a physical equivalent

Because most VMMs have the first two properties, VMMs that also meet the final criterion are considered efficient VMMs.

## 1.5. History

Virtualization technology starts from the IBM370 architecture, and its VM operating system can provide several complete virtual machines for different programs running on the same computer.

- 1960 - CP-40 was intended to implement full virtualization
- 1965 - IBM begins work on CP-67, a re-implementation of CP-40 for the S/360-67.
- 1977 - Initial commercial release of VAX/VMS, later renamed OpenVMS.
- 1985 - Announcement of the Intel 80286-based AT&T 6300+ with Simultask, a virtual machine monitor developed by Locus Computing Corporation in collaboration with AT&T
- 1997 - First version of Virtual PC for Macintosh platform was released in June 1997 by Connectix
- 1998 -  VMware filed for a patent on their techniques, which was granted as U.S. Patent 6,397,242
- 1999 - VMware introduced the first x86 virtualization product for the Intel IA-32 architecture, known as VMware Virtual Platform, 
- 2000 - FreeBSD 4.0 is released, including initial implementation of FreeBSD jails.
- 2001 - VMware created the first x86 server virtualization product
- 2003 - First release of first open-source x86 hypervisor, Xen  
- 2005 - VMware releases VMware Player, a free player for virtual machines, to the masses.
- 2007 - Open source KVM released which is integrated with Linux kernel and provides virtualization on only Linux system, it needs hardware support,  innoTek released VirtualBox Open Source Edition (OSE), the first professional PC virtualization solution released as open source under the GNU General Public License (GPL). It includes some code from the QEMU project.
- 2008 -  Sun Microsystems announced that it had entered into a stock purchase agreement to acquire innotek, makers of VirtualBox.
- 2013 - Docker, Inc. releases Docker, a series of platform as a service (PaaS) products that use OS-level virtualization. 

### 2.1 Virtualization technologies at different levels of abstraction

Before introducing various virtualization concepts, we will introduce two important terms in virtualization. In virtualization, physical resources usually have an attribute called host (Host), while virtualized resources usually have an attribute called client (Guest).

In a computer system, it can be divided into: hardware layer, operating system layer, function library layer, and application program layer from the bottom layer to the top layer. When a layer is virtualized, the interface between this layer and the upper layer does not occur. Changes, but only the implementation of this layer. From the perspective of a Guest using virtual resources, virtualization can occur at any of the above four layers. It should be noted that when a certain layer of the Guest is virtualized, there is no requirement on which layer the Host implements it. This is where confusion often arises.

 

#### 2.1.1 Virtualization on the hardware abstraction layer

Virtualization on the hardware abstraction layer refers to the realization of virtual machines through the virtual hardware abstraction layer, and presents the same or similar hardware abstraction layer as the physical hardware for the guest operating system, also known as instruction set level virtualization. Virtualization granularity is minimal.

The virtualization technology implemented at this layer can virtualize the entire computer system, that is, one physical computer system can be virtualized into one or more virtual computer systems, so it can also be called system-level virtualization. Each virtual computer system (abbreviated as a virtual machine) has its own virtual hardware (such as CPU, memory, and devices) to provide an independent virtual machine execution environment. The operating systems in each virtual machine can be completely different, and their execution environments are completely independent. Since what the guest operating system sees is the hardware abstraction layer, the guest operating system behaves no differently than it would on a physical platform.

 

#### 2.1.2 Virtualization on the operating system layer

Virtualization at the operating system layer means that the kernel of the operating system can provide multiple isolated user mode instances. These user-mode instances (often referred to as containers) are like a real computer to its users, with their own independent file system, network, system settings, and library functions.

Since this is virtualization actively provided by the operating system kernel, virtualization at the operating system layer is usually very efficient, and its virtualization resources and performance overhead are very small, and no special support from hardware is required. But it's relatively less flexible, and the OS in each container usually has to be the same one. In addition, although virtualization on the operating system layer provides relatively strong isolation between user mode instances, its granularity is relatively coarse.

 

#### 2.1.3 Virtualization on the library function layer

The operating system usually provides a set of services to applications through application-level library functions, such as file operation services and time operation services. These library functions can hide some details inside the operating system, making application programming easier. Different operating system library functions have different service interfaces, for example, the service interface of Linux is different from that of Windows. Virtualization at the library function layer is to virtualize the service interface of the application-level library function of the operating system, so that the application program can run seamlessly in different operating systems without modification, thereby improving the interoperability between systems.

For example, Wine simulates the library function interface of Windows on Linux, so that a Windows application can run normally on Linux.

 

#### 2.1.4 Virtualization on the programming language layer

Another category of virtual machines on the programming language layer is called language-level virtual machines, such as JVM (Java Virtual Machine) and Microsoft's CLR (Common Language Runtime). This type of virtual machine runs process-level jobs. The difference is that these programs are not aimed at a hardware architecture, but a virtual architecture. The codes of these programs are first compiled into intermediate codes for their virtual architecture, and then translated into hardware machine language by the runtime support system of the virtual machine for execution.

 

### 2.2 System-level virtualization

System-level virtualization, that is, virtualization at the hardware abstraction layer and instruction set-level virtualization, is the first virtualization technology proposed and researched. There are currently many specific implementations of this technology. Before introducing them, there are It is essential to understand the paths that can be taken to implement system-level virtualization.

Each virtual machine has its own virtual hardware. Through the simulation of the virtualization layer, the operating system in the virtual machine thinks that it is still running exclusively on a system. This virtualization layer is called a virtual machine monitor (Virtual Machine Monitor). Machine Monitor, VMM). The virtualization of physical resources by VMM can be attributed to three main tasks: processor virtualization, memory virtualization, and I/O virtualization. Among them, processor virtualization is the core part of VMM, because accessing memory or performing I/O itself is realized through some instructions.

 

#### 2.2.1 Virtualizable architecture and non-virtualizable architecture

In system-level virtualization, the virtual computer system and the physical computer system can be two completely different ISA (Instruction Set Architecture, instruction set architecture) systems, for example, an Itanium virtual computer can be run on an x86 physical computer . However, different ISAs make every instruction of the virtual machine need to be simulated and executed on the physical machine, resulting in a great drop in performance.

Obviously, system virtualization with the same architecture usually has better performance, and the VMM is relatively simple to implement. In this case, most of the instructions of the virtual machine can be run directly on the processor, and only those sensitive instructions closely related to hardware resources will be processed by the VMM. A problem at this time is to be able to screen out these sensitive instructions well. But in fact, some processors did not fully consider the requirements of virtualization at the beginning of the design, resulting in the inability to identify all sensitive instructions, and thus do not have a complete virtualizable structure.

Most modern computer architectures have two or more privilege levels that separate system software from application software. Some instructions for operating and managing key system resources in the system will be designated as privileged instructions, and these instructions can only be executed correctly at the highest privilege level. If running at a non-highest privileged level, the privileged instruction will cause an exception, and the processor will fall into the highest privileged level, which will be handled by the system software.

In the x86 architecture, all privileged instructions are sensitive instructions, but not all sensitive instructions are privileged instructions.

In order for the VMM to fully control system resources, it does not allow the operating system on the virtual machine to directly execute sensitive instructions. If all sensitive instructions on a system are privileged instructions, a very simple method can be used to implement a virtual environment: run the VMM at the highest privilege level of the system, and run the guest operating system at a non-highest privilege level , when the guest operating system is trapped into the VMM due to the execution of sensitive instructions, the VMM emulates the execution of sensitive instructions that cause exceptions. This method is called "trapped re-emulation".

All in all, the core of judging whether an architecture can be virtualized lies in the structure's support for sensitive instructions. If all sensitive instructions in an architecture are privileged instructions, it is called a virtualizable architecture, otherwise it is called a non-virtualizable architecture.

 

#### 2.2.2 Classification by implementation method

There are many different specific implementation schemes for system-level virtualization, which can be divided into the following categories according to different implementation methods.

##### (1) Emulation

We already know that the method of implementing a virtual machine by trapping and simulating the execution of sensitive instructions has a prerequisite: all sensitive instructions must be privileged instructions. If there are sensitive instructions on an architecture that are not privileged instructions, then there are virtualization holes, and some methods can be used to fill or avoid these holes. The simplest and most direct method is that all instructions are realized by simulation, that is, to fetch an instruction and simulate the execution effect of this instruction. This method is called emulation.

Emulation is the most complex virtualization implementation technology. Using the emulation method, an operating system designed for PowerPC can run on an x86 processor, which cannot be realized in other virtualization solutions. It is even possible to run multiple virtual machines, each emulating a different processor. In addition, this method does not require special support for the host operating system, and the virtual machine can run completely as an application layer program.

As mentioned earlier, the main problem with using the emulation method is that it can be very slow. Since every instruction has to be emulated on the underlying hardware, it's not uncommon to see a 100x slowdown. To achieve high-fidelity simulations, including cycle accuracy, CPU cache behavior, etc., the actual speed difference may even reach as much as 1000 times.

A typical implementation that uses this approach is Bochs.

##### (2) Full Virtualization

From the perspective of the guest operating system, the fully virtualized virtual platform is the same as the real platform, and the guest operating system is unaware that it is running on a virtual platform. Such a virtual platform can run the existing operating system without operating No modifications are made to the system, so this approach is called full virtualization.

Furthermore, the behavior of the guest is reflected by the execution, so the VMM needs to be able to correctly handle all possible instructions. In terms of implementation, taking the x86 architecture as an example, full virtualization has gone through two stages: software-assisted full virtualization and hardware-assisted full virtualization.

①Complete virtualization implemented by software

In the early days of x86 virtualization technology, there was no support for virtualization at the hardware level, so full virtualization could only be realized through software. A typical approach is binary code translation (Binary Translation).

The idea of ​​binary code translation is to convert instructions that are difficult to virtualize into instructions that support virtualization by scanning and modifying the binary code of the client. VMM usually scans the binary code of the operating system, and once it finds an instruction that needs to be processed, it translates it into an instruction block (Cache Block) that supports virtualization. These instruction blocks can cooperate with the VMM to access restricted virtual resources, or explicitly raise exceptions for further processing by the VMM.

Although this technology can realize complete virtualization, it is difficult to guarantee its integrity in architecture. Therefore, x86 manufacturers have added support for virtualization on the hardware, thus realizing virtualization on the hardware architecture.

②Hardware Assisted Full Virtualization

It can be expected that if the hardware itself has sufficient virtualization functions, the execution of sensitive instructions or access to sensitive resources by the operating system can be intercepted, and then reported to the VMM in an abnormal manner, thus solving the problem of virtualization. Hardware virtualization is a complete virtualization method. Therefore, the access to memory and peripherals is also carried by instructions. The interception of processor instruction level means that VMM can simulate an environment exactly the same as the real host.

Intel's VT-x and AMD's AMD-V are representatives of this direction. Taking VT-x as an example, it introduces a new execution mode on the processor to run the virtual machine. When the virtual machine executes in this special mode, it still faces a complete set of processor registers. and execution environment, but any sensitive operations will be intercepted by the processor and reported to the VMM.

In the current system-level virtualization solutions, full virtualization is very common. Typical well-known products include VirtualBox, KVM, VMware Workstation and VMware ESX (it was renamed VMware vSphere in its version 4.0), Xen (full virtualization is also supported).

##### (3) Class virtualization (Para-Virtualization)

Such a virtual platform requires more or less modifications to the running guest operating system to adapt to the virtual environment, so the guest operating system knows that it is running on the virtual platform and will actively adapt. This approach is called class virtualization, and sometimes paravirtualization. In addition, it is worth pointing out that a VMM can provide both a fully virtualized virtual platform and a quasi-virtualized virtual platform.

Class virtualization enables the VMM to virtualize physical resources by modifying instructions at the source code level to avoid virtualization vulnerabilities. As mentioned above, x86 has some instructions that are difficult to virtualize. Full virtualization uses Binary Translation to avoid virtualization vulnerabilities at the binary code level. Class virtualization adopts another idea, that is, to modify the code of the operating system kernel so that the operating system kernel completely avoids these difficult-to-virtualize instructions.

Now that the kernel code already needs modification, class virtualization can further be used to optimize I/O. In other words, class virtualization is not to simulate real-world devices, because too many register simulations will reduce performance. In contrast, class virtualization can customize highly optimized protocol I/O. This I/O protocol is completely transaction-based and can achieve a speed similar to that of a physical machine.

This kind of virtual technology is represented by Xen. Microsoft's Hyper-V adopts a technology similar to Xen, and Hyper-V can also be classified as paravirtualization.

 

#### 2.2.3 Classification according to the implementation structure

In the implementation of system-level virtualization, VMM plays a key role. The components of VMM have been introduced earlier. From the perspective of Host implementing VMM, the current mainstream virtualization technologies can be divided into the following three categories according to the implementation structure.

##### Hypervisor Model
The term Hypervisor appeared in the 1970s. In the early computer world, the operating system was called Supervisor, so the operating system that can run on other operating systems was called Hypervisor.

In the Hypervisor model, VMM can be regarded as a complete operating system first, but unlike traditional operating systems, VMM is designed for virtualization, so it also has virtualization functions. From an architectural point of view, first of all, all physical resources such as processors, memory, and I/O devices are owned by the VMM. Therefore, the VMM is responsible for managing physical resources; secondly, the VMM needs to provide virtual machines to run customers. Therefore, the VMM is also responsible for the creation and management of the virtual environment.

Since the VMM has both a physical resource management function and a virtualization function, the efficiency of physical resource virtualization will be higher. In terms of security, the security of the virtual machine only depends on the security of the VMM. While the hypervisor model has high virtualization efficiency, it also has its disadvantages. Since the VMM fully owns the physical resources, the VMM needs to manage the physical resources, including device drivers. We know that the workload of device driver development is huge. Therefore, it is a big challenge for the hypervisor model. In fact, in actual products, the VMM based on the Hypervisor model usually selectively selects some I/O devices to support according to product positioning, rather than supporting all I/O devices.

A typical example of this model is VMware vSphere for enterprise applications.

 

##### Host Model
Different from the Hypervisor model. In the hosting model, physical resources are managed by the host operating system. The host operating system is a traditional operating system, such as Windows, Linux, etc. These traditional operating systems are not designed for virtualization, so they do not have virtualization functions themselves, and the actual virtualization functions are provided by the VMM. VMM is usually an independent kernel module of the host operating system, and some implementations also include user-mode processes, such as the user-mode device model responsible for I/O virtualization. The VMM obtains resources by invoking services of the host operating system, and realizes virtualization of processors, memory, and I/O devices. After the VMM creates a virtual machine, it usually takes the virtual machine as a process of the host operating system to participate in scheduling.

The advantages and disadvantages of the host model are exactly the opposite of those of the Hypervisor model. The biggest advantage of the host model is that it can make full use of the device drivers of the existing operating system. VMM does not need to re-implement the drivers for various I/O devices, and can focus on the virtualization of physical resources. Considering that there are many types of I/O devices, and the workload of device driver development is very large, this advantage is of great significance. In addition, the host model can also take advantage of other functions of the host operating system, such as scheduling and power management, which can be used directly without VMM reimplementation.

Of course, the host model also has disadvantages. Since the physical resources are controlled by the host operating system, VMM has to call the services of the host operating system to obtain resources for virtualization, and those system services did not consider virtualization support at the beginning of design and development. Therefore, the efficiency and functionality of VMM virtualization will be affected to some extent. In addition, in terms of security, since the VMM is a part of the kernel of the host operating system, if the kernel of the host operating system is insecure, then the VMM is also insecure. The system is also insecure. In other words, the security of the virtual machine not only depends on the security of the VMM, but also depends on the security of the host operating system.

Typical examples of this model are KVM, VirtualBox and VMware Workstation.

##### Hybrid Model
A hybrid model is a confluence of the above two models. The VMM is still at the lowest level and has all the physical resources. Different from the Hypervisor mode, VMM will actively give up control of most I/O devices and hand them over to a privileged operating system running in a privileged virtual machine. Correspondingly, the responsibility of VMM virtualization is also shared. The virtualization of the processor and memory is still done by the VMM, while the virtualization of the I/O is done by the cooperation of the VMM and the privileged operating system.

I/O device virtualization is accomplished jointly by the VMM and the privileged operating system. Therefore, the device model module is located in the privileged operating system and cooperates with the VMM through a corresponding communication mechanism.

The hybrid model combines the advantages of the above two models. The VMM can utilize the I/O device driver of the existing operating system and does not require additional development. VMM directly controls physical resources such as processors and memory, and the efficiency of virtualization is relatively high.

In terms of security, if the permissions of the privileged operating system are properly controlled, the security of the virtual machine only depends on the VMM. Of course, hybrid models also have drawbacks. Since the privileged operating system runs on the virtual machine, when the privileged operating system is required to provide services, the VMM needs to switch to the privileged operating system, which generates context switching overhead. When the switching is frequent, the overhead of context switching will cause a significant decrease in performance. For performance reasons, many functions must still be implemented in the VMM, such as the scheduler and power management.

A typical example of this model is Xen.

 

### 2.3 OS level virtualization

In operating system virtualization technology, there is only a unique system kernel on each node, and no hardware device is virtualized. Multiple virtual environments can be isolated from each other by using features provided by the operating system. The so-called container (Container) technology, such as the most popular container system Docker so far, belongs to the operating system level virtualization. In addition, in different scenarios, the isolated virtual environment is also called a virtual environment (ie, VE, Virtual Environment) or a virtual private server (ie, VPS, Virtual Private Server).

Taking container technology as an example, it has its own unique advantages. Its appearance, on the one hand, solves the problem of independence between applications that is neglected and lacked by traditional operating systems. On the other hand, it avoids relatively cumbersome system-level virtualization It is a lightweight virtualization solution.

A major challenge in the field of operating systems has always been the contradiction between mutual independence and resource interoperability among applications, that is, each application hopes to run in a relatively independent system environment without being affected by Interference from other programs, while being able to exchange and share system resources with other programs in a convenient and quick manner. The current general-purpose operating system puts more emphasis on the interoperability between programs, but lacks effective support for the relative independence of programs. However, for many distributed systems such as Web services, databases, game platforms and other application fields, it can provide efficient resource interoperability at the same time. It is equally important to maintain the relative independence between programs.

Mainstream virtualization products such as VMware and Xen all adopt the hypervisor model (the hybrid model adopted by Xen is not much different from the hypervisor model, and can be collectively referred to as the hypervisor model). This model realizes the isolation of upper-layer applications by running applications in multiple different virtual machines. However, since the Hypervisor model tends to have a relatively independent system resource for each virtual machine to provide more complete independence, this strategy makes it very difficult to achieve interoperability between applications in different virtual machines. For example, even if they are running on the same physical machine, if they are in different virtual machines, the applications can only exchange data through the network instead of sharing memory or files. However, if container technology is used, since each container shares the same host operating system, it can provide efficient system resource sharing support while meeting basic independence requirements.

Container technology can also use system resources more efficiently. Because containers do not require additional overhead such as hardware virtualization and running a complete operating system, compared with virtual machine technology, a host with the same configuration can often run a larger number of applications. In addition, containers also have a faster startup time. Traditional virtual machine technology often takes several minutes to start application services. For containers, because they run directly on the host kernel and do not need to start a complete operating system, it can be done in seconds or even The millisecond-level startup time greatly saves the time for application development, testing, and deployment.

 

## 3 Typical virtualization technology implementation and its characteristics

### 3.1 System-level virtualization implementation

#### 3.1.1 VMware

VMware is one of the mainstream vendors of x86 virtualization software. Three of the five founders of VMware have studied operating system virtualization at Stanford University, and projects include the SimOS system simulator and the Disco virtual machine monitor. In 1998, they co-founded VMware with two other founders, headquartered in Palo Alto, California.

VMware provides a series of virtualization products, and the application areas of products range from servers to desktops. The following is a brief introduction to VMware's main products, including VMware ESX, VMware Server and VMware Workstation.

VMware ESX Server is VMware's flagship product, and subsequent versions are renamed VMware vSphere. Based on the Hypervisor model, ESX Server has been optimized in terms of performance and security, and is a product for enterprise-level applications. VMware ESX Server supports full virtualization and can run guest operating systems such as Windows, Linux, Solaris, and Novell Netware. VMware ESX Server also supports class virtualization and can run guest operating systems above Linux 2.6.21. Early versions of ESX Server used software virtualization based on Binary Translation technology. Since ESX Server 3 has adopted hardware virtualization technology, it supports Intel VT technology and AMD-V technology.

VMware Server, formerly known as VMware GSX Server, is VMware's entry-level server-oriented product. VMware Server adopts the host model, and the host operating system can be Windows or Linux. The functions of VMware Server are similar to those of ESX Server, but there is a gap with ESX Server in terms of performance and security. VMware Server also has its own advantages. Because of the host model, VMware Server supports more types of hardware than ESX Server.

VMware Workstation is VMware's flagship product for the desktop. Similar to VMware Server, VMware Workstation is also based on the host model, and the host operating system can be Windows or Linux. VMware Workstation also supports full virtualization and can run guest operating systems such as Windows, Linux, Solaris, Novell Netware, and FreeBSD. Different from VMware Server, VMware Workstation is specially optimized for desktop applications, such as assigning USB devices to virtual machines and 3D acceleration for virtual machine graphics cards.

 

#### 3.1.2 Microsoft

Microsoft started later than VMware in terms of virtualization products, but after realizing the importance of virtualization, Microsoft has launched a series of virtualization products through external acquisitions and internal development, and has formed a relatively complete virtualization product line. Microsoft's virtualization products cover server virtualization (Hyper-V) and desktop virtualization (Virtual PC).

Virtual PC is a desktop-oriented virtualization product, first developed by Connectix, and later acquired by Microsoft. Virtual PC is a virtual machine product based on the host model, and the host operating system is Windows. Early versions also adopted software virtualization, based on Binary Translation technology. Later versions already support hardware virtualization technology.

Windows Server 2008 is a server operating system launched by Microsoft, and one of the important new functions is the virtualization function. Its virtualization architecture adopts a hybrid model. Hyper-V, one of the important components, runs at the bottom layer as a Hypervisor, and Server 2008 itself runs on top of Hyper-V as a privileged operating system. Server 2008 uses hardware virtualization technology and must run on a processor that supports Intel VT technology or AMD-V technology.

 

#### 3.1.3 Xen

Xen is an open source virtual machine software licensed under the GPL. Xen originated as a research project led by Ian Pratt at the University of Cambridge in the United Kingdom. Later, Xen became an independent community-driven open source software project. The Xen community has attracted developers from many companies and scientific research institutes, and has developed very rapidly. Afterwards, Ian founded XenSource to commercialize Xen, and launched a Xen-based product, Xen Server. In 2007, Ctrix acquired XenSource to continue to promote the commercial application of Xen, and the Xen open source project itself was independent to http://www.xen.org.

From a technical point of view, Xen is based on a hybrid model. The privileged operating system (called Domain 0 in Xen) can be Linux, Solaris, and NetBSD. In theory, other operating systems can also be transplanted as the privileged operating system of Xen. Xen's initial virtualization idea is class virtualization. By modifying the Linux kernel, processor and memory virtualization is realized, and device class virtualization is realized by introducing the I/O front-end driver/backend driver (front/backend) architecture. Later, full virtualization and hardware virtualization technologies were also supported.

 

#### 3.1.4 KVM

KVM (Kernel-based Virtual Machine) is also an open source virtual machine software based on GPL authorization. KVM was first developed by Qumranet, and appeared on the Linux kernel mailing list in 2006. It was integrated into the Linux 2.6.20 kernel in 2007 and became part of the kernel.

KVM supports hardware virtualization methods and combines with QEMU to provide device virtualization. The feature of KVM is that it is very well integrated with the Linux kernel, and like Xen, as an open source software, KVM is also very portable.

 

#### 3.1.5 Oracle VM VirtualBox

VirtualBox is an open source virtual machine software, similar to VMware Workstation. VirtualBox is a software developed by Innotek of Germany and produced by Sun Microsystems. It is written in Qt and officially changed its name to Oracle VM VirtualBox after Sun was acquired by Oracle. Innotek releases VirtualBox under the GNU General Public License (GPL). Users can install and execute Solaris, Windows, DOS, Linux, BSD and other systems on VirtualBox as client operating systems. Now developed by Oracle Corporation, it is part of Oracle's VM virtualization platform technology.

 

#### 3.1.6 Bochs

    Bochs is an x86 computer emulator that is portable and runs on many platforms, including x86, PowerPC, Alpha, SPARC, and MIPS. It enables Bochs to simulate not only the processor, but also the entire computer, including computer peripherals, such as keyboard, mouse, video and image hardware, network card (NIC) and so on.

Bochs can be configured as an older Intel® 386 processor or its successors, such as 486, Pentium, Pentium Pro or 64-bit processors. It even emulates some optional graphics commands, such as MMX and 3DNow.

 

#### 3.1.7 QEMU

    QEMU is a set of free software for emulating processors written by Fabrice Bellard. It is similar to Bochs and PearPC, but it has some features that the latter two do not have, such as high speed and cross-platform features, qemu can virtualize virtual machines of different architectures, such as virtual power machines on the x86 platform . Kqemu is the accelerator of qemu. Through kqemu, an open source accelerator, QEMU can simulate the speed close to the real computer.

QEMU itself may not depend on KVM, but if there is KVM and the hardware (processor) supports functions such as Intel VT, then QEMU can use the functions provided by KVM to improve performance when virtualizing the processor. In other words, KVM lacks device virtualization and corresponding user space management tools for virtual machines, so it borrows QEMU's code and streamlines it, and together with KVM, it forms a complete virtualization solution, which may be called: KVM+QEMU .

 

### 3.2 Operating system level virtualization implementation

#### 3.2.1 chroot

The concept of the container began with UNIX chroot in 1979, which is a system call on the UNIX operating system to change the root directory of a process and its child processes to a new location in the file system, so that these processes can only access to that directory. The idea of ​​this feature is to give each process its own disk space. Then in 1982, it was added to the BSD system.

 

#### 3.2.2 LXC

LXC means LinuX Containers, which is the first and most complete implementation of the Linux container manager, implemented through cgroups and the Linux namespace namespace. LXC exists in the liblxc library, which provides API implementations in various programming languages, including Python3, Python2, Lua, Go, Ruby, and Haskell. Unlike other container technologies, LXC can work on the normal Linux kernel without adding patches. Currently the LXC project is sponsored and hosted by Canonical.

 

#### 3.2.3 Docker

Docker is by far the most popular and widely used container management system. It started as an internal project of a PaaS service company called dotCloud, which later changed its name to Docker. Docker also used LXC at the beginning, and then replaced it with libcontainer developed by itself. Unlike other container platforms, Docker introduces an entire ecosystem for managing containers, including an efficient, layered container image model, global and local container registries, clear REST API, command line, and more. At a later stage, Docker promoted the implementation of a container cluster management solution called Docker Swarm.

 

#### 3.2.4 Linux VServer

    Linux-VServer is also an OS-level virtualization solution. Linux-VServer virtualizes the Linux kernel so that multiple user space environments—also known as Virtual Private Servers (VPS)—can run independently without knowing each other. Linux-VServer realizes the isolation of user space by modifying the Linux kernel.

Linux-VServer also uses chroot to isolate the root directory for each VPS. While chroot allows specifying a new root directory, some other feature (called Chroot-Barrier) is required to restrict the VPS from going back to the parent directory from its isolated root directory. Given an isolated root directory, each VPS can have its own user list and root password.

Versions 2.4 and 2.6 of the Linux kernel support Linux-VServer, which runs on many platforms, including x86, x86-64, SPARC, MIPS, ARM, and PowerPC.

 

#### 3.2.5 Virtuozzo/OpenVZ

Virtuozzo is the name of the operating system virtualization software of SWsoft (currently SWsoft has been renamed Parallels). Virtuozzo is a commercial solution, and OpenVZ is an open source project based on Virtuozzo. They also use operating system-level virtualization technology. OpenVZ is similar to Linux-VServer in that it provides virtualization, isolation, resource management, and stateful inspection by patching the Linux kernel. Each OpenVZ container has an isolated file system, user and user group, etc.

 