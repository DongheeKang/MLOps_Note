# Linux Administration: Advanced


## Contents
  * [X.509 Certificates and Public Key Infrastructures](#X509_certificates)  
  * [X.509 Certificates for Encryption, Signing and Authentication](#X509_certificates_2)

  * [are not covered yet](#willbeupdated)
    * [Encrypted File Systems]
    * [DNS and Cryptography]
    * [Host Hardening]
    * [Host Intrusion Detection	]
    * [User Management and Authentication]
    * [FreeIPA Installation and Samba Integration]
    * [Discretionary Access Control]
    * [Mandatory Access Control]
    * [Network File Systems]
    * [Network Hardening]
    * [Network Intrusion Detection]
    * [Packet Filtering]
    * [Virtual Private Networks]

  * [Virtualization Concepts and Theory](#virtualization)
  * [Xen](#xen)
  * [KVM](#kvm)
  * [Other Virtualization Solutions](#other_virtualization)
  * [Libvirt and Related Tools ](#libvirt)
  * [Cloud Management Tools](#management_tools)
  * [High Availability Concepts and Theory](#ha_concept)
  * [Load Balanced Clusters](#load_balancer)
  * [Failover Clusters](#failover)
  * [High Availability in Enterprise Linux Distributions](#ha_enterprise)
  * [DRBD / cLVM (Cluster Storage)](#drbd)
  * [Clustered File Systems](#clustered_filesystem)



<br/><a name="X509_certificates"></a>

### X.509 Certificates and Public Key Infrastructures

    ===============================================================================================
    325.1 X.509 Certificates and Public Key Infrastructures				 
    ===============================================================================================
    Description:
      Candidates should understand X.509 certificates and public key infrastructures.
      They should know how to configure and use OpenSSL to implement certification authorities and
      issue SSL certificates for various purposes.

      Key Knowledge Areas:
      Understand X.509 certificates, X.509 certificate lifecycle,
      X.509 certificate fields and X.509v3 certificate extensions
      Understand trust chains and public key infrastructures
      Generate and manage public and private keys
      Create, operate and secure a certification authority
      Request, sign and manage server and client certificates
      Revoke certificates and certification authorities

      The following is a partial list of the used files, terms and utilities:
      openssl, including relevant subcommands
      OpenSSL configuration
      PEM, DER, PKCS
      CSR
      CRL
      OCSP


    - X.509
      In cryptography, X.509 is an important standard for a public key infrastructure (PKI) to
      manage digital certificates and public-key encryption and a key part of the TLS (Transport
      Layer Security) protocol used to secure web and email communication.
      .pem
      .cer, .crt
      .p7b
      .p12
      .pfx

    - Public Key Infrastructure (PKI)
      PKI refers to the technical mechanisms, procedures and policies that collectively provide
      a framework for addressing the security, authentication, confidentiality and access control
      PKI utilises two core elements; Public Key Cryptography and Certification Authorities.

    - PKI standards for X.509
      PKCS7    — 	Cryptographic Message Syntax Standard, public keys with proof of identity for
      signed and/or encrypted message for PKI)
      TLS/SSL  — 	cryptographic protocols for Internet secure communications
      OCSP/CRL    Online Certificate Status Protocol / certificate revocation list,
      this is to check certificate revocation status
      PKCS12   —  Personal Information Exchange Syntax Standard, used to store a private key
      with the appropriate public key certificate

    - Digital Certificate (DA)
      A Digital Certificate is a digital file used to cryptographically bind an entity's Public
      Key to specific attributes relating to its identity.
      Digital Certificates are issued by Certification Authorities (CA) and under the technical
      recommendations of the x.509 Digital Certificate format.

    - Certificate Authority (CAs)
      CAs are characteristic of many public key infrastructure (PKI) schemes.[
      CA provides a Certification Practice Statement (CPS) that clearly states its policies and
      practices regarding the issuance and maintenance of Certificates within the PKI. The CPS
      contains operational information and legal information on the roles and responsibilities of
      all entities involved in the Certificate lifecycle (from the day it is issued to the day
      it expires).

    - Distributing Digital Certificates
      A's private -> CA( A's public + B's public ) -> B's Private
      As well as Digital Certificates being available in public repositories, they may also be
      distributed through the use of Digital Signatures. For example, when Alice Digitally signs
      a message for Bob she also attaches her Certificate to the outgoing message. Therefore,
      upon receiving the signed message Bob can verify the validity of Alice's Certificate.
      If it is successfully verified, Bob now has Alice's Public Key and can verify the validity
      of the original message signed by Alice.


<br/><a name="X509_certificates_2"></a>

### X.509 Certificates for Encryption, Signing and Authentication
    ===============================================================================================
    325.2 X.509 Certificates for Encryption, Signing and Authentication		
    ===============================================================================================
      Description: Candidates should know how to use X.509 certificates for both server and
      client authentication. Candidates should be able to implement user and server authentication
      for Apache HTTPD. The version of Apache HTTPD covered is 2.4 or higher.

      Key Knowledge Areas:
      Understand SSL, TLS and protocol versions
      Understand common transport layer security threats, for example Man-in-the-Middle
      Configure Apache HTTPD with mod_ssl to provide HTTPS service, including SNI and HSTS
      Configure Apache HTTPD with mod_ssl to authenticate users using certificates
      Configure Apache HTTPD with mod_ssl to provide OCSP stapling
      Use OpenSSL for SSL/TLS client and server tests

      Terms and Utilities:
      Intermediate certification authorities
      Cipher configuration (no cipher-specific knowledge)
      httpd.conf
      mod_ssl
      openssl

<br/><a name="willbeupdated"></a>

### not available yet
    ===============================================================================================
    will be updated
    ===============================================================================================
    325.3 Encrypted File Systems				

    325.4 DNS and Cryptography										

    326.1 Host Hardening													

    326.2 Host Intrusion Detection									

    326.3 User Management and Authentication								

    326.4 FreeIPA Installation and Samba Integration			

    327.1 Discretionary Access Control			

    327.2 Mandatory Access Control				

    327.3 Network File Systems				

    328.1 Network Hardening				

    328.2 Network Intrusion Detection			

    328.3 Packet Filtering			

    328.4 Virtual Private Networks	
    ===============================================================================================




<br/><a name="virtualization"></a>

### Virtualization Concepts and Theory

    ===============================================================================================
    330.1 Virtualization Concepts and Theory
    ===============================================================================================
    - Pros and Cons of Virtualization
      System virtual machine advantages:
      • multiple OS environments can co-exist on the same computer, in strong isolation
      from each other
      • the virtual machine can provide an instruction set architecture (ISA)
      that is somewhat different from that of the real machine
      • application provisioning, maintenance, high availability and disaster recovery

      The main disadvantages of VMs:
      • a virtual machine is less efficient than a real machine when it accesses the hardware
      indirectly
      • when multiple VMs are concurrently running on the same physical host, each VM may
      exhibit a varying and unstable performance (Speed of Execution, and not results),
      which highly depends on the workload imposed on the system by other VMs, unless
      proper techniques are used for temporal isolation among VMs.

      - Virtual Machine Monitor (VMM)
      is the proprietary name for a kernel-mode driver that functions as a firewall between
      the host OS and the virtual machines. It can prevent any single program, running in
      one of the virtual machines, from overusing the resources of the host OS

      The software that controlled virtualization was called a "control program",
      but the terms "hypervisor" or "virtual machine monitor" became preferred over time

      Variations of Virtual Machine Monitors
      • Type1 : VMwarie ESXi Server,  Microsoft Hyper-V, Citrix/XenServer
      Type2 : VMware Worksation, MS Virtual PC, VirtualBox, QEMU, KVM
      • Fully virualized
      Para-vritualized

    - Hypervisor (VMM)
      is a piece of computer software, firmware or hardware that creates and runs virtual
      machines. The hypervisor presents the guest operating systems with a virtual operating
      platform and manages the execution of the guest operating systems.

      a hypervisor, also called virtual machine manager (VMM), is one of many hardware
      virtualization techniques allowing multiple operating systems, termed guests, to run
      concurrently on a host computer. It is so named because it is conceptually one level
      higher than a supervisory program. The hypervisor presents to the guest operating
      systems a virtual operating platform and manages the execution of the guest operating
      systems. Multiple instances of a variety of operating systems may share the virtualized
      hardware resources. Hypervisors are very commonly installed on server hardware, with
      the function of running guest operating systems, that themselves act as servers.

    - Hardware Virtual Machine (HVM)
      almost complete simulation of the actual hardware to allow software,
      which typically consists of a guest operating system, to run unmodified.
      Full Virtualization or Hardware-assisted virtualizion (HVM) uses virtualization
      extensions from the host CPU to virtualize guests.

      hHVM is a platform virtualization approach that enables efficient full virtualization
      using help from hardware capabilities, primarily from the host processors. Full
      virtualization is used to simulate a complete hardware environment, or virtual machine,
      in which an unmodified guest operating system (using the same instruction set as the
      host machine) executes in complete isolation. Hardware-assisted virtualization was
      added to x86 processors (Intel VT-x or AMD-V) in 2006.

      Hardware-assisted virtualization is also known as accelerated virtualization.
      Xen calls it HVM, Virtual Iron calls it native virtualization.

    - Paravirtualization (PV)
      a hardware environment is not simulated; however, the guest programs are executed
      in their own isolated domains, as if they are running on a separate system.
      PV is a virtualization technique that presents a software interface to virtual machines
      that is similar but not identical to that of the underlying hardware.
      Guest OS kernel must replace privileged CPU instructions with calls to the hypervisor.
      Guest programs need to be specifically modified to run in this environment.
      A paravirutalized device drives will be installed in the guest (not host/hypervisor)

      The intent of the modified interface is to reduce the portion of the guest's execution
      time spent performing operations which are substantially more difficult to run in a
      virtual environment compared to a non-virtualized environment. The paravirtualization
      provides specially defined 'hooks' to allow the guest(s) and host to request and
      acknowledge these tasks, which would otherwise be executed in the virtual domain
      (where execution performance is worse). A successful paravirtualized platform may
      allow the virtual machine monitor (VMM) to be simpler (by relocating execution of
      critical tasks from the virtual domain to the host domain), and/or reduce the overall
      performance degradation of machine-execution inside the virtual-guest.

      Paravirtualization requires the guest operating system to be explicitly ported for
      the para-API — a conventional OS distribution which is not paravirtualization-aware
      cannot be run on top of a paravirtualizing VMM. However, even in cases where the
      operating system cannot be modified, still components may be available that enable
      many of the significant performance advantages of paravirtualization; for example,
      the XenWindowsGplPv project provides a kit of paravirtualization-aware device drivers,
      licensed under the terms of the GPL, that are intended to be installed into a MS
      Windows virtual-guest running on the Xen hypervisor.

    - Container Virtualization
      Containers use the host operating system as their base, and not the hypervisor.
      Rather than virtualizing the hardware (which requires full virtualized operating
      system images for each guest), containers virtualize the OS itself, sharing
      the host OS kernel and its resources with both the host and other containers.

      the hypervisor virtualization model suffer from certain built-in limitations,
      • Increased overhead of running a fully installed guest operating system.
      • Inability to freely allocate resources to processes.
      • Significant overhead from calls to the hypervisor from the guest OS can sometimes
      reduce application performance.
      Container virtualization exists largely to address some of these challenges.

    - Emulation and Simulation
      Hardware virtualization is not the same as hardware emulation. In hardware emulation,
      a piece of hardware imitates another, while in hardware virtualization, a hypervisor
      (a piece of software) imitates a particular piece of computer hardware or the entire
      computer. Furthermore, a hypervisor is not the same as an emulator; both are computer
      programs that imitate hardware, but their domain of use in language differs.

      In integrated circuit design, hardware emulation is the process of imitating the
      behavior of one or more pieces of hardware (typically a system under design) with
      another piece of hardware, typically a special purpose emulation system. The emulation
      model is usually based on RTL (e.g. Verilog) source code, which is compiled into the
      format used by emulation system. The goal is normally debugging and functional
      verification of the system being designed. Often an emulator is fast enough to be
      plugged into a working target system in place of a yet-to-be-built chip, so the whole
      system can be debugged with live data. This is a specific case of in-circuit emulation.

      Sometimes hardware emulation can be confused with hardware devices such as expansion
      cards with hardware processors that assist functions of software emulation, such as
      older daughterboards with x86 chips to allow x86 OSes to run on motherboards of
      different processor families.

      In computing, a virtual machine (VM) is an emulation of a computer system.
      And emulation reimplements a whole computing system in software, thus allows operation
      system to be run on foreign architectures.

      A computer simulation, a computer model, or a computational model is a computer
      program, or network of computers, that attempts to simulate an abstract model of a
      particular system. Computer simulations have become a useful part of mathematical
      modeling of many natural systems in physics (computational physics), astrophysics,
      chemistry and biology, human systems in economics, psychology, social science, and
      engineering. Simulation of a system is represented as the running of the system's
      model. It can be used to explore and gain new insights into new technology, and to
      estimate the performance of systems too complex for analytical solutions.

    - CPU flags
      indicating the features supported by a cpu.
      Relevant flags for virtualization:
      • HVM Hardware support for virtual machines (Xen abbreviation for AMD SVM / Intel VMX)
      • SVM Secure Virtual Machine. (AMD’s virtualization extensions to the x86 architecture,
      equivalent to Intel’s VMX, both also known as HVM in the Xen hypervisor.)
      • VMX Intel’s equivalent to AMD’s SVM

      $ cat /proc/cpuinfo
      |model name    : Intel(R) Core(TM)2 Duo CPU     E8400  @ 3.00GHz
      |…
      |flags : fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat
      |		 pse36 clflush dts acpi mmx fxsr sse sse2 ss ht tm pbe syscall nx lm
      |		 constant_tsc arch_perfmon pebs bts rep_good aperfmperf pni dtes64
      |		 monitor ds_cpl vmx smx est tm2 ssse3 cx16 xtpr pdcm sse4_1 lahf_lm
      |		 tpr_shadow vnmi flexpriority
      ---------------------------------------------------------------
      fpu    : Onboard FPU (floating point support)
      vme    : Virtual 8086 mode enhancements
      de     : Debugging Extensions (CR4.DE)
      pse    : Page Size Extensions (4MB memory pages)
      tsc    : Time Stamp Counter (RDTSC)
      msr    : Model-Specific Registers (RDMSR, WRMSR)
      pae    : Physical Address Extensions (support for more than 4GB of RAM)
      mce    : Machine Check Exception
      cx8    : CMPXCHG8 instruction (64-bit compare-and-swap)
      apic   : Onboard APIC
      sep    : SYSENTER/SYSEXIT
      mtrr   : Memory Type Range Registers
      pge    : Page Global Enable (global bit in PDEs and PTEs)
      mca    : Machine Check Architecture
      cmov   : CMOV instructions (conditional move) (also FCMOV)
      pat    : Page Attribute Table
      pse36  : 36-bit PSEs (huge pages)
      pn     : Processor serial number
      clflush: Cache Line Flush instruction
      dts    : Debug Store (buffer for debugging and profiling instructions)
      acpi   : ACPI via MSR (temperature monitoring and clock speed modulation)
      mmx    : Multimedia Extensions
      fxsr   : FXSAVE/FXRSTOR, CR4.OSFXSR
      sse    : Intel SSE vector instructions
      sse2   : SSE2
      ss     : CPU self snoop
      ht     : Hyper-Threading
      tm     : Automatic clock control (Thermal Monitor)

      Q. How can I tell if I have Intel-VT or AMD-V?
      A. With a recent enough Linux kernel, run the command:
      $ egrep '^flags.*(vmx|svm)' /proc/cpuinfo

      $ egrep -c ' lm ' /proc/cpuinfo
      If 0 is printed, it means that your CPU is not 64-bit.
      If 1 or higher, it is a 64-bit CPU.

    - Migration
      Physical to Virtual Machines(P2V)
      Virtual to Physical Machinew(V2P)
      Virtual Machines between Host systems(V2V)

      Microsoft System Center Virtual Machine Manager (SCVMM)
      VMware vCenter(V2V, V2P, P2V)
      XenConvert : part of XenServer hypervisor
      Virtuozzo  : contains automatic P2V/V2P migration tools.

      LinuxPMI : Linuix process migration infrastructure technology

    - Cloud Computing
      Internet-based computing that provides shared computer processing resources
      and data to computers and other devices on demand

    - Virutalizaiton service
      • IaaS (Infrastructure as a Service) – provides virtual infrastructure as a service
      where consumers can easily implement and utilize VMs without needing to understand,
      manage, or own the underlying physical infrastructure.

      • SaaS (Software as a Service) – provides software applications as a service
      where consumers can easily use applications without needing to understand, manage,
      or own the underlying server operating systems, software applications, databases,
      or infrastructure. (Google Apps and Salesforce CRM)

      • PaaS (Platform as a Service) – provides a software development platform
      as a service where consumers can easily build applications on a provided platform
      without any need to understand, manage, or own the underlying infrastructure.
      It allows developers to easily create applications that are easily portable.
      (Microsoft Azure and Force.com)


<br/><a name="xen"></a>

### Xen

    ===============================================================================================
    330.2 Xen
    ===============================================================================================
    - Xen architecture
      • PV ----------------------------------------------------------------------------------
      an efficient and lightweight virtualization technique. PV does not require
      virtualization extensions from the host CPU. However, paravirtualized guests
      require a PV-enabled kernel and PV drivers, so the guests are aware of the
      hypervisor and can run efficiently without emulation or virtual emulated hardware.

      • HVM ---------------------------------------------------------------------------------
      Full Virtualization or Hardware-assisted virtualizion (HVM) uses virtualization
      extensions from the host CPU to virtualize guests. HVM requires Intel VT or AMD-V
      hardware extensions. The Xen uses Qemu to emulate PC hardware, including BIOS,
      IDE disk controller, VGA graphic adapter, USB controller, network adapter etc.
      Virtualization hardware extensions are used to boost performance of the emulation.

      • PVH ---------------------------------------------------------------------------------
      This is essentially a PV guest using PV drivers for boot and I/O.
      Otherwise it uses HW virtualization extensions, without the need for emulation.
      PVH requires support in the guest operating system and is enabled with pvh=1
      in the configuration file.

      • PVHVM -------------------------------------------------------------------------------
      To boost performance, fully virtualized HVM guests can use special paravirtual
      device drivers (PVHVM or PV-on-HVM drivers). These drivers are optimized PV drivers
      for HVM environments and bypass the emulation for disk and network IO, thus giving
      you PV like performance on HVM systems.

      - XAPI, XenStore, & Xen Tools
      XAPI
      The Xen can be run with Libvirt and with XAPI. The pairing of the Xen hypervisor
      and XAPI became known as XCP (Xen Cloud Platform). In other words, Xen or XCP also
      provides good pointers on whether to use Xen or XCP, which has been supplanted by
      open source XenServer. XCP comes with several CLI(Command Lind Interface) management
      clients built into dom0 (the thin hypervisor host)

      XenStore
      is an information storage space shared between domains maintained

      xen-tools
      effectively automates the process of setting up a PV guest from scratch right to
      the point of creating config files and starting the guest. xen-tools is aimed
      at systems administered from the command-line using xl/xm
      The process can be summarized as follows:
      -Create logical volume for rootfs
      -Create logical volume for swap
      -Create filesystem for rootfs
      -Mount rootfs
      -Install operating system using debootstrap (or rinse, only debootstrap covered here)
      -Run a series of scripts to generate guest config files like fstab/inittab/menu.lst
      -Create a VM config file for the guest
      -Generate a root password for the guest system
      -Unmount the guest filesystem

      xm
      xm is the main interface for managing Xen guest domains.
      This program can be used to create, pause, and shutdown domains.

      xl
      xl toolstack is intended to remain backwards compatible with existing xm domain
      a new tool for managing xen domains, only xl is available starting from xen v4.5

      xe
      uses (/usr/bin/xe) the XAPI and can be run from any network host(xe = XCP-CLI = XAPI)

      XenTop
      displays real-time information about a Xen system, is shipped with xen tools.
      xentop utility is included in all versions of XenServer.

      XenServer
      is the leading open source virtualization platform, powered by the Xen hypervisor and
      the XAPI toolstack. It is used in the world's largest clouds and enterprises.

      XenCenter
      the extensible management dashboard for virtualised infrastructure including XenServer.
      It can be used to manage xcp hosts (but supports only windows plattform)

      OpenXenManager
      a graphical interface to manage XenServer / XCP hosts through the network.
      OpenXenManager is an open-source multiplatform clone of XenCenter

      XenApp
      application virtualization software produced by Citrix that allows Windows applications
      to be accessed via individual devices from a shared server or cloud system.

      XenDesktop
      is a Virtual Desktop Infrastructure (VDI) product developed by Citrix. It allows users
      to virtually access and run Microsoft Windows from a public or private cloud, via
      devices located anywhere. Users are able to access virtual desktops and applications
      through Citrix Receiver. Applications are delivered and managed via XenApp.

      - Dom0 and DomU
      Domain0 (Dom0)
      The architecture employs a special domain called domain 0 which contains drivers for
      the hardware, as well as the toolstack to control VMs. The Xen hypervisor is inserted
      in the GRUB menu and boots first. The hypervisor then starts Domain0.

      DomainU (DomU)
      is used as a guest. Dom0 has drivers for hardware, and it provides Xen virtual disks
      and network access for guests each referred to as a domU (unprivileged domains).

      - PV-DomU vs HVM-DomU
      PV-DomU   modify OS     Dom0-dirvers    DomU-drivers
      HVM-DomU  original OS   Qemu-dm         Xen-virtual-firmware

      - Installation
      based on Ubuntu (https://help.ubuntu.com/community/Xen)
      $ sudo apt-get install xen-hypervisor-amd64

      You need stop network-manager and set up open vSwitch and Bridge
      $ sudo apt-get install bridge-utils
      $ vi /etc/network/interfaces
      | auto lo
      | iface lo inet loopback
      |
      | auto eth0
      | iface eth0 inet manual
      |
      | auto xenbr0
      | iface xenbr0 inet dhcp
      | bridge_ports eth0
      then you can install guest OS by xen-tools(xl) or virt-manager(libvirt) or XAPI

      ---------------------------------------------------------------------------------------
      Configuration of the operating parameters, when guest VMs are generated
      $ vi /etc/xen/xmdomain.cfg
      | kernel = "/boot/vmlinuz-2.6-xenU"
      | memory = 128
      | name = "MyLinux"
      | root = "/dev/hda1 ro"
      | disk = [ "file:/var/xen/mylinux.img,hda1,w" ]
      |
      # you shuold also use
      | ramdisk     	path to initrd for the initial ramdisk
      | nic 		number of network interface cards for a domain (default is 1)
      | vif           arrays of virtual interface stanzas
      |		(each stanza represents a set of name=value operations)
      | builder	Determines the builder that constructs the domain (default is linux)
      | cpu		CPU count for the domain to start on. 0 indicates the first CPU
      | cpus		which CPUs the domain's VCPU are executable
      | extra   	the additional information to append to end of the kernel parameter line
      | nef_server 	the NFS server IP address
      | nft_root	the root directory as a path for the NFS server
      | vcpus		the number of virtual CPUs to allocate to a domain (default is 1)
      | on_shutdown   shutdown parameter to trigger a graceful shutdown
      | on_reboot	shutdown parameter to trigger a graceful reboot
      | on_crash	shutdown parameter that trigger DomU crashes.

      ---------------------------------------------------------------------------------------
      Configure Domain 0 Memory
      In order to do this you must first add the dom0_mem option to your hypervisor command
      line. This is done by editing
      $ vi /etc/default/grub
      | # Xen boot parameters for all Xen boots
      | GRUB_CMDLINE_XEN="dom0_mem=1024M,max:1024M"

      On the XenServer 6.1 or later host,
      $ /opt/xensource/libexec/xen-cmdline --set-xen dom0_mem=4096M,max:4096M
      then reboot

      ---------------------------------------------------------------------------------------
      Create a PV guest in LVM
      $ sudo vgs
      $ sudo lvcreate -L 10G -n lv_vm_ubuntu /dev/<VGNAME>
      $ sudo lvs

      Set up the initial guest configuration from copying xlexample.pvlinux
      $ vi /etc/xen/ubuntu.cfg
      | name = "ubuntu"
      |
      | kernel = "/var/lib/xen/images/ubuntu-netboot/trusty14LTS/vmlinuz"
      | ramdisk = "/var/lib/xen/images/ubuntu-netboot/trusty14LTS/initrd.gz"
      | #bootloader = "/usr/lib/xen-4.4/bin/pygrub"
      |
      | memory = 1024
      | vcpus = 1
      |
      | # Custom option for Open vSwitch
      | vif = [ 'script=vif-openvswitch,bridge=ovsbr0' ]
      |
      | disk = [ '/dev/<VGNAME>/lv_vm_ubuntu,raw,xvda,rw' ]
      |
      | # You may also consider some other options
      | # http://xenbits.xen.org/docs/4.4-testing/man/xl.cfg.5.html

      Start the VM and connect to console (-c):
      $ sudo xm create -c /etc/xen/ubuntu.cfg

      Once installed and back to command line, modify guest configuration to use pygrub
      $ sudo ln -s /usr/lib/xen-4.1/bin/pygrub /usr/bin/pygrub
      $ sudo xl shutdown ubud1
      $ vi /etc/xen/ubuntu.cfg
      | #kernel = "/var/lib/xen/images/ubuntu-netboot/trusty14LTS/vmlinuz"
      | #ramdisk = "/var/lib/xen/images/ubuntu-netboot/trusty14LTS/initrd.gz"
      | bootloader = "/usr/lib/xen-4.4/bin/pygrub"

      one can also configure GRUB2 to start xen with re-creating /boot/grub/grub.cfg
      the default boot option will be dom0 running on top of the hypervisor!
      $ dpkg-divert --divert /etc/grub.d/08_linux_xen --rename /etc/grub.d/20_linux_xen
      $ update-grup

      Now let's restart the VM again with the new bootloader.
      $ sudo xl shutdown ubud1
      $ sudo xl create -c /etc/xen/ubud1.cfg

      ---------------------------------------------------------------------------------------
      Manually installing an HVM Guest VM
      $ sudo pvs
      $ sudo lvcreate -L 4G -n ubuntu-hvm /dev/<VG>
      $ vi /etc/xen/ubuntu-hvm.cfg
      | builder = "hvm"                         : hvm=HVM, generic=PV
      | name = "ubuntu-hvm"
      | memory = "512"
      | vcpus = 1
      | vif = ['']
      | disk = ['phy:/dev/<VG>/ubuntu-hvm,hda,w',\
      |         'file:/root/ubuntu-12.04-desktop-amd64.iso,hdc:cdrom,r']
      | vnc = 1
      | boot="dc"
      $ xl create /etc/xen/ubuntu-hvm.cfg
      $ vncviewer localhost:0
      $ vi /etc/xen/ubuntu-hvm.cfg
      | # disk = ['phy:/dev/<VG>/ubuntu-hvm,hda,w',\
      |           'file:/root/ubuntu-12.04-desktop-amd64.iso,hdc:cdrom,r']
      | disk = ['phy:/dev/<VG>/ubuntu-hvm,hda,w']

      ---------------------------------------------------------------------------------------
      Xen & Libvirt, which contains support for Xen, both xend and libxl
      $ sudo apt-get install virtinst
      $ sudo virt-install --connect=xen:/// --name u14.04 --ram 1024 \
      --disk u14.04.img,size=4 --location \
      http://ftp.ubuntu.com/ubuntu/dists/trusty/main/installer-amd64/

      ---------------------------------------------------------------------------------------
      Configuration of the xend (Xen daemon), to enable live migration, here must be made
      $ vi /etc/xen/xend-config.sxp
      | (xend-relocation-server yes)
      | (xend-relocation-port 8002)
      | (xend-relocation-address '')
      | (xend-relocation-hosts-allow '')

      - xm
      Status of Guest System  ---------------------------------------------------------------
      $ xm list
      | Name         ID   Mem VCPUs      State   Time(s)
      | Domain-0      0   389     1     r-----   1414.9
      | XenFed            305     1               349.9
      | myFedoraXen       300     1                 0.0
      | myXenGuest    6   300     1     -b----     10.6
      # explanation of state
      | r running  - running and healthy
      | b blocked  - blocked, and not running. This can be caused because domain is waiting
      | 	       	   on IO or has gone to sleep because there was nothing else for it to do.
      | p paused   - paused, typically as a result of the administrator running the xm pause
      |              When in a paused state the domain will still consume allocated resources
      |	           like memory, but will not be eligible for scheduling by Xen hypervisor.
      | s shutdown - requested to be shutdown, rebooted or suspended, and the domain is in
      |              the process of being destroyed in response.
      | c crashed  - crashed. Usually this state can only occur if the domain has been
      | 	           configured not to restart on crash.
      | d dying    - in process of dying, but hasn't completely shutdown or crashed.

      Start and Stop a Xen Guest System -----------------------------------------------------
      $ su -
      $ xm start myXenGuest
      $ xm shutdown myXenGuest
      $ xm destroy myXenGuest
      $ xm pause myXenGuest
      $ xm resume myXenGuest
      $ xm suspend myXenGuest
      $ xm save myXenGuest path_to_save
      $ xm restore myXenGuest path_to_save
      $ xm reboot myXenGuest
      $ xm mem-set myXenGuest 256

      Migrating a Domain to a Different Host ------------------------------------------------
      $ xm migrate -l domainName 192.168.1.1
      -l      --live            use live migration
      -p=num  --port=num        use specified port for migration
      -r=MBIT --resource=MBIT   set level of resource usage for migration

      Connecting to a Running Xen Guest System ----------------------------------------------
      $ vncviewer

      - xl
      configuration files are xl.cfg & xl.conf
      Quick XL Console Tips
      $ xl console ubuntu1  	   : Connect to the VM console
      $ Ctrl+]                   : Disconnect from the console
      $ xl vncviewer		       : Attach to domain's VNC server
      $ xl block-list            : displays all virtual block devices attached to a VM.
      $ xl list --help           : displays all options available to the list
      $ xl list            	   : information about one hypervisor(dom0) or more
      $ xl info		       : information about the Xen host in name, value format
      $ xl dmesg  		   : debugging xen information via dmesg
      $ xl pause 		       : pause execution of a domain
      $ xl unpause 		   : unpause a paused domain
      $ xl cd-insert 		   : insert a cdrom into a guest's cd drive
      $ xl cd-eject 		   : eject a cdrom from a guest's cd drive
      $ xl mem-set 	 	   : set the current memory usage for a domain

      have a look
      http://wiki.christophchamp.com/index.php?title=Xl_(XenServer)

      - xenstore
      an information shared between domains maintained
      You can dump the contents of the xenstore using the xenstore-ls tool.
      $ xenstore-ls
      $ xenstore-ls -f

      xenstore-ls               Displays the full dump of the XenStore database.
      xenstore-read entry       Displays the value of the specified XenStore entry.
      xenstore-exists xpath     Reports whether the specified XenStore path exists.
      xenstore-list xpath       Displays all the children entries of the specified path.
      xenstore-write entry      Updates the value of the specified XenStore entry.
      xenstore-rm path          Removes the specified XenStore entry or directory.
      xenstore-chmod path mode  Updates the read/write permission on the specified path.
      xenstore-control          Sends a command to the xenstored back-end,
      such as triggering an integrity check.

      - xentop
      It displays real-time information about a XenServer system and running domains.
      $ xentop
      -d --delay=SECONDS 	   :Seconds between updates (default 3)
      -n --networks		   :Show network information
      -x --vbds		   :Show vbd block device data
      -r --repeat-header	   :Repeat table header before each domain
      -v --vcpus		   :Show VCPU data
      -b --batch		   :Redirect output data to stdout (batch mode)
      -i --iterations=ITERATIONS :Max. # of updates that xentop should produce before ending

      Interactive commands
      D  	 	 delay between updates
      N  	 	 Toggle display of network information
      Q, Esc   Quit
      R  	 	 Toggle table header before each domain
      S   	 Cycle sort order
      V   	 Toggle display of VCPU information
      Arrows   Scroll domain display

      - Autostart of xen
      1. Chkconfig configuration for xend and xendomains
      $ chkconfig --list | grep -i xen
      $ chkconfig xend on; chkconfig xendomains on.

      2. Use ln -s to link non-xml file in the auto directory of xen.
      ln -s /etc/xen/vm/myvm1 /etc/xen/auto/myvm1

      - Xen senario
      Xen installed and is working fine. Few VMs are started
      Now turn off xend daemon by issuing /etc/init.d/xend stop.
      What happen to the running VMs? They will continue running, still.
      What happen DomO and Xen-hypervisor? Thwy will continue working, still
      But xm command will stop woking!



<br/><a name="kvm"></a>

### KVM

    ===============================================================================================
    330.3 KVM
    ===============================================================================================
      - KVM
      KVM (Kernel-based Virtual Machine) is a full virtualization solution for Linux on x86
      hardware containing virtualization extensions (Intel VT or AMD-V). It consists of
      a loadable kernel module, kvm.ko, that provides the core virtualization infrastructure
      and a processor specific module, kvm-intel.ko or kvm-amd.ko. KVM also requires a
      modified QEMU although work is underway to get the required changes upstream.
      Using KVM, one can run multiple virtual machines running unmodified Linux or Windows
      images. Each virtual machine has private virtualized hardware: a network card, disk,
      graphics adapter, etc.

      Kernel modules: kvm, kvm-intel(kvm_intel.ko or kvm-intel.ko) and kvm-amd

      Start intel case
      $ modprobe kvm
      $ modprobe kvm_intel

      Start amd case
      $ modprobe kvm
      $ modprobe kvm_amd

      While the rest of this documentation focuses on using KVM through libvirt, it is also
      possible to work with KVM directly. This is not the recommended way due to it being
      cumbersome but can be very useful at times.

      /etc/kvm/    : Location for storing vm configuration and scripts.
      /dev/kvm     : Location of kvm running device

      Create a virtual machine from the command line.
      $ kvm -m 512 -hda disk.img -cdrom ubuntu.iso -boot d -smp 2
      $ kvm -smp 2 -m 4096 -daemonize -boot d
      -hda /home/donghee/kvm/test_centOS.img
      -cdrom /media/donghee/Data/program/CentOS-7-x86_64-DVD-1511.iso
      $ kvm -m 4096 -hda /home/donghee/kvm/test_centOS.img
      - m   = memory (in MB) (via default memory management of host OS kernel)
      - hda = first hard drive
      you can use a number of image file types, You can also boot a hard drive.
      Be careful with this option as you do not want to boot the host root partition
      - Syntax: -hda /dev/sda
      - This will call your grub menu from your MBR when you boot kvm.
      - cdrom can be an iso image or a CD/DVD drive.
      - boot [a|c|d|n] boot on floppy (a), hard disk (c), CD-ROM (d), or network (n)
      - smp = number of CPU allow maxinum 16
      - alt-grab change Ctrl-Alt mouse grab combination for Ctrl-Alt-Shift (very practical
      if you often use some control key combinations like Ctrl-Alt-Del or Windows-E)

      Creating a virtual machine by qemu-kvm
      $ qemu-kvm -hda win2k.img -cdrom win2k.iso -m 512 -boot d

      Creating a new Guest VM using virt-install
      $ virt-install -n myRHELVM1 --description "RHEL 6" --os-type=Linux --os-variant=rhel6 \
      --ram=2048 --vcpus=2 --disk path=/var/lib/libvirt/myRHELVM1.img,bus=virtio,size=10 \
      --graphics none --cdrom /var/rhel-server-6.5-x86_64-dvd.iso --network bridge:br0

      - kvm_stat
      kvm_stat command is a python script which retrieves runtime statistics from the kvm
      kernel module. The kvm_stat command can be used to diagnose guest behavior visible
      to kvm. In particular, performance related issues with guests. Currently, the reported
      statistics are for the entire system; the behavior of all running guests is reported.

      Please ensure the kvm modules are loaded. Mount debugfs if required:
      $ mount -t debugfs debugfs /sys/kernel/debug

      The kvm_stat command outputs statistics for all guests and the host.
      The output is updated until the command is terminated (using Ctrl+ c or the q key).

      $ kvm_stat
      |kvm statistics
      |efer_reload                 94       0
      |exits                  4003074   31272
      |fpu_reload             1313881   10796
      |halt_exits               14050     259
      |halt_wakeup               4496     203
      |host_state_reload	1638354   24893
      |hypercalls                   0       0
      |insn_emulation         1093850    1909
      |insn_emulation_fail          0       0
      |invlpg                   75569       0
      |io_exits               1596984   24509
      |irq_exits                21013     363
      |irq_injections           48039    1222
      |irq_window               24656     870
      |largepages                   0       0
      |mmio_exits               11873       0
      |mmu_cache_miss           42565       8
      |mmu_flooded              14752       0
      |mmu_pde_zapped           58730       0
      |mmu_pte_updated              6       0
      |mmu_pte_write           138795       0
      |mmu_recycled                 0       0
      |mmu_shadow_zapped        40358       0
      |mmu_unsync                 793       0
      |nmi_injections               0       0
      |nmi_window                   0       0
      |pf_fixed                697731    3150
      |pf_guest                279349       0
      |remote_tlb_flush             5       0
      |request_irq                  0       0
      |signal_exits                 1       0
      |tlb_flush               200190       0

      Explanation of variables:
      |efer_reload         : Number of Extended Feature Enable Register (EFER) reloads.
      |exits               : Count of all VMEXIT calls.
      |fpu_reload          : Number of times a VMENTRY reloaded the FPU state. this is
      |		       		   incremented when a guest is using the Floating Point Unit (FPU).
      |halt_exits          : Number of guest exits due to halt calls.
      |		               This type of exit is usually seen when a guest is idle.
      |halt_wakeup         : Number of wakeups from a halt.
      |host_state_reload   : Count of full reloads of the host state (currently tallies MSR
      |		               setup and guest MSR reads).
      |hypercalls          : Number of guest hypervisor service calls.
      |insn_emulation      : Number of guest instructions emulated by the host.
      |insn_emulation_fail : Number of failed insn_emulation attempts.
      |io_exits	     	 : Number of guest exits from I/O port accesses.
      |irq_exits           : Number of guest exits due to external interrupts.
      |irq_injections      : Number of interrupts sent to guests.
      |irq_window          : Number of guest exits from an outstanding interrupt window.
      |largepages          : Number of large pages currently in use.
      |mmio_exits          : Number of guest exits due to memory mapped I/O (MMIO) accesses.
      |mmu_cache_miss      : Number of KVM MMU shadow pages created.
      |mmu_flooded         : Detection count of excessive write operations to an MMU page.
      |	                   This counts detected write operations not of individual write.
      |mmu_pde_zapped      : Number of page directory entry (PDE) destruction operations.
      |mmu_pte_updated     : Number of page table entry (PTE) destruction operations.
      |mmu_pte_write       : Number of guest page table entry (PTE) write operations.
      |mmu_recycled        : Number of shadow pages that can be reclaimed.
      |mmu_shadow_zapped   : Number of invalidated shadow pages.
      |mmu_unsync          : Number of non-synchronized pages which are not yet unlinked.
      |nmi_injections      : Number of Non-maskable Interrupt (NMI) injections to the guest.
      |nmi_window          : Number of guest exits from (outstanding) Non-maskable Interrupt
      |		       		   (NMI) windows.
      |pf_fixed            : Number of fixed (non-paging) page table entry (PTE) maps.
      |pf_guest            : Number of page faults injected into guests.
      |remote_tlb_flush    : Number of remote (sibling CPU) Translation Lookaside Buffer
      |		       	 	   (TLB) flush requests.
      |request_irq         : Number of guest interrupt window request exits.
      |signal_exits        : Number of guest exits due to pending signals from the host.
      |tlb_flush           : Number of tlb_flush operations performed by the hypervisor.

    - kvm networking
      There are two parts to networking within QEMU:
      - the virtual network device : is provided to the guest (e.g. a PCI network card).
      - the network backend        : interacts with the emulated NIC
      (e.g. puts packets onto the host's network).

      ---------------------------------------------------------------------------------------
      Creating a network backend
      $(qemu) -netdev TYPE,id=NAME, ...
      The id option gives the name by which the virtual network device and the network
      backend are associated with each other. If you want multiple virtual network devices
      inside the guest they each need their own network backend.

      Network backend TYPES
      In most cases, if you don't have any specific networking requirements other than to be
      able to access to a web page from your guest, user networking (slirp) is a good choice.
      However, if you are looking to run any kind of network service or have your guest
      participate in a network in any meaningful way, tap is usually the best choice.

      • User Networking (SLIRP)
      This is the default networking backend and generally is the easiest to use. It does
      not require root / Administrator privileges. It has the following limitations:
      - there is a lot of overhead so the performance is poor
      - ICMP traffic does not work (so you cannot use ping within a guest)
      - the guest is not directly accessible from the host or the external network
      slirp provides a full TCP/IP stack within QEMU and uses that stack to implement
      a virtual NAT'd network.

      To change the network configuration to use 192.168.76.0/24 instead of the default
      (10.0.2.0/24) and will start guest DHCP allocation from 9 (instead of 15):
      $(qemu) -netdev user,id=mynet0,net=192.168.76.0/24,dhcpstart=192.168.76.9

      You can isolate the guest from the host (and broader network) using the restrict option
      "-netdev user,id=mynet0,restrict=y" or "-netdev type=user,id=mynet0,restrict=yes"
      will restrict networking to just the guest and any virtual devices. This can be used to
      prevent software running inside the guest from phoning home while still providing a
      network inside the guest. You can selectively override this using hostfwd and guestfwd.
      $(qemu) -netdev user,id=mynet0,dns=xxx
      $(qemu) -netdev user,id=mynet0,tftp=xxx,bootfile=yyy
      $(qemu) -netdev user,id=mynet0,smb=xxx,smbserver=yyy
      $(qemu) -netdev user,id=mynet0,hostfwd=hostip:hostport-guestip:guestport
      $(qemu) -netdev user,id=mynet0,guestfwd=
      $(qemu) -netdev user,id=mynet0,host=xxx,hostname=yyy

      • Tap
      The tap networking backend makes use of a tap networking device in the host. It offers
      very good performance and can be configured to create virtually any type of network
      topology. Unfortunately, it requires configuration of that network topology in the host
      which tends to be different depending on the operating system you are using. Generally
      speaking, it also requires that you have root privileges.
      $(qemu) -netdev tap,id=mynet0

      • VDE
      The VDE networking backend uses the Virtual Distributed Ethernet(VDE) infrastructure
      to network guests. Unless you specifically know that you want to use VDE, it is
      probably not the right backend to use.

      • Socket
      The socket networking backend, together with QEMU VLANs, allow you to create a network
      of guests that can see each other. It's primarily useful in extending the network
      created by Documentation/Networking/Slirp to multiple virtual machines. In general,
      if you want to have multiple guests communicate, tap is a better choice unless you do
      not have root access to the host environment.
      $(qemu) -netdev socket,id=mynet0

      ---------------------------------------------------------------------------------------
      Creating a virtual network device
      The virtual network device that you choose depends on your needs and guest environment
      (i.e. the hardware that you are emulating). For example, if you are emulating a
      particular embedded board, then you should use the virtual network device that matches
      that embedded board's configuration.

      On machines that have PCI bus, there are a wider range of options. The e1000 is default
      network adapter in qemu. The rtl8139 is the default network adapter in qemu-kvm.
      In both projects, the virtio-net (para-virtualised) network adapter has the best
      performance, but requires special guest driver support.

      The virtual network device will be associated with this network backend.
      To add a particular virtual network device to your virtual machine:
      $(qemu) -device TYPE,netdev=NAME
      $(qemu) -device DEVNAME,netdev=NET-ID,macaddr=MACADDR,DEV-OPTS,
      - DEVNAME  : device (e.g. i82559c for an Intel i82559C Ethernet device),
      - NET_ID   : network identifier to attach the device
      - MACADDR  : MAC address for the device, and
      - DEV-OPTS : any additional device options that you may wish to pass if supported
      (bus=PCI-BUS,addr=DEVFN to control the PCI device address)

      $(qemu) -device \?   : to get a list of the devices (including network devices)

      ---------------------------------------------------------------------------------------
      Monitoring Networking
      monitor the network configuration using 'info network' and 'info usernet' commands.
      You can capture network traffic from within qemu using the '-net dump' command option.

      The legacy option
      QEMU previously used the   -net nic     instead of   -device DEVNAME
      and   -net TYPE    instead of   -netdev TYPE.
      This is considered obsolete since QEMU 0.12, although it continues to work.
      The legacy syntax to create virtual network devices is:
      $(qemu) -net nic,model=MODEL

      You can use -net nic,model=? to get a list of valid network devices that you can pass
      to the -net nic option. Note that these model names are different from the -device ?
      names and are therefore only useful if you are using the -net nic,model=MODEL syntax.
      [If you'd like to know all of the virtual network devices that are currently provided
      in QEMU, a search for “NetClientInfo” in the source code may be useful.]

      QEMU “VLANs”
      The obsolete -net syntax automatically created an emulated hub (called a QEMU “VLAN”,
      for virtual LAN) that forwards traffic from any device connected to it to every other
      device on the “VLAN”. It is not an 802.1q VLAN, just an isolated network segment.
      When creating multiple network devices using the -net syntax, you generally want to
      specify different vlan ids. The exception is when dealing with the socket backend.
      $ qemu -net user,vlan=0 -net nic,vlan=0 -net user,vlan=1 -net nic,vlan=1

    - kvm monitor(QEMU)
      When QEMU is running, it provides a monitor console for interacting with QEMU.
      Through various commands, the monitor allows you to inspect the running guest OS,
      change removable media and USB devices, take screenshots and audio grabs, and control
      various aspects of the virtual machine.

      The monitor is accessed from within QEMU by holding down the Ctrl+Alt+2.
      Once in the monitor, Ctrl+Alt+1 switches back to the guest OS.
      Typing help or ? in the monitor brings up a list of all commands. Alternatively the
      monitor can be redirected to using the -monitor <dev> command line option
      Using -monitor stdio will send the monitor to the standard output, this is most useful
      when using qemu on the command line.

      $(qemu) help info
      | block      – block devices such as hard drives, floppy drives, cdrom
      | blockstats – read and write statistics on block devices
      | capture    – active capturing (audio grabs)
      | history    – console command history
      | irq        – statistics on interrupts (if compiled into QEMU)
      | jit        – statistics on QEMU's Just In Time compiler
      | kqemu      – whether the kqemu kernel module is being utilised
      | mem        – list the active virtual memory mappings
      | mice       – mouse on the guest that is receiving events
      | network    – network devices and VLANs
      | pci        – PCI devices being emulated
      | pcmcia     – PCMCIA card devices
      | pic        – state of i8259 (PIC)
      | profile    – info on the internal profiler, if compiled into QEMU
      | registers  – the CPU registers
      | snapshots  – list the VM snapshots
      | tlb        – list the TLB (Translation Lookaside Buffer),
      |              i.e. mappings between physical memory and virtual memory
      | usb        – USB devices on the virtual USB hub
      | usbhost    – USB devices on the host OS
      | uuid       – Unique id of the VM
      | version    – QEMU version number
      | vnc        – VNC information

      $(qemu) info block      : block devices such as hard drives, floppy drives, cdrom
      $(qemu) change ide1-cd0 /path/to/my.iso       : to change the disc in a CD or DVD drive
      $(qemu) change ide1-cd0 /dev/sr0 host_device  : or witch between different .iso files
      $(qemu) eject -f device : to release device connected to the removable media specified
      $(qemu) usb_add disk:/tmp/disk.usb : dd if=/dev/zero of=/tmp/disk.usb bs=1024k count=32

      $(qemu) info usb                   : you should find device number
      $(qemu) usb_del 0.2                : apply device number found above
      $(qemu) screendump filename  	   : screen and audio grabs sve into a ppm image file
      $(qemu) commit device/all          : commit changes to the device, or all devices.
      $(qemu) quit			   		   : quit or q Quit QEMU immediately

      $(qemu) info snapshot              : you should find device number
      $(qemu) savevm  name	    	   : Save the virtual machine for qcow2
      $(qemu) loadvm	name	    	   : Load the virtual machine
      $(qemu) stop 		   			   : Suspend execution of VM
      $(qemu) cont 		    	: Reverse a previous stop command - resume execution of VM
      $(qemu) system_reset	    : Filesystems may be left in an unclean state.
      $(qemu) system_powerdown    : VM will get an ACPI shutdown request and usually shutdown
      $(qemu) log option	    :
      $(qemu) logfile filename    : Write logs to file instead of default path /tmp/qemu.log
      $(qemu) print 16            : the result will be printed in hexadecimal

      How to display memory at the specified virtual/physical address,
      $(qemu) x /format address   : displays memory at the virtual
      $(qemu) xp /format address  : displays memory at the physical
      | /format          : is used to specify the output format the displayed memory.
      |                    it is broken down as /[count][data_format][size]
      | -count           : number of item to display (base 10)
      | -data_format     : 'x'=hex, 'd'=decimal, 'u'=unsigned decimal, 'o'=octal
      |                    'c'=char, 'i'=(disassembled) processor instructions
      | -size            : 'b'=8 bits, 'h'=16 bits, 'w'=32 bits, 'g'=64 bits.
      | address          : either direct or register
      | - Direct address : 0×20000
      | - Register       : $eip
      $(qemu) xp /3i $eip   : display 3 instructions on x86 processor starting at the
      current instruction
      $(qemu) xp /20wx $esp : display the last 20 words on the stack for an x86 processor

      Starts a remote debugger session for the GNU debugger (gdb).
      $(qemu) gdbserver
      To connect to it from the host machine, run the following commands:
      $ gdb qemuKernelFile
      $(gdb) target remote localhost:1234

      how to launch a Linux kernel inside QEMU without having to make a full bootable image.
      It is very useful for fast Linux kernel testing.
      $ qemu -kernel arch/i386/boot/bzImage -hda root-2.4.20.img -append "root=/dev/hda"

    - kvm storage
      Host storage:
      Flat files (img, iso), also over NFS
      CD-ROM host device (/dev/cdrom)
      Block devices (/dev/sda3, LVM volumes, iSCSI LUNs)
      Distributed storage (Sheepdog, Ceph)

      Supported image formats:
      QCOW2, QED – QEMU                <---- qcow2 is best choice for kvm storage
      VMDK       – VMware
      VHD        – Microsoft
      VDI        – VirtualBox
      VPC 	     – Virtual PC

      Features that various image formats provide:
      Sparse images, Backing files (delta images), Encryption, Compression, Snapshots

    - QEMU
      QEMU is a generic machine emulator and virtualizer. It also can be used together
      with KVM in order to run virtual machines at near-native speed
      (requiring hardware virtualization extensions on x86 machines)
      QEMU is a hosted virtual machine monitor as a KVM monitor

      Emulation:
      For cross-compilation, development environments
      Android Emulator, shipping in an Android. SDK near you

      Virtualization:
      KVM and Xen use QEMU device emulation

      KVM Hosting
      Here QEMU deals with the setting up and migration of KVM images. It is still involved
      in the emulation of hardware, but the execution of the guest is done by KVM as
      requested by QEMU.

      Xen Hosting
      QEMU is involved only in the emulation of hardware; the execution of the guest is done
      within Xen and is totally hidden from QEMU.

      - QEMU as a controller of kvm storage
      $ qemu -drive
      $   if=ide|virtio|scsi,           		//set storage interface
      $   file=path/to/img,             		//set path to image file or device
      $   cache=writethrough|writeback|none|unsafe    //caching mode

      $ qemu -drive file=install-disc-1.iso,media=cdrom
      $ qemu -drive file=iscsi://user%password@server/xxx.com.example/1
      $ qemu -drive file=ssh://user@server/tmp/file.img
      $ qemu -drive file=http://user:password@server/pub/linux.iso,media=cdrom
      $ qemu -drive file=gluster://1.2.3.4/a.img,file.debug=9

      QEMU supports a wide variety for storage formats and back-ends. Easiest to use are the
      raw and qcow2 formats, but for the best performance it is best to use a raw partition.
      You can create either a logical volume or a partition and assign it to the guest:
      $ qemu -drive file=/dev/mapper/ImagesVolumeGroup-Guest1,cache=none,if=virtio

      QEMU also supports a wide variety of caching modes. If you're using raw volumes or
      partitions, it is best to avoid the cache completely, which reduces data copies and bus
      traffic. For storage interfaces, use the virtio interface:
      $ qemu -drive file=/dev/mapper/ImagesVolumeGroup-Guest1,cache=none,if=virtio

      Virtual FAT filesystem (VVFAT)
      Qemu can emulate a virtual drive with a FAT filesystem. By default it's read-only,
      if you need to make it writable append rw: to the aforementioned prefix.
      $ qemu -drive file=fat:rw:/some/directory

      WARNING:
      1. keep in mind that QEMU makes the virtual FAT table once, when adding the device, and
      then doesn't update it in response to changes to the specified directory made by the
      host system. If you modify the directory while VM is running, QEMU might get confused.
      2. Don't use the linux filesystem btrfs on the host for the image files. It will result
      in low IO performance. The kvm guest may even freeze when high IO traffic on the guest.

      Cache policies
      QEMU can cache access to the disk image files, and it provides several methods to do
      This can be specified using the cache modifier.
      ------------------------------------------------------
      Policy		Description
      unsafe		Like writeback, but without performing an fsync.
      writethrough	Data is written to disk and cache simultaneously. (default)
      writeback	Data is written to disk when discarded from the cache.
      none		Disable caching.
      ------------------------------------------------------
      $ qemu -drive file=disk.img,cache=writeback ...

      Creating an image
      To set up your own guest OS image, you first need to create a blank disc image.
      QEMU has the qemu-img command for creating and manipulating disc images, and supports
      a variety of formats. If you don't tell it what format to use, it will use raw files.
      The “native” format for QEMU is qcow2, and this format offers some flexibility.
      $ qemu-img create -f qcow2 test_centOS.img 10G

      The easiest way to install a guest OS is to create an ISO image of a boot CD/DVD and
      tell QEMU to boot off it. Many free operating systems can be downloaded from the
      Internet as bootable ISO images, and you can use them directly without having to burn
      them to disc. Here we'll boot off an ISO image of a properly licensed Windows XP boot
      disc. We'll also give it 256MB of RAM, but we won't use the kqemu kernel module just
      yet because it causes problems during Windows XP installation.
      $ qemu -m 256 -hda winxp.img -cdrom winxpsp2.iso -boot d

      - Snapshot
      The qcow2 is an acronym for copy on write, a neat trick that allows you to set up an
      image once and use it many times without changing it. This is ideal for developing and
      testing software, which generally requires a known stable environment to start off with
      You can create your known stable environment in one image, and then create several
      disposable copy-on-write images to work in.

      To start a new disposable environment based on a known good image, invoke the qemu-img
      command with the option -b and tell it what image to base its copy on. When you run
      QEMU using the disposable environment, all writes to the virtual disc will go to this
      disposable image, not the base copy.
      $ qemu-img create -f qcow2 -b winxp.img test01.img
      $ qemu -m 256 -hda test01.img -kernel-kqemu &

      The option -b is not supported on qemu-img, instead you can use the option backing_file
      $ qemu-img create -f qcow2 -o backing_file=winxp.img test01.img

      Creating a snapshot, you would run QEMU against snapshot.img. Making any changes to
      its backing file (centos-cleaninstall.img) will corrupt this snapshot image.
      $ qemu-img create -f qcow2 -b centos-cleaninstall.img snapshot.img

      To list the snapshots existing in the disk image
      $ qemu-img snapshot -l vm1.img
      $ qemu-img info Platte-qcow2.img

      Es ist möglich eine virtuelle Maschine mit dem Zustand eines VM-Snapshots zu starten.
      $ qemu -hda Platte.img -loadvm vor_PatchDay

      Einer Befehl speichert einen VM-Snapshot mit dem Namen vor_update auf qcow2-Image.
      $ qemu-img snapshot -c vor_update Platte-qcow2.img

      Den Zustand des angegebenen VM-Snapshot stellt man mit -a Option wieder her
      $ qemu-img snapshot -a vor_update


<br/><a name="other_virtualization"></a>

### Other Virtualization Solutions
    ===============================================================================================
    330.4 Other Virtualization Solutions
    ===============================================================================================
    - Basic knowledge of virtualization provisioning tools

    - OpenVZ
      OpenVZ is not true virtualization but really containerization like FreeBSD Jails.
      Technologies like VMWare and Xen are more flexible in that they virtualize the entire
      machine and can run multiple operating systems, at the expense of greater overhead
      required to handle hardware virtualization. OpenVZ uses a single patched Linux kernel
      and therefore can run only Linux. However because it doesn't have the overhead of a
      true hypervisor, it is very fast and efficient. The disadvantage with this approach is
      the single kernel. All guests must function with the same kernel version that the host
      uses. As OpenVZ employs a single kernel model, it is as scalable as the Linux kernel.
      it supports up to 4096 CPUs and up to 64 GiB of RAM on 32-bit with PAE. A single
      container can scale up to the whole physical system, use all the CPUs and all the RAM.

      The advantages, however, are that memory allocation is soft in that memory not used in
      one virtual environment can be used by others or for disk caching. OpenVZ uses a common
      file system so each virtual environment is just a directory of files that is isolated
      using chroot, newer versions of OpenVZ also allow the container to have its own file
      system. Thus a virtual machine can be cloned by just copying the files in one directory
      to another and creating a config file for the virtual machine and starting it.
      Some fixes such as a kernel update will affect all containers automatically, while
      other changes can simply be “pushed” to all the containers by a simple shell script.

      The OpenVZ kernel is a Linux kernel, modified to add support for OpenVZ containers.
      Modified kernel provides virtualization, isolation, resource management, checkpointing.
      Each container is a separate entity, and behaves largely as a physical server would.
      If needed, any container can be granted access to real devices like network interfaces,
      serial ports, disk partitions, & IPC objects like shared memory, semaphores, messages.

      OpenVZ resource management consists of three components:
      Resources can be changed during container run time, eliminating the need to reboot.
      • Two-level disk quota (user and group)
      • CPU scheduler by each container and standard Linux process priotiry
      • I/O scheduler by each container and available I/O bandwidth according to the priority

      User Beancounters is a set of per-container counters, limits, and guarantees. There is
      a set of about 20 parameters which is meant to control all the aspects of container.
      This is meant to prevent a single container from monopolizing system resources.
      These resources primarily consist of memory and various in-kernel objects such as IPC
      shared memory segments, and network buffers. Each resource can be seen from
      /proc/user_beancounters and has five values associated with it
      1. current usage
      2. maximum usage      : for the lifetime of a container)
      3. barrier            : a soft limit
      4. limit	      : a hard limit
      5. fail counter
      If any resource hits the limit, the fail counter for it is increased. This allows the
      owner to detect problems by monitoring /proc/user_beancounters in the container.

      Checkpointing and live migration
      A live migration and checkpointing feature is possible to move a container from one
      physical server to another without shutting down the container. The process is known as
      checkpointing: a container is frozen and its whole state is saved to a file on disk.
      This file can then be transferred to another machine and a container can be unfrozen
      (restored) there; the delay is roughly a few seconds. Because state is usually
      preserved completely, this pause may appear to be an ordinary computational delay.

      OpenVZ limitation
      OpenVZ restricts access to /dev devices to a small subset. The container may be
      impacted in not having access to devices that are used – not in providing access to
      physical hardware – but in adding or configuring kernel-level features. /dev/loopN is
      often restricted in deployments, as it relies on a limit pool of kernel threads.
      It's absence restricts the ability to mount disk images. OpenVZ is limited to the
      providing only some VPN technologies based on PPP (PPTP/L2TP) & TUN/TAP. IPsec is not
      supported inside containers, including L2TP secured with IPsec.

      Installation
      $ cat << EOF > /etc/apt/sources.list.d/openvz-rhel6.list
      | deb http://download.openvz.org/debian wheezy main
      | # deb http://download.openvz.org/debian wheezy-test main
      | EOF
      $ wget http://ftp.openvz.org/debian/archive.key
      $ apt-key add archive.key
      $ apt-get install linux-image-openvz-amd64
      $ vi sysctl.conf
      |
      | https://www.unixmen.com/install-and-configure-openvz-in-ubuntu/
      |
      $ apt-get install vzctl vzquota ploop vzstats
      $ reboot
      select Advanced options to use openvz in the Grub boot loader menu
      $ vi /etc/vz/vz.conf
      | # NEIGHBOUR_DEVS=detect
      | NEIGHBOUR_DEVS=all

      Creating Containers in OpenVZ
      $ cd /vz/template/cache/
      $ wget http://download.openvz.org/template/precreated/centos-7-x86_64-minimal.tar.gz
      $ vzctl create 101 --ostemplate centos-7-x86_64-minimal
      $ vzctl set 101 --ipadd 192.168.1.103 --save
      $ vzctl set 101 --nameserver 8.8.8.8 --save
      $ vzctl start 101
      $ vzlist -a

      Creating containers using EZ template by vzpkg (tool for managing Virtuozzo templates)
      $ vzpkg install template centos-6-x86
      $ vzpkg list

      Einer der größten Vorteile von OpenVZ ist die einfache Wartung der VEs.
      Running Commands in Container
      $ vzctl exec 103 /etc/init.d/sshd status

    - LXC
      LXC (Linux Containers) is an operating-system-level virtualization for running multiple
      isolated Linux systems (containers) on a control host using a single Linux kernel.
      The Linux kernel provides the cgroups functionality that allows limitation and priority
      of resources (CPU, memory, block I/O, network, etc.) without the need for starting any
      virtual machines, and also namespace isolation functionality that allows complete
      isolation of an applications' view of the operating environment, including networking,
      process trees, user IDs and mounted file systems.

      LXC combines kernel's cgroups and support for isolated namespaces to provide isolated
      environment for applications. Docker can also use LXC as one of its execution drivers,
      enabling image management and providing deployment services.

      LXC       : is a userspace interface for the Linux kernel containment features.
      LXD       : a container hypervisor and a new user experience for LXC (e.g. OpenStack)
      LXCFS     : userspace filesystem designed to work around limitations of Linux kernel
      CGManager : central privileged daemon that manages all cgroups through a D-Bus API

      $ apt-get install lxc
      $ lxc-create -t ubuntu -n cn-01
      $ lxc-start -n cn-01

    - Docker
      Docker provides an additional layer of abstraction and automation of operating-system-
      level virtualization on Linux. Docker uses the resource isolation features of the Linux
      kernel such as cgroups and kernel namespaces, and a union-capable file system such as
      OverlayFS and others to allow independent "containers" to run within a single Linux
      instance, avoiding the overhead of starting and maintaining virtual machines.

      The Linux kernel's support for namespaces mostly isolates an application's view of the
      operating environment, including process trees, network, userIDs and mounted file
      systems, while the kernel's cgroups provide resource limiting, including the CPU,
      memory, block I/O and network. Since version 0.9, Docker includes the libcontainer
      library as its own way to directly use virtualization facilities provided by the Linux
      kernel, in addition to using abstracted virtualization interfaces via libvirt, LXC and
      systemd-nspawn

      Docker Compose : a tool for defining and running multi-container Docker applications.
      you use a Compose file to configure your application’s services.
      Docker Engine  : dockerize an applications, run containers, build Docker images,
      share docker images with others.
      Docker Machine : install, run, provision and manage multiple remote Docker hosts and
      provision Swarm clusters
      Docker Hub     : a cloud-based registry service which allows you to link to repository,
      build your images and test them, stores manually pushed images, and
      links to Docker Cloud so you can deploy images to your hosts.
      Docker Cloud   : build and testing facilities for Dockerized application images, tools
      to set up and manage host, and deploying images to the infrastructure
      Docker Datacenter : Universal Control Plane and Docker Trusted Registry on Linux or AWS

      $ service docker start
      $ docker pull ubuntu:latest
      $ docker run -i -t ubuntu /bin/bash
      $ docker run -d -P training/webapp python app.py

    - packer
      Packer is a tool for creating identical machine images for multiple platforms
      from a single source configuration.
      $ packer validate example.json
      $ packer build example.json

    - vagrant (coreOS)
      Vagrant is a software product for building and maintaining portable virtual development
      The core idea behind its creation lies in the fact that the environment maintenance
      becomes increasingly difficult in a large project with multiple technical stacks.
      Vagrant manages all the necessary configurations for the developers in order to avoid
      the unnecessary maintenance and setup time, and increases development productivity.
      Vagrant is written in the Ruby language but supports in all major languages.
      Puppet and Chef are the two most widely used provisioners in the Vagrant.
      Providers are the services that Vagrant uses to set up and create virtual environments.
      Support for VirtualBox, Hyper-V, and Docker virtualization ships with Vagrant,
      while VMWare and AWS are supported via plugins. Machines are provisioned on top of
      VirtualBox, VMware, AWS, or any other provider. Then, industry-standard provisioning
      tools such as shell scripts, Chef, or Puppet, can be used to automatically install and
      configure software on the machine.

      $ vagrant init hashicorp/precise64
      $ vagrant up

    - VirtualBox
      VirtualBox uses its own format for storage containers – Virtual Disk Image (VDI).
      VirtualBox also supports other well-known storage formatssuch as VMDK (VMware) as well
      as the VHD (Microsoft) format.

      $ VBoxManage startvm ubuservloc --type headless

    - VMware vSphere
      is VMware's cloud computing virtualization platform (formerly VMware Infrastructure).
      utilizing VMware ESX

      $ esxcli
      $ vicfg-*
      $ vifs
      $ vmkfstools
      $ vmware-cmd

    - Virtualization Software
      • Hardware virtualization (hypervisors)
      HyperV, KVM, Xen, oVirt, Parallels, QEMU, libvirt, VMware vSphere, VMware xxx

      • OS-level virtualization
      cgroups-based(CoreOS, Linux VServer, LXC, Docker, OpenVZ), Kubernetes, namespaces

      • Desktop virtualization
      XenApp, XenDesktop, VMware Horizon View

      • Application virtualization
      XenApp, VMware ThinApp

      • Network virtualization
      Open vSwitch


<br/><a name="libvirt"></a>

### Libvirt and Related Tools
    ===============================================================================================
    330.5 Libvirt and Related Tools
    ===============================================================================================
    - libvirt
      is an open source API, daemon and management tool for managing platform virtualization
      It can be used to manage KVM, Xen, VMwareESX, QEMU and other virtualization technology

      libvirt architecture in a simple view (xen,kvm -> libvirt -> virt-manager,virsh,oVirt)

      Graphical Interfaces are provided by Virtual Machine Manager(virt-manager)
      The most popular command line interface is 'virsh', and higher level tools 'oVirt'

      First few steps
      $ sudo apt-get install virtinst libvirt-bin
      $ ps u -C libvirtd
      $ sudo usermod -a -G libvirt myuser1
      $ sudo adduser $USER libvirtd

      Configure libvirtd daemon
      $ vi /etc/libvirt/libvirtd.conf

      When the libvirt default network is running, you will see an isolated bridge device.
      Libvirt will add iptables rules to allow traffic to/from guests attached to the virbr0
      $ brctl show
      | bridge name	bridge id	STP enabled	interfaces
      | virbr0	8000.0000	yes

      Define networks I
      $ virsh -c qemu:///system net-start default
      $ ip address show
      $ virsh -c qemu:///system net-list
      $ echo 1 >> /proc/sys/net/ipv4/ip_forward
      $ iptables -t nat -nvL

      Define networks II
      $ virsh net-define /usr/share/libvirt/networks/default.xml
      $ virsh net-autostart default
      $ virsh net-start default

      Change the configuration of the network, you can check and use
      $ virsh -c qemu:///system net-dumpxml default   <--- show network conf in xml format
      $ virsh -c qemu:///system net-edit default      <--- edit default network setting
      $ virsh -c qemu:///system net-update default    <--- apply modification on the fly!

      Managing storage, for volumes use commands
      $ virsh -c qemu:///system pool-define-as devel dir --target /opt/kvms/pools/devel
      $ virsh -c qemu:///system pool-list --all
      $ virsh -c qemu:///system pool-autostart devel
      $ virsh -c qemu:///system pool-start devel
      $ virsh -c qemu:///system vol-create-as devel volume 8G
      $ virsh -c qemu:///system vol-resize volume 10G --pool devel
      $ virsh -c qemu:///system vol-delete volume --pool devel
      $ virsh -c qemu:///system vol-list --details devel
      $ virsh -c qemu:///system list                 : to list running VMs
      $ virsh -c qemu:///system list --all           : to list available VMs
      $ virsh -c qemu:///system start devel          : to start a virtual machine
      $ virsh -c qemu:///system autostart devel      : similarly, to start a VM at boot
      $ virsh -c qemu:///system reboot devel         : reboot a VM
      $ virsh -c qemu:///system shutdown devel       : to stop VM safely.
      $ virsh -c qemu:///system destroy my-vm        : to stop VM violently.
      $ virsh -c qemu:///system save devel devel-001.state                : save a VM
      $ virsh -c qemu:///system restore devel-001.state                   : restart a VM
      $ virsh -c qemu:///system attach-disk devel /dev/cdrom /media/cdrom : CDROM mount
      $ virsh -c qemu:///system change-media guest01 hdb /pool/disc.iso   : change CD
      $ virsh -c qemu:///system change-media guest01 hdb --eject          : eject CD

      Question, what action perform with "virsh restore"
      Answer, it restarts a VM from a state file!

      Tuning vCPU Pinning with virsh, will pin the vcpu thread ID 4 to physical cpu ID 2
      $ virsh vcpupin rhel7 4 2
      Set virtual machine rhel7 to have 2 virtual CPUs
      $ virsh setvcpus rhel7 2

      Installing a virtual machine (guest OS)
      $ virt-install
      -n debian-testing  : the name of the new virtual machine
      --ram 2048         : give it 2 GB RAM
      --vcpus=2          : give it 2 CPUs
      --cpu=host
      : VM should just be provided the same CPU as physical machine
      -c ./netinst/debian-6.0.7-amd64-netinst.iso
      : VM should be configured to have a "CD-ROM" drive including image
      --os-type=linux
      --os-variant=debiansqueeze
      : are optional, but allow to configure VM with the optimal parameters
      --disk pool=devel,size=2,format=qcow2
      : asks to automatically allocate 2 GB of space from the devel
      -w network=devel
      : VM should be connected to the default network
      -graphics=vnc
      : want to have a vnc window to control the VM

      $ virt-install
      -n web_devel
      -r 256
      : specifies the amount of memory the virtual machine (megabytes).
      --disk path=/var/lib/libvirt/images/web_devel.img,size=4
      : using virtio for the disk bus.
      -c ubuntu-16.04-server-i386.iso
      : file to be used as a virtual CDROM.
      --network network=default,model=virtio
      : provides details related to the VM's network interface.
      --graphics vnc,listen=0.0.0.0
      : exports the guest's virtual console using VNC and on all host interfaces.
      --noautoconsole
      : will not automatically connect to the VM's console.
      -v
      : creates a fully virtualized guest.

      Typically servers have no GUI, so another GUI based computer on the Local Area Network
      (LAN) can connect via VNC to complete the installation.

      Converting an existing virtual machine by --import option
      $ cp my-vm.qcow2 /opt/kvms/pools/devel             : copy a image into the pool
      $ virsh -c qemu:///system pool-refresh default     : refresh the pool
      $ virt-install --connect qemu:///system --ram 1024 -n my-vm --os-type=linux \
      --os-variant=debianwheezy --vcpus=1 --vnc --import \
      --disk vol=default/my-vm.qcow2,device=disk,format=qcow2

      Copy one virtual machine to another.
      $ virt-clone --original my-vm --auto-clone     : to make an exact copy of your VM.
      $ virt-clone -o web_devel -n database_devel -f /path/to/database_devel.img \
      --connect=qemu:///system
      |-o: original virtual machine.
      |-n: name of the new virtual machine.
      |-f: path to the file, logical volume, or partition to be used by the new VM.
      |--connect: specifies which hypervisor to connect to.

      Snapshots and check with using qemu-img
      $ virsh snapshot-save my-vm
      $ qemu-img info /opt/kvms/pools/devel/my-vm.qcow2

      Connect to the VNC console of VM form a remote desktop without virt-viewer
      $ ssh rabexc@server -L 5905:localhost:5900
      $ vncviewer :5

      Virt-manager package (a graphical utility)
      $ sudo apt install virt-manager
      To connect to the local libvirt service
      $ virt-manager -c qemu:///system
      You can connect to the libvirt service running on another host
      $ virt-manager -c qemu+ssh://virtnode1.mydomain.com/system

      Screen of VM opened up in a vnc client, connect to a virtual machine's console
      $ virt-viewer my-vm

    - oVirt
      oVirt is high level Tools, typically used in Data Center

      oVirt Engine is a graphical user interface or it is a Web administrator portal from
      where we can manage virtual machines, compute, network and storage resources.

      oVirt Node is either a RHEL Server on which vdsm service is up and running.
      it will act as Hypervisor (KVM) on which all the Virtual machines will be created.

      Installation of oVirt engine on CentOS via engine installer
      $ yum -y update
      $ yum install http://resources.ovirt.org/pub/yum-repo/ovirt-release40.rpm
      $ yum install ovirt-engine -y
      $ engine-setup --generate-answer=/root/answer.txt

      Open the web browser and type the URI, you will see a web administrator portal
      $ https://IP_Address

      Installation of oVirt Node (Hypervisor)
      you can easily create an instance via web administrator portal but before it,
      do download the ovirt-node iso from official site, then use it to intall later.
      Before starting installation of a node, you should upload iso image to storage domain.
      You should now create data storage domain, which will be used for storing VM
      At the end, create oVirt Node from web administrator portal


<br/><a name="management_tools"></a>

### Cloud Management Tools

    ===============================================================================================
    330.6 Cloud Management Tools
    ===============================================================================================
    - OpenStack
      OpenStack is a software platform for cloud computing, mostly deployed as an IaaS.
      (Python)

    - CloudStack
      CloudStack is a cloud computing software for creating, managing, and deploying
      infrastructure cloud services.
      (Java, C)

    - Eucalyptus
      Eucalyptus is a software for building AWS-compatible private and hybrid cloud computing
      environments
      (Java, C)

    - OpenNebula
      OpenNebula is a cloud computing platform for managing heterogeneous distributed data
      center infrastructures.
      (C++, C, Ruby, Java, Shell)



<br/><a name="ha_concept"></a>

### High Availability Concepts and Theory

    ===============================================================================================
    334.1 High Availability Concepts and Theory
    ===============================================================================================
    - Cluster architectures
      Design a cluster architecture consider recovery and reorganization mechanisms,
      and application aspects and operational considerations of high availability

    As you are designing your infrastructure, consider the following forces:
    - Users expect applications to be available and responsive when they use them.
    - Continuous uptime in a production environment, whether it is a database powering a
      critical client/server application or an e-commerce Web site, is becoming a common
      business requirement.
    - There is a potential for high monetary loss when an application fails. For example,
      a high-volume online store bringing in $25,000 per hour goes down due to a single
      server that fails in the infrastructure. The monetary impact can become quite severe
      if the outage lasts for several hours.
    - All systems within an application infrastructure require maintenance. Individual
      systems must be able to accommodate both hardware and software upgrades without
      incurring application downtime. For example, a patch is released to repair a security
      issue associated with a component running on a server that delivers the application.
      If this is the only server, the application will experience downtime. If it is one of
      a series of servers, only the server will experience downtime, not the application.
    - Adding hardware can increase the cost and complexity of the solution. New or more
      capable hardware requires additional development and testing to enable an application
      to take full advantage of the more capable environment. Additional maintenance and
      training costs are also associated with managing a more complex environment.

    - Type of Cluster
      • Active/Passive Cluster
      A single Adaptive Server runs either on the primary node or on the secondary node.
      The Adaptive Server runs on the primary node before a fail over and the secondary
      node after fail over.

      • Active/Active Cluster
      Two Adaptive Servers are configured as companion servers, each with independent
      workloads. These companions run on the primary and secondary nodes, respectively,
      as individual servers until one fails over.

      • Failover Cluster
      failover is switching to a redundant or standby system upon the failure or abnormal
      termination of the previously active application, server, system, or network.

      • Load Balanced Cluster
      Install the service or application onto multiple servers that are configured to
      share the workload. This type of configuration is a load-balanced cluster.

      • Shared-Nothing Cluster
      a distributed computing architecture in which each node is independent and
      self-sufficient, and there is no single point of contention across the system

      • Shared-Disk Cluster
      A Cluster Shared Volume is a shared disk containing an NTFS or ReFS (ReFS: Windows
      Server 2012 R2 or newer) volume that is made accessible for read and write operations
      by all nodes within a Windows Server Failover Cluster.

    - Cluster service and resources
      A resource is a service made highly available by a cluster. The simplest type of
      resource, a primitive resource, is described in this section. More complex forms,
      such as groups and clones, are described in later sections.
      Every primitive resource has a resource agent. A resource agent is an external program
      that abstracts the service it provides and present a consistent view to the cluster.
      This allows the cluster to be agnostic about the resources it manages. The cluster
      doesn’t need to understand how the resource works because it relies on the resource
      agent to do the right thing when given a start, stop or monitor command.
      For this reason, it is crucial that resource agents are well-tested.
      Typically, resource agents come in the form of shell scripts. However, they can be
      written using any technology (such as C, Python).

    - Fencing
      process of isolating a node of a computer cluster or protecting shared resources
      when a node appears to be malfunctioning. Fencing is the isolation of a failed node
      so that it does not cause disruption to a computer cluster.
      There are a variety of fencing techniques available.

      One can either fence nodes - using Node Fencing, or fence resources using Resource
      Fencing. Some types of resources are Self Fencing Resources, and some aren't damaged
      by simultaneous use, and don't require fencing at all.

      Node Fencing is the idea of fencing an entire node out of a cluster at once,
      independently of what kind of resources it might be running. In Pacemaker/CRM,
      Node Fencing is implemented by STONITH.

      Resource Fencing is fencing at resource granularity. It ensures exclusive access to
      a given resource. Common techniques for this include changing the zoning of the node
      from a SAN fiber channel switch (locking the node out of access to its disks) and
      things like SCSI reserve. Resource fencing is implemented differently depending on
      the type of resource and how access to it is granted, and hence can be denied.
      Compared to Node Fencing, where we prevent a failed node from accessing shared
      resources entirely, it has finer granularity, not all resources support this
      functionality, or it might have some limitations that keep it from being useful in a
      particular situation.

    - Quorum
      is a distributed computing, Minimum number of votes that a distributed transaction has
      to obtain in order to be allowed to perform an operation in the distributed system.

      One way to solve the mutual fencing dilemma described above is to somehow select only
      one of these two subclusters to carry on and fence the subclusters it can't communicate
      with. Of course, you have to solve it without communicating with the other subclusters
      - since that's the problem - you can't communicate with them. The idea of quorum
      represents the process of selecting a unique (or distinguished for the mathematically
      inclined) subcluster.

      The most classic solution to selecting a single subcluster is a majority vote. If you
      choose a subcluster with more than half of the members in it, then (barring bugs)
      you know there can't be any other subclusters like this one. So, this is looks like
      a simple and elegant solution to the problem. For many cases, that's true. But, what
      if your cluster only has two nodes in it? Now, if you have a single node fail, then
      you can't do anything - no one has quorum. If this is the case, then two machines have
      no advantage over a single machine - it's not much of an HA cluster. Since 2-node
      HA clusters are by far the most common size of HA cluster, it's kind of an important
      case to handle well. So, how are we going to get out of this problem?

      Quorum variants and improvements
      What you need in this case, is some kind of a 3rd party arbitrator to help select who
      can fence off the other nodes and allow you to bring up resources - safely. To solve
      this problem there is a variety of other methods available to act as this arbitrator -
      either software or hardware. Although there are several methods available to use as
      arbitrator, we'll only talk about one each of hardware and software methods:
      SCSI reserve and Quorum Daemon.

      SCSI reserve
      In hardware, we fall back on our friend SCSI reserve. In this usage, both nodes try
      and reserve a disk partition available to both of them, and the SCSI reserve mechanism
      ensures that only one of the two of them can succeed. SCSI reserve creates its own set
      of problems including it won't work reliably over geographic distances. A disk which
      one uses in this way with SCSI reserve to determine quorum is called a quorum disk.
      Some HA implementations (notably Microsoft's) require a quorum disk.

      Quorum daemon
      In Linux-HA, a quorum daemon purpose is to arbitrate quorum disputes between cluster
      members. One could argue that for the purposes of quorum this is basically SCSI reserve
      implemented in software - and such an analogy is a reasonable one. However,
      since it is designed for only this purpose, it has a number of significant advantages
      over SCSI reserve - one of which is that it can conveniently and reliably operate over
      geographic distances, making it ideal for disaster recovery (DR) type situations.

    - Split brain
      a split-brain typically refers to an error state. A shared storage may experience data
      corruption. If the data storages are kept the data inconsistencies originating
      from the maintenance of two separate data sets with overlap in scope that might require
      operator intervention and cleanup, when a failure condition based on servers not
      communicating and synchronizing their data to each other.

      the split-brain may occur when all of the private links go down simultaneously, but the
      cluster nodes are still running, each one believing they are the only one running.
      The data sets of each cluster may then randomly serve clients by their own data set
      updates, without any coordination with the other data sets.

    - Cluster management, monitoring and diagnostics
      Service Level Agreement (SLA)
      official commitment that prevails between a service provider and the customer

      Mean Time Before(Between) Failure (MTBF)
      MTBF describes the expected time between two failures for a repairable system

      Mean Time To Repair (MTTR)
      MTTF denotes the expected time to failure for a non-repairable system.

      Disaster Recovery Plan (DRP)
      a documented process or set of procedures to recover and protect a IT infrastructure
      in the event of a disaster

      Redundancy 중복검사
      Replication 복사
      Session handling


<br/><a name="load_balancer"></a>

### Load Balanced Clusters

    ===============================================================================================
    334.2 Load Balanced Clusters
    ===============================================================================================
    - Front and Back Ends
      Front End
      between the presentation layer – which is the interface between the user

      Back End
      the data access layer.

    - Bladeserver
      is a stripped-down server computer with a modular design optimized to minimize the
      use of physical space and energy. it is mounted with server-rack.

    - LVS
      LVS (Linux Virtual Server) is a highly scalable and available server built on a cluster
      of real servers, with the load balancer running on the Linux operating system.

    - IPVS
      IPVS (IP Virtual Server) is incorporated into the LVS, where
      it runs on a host and acts as a load balancer in front of a cluster of real servers.
      IPVS implements transport-layer load balancing inside the Linux Kernel, usually called
      Layer-4 switching. IPVS running on a host acts as a load balancer at the front of a
      cluster of real servers, it can direct requests for TCP/UDP based services to the
      real servers, and makes services of the real servers to appear as a virtual service on
      a single IP address.

    - LVS how works?
      One of the advantages of using LVS is its ability to perform flexible, IP-level load
      balancing on the real server pool. This flexibility is due to the variety of scheduling
      algorithms an administrator can choose from when configuring LVS. LVS load balancing
      is superior to less flexible methods, such as Round-Robin DNS where the hierarchical
      nature of DNS and the caching by client machines can lead to load imbalances.
      Additionally, the low-level filtering employed by the LVS router has advantages over
      application-level request forwarding because balancing loads at the network packet
      level causes minimal computational overhead and allows for greater scalability.

      Using scheduling, the active router can take into account the real servers' activity
      and, optionally, an administrator-assigned weight factor when routing service requests.
      Using assigned weights gives arbitrary priorities to individual machines. Using this
      form of scheduling, it is possible to create a group of real servers using a variety
      of hardware and software combinations and the active router can evenly load each real
      server.

      The scheduling mechanism for LVS is provided by a collection of kernel patches called
      IPVS modules. These modules enable Layer 4 transport layer switching, which is designed
      to work well with multiple servers on a single IP address.

      To track and route packets to the real servers efficiently, IPVS builds an IPVS table
      in the kernel. This table is used by the active LVS router to redirect requests from
      a virtual server address to and returning from real servers in the pool. The IPVS table
      is constantly updated by a utility called ipvsadm — adding and removing cluster members
      depending on their availability.

    - LVS components
      The lvs daemon runs on the active LVS router once called by pulse. It reads the
      configuration file /etc/sysconfig/ha/lvs.cf, calls the ipvsadm utility to build and
      maintain the IPVS routing table, and assigns a nanny process for each configured LVS.
      If nanny reports a real server is down, lvs instructs the ipvsadm utility to remove
      the real server from the IPVS routing table.
      • lvs daemon
      • ipvsadm
      • iptables & other linux networking tools

      • pulse       : Through pulse(heartbeat), determines the health of the active router
      • nanny       : monitoring daemon runs on the active LVS router
      • piranha     : monitor an LVS cluster
      • send_arp    : notify network of a new IP address / MAC address mapping

    - Job Scheduling Algorithms in LVS
      Job Scheduling = connection scheduling = Load Balancing algorithms

      • Round-Robin Scheduling
      A round-robin algorithm distributes the load equally to each server, regardless
      of the current number of connections or the response time. Round-robin is suitable
      when the servers in the cluster have equal processing capabilities; otherwise,
      some servers may receive more requests than they can process while others are
      using only part of their resources.

      Distributes each request sequentially around the pool of real servers. Using this
      algorithm, all the real servers are treated as equals without regard to capacity or
      load. This scheduling model resembles round-robin DNS but is more granular due to
      the fact that it is network-connection based and not host-based. LVS round-robin
      scheduling also does not suffer the imbalances caused by cached DNS queries.

      • Weighted Round-Robin Scheduling
      accounts for the different processing capabilities of each server. Administrators
      manually assign a performance weight to each server, and a scheduling sequence is
      automatically generated according to the server weight. Requests are then directed
      to the different servers according to a round-robin scheduling sequence.

      Distributes each request sequentially around the pool of real servers but gives more
      jobs to servers with greater capacity. Capacity is indicated by a user-assigned
      weight factor, which is then adjusted upward or downward by dynamic load information.
      Refer to Section 1.3.2, “Server Weight and Scheduling” for more on weighting real
      servers. Weighted round-robin scheduling is a preferred choice if there are
      significant differences in the capacity of real servers in the pool. However, if the
      request load varies dramatically, the more heavily weighted server may answer more
      than its share of requests.

      • Least-Connection Scheduling
      sends requests to servers in a cluster, based on which server is currently serving
      the fewest connections. Distributes more requests to real servers with fewer active
      connections. Because it keeps track of live connections to the real servers through
      the IPVS table, least-connection is a type of dynamic scheduling algorithm, making
      it a better choice if there is a high degree of variation in the request load. It is
      best suited for a real server pool where each member node has roughly the same
      capacity. If a group of servers have different capabilities, weighted leastc connec-
      tion scheduling is a better choice.

      • Weighted Least-Connection Scheduling (defualt)
      Distributes more requests to servers with fewer active connections relative to their
      capacities. Capacity is indicated by a user-assigned weight, which is then adjusted
      upward or downward by dynamic load information. The addition of weighting makes this
      algorithm ideal when the real server pool contains hardware of varying capacity.
      Refer to Section 1.3.2, “Server Weight and Scheduling” for more on weighting real
      servers.

      • Locality-Based Least-Connection Scheduling
      Distributes more requests to servers with fewer active connections relative to their
      destination IPs. This algorithm is designed for use in a proxy-cache server cluster.
      It routes the packets for an IP address to the server for that address unless that
      server is above its capacity and has a server in its half load, in which case it
      assigns the IP address to the least loaded real server.

      • Locality-Based Least-Connection with Replication Scheduling
      Distributes more requests to servers with fewer active connections relative to their
      destination IPs. This algorithm is also designed for use in a proxy-cache server
      cluster. It differs from Locality-Based Least-Connection Scheduling by mapping the
      target IP address to a subset of real server nodes. Requests are then routed to the
      server in this subset with the lowest number of connections. If all the nodes for
      the destination IP are above capacity, it replicates a new server for that
      destination IP address by adding the real server with the least connections from the
      overall pool of real servers to the subset of real servers for that destination IP.
      The most loaded node is then dropped from the real server subset to prevent
      over-replication.

      • Destination Hashing Scheduling
      Distributes requests to the pool of real servers by looking up the destination IP in
      a static hash table. This is designed for use in a proxy-cache server cluster.

      • Source Hashing Scheduling
      Distributes requests to the pool of real servers by looking up the source IP in a
      static hash table. This algorithm is designed for LVS routers with multiple firewall.

      • Shortest Expected Delay Scheduling
      The shortest expected delay scheduling algorithm assigns network connections to the
      server with the shortest expected delay. The expected delay that the job will
      experience is (Ci + 1) / Ui if sent to the ith server, in which Ci is the number of
      connections on the the ith server and Ui is the fixed service rate (weight) of the
      ith server.

      • Never Queue Scheduling
      The never queue scheduling algorithm adopts a two-speed model. When there is an
      idle server available, the job will be sent to the idle server, instead of waiting
      for a fast one. When there is no idle server available, the job will be sent to the
      server that minimize its expected delay
      (The Shortest Expected Delay scheduling algorithm).

    - Load Balancing algorithms (quick summary)
      http://www.peplink.com/technology/load-balancing-algorithms/
      • Weighted Balance
      • Priority
      • Overflow
      • Persistence
      • Least Used
      • Lowest Latency
      • Enforced

    - LVS Forwarding
      different ways of forwarding packets:
      • Network Address Translation (NAT)
      • Direct Routing
      • Tunneling
      • Local Node
      ipvsadm, iptables(ipchains), arptables are used for the configuration in RedHat

    - Network Address Translation (NAT):
      A method of manipulating the source and/or destination port and/or address of a packet.
      The most common use of this is IP masquerading which is often used to enable private
      networks to access the Internet. In the context of layer 4 switching, packets are
      received from end users and the destination port and IP address are changed to that
      of the chosen real server. Return packets pass through the linux director at which time
      the mapping is undone so the end user sees replies from the expected source.

      Due to the shortage of IP address in IPv4 and some security reasons, more and more
      networks use internal IP addresses (such as 10.0.0.0/255.0.0.0, 172.16.0.0/255.240.0.0
      and 192.168.0.0/255.255.0.0) which cannot be used in the Internet. The need for network
      address translation arises when hosts in internal networks want to access the Internet.

      NAT is a feature by which IP addresses are mapped from one group to another. When the
      address mapping is N-to-N, it is called static NAT; when the mapping is M-to-N (M>N),
      it is called dynamic NAT. Network address port translation is an extension to basic
      NAT, in that many network addresses and their TCP/UDP ports are translated to a single
      network address and its TCP/UDP ports. This is N-to-1 mapping, in which way Linux IP
      Masquerading was implemented. Virtual server via NAT on Linux is done by network
      address port translation. The code is implemented on Linux IP Masquerading codes, and
      some of Steven Clarke's port forwarding codes are reused.

      ---------------------------------------------------------------------------------------
      Legacy 1. How to use it (see figure)
      ipfwadm is used to make the virtual server accept packets from real servers
      $ echo 1 > /proc/sys/net/ipv4/ip_forward
      $ ipfwadm -F -a m -S 172.16.0.0/24 -D 0.0.0.0/0

      The following table illustrates rules specified in the Linux box with virtual server
      |Protoc. Virtual IP 	Port Real IP    Port	Weight
      |TCP	 202.103.106.5	80   172.16.0.2 80	    1
      |		                     172.16.0.3 8000    2
      |TCP	 202.103.106.5	21   172.16.0.3 21	    1

      All traffic destined for IP address 202.103.106.5 Port 80 is load-balanced over real
      IP address 172.16.0.2 Port 80 and 172.16.0.3 Port 8000. Traffic destined for IP
      202.103.106.5 Port 21 is port-forwarded to real IP address 172.16.0.3 Port 21.

      Packet rewriting works as follows.
      The incoming packet for web service would has source and destination addresses as:
      | SOURCE 202.100.1.2:3456 DEST 202.103.106.5:80
      The load balancer will choose a real server, e.g. 172.16.0.3:8000.
      The packet would be rewritten and forwarded to the server as:
      | SOURCE 202.100.1.2:3456 DEST 172.16.0.3:8000
      Replies get back to the load balancer as:
      | SOURCE 172.16.0.3:8000 DEST 202.100.1.2:3456
      The packets would be written back to the virtual server address and returned to the
      client as:
      | SOURCE 202.103.106.5:80 DEST 202.100.1.2:3456

      - Tunnelling (TUN):
      IP encapsulation allows packets addressed to an IP address to be redirected to
      another address, possibly on a different network. In the context of layer 4 switching
      the behaviour is very similar to that of direct routing, except that when packets are
      forwarded they are encapsulated in an IP packet, rather than just manipulating the
      ethernet frame. The main advantage of using tunnelling is that real servers can be on
      a different networks.

      Tunneling is a technique to encapsulate IP datagram within IP datagrams, which allows
      datagrams destined for one IP to be wrapped and redirected to another IP address.
      IP encapsulation is now commonly used in Extranet, Mobile-IP, IP-Multicast, tunneled
      host or network.

      ---------------------------------------------------------------------------------------
      Legacy 1. How to use it (see figure)
      The following table illustrates rules specified in the Linux box with virtual server
      | Protocol  Virtual IP 	   Port  Real IP    	Weight
      | TCP	    202.103.106.5  80    202.103.107.2	1
      |			202.103.106.3	2
      Note that the services running on the real servers must run on the same port as virtual
      service, so it is not necessary to specify the service port on the real servers.

      All traffic destined for IP address 202.103.106.5 Port 80 is load-balanced over real
      IP address 202.103.107.2 Port 80 and 202.103.106.3 Port 80.

      We can use the following commands to specify the rules in the table above in the system.
      $ ipvsadm -A -t 202.103.106.5:80 -s wlc
      $ ipvsadm -a -t 202.103.106.5:80 -r 202.103.107.2 -i -w 1
      $ ipvsadm -a -t 202.103.106.5:80 -r 202.103.106.3 -i -w 2

      The configuration is as follows. The load balancer has 172.26.20.111 address, and the
      real server 172.26.20.112. The 172.26.20.110 is the virtual IP address.
      In the following examples, "telnet 172.26.20.110” will actually reach the real server.

      The load balancer (LinuxDirector), kernel 2.2.14
      $ ifconfig eth0 172.26.20.111 netmask 255.255.255.0 broadcast 172.26.20.255 up
      $ ifconfig eth0:0 172.26.20.110 netmask 255.255.255.255 broadcast 172.26.20.110 up
      $ echo 1 > /proc/sys/net/ipv4/ip_forward
      $ ipvsadm -A -t 172.26.20.110:23 -s wlc
      $ ipvsadm -a -t 172.26.20.110:23 -r 172.26.20.112 -i

      The real server 1, kernel 2.0.36 (IP forwarding enabled)
      $ ifconfig eth0 172.26.20.112 netmask 255.255.255.0 broadcast 172.26.20.255 up
      $ route add -net 172.26.20.0 netmask 255.255.255.0 dev eth0
      $ ifconfig tunl0 172.26.20.110 netmask 255.255.255.255 broadcast 172.26.20.110 up
      $ route add -host 172.26.20.110 dev tunl0

      ---------------------------------------------------------------------------------------
      Legacy 2. Real server running kernel 2.2.14 or later with hidden device
      The load balancer (LinuxDirector), kernel 2.2.14
      $ echo 1 > /proc/sys/net/ipv4/ip_forward
      $ ipvsadm -A -t 172.26.20.110:23 -s wlc
      $ ipvsadm -a -t 172.26.20.110:23 -r 172.26.20.112 -i

      The real server 1, kernel 2.2.14
      $ echo 1 > /proc/sys/net/ipv4/ip_forward
      $ modprobe ipip
      $ ifconfig tunl0 0.0.0.0 up
      $ echo 1 > /proc/sys/net/ipv4/conf/all/hidden
      $ echo 1 > /proc/sys/net/ipv4/conf/tunl0/hidden
      $ ifconfig tunl0 172.26.20.110 netmask 255.255.255.255 broadcast 172.26.20.110 up

      Since the kernel 2.2 just has one tunnel device tunl0, you can only have one VIP in
      this configuration. For multiple VIPs, you can make the tunl0 device up, and configure
      them on aliases of tunnel/dummy/loopback devices and hide that device.
      $ echo 1 > /proc/sys/net/ipv4/ip_forward
      $ modprobe ipip
      $ ifconfig tunl0 0.0.0.0 up
      $ ifconfig dummy0 0.0.0.0 up
      $ echo 1 > /proc/sys/net/ipv4/conf/all/hidden
      $ echo 1 > /proc/sys/net/ipv4/conf/dummy0/hidden
      $ ifconfig dummy0:0 172.26.20.110 up
      $ route add -host 172.26.20.110 dev dummy0:0
      $ ifconfig dummy0:1 <Another-VIP> up

      ---------------------------------------------------------------------------------------
      Legacy 3. Real servers runing kernel 2.2.x with redirect approach
      The load balancer's configuration is the same as the example above.
      Real servers running kernel 2.2.x can be configured as follows:
      $ echo 1 > /proc/sys/net/ipv4/ip_forward
      $ modprobe ipip
      $ ifconfig tunl0 0.0.0.0 up
      $ ipchains -A input -j REDIRECT 23 -d 172.26.20.110 23 -p tcp

      - Direct Routing (DR):
      Packets from end users are forwarded directly to the real server.
      The IP packet is not modified, so the real servers must be configured to accept
      traffic for the virtual server's IP address. This can be done using a dummy interface
      or packet filtering to redirect traffic addressed to the virtual server's IP address
      to a local port. The real server may send replies directly back to the end user.
      Thus, the linux director does not need to be in the return path.

      This request dispatching approach is similar to the one implemented in IBM's
      NetDispatcher. The virtual IP address is shared by real servers and the load balancer.
      The load balancer has an interface configured with the virtual IP address too, which is
      used to accept request packets, and it directly route the packets to the chosen servers
      All the real servers have their non-arp(address resolution protocol) alias interface
      configured with the virtual IP address or redirect packets destined for the virtual IP
      address to a local socket, so that the real servers can process the packets locally.

      ---------------------------------------------------------------------------------------
      Legacy 1. testing virtual server via direct routing.
      The load balancer has 172.26.20.111 address, and the real server 172.26.20.112.
      The 172.26.20.110 is the virtual IP address. In the following examples,
      “telnet 172.26.20.110” will actually reach the real server.

      The load balancer (LinuxDirector), for kernel 2.2.14
      $ ifconfig eth0 172.26.20.111 netmask 255.255.255.0 broadcast 172.26.20.255 up
      $ ifconfig eth0:0 172.26.20.110 netmask 255.255.255.255 broadcast 172.26.20.110 up
      $ echo 1 > /proc/sys/net/ipv4/ip_forward
      $ ipvsadm -A -t 172.26.20.110:23 -s wlc
      $ ipvsadm -a -t 172.26.20.110:23 -r 172.26.20.112 -g

      The real server 1, for kernel 2.0.36 (IP forwarding enabled)
      $ ifconfig eth0 172.26.20.112 netmask 255.255.255.0 broadcast 172.26.20.255 up
      $ route add -net 172.26.20.0 netmask 255.255.255.0 dev eth0
      $ ifconfig lo:0 172.26.20.110 netmask 255.255.255.255 broadcast 172.26.20.110 up
      $ route add -host 172.26.20.110 dev lo:0

      ---------------------------------------------------------------------------------------
      Legacy 2. Real server running kernel 2.2.14 or later with hidden device
      The load balancer (LinuxDirector), kernel 2.2.14
      $ echo 1 > /proc/sys/net/ipv4/ip_forward
      $ ipvsadm -A -t 172.26.20.110:23 -s wlc
      $ ipvsadm -a -t 172.26.20.110:23 -r 172.26.20.112 -g

      The real server 1, kernel 2.2.14
      $ echo 1 > /proc/sys/net/ipv4/ip_forward
      $ echo 1 > /proc/sys/net/ipv4/conf/all/hidden
      $ echo 1 > /proc/sys/net/ipv4/conf/lo/hidden
      $ ifconfig lo:0 172.26.20.110 netmask 255.255.255.255 broadcast 172.26.20.110 up

      You can configure the VIP on alias of other devices like dummy and hide it.
      Since it is the alias interface, you can configure as many VIPs as you want.
      An example using dummy device is as follows:
      $ echo 1 > /proc/sys/net/ipv4/ip_forward
      $ ifconfig dummy0 0.0.0.0 up
      $ echo 1 > /proc/sys/net/ipv4/conf/all/hidden
      $ echo 1 > /proc/sys/net/ipv4/conf/dummy0/hidden
      $ ifconfig dummy0:0 172.26.20.110 up
      $ ifconfig dummy0:1 <Another-VIP> up

      ---------------------------------------------------------------------------------------
      Legacy 3. Real servers runing kernel 2.2.x with redirect approach
      The load balancer's configuration is the same as the example above. Real servers
      running kernel 2.2.x can be configured as follows:
      $ echo 1 > /proc/sys/net/ipv4/ip_forward
      $ ipchains -A input -j REDIRECT 23 -d 172.26.20.110 23 -p tcp
      $ ...
      With this ipchains redirect commands, packets destined for the address 172.26.20.110
      port 23 and the tcp protocol will be redirected to a local socket. Note that the
      service daemon must listen on all addresses (0.0.0.0) or on the VIP address
      (172.26.20.110 here).

      ---------------------------------------------------------------------------------------
      Legacy 4. Real servers having different network routes
      In the virtual server via direct routing, the servers can follows the different network
      routes to the clients (different Internet links), it is good for performance. The load
      balancer and real servers use a private LAN to communicate.

      The load balancer (LinuxDirector), kernel 2.2.14
      $ ifconfig eth0 <an IP address> ...
      $ ...
      $ ifconfig eth0:0 <VIP> netmask 255.255.255.255 broadcast <VIP> up
      $ ifconfig eth1 192.168.0.1 netmask 255.255.255.0 broadcast 192.168.0.255 up
      $ ipvsadm -A -t <VIP>:23
      $ ipvsadm -A -t <VIP>:23 -r 192.168.0.2 -g
      $ ...

      The real server 1, kernel 2.0.36
      $ ifconfig eth0 <a seperate IP address> ...
      $ # Follow the different network route
      $ ...
      $ ifconfig eth1 192.168.0.2 netmask 255.255.255.0 broadcast 192.168.0.255 up
      $ route add -net 192.168.0.0 netmask 255.255.255.0 dev eth1
      $ ifconfig lo:0 <VIP> netmask 255.255.255.255 broadcast <VIP> up
      $ route add -host <VIP> dev lo:0

    - Local Node
      using the director as a “sorry server” (e.g. when all realservers are overloaded and
      you want to display a “please come back later message”).
      With Local Node, the director machine can be a realserver too. This is convenient
      when only a small number of machines are available as servers. To use localnode with
      ipvsadm you add a realserver with IP 127.0.0.1 (or any local IP on your director).
      You then setup the service to listen to the VIP on the director, so that when the
      service replies to the client, the src_addr of the reply packets are from the VIP.
      The client is not connecting to a service on 127.0.0.1 (or a local IP on the director),
      despite ipvsadm installing a service with RIP=127.0.0.1.
      Some services, e.g. telnet listen on all IP's on the machine and you won't have to do
      anything special for them, they will already be listening on the VIP. Other services,
      e.g. http, sshd, have to be specifically configured to listen to each IP.
      Note. Configuring the service to listen to an IP which is not the VIP, is the most
      common mistake of setting for LocalNode. LocalNode operates independantly of NAT, TUN
      or DR modules (i.e. you have LocalNode running on a director that is forwarding packets
      to realservers by any of the forwarding methods).

    - vRRP
      Virtual Router Redundancy Protocol (VRRP) is a computer networking protocol that
      provides for automatic assignment of available IP routers to participating hosts.
      It's a daemon that implements the VRRPv2 (Virtual Router Redundant Protocol) (RFC 2338)
      for Linux. The daemon has to be run on each of the boxes that together make the high
      availability system.

      Basically, its function is to create a set of nodes with the same IP, so if one dies,
      another box of the same set can take its place transparently for the end user or host,
      e.g. a redundant system. Usually (though not necessarily) it is used on routers.

      How it works
      Each group has a master box, that honours services associated to an IP. That IP is
      shared throughout all the set. When the master node fails, a backup node takes its
      place as a new master. To choose which one should be the new master, static priorities
      are assigned to each node. Furthermore, as a box can be part of other redundancy
      groups, sets are attached together with a unique ID, called VRID (Virtual Router ID),

      Virtual IP and virtual MAC
      The shared IP is a virtual IP, but that has to be completely valid; any host that uses
      that IP must not suffer from bad catching in the ARP table (MAC/IP couple). So this
      doesn't happen, a virtual MAC associated to the virtual IP is created. Otherwise,
      we'd have to wait for the arp table in each host that uses the redundant system entry
      to timeout for the new master to work, wrecking the whole point of use.
      Therefore, even if the host that serves that IP changes, as the MAC is consistent
      through all the set, the ARP table entry of each box that uses the high availability
      system is absolutely valid. The service is attended transparently by each node of the
      set so that the end user hardly notices the change.
      To makes things extra simple, the virtual MAC is made up from the standard prefix
      00:00:5E:00:00:01 and the VRID. Let's say we have assigned VRID 1 to our set.
      Then our virtual MAC will be 00:00:5E:00:00:01 + 01 = 00:00:5E:00:00:01:01.

      Node intercommunication (synchronization)
      The master node takes active part in synchronizing the whole system. Every fixed
      period of time (by default, 1 second) it announces that it is up and running, sending
      out a packet to the 224.0.0.18 multicast address. When a few cycles of these
      announcements pass (3 by default) without any announcement of the master, the working
      highest priority backup node comes into play, taking its turn to be master node.
      If this actually happens, and the master node comes back to life afterwards, because
      it has higher priority, it preempts the temporal master; the first king goes back to
      its master ruling postion, setting the old backup node back to its idle wait status.

      Priorities (master node and backup nodes)
      Each node has a static priority so that, in case of competition, decide who shall be
      master. The alive node with highest priority will be the new ruler.

      How long does it take?
      It seems that the response is quicker when the master fails, to when the master that
      had failed cames back. On average, in the first case, a backup starts working after
      about 10 seconds. In the second case, when the master that had failed comes back to
      its original status, it doesn't become functional till about 30 seconds or 1 min after

      Where to see it
      Around in /var/log/syslog of each node. Here you have an extract from a backup

      Usage
      Virtual IP 172.16.0.222 inside the network 172.16.0.0/16.
      The announcements of the master node are not to 255.255.255.255, only to the range the
      virtual IP is in, 172.16.0.0/16. the master has to have the highest priority.

      At the master node, box A
      $ ifconfig eth0
      | HWaddr 00:E0:4C:31:69:5C
      | inet addr:172.168.0.3
      $ vrrpd -i eth0 -v 1 -D -p 100 172 168.0.222
      $ ifconfig eth0
      | HWaddr 00:00:5E:00:00:01:01

      At the backup node, box B
      $ ifconfig eth0
      | HWaddr 00:07:95:A6:BE:81
      | inet addr:172.168.0.50
      $ vrrpd -i eth0 -v 1 -D -p 150 -n 172 168.0.222
      | HWaddr 00:07:95:A6:BE:81

      -i eth0 : interface that is going to be modified and whose IP is
      -v 1    : VRID of the set
      -D      : daemonize.
      -p 100  : this node's priority
      -n      : do not change immediately the MAC to the virtual one

      If now, carrying on with the example, we disconnect the master, the backup will come
      into play. When the master arrives again, it'll be master and rule again

    - keepalived
      is a routing software written in C. The main goal is to provide robust facilities for
      loadbalancing and high-availability to Linux based infrastructures.
      Loadbalancing framework relies on well-known Linux Virtual Server (IPVS) kernel module
      providing Layer4 loadbalancing. Internally keepalived uses VRRP.

      $ sudo apt-get install nginx keepalived
      $ vim /etc/keepalived/keepalived.conf

      Global definitions synopsis
      |global_defs {
      |    notification_email {
      |		email
      |		email
      |    }
      |    notification_email_from email          //email to use when processing MAIL FROM
      |    smtp_server host
      |    smtp_connect_timeout num
      |    lvs_id string                          //specify the name of the LVS director
      |}

      Virtual server definitions synopsis
      | virtual_server (@IP PORT)|(fwmark num) {  //specify that virtual server is a FWMARK
      |   delay_loop num	                //specify in sec. the interval between checks
      |   lb_algo rr|wrr|lc|wlc|sh|dh|lblc    //select a specific scheduler
      |   lb_kind NAT|DR|TUN		        //select a specific forwarding method
      |   (nat_mask @IP)
      |   persistence_timeout num     //specify a timeout value for persistent connections
      |   persistence_granularity @IP //specify a granularity mask for persistent connections
      |   virtualhost string          //specify a HTTP virtualhost to use for HTTP|SSL_GET
      |   protocol TCP|UDP            //specify the protocol kind (TCP or UDP)
      |   sorry_server @IP PORT       //server to be added if all real servers are down
      |   real_server @IP PORT {      //specify a real server member
      |     weight num		//specify server weight for load balancing decisions
      |     TCP_CHECK {		//check server availability using TCP connect
      |       connect_port num
      |       connect_timeout num
      |     }
      |   }
      |   real_server @IP PORT {
      |     weight num
      |     MISC_CHECK {          //check real server availability using user defined script
      |       misc_path /path_script/script.sh
      |       (or misc_path “/path_script/script.sh <arg_list>”)   //require "" for path+arg
      |     }
      |   }
      |   real_server @IP PORT {
      |     weight num
      |     HTTP_GET|SSL_GET { //check real server availability using HTTP|SSL GET request
      |  	url {      # You can add multiple url block
      |  	  path alphanum                //specify the url path
      |  	  digest alphanum              //specify the digest for a specific url path
      |  	}
      |  	connect_port num
      |  	connect_timeout num
      |  	nb_get_retry num               //maximum number of retries
      |  	delay_before_retry num         //delay between two successive retries
      |     }
      |   }
      | }

      VRRP Instance definitions synopsis
      | vrrp_sync_group string {
      |   group {
      |     string
      |     string
      |   }
      |   notify_master /path_script/script_master.sh
      |     (or notify_master “/path_script/script_master.sh <arg_list>”)
      |   notify_backup /path_script/script_backup.sh
      |     (or notify_backup “/path_script/script_backup.sh <arg_list>”)
      |   notify_fault /path_script/script_fault.sh
      |     (or notify_fault “/path_script/script_fault.sh <arg_list>”)
      | }
      | vrrp_instance string {
      |   state MASTER|BACKUP              //the instance state in standard use
      |   interface string                 //network interface for instance to run on
      |   mcast_src_ip @IP                 //src IP value for VRRP adverts IP header
      |   lvs_sync_daemon_interface string //network interface for LVS sync_daemon to run on
      |   virtual_router_id num            //to which VRRP router id the instance belongs
      |   priority num		     //instance priority in the VRRP router
      |   advert_int num                   //advertisement interval in seconds (set to 1)
      |   smtp_alert			     //activate SMTP notification for MASTER transition
      |   authentication {                 //identify a VRRP authentication definition block
      |     auth_type PASS|AH              //specify which kind of authentication to use
      |     auth_pass string               //specify the password string to use
      |   }
      |   virtual_ipaddress { # Block limited to 20 IP addresses
      |     @IP
      |     @IP       // identify a VRRP VIP definition block
      |     @IP
      |   }
      |   virtual_ipaddress_excluded { # Unlimited IP addresses number
      |     @IP
      |     @IP       // identify a VRRP VIP excluded definition block (not protocol VIPs)
      |     @IP
      |   }
      |   notify_master /path_to_script/script_master.sh
      |     (or notify_master “/path_to_script/script_master.sh <arg_list>”)
      |   notify_backup /path_to_script/script_backup.sh
      |     (or notify_backup “/path_to_script/script_backup.sh <arg_list>”)
      |   notify_fault /path_to_script/script_fault.sh
      |     (or notify_fault “/path_to_script/script_fault.sh <arg_list>”)
      |
      |   //notify_master: specify a script to be executed during transition to master state
      |   //notify_backup: specify a script to be executed during transition to backup state
      |   //notify_fault : specify a script to be executed during transition to fault state
      |   //vrrp_sync_group: 	Identify the VRRP synchronization instances group
      | }

      Example configuration of keepalived
      -LB, IP of load balancer's external interface(eth0): 192.168.1.9
      -LB, external VIP of our realserver: 192.168.1.11
      -LB, IP of load balancer's interface(eth1): 10.20.40.2
      -LB, internal VIP our realserver will use as a default gateway: 10.20.40.1
      -Real Server IP: 10.20.40.10
      -Real Server default gateway: 10.20.40.1
      | global_defs {
      |   notification_email {
      |     admins@example.com
      |     fakepager@example.com
      |   }
      |   notification_email_from admins@example.com
      |   smtp_server 127.0.0.1
      |   smtp_connect_timeout 30
      |   lvs_id LVS_EXAMPLE_01
      | }
      | vrrp_sync_group VG1 {
      |   group {
      |     VI_1
      |     VI_GATEWAY
      |   }
      | }
      | vrrp_instance VI_1 {
      |   state MASTER
      |   interface eth0
      |   lvs_sync_daemon_inteface eth0
      |   virtual_router_id 51
      |   priority 150
      |   advert_int 1
      |   smtp_alert
      |   authentication {
      |     auth_type PASS
      |     auth_pass example
      |   }
      |   virtual_ipaddress {
      |     192.168.1.11
      |   }
      | }
      | vrrp_instance VI_GATEWAY {
      |   state MASTER
      |   interface eth1
      |   lvs_sync_daemon_inteface eth1
      |   virtual_router_id 52
      |   priority 150
      |   advert_int 1
      |   smtp_alert
      |   authentication {
      |     auth_type PASS
      |     auth_pass example
      |   }
      |   virtual_ipaddress {
      |     10.20.40.1
      |   }
      | }
      | virtual_server 192.168.1.11 22 {
      |   delay_loop 6
      |   lb_algo rr
      |   lb_kind NAT
      |   nat_mask 255.255.255.0
      |   protocol TCP
      |   real_server 10.20.40.10 22 {
      |     weight 1
      |     TCP_CHECK {
      |       connect_timeout 3
      |       connect_port 22
      |     }
      |   }
      | }

      Check it after keepalived starts up
      $ keepalived -d
      $ cat /var/log/message
      $ ipvsadm
      $ ip addr list

      we can easily move to a failover situation, for that, all you have to do is here
      - setup keepalived on another box,
      - copy over the keepalived.conf,
      - change the lvs_id,
      - change any priorities down 50 points,
      - states to BACKUP,
      - run keepalived.
      - see logs, the backup tserver accepts it's BACKUP state
      - if you unplug the network cable from MASTER server, the BACKUP server takes over.

      On the BACKUP machine, only few line have to be modified!
      | global_defs {
      |   ...
      |   lvs_id LVS_EXAMPLE_02     // f01 ---> 02
      |   ...
      | }
      | vrrp_instance VI_1 {
      |   ...
      |   state BACKUP              // MASTER to BACKUP
      |   priority 100              // 150 ---> 100
      |   ...
      | }
      | vrrp_instance VI_GATEWAY {
      |   ...
      |   state BACKUP              // MASTER to BACKUP
      |   priority 100              // 150 ---> 100
      |   ...
      | }

      Once you startup keepalived on the MASTER and the BACKUP, you should be able to kill
      keepalived on the MASTER server and watch the BACKUP take over in the logs on the
      BACKUP server.

    - ldirectord
      is a daemon to monitor and administer real servers in a LVS cluster of load balanced
      virtual servers. ldirectord typically used as a resource for HAProxy , but can also be
      run from the command line.

      This should be just enough information to get you up and running with Pacemaker
      managing ldirectord and a virtual IP address. It does not cover configuring the actual
      service you want load-balanced.

      Configuration for a hypothetical TCP service ldirectord
      - Create /etc/ha.d/ldirectord.cf on all nodes
      - virtual IP 192.168.1.100 port 8888
      - real servers 192.168.1.10 and 192.168.1.20
      $ vi /etc/ha.d/ldirectord.cf
      | checktimeout=3
      | checkinterval=5
      | autoreload=yes
      | logfile="/var/log/ldirectord.log"
      | quiescent=no
      | virtual=192.168.1.100:8888
      | real=192.168.1.10:8888 gate
      | real=192.168.1.20:8888 gate
      | scheduler=wrr
      | protocol=tcp
      | checktype=connect
      | checkport=8888

      Configure Pacemaker using the crm shell:
      $ crm
      | primitive ip ocf:heartbeat:IPaddr2 \
      |   op monitor interval="60" timeout="20" \
      |   params ip="192.168.1.100" lvs_support="true"
      | primitive ip-lo ocf:heartbeat:IPaddr2 \
      |   op monitor interval="60" timeout="20" \
      |   params ip="192.168.1.100" nic="lo" cidr_netmask="32"
      | primitive lvs ocf:heartbeat:ldirectord \
      |   op monitor interval="20" timeout="10"
      | group ip-lvs ip lvs
      | clone c-ip-lo ip-lo meta interleave="true"
      | colocation lo-never-lvs -inf: c-ip-lo ip-lvs

      This gives you the virtual IP address and ldirectord running together in a group
      (ip-lvs) on one node, and the same virtual IP address assigned to the loopback address
      on all other nodes. This is necessary to make the routing work correctly.

    - ipvsadm
      is used to set up, maintain or inspect the virtual server table in the Linux kernel.
      $ ipvsadm -ln                   //the output of ipvs rules
      $ ipvsadm --start-daemon
      $ ipvsadm --stop-daemon
      $ ipvsadm -A --add-server
      $ ipvsadm -D --delete-server
      $ ipvsadm -E --edit-server
      $ ipvsadm -a --add-service
      $ ipvsadm -d --delete-service
      $ ipvsadm -e --edit-service
      $ ipvsadm -t --tcp-service [ipaddress]
      $ ipvsadm -r --real-server [ipaddress]
      $ ipvsadm -f --fwmark-service [integer]
      $ ipvsadm -m --masquerading
      $ ipvsadm -s --scheduler [rr|wrr|lc|wlc|lblc|lblcr|dh|sh|nq]

      Simple Virtual Service, configure a Linux Director to distribute incoming requests
      addressed to port 80 on 207.175.44.110 equally to port 80 on 3 real servers.
      Each of the real servers is being masqueraded by the Linux Director.
      The default route of the real servers must be set to the linux director, which will
      need to be configured to forward and masquerade packets.
      $ ipvsadm -A -t 207.175.44.110:80 -s rr
      $ ipvsadm -a -t 207.175.44.110:80 -r 192.168.10.1:80 -m
      $ ipvsadm -a -t 207.175.44.110:80 -r 192.168.10.2:80 -m
      $ ipvsadm -a -t 207.175.44.110:80 -r 192.168.10.3:80 -m
      $ echo "1" > /proc/sys/net/ipv4/ip_forward

      Firewall-Mark Virtual Service, configure a Linux Director to distribute incoming
      requests addressed to any port on 207.175.44.110 or 207.175.44.111 equally to the
      corresponding port on 3 real servers.
      $ ipvsadm -A -f 1 -s rr
      $ ipvsadm -a -f 1 -r 192.168.10.1:0 -m
      $ ipvsadm -a -f 1 -r 192.168.10.2:0 -m
      $ ipvsadm -a -f 1 -r 192.168.10.3:0 -m
      The real server should also be configured to mark incoming packetsm addressed to any
      port on 207.175.44.110 and  207.175.44.111 with firewall-mark 1. If FTP traffic is to
      be handled by this virtual service, then the ip_vs_ftp kernel module needs to be
      inserted into the kernel. These operations can be achieved using following
      $ echo "1" > /proc/sys/net/ipv4/ip_forward
      $ modprobe ip_tables
      $ iptables -A PREROUTING -t mangle -d 207.175.44.110/31 -j MARK --set-mark 1
      $ modprobe ip_vs_ftp

      Using firewall-mark virtual services provides a convenient method of grouping together
      different IP addresses, ports and protocols into a single virtual service. This is
      useful for both simplifying configuration if a large number of virtual services are
      required and grouping persistence across what would be multiple virtual services.
      It can be used to build a virtual service assoicated with the same real servers,
      covering multiple IP address, port and protocol tripplets

      The Linux Virtual Server implements three defense strategies against some types of
      denial of service (DoS) attacks. Valid values for each variable are 0 through to 3.
      (1 & 2=automatic modes, 3=always enable)
      /proc/sys/net/ipv4/vs/drop_entry
      /proc/sys/net/ipv4/vs/drop_packet
      /proc/sys/net/ipv4/vs/secure_tcp
      /proc/sys/net/ipv4/vs/amemthresh        //available memory threshold
      /proc/sys/net/ipv4/vs/timeout_*		//secure TCP timeouts

    - syncd
      Server State Sync Daemon, syncd (saving the director's connection state on failover)

      On the primary load balancer,
      $ ipvsadm --start-daemon=master --mcast-interface=eth0

      On the backup load balancers, run
      $ ipvsadm --start-daemon=backup --mcast-interface=eth0

      Note that there is some performance penalty when connection synchronization, because
      a highly loaded load balancer may need to multicast a lot of connection information.
      If the daemon is not started, the performance will not be affected. Syncd boxes must
      have the same time.

      $ primary $ ipvsadm -lc
      $ backup  $ ipvsadm -lc

    - genhash
      is a tool used for generating md5sum hashes of remote web pages. genhash can use HTTP
      or HTTPS to connect to the web page. The output by this utility includes the HTTP
      header, page data, and the md5sum of the data. This md5sum can then be used within the
      keepalived program, for monitoring HTTP and HTTPS services.

    - HAProxy
      provides a high availability load balancer and proxy server for TCP and HTTP-based
      applications that spreads requests across multiple servers. It is particularly suited
      for web crawling under very high loads while needing persistence or Layer7 processing.
      Supporting tens of thousands of connections is clearly realistic with todays hardware.
      Its mode of operation makes its integration into existing architectures very easy and
      riskless, while still offering the possibility not to expose fragile web servers to
      the Net.

      $ sudo apt-get install haproxy
      $ vi /etc/default/haproxy
      | ENABLED=1
      $ sudo service haproxy

      Configuring HAProxy
      HAProxy's configuration process involves 3 major sources of parameters :
      - the arguments from the command-line, which always take precedence
      - the “global” section, which sets process-wide parameters
      - the proxies sections which take form of “defaults”, “listen”, “frontend”, “backend”.

      The configuration file syntax consists in lines beginning with a keyword referenced in
      this manual, optionally followed by one or several parameters delimited by spaces. If
      spaces have to be entered in strings, then they must be preceded by a backslash ('\')
      to be escaped. Backslashes also have to be escaped by doubling them.

      HAproxy Algorithms
      Round Robin, Static Round Robin, Least Connections, Source, URI, URI parameter
      | backend staic_server
      |     balance uri
      |     balance uri_param id
      |     balance leastconn
      |     balance roundrobin
      |     balance source

      Time format
      Some parameters involve values representing time, such as timeouts. These values are
      generally expressed in milliseconds (unless explicitly stated otherwise) but may be
      expressed in any other unit by suffixing the unit to the numeric value.
      units: us(micro),ms(default),s(1000ms),m(60000ms),h(3600000ms),d(86400000ms)

      Simple configuration for an HTTP proxy listening on port 80 on all interfaces and
      forwarding requests to a single backend "servers" with a single server "server1"
      $ vi /etc/haproxy/configuration.conf
      | # listening on 127.0.0.1:8000
      | global
      |     daemon
      |     maxconn 256
      | defaults
      |     mode http
      |     timeout connect 5000ms
      |     timeout client 50000ms
      |     timeout server 50000ms
      | frontend http-in
      |     bind *:80
      |     default_backend servers
      | backend servers
      |     server server1 127.0.0.1:8000 maxconn 32
      |
      The same configuration defined with a single listen block. Shorter but
      less expressive, especially in HTTP mode.
      | global
      |     daemon
      |     maxconn 256
      | defaults
      |     mode http
      |     timeout connect 5000ms
      |     timeout client 50000ms
      |     timeout server 50000ms
      | listen http-in
      |     bind *:80
      |     server server1 127.0.0.1:8000 maxconn 32

      Another example
      | global
      |     log 127.0.0.1 local0 notice
      |     maxconn 2000
      |     user haproxy
      |     group haproxy
      | defaults
      |     log     global
      |     mode    http
      |     option  httplog
      |     option  dontlognull
      |     retries 3
      |     option redispatch
      |     timeout connect  5000
      |     timeout client  10000
      |     timeout server  10000
      | listen appname 0.0.0.0:80
      |     mode http
      |     stats enable
      |     stats uri /haproxy?stats
      |     stats realm Strictly Private
      |     stats auth A_Username:YourPassword
      |     stats auth Another_User:passwd
      |     balance roundrobin
      |     option httpclose
      |     option forwardfor
      |     server lamp1 172.22.10.101:80 check
      |     server lamp2 172.22.10.102:80 check

      Assuming haproxy is in $PATH, test these configurations in a shell with:
      $ sudo haproxy -f configuration.conf -c

      Global parameters
      Parameters in the “global” section are process-wide and often OS-specific. They are
      generally set once for all and do not need being changed once correct. Some of them
      have command-line equivalents.

      The following keywords are supported in the “global” section :
      * Process management and security and Debugging
      ca-base,chroot,crt-base,daemon,gid,group,log,log-send-hostname,user,stats,node,
      debug,quiet ...
      * Performance tuning
      maxconn,maxconnrate,maxpipes,maxsslconn,noepoll,nokqueue,nopoll,nosepoll,nosplice
      spread-checks,tune.bufsize,tune.chksize,tune.http.maxhdr,tune.maxaccept,
      tune.maxpollevents,tune.maxrewrite,tune.pipesize,tune.rcvbuf.client ...

      If you are interesting to use for balancing of email servers, use tcp instead of http
      | defaults
      | mode tcp

    - Nginx as a load balancing
      Study ...

    - ACLs
      An access control list (ACL), with respect to a computer file system, is a list of
      permissions attached to an object. An ACL specifies which users or system processes
      are granted access to objects, as well as what operations are allowed on given objects

      The use of Access Control Lists (ACL) provides a flexible solution to perform content
      switching and generally to take decisions based on content extracted from the request,
      the response or any environmental status. The principle is simple:
      - define test criteria with sets of values
      - perform actions only if a set of tests is valid
      the actions generally consist in blocking the request, or selecting a backend.
      $ acl <aclname> <criterion> [flags] [operator] <value>

      ACL names must be formed from upper and lower case letters, digits, dash, underscore,
      dot and colon. ACL names are case-sensitive, which means that “my\_acl” and “My\_Acl”
      are two different ACLs. There is no enforced limit to the number of ACLs. The unused
      ones do not affect performance, they just consume a small amount of memory.

      The following ACL flags are currently supported:
      -i	ignore case during matching of all subsequent patterns.
      -f	load patterns from a file.
      – 	force end of flags. Useful when a string looks like one of the flags.
      The ”-f” flag is special as it loads all of the lines it finds in the file specified
      in argument and loads all of them before continuing. It is even possible to pass
      multiple ”-f” arguments if the patterns are to be loaded from multiple files.
      All leading spaces and tabs will be stripped. If it is absolutely needed to insert a
      valid pattern beginning with a sharp, just prefix it with a space so that it is not
      taken for a comment. Depending on the data type and match method, HAproxy may load the
      lines into a binary tree, allowing very fast lookups. This is true for IPv4 and exact
      string matching. In this case, duplicates will automatically be removed. Also, note
      that the ”-i” flag applies to subsequent entries and not to entries loaded from files
      preceeding it.

      For instance:
      $ acl valid-ua hdr(user-agent) -f exact-ua.lst -i -f generic-ua.lst  test
      In this example, each line of “exact-ua.lst” will be exactly matched against the
      “user-agent” header of the request. Then each line of “generic-ua” will be
      case-insensitively matched. Then the word “test” will be insensitively matched too.

      Note that right now it is difficult for the ACL parsers to report errors, so if a
      file is unreadable or unparsable, the most you'll get is a parse error in the ACL.
      Thus, file-based ACLs should only be produced by reliable processes.
      Supported types of values are:
      - integers or integer ranges
      - strings
      - regular expressions
      - IP addresses and networks

      Some actions are only performed upon a valid condition. A condition is a combination
      of ACLs with operators. 3 operators are supported:
      - AND (implicit)
      - OR (explicit with the "or" keyword or the "||" operator)
      - Negation with the exclamation mark ("!")

      A condition is formed as a disjunctive form:
      $ [!]acl1 [!]acl2 ... [!]acln { or [!]acl1 [!]acl2 ... [!]acln } ...
      Such conditions are generally used after an “if” or “unless” statement, indicating
      when the condition will trigger the action.
      For instance, to block HTTP requests to the “*” URL with methods other than “OPTIONS”,
      as well as POST requests without content-length, and GET or HEAD requests with a
      content-length greater than 0, and finally every request which is not either
      GET/HEAD/POST/OPTIONS!
      $ acl missing_cl hdr_cnt(Content-length) eq 0
      $ block if HTTP_URL_STAR !METH_OPTIONS || METH_POST missing_cl
      $ block if METH_GET HTTP_CONTENT
      $ block unless METH_GET or METH_POST or METH_OPTIONS

      To select a different backend for requests to static contents on the “www” site and
      to every request on the “img”, “video”, “download” and “ftp” hosts:
      $ acl url_static  path_beg         /static /images /img /css
      $ acl url_static  path_end         .gif .png .jpg .css .js
      $ acl host_www    hdr_beg(host) -i www
      $ acl host_static hdr_beg(host) -i img. video. download. ftp.
      Now use backend "static" for all static-only hosts, and for static urls of host "www".
      Use backend "www" for the rest.
      $ use_backend static if host_static or host_www url_static
      $ use_backend www    if host_www

      It is also possible to form rules using “anonymous ACLs”. Those are unnamed ACL
      expressions that are built on the fly without needing to be declared. They must be
      enclosed between braces, with a space before and after each brace
      (because the braces must be seen as independant words).
      The following rule:
      $ acl missing_cl hdr_cnt(Content-length) eq 0
      $ block if METH_POST missing_cl
      can also be written that way:
      $ block if METH_POST { hdr_cnt(Content-length) eq 0 }

      It is generally not recommended to use this construct because it's a lot easier to
      leave errors in the configuration when written that way. However, for very simple
      rules matching only one source IP address for instance, it can make more sense to use
      them than to declare ACLs with random names. Another example of good use is the
      following
      with named ACLs:
      $ acl site_dead nbsrv(dynamic) lt 2
      $ acl site_dead nbsrv(static)  lt 2
      $ monitor fail  if site_dead
      with anonymous ACLs:
      $ monitor fail if { nbsrv(dynamic) lt 2 } || { nbsrv(static) lt 2 }


<br/><a name="failover"></a>

### Failover Clusters

    ===============================================================================================
    334.3 Failover Clusters 
    ===============================================================================================
    - Failover
      When the VM is temporarily stopped, snapshotted, moved, and then resumed on the new
      host, this is known as migration. If the older snapshots are kept in sync regularly,
      this operation can be quite fast, and allow the VM to provide uninterrupted service
      while its prior physical host is, for example, taken down for physical maintenance.

      Similarly to the migration mechanism described above, failover allows the VM to
      continue operations if the host fails. However, in this case, the VM continues
      operation from the last-known coherent state, rather than the current state,
      based on whatever materials the backup server was last provided with.

    - Pacemaker
      A scalable High Availability cluster resource manager.
      Pacemaker is the thing that starts and stops services, and contains logic for ensuring
      both that they’re running, and that they’re only running in one location.
      But it can’t do that without the ability to talk to instances of itself on the other
      node(s), which is where Heartbeat and/or Corosync come in.

      Pacemaker architecture and internal components, is composed of 5 key components
      CIB      : Cluster Information Base
      CRMd     : Cluster Resource Management daemon (a message broker for the PEngine & LRM)
      LRMd     : Local Resource Management daemon
      PEngine  : Policy Engine (PE)
      STONITHd : Fencing daemon

      Pacemaker management : pcs and crmsh (as a console configuration)
      $ crm                                : connect to the crm manager
      $ crm(live) # status                 : check the status of the resources
      $ crm(live) # cib new test-conf      : create a new shadow copy
      $ crm(live) # cib use test-conf      : use it
      $ crm(test-conf) # configure         : enter the configuration menu
      $ crm(test-conf)configure #          : now inside configuration
      $ crm(test-conf)configure # show     : show configuration file in heartbeat cluster
      $ crm(test-conf)configure # show xml : show configuration file with xml format
      $ crm(test-conf)configure # verity   : verifify configuration
      $ crm(test-conf)configure # end      : break the current setup
      $ crm(live) # cib commit test-conf   : commit configuration
      $ crm(live) # quit                   : exit crm
      $ crm(live) # edit                   : edit configuration (similar XML)

      ---------------------------------------------------------------------------------------
      Failover IP
      $ crm) primitive failover-ip ocf:heartbeat:IPaddr params ip=1.1.1.1 \
      op monitor interval=10s

      ---------------------------------------------------------------------------------------
      Failover IP + one service
      $ crm) primitive failover-ip ocf:heartbeat:IPaddr params ip=1.1.1.1 \
      op monitor interval=10s
      $ crm) primitive failover-apache lsb::apache op monitor interval=15s

      ---------------------------------------------------------------------------------------
      Failover IP Service in a group
      $ crm) primitive failover-ip ocf:heartbeat:IPaddr params ip=1.1.1.1 \
      op monitor interval=10s
      $ crm) primitive failover-apache lsb::apache op monitor interval=15s
      $ crm) group my_web_cluster failover-ip failover-apache

      ---------------------------------------------------------------------------------------
      Failover IP Service in a group running on a connected node
      $ crm) primitive pingd ocf:pacemaker:pingdparams host_list=85.9.12.100 \
      multiplier=100 op monitor interval=15s timeout=5s
      $ crm) clone pingdclone pingd meta globally-unique=false
      $ crm) location my_web_cluster_on_connected_node my_web_cluster \
      rule -inf: not_defined pingd or pingd lte 0
      * This tells the cluster to only run the group on a node with a working network
      connection to the default gateway.

      Example XML file in pacemaker
      ---------------------------------------------------------------------------
      cib
      configuration
      crm_config/
      nodes/
      resources/
      primitive/ id=failover-ip class=ocf provider=heartbeat type=IPaddr
      operations/
      op id=failover-ip-monitor name=monitor interval=10s/
      /operations
      instance_attributes id=failover-ip-attribs
      nvpair id=failover-ip-addr name=ip value=85.9.12.3/
      /instance_attributes
      /primitive
      /resources
      /configuration
      status/
      /cib
      ---------------------------------------------------------------------------

      Pacemaker commnad line tools
      crm_mon       : monitoring the Current State of the Cluster
      crm_verify    : Check a confiuration for syntax and common conceptual errors
      crm_simulate  : Tool for simulating the cluster’s response to events
      crm_shadow    : Perform configuration changes in a sandbox before updating the cluster
      crm_resource  : Perform tasks related to cluster resources(queried, modified & moved)
      crm_attribute : Allows node attributes and cluster options(queried, modified & deleted)
      crm_node      : Tool for displaying low−level node information
      crm_standby   : Check, enable or disable standby mode for a cluster node.
      crm_diff      : A utility for comparing Pacemaker configurations (XML format)

      cibadmin : Provides direct access to the cluster configuration for manipulating CIB.
      Query the configuration from the local node
      $ cibadmin --query --local
      Query the just the cluster options configuration
      $ cibadmin --query --scope crm_config
      Query all 'target-role' settings
      $ cibadmin --query --xpath "//nvpair[@name='target-role']"
      Remove all 'is-managed' settings
      $ cibadmin --delete-all --xpath "//nvpair[@name='is-managed']"
      Remove the resource named 'old'
      $ cibadmin --delete --xml-text '<primitive id="old"/>'
      Remove all resources from the configuration
      $ cibadmin --replace --scope resources --xml-text '<resources/>'
      Replace the complete configuration with the contents of $HOME/pacemaker.xml
      $ cibadmin --replace --xml-file $HOME/pacemaker.xml
      Replace the constraints section with the contents of $HOME/constraints.xml
      $ cibadmin --replace --scope constraints --xml-file $HOME/constraints.xml
      Increase the version to prevent old configurations from loading accidentally
      $ cibadmin --modify --xml-text '<cib admin_epoch="admin_epoch++"/>'
      Edit the configuration with your favorite editor
      $ cibadmin --query > $HOME/local.xml
      $ vi $HOME/local.xml
      $ cibadmin --replace --xml-file $HOME/local.xml
      Options for cibadmin
      −Q, −−query
      −C, −−create
      −E, −−erase
      −R, −−replace
      −X, −−xml−text=value
      −x, −−xml−file=value
      −o, −−scope=value
      : nodes, resources, constraints, crm_config, rsc_defaults, op_defaults, status

      If you want to make a backup of the current state of a pacemaker cluster, then do
      $ cibadmin -Q > backup.xml

    - Resource classes/agents in pacemaker
      Main resource class
      OCF - Open Cluster Framework is an extension of the LSB conventions for init scripts
      to support parameters, make them self-describing, and make them extensible
      configuration parameters are defined with the special prefix OCF_RESKEY_.
      LSB - Linux Standard Base (LSB) is a project to standardize the software structure,
      including the filesystem hierarchy used in the Linux operating system
      The LSB is designed to be binary-compatible and produce a stable application
      binary interface (ABI)
      $ /etc/init.d/some_service start ; echo "result: $?"

      Other resource classes
      • Heartbeat Resource Agents - legacy
      • Systemd - init system used in Linux distributions to manage all processes
      • Upstart - an event-based replacement for the traditional init daemon
      • Service - run a System V init script and allow start and stop the service.
      • Nagios  - offers monitoring and alerting services for servers and services.
      • STONITH - called fencing, protects your data from being corrupted by rogue nodes

      In order, Pacemaker will try to find the named service as :
      1. an LSB init script
      2. a Systemd unit file
      3. an Upstart job

      Actions
      start, stop, monitor, recover, reload, migrate_from, meta-data, validate-all, notify

    - Resource rules and constraints
      • location   : to determine whether a resource will prefer or avoid a specified node
      • order      : to determine the order in which resources start and stop
      • colocation : location of one resource depends on the location of another resource

      Pacemaker tool(=pcs)
      $ pcs constraint location Webserver prefers node1=200
      $ pcs constraint order set node1 node2 node3
      $ pcs constraint order stop 100 then start 200

      $ pcs constraint colocation add myrsc1 with myrsc2 score=INFINITY
      $ pcs constraint colocation add myrsc1 with myrsc2 score=-INFINITY
      | score indicates whether a resource should run on or avoid a node.
      | Because INFINITY was used, if myrsc2 cannot run on any of the cluster nodes
      | (for whatever reason) then myrsc1 will not be allowed to run.
      | In contrast, a cluster in which myrsc1 cannot run on the same machine as myrsc2,
      | when -INFINITY is used. If the only place left to run is where myrsc2 already is,
      | then myrsc1 may not run anywhere.

      List all current location, order, and colocation constraints.
      $ pcs constraint show

      Configuring an "Opt-In" Cluster :
      Configure a cluster in which no resource can run anywhere and then selectively enable
      allowed nodes for specific resources
      $ pcs property set symmetric-cluster=false
      $ pcs constraint location Webserver prefers node1=200
      $ pcs constraint location Webserver prefers node3=0
      $ pcs constraint location Database prefers node2=200
      $ pcs constraint location Database prefers node3=0
      the resource Webserver prefers node1, the resource Database prefers node2, and both
      resources can fail over to node3 if their preferred node fails.

      Configuring an "Opt-Out" Cluster :
      Configure a cluster in which all resources can run anywhere and then create location
      constraints for resources that are not allowed to run on specific nodes.
      $ pcs property set symmetric-cluster=true
      $ pcs constraint location Webserver prefers node1=200
      $ pcs constraint location Webserver avoids node2=INFINITY
      $ pcs constraint location Database avoids node1=INFINITY
      $ pcs constraint location Database prefers node2=200

      Ordered resource have an extra attribute to allow for sets of resources whose actions
      may run in parallel.
      $ crm) order apache_1 Mandatory: apache:start ip_1  : apache start already and ip_1
      $ crm) order apache_2 Mandatory: [ A B ] C          : set A & B start aleady and C
      $ crm) order apache_3 Mandatory: A B                : A strat already and B

    - Advanced resource features
      • groups
      One of the most common elements of a cluster is a set of resources that need to be
      located together, start sequentially, and stop in the reverse order

      • clone resources
      Clones is conceived as a convenient way to start multiple instances of an IP resource
      and have them distributed throughout the cluster for load balancing

      • multi-state resources
      allow the instances to be in one of two operating modes (called roles). The roles are
      called master and slave, but can mean whatever you wish them to mean. The only
      limitation is that when an instance is started, it must come up in the slave role.

      • templates
      resource templates help to reduce the amount of configuration work. If any changes are
      needed, they can be done to the template definition and will take effect globally in
      all resource definitions referencing that template.

    - STONITH
      Shoot The Other Node In The Head (STONITH) is a technique for fencing.
      STONITH fences failed nodes by resetting or powering down the failed node.
      STONITH is a technique for NodeFencing,  where the errant node which might have run
      amok with cluster resources is simply shot in the head. Normally, when an HA system
      declares a node as dead, it is merely speculating that it is dead.

      Multi-node error-prone contention in a cluster can have catastrophic results, such as
      if both nodes try writing to a shared storage resource. STONITH rovides effective,
      if rather drastic, protection against these problems. Single node systems use
      a comparable mechanism called a watchdog timer. A watchdog timer will reset the node
      if the node does not tell the watchdog circuit that it is operating well. A STONITH
      decision can be based on various decisions which can be customer specific plugins.

      There are a few properties a STONITH plugin must have for it to be usable:
      1. It must never report false positives for reset. If a STONITH plugin reports
      that the node is down, it had better be down.
      2. It must support the RESET command (on and off are optional)
      3. When given a RESET/OFF command it must not return control to its caller until the
      node is no longer running. Waiting until it comes up again for RESET is optional.
      4. All commands should work in all circumstances:
      - RESET when node is ON or OFF should succeed and bring the node up
      - OFF when node is OFF should succeed.
      - ON when node is ON should succeed.

      $ stonith_admin --list-installed
      $ stonith_admin --metadata --agent $AGENT_NAME
      $ cibadmin -C -o resources --xml-file stonith.xml
      $ crm_attribute -t crm_config -n stonith-enabled -v true
      $ stonith_admin --reboot nodename

    - Floating IP
      The IP address that points to one of the servers, and can be remapped in the event of
      the failure of the active server.

      A floating IP address is a service provided by Neutron/OpenStack. It's not using any
      DHCP service or being set statically within the guest. As a matter of fact the guest's
      operating system has no idea that it was assigned a floating IP address. The delivery
      of packets to the interface with the assigned floating address is the responsibility
      of Neutron's L3 agent. Instances with an assigned floating IP address can be accessed
      from the public network by the floating IP.

      A floating IP address and a private IP address can be used at the same time on a single
      network-interface. The private IP address is likely to be used for accessing the
      instance by other instances in private networks while the floating IP address would be
      used for accessing the instance from public networks.

    - corosync + pacemaker
      Configuration and Management of corosync in conjunction with Pacemaker
      corosync is a group communication system with additional features for implementing
      high availability within applications. corosync is designed to operate on UDP/IP
      and InfiniBand networks natively.

      The corosync cluster engine provides four C programming interfaces features:
      • closed process group communication model with virtual synchrony guarantees for
      creating replicated state machines.
      • simple availability manager that restarts the application process when it has failed.
      • configuration and statistics in-memory database that provide the ability to set,
      retrieve, and receive change notifications of information.
      • quorum system that notifies applications when quorum is achieved or lost.

      Senario
      Active/Passive Server, failover mechanism, if the active server becomes unavailable,
      and the Floating IP is remapped to the standby server

      1. Create primary and secondary machine/droplet, use bash shell on both
      | #!/bin/bash
      | apt-get -y update
      | apt-get -y install nginx
      | export HOSTNAME=$(curl -s http://169.254.169.254/metadata/v1/hostname)
      | export PUBLIC_IPV4=$(curl -s \
      |        http://169.254.169.254/metadata/v1/interfaces/public/0/ipv4/address)
      | echo Droplet: $HOSTNAME, IP Address: $PUBLIC_IPV4 > /usr/share/nginx/html/index.html

      2. Create a floating IP
      DigitalOcean/Openstack!

      3. Configure DNS (optional)
      create an A record in your DNS that points your domain to your Floating IP address

      4. Configure Time Synchronization
      $ sudo dpkg-reconfigure tzdata
      $ sudo apt-get update
      $ sudo apt-get -y install ntp

      5. Configure Firewall
      Corosync uses UDP transport between ports 5404 and 5406
      $ sudo iptables -A INPUT  -i eth1 -p udp -m multiport --dports 5404,5405,5406 \
      -m conntrack --ctstate NEW,ESTABLISHED -j ACCEPT
      $ sudo iptables -A OUTPUT  -o eth1 -p udp -m multiport --sports 5404,5405,5406 \
      -m conntrack --ctstate ESTABLISHED -j ACCEPT

      6. Install pacemaker and corosync in both
      $ primary   $ sudo apt-get install pacemaker
      $ secondary $ sudo apt-get install pacemaker

      7. Corosync requires that each node possesses an identical cluster authorization key.
      $ primary   $ sudo apt-get install haveged
      $ primary   $ sudo corosync-keygen
      $ primary   $ sudo apt-get remove --purge haveged
      $ primary   $ sudo apt-get clean

      8. Servers should have an identical authorization key in the /etc/corosync/authkey
      So, copy the authkey from primary to the secondary server
      $ primary   $ sudo scp /etc/corosync/authkey username@secondary_ip:/tmp
      $ secondary $ sudo mv /tmp/authkey /etc/corosync
      $ secondary $ sudo chown root: /etc/corosync/authkey
      $ secondary $ sudo chmod 400 /etc/corosync/authkey

      9. Configure Corosync Cluster on both servers
      $ sudo vi /etc/corosync/corosync.conf
      | totem {
      |   version: 2
      |   cluster_name: lbcluster
      |   transport: udpu
      |   interface {
      |     ringnumber: 0
      |     bindnetaddr: server_private_IP_address
      |     broadcast: yes
      |     mcastport: 5405
      |   }
      | }
      | quorum {
      |   provider: corosync_votequorum
      |   two_node: 1
      | }
      | nodelist {
      |   node {
      |     ring0_addr: primary_private_IP_address
      |     name: primary
      |     nodeid: 1
      |   }
      |   node {
      |     ring0_addr: secondary_private_IP_address
      |     name: secondary
      |     nodeid: 2
      |   }
      | }
      | logging {
      |   to_logfile: yes
      |   logfile: /var/log/corosync/corosync.log
      |   to_syslog: yes
      |   timestamp: on
      | }

      totem    : refers to the Totem protocol that Corosync uses for cluster membership,
      specifies how the cluster members should communicate with each other
      quorum   : specifies that this is a two-node cluster, so only a single node is
      required for quorum (two_node: 1).
      nodelist : specifies each node in the cluster, and how each node can be reached
      logging  : specifies that the Corosync logs should be written to
      /var/log/corosync/corosync.log.

      10. Configure Corosync to allow the Pacemaker service on both servers
      $ sudo vi /etc/corosync/service.d/pcmk
      | service {
      |   name: pacemaker
      |   ver: 1
      | }
      $ sudo vi /etc/default/corosync
      | START=yes
      $ sudo service corosync start
      $ sudo corosync-cmapctl | grep members

      corosync-cfgtool    : to check whether cluster communication is happy
      corosync-cmapctl    : to check the membership and quorum APIs
      corosync-quorumtool : display the state of quorum and set vote quorum options
      stonith_admin       : allows to add/remove/list devices, check device and fence hosts

      11. Start and configure pacemaker (Pacemaker,20 starts after Corosync,19)
      $ sudo update-rc.d pacemaker defaults 20 01
      $ sudo service pacemaker start
      $ sudo crm status
      $ sudo crm_mon

      $ sudo crm configure property stonith-enabled=false
      $ sudo crm configure property no-quorum-policy=ignore
      $ sudo crm configure show

      12. Create Floating IP Reassignment Resource Agent
      Now that Pacemaker is running and configured, we need to add resources to manage.
      Resources are services that the cluster is responsible for making highly available.
      In Pacemaker, adding a resource requires the use of a resource agent, which act as
      the interface to the service that will be managed. Pacemaker ships with several
      resource agents for common services, and allows custom resource agents to be added.

      we need a way to ensure that our Floating IP is always pointing to server that is
      available. To enable this, we need to set up a resource agent that each node can run
      to determine if it owns the Floating IP and, if necessary, run a script to point the
      Floating IP to itself. We'll refer to the resource agent as "FloatIP OCF", and the
      Floating IP reassignment script as assign-ip. Once we have the FloatIP OCF resource
      agent installed, we can define the resource itself, which we'll refer to as FloatIP.

      On both servers, download python script, and use to reassign a Floating IP
      $ sudo curl -L -o /usr/local/bin/assign-ip http://do.co/assign-ip
      $ sudo chmod +x /usr/local/bin/assign-ip

      Pacemaker allows the addition of OCF resource agents by placing them in a specific
      directory, download FloatIP OCF resource agent, and use it to define FloatIP resouce
      $ sudo mkdir /usr/lib/ocf/resource.d/digitalocean
      $ sudo curl -o /usr/lib/ocf/resource.d/digitalocean/floatip \
      https://gist.githubusercontent.com/thisismitch/b4c91438e56bfe6b7bfb/raw/ \
      2dffe2ae52ba2df575baae46338c155adbaef678/floatip-ocf
      $ sudo chmod +x /usr/lib/ocf/resource.d/digitalocean/floatip

      13. Add FloatIP Resource to the cluster
      $ sudo crm configure primitive FloatIP ocf:digitalocean:floatip \
      $      params do_token=your_digitalocean_personal_access_token \
      $      floating_ip=your_floating_ip

      14. Test high availability using curl
      $ while true; do curl floating_IP_address; sleep 1; done
      $ sudo reboot

      15. Monitoring for troubleshooting
      $ sudo crm_mom
      $ sudo crm configure show
      $ sudo tail -f /var/log/corosync/corosync.log

    - HeartBeat
      HeartBeat ----------------------------------------------------------------------------
      Heartbeat is a daemon that provides cluster infrastructure (communication and membership)
      services to its clients. This allows clients to know about the presence (or disappearance!)
      of peer processes on other machines and to easily exchange messages with them.

      In order to be useful to users, the Heartbeat daemon needs to be combined with a
      cluster resource manager (CRM) which has the task of starting and stopping the services
      (IP addresses, web servers, etc.) that cluster will make highly available. Pacemaker is
      the preferred cluster resource manager for clusters based on Heartbeat.

      since up to release 2.1.4 the messaging layer (Heartbeat proper), the Local Resource
      Manager, "plumbing" infrastructure and STONITH (now known as Cluster Glue), the Resource
      Agents, and the Cluster Resource Manager (now Pacemaker) were all part of a single package
      named heartbeat, the name was often applied to the Linux-HA project as a whole.

      This generalization is no longer accurate, the name heartbeat should thus be used for
      the messaging layer exclusively.

      purpose as a cluster manager is to ensure that the cluster maintains its services to
      the clients, even if single machines of the cluster fail. Failover automation usually
      uses a "heartbeat" system which connects two servers, either through using a separate
      cable (for example, RS-232 serial ports/cable) or a network connection.

      Location where resource agent scripts are stored
      /usr/lib/heartbeat/ResourceManager

      Location where cluster configuration is stored.
      /etc/ha.d/

      Sample Heartbeat configuration:
      $ vi /etc/ha.d/ha.cf
      | # Node communication
      | bcast		eth0
      | udpport	694
      | autojoin	any
      | # Logging
      | debug		    0
      | coredumps	    true
      | logfacility	daemon
      | # Timeouts suitable for clusters with 2-8 nodes
      | keepalive	1
      | warntime	6
      | deadtime	10
      | minitdead	15
      |# Advanced settings, read the detailed configuration notes before changing
      | realtime			         yes
      | compression		 	     bz2
      | traditional_compression	 off

      Enabling pacemaker for heartbeat
      to enable the user of pacemaker with heartbeat, add following to ha.cf
      $ vi /et/ha.d/ha.cf
      | crm respawn

    - OpenAIS, CMAN (other cluster engines)
      OpenAIS ------------------------------------------------------------------------------
      is a messaging and membership layer. It tells programs like Pacemaker which nodes are
      part of the cluster and provides a way to send messages between them.

      OpenAIS provides cluster communications using the totem protocol.
      OpenAIS is the heart of the cluster. All other computers operate though this component,
      and no cluster component can work without it. Further, it is shared between both
      Pacemaker and RHCS clusters.

      In Red Hat clusters, openais is configured via the central cluster.conf file.
      In Pacemaker clusters, it is configured directly in openais.conf. As we will be
      building an RHCS, we will only use cluster.conf. That said, (almost?) all openais.conf
      options are available in cluster.conf(ccs system). This is important to note as you
      will see references to both configuration files when searching the Internet.

      Location where OpenAIS configuration is stored.
      /etc/ais/

      CMAN ---------------------------------------------------------------------------------
      is a symmetric, kernel-based cluster manager. It has two parts.
      Connection Manager(cnxman) handles membership, messaging, quorum, event notification
      and transitions. Service Manager(sm) handles "service groups" which are a way of
      representing & managing instances of external systems that require cluster management.


<br/><a name="ha_enterprise"></a>

### High Availability in Enterprise Linux Distributions

    ===============================================================================================
    334.4 High Availability in Enterprise Linux Distributions
    ===============================================================================================
    - RedHat
      Red Hat Enterprise Linux High Availability Add-On
      Essential cluster configuration in Red Hat using OpenAIS, corosync & tools below
      mkqdisk     : mkqdisk –l HA585 –c /dev/mapper/qdisk, cluster quorum disk utility
      qdiskd      : a disk-based quorum daemon
      ricci       : agent component of conga
      luci        : server component of conga
      cman        : manages the CMAN cluster manager.
      cman_tool   : provides the capability to join/leave/kill a cluster
      rgmanager   : checks the status of individual resources, not whole services(clumanger)
      fence_tool  : join or leave the default fence domain
      fence_apc   : I/O Fencing agent which can be used with the APC network power switch
      clustat     : displays the status of the cluster for HA service
      clusvcadm   : enable/disable/relocate/restart HA services in a cluster
      ccsd        : Cluster Configuration System (CCS) (/etc/cluster/cluster.conf)
      ccs_tool    : manage the cluster configuration information file

      Piranha : provides a daemon called 'lvs' that runs on the primary and backup nodes.
      process controls piranha and supports communication among its components.
      /etc/lvs.cf file contains the configuration information for piranha
      GFS     : Red Hat GFS is a cluster file system that allows a cluster of nodes to
      simultaneously access a block device that is shared among the nodes.
      conga   : is an agent/server architecture for remote administration of systems.
      The agent component is called “ricci”, and the server is called “luci”.
      GNBD    : Global Netwwork Block Device privicds block-level storage access over LAN
      GNBD run as a client in a GFS node and as a server in a GNBD server node

      Recently, Red Hat Enterprise Linux HA Add-On are updated with
      LVS, cVLM, Pacemaker and some of above tools from Red Hat Suite.

      With Red Hat Cluster, resources are prepared with IP, NFS, File Systems, but there are
      no authentication, that will be covered by SSL or advanced security issue.

      - SUSE
      SUSE Linux Enterprise High Availability Extension
      Pacemaker GUI : installable graphical user interface for administration of cluster
      Hawk : a Web interface allows you to monitor and administer cluster
      YaST : configuring IP Load Balancing and Network Bonding Devices with YaST
      Rear : disaster recovery

      - Distribution specific configuration tools
      Integration of cluster engines,
      load balancers, storage technology, cluster filesystems, etc.


<br/><a name="drbd"></a>

### DRBD / cLVM (Cluster Storage)

    ===============================================================================================
    335.1 DRBD / cLVM (Cluster Storage)   						      Weight: 3
    ===============================================================================================
    - DRBD (Distributed Replicated Block Device)
      is a software-based, shared-nothing, replicated storage solution mirroring the content
      of block devices (hard disks, partitions, logical volumes etc.) between servers.

      DRBD mirrors data
      Replication occurs continuously, while applications modify the data on the device.
      With synchronous mirroring, a writing application is notified of write completion only
      after the write has been carried out on both computer systems.
      Asynchronous mirroring can also possible.

      DRBD kernel module
      DRBD's core functionality is implemented by way of a Linux kernel module.
      DRBD constitutes a driver for a virtual block device, so DRBD is situated
      'right near the bottom' of a system's I/O stack

      User space administration tools
      • drbdadm   : drbdadm acts as a front-end application for both drbdsetup and drbdmeta
      • drbdsetup : allows users to configure the DRBD module(loaded into the running kernel)
      • drbdmeta  : allows users to create, dump, restore, and modify DRBD's data structures

      drbdadm cstate <resource>      : connection states
      drbdadm dstate <resource>      : disk states, backing storage devices
      drbdadm syncer <resource>	   : loads resynchronization parameters into device
      drbdadm role <resource>		   : Shows the current roles of the devices
      attach, detach, connect, sidsconnect, invalidate, up, down, primary, secondary,
      resize, dump, verify, pause-sync, resume-sync, adjust, wait-connect, etc...

      DRBD resources
      In DRBD, resource is the collective term that refers to all aspects of a particular
      replicated storage device, these include disk and network configuration and also
      Resource name : This can be any arbitrary, US-ASCII name not containing whitespace
      by which the resource is referred to.
      DRBD device   : This is the virtual block device managed by DRBD. It has a device
      major number of 147, and its minor numbers are numbered from 0 onwards,
      as is customary. The associated block device is always named
      /dev/drbd m, where m is the device minor number.

      DRBD resource roles
      In DRBD, every resource has a role, which may be Primary or Secondary.
      - A DRBD device in the primary role can be used unrestrictedly for read and write
      operations. It may be used for creating and mounting file systems, raw or direct I/O
      to the block device, etc.
      - A DRBD device in the secondary role receives all updates from the peer node's device,
      but otherwise disallows access completely. It can not be used by applications,
      neither for read nor write access. The reason for disallowing even read-only access
      to the device is the necessity to maintain cache coherency, which would be impossible
      if a secondary resource were made accessible in any way.

    - DRBD feutures
      Replication modes
      • Protocol A  Asynchronous replication protocol.
      • Protocol B  Memory synchronous (semi-synchronous) replication protocol.
      • Protocol C  Synchronous replication protocol. most commonly used protocol

      Three-way replication
      ! have a look graph https://www.drbd.org/en/doc/users-guide-83/s-three-way-repl
      DRBD adds a third node to an existing 2-node cluster and replicates data to that node,
      where it can be used for backup and disaster recovery purposes.
      Three-way replication works by adding another, stacked DRBD resource on top of the
      existing resource holding your production data
      Primary     : a primary cluster node manipulates the data at any moment
      Secondary   : a stanby cluster node, usually make use of protocol C
      Backup      : asynchronous replication by protocol A

    - DRBD configuration
      /etc/drbd.conf                  : is read by drbdadm
      /etc/drbd.d/global_common.conf  : global and common section option
      /etc/drbd.d/*.res               : resource sections

      # minimal setup!
      |global {
      |  usage-count yes;
      |}
      |common {
      |  protocol C;
      |}
      |resource r0 {
      | on alice {
      |    device    /dev/drbd1;
      |    disk      /dev/sda7;
      |   address   10.1.1.31:7789;
      |    meta-disk internal;
      |  }
      |  on bob {
      |    device    /dev/drbd1;
      |    disk      /dev/sda7;
      |    address   10.1.1.32:7789;
      |    meta-disk internal;
      |  }
      |}

      Status information in /proc/drbd
      $ cat /proc/drbd
      |version: 8.3.0 (api:88/proto:86-89)
      |GIT-hash: 9ba8b93e24d842f0 build by buildsystem@linbit, 2008-12-18 16:02:26
      |0: cs:Connected ro:Secondary/Secondary ds:UpToDate/UpToDate C r---
      |   ns:0 nr:8 dw:8 dr:0 al:0 bm:2 lo:0 pe:0 ua:0 ap:0 ep:1 wo:b oos:0
      |1: cs:Connected ro:Secondary/Secondary ds:UpToDate/UpToDate C r---
      |   ns:0 nr:12 dw:12 dr:0 al:0 bm:1 lo:0 pe:0 ua:0 ap:0 ep:1 wo:b oos:0
      |2: cs:Connected ro:Secondary/Secondary ds:UpToDate/UpToDate C r---
      |   ns:0 nr:0 dw:0 dr:0 al:0 bm:0 lo:0 pe:0 ua:0 ap:0 ep:1 wo:b oos:0
      |cs  (connection state).
      |ro  (roles).
      |ds  (disk states).
      |ns  (network send).
      |nr  (network receive).
      |dw  (disk write).
      |dr  (disk read).
      |al  (activity log).
      |bm  (bit map).
      |lo  (local count).
      |pe  (pending).
      |ua  (unacknowledged).
      |ap  (application pending).
      |ep  (epochs).
      |wo  (write order).
      |oos (out of sync).

    - LVM setup for DRBD
      vgchange - switching the Volume Group (VG) to the other node

      To make them available on the other node, first issue the following sequence of
      commands on the primary node:
      $ vgchange -a n replicated
      | 0 logical volume(s) in volume group "replicated" now active
      $ drbdadm secondary r0

      Then, issue these commands on the other (still secondary) node:
      $ drbdadm primary r0
      $ vgchange -a y replicated

      vgscan (vgs)
      scan all disks for volume groups and rebuild caches
      When you build a nested LVM configuration with DRBD, you have to set /etc/lvm/lvm.conf
      After modifying the lvm.conf file, you must run the vgscan command so LVM discards its
      configuration cache and re-scans devices for Physical Volume(PV) signatures.

    - DRBD with pacemaker
      Basic configuration
      The most common way to configure DRBD to replicate a volume between two fixed nodes,
      using IP addresses statically assigned on each.

      ---------------------------------------------------------------------------------------
      • Configuring the resource of pacemaker in the CIB(Cluster Information Base)
      Setting up DRBD with Pacemaker, we will assume that you've setup DRBD
      In the crm shell, you first have to create the primitive resource and then embed
      that into the master resource.
      $ crm
      | configure
      | primitive drbd0 ocf:heartbeat:drbd \
      |   params drbd_resource=drbd0 \
      |   op monitor role=Master interval=59s timeout=30s \
      |   op monitor role=Slave interval=60s timeout=30s
      | ms ms-drbd0 drbd0 \
      |   meta clone-max=2 notify=true globally-unique=false target-role=stopped
      | commit
      | quit

      The primitive DRBD resource, similar to what you would have used to configure drbddisk,
      is now embedded in a complex object master. This specifies the abilities and limitation
      of DRBD there can be only two instances (clone-max), one per node (clone-node-max),
      and only one master ever (master-max). The notify attribute specifies that DRBD needs
      to be told about what happens to its peer; globally-unique set to false lets Pacemaker
      know that the instances cannot be told apart on a single node.

      Note:  we're creating the resource in stopped state first, so that we can finish
      configuring its constraints and dependencies before activating it. Specifying the nodes
      where the DRBD RA can be run.

      If you want to run drbd0 on two out of more nodes only, you will have to tell the
      cluster about this constraint:
      $ crm configure location ms-drbd0-placement ms-drbd0 rule \
      -inf: \#uname ne xen-1 and \#uname ne xen-2
      This will tell the Policy Engine that, first, drbd0 can not run anywhere else except on
      xen-1 or xen-2. Second, it tells the PE that yes, it can run on those two.
      If your cluster is asymmetric, you will have to invert the rules (symmetric is default)

      ---------------------------------------------------------------------------------------
      • Prefering a node to run the master role
      With the configuration so far, the cluster would pick a node to promote DRBD on. If you
      want to prefer a node to run the master role (xen-1 in this example), then
      $ crm configure location ms-drbd0-master-on-xen-1 ms-drbd0 \
      rule role=master 100: \#uname eq xen-1

      You can now activate the DRBD resource:
      $ crm resource start ms-drbd0

      It should be started and promoted on one of the two nodes - or, if you specified a
      constraint as shown above, on the node you preferred.

      ---------------------------------------------------------------------------------------
      • Referencing the master or slave resource in constraints
      DRBD is rarely useful by itself; you will propably want to run a service on top of it.
      Or, very likely, you want to mount the filesystem on the master side.

      Let us assume that you've created an ext3 filesystem on /dev/drbd0, which you now want
      managed by the cluster as well. The filesystem resource object is straightforward, and
      if you have got any experience with configuring Pacemaker at all, will look rather familar:

      $ crm configure primitive fs0 ocf:heartbeat:Filesystem params fstype=ext3 \
      directory=/mnt/share1 device=/dev/drbd0 meta target-role=stopped

      Make sure that the various settings match your setup. Again, this object has been created
      as stopped first.

      Now the interesting bits. Obviously, the filesystem should only be mounted on the same
      node where drbd0 is in primary state, and only after drbd0 has been promoted, which is
      expressed in these two constraints:
      $ crm
      | configure
      | order ms-drbd0-before-fs0 mandatory: ms-drbd0:promote fs0:start
      | colocation fs0-on-ms-drbd0 inf: fs0 ms-drbd0:Master
      | commit
      | quit

      You now can activate the filesystem resource and it'll be mounted at the proper time
      in the proper place.
      $ crm resource start fs0

      Just as this was done with a single filesystem resource, this can be done with a group:
      In a lot of cases, you will not just want a filesystem, but also an IP-address and some
      sort of daemon to run on top of the DRBD master. Put those resources in a group,
      use the constraints above and replace fs0 with the name of your group. apache webserver
      $ crm
      | configure
      | primitive drbd0 ocf:heartbeat:drbd \
      |   params drbd_resource=drbd0 \
      |   op monitor role=Master interval=59s timeout=30s \
      |   op monitor role=Slave interval=60s timeout=30s
      | ms ms-drbd0 drbd0 \
      |   meta clone-max=2 notify=true globally-unique=false target-role=stopped
      | primitive fs0 ocf:heartbeat:Filesystem \
      |   params fstype=ext3 directory=/usr/local/apache/htdocs device=/dev/drbd0
      | primitive webserver ocf:heartbeat:apache \
      |   params configfile=/usr/local/apache/conf/httpd.conf \
      |     httpd=/usr/local/apache/bin/httpd port=80 \
      |   op monitor interval=30s timeout=30s
      | primitive virtual-ip ocf:heartbeat:IPaddr2 \
      |   params ip=10.0.0.1 broadcast=10.0.0.255 nic=eth0 cidr_netmask=24 \
      |   op monitor interval=21s timeout=5s
      | group apache-group fs0 webserver virtual-ip
      | order ms-drbd0-before-apache-group mandatory: ms-drbd0:promote apache-group:start
      | colocation apache-group-on-ms-drbd0 inf: apache-group ms-drbd0:Master
      | location ms-drbd0-master-on-xen-1 ms-drbd0 rule role=master 100: #uname eq xen-1
      | commit
      | end
      | resource start ms-drbd0
      | quit

      This will load the drbd module on both nodes and promote the instance on xen-1. After
      successful promotion, it will first mount /dev/drbd0 to /usr/local/apache/htdocs, then
      start the apache webserver and in the end configure the service IP-address 10.0.0.1/24
      on network card eth0.

      ---------------------------------------------------------------------------------------
      Moving the master role to a different node
      If you want to move the DRBD master role the other node, you should not attempt to just
      move the master role. On top of DRBD, you will propably have a Filesystem resource or a
      resource group with your application/Filesystem/IP-Address or whatever (remember, DRBD
      isn't usually useful by itself). If you want to move the master role, you can
      accomplish that by moving the resource that is co-located with the DRBD master (and
      properly ordered). This can be done with the crm shell or crm_resource. Given the group
      example from above, you would use

      $ crm resource migrate apache-group [hostname]

      This will stop all resources in the group, demote the current master, promote the other
      DRBD instance and start the group after successful promotion.

      ---------------------------------------------------------------------------------------
      Keeping the master role on a network connected node
      It is most likely desirable to keep the master role on a node with a working network
      connection. I assume you are familiar with [pingd]. So if you configured pingd, all you
      need to do is a rsc_location constraint for the master role, which looks at the pingd
      attribute of the node.

      $ crm configure location ms-drbd-0_master_on_connected_node ms-drbd0 \
      rule role=master -inf: not_defined pingd or pingd lte 0

      This will force the master role off of any node with a pingd attribute value of less or
      equal 0 or without a pingd attribute at all.
      Note: This will prevent the master role and all its colocated resources from running at
      all if all your nodes lose network connection to the ping nodes.
      If you don't want that, you can also configure a different score value than -INFINITY,
      but that requires cluster-individual score-maths depending on your number of resources,
      stickiness values and constraint scores.

    - DRBD with HeartBeat
      ---------------------------------------------------------------------------------------
      Heartbeat R1-style configuration
      In R1-style clusters, Heartbeat keeps its complete configuration files:
      /etc/ha.d/ha.cf       : configuration file for the Heartbeat cluster messaging layer
      /etc/ha.d/authkeys    : contains pre-shared secrets used for authentication
      /etc/ha.d/haresources : the resource configuration file

      Here is an example of haresources file, which is a Heartbeat R1-compatible resource
      configuration involving a MySQL backed by DRBD:
      $ vi /etc/ha.d/haresources
      | bob drbddisk::mysql Filesystem::/dev/drbd0::/var/lib/mysql::ext3 10.9.42.1 mysql

      This resource configuration contains one resource group whose home node (the node where
      its resources are expected to run under normal circumstances) is named bob.
      Consequentially, this resource group would be considered the local resource group on
      host bob, whereas it would be the foreign resource group on its peer host.

      The resource group includes a DRBD resource named mysql, which will be promoted to the
      primary role by the cluster manager (specifically, the drbddisk resource agent) on
      whichever node is currently the active node. Of course, a corresponding resource must
      exist and be configured in /etc/drbd.conf for this to work.

      That DRBD resource translates to the block device named /dev/drbd0, which contains an
      ext3 filesystem that is to be mounted at /var/lib/mysql(default location of MySQL data)
      The resource group also contains a service IP address, 10.9.42.1. Heartbeat will make
      sure that this IP is configured and available on whichever node is currently active.

      Finally, Heartbeat will use the LSB resource agent named mysql in order to start the
      MySQL daemon, which will then find its data files at /var/lib/mysql and be able to
      listen on the service IP address, 192.168.42.1.

      It is important to understand that the resources listed in the haresources file are
      always evaluated from left to right when resources are being started, and from right
      to left when they are being stopped.

      ---------------------------------------------------------------------------------------
      Heartbeat CRM configuration
      In CRM clusters, Heartbeat keeps part of configuration in the following config files:
      $ vi /etc/ha.d/ha.cf
      | crm yes         : to enable CRM mode

      Creating authentication keys
      $ echo -ne "auth 1\n1 sha1 "; \
      dd if=/dev/urandom bs=512 count=1 | openssl md5 ) > /etc/ha.d/authkeys
      $ chmod 0600 /etc/ha.d/authkeys

      Contrary to the two relevant configuration files, the CIB need not be manually
      distributed among cluster nodes, the Heartbeat services take care of that automatically

      The CIB is kept in one XML file, /var/lib/heartbeat/crm/cib.xml.
      It is, however, not recommended to edit the contents of this file directly, except in
      the case of creating a new cluster configuration from scratch. Instead, Heartbeat comes
      with both command-line applications and a GUI to modify the CIB. The CIB actually
      contains both the cluster configuration (which is persistent and is in the cib.xml),
      and information about the current cluster status (which is volatile).

      After creating a new Heartbeat CRM cluster — that is, creating the ha.cf and authkeys,
      distributing them among cluster nodes, starting Heartbeat services, and waiting for
      nodes to establish intra-cluster communications — a new, empty CIB is created
      automatically. Its contents will be similar to this:
      $ vi /var/lib/heartbeat/crm/cib.xml
      |<cib>
      |  <configuration>
      |    <crm_config>
      |      <cluster_property_set id="cib-bootstrap-options">
      |        <attributes/>
      |      </cluster_property_set>
      |    </crm_config>
      |    <nodes>
      |      <node uname="aha" type="normal" id="f11899c3-ed6e-4e63-abae-b9af90c62283"/>
      |      <node uname="bob" type="normal" id="663bae4d-44a0-407f-ac14-389150407159"/>
      |    </nodes>
      |    <resources/>
      |    <constraints/>
      |  </configuration>
      |</cib>
      this cluster has two nodes named aha and bob, and that neither any resources nor any
      resource constraints have been configured at this point.

      How to enable a DRBD-backed service in a Heartbeat CRM cluster.
      Using the drbddisk resource agent (a DRBD-backed service) in a Heartbeat CRM config.
      Even though you are using Heartbeat in CRM mode, you may still utilize R1-compatible
      resource agents such as drbddisk. This resource agent provides no secondary node
      monitoring, and ensures only resource promotion and demotion.
      $ vi /tmp/hb_mysql.xml
      |<group ordered="true" collocated="true" id="rg_mysql">
      | ...
      | ! a complicated xml code.... :)
      | ...
      |</group>
      Add above resource to the cluster configuration using command (on any cluster node):
      $ cibadmin -o resources -C -x /tmp/hb_mysql.xml

      After this, Heartbeat will automatically propagate the newly-configured resource group
      to all nodes. Using the drbd OCF resource agent in a Heartbeat CRM configuration

      The drbd resource agent is a “pure-bred” OCF RA which provides Master/Slave capability,
      allowing Heartbeat to start and monitor the DRBD resource on multiple nodes and
      promoting and demoting as needed. You must, however, understand that the drbd RA
      disconnects and detaches all DRBD resources it manages on Heartbeat shutdown, and also
      upon enabling standby mode for a node.

      In order to enable a DRBD-backed configuration for a MySQL database in a Heartbeat
      CRM cluster with the drbd OCF resource agent, must create both the necessary resources,
      and Heartbeat constraints to ensure your service only starts on a previously promoted
      DRBD resource. It is recommended that you start with the constraints
      $ vi /tmp/constraint.xml
      |<constraints>
      |  <rsc_order id="mysql_after_drbd" from="rg_mysql" action="start"
      |             to="ms_drbd_mysql" to_action="promote" type="after"/>
      |  <rsc_colocation id="mysql_on_drbd" to="ms_drbd_mysql"
      |             to_role="master" from="rg_mysql" score="INFINITY"/>
      |</constraints>
      |
      $ cibadmin -U -x /tmp/constraints.xml

      Subsequently, you would create your relevant resources:
      |<resources>
      |  <master_slave id="ms_drbd_mysql">
      |  ...
      |  </master_slave>
      |  <group id="rg_mysql">
      |  ...
      |  </group>
      |</resources>
      $ cibadmin -U -x /tmp/resource.xml
      Heartbeat now selects a node on which it promotes the DRBD resource, and then starts
      the DRBD-backed resource group on that same node.

    - cLVM
      The Cluster Logical Volume Manager (CLVM) provides a cluster-wide version of LVM2.
      CLVM provides the same capabilities as LVM2 on a single node, but makes the volumes
      available to all nodes in a Red Hat cluster. The logical volumes created with CLVM
      make logical volumes available to all nodes in a cluster.

      The key component in CLVM is clvmd. clvmd is a daemon that provides clustering
      extensions to the standard LVM2 tool set and allows LVM2 to manage shared storage.
      The clvmd daemon runs in each cluster computer and distributes LVM metadata updates in
      a cluster, presenting each cluster computer with the same view of the logical volumes.

      If you want to manage LVM in "NO cluster mode", then you should configure lvm.conf
      $ vi /etc/lvm/lvm.conf
      | config{ }
      | device{ }
      | allocation{ }
      | log{ }
      | shell{ }
      | global{ }
      | activation{ }
      | metadata{ }
      | dmeventd{ }

    - cLVM + pacemaker
      normally cLVM can cooperate with GFS2 & pacemaker together.
      CLVM is a set of clustering extensions to LVM. These extensions allow a cluster of
      computers to manage shared storage using LVM

      clustered LVM logical volumes are supported only in conjunction with Pacemaker
      clusters, and must be configured as cluster resources.



<br/><a name="clustered_filesystem"></a>

### Clustered File Systems

    ===============================================================================================
    335.2 Clustered File Systems
    ===============================================================================================
    - GFS2
      Global File System 2 (GFS2) is a shared disk file system for Linux clusters.
      GFS2 differs from distributed file systems (such as AFS, Coda, or InterMezzo) because
      GFS2 allows all nodes to have direct concurrent access to the same shared block storage
      In addition, GFS or GFS2 can also be used as a local filesystem.

      GFS has no disconnected operating-mode, and no client or server roles. All nodes in a
      GFS cluster function as peers. Using GFS in a cluster requires hardware to allow access
      to the shared storage, and a lock manager to control access to the storage. The lock
      manager operates as a separate module: thus GFS and GFS2 can use the Distributed Lock
      Manager (DLM) for cluster configurations and the “nolock” lock manager for local
      filesystems. Older versions of GFS also support GULM, a server based lock manager which
      implements redundancy via failover.

    - Legacy GFS2
      1. Install the GFS2
      $ yum -y install modcluster rgmanager gfs2 gfs2-utils lvm2-cluster cman

      2. Second step is to create a cluster on gfs1
      $ ccs_tool create GFStestCluster

      3. Add the fencing devices in every node (use Cluster Configuration System Tool)
      $ ccs_tool addfence -C gfs1_vmware fence_vmware ipaddr=esxtest login=esxuser \
      passwd=esxpass vmlogin=root vmpasswd=esxpass \
      port=”/vmfs/volumes/49086551-c64fd83c-0401-001e0bcd6848/eagle1/gfs1.vmx”
      $ ccs_tool addfence -C gfs2_vmware fence_vmware ipaddr=esxtest login=esxuser \
      passwd=esxpass vmlogin=root vmpasswd=esxpass \
      port=”vmfs/volumes/49086551-c64fd83c-0401-001e0bcd6848/gfs2/gfs2.vmx”

      4. Add the nodes
      $ ccs_tool addnode -C gfs1 -n 1 -v 1 -f gfs1_vmware
      $ ccs_tool addnode -C gfs2 -n 2 -v 1 -f gfs2_vmware

      5. copy this configuration over to the other nodes from gfs1
      $ scp /etc/cluster/cluster.conf root@gfs2:/etc/cluster/cluster.conf

      6. verify the config on all nodes by running
      $ ccs_tool lsnode
      $ ccs_tool lsfence

      7. ready to start up daemons on all the nodes in the cluster
      $ /etc/init.d/cman start
      $ /etc/init.d/rgmanager start

      8. check the status of cluster by running
      $ clustat
      $ cman_tool status

      9. test the vmware fencing (run command below on node-1 and use node-2 as fencing)
      $ fence_vmware -a esxtest -l esxuser -p esxpass -L root -P esxpass -v \
      -n “/vmfs/volumes/49086551-c64fd83c-0401-001e0bcd6848/gfs2/gfs2.vmx”

      10. enable clustering in LVM2
      $ lvmconf –enable-cluster

      11. create the LVM2 Volume
      $ pvcreate MyTestGFS /dev/sdb
      $ vgcreate -c y mytest_gfs2 /dev/sdb
      $ lvcreate -n MyGFS2test -L 5G my
      $ /etc/init.d/clvmd start         <----   start clvmd also on the other nodes.

      13. create the GFS2 file system
      $ mkfs -t <filesystem> -p <locking mechanism> -t <ClusterName>:<PhysicalVolumeName> \
      -j <JournalsNeeded == amount of nodes in cluster> <location of filesystem>
      $ mkfs -t gfs2 -p lock_dlm -t MyCluster:MyTestGFS -j 4 /dev/mapper/my_GFS2

      14. mount the GFS2 file system on all nodes.
      $ mount /dev/mapper/my_GFS2 /mnt/

      15. Once you mounted your GFS2 file system
      $ gfs2_tool list
      $ gfs2_tool df

      16. wrap it up and all the necessary daemons start up with the cluster
      $ chkconfig --level 345 rgmanager on
      $ chkconfig --level 345 clvmd on
      $ chkconfig --level 345 cman on
      $ chkconfig --level 345 gfs2 on

      17. GFS2 file system to be mounted at startup, add to /etc/fstab.
      $ echo “/dev/mapper/my_GFS2 /GFS gfs2 defaults,noatime,nodiratime 0 0″ >> /etc/fstab

      - Tools for gfs2
      mkfs.gfs2    : make a GFS2 file system
      mount.gfs2   : mount GFS2
      fsck.gfs2    : check GFS2
      gfs2_grow    : expand a GFS2 after device where file system resides has been expanded
      gfs2_edit    : edit or display GFS2/GFS file system
      gfs2_jadd    : add journals to a GFS2 file system
      gfs2_convert : convert GFS to GFS2

      - GFS2 + cLVM via pacemaker
      Install required packages on all Nodes.
      $ yum -y install fence-agents-all lvm2-cluster gfs2-utils
      $ lvmconf --enable-cluster
      $ reboot

      Configure fence device(just in one node)
      $ cat /proc/partitions
      $ ll /dev/disk/by-id | grep sda
      $ pcs stonith create scsi-shooter fence_scsi devices=/dev/disk/by-id/\
      wwn-0x6001405189b893893594dffb3a2cb3e9 meta provides=unfencing
      $ pcs property set no-quorum-policy=freeze
      $ pcs stonith show scsi-shooter

      Add required resources.
      $ pcs resource create dlm ocf:pacemaker:controld op monitor \
      interval=30s on-fail=fence clone interleave=true ordered=true
      $ pcs resource create clvmd ocf:heartbeat:clvm op monitor \
      interval=30s on-fail=fence clone interleave=true ordered=true
      $ pcs constraint order start dlm-clone then clvmd-clone
      $ pcs constraint colocation add clvmd-clone with dlm-clone
      $ pcs status resources

      Create volumes on shared storage and format with GFS2.
      $ pvcreate /dev/sdb1
      $ vgcreate -cy vg_cluster /dev/sdb1
      $ lvcreate -l100%FREE -n lv_cluster vg_cluster
      $ mkfs.gfs2 -p lock_dlm -t ha_cluster:gfs2 -j 2 /dev/vg_cluster/lv_cluster

      Add shared storage to cluster resource.
      $ pcs resource create fs_gfs2 Filesystem \
      device="/dev/vg_cluster/lv_cluster" directory="/mnt" fstype="gfs2" \
      options="noatime,nodiratime" op monitor interval=10s on-fail=fence \
      clone interleave=true
      $ pcs resource show
      $ pcs constraint order start clvmd-clone then fs_gfs2-clone
      $ pcs constraint colocation add fs_gfs2-clone with clvmd-clone
      $ pcs constraint show

      Mounted GFS2 file system to the cluster!
      [root@node01]# df -hT
      | Filesystem                        Type      Size  Used Avail Use% Mounted on
      | /dev/mapper/centos-root           xfs        27G  1.1G   26G   4% /
      | devtmpfs                          devtmpfs  2.0G     0  2.0G   0% /dev
      | tmpfs                             tmpfs     2.0G   76M  1.9G   4% /dev/shm
      | tmpfs                             tmpfs     2.0G  8.4M  2.0G   1% /run
      | tmpfs                             tmpfs     2.0G     0  2.0G   0% /sys/fs/cgroup
      | /dev/vda1                         xfs       497M  126M  371M  26% /boot
      | /dev/mapper/vg_cluster-lv_cluster gfs2     1016M  259M  758M  26% /mnt

    - OCFS2
      Oracle Cluster File System (OCFS2 as a 2nd edition) is a shared disk file system
      OCFS2 use a distributed lock manager which resembles OpenVMS DLM but is much simpler

      Cluster-aware applications can make use of cache-coherent parallel I/Os from multiple
      nodes to scale out applications easily. Other applications can make use of the file
      system facilities to failover running application in the event of a node failure.
      OCFS2 was added to SUSE Linux Enterprise Server 9 to support Oracle application
      Support for up to 255 cluster nodes
      /etc/ocfs2/cluster.conf

    - tools for ocfs2
      mkfs.ocfs2    : make a OCFS2 file system
      mount.ocfs2   : mount a OCFS2 file system
      fsck.ocfs2    : check a OCFS2 file system
      tunefs.ocfs2  : Change OCFS2 file system parameters.
      mounted.ocfs2 : detects all OCFS2 volumes on a system.
      o2info        : dump OCFS2 file system information on disk.
      o2image       : copy or restore OCFS2 file system meta-data
      ocfs2console  : console for the ocfs2 file system
      o2cb_ctl      : control program for the O2CB cluster service

    - O2CB
      manages the shared file access within a cluster of servers, and is a simple set of
      clustering services required to get OCFS2 going.
      Five file system services of the O2CB cluster stack:
      Node Manager, Heartbeat, TCP, DLM & DLMFS, CONFIGFS

    - OSFS2 + pacemaker
      Setting up OCFS2 in Pacemaker requires configuring the Pacemaker DLM, the O2CB lock
      manager for OCFS2, and an OCFS2 filesystem itself.
      Prerequisites
      - OCFS2 with Pacemaker integration is supported on Debian (squeeze-backports)
      and Ubuntu (10.04 LTS), need the dlm-pcmk, ocfs2-tools, and ocfs2-tools-pacemaker.
      - Fencing is imperative. Get a proper fencing/STONITH configuration set up
      and test it thoroughly.
      - Running OCFS2/Pacemaker integration requires that you start your corosync processes
      with the following insanely-named environment variable:
      COROSYNC_DEFAULT_CONFIG_IFACE=“openaisserviceenableexperimental:corosync_parser”
      You'll have to export it from /etc/default/corosync, which corosync init script.

      Pacemaker configuration, shown here in crm shell syntax
      | primitive p_dlm_controld ocf:pacemaker:controld \
      |   op start interval="0" timeout="90" \
      |   op stop interval="0" timeout="100" \
      |   op monitor interval="10"
      | primitive p_o2cb ocf:pacemaker:o2cb \
      |   op start interval="0" timeout="90" \
      |   op stop interval="0" timeout="100" \
      |   op monitor interval="10"
      | primitive p_fs_ocfs2 ocf:heartbeat:Filesystem \
      |   params device="<your device path>" \
      |     directory="<your mount point>" \
      |     fstype="ocfs2" \
      |   meta target-role=Stopped \
      |   op monitor interval="10"
      | group g_ocfs2 p_dlm_controld p_o2cb p_fs_ocfs2
      | clone cl_ocfs2 g_ocfs2 \
      |   meta interleave="true"

      primitive 1. Pacemaker’s interface to the DLM
      primitive 2. Pacemaker’s interface to OCFS2 cluster management
      primitive 3. the generic filesystem management resource agent which supports cluster
      file systems when configured as a Pacemaker clone.

    - DLM (Distributed Lock Manager)
      A distributed lock manager (DLM) provides distributed software applications with a
      means to synchronize their accesses to shared resources. A lock manager is a traffic
      cop who controls access to resources in the cluster, such as access to a GFS file
      system. Without a lock manager, there would be no control over access to your shared
      storage, and the nodes in the cluster would corrupt each other's data.

      Lock management is a common cluster-infrastructure service that provides a mechanism
      for other cluster infrastructure components to synchronize their access to shared
      resources. In a Red Hat cluster, DLM (Distributed Lock Manager) is the lock manager.
      DLM is distributed across all nodes in the cluster. GFS2 uses locks from the DLM to
      synchronize access to file system metadata (on shared storage). CLVM uses locks from
      the DLM to synchronize updates to LVM volumes and volume groups (on shared storage).
      In addition, rgmanager uses DLM to synchronize service states.

      DLM Locking Model
      The DLM locking model provides a rich set of locking modes and both synchronous and a
      synchronous execution. An application acquires a lock on a lock resource. A one-to-many
      relationship exists between lock resources and locks: a single lock resource can have
      multiple locks associated with it.

      A lock resource can correspond to an actual object, such as a file, a data structure,
      a database, or an executable routine, but it does not have to correspond to one of
      these things. The object you associate with a lock resource determines the granularity
      of the lock. For example, locking an entire database is considered locking at coarse
      granularity. Locking each item in a DB is considered locking at a fine granularity.

      The DLM locking model supports:
      • Six locking modes that increasingly restrict access to a resource
      • The promotion and demotion of locks through conversion
      • Synchronous completion of lock requests
      • Asynchronous completion
      • Global data through lock value blocks

      The DLM provides its own mechanisms to support its locking features, such as inter-node
      communication to manage lock traffic and recovery protocols to re-master locks after a
      node failure or to migrate locks when a node joins the cluster. However, the DLM does
      not provide mechanisms to actually manage the cluster itself. Therefore the DLM expects
      to operate in a cluster in conjunction with another cluster infrastructure environment
      that provides the following minimum requirements:
      • The node is a part of a cluster.
      • All nodes agree on cluster membership and has quorum.
      • An IP address must communicate with the DLM on a node. Normally the DLM uses TCP/IP
      for inter-node communications which restricts it to a single IP address per node
      (though this can be made more redundant using the bonding driver). The DLM can be
      configured to use SCTP as its inter-node transport which allows multiple IPs per node

      The DLM works with any cluster infrastructure environments that provide the minimum
      requirements listed above. The choice of an open source or closed source environment is
      up to the user. However, the DLM’s main limitation is the amount of testing performed
      with different environments.

      Lock States
      indicates the current status of a lock request. A lock is always in one of three states
      • Granted    — The lock request succeeded and attained the requested mode.
      • Converting — A client attempted to change the lock mode and the new mode is
      incompatible with an existing lock.
      • Blocked    — request for a new lock could not be granted because locks exist.
      A lock's state is determined by its requested mode and the modes of the other locks
      on the same resource.

    - Other clustered file systems
      Coda
      is a distributed file system developed at Carnegie Mellon, It descended directly from
      an older version of AFS (AFS-2) and offers many similar features. The InterMezzo file
      system was inspired by Coda. Coda is still under development, though the focus has
      shifted from research to creating a robust product for commercial use.

      CephFS
      POSIX standard filesystem that uses a Ceph Storage Cluster to store data
      The Ceph File System use the same Ceph Storage Cluster system as the Ceph Block Device,
      Ceph Object Gateway, or librados API.
      Ceph’s rados block device (RBD) is the block storage component of Ceph.
      Object Storage Device (OSD) is a physical or logical storage unit (e.g., LUN) in Ceph.

      GlusterFS (RedHat)
      GlusterFS is a scale-out NAS file system. It is free software, with some parts licensed
      under the GNU GPL v3 while others are dual licensed under either GPL v2 or the LGPL v3.
      It aggregates various storage servers over Ethernet or Infiniband RDMA interconnect
      into one large parallel network file system. GlusterFS is based on a stackable user
      space design. It has found a variety of applications including cloud computing,
      streaming media services, and content delivery networks. GlusterFS was developed
      originally by Gluster, Inc., then by Red Hat, Inc., after their purchase of Gluster

      AFS (Andrew File System)
      a distributed networked file system which uses a set of trusted servers to present a
      homogeneous, location-transparent file name space to all the client workstations.
      It was developed by Carnegie Mellon as part of the Andrew Project. Its primary use is
      in distributed computing.
      AFS uses Kerberos for authentication, and implements access control lists on
      directories for users and groups.

      lustre
      Lustre is a type of parallel distributed file system, generally used for large-scale
      cluster computing.

      HDFS
      The Hadoop distributed file system (HDFS) is a distributed, scalable, and portable
      file system written in Java for the Hadoop framework.

      DFS
      Distributed file System (DFS) is a set of client and server services that allow an
      organization using Microsoft Windows servers to organize many distributed SMB file
      shares into a distributed file system

      EVMS volume
      Enterprise Volume Management System, both LVM and EVMS were competing for inclusion
      in the mainline kernel.

    - GPFS
      IBM General Parallel File System provides high performance by allowing data to be
      accessed over multiple computers at once. Most existing file systems are designed for a
      single server environment, and adding more file servers does not improve performance.
      GPFS provides higher input/output performance by striping blocks of data from
      individual files over multiple disks, and reading and writing these blocks in parallel.
      Other features provided by GPFS include high availability, support for heterogeneous
      clusters, disaster recovery, security, DMAPI, HSM and ILM.

      To prevent data loss, the filesystem nodes have RAID controllers — multiple copies of
      each block are written to the physical disks on the individual nodes. It is also
      possible to opt out of RAID-replicated blocks, and instead store two copies of each
      block on different filesystem nodes.

      Other features of the filesystem include

      Distributed metadata, including the directory tree. There is no single "directory
      controller" or "index server" in charge of the filesystem.
      Efficient indexing of directory entries for very large directories. Many filesystems
      are limited to a small number of files in a single directory (often, 65536 or a similar
      small binary number). GPFS does not have such limits.
      Distributed locking. This allows for full Posix filesystem semantics, including locking
      for exclusive file access.
      Partition Aware. A failure of the network may partition the filesystem into two or
      more groups of nodes that can only see the nodes in their group. This can be detected
      through a heartbeat protocol, and when a partition occurs, the filesystem remains live
      for the largest partition formed. This offers a graceful degradation of the filesystem.
      Filesystem maintenance can be performed online. Most of the filesystem maintenance
      chores (adding new disks, rebalancing data across disks) can be performed while the
      filesystem is live. This ensures the filesystem is available more often, so keeps the
      supercomputer cluster itself available for longer.

      It is interesting to compare this with Hadoop's HDFS filesystem, which is designed to
      store similar or greater quantities of data on commodity hardware — that is,
      datacenters without RAID disks and a Storage Area Network (SAN).


      HDFS does not expect reliable disks, so instead stores copies of the blocks on
      different nodes. The failure of a node containing a single copy of a block is a minor
      issue, dealt with by re-replicating another copy of the set of valid blocks, to bring
      the replication count back up to the desired number. In contrast, while GPFS supports
      recovery from a lost node, it is a more serious event, one that may include a higher
      risk of data being (temporarily) lost.
      GPFS supports full Posix filesystem semantics. HDFS and GFS do not support full Posix
      compliance.
      GPFS distributes its directory indices and other metadata across the filesystem.
      Hadoop, in contrast, keeps this on the Primary and Secondary Namenodes, large servers
      which must store all index information in-RAM.
      GPFS breaks files up into small blocks. Hadoop HDFS likes blocks of 64 MB or more,
      as this reduces the storage requirements of the Namenode. Small blocks or many small
      files fill up a filesystem's indices fast, so limit the filesystem's size.
