---
layout: post 
title: Virtualization and you + a crash course
date: 2022-11-9
---


<br>

# INTRODUCTION

What is virtualization ? 

Virtualization uses software to create an abstraction layer over computer hardware that allows the hardware elements of a single computer—processors, memory, storage and more—to be divided into multiple virtual computers, commonly called virtual machines (VMs). Each VM runs its own operating system (OS) and behaves like an independent computer, even though it is running on just a portion of the actual underlying computer hardware.

It follows that virtualization enables more efficient utilization of physical computer hardware and allows a greater return on an organization’s hardware investment.

Today, virtualization is a standard practice in enterprise IT architecture. It is also the technology that drives cloud computing economics. Virtualization enables cloud providers to serve users with their existing physical computer hardware; it enables cloud users to purchase only the computing resources they need when they need it, and to scale those resources cost-effectively as their workloads grow [VPS] (https://www.ibm.com/cloud/learn/vps).


# Benfetis of virtualization 

* Resource efficiency: Before virtualization, each application server required its own dedicated physical CPU—IT staff would purchase and configure a separate server for each application they wanted to run. (IT preferred one application and one operating system (OS) per computer for reliability reasons.) Invariably, each physical server would be underused. In contrast, server virtualization lets you run several applications—each on its own VM with its own OS—on a single physical computer (typically an x86 server) without sacrificing reliability. This enables maximum utilization of the physical hardware’s computing capacity.

* Easier management: Replacing physical computers with software-defined VMs makes it easier to use and manage policies written in software. This allows you to create automated IT service management workflows. For example, automated deployment and configuration tools enable administrators to define collections of virtual machines and applications as services, in software templates. This means that they can install those services repeatedly and consistently without cumbersome, time-consuming. and error-prone manual setup. Admins can use virtualization security policies to mandate certain security configurations based on the role of the virtual machine. Policies can even increase resource efficiency by retiring unused virtual machines to save on space and computing power.7

* Minimal downtime: OS and application crashes can cause downtime and disrupt user productivity. Admins can run multiple redundant virtual machines alongside each other and failover between them when problems arise. Running multiple redundant physical servers is more expensive.

* Faster provisioning: Buying, installing, and configuring hardware for each application is time-consuming. Provided that the hardware is already in place, provisioning virtual machines to run all your applications is significantly faster. You can even automate it using management software and build it into existing workflows.


# Hypervisors 

There are actually two types of hypervisors: 

* **Type 1 or bare metal hypervisors** interact with the underlying physical resources, replacing the traditional operating system altogether. They most commonly appear in virtual server scenarios.

* **Type 2 hypervisors**  run as an application on an existing OS. Most commonly used on endpoint devices to run alternative operating systems, they carry a performance overhead because they must use the host OS to access and coordinate the undrlying hardware resources.

## Type 1 hypervisors:
1. VMware ESX and ESXi

These hypervisors offer advanced features and scalability, but require licensing, so the costs are higher.

There are some lower-cost bundles that VMware offers and they can make hypervisor technology more affordable for small infrastructures.

VMware is the leader in the Type-1 hypervisors. Their vSphere/ESXi product is available in a free edition and 5 commercial editions.
2. Microsoft Hyper-V

The Microsoft hypervisor, Hyper-V doesn’t offer many of the advanced features that VMware’s products provide.
However,  with XenServer and vSphere, Hyper-V is one of the top 3 Type-1 hypervisors.

It was first released with Windows Server, but now Hyper-V has been greatly enhanced with Windows Server 2012 Hyper-V. Hyper-V is available in both a free edition (with no GUI and no virtualization rights) and 4 commercial editions – Foundations (OEM only), Essentials, Standard, and Datacenter. Hyper-V
3. Citrix XenServer

It began as an open source project.
The core hypervisor technology is free, but like VMware’s free ESXi, it has almost no advanced features.
Xen is a type-1 bare-metal hypervisor. Just as Red Hat Enterprise Virtualization uses KVM, Citrix uses Xen in the commercial XenServer.

Today, the Xen open source projects and community are at Xen.org. Today, XenServer is a commercial type-1 hypervisor solution from Citrix, offered in 4 editions. Confusingly, Citrix has also branded their other proprietary solutions like XenApp and XenDesktop with the Xen name.
4. Oracle VM

The Oracle hypervisor is based on the open source Xen.
However, if you need hypervisor support and product updates, it will cost you.
Oracle VM lacks many of the advanced features found in other bare-metal virtualization hypervisors.

##Type 2 hypervisor
1. VMware Workstation/Fusion/Player (CLOSED SOURCE PRIVATE USE s-FREE)

VMware Player is a free virtualization hypervisor.

It is intended to run only one virtual machine (VM) and does not allow creating VMs.
VMware Workstation is a more robust hypervisor with some advanced features, such as record-and-replay and VM snapshot support.

VMware Workstation has three major use cases:

    for running multiple different operating systems or versions of one OS on one desktop,
    for developers that need sandbox environments and snapshots, or
    for labs and demonstration purposes.

2. VMware Server(CLOSED SOURCE)

VMware Server is a free, hosted virtualization hypervisor that’s very similar to the VMware Workstation.
VMware has halted development on Server since 2009
3. Microsoft Virtual PC (CLOSED SOURCE)

This is the latest Microsoft’s version of this hypervisor technology, Windows Virtual PC and runs only on Windows 7 and supports only Windows operating systems running on it.
4. Oracle VM VirtualBox (FOSS)

VirtualBox hypervisor technology provides reasonable performance and features if you want to virtualize on a budget. Despite being a free, hosted product with a very small footprint, VirtualBox shares many features with VMware vSphere and Microsoft Hyper-V.
5. Red Hat Enterprise Virtualization (OSS PAID)

Red Hat’s Kernel-based Virtual Machine (KVM) has qualities of both a hosted and a bare-metal virtualization hypervisor. It can turn the Linux kernel itself into a hypervisor so the VMs have direct access to the physical hardware.

6. KVM (FOSS)

This is a virtualization infrastructure for the Linux kernel. It supports native virtualization on processors with hardware virtualization extensions.

The open-source KVM (or Kernel-Based Virtual Machine) is a Linux-based type-1 hypervisor that can be added to most Linux operating systems including Ubuntu, Debian, SUSE, and Red Hat Enterprise Linux, but also Solaris, and Windows.

00. KVM

This is a virtualization infrastructure for the Linux kernel. It supports native virtualization on processors with hardware virtualization extensions.

The open-source KVM (or Kernel-Based Virtual Machine) is a Linux-based type-1 hypervisor that can be added to most Linux operating systems including Ubuntu, Debian, SUSE, and Red Hat Enterprise Linux, but also Solaris, and Windows.


## Types of virtualization 
To this point we’ve discussed server virtualization, but many other IT infrastructure elements can be virtualized to deliver significant advantages to IT managers (in particular) and the enterprise as a whole. In this section, we'll cover the following types of virtualization:

    Desktop virtualization
    Network virtualization
    Storage virtualization
    Data virtualization
    Application virtualization
    Data center virtualization
    CPU virtualization
    GPU virtualization
    Linux virtualization
    Cloud virtualization

Desktop virtualization

Desktop virtualization lets you run multiple desktop operating systems, each in its own VM on the same computer.

There are two types of desktop virtualization:

    Virtual desktop infrastructure (VDI) runs multiple desktops in VMs on a central server and streams them to users who log in on thin client devices. In this way, VDI lets an organization provide its users access to variety of OS's from any device, without installing OS's on any device. See "What is Virtual Desktop Infrastructure (VDI)?" for a more in-depth explanation.
    Local desktop virtualization runs a hypervisor on a local computer, enabling the user to run one or more additional OSs on that computer and switch from one OS to another as needed without changing anything about the primary OS.

For more information on virtual desktops, see “Desktop-as-a-Service (DaaS).”
Network virtualization

Network virtualization uses software to create a “view” of the network that an administrator can use to manage the network from a single console. It abstracts hardware elements and functions (e.g., connections, switches, routers, etc.) and abstracts them into software running on a hypervisor. The network administrator can modify and control these elements without touching the underlying physical components, which dramatically simplifies network management.

Types of network virtualization include software-defined networking (SDN), which virtualizes hardware that controls network traffic routing (called the “control plane”), and network function virtualization (NFV), which virtualizes one or more hardware appliances that provide a specific network function (e.g., a firewall, load balancer, or traffic analyzer), making those appliances easier to configure, provision, and manage.
Storage virtualization

Storage virtualization enables all the storage devices on the network— whether they’re installed on individual servers or standalone storage units—to be accessed and managed as a single storage device. Specifically, storage virtualization masses all blocks of storage into a single shared pool from which they can be assigned to any VM on the network as needed. Storage virtualization makes it easier to provision storage for VMs and makes maximum use of all available storage on the network.

For a closer look at storage virtualization, check out "What is Cloud Storage?"
Data virtualization

Modern enterprises store data from multiple applications, using multiple file formats, in multiple locations, ranging from the cloud to on-premise hardware and software systems. Data virtualization lets any application access all of that data—irrespective of source, format, or location.

Data virtualization tools create a software layer between the applications accessing the data and the systems storing it. The layer translates an application’s data request or query as needed and returns results that can span multiple systems. Data virtualization can help break down data silos when other types of integration aren’t feasible, desirable, or affordable.
Application virtualization

Application virtualization runs application software without installing it directly on the user’s OS. This differs from complete desktop virtualization (mentioned above) because only the application runs in a virtual environment—the OS on the end user’s device runs as usual. There are three types of application virtualization: 

    Local application virtualization: The entire application runs on the endpoint device but runs in a runtime environment instead of on the native hardware.
    Application streaming: The application lives on a server which sends small components of the software to run on the end user's device when needed.
    Server-based application virtualization The application runs entirely on a server that sends only its user interface to the client device.

Data center virtualization

Data center virtualization abstracts most of a data center’s hardware into software, effectively enabling an administrator to divide a single physical data center into multiple virtual data centers for different clients.

Each client can access its own infrastructure as a service (IaaS), which would run on the same underlying physical hardware. Virtual data centers offer an easy on-ramp into cloud-based computing, letting a company quickly set up a complete data center environment without purchasing infrastructure hardware.
CPU virtualization

CPU (central processing unit) virtualization is the fundamental technology that makes hypervisors, virtual machines, and operating systems possible. It allows a single CPU to be divided into multiple virtual CPUs for use by multiple VMs.

At first, CPU virtualization was entirely software-defined, but many of today’s processors include extended instruction sets that support CPU virtualization, which improves VM performance.
GPU virtualization

A GPU (graphical processing unit) is a special multi-core processor that improves overall computing performance by taking over heavy-duty graphic or mathematical processing. GPU virtualization lets multiple VMs use all or some of a single GPU’s processing power for faster video, artificial intelligence (AI), and other graphic- or math-intensive applications.

    Pass-through GPUs make the entire GPU available to a single guest OS.
    Shared vGPUs divide physical GPU cores among several virtual GPUs (vGPUs) for use by server-based VMs.

Linux virtualization

Linux includes its own hypervisor, called the kernel-based virtual machine (KVM), which supports Intel and AMD’s virtualization processor extensions so you can create x86-based VMs from within a Linux host OS.

As an open source OS, Linux is highly customizable. You can create VMs running versions of Linux tailored for specific workloads or security-hardened versions for more sensitive applications.
Cloud virtualization

As noted above, the cloud computing model depends on virtualization. By virtualizing servers, storage, and other physical data center resources, cloud computing providers can offer a range of services to customers, including the following: 

    Infrastructure as a service (IaaS): Virtualized server, storage, and network resources you can configure based on their requirements.  
    Platform as a service (PaaS): Virtualized development tools, databases, and other cloud-based services you can use to build you own cloud-based applications and solutions.
    Software as a service (SaaS): Software applications you use on the cloud. SaaS is the cloud-based service most abstracted from the hardware.

If you’d like to learn more about these cloud service models, see our guide: “IaaS vs. PaaS vs. SaaS.”


## Security

Virtualization offers some security benefits. For example, VMs infected with malware can be rolled back to a point in time (called a snapshot) when the VM was uninfected and stable; they can also be more easily deleted and recreated. You can’t always disinfect a non-virtualized OS, because malware is often deeply integrated into the core components of the OS, persisting beyond system rollbacks.

Virtualization also presents some security challenges. If an attacker compromises a hypervisor, they potentially own all the VMs and guest operating systems. Because hypervisors can also allow VMs to communicate between themselves without touching the physical network, it can be difficult to see their traffic, and therefore to detect suspicious activity.

A Type 2 hypervisor on a host OS is also susceptible to host OS compromise.

The market offers a range of virtualization security products that can scan and patch VMs for malware, encrypt entire VM virtual disks, and control and audit VM access.


## Guide to install KVM on [Arch](https://archlinux.org)
## Checking support for KVM

**Hardware support**

Checking hardware support for KVM (named VT-x for Intel and AMD-V for AMD CPUs):

```term
LC_ALL=C lscpu | grep Virtualization
```

Or:

```term
grep -E --color=auto 'vmx|svm|0xc0f' /proc/cpuinfo
```

If nothing is displayed after running either command, then your processor does **not** support hardware virtualization, and you will **not** be able to use KVM.

**Note:** You may need to enable virtualization support in your BIOS. All x86_64 processors manufactured by AMD and Intel in the last 10 years support virtualization. If it looks like your processor does not support virtualization, it's almost certainly turned off in the BIOS.


**Kernel support**

Check if the necessary modules: `kvm` and either `kvm_amd` or `kvm_intel`, are available in the kernel:

```term
zgrep CONFIG_KVM /proc/config.gz
```

You must see the module set either to `y` or `m`.

Then ensure that kernel modules are automatically loaded at boot:

```term
lsmod | grep kvm
```

Output example:

```term
kvm_intel             245760  0
kvmgt                  28672  0
mdev                   20480  2 kvmgt,vfio_mdev
vfio                   32768  3 kvmgt,vfio_mdev,vfio_iommu_type1
kvm                   737280  2 kvmgt,kvm_intel
irqbypass              16384  1 kvm
```

If the command returns nothing, the module needs to be loaded manually, see: [Kernel module handling](https://wiki.archlinux.org/index.php/Kernel_module#Manual_module_handling)

**Note:** If modprobing `kvm_intel` or `kvm_amd` fails but modprobing `kvm succeeds`, and `lscpu` claims that hardware acceleration is supported, check the BIOS settings. Some vendors, especially laptop vendors, disable these processor extensions by default. To determine whether there is no hardware support or whether the extensions are disabled in BIOS, the output from `dmesg` after having failed to modprobe will tell.

<br>

### Para-virtualization with Virtio

**Kernel Support**

Check if the VIRTIO modules are available in the kernel inside the virtual machine:

```term
zgrep VIRTIO /proc/config.gz
```

Then, check if kernel modules are automatically loaded at boot:

```term
lsmod | grep virtio
```

Also here, if the above commands return nothing, you need to load the kernel modules manually.

<br>

### Install qemu, libvirt, virt-manager and other packages needed

```term
sudo pacman -S libvirt qemu virt-manager ebtables dnsmasq bridge-utils
```

For complete information about packages and other utilities and settings see:

[QEMU](https://wiki.archlinux.org/index.php/QEMU)

[libvirt](https://wiki.archlinux.org/index.php/Libvirt)

[libvirt clients](https://wiki.archlinux.org/index.php/Libvirt#Client)

<br>

### Set user Group

Add user to `libvirt` Group:

```term
sudo usermod -aG libvirt <username>
```
<br>

### Systemctl Service libvirtd

Start the `libvirtd.service` service:

```term
sudo systemctl start libvirtd.service
```

Enable `libvirt.service` service at boot:

```term
sudo systemctl enable libvirtd.service
```

Start `virt-manager`:

```term
virt-manager
```
See: [Virtual Machine Manager](https://virt-manager.org/)

---
Source: [KVM - ArchWiki](https://wiki.archlinux.org/index.php/KVM)
