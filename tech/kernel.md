imlcm@Linux启动过程分析
---
- [关心的问题](#关心的问题)
  - [linux内核启动过程中创建登录会话的时候会执行哪些操作](#linux内核启动过程中创建登录会话的时候会执行哪些操作)
  - [conda activate  执行后，会执行哪些操作](#conda-activate--执行后会执行哪些操作)
- [dmesg命令查看到的内核日志](#dmesg命令查看到的内核日志)
- [文心一言分析](#文心一言分析)
- [文心分析2](#文心分析2)

---


# 关心的问题

## linux内核启动过程中创建登录会话的时候会执行哪些操作

在Linux内核启动过程中，创建登录会话时，通常会执行以下操作：

1. **会话领头进程创建**：登录时第一个创建的进程是shell（如/bin/bash），这个进程就是会话的领头进程。会话领头进程的PID通常与会话ID（SID）相同。
2. **终端设备分配**：领头进程默认处于一个前台进程组中，并打开一个终端设备，等待数据的读写（例如，执行命令或程序）。
3. **执行用户配置文件**：在登录Linux时，首先会启动`/etc/profile`文件，然后再启动用户目录下的`~/.bash_profile`、`~/.bash_login`或`~/.profile`文件中的其中一个。执行的顺序通常为：`~/.bash_profile`、`~/.bash_login`、`~/.profile`。如果`~/.bash_profile`文件存在，一般还会执行`~/.bashrc`文件。
4. **会话环境设置**：根据用户配置文件中的设置，系统会配置会话的环境变量、别名、函数等。
5. **启动其他登录脚本**：根据用户的配置，可能还会执行其他登录脚本或程序，以进一步定制会话环境。
6. **等待用户输入**：一旦会话环境设置完成，系统就会等待用户输入命令或执行其他操作。

请注意，具体的操作可能会因不同的Linux发行版、shell类型以及用户的个人配置而有所差异。因此，在实际操作中，你可能需要查阅特定系统的文档或咨询系统管理员以获取更详细的信息。


---
# dmesg命令查看到的内核日志
[    0.000000] Linux version 5.15.146.1-microsoft-standard-WSL2 (root@65c757a075e2) (gcc (GCC) 11.2.0, GNU ld (GNU Binutils) 2.37) #1 SMP Thu Jan 11 04:09:03 UTC 2024
[    0.000000] Command line: initrd=\initrd.img WSL_ROOT_INIT=1 panic=-1 nr_cpus=8 hv_utils.timesync_implicit=1 console=hvc0 debug pty.legacy_count=0
[    0.000000] KERNEL supported cpus:
[    0.000000]   Intel GenuineIntel
[    0.000000]   AMD AuthenticAMD
[    0.000000]   Centaur CentaurHauls
[    0.000000] BIOS-provided physical RAM map:
[    0.000000] BIOS-e820: [mem 0x0000000000000000-0x000000000009ffff] usable
[    0.000000] BIOS-e820: [mem 0x00000000000e0000-0x00000000000e0fff] reserved
[    0.000000] BIOS-e820: [mem 0x0000000000100000-0x00000000001fffff] ACPI data
[    0.000000] BIOS-e820: [mem 0x0000000000200000-0x00000000f7ffffff] usable
[    0.000000] BIOS-e820: [mem 0x0000000100000000-0x00000002019fffff] usable
[    0.000000] NX (Execute Disable) protection: active
[    0.000000] DMI not present or invalid.
[    0.000000] Hypervisor detected: Microsoft Hyper-V
[    0.000000] Hyper-V: privilege flags low 0xae7f, high 0x3b8030, hints 0xa4e24, misc 0xe4bed7b6
[    0.000000] Hyper-V Host Build:22621-10.0-3-0.3447
[    0.000000] Hyper-V: Nested features: 0x3e0101
[    0.000000] Hyper-V: LAPIC Timer Frequency: 0x1e8480
[    0.000000] Hyper-V: Using hypercall for remote TLB flush
[    0.000000] clocksource: hyperv_clocksource_tsc_page: mask: 0xffffffffffffffff max_cycles: 0x24e6a1710, max_idle_ns: 440795202120 ns
[    0.000003] tsc: Detected 2803.214 MHz processor
[    0.000012] e820: update [mem 0x00000000-0x00000fff] usable ==> reserved
[    0.000014] e820: remove [mem 0x000a0000-0x000fffff] usable
[    0.000016] last_pfn = 0x201a00 max_arch_pfn = 0x400000000
[    0.000038] x86/PAT: Configuration [0-7]: WB  WC  UC- UC  WB  WP  UC- WT  
[    0.000048] last_pfn = 0xf8000 max_arch_pfn = 0x400000000
[    0.000055] Using GB pages for direct mapping
[    0.000099] RAMDISK: [mem 0x03dd8000-0x03faefff]
[    0.000102] ACPI: Early table checksum verification disabled
[    0.000106] ACPI: RSDP 0x00000000000E0000 000024 (v02 VRTUAL)
[    0.000109] ACPI: XSDT 0x0000000000100000 000044 (v01 VRTUAL MICROSFT 00000001 MSFT 00000001)
[    0.000114] ACPI: FACP 0x0000000000101000 000114 (v06 VRTUAL MICROSFT 00000001 MSFT 00000001)
[    0.000118] ACPI: DSDT 0x00000000001011B8 01E191 (v02 MSFTVM DSDT01   00000001 MSFT 05000000)
[    0.000121] ACPI: FACS 0x0000000000101114 000040
[    0.000124] ACPI: OEM0 0x0000000000101154 000064 (v01 VRTUAL MICROSFT 00000001 MSFT 00000001)
[    0.000127] ACPI: SRAT 0x000000000011F349 000330 (v02 VRTUAL MICROSFT 00000001 MSFT 00000001)
[    0.000129] ACPI: APIC 0x000000000011F679 000088 (v04 VRTUAL MICROSFT 00000001 MSFT 00000001)
[    0.000131] ACPI: Reserving FACP table memory at [mem 0x101000-0x101113]
[    0.000133] ACPI: Reserving DSDT table memory at [mem 0x1011b8-0x11f348]
[    0.000133] ACPI: Reserving FACS table memory at [mem 0x101114-0x101153]
[    0.000134] ACPI: Reserving OEM0 table memory at [mem 0x101154-0x1011b7]
[    0.000135] ACPI: Reserving SRAT table memory at [mem 0x11f349-0x11f678]
[    0.000135] ACPI: Reserving APIC table memory at [mem 0x11f679-0x11f700]
[    0.000912] Zone ranges:
[    0.000914]   DMA      [mem 0x0000000000001000-0x0000000000ffffff]
[    0.000928]   DMA32    [mem 0x0000000001000000-0x00000000ffffffff]
[    0.000929]   Normal   [mem 0x0000000100000000-0x00000002019fffff]
[    0.000930]   Device   empty
[    0.000931] Movable zone start for each node
[    0.000932] Early memory node ranges
[    0.000932]   node   0: [mem 0x0000000000001000-0x000000000009ffff]
[    0.000934]   node   0: [mem 0x0000000000200000-0x00000000f7ffffff]
[    0.000935]   node   0: [mem 0x0000000100000000-0x00000002019fffff]
[    0.000936] Initmem setup node 0 [mem 0x0000000000001000-0x00000002019fffff]
[    0.001372] On node 0, zone DMA: 1 pages in unavailable ranges
[    0.001410] On node 0, zone DMA: 352 pages in unavailable ranges
[    0.020452] On node 0, zone Normal: 26112 pages in unavailable ranges
[    0.020497] ACPI: LAPIC_NMI (acpi_id[0x01] dfl dfl lint[0x1])
[    0.020796] IOAPIC[0]: apic_id 8, version 17, address 0xfec00000, GSI 0-23
[    0.020800] ACPI: INT_SRC_OVR (bus 0 bus_irq 9 global_irq 9 high level)
[    0.020805] ACPI: Using ACPI (MADT) for SMP configuration information
[    0.020806] TSC deadline timer available
[    0.020807] smpboot: Allowing 8 CPUs, 0 hotplug CPUs
[    0.020816] [mem 0xf8000000-0xffffffff] available for PCI devices
[    0.020817] Booting paravirtualized kernel on Hyper-V
[    0.020819] clocksource: refined-jiffies: mask: 0xffffffff max_cycles: 0xffffffff, max_idle_ns: 19112604462750000 ns
[    0.026089] setup_percpu: NR_CPUS:256 nr_cpumask_bits:256 nr_cpu_ids:8 nr_node_ids:1
[    0.026615] percpu: Embedded 53 pages/cpu s177624 r8192 d31272 u262144
[    0.026622] pcpu-alloc: s177624 r8192 d31272 u262144 alloc=1*2097152
[    0.026624] pcpu-alloc: [0] 0 1 2 3 4 5 6 7 
[    0.026636] Hyper-V: PV spinlocks enabled
[    0.026637] PV qspinlock hash table entries: 256 (order: 0, 4096 bytes, linear)
[    0.026641] Built 1 zonelists, mobility grouping on.  Total pages: 2037661
[    0.026643] Kernel command line: initrd=\initrd.img WSL_ROOT_INIT=1 panic=-1 nr_cpus=8 hv_utils.timesync_implicit=1 console=hvc0 debug pty.legacy_count=0
[    0.026712] Unknown kernel command line parameters "WSL_ROOT_INIT=1", will be passed to user space.
[    0.028725] Dentry cache hash table entries: 1048576 (order: 11, 8388608 bytes, linear)
[    0.029886] Inode-cache hash table entries: 524288 (order: 10, 4194304 bytes, linear)
[    0.030026] mem auto-init: stack:off, heap alloc:off, heap free:off
[    0.066654] Memory: 4124576K/8282748K available (18452K kernel code, 2628K rwdata, 9636K rodata, 2004K init, 1824K bss, 256992K reserved, 0K cma-reserved)
[    0.066689] SLUB: HWalign=64, Order=0-3, MinObjects=0, CPUs=8, Nodes=1
[    0.066701] ftrace: allocating 52828 entries in 207 pages
[    0.093797] ftrace: allocated 207 pages with 6 groups
[    0.094266] rcu: Hierarchical RCU implementation.
[    0.094268] rcu: 	RCU restricting CPUs from NR_CPUS=256 to nr_cpu_ids=8.
[    0.094269] 	Rude variant of Tasks RCU enabled.
[    0.094270] 	Tracing variant of Tasks RCU enabled.
[    0.094270] rcu: RCU calculated value of scheduler-enlistment delay is 10 jiffies.
[    0.094271] rcu: Adjusting geometry for rcu_fanout_leaf=16, nr_cpu_ids=8
[    0.097880] Using NULL legacy PIC
[    0.097883] NR_IRQS: 16640, nr_irqs: 488, preallocated irqs: 0
[    0.098143] random: crng init done
[    0.098168] Console: colour dummy device 80x25
[    0.098176] ACPI: Core revision 20210730
[    0.098247] Failed to register legacy timer interrupt
[    0.098247] APIC: Switch to symmetric I/O mode setup
[    0.099584] x2apic enabled
[    0.101400] Switched APIC routing to physical x2apic.
[    0.101435] Hyper-V: Using IPI hypercalls
[    0.101512] clocksource: tsc-early: mask: 0xffffffffffffffff max_cycles: 0x28681cc9f58, max_idle_ns: 440795236420 ns
[    0.101522] Calibrating delay loop (skipped), value calculated using timer frequency.. 5606.42 BogoMIPS (lpj=28032140)
[    0.101637] x86/cpu: User Mode Instruction Prevention (UMIP) activated
[    0.101652] Last level iTLB entries: 4KB 0, 2MB 0, 4MB 0
[    0.101654] Last level dTLB entries: 4KB 0, 2MB 0, 4MB 0, 1GB 0
[    0.101659] Spectre V1 : Mitigation: usercopy/swapgs barriers and __user pointer sanitization
[    0.101662] Spectre V2 : Mitigation: Enhanced IBRS
[    0.101662] Spectre V2 : Spectre v2 / SpectreRSB mitigation: Filling RSB on context switch
[    0.101663] Spectre V2 : Spectre v2 / PBRSB-eIBRS: Retire a single CALL on VMEXIT
[    0.101664] RETBleed: Mitigation: Enhanced IBRS
[    0.101666] Spectre V2 : mitigation: Enabling conditional Indirect Branch Prediction Barrier
[    0.101668] Speculative Store Bypass: Mitigation: Speculative Store Bypass disabled via prctl and seccomp
[    0.101670] GDS: Unknown: Dependent on hypervisor status
[    0.101697] x86/fpu: Supporting XSAVE feature 0x001: 'x87 floating point registers'
[    0.101699] x86/fpu: Supporting XSAVE feature 0x002: 'SSE registers'
[    0.101700] x86/fpu: Supporting XSAVE feature 0x004: 'AVX registers'
[    0.101702] x86/fpu: Supporting XSAVE feature 0x020: 'AVX-512 opmask'
[    0.101703] x86/fpu: Supporting XSAVE feature 0x040: 'AVX-512 Hi256'
[    0.101704] x86/fpu: Supporting XSAVE feature 0x080: 'AVX-512 ZMM_Hi256'
[    0.101711] x86/fpu: xstate_offset[2]:  576, xstate_sizes[2]:  256
[    0.101713] x86/fpu: xstate_offset[5]:  832, xstate_sizes[5]:   64
[    0.101715] x86/fpu: xstate_offset[6]:  896, xstate_sizes[6]:  512
[    0.101716] x86/fpu: xstate_offset[7]: 1408, xstate_sizes[7]: 1024
[    0.101718] x86/fpu: Enabled xstate features 0xe7, context size is 2432 bytes, using 'compacted' format.
[    0.111517] Freeing SMP alternatives memory: 60K
[    0.111517] pid_max: default: 32768 minimum: 301
[    0.111517] LSM: Security Framework initializing
[    0.111517] landlock: Up and running.
[    0.111517] Mount-cache hash table entries: 16384 (order: 5, 131072 bytes, linear)
[    0.111517] Mountpoint-cache hash table entries: 16384 (order: 5, 131072 bytes, linear)
[    0.111517] smpboot: CPU0: 11th Gen Intel(R) Core(TM) i7-1165G7 @ 2.80GHz (family: 0x6, model: 0x8c, stepping: 0x1)
[    0.111517] Performance Events:  AnyThread deprecated, Icelake events, 32-deep LBR, full-width counters, Intel PMU driver.
[    0.111517] ... version:                5
[    0.111517] ... bit width:              48
[    0.111517] ... generic registers:      8
[    0.111517] ... value mask:             0000ffffffffffff
[    0.111517] ... max period:             00007fffffffffff
[    0.111517] ... fixed-purpose events:   4
[    0.111517] ... event mask:             0001000f000000ff
[    0.111517] signal: max sigframe size: 3632
[    0.111517] rcu: Hierarchical SRCU implementation.
[    0.111517] smp: Bringing up secondary CPUs ...
[    0.111517] x86: Booting SMP configuration:
[    0.111517] .... node  #0, CPUs:      #1 #2 #3 #4 #5 #6 #7
[    0.111517] smp: Brought up 1 node, 8 CPUs
[    0.111517] smpboot: Max logical packages: 1
[    0.111517] smpboot: Total of 8 processors activated (44851.42 BogoMIPS)
[    0.121604] node 0 deferred pages initialised in 20ms
[    0.122496] devtmpfs: initialized
[    0.122496] x86/mm: Memory block size: 128MB
[    0.122496] clocksource: jiffies: mask: 0xffffffff max_cycles: 0xffffffff, max_idle_ns: 19112604462750000 ns
[    0.122496] futex hash table entries: 2048 (order: 5, 131072 bytes, linear)
[    0.122496] NET: Registered PF_NETLINK/PF_ROUTE protocol family
[    0.122496] audit: initializing netlink subsys (disabled)
[    0.122496] audit: type=2000 audit(1714137307.020:1): state=initialized audit_enabled=0 res=1
[    0.122496] thermal_sys: Registered thermal governor 'step_wise'
[    0.122496] cpuidle: using governor menu
[    0.122496] ACPI: bus type PCI registered
[    0.122496] PCI: Fatal: No config space access function found
[    0.123079] kprobes: kprobe jump-optimization is enabled. All kprobes are optimized if possible.
[    0.123120] HugeTLB registered 1.00 GiB page size, pre-allocated 0 pages
[    0.123120] HugeTLB registered 2.00 MiB page size, pre-allocated 0 pages
[    0.123120] raid6: skip pq benchmark and using algorithm avx512x4
[    0.123120] raid6: using avx512x2 recovery algorithm
[    0.131530] ACPI: Added _OSI(Module Device)
[    0.131533] ACPI: Added _OSI(Processor Device)
[    0.131533] ACPI: Added _OSI(3.0 _SCP Extensions)
[    0.131534] ACPI: Added _OSI(Processor Aggregator Device)
[    0.131535] ACPI: Added _OSI(Linux-Dell-Video)
[    0.131535] ACPI: Added _OSI(Linux-Lenovo-NV-HDMI-Audio)
[    0.131536] ACPI: Added _OSI(Linux-HPI-Hybrid-Graphics)
[    0.134934] ACPI: 1 ACPI AML tables successfully acquired and loaded
[    0.137511] ACPI: Interpreter enabled
[    0.137517] ACPI: PM: (supports S0 S5)
[    0.137519] ACPI: Using IOAPIC for interrupt routing
[    0.137532] PCI: Using host bridge windows from ACPI; if necessary, use "pci=nocrs" and report a bug
[    0.137645] ACPI: Enabled 2 GPEs in block 00 to 0F
[    0.140025] iommu: Default domain type: Translated 
[    0.140028] iommu: DMA domain TLB invalidation policy: lazy mode 
[    0.140086] SCSI subsystem initialized
[    0.140092] ACPI: bus type USB registered
[    0.140099] usbcore: registered new interface driver usbfs
[    0.140102] usbcore: registered new interface driver hub
[    0.140105] usbcore: registered new device driver usb
[    0.140111] pps_core: LinuxPPS API ver. 1 registered
[    0.140111] pps_core: Software ver. 5.3.6 - Copyright 2005-2007 Rodolfo Giometti <giometti@linux.it>
[    0.140113] PTP clock support registered
[    0.141544] hv_vmbus: Vmbus version:5.3
[    0.141643] PCI: Using ACPI for IRQ routing
[    0.141645] PCI: System does not support PCI
[    0.141752] clocksource: Switched to clocksource tsc-early
[    0.141823] hv_vmbus: Unknown GUID: c376c1c3-d276-48d2-90a9-c04748072c60
[    0.141875] hv_vmbus: Unknown GUID: 6e382d18-3336-4f4b-acc4-2b7703d4df4a
[    0.141891] hv_vmbus: Unknown GUID: dde9cbc0-5060-4436-9448-ea1254a5d177
[    0.165155] VFS: Disk quotas dquot_6.6.0
[    0.165169] VFS: Dquot-cache hash table entries: 512 (order 0, 4096 bytes)
[    0.165199] FS-Cache: Loaded
[    0.165227] pnp: PnP ACPI init
[    0.168441] pnp: PnP ACPI: found 1 devices
[    0.172632] NET: Registered PF_INET protocol family
[    0.172709] IP idents hash table entries: 131072 (order: 8, 1048576 bytes, linear)
[    0.173601] tcp_listen_portaddr_hash hash table entries: 4096 (order: 4, 65536 bytes, linear)
[    0.173627] Table-perturb hash table entries: 65536 (order: 6, 262144 bytes, linear)
[    0.173631] TCP established hash table entries: 65536 (order: 7, 524288 bytes, linear)
[    0.189823] TCP bind hash table entries: 65536 (order: 8, 1048576 bytes, linear)
[    0.190126] TCP: Hash tables configured (established 65536 bind 65536)
[    0.190169] UDP hash table entries: 4096 (order: 5, 131072 bytes, linear)
[    0.190226] UDP-Lite hash table entries: 4096 (order: 5, 131072 bytes, linear)
[    0.190320] NET: Registered PF_UNIX/PF_LOCAL protocol family
[    0.191500] RPC: Registered named UNIX socket transport module.
[    0.191504] RPC: Registered udp transport module.
[    0.191505] RPC: Registered tcp transport module.
[    0.191506] RPC: Registered tcp NFSv4.1 backchannel transport module.
[    0.191510] PCI: CLS 0 bytes, default 64
[    0.191537] PCI-DMA: Using software bounce buffering for IO (SWIOTLB)
[    0.191539] software IO TLB: mapped [mem 0x00000000f4000000-0x00000000f8000000] (64MB)
[    0.191593] KVM: vmx: using Hyper-V Enlightened VMCS
[    0.191594] Trying to unpack rootfs image as initramfs...
[    0.192194] Freeing initrd memory: 1884K
[    0.291525] kvm: already loaded the other module
[    0.293671] Initialise system trusted keyrings
[    0.293771] workingset: timestamp_bits=46 max_order=21 bucket_order=0
[    0.294434] squashfs: version 4.0 (2009/01/31) Phillip Lougher
[    0.294622] NFS: Registering the id_resolver key type
[    0.294629] Key type id_resolver registered
[    0.294630] Key type id_legacy registered
[    0.294632] nfs4filelayout_init: NFSv4 File Layout Driver Registering...
[    0.294636] nfs4flexfilelayout_init: NFSv4 Flexfile Layout Driver Registering...
[    0.294637] Installing knfsd (copyright (C) 1996 okir@monad.swb.de).
[    0.295245] Key type cifs.idmap registered
[    0.295311] fuse: init (API version 7.34)
[    0.295474] SGI XFS with ACLs, security attributes, realtime, scrub, repair, quota, no debug enabled
[    0.297781] 9p: Installing v9fs 9p2000 file system support
[    0.297792] FS-Cache: Netfs '9p' registered for caching
[    0.297835] FS-Cache: Netfs 'ceph' registered for caching
[    0.297837] ceph: loaded (mds proto 32)
[    0.310987] NET: Registered PF_ALG protocol family
[    0.310995] xor: automatically using best checksumming function   avx       
[    0.310999] Key type asymmetric registered
[    0.311001] Asymmetric key parser 'x509' registered
[    0.311951] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 248)
[    0.313070] hv_vmbus: registering driver hv_pci
[    0.313350] hv_pci ec8ecc06-c2ed-4f8b-90e9-9f31742e6d3f: PCI VMBus probing: Using version 0x10004
[    0.314292] hv_pci ec8ecc06-c2ed-4f8b-90e9-9f31742e6d3f: PCI host bridge to bus c2ed:00
[    0.314294] pci_bus c2ed:00: root bus resource [mem 0x9ffe00000-0x9ffe02fff window]
[    0.314296] pci_bus c2ed:00: No busn resource found for root bus, will use [bus 00-ff]
[    0.314876] pci c2ed:00:00.0: [1af4:1043] type 00 class 0x010000
[    0.315858] pci c2ed:00:00.0: reg 0x10: [mem 0x9ffe00000-0x9ffe00fff 64bit]
[    0.316336] pci c2ed:00:00.0: reg 0x18: [mem 0x9ffe01000-0x9ffe01fff 64bit]
[    0.316987] pci c2ed:00:00.0: reg 0x20: [mem 0x9ffe02000-0x9ffe02fff 64bit]
[    0.318886] pci_bus c2ed:00: busn_res: [bus 00-ff] end is updated to 00
[    0.318893] pci c2ed:00:00.0: BAR 0: assigned [mem 0x9ffe00000-0x9ffe00fff 64bit]
[    0.319206] pci c2ed:00:00.0: BAR 2: assigned [mem 0x9ffe01000-0x9ffe01fff 64bit]
[    0.319487] pci c2ed:00:00.0: BAR 4: assigned [mem 0x9ffe02000-0x9ffe02fff 64bit]
[    0.320059] hv_pci 42bc0f33-7b47-4c1c-bc13-76df94a159ac: PCI VMBus probing: Using version 0x10004
[    0.320587] hv_pci 42bc0f33-7b47-4c1c-bc13-76df94a159ac: PCI host bridge to bus 7b47:00
[    0.320590] pci_bus 7b47:00: No busn resource found for root bus, will use [bus 00-ff]
[    0.320842] pci 7b47:00:00.0: [1414:008e] type 00 class 0x030200
[    0.323841] pci_bus 7b47:00: busn_res: [bus 00-ff] end is updated to 00
[    0.323999] ACPI: AC: AC Adapter [AC1] (off-line)
[    0.324697] ACPI: battery: Slot [BAT1] (battery present)
[    0.327673] Serial: 8250/16550 driver, 4 ports, IRQ sharing disabled
[    0.363271] Non-volatile memory driver v1.3
[    0.363463] [drm] Initialized vgem 1.0.0 20120112 for vgem on minor 0
[    0.364895] printk: console [hvc0] enabled
[    0.366135] brd: module loaded
[    0.367447] loop: module loaded
[    0.367740] hv_vmbus: registering driver hv_storvsc
[    0.368061] wireguard: WireGuard 1.0.0 loaded. See www.wireguard.com for information.
[    0.368572] wireguard: Copyright (C) 2015-2019 Jason A. Donenfeld <Jason@zx2c4.com>. All Rights Reserved.
[    0.369147] tun: Universal TUN/TAP device driver, 1.6
[    0.369476] PPP generic driver version 2.4.2
[    0.369827] scsi host0: storvsc_host_t
[    0.369882] PPP BSD Compression module registered
[    0.371390] PPP Deflate Compression module registered
[    0.372416] PPP MPPE Compression module registered
[    0.372818] NET: Registered PF_PPPOX protocol family
[    0.373555] usbcore: registered new interface driver cdc_ether
[    0.373991] usbcore: registered new interface driver cdc_ncm
[    0.374420] usbcore: registered new interface driver r8153_ecm
[    0.374850] hv_vmbus: registering driver hv_netvsc
[    0.375323] VFIO - User Level meta-driver version: 0.3
[    0.375755] usbcore: registered new interface driver cdc_acm
[    0.376052] cdc_acm: USB Abstract Control Model driver for USB modems and ISDN adapters
[    0.376669] vhci_hcd vhci_hcd.0: USB/IP Virtual Host Controller
[    0.377064] vhci_hcd vhci_hcd.0: new USB bus registered, assigned bus number 1
[    0.377459] vhci_hcd: created sysfs vhci_hcd.0
[    0.377965] usb usb1: New USB device found, idVendor=1d6b, idProduct=0002, bcdDevice= 5.15
[    0.378486] usb usb1: New USB device strings: Mfr=3, Product=2, SerialNumber=1
[    0.378950] usb usb1: Product: USB/IP Virtual Host Controller
[    0.379333] usb usb1: Manufacturer: Linux 5.15.146.1-microsoft-standard-WSL2 vhci_hcd
[    0.379720] usb usb1: SerialNumber: vhci_hcd.0
[    0.380169] hub 1-0:1.0: USB hub found
[    0.380423] hub 1-0:1.0: 8 ports detected
[    0.381055] vhci_hcd vhci_hcd.0: USB/IP Virtual Host Controller
[    0.382812] vhci_hcd vhci_hcd.0: new USB bus registered, assigned bus number 2
[    0.383684] usb usb2: We don't know the algorithms for LPM for this host, disabling LPM.
[    0.384274] usb usb2: New USB device found, idVendor=1d6b, idProduct=0003, bcdDevice= 5.15
[    0.384642] usb usb2: New USB device strings: Mfr=3, Product=2, SerialNumber=1
[    0.384974] usb usb2: Product: USB/IP Virtual Host Controller
[    0.385280] usb usb2: Manufacturer: Linux 5.15.146.1-microsoft-standard-WSL2 vhci_hcd
[    0.386132] usb usb2: SerialNumber: vhci_hcd.0
[    0.386693] hub 2-0:1.0: USB hub found
[    0.386937] hub 2-0:1.0: 8 ports detected
[    0.387572] hv_vmbus: registering driver hyperv_keyboard
[    0.387852] rtc_cmos 00:00: RTC can wake from S4
[    0.389666] rtc_cmos 00:00: registered as rtc0
[    0.390327] rtc_cmos 00:00: setting system clock to 2024-04-26T13:15:07 UTC (1714137307)
[    0.390763] rtc_cmos 00:00: alarms up to one month, 114 bytes nvram
[    0.391276] device-mapper: ioctl: 4.45.0-ioctl (2021-03-22) initialised: dm-devel@redhat.com
[    0.391745] device-mapper: raid: Loading target version 1.15.1
[    0.392096] usbcore: registered new interface driver usbhid
[    0.392307] usbhid: USB HID core driver
[    0.392555] hv_utils: Registering HyperV Utility Driver
[    0.392741] hv_vmbus: registering driver hv_utils
[    0.392982] hv_vmbus: registering driver hv_balloon
[    0.393338] hv_utils: TimeSync IC version 4.0
[    0.393903] hv_vmbus: registering driver dxgkrnl
[    0.394384] hv_balloon: Using Dynamic Memory protocol version 2.0
[    0.394943] drop_monitor: Initializing network drop monitor service
[    0.395504] Mirror/redirect action on
[    0.395724] u32 classifier
[    0.395899]     Performance counters on
[    0.396066] Free page reporting enabled
[    0.396123]     input device check on
[    0.396358] hv_balloon: Cold memory discard hint enabled with order 9
[    0.396974]     Actions configured
[    0.398901] IPVS: Registered protocols (TCP, UDP)
[    0.399360] IPVS: Connection hash table configured (size=4096, memory=32Kbytes)
[    0.399797] IPVS: ipvs loaded.
[    0.400018] IPVS: [rr] scheduler registered.
[    0.400248] IPVS: [wrr] scheduler registered.
[    0.400461] IPVS: [sh] scheduler registered.
[    0.400761] ipt_CLUSTERIP: ClusterIP Version 0.8 loaded successfully
[    0.401045] Initializing XFRM netlink socket
[    0.401909] NET: Registered PF_INET6 protocol family
[    0.402866] Segment Routing with IPv6
[    0.403041] In-situ OAM (IOAM) with IPv6
[    0.403206] NET: Registered PF_PACKET protocol family
[    0.403466] Bridge firewalling registered
[    0.403613] 8021q: 802.1Q VLAN Support v1.8
[    0.403759] sctp: Hash tables configured (bind 256/256)
[    0.403991] 9pnet: Installing 9P2000 support
[    0.404182] Key type dns_resolver registered
[    0.404382] Key type ceph registered
[    0.404619] libceph: loaded (mon/osd proto 15/24)
[    0.404923] NET: Registered PF_VSOCK protocol family
[    0.405156] hv_vmbus: registering driver hv_sock
[    0.405423] IPI shorthand broadcast: enabled
[    0.405783] sched_clock: Marking stable (401524909, 3386900)->(424586200, -19674391)
[    0.406556] registered taskstats version 1
[    0.407146] Loading compiled-in X.509 certificates
[    0.408037] Btrfs loaded, crc32c=crc32c-generic, zoned=no, fsverity=no
[    0.410399] Freeing unused kernel image (initmem) memory: 2004K
[    0.503482] Write protecting the kernel read-only data: 30720k
[    0.504369] Freeing unused kernel image (text/rodata gap) memory: 2024K
[    0.504843] Freeing unused kernel image (rodata/data gap) memory: 604K
[    0.505118] Run /init as init process
[    0.505268]   with arguments:
[    0.505417]     /init
[    0.505523]   with environment:
[    0.505671]     HOME=/
[    0.505769]     TERM=linux
[    0.505868]     WSL_ROOT_INIT=1
[    0.509732] scsi 0:0:0:0: Direct-Access     Msft     Virtual Disk     1.0  PQ: 0 ANSI: 5
[    0.514464] sd 0:0:0:0: Attached scsi generic sg0 type 0
[    0.515150] sd 0:0:0:0: [sda] 795664 512-byte logical blocks: (407 MB/389 MiB)
[    0.515590] sd 0:0:0:0: [sda] Write Protect is on
[    0.515807] sd 0:0:0:0: [sda] Mode Sense: 0f 00 80 00
[    0.516160] sd 0:0:0:0: [sda] Write cache: disabled, read cache: enabled, doesn't support DPO or FUA
[    0.519033] sd 0:0:0:0: [sda] Attached SCSI disk
[    0.573702] scsi 0:0:0:1: Direct-Access     Msft     Virtual Disk     1.0  PQ: 0 ANSI: 5
[    0.579487] sd 0:0:0:1: Attached scsi generic sg1 type 0
[    0.580208] sd 0:0:0:1: [sdb] 4194312 512-byte logical blocks: (2.15 GB/2.00 GiB)
[    0.580598] sd 0:0:0:1: [sdb] 4096-byte physical blocks
[    0.580868] sd 0:0:0:1: [sdb] Write Protect is off
[    0.581191] sd 0:0:0:1: [sdb] Mode Sense: 0f 00 00 00
[    0.581537] sd 0:0:0:1: [sdb] Write cache: enabled, read cache: enabled, doesn't support DPO or FUA
[    0.583405] sd 0:0:0:1: [sdb] Attached SCSI disk
[    0.597504] EXT4-fs (sda): mounted filesystem without journal. Opts: (null). Quota mode: none.
[    0.641711] Adding 2097152k swap on /dev/sdb.  Priority:-2 extents:1 across:2097152k 
[    0.786463] IPv6: ADDRCONF(NETDEV_CHANGE): eth0: link becomes ready
[    0.900549] hv_pci 3b11e76d-6541-44f5-b1ce-2f6e02b0e1f7: PCI VMBus probing: Using version 0x10004
[    0.902443] hv_pci 3b11e76d-6541-44f5-b1ce-2f6e02b0e1f7: PCI host bridge to bus 6541:00
[    0.902841] pci_bus 6541:00: root bus resource [mem 0xc00000000-0xe00001fff window]
[    0.903136] pci_bus 6541:00: No busn resource found for root bus, will use [bus 00-ff]
[    0.904109] pci 6541:00:00.0: [1af4:105a] type 00 class 0x088000
[    0.906381] pci 6541:00:00.0: reg 0x10: [mem 0xe00000000-0xe00000fff 64bit]
[    0.908588] pci 6541:00:00.0: reg 0x18: [mem 0xe00001000-0xe00001fff 64bit]
[    0.911161] pci 6541:00:00.0: reg 0x20: [mem 0xc00000000-0xdffffffff 64bit]
[    0.916606] pci_bus 6541:00: busn_res: [bus 00-ff] end is updated to 00
[    0.917260] pci 6541:00:00.0: BAR 4: assigned [mem 0xc00000000-0xdffffffff 64bit]
[    0.919261] pci 6541:00:00.0: BAR 0: assigned [mem 0xe00000000-0xe00000fff 64bit]
[    0.920999] pci 6541:00:00.0: BAR 2: assigned [mem 0xe00001000-0xe00001fff 64bit]
[    0.932009] virtiofs virtio1: Cache len: 0x200000000 @ 0xc00000000
[    1.026323] memmap_init_zone_device initialised 2097152 pages in 50ms
[    1.038570] FS-Cache: Duplicate cookie detected
[    1.038938] FS-Cache: O-cookie c=00000005 [p=00000002 fl=222 nc=0 na=1]
[    1.039235] FS-Cache: O-cookie d=000000007a70dec6{9P.session} n=00000000deeda247
[    1.039613] FS-Cache: O-key=[10] '34323934393337333930'
[    1.039826] FS-Cache: N-cookie c=00000006 [p=00000002 fl=2 nc=0 na=1]
[    1.040149] FS-Cache: N-cookie d=000000007a70dec6{9P.session} n=000000004ec0826e
[    1.040648] FS-Cache: N-key=[10] '34323934393337333930'
[    1.042546] FS-Cache: Duplicate cookie detected
[    1.042848] FS-Cache: O-cookie c=00000005 [p=00000002 fl=222 nc=0 na=1]
[    1.043188] FS-Cache: O-cookie d=000000007a70dec6{9P.session} n=00000000deeda247
[    1.043662] FS-Cache: O-key=[10] '34323934393337333930'
[    1.044053] FS-Cache: N-cookie c=00000007 [p=00000002 fl=2 nc=0 na=1]
[    1.044499] FS-Cache: N-cookie d=000000007a70dec6{9P.session} n=000000007ee9cd47
[    1.044876] FS-Cache: N-key=[10] '34323934393337333930'
[    1.079085] scsi 0:0:0:2: Direct-Access     Msft     Virtual Disk     1.0  PQ: 0 ANSI: 5
[    1.082961] sd 0:0:0:2: Attached scsi generic sg2 type 0
[    1.083683] sd 0:0:0:2: [sdc] 2147483648 512-byte logical blocks: (1.10 TB/1.00 TiB)
[    1.083992] sd 0:0:0:2: [sdc] 4096-byte physical blocks
[    1.084221] sd 0:0:0:2: [sdc] Write Protect is off
[    1.084393] sd 0:0:0:2: [sdc] Mode Sense: 0f 00 00 00
[    1.084679] sd 0:0:0:2: [sdc] Write cache: enabled, read cache: enabled, doesn't support DPO or FUA
[    1.086388] sd 0:0:0:2: [sdc] Attached SCSI disk
[    1.105428] EXT4-fs (sdc): recovery complete
[    1.107189] EXT4-fs (sdc): mounted filesystem with ordered data mode. Opts: discard,errors=remount-ro,data=ordered. Quota mode: none.
[    1.293453] clocksource: tsc: mask: 0xffffffffffffffff max_cycles: 0x28681cc9f58, max_idle_ns: 440795236420 ns
[    1.296036] clocksource: Switched to clocksource tsc
[    1.533085] FS-Cache: Duplicate cookie detected
[    1.533875] FS-Cache: O-cookie c=00000008 [p=00000002 fl=222 nc=0 na=1]
[    1.534437] FS-Cache: O-cookie d=000000007a70dec6{9P.session} n=000000008d7ddb92
[    1.535020] FS-Cache: O-key=[10] '34323934393337343339'
[    1.535460] FS-Cache: N-cookie c=00000009 [p=00000002 fl=2 nc=0 na=1]
[    1.535922] FS-Cache: N-cookie d=000000007a70dec6{9P.session} n=0000000049d80f30
[    1.536445] FS-Cache: N-key=[10] '34323934393337343339'
[    1.567486] WSL (1) ERROR: ConfigApplyWindowsLibPath:2537: open /etc/ld.so.conf.d/ld.wsl.conf
[    1.567488]  failed 2
[    1.572798] FS-Cache: Duplicate cookie detected
[    1.573298] FS-Cache: O-cookie c=0000000a [p=00000002 fl=222 nc=0 na=1]
[    1.573695] FS-Cache: O-cookie d=000000007a70dec6{9P.session} n=0000000089005082
[    1.574052] FS-Cache: O-key=[10] '34323934393337343433'
[    1.574295] FS-Cache: N-cookie c=0000000b [p=00000002 fl=2 nc=0 na=1]
[    1.574595] FS-Cache: N-cookie d=000000007a70dec6{9P.session} n=00000000faa30daf
[    1.574996] FS-Cache: N-key=[10] '34323934393337343433'
[    1.583826] WSL (1) WARNING: /usr/share/zoneinfo/Asia/Shanghai not found. Is the tzdata package installed?
[    1.772017] misc dxg: dxgk: dxgkio_query_adapter_info: Ioctl failed: -22
[    1.772484] misc dxg: dxgk: dxgkio_query_adapter_info: Ioctl failed: -22
[    1.772747] misc dxg: dxgk: dxgkio_query_adapter_info: Ioctl failed: -22
[    1.773012] misc dxg: dxgk: dxgkio_query_adapter_info: Ioctl failed: -22
[    1.774372] misc dxg: dxgk: dxgkio_query_adapter_info: Ioctl failed: -2
[    1.873509] scsi 0:0:0:3: Direct-Access     Msft     Virtual Disk     1.0  PQ: 0 ANSI: 5
[    1.878988] sd 0:0:0:3: Attached scsi generic sg3 type 0
[    1.879993] sd 0:0:0:3: [sdd] 2147483648 512-byte logical blocks: (1.10 TB/1.00 TiB)
[    1.880494] sd 0:0:0:3: [sdd] 4096-byte physical blocks
[    1.880908] sd 0:0:0:3: [sdd] Write Protect is off
[    1.881232] sd 0:0:0:3: [sdd] Mode Sense: 0f 00 00 00
[    1.881650] sd 0:0:0:3: [sdd] Write cache: enabled, read cache: enabled, doesn't support DPO or FUA
[    1.887470] sd 0:0:0:3: [sdd] Attached SCSI disk
[    1.904706] EXT4-fs (sdd): mounted filesystem with ordered data mode. Opts: discard,errors=remount-ro,data=ordered. Quota mode: none.
[    1.938189] FS-Cache: Duplicate cookie detected
[    1.938736] FS-Cache: O-cookie c=00000010 [p=00000002 fl=222 nc=0 na=1]
[    1.939189] FS-Cache: O-cookie d=000000007a70dec6{9P.session} n=00000000175607d1
[    1.939733] FS-Cache: O-key=[10] '34323934393337343830'
[    1.940069] FS-Cache: N-cookie c=00000011 [p=00000002 fl=2 nc=0 na=1]
[    1.940370] FS-Cache: N-cookie d=000000007a70dec6{9P.session} n=00000000a5efab16
[    1.940810] FS-Cache: N-key=[10] '34323934393337343830'
[    1.948513] WSL (2) ERROR: UtilCreateProcessAndWait:665: /bin/mount failed with 2
[    1.949496] WSL (1) ERROR: UtilCreateProcessAndWait:687: /bin/mount failed with status 0xff00

[    1.950198] WSL (1) ERROR: ConfigMountFsTab:2589: Processing fstab with mount -a failed.
[    1.951572] WSL (1) ERROR: ConfigApplyWindowsLibPath:2537: open /etc/ld.so.conf.d/ld.wsl.conf
[    1.951575]  failed 2
[    1.956003] WSL (3) ERROR: UtilCreateProcessAndWait:665: /bin/mount failed with 2
[    1.956745] WSL (1) ERROR: UtilCreateProcessAndWait:687: /bin/mount failed with status 0xff00

[    1.958303] WSL (4) ERROR: UtilCreateProcessAndWait:665: /bin/mount failed with 2
[    1.958924] WSL (1) ERROR: UtilCreateProcessAndWait:687: /bin/mount failed with status 0xff00

[    1.963509] WSL (1) WARNING: /usr/share/zoneinfo/Asia/Shanghai not found. Is the tzdata package installed?
[    2.056301] misc dxg: dxgk: dxgkio_query_adapter_info: Ioctl failed: -22
[    2.059968] misc dxg: dxgk: dxgkio_query_adapter_info: Ioctl failed: -22
[    2.061477] misc dxg: dxgk: dxgkio_query_adapter_info: Ioctl failed: -22
[    2.062123] misc dxg: dxgk: dxgkio_query_adapter_info: Ioctl failed: -22
[    2.062580] misc dxg: dxgk: dxgkio_query_adapter_info: Ioctl failed: -2
[    2.269525] loop0: detected capacity change from 0 to 938696
[    2.298888] ISO 9660 Extensions: Microsoft Joliet Level 3
[    2.299565] ISO 9660 Extensions: RRIP_1991A
[    2.316287] loop1: detected capacity change from 0 to 346252
[    2.330073] ISO 9660 Extensions: RRIP_1991A
[    2.528723] netlink: 'init': attribute type 4 has an invalid length.
[    2.603875] IPv6: ADDRCONF(NETDEV_CHANGE): services1: link becomes ready
[    2.932682] loop2: detected capacity change from 0 to 1232380
[    2.947691] ISO 9660 Extensions: Microsoft Joliet Level 3
[    2.948613] ISO 9660 Extensions: RRIP_1991A
[    3.018562] kmem.limit_in_bytes is deprecated and will be removed. Please report your usecase to linux-mm@kvack.org if you depend on this functionality.
[    3.183046] capability: warning: `lifecycle-serve' uses 32-bit capabilities (legacy support in use)
[    3.623469] IPv6: ADDRCONF(NETDEV_CHANGE): services0: link becomes ready
[    5.212613] docker0: port 1(veth0ae73e5) entered blocking state
[    5.213314] docker0: port 1(veth0ae73e5) entered disabled state
[    5.213805] device veth0ae73e5 entered promiscuous mode
[    5.214287] docker0: port 1(veth0ae73e5) entered blocking state
[    5.214933] docker0: port 1(veth0ae73e5) entered forwarding state
[    5.215531] docker0: port 1(veth0ae73e5) entered disabled state
[    5.434085] eth0: renamed from veth6b5ee93
[    5.493913] IPv6: ADDRCONF(NETDEV_CHANGE): veth0ae73e5: link becomes ready
[    5.497239] docker0: port 1(veth0ae73e5) entered blocking state
[    5.497996] docker0: port 1(veth0ae73e5) entered forwarding state
[    5.498724] IPv6: ADDRCONF(NETDEV_CHANGE): docker0: link becomes ready
[    7.575128] scsi 0:0:0:4: Direct-Access     Msft     Virtual Disk     1.0  PQ: 0 ANSI: 5
[    7.580017] sd 0:0:0:4: Attached scsi generic sg4 type 0
[    7.580545] sd 0:0:0:4: [sde] 2147483648 512-byte logical blocks: (1.10 TB/1.00 TiB)
[    7.581190] sd 0:0:0:4: [sde] 4096-byte physical blocks
[    7.581695] sd 0:0:0:4: [sde] Write Protect is off
[    7.581978] sd 0:0:0:4: [sde] Mode Sense: 0f 00 00 00
[    7.582374] sd 0:0:0:4: [sde] Write cache: enabled, read cache: enabled, doesn't support DPO or FUA
[    7.584863] sd 0:0:0:4: [sde] Attached SCSI disk
[    7.599320] EXT4-fs (sde): mounted filesystem with ordered data mode. Opts: discard,errors=remount-ro,data=ordered. Quota mode: none.
[    7.946555] FS-Cache: Duplicate cookie detected
[    7.947223] FS-Cache: O-cookie c=0000001c [p=00000002 fl=222 nc=0 na=1]
[    7.947891] FS-Cache: O-cookie d=000000007a70dec6{9P.session} n=00000000150e3d74
[    7.948720] FS-Cache: O-key=[10] '34323934393338303831'
[    7.949306] FS-Cache: N-cookie c=0000001d [p=00000002 fl=2 nc=0 na=1]
[    7.950008] FS-Cache: N-cookie d=000000007a70dec6{9P.session} n=00000000c42bada5
[    7.951182] FS-Cache: N-key=[10] '34323934393338303831'
[    7.989894] new mount options do not match the existing superblock, will be ignored
[    8.160977] systemd-journald[36]: Received client request to flush runtime journal.
[    8.174079] systemd-journald[36]: File /var/log/journal/5922aaca21f04cba9483e028ed2d9f52/system.journal corrupted or uncleanly shut down, renaming and replacing.
[    9.701729] misc dxg: dxgk: dxgkio_query_adapter_info: Ioctl failed: -22
[    9.702357] misc dxg: dxgk: dxgkio_query_adapter_info: Ioctl failed: -22
[    9.702831] misc dxg: dxgk: dxgkio_query_adapter_info: Ioctl failed: -22
[    9.703916] misc dxg: dxgk: dxgkio_query_adapter_info: Ioctl failed: -22
[    9.704580] misc dxg: dxgk: dxgkio_query_adapter_info: Ioctl failed: -2
[   11.305701] WSL (2): Creating login session for imlcm
[   11.533805] systemd-journald[36]: File /var/log/journal/5922aaca21f04cba9483e028ed2d9f52/user-1000.journal corrupted or uncleanly shut down, renaming and replacing.
[   17.906728] WSL (2): Creating login session for root
[   49.264554] hv_balloon: Max. dynamic memory size: 8090 MB
[37981.285250] docker0: port 1(veth0ae73e5) entered disabled state
[37981.286153] veth6b5ee93: renamed from eth0
[37981.443278] docker0: port 1(veth0ae73e5) entered disabled state
[37981.445204] device veth0ae73e5 left promiscuous mode
[37981.445243] docker0: port 1(veth0ae73e5) entered disabled state
[38002.940052] Exception: 
[38002.940100] Operation canceled @p9io.cpp:258 (AcceptAsync)

[39660.403743] EXT4-fs (sde): mounted filesystem with ordered data mode. Opts: discard,errors=remount-ro,data=ordered. Quota mode: none.
[39660.461516] FS-Cache: Duplicate cookie detected
[39660.462161] FS-Cache: O-cookie c=00000027 [p=00000002 fl=222 nc=0 na=1]
[39660.462718] FS-Cache: O-cookie d=000000007a70dec6{9P.session} n=000000004af76021
[39660.463517] FS-Cache: O-key=[10] '34323938393033333338'
[39660.463799] FS-Cache: N-cookie c=00000028 [p=00000002 fl=2 nc=0 na=1]
[39660.464136] FS-Cache: N-cookie d=000000007a70dec6{9P.session} n=00000000c88afa29
[39660.464470] FS-Cache: N-key=[10] '34323938393033333338'
[39660.605301] new mount options do not match the existing superblock, will be ignored
[39660.776136] systemd-journald[36]: Received client request to flush runtime journal.
[39660.785683] systemd-journald[36]: File /var/log/journal/5922aaca21f04cba9483e028ed2d9f52/system.journal corrupted or uncleanly shut down, renaming and replacing.
[39662.591168] misc dxg: dxgk: dxgkio_query_adapter_info: Ioctl failed: -22
[39662.591705] misc dxg: dxgk: dxgkio_query_adapter_info: Ioctl failed: -22
[39662.592260] misc dxg: dxgk: dxgkio_query_adapter_info: Ioctl failed: -22
[39662.592830] misc dxg: dxgk: dxgkio_query_adapter_info: Ioctl failed: -22
[39662.593286] misc dxg: dxgk: dxgkio_query_adapter_info: Ioctl failed: -2
[39663.148397] WSL (2): Creating login session for imlcm
[43917.064859] hv_utils: TimeSync IC version 4.0


# 文心一言分析

1-microsoft-standard-WSL2`: 表示当前运行的是Linux内核版本5.15.146.1，专门为WSL 2定制。
   - `root@65c757a075e2`: 表示编译内核的用户是root，编译环境的主机名是65c757a075e2。
   - `gcc (GCC) 11.2.0`: 使用的编译器是GCC版本11.2.0。
   - `#1 SMP Thu Jan 11 04:09:03 UTC 2024`: 表示这是第一次编译的内核版本，编译时间是2024年1月11日。

2. **命令行参数**:
   - `initrd=\initrd.img`: 初始化RAM磁盘镜像文件。
   - `WSL_ROOT_INIT=1`: WSL特定的初始化参数。
   - `panic=-1`: 设置内核panic级别。
   - `nr_cpus=8`: 设置CPU数量为8。
   - `hv_utils.timesync_implicit=1`: 启用Hyper-V的时间同步功能。
   - `console=hvc0`: 设置控制台设备为hvc0。
   - `debug`: 启用调试模式。
   - `pty.legacy_count=0`: 设置传统伪终端的数量。

3. **支持的CPU类型**:
   - 列出了内核支持的CPU类型，包括Intel、AMD和Centaur。

4. **BIOS提供的物理RAM映射**:
   - 列出了BIOS提供的物理内存映射信息。

5. **NX (Execute Disable) protection**:
   - 表示启用了NX保护，这是一种硬件级别的安全特性，用于防止数据执行保护（DEP）攻击。

6. **DMI信息**:
   - DMI（Desktop Management Interface）信息未找到或无效。

7. **Hypervisor检测**:
   - 检测到了Microsoft Hyper-V虚拟化技术。

8. **Hyper-V特性**:
   - 列出了Hyper-V支持的特性，如嵌套虚拟化、LAPIC计时器频率等。

9. **时钟源和TSC频率检测**:
   - 检测到并使用了Hyper-V时钟源和TSC（时间戳计数器）频率。

10. **内存区域初始化**:
    - 初始化了不同内存区域的范围，如DMA、DMA32和Normal。

11. **ACPI表加载**:
    - 加载了ACPI（Advanced Configuration and Power Interface）表，如FACP、DSDT等，这些表包含了电源管理和硬件配置信息。

12. **I/O APIC和中断配置**:
    - 配置了I/O APIC（Advanced Programmable Interrupt Controller）和中断源覆盖。

13. **SMP（对称多处理）配置**:
    - 配置了多处理器系统，允许8个CPU。

14. **内存初始化**:
    - 初始化了内存，包括可用内存的范围和节点信息。

15. **内核命令行参数确认**:
    - 确认了启动时使用的内核命令行参数。

16. **未知内核命令行参数**:
    - 警告有未知的命令行参数`WSL_ROOT_INIT=1`，这些参数将被传递给用户空间。

17. **文件系统和缓存初始化**:
    - 初始化了dentry缓存、inode缓存和内存自动初始化设置。

18. **内存信息**:
    - 显示了总的可用内存、内核代码大小、读写数据大小等。

19. **SLUB内存分配器初始化**:
    - 初始化了SLUB（Slab Unifier）内存分配器。

20. **ftrace初始化**:
    - 初始化了ftrace（Function Tracer），用于跟踪内核函数执行。

21. **RCU（Read-Copy Update）初始化**:
    - 初始化了RCU，这是一种用于并发读写的同步机制。

22. **NR_IRQS和预分配的中断**:
    - 初始化了中断向量表的大小和预分配的中断数量。

23. **随机数生成器初始化**:
    - 初始化了随机数生成器。

24. **控制台初始化**:
    - 初始化了控制台设备。

25. **ACPI核心初始化**:
    - 初始化了ACPI核心。

26. **APIC和x2APIC初始化**:
    - 初始化了APIC（Advanced Programmable Interrupt Controller）和x2APIC。

27. **时钟源切换**:
    - 切换了时钟源到tsc-early。

28. **CPU特性和Spectre/Meltdown缓解措施**:
    - 列出了CPU的特性和对Spectre/Meltdown等安全漏洞的缓解措施。

29. **x86 FPU和XSAVE特性初始化**:
    - 初始化了x86 FPU（浮点单元）和XSAVE特性。

30. **Freeing SMP alternatives memory**:
    - 释放了SMP（对称多处理）替代指令的内存。

31. **PID最大限制和LSM初始化**:
    - 设置了PID的最大限制和初始化了LSM（Linux Security Module）框架。

32. **Mount缓存哈希表初始化**:
    - 初始化了挂载点缓存和哈希表。

33. **smpboot和CPU信息**:
    - 启动了secondary CPUs并确认了CPU的数量和类型。

34. **deferred pages和devtmpfs初始化**:
    - 初始化了延迟页面和devtmpfs（设备临时文件系统）。

35. **futex哈希表初始化**:
    - 初始化了futex（快速用户空间互斥体）哈希表。

36. **网络协议族注册**:
    - 注册了NETLINK和ROUTE协议族。

37. **审计子系统初始化**:
    - 初始化了审计子系统但禁用了它。

38. **ACPI表和其他设备驱动加载**:
    - 加载了其他ACPI表和设备驱动，如USB、PCI等。

39. **Kprobes和HugeTLB页面注册**:
    - 注册了Kprobes和HugeTLB页面。

40. **RAID和SCSI子系统初始化**:
    - 初始化了RAID和SCSI子系统。

41. **USB核心和USB设备驱动加载**:
    - 加载了USB核心和USB设备驱动。

42. **网络协议族注册和设备驱动加载**:
    - 注册了INET、INET6、PACKET等网络协议族并加载了相关设备驱动。

43. **文件系统注册和初始化**:
    - 注册和初始化了各种文件系统，如EXT4、XFS等。

44. **内存映射和释放**:
    - 初始化了内存映射并释放了未使用的内核镜像内存。

45. **内核写保护和其他内存释放**:
    - 对内核的只读数据区域进行了写保护并释放了未使用的内核镜像内存。

46. **启动/init进程**:
    - 运行了/init作为init进程，并传递了环境变量。

47. **SCSI磁盘检测和挂载**:
    - 检测到了SCSI磁盘并挂载了文件系统。

48. **网络配置和链接状态变化**:
    - 配置了网络接口并显示了链接状态的变化。

49. **PCI设备检测和初始化**:
    - 检测并初始化了PCI设备。

50. **文件系统挂载和错误**:
    - 尝试挂载文件系统但出现了错误。

51. **登录会话创建**:
    - 为用户imlcm和root创建了登录会话。

52. **Hyper-V Balloon驱动信息**:
    - 显示了Hyper-V Balloon驱动的信息，用于动态内存管理。

53. **网络异常和错误**:
    - 出现了网络异常和错误，可能与9P协议或Docker容器有关。

54. **文件系统重新挂载和日志错误**:
    - 尝试重新挂载文件系统并出现了日志错误。

55. **Hyper-V和DXGK错误**:
    - 出现了与Hyper-V和DXGK（DirectX Graphics Kernel）相关的错误。

这个日志详细记录了Linux内核从启动到初始化完成过程中的各种活动和配置信息，包括硬件检测、内存管理、文件系统挂载、网络配置等。同时，也包含了一些错误和警告信息，这些可能需要进一步调查和处理。

# 文心分析2
当然可以，下面我会尽量详细地解释这些日志行，包括它们的作用、涉及的命令或配置以及参数的意义。

1. **启动信息**:
   - `[    0.000000] Linux version 5.15.146.1-microsoft-standard-WSL2 (root@65c757a075e2) (gcc (GCC) 11.2.0, GNU ld (GNU Binutils) 2.37) #1 SMP Thu Jan 11 04:09:03 UTC 2024`
     - 这行显示了Linux内核的版本信息，编译者（root用户），编译时间，以及使用的编译器和链接器版本。

2. **命令行参数**:
   - `[    0.000000] Command line: initrd=\initrd.img WSL_ROOT_INIT=1 panic=-1 nr_cpus=8 hv_utils.timesync_implicit=1 console=hvc0 debug pty.legacy_count=0`
     - 这是传递给内核的命令行参数。其中，`initrd`指定了初始RAM磁盘的位置，`WSL_ROOT_INIT`是WSL特有的参数，`panic`设置了内核崩溃时的行为，`nr_cpus`设置了CPU数量，`hv_utils.timesync_implicit`启用了Hyper-V的时间同步，`console`设置了控制台设备，`debug`启用了调试模式，`pty.legacy_count`设置了传统伪终端的数量。

3. **支持的CPU类型**:
   - `[    0.000000] KERNEL supported cpus:` 后续列出了内核支持的CPU类型。

4. **BIOS提供的物理RAM映射**:
   - `[    0.000000] BIOS-provided physical RAM map:` 后续列出了BIOS提供的物理内存映射信息。

5. **NX保护**:
   - `[    0.000000] NX (Execute Disable) protection: active`
     - 启用了NX保护，这是一种防止数据执行保护（DEP）攻击的安全特性。

6. **DMI信息**:
   - `[    0.000000] DMI not present or invalid.`
     - 表示DMI信息不存在或无效。

7. **Hypervisor检测**:
   - `[    0.000000] Hypervisor detected: Microsoft Hyper-V`
     - 检测到了Microsoft Hyper-V虚拟化技术。

8. **Hyper-V特性**:
   - `[    0.000000] Hyper-V: privilege flags low 0xae7f, high 0x3b8030, hints 0xa4e24, misc 0xe4bed7b6` 等行提供了Hyper-V的具体配置和特性信息。

9. **时钟源和TSC频率检测**:
   - `[    0.000003] tsc: Detected 2803.214 MHz processor` 等行提供了TSC（时间戳计数器）的频率信息。

10. **内存区域初始化**:
    - `[    0.000012] e820: update [mem 0x00000000-0x00000fff] usable ==> reserved` 等行初始化了不同的内存区域。

11. **ACPI表加载**:
    - `[    0.000106] ACPI: RSDP 0x00000000000E0000 000024 (v02 VRTUAL)` 等行加载了ACPI表，这些表包含了电源管理和硬件配置信息。

12. **I/O APIC和中断配置**:
    - `[    0.020796] IOAPIC[0]: apic_id 8, version 17, address 0xfec00000, GSI 0-23` 等行配置了I/O APIC和中断源。

13. **SMP配置**:
    - `[    0.020807] smpboot: Allowing 8 CPUs, 0 hotplug CPUs`
     - 配置了多处理器系统，允许8个CPU。

14. **内存初始化**:
    - `[    0.066654] Memory: 4124576K/8282748K available (18452K kernel code, 2628K rwdata, 9636K rodata, 2004K init, 1824K bss, 256992K reserved, 0K cma-reserved)`
     - 显示了总的可用内存、内核代码大小等信息。

15. **SLUB内存分配器初始化**:
    - `[    0.066689] SLUB: HWalign=64, Order=0-3, MinObjects=0, CPUs=8, Nodes=1`
     - 初始化了SLUB内存分配器。

16. **ftrace初始化**:
    - `[    0.093797] ftrace: allocating 52828 entries in 207 pages`
     - 初始化了ftrace。

17. **RCU初始化**:
    - `[    0.094266] rcu: Hierarchical RCU implementation.`
     - 初始化了RCU。

18. **NR_IRQS和预分配的中断**:
    - `[    0.097880] NR_IRQS: 16640, nr_irqs: 488, preallocated irqs: 0`
     - 初始化了中断向量表的大小和预分配的中断数量。

19. **随机数生成器初始化**:
    - `[    0.098143] random: crng init done`
     - 初始化了随机数生成器。

20. **控制台初始化**:
    - `[    0.098176] Console: colour dummy device 80x25`
     - 初始化了控制台设备。

...
（其他部分的解释遵循相似的模式，提供初始化、配置和加载的信息。）

55. **Hyper-V和DXGK错误**:
    - 这些错误可能与Hyper-V虚拟化和图形驱动有关。

这个日志详细记录了Linux内核从启动到初始化完成过程中的各种活动和配置信息，对于系统管理员和开发人员来说，是排查问题和了解系统行为的重要资源。