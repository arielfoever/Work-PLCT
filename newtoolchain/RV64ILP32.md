# RUYISDK RV64ILP32 GNU工具链文档检验

## 编译器构建测试

本地构建 [RV64ILP32 工具链](https://github.com/ruyisdk/riscv-gnu-toolchain-rv64ilp32.git)

整个仓库大小约占 7G 空间。

```
asciinema rec ~/toolchain.cast  --overwrite
git clone https://github.com/ruyisdk/riscv-gnu-toolchain-rv64ilp32.git --depth=1
cd riscv-gnu-toolchain-rv64ilp32
./configure --prefix=/home/ariel/riscv --with-arch=rv64gc --with-abi=ilp32d
make -j $(nproc)
/home/ariel/riscv/bin/riscv64-unknown-elf-gcc -v
exit
```

构建过程见 [toolchain.cast](https://github.com/ArielHeleneto/Work-PLCT/tree/master/newtoolchain/toolchain.cast)

构建后可以获得如下版本的产物。

```
Using built-in specs.
COLLECT_GCC=/home/ariel/riscv/bin/riscv64-unknown-elf-gcc
COLLECT_LTO_WRAPPER=/home/ariel/riscv/libexec/gcc/riscv64-unknown-elf/13.2.0/lto-wrapper
Target: riscv64-unknown-elf
Configured with: /home/ariel/riscv-gnu-toolchain-rv64ilp32/gcc/configure --target=riscv64-unknown-elf --prefix=/home/ariel/riscv --disable-shared --disable-threads --enable-languages=c,c++ --with-pkgversion= --with-system-zlib --enable-tls --with-newlib --with-sysroot=/home/ariel/riscv/riscv64-unknown-elf --with-native-system-header-dir=/include --disable-libmudflap --disable-libssp --disable-libquadmath --disable-libgomp --disable-nls --disable-tm-clone-registry --src=.././gcc --disable-multilib --with-abi=ilp32d --with-arch=rv64gc --with-tune=rocket --with-isa-spec=20191213 'CFLAGS_FOR_TARGET=-Os    -mcmodel=medlow' 'CXXFLAGS_FOR_TARGET=-Os    -mcmodel=medlow'
Thread model: single
Supported LTO compression algorithms: zlib zstd
gcc version 13.2.0 () 
```

该工具链包含

```
riscv64-unknown-elf-addr2line  riscv64-unknown-elf-elfedit     riscv64-unknown-elf-gcc-ranlib     riscv64-unknown-elf-gprof     riscv64-unknown-elf-objdump  riscv64-unknown-elf-strip
riscv64-unknown-elf-ar         riscv64-unknown-elf-g++         riscv64-unknown-elf-gcov           riscv64-unknown-elf-ld        riscv64-unknown-elf-ranlib
riscv64-unknown-elf-as         riscv64-unknown-elf-gcc         riscv64-unknown-elf-gcov-dump      riscv64-unknown-elf-ld.bfd    riscv64-unknown-elf-readelf
riscv64-unknown-elf-c++        riscv64-unknown-elf-gcc-13.2.0  riscv64-unknown-elf-gcov-tool      riscv64-unknown-elf-lto-dump  riscv64-unknown-elf-run
riscv64-unknown-elf-c++filt    riscv64-unknown-elf-gcc-ar      riscv64-unknown-elf-gdb            riscv64-unknown-elf-nm        riscv64-unknown-elf-size
riscv64-unknown-elf-cpp        riscv64-unknown-elf-gcc-nm      riscv64-unknown-elf-gdb-add-index  riscv64-unknown-elf-objcopy   riscv64-unknown-elf-strings
```

#### 内核构建测试

```
asciinema rec ~/corebuild.cast  --overwrite
git clone https://github.com/ruyisdk/linux-xuantie-kernel.git--depth=1
cd linux-xuantie-kernel
make ARCH=riscv CROSS_COMPILE=/home/ariel/riscv/bin/riscv64-unknown-elf- rv64ilp32_defconfig all -j$(nproc)
exit
```

#### 内核测速

测试从 [Releases](https://github.com/ruyisdk/riscv-gnu-toolchain-rv64ilp32/releases) 下载的内核。测得新内核的速度比旧内核快 0.14 倍。

```
asciinema rec ~/arch.cast  --overwrite
wget -c https://github.com/ruyisdk/riscv-gnu-toolchain-rv64ilp32/releases/download/2024.07.25/riscv64ilp32-elf-ubuntu-22.04-gcc-nightly-2024.07.25-nightly.tar.gz
tar zxvf riscv64ilp32-elf-ubuntu-22.04-gcc-nightly-*-nightly.tar.gz
cd riscv/qemu-linux
./start-qemu-rv64ilp32.sh rootfs.ext2 (测试创新32位Linux内核)
./start-qemu-rv32ilp32.sh rootfs.ext2 (测试传统32位Linux内核)
```

##### 创新32位

使用 `./start-qemu-rv64ilp32.sh rootfs.ext2` 测试。平均速度为 2.59 Gbits/sec。   

见 [start-qemu-rv64ilp32.cast](https://github.com/ArielHeleneto/Work-PLCT/tree/master/newtoolchain/start-qemu-rv64ilp32.cast)

```
OpenSBI v1.4-15-g9c8b18e                                                                        
   ____                    _____ ____ _____                                                     
  / __ \                  / ____|  _ \_   _|                                                                                                                                                    
 | |  | |_ __   ___ _ __ | (___ | |_) || |                                                      
 | |  | | '_ \ / _ \ '_ \ \___ \|  _ < | |                                                                                                                                                      
 | |__| | |_) |  __/ | | |____) | |_) || |_                                                     
  \____/| .__/ \___|_| |_|_____/|____/_____|                                                    
        | |                                                                                                                                                                                     
        |_|                                                                                     
                                                                                                                                                                                                
Platform Name             : riscv-virtio,qemu                                                                                                                                                   
Platform Features         : medeleg                                                                                                                                                             
Platform HART Count       : 1                                                                                                                                                                   
Platform IPI Device       : aclint-mswi                                                                                                                                                         
Platform Timer Device     : aclint-mtimer @ 10000000Hz                                                                                                                                          
Platform Console Device   : uart8250                                                                                                                                                            
Platform HSM Device       : ---                                                                                                                                                                 
Platform PMU Device       : ---                                                                                                                                                                 
Platform Reboot Device    : syscon-reboot                                                                                                                                                       
Platform Shutdown Device  : syscon-poweroff                                                                                                                                                     
Platform Suspend Device   : ---                                                                 
Platform CPPC Device      : ---                                                                 
Firmware Base             : 0x80000000                                                                                                                                                          
Firmware Size             : 323 KB                                                                                                                                                              
Firmware RW Offset        : 0x40000                                                                                                                                                             
Firmware RW Size          : 67 KB                                                               
Firmware Heap Offset      : 0x48000                                                                                                                                                             
Firmware Heap Size        : 35 KB (total), 2 KB (reserved), 10 KB (used), 22 KB (free)                                                                                                          
Firmware Scratch Size     : 4096 B (total), 336 B (used), 3760 B (free)                         
Runtime SBI Version       : 2.0                                                                 
                                                                                                                                                                                                
Domain0 Name              : root                                                                                                                                                                
Domain0 Boot HART         : 0                                                                                                                                                                   
Domain0 HARTs             : 0*                                                                                                                                                                  
Domain0 Region00          : 0x0000000000100000-0x0000000000100fff M: (I,R,W) S/U: (R,W)         
Domain0 Region01          : 0x0000000010000000-0x0000000010000fff M: (I,R,W) S/U: (R,W)         
Domain0 Region02          : 0x0000000002000000-0x000000000200ffff M: (I,R,W) S/U: ()                                                                                                            
Domain0 Region03          : 0x0000000080040000-0x000000008005ffff M: (R,W) S/U: ()                                                                                                              
Domain0 Region04          : 0x0000000080000000-0x000000008003ffff M: (R,X) S/U: ()
Domain0 Region03          : 0x0000000080040000-0x000000008005ffff M: (R,W) S/U: ()                                                                                            13:44:32 [498/542]Domain0 Region04          : 0x0000000080000000-0x000000008003ffff M: (R,X) S/U: ()                                                                                                              
Domain0 Region05          : 0x000000000c400000-0x000000000c5fffff M: (I,R,W) S/U: (R,W)                                                                                                         
Domain0 Region06          : 0x000000000c000000-0x000000000c3fffff M: (I,R,W) S/U: (R,W)                                                                                                         
Domain0 Region07          : 0x0000000000000000-0xffffffffffffffff M: () S/U: (R,W,X)                                                                                                            
Domain0 Next Address      : 0x0000000080400000                                                                                                                                                  
Domain0 Next Arg1         : 0x00000000bfe00000                                                                                                                                                  
Domain0 Next Mode         : S-mode                                                              
Domain0 SysReset          : yes                                                                 
Domain0 SysSuspend        : yes                                                                                                                                                                 
                                                                                                
Boot HART ID              : 0                                                                                                                                                                   
Boot HART Domain          : root                                                                
Boot HART Priv Version    : v1.12                                                               
Boot HART Base ISA        : rv64imafdch                                                                                                                                                         
Boot HART ISA Extensions  : sstc,zicntr,zihpm,zicboz,zicbom,sdtrig                              
Boot HART PMP Count       : 16                                                                                                                                                                  
Boot HART PMP Granularity : 2 bits                                                                                                                                                              
Boot HART PMP Address Bits: 54                                                                                                                                                                  
Boot HART MHPM Info       : 16 (0x0007fff8)                                                                                                                                                     
Boot HART Debug Triggers  : 2 triggers                                                                                                                                                          
Boot HART MIDELEG         : 0x0000000000001666                                                                                                                                                  
Boot HART MEDELEG         : 0x0000000000f0b509                                                                                                                                                  
[    0.000000] Linux version 6.6.0-gbf63582b08f4 (runner@fv-az841-564) (riscv64-unknown-elf-gcc () 13.2.0, GNU ld (GNU Binutils) 2.42) #2 SMP Wed Feb 21 04:42:14 UTC 2024                      
[    0.000000] random: crng init done                                                                                                                                                           
[    0.000000] OF: fdt: Ignoring memory range 0x80000000 - 0x80400000                                                                                                                           
[    0.000000] Machine model: riscv-virtio,qemu                                                                                                                                                 
[    0.000000] SBI specification v2.0 detected                                                  
[    0.000000] SBI implementation ID=0x1 Version=0x10004                                        
[    0.000000] SBI TIME extension detected                                                                                                                                                      
[    0.000000] SBI IPI extension detected                                                                                                                                                       
[    0.000000] SBI RFENCE extension detected                                                                                                                                                    
[    0.000000] SBI SRST extension detected                                                      
[    0.000000] efi: UEFI not found.                                                                                                                                                             
[    0.000000] OF: reserved mem: 0x80000000..0x8003ffff (256 KiB) nomap non-reusable mmode_resv1@80000000                                                                                       
[    0.000000] OF: reserved mem: 0x80040000..0x8005ffff (128 KiB) nomap non-reusable mmode_resv0@80040000
[    0.000000] Zone ranges:                                                                     
[    0.000000]   Normal   [mem 0x0000000080400000-0x00000000bfffffff]                                                                                                                           
[    0.000000] Movable zone start for each node                                                                                                                                                 
[    0.000000] Early memory node ranges                                                                                                                                                         
[    0.000000]   node   0: [mem 0x0000000080400000-0x00000000bfffffff]                                                                                                                          
[    0.000000] Initmem setup node 0 [mem 0x0000000080400000-0x00000000bfffffff]                 
[    0.000000] On node 0, zone Normal: 1024 pages in unavailable ranges                         
[    0.000000] SBI HSM extension detected                                                                                                                                                       
[    0.000000] Falling back to deprecated "riscv,isa"                                                                                                                                           
[    0.000000] riscv: base ISA extensions acdfhim
[    0.000000] Falling back to deprecated "riscv,isa"                                                                                                                         13:44:32 [454/542][    0.000000] riscv: base ISA extensions acdfhim                                                                                                                                               
[    0.000000] riscv: ELF capabilities acdfim                                                                                                                                                   
[    0.000000] percpu: Embedded 16 pages/cpu s32800 r8192 d24544 u65536                                                                                                                         
[    0.000000] Kernel command line: rootwait root=/dev/vda ro console=ttyS0 earlycon=sbi                                                                                                        
[    0.000000] Dentry cache hash table entries: 131072 (order: 7, 524288 bytes, linear)                                                                                                         
[    0.000000] Inode-cache hash table entries: 65536 (order: 6, 262144 bytes, linear)                                                                                                           
[    0.000000] Built 1 zonelists, mobility grouping on.  Total pages: 259080                    
[    0.000000] mem auto-init: stack:all(zero), heap alloc:off, heap free:off                    
[    0.000000] Virtual kernel memory layout:                                                                                                                                                    
[    0.000000]       fixmap : 0x9da00000 - 0x9e000000   (6144 kB)                               
[    0.000000]       pci io : 0x9e000000 - 0x9f000000   (  16 MB)                                                                                                                               
[    0.000000]      vmemmap : 0x9f000000 - 0xa0000000   (  16 MB)                               
[    0.000000]      vmalloc : 0xa0000000 - 0xc0000000   ( 512 MB)                               
[    0.000000]       lowmem : 0xc0000000 - 0xffc00000   (1020 MB)                                                                                                                               
[    0.000000] Memory: 1022788K/1044480K available (8912K kernel code, 756K rwdata, 2256K rodata, 357K init, 346K bss, 21692K reserved, 0K cma-reserved)
[    0.000000] SLUB: HWalign=64, Order=0-3, MinObjects=0, CPUs=1, Nodes=1                                                                                                                       
[    0.000000] rcu: Hierarchical RCU implementation.                                                                                                                                            
[    0.000000] rcu:     RCU restricting CPUs from NR_CPUS=64 to nr_cpu_ids=1.                                                                                                                   
[    0.000000] rcu:     RCU debug extended QS entry/exit.                                                                                                                                       
[    0.000000]  Tracing variant of Tasks RCU enabled.                                                                                                                                           
[    0.000000] rcu: RCU calculated value of scheduler-enlistment delay is 25 jiffies.                                                                                                           
[    0.000000] rcu: Adjusting geometry for rcu_fanout_leaf=16, nr_cpu_ids=1                                                                                                                     
[    0.000000] NR_IRQS: 64, nr_irqs: 64, preallocated irqs: 0                                                                                                                                   
[    0.000000] riscv-intc: 64 local interrupts mapped                                                                                                                                           
[    0.000000] plic: plic@c000000: mapped 95 interrupts with 1 handlers for 2 contexts.                                                                                                         
[    0.000000] riscv: providing IPIs using SBI IPI extension                                                                                                                                    
[    0.000000] rcu: srcu_init: Setting srcu_struct sizes based on contention.                   
[    0.000000] clocksource: riscv_clocksource: mask: 0xffffffffffffffff max_cycles: 0x24e6a1710, max_idle_ns: 440795202120 ns
[    0.000128] sched_clock: 64 bits at 10MHz, resolution 100ns, wraps every 4398046511100ns                                                                                                     
[    0.000376] riscv-timer: Timer interrupt in S-mode is available via sstc extension                                                                                                           
[    0.014648] Console: colour dummy device 80x25                                                                                                                                               
[    0.017184] Calibrating delay loop (skipped), value calculated using timer frequency.. 20.00 BogoMIPS (lpj=40000)
[    0.017414] pid_max: default: 32768 minimum: 301                                                                                                                                             
[    0.019293] LSM: initializing lsm=capability,integrity                                                                                                                                       
[    0.022269] Mount-cache hash table entries: 2048 (order: 1, 8192 bytes, linear)                                                                                                              
[    0.022335] Mountpoint-cache hash table entries: 2048 (order: 1, 8192 bytes, linear)         
[    0.060094] RCU Tasks Trace: Setting shift to 0 and lim to 1 rcu_task_cb_adjust=1.                                                                                                           
[    0.061028] ASID allocator using 16 bits (65536 entries)                                                                                                                                     
[    0.062585] rcu: Hierarchical SRCU implementation.                                                                                                                                           
[    0.062632] rcu:     Max phase no-delay instances is 1000.                                                                                                                                   
[    0.067348] EFI services will not be available.                                              
[    0.069934] smp: Bringing up secondary CPUs ...                                              
[    0.070806] smp: Brought up 1 node, 1 CPU                                                                                                                                                    
[    0.086311] devtmpfs: initialized                                                                                                                                                            
[    0.098421] clocksource: jiffies: mask: 0xffffffff max_cycles: 0xffffffff, max_idle_ns: 7645041785100000 ns
[    0.086311] devtmpfs: initialized                                                                                                                                          13:44:32 [410/542][    0.098421] clocksource: jiffies: mask: 0xffffffff max_cycles: 0xffffffff, max_idle_ns: 7645041785100000 ns                                                                                  
[    0.098622] futex hash table entries: 256 (order: 2, 16384 bytes, linear)                                                                                                                    
[    0.101690] pinctrl core: initialized pinctrl subsystem                                                                                                                                      
[    0.111073] NET: Registered PF_NETLINK/PF_ROUTE protocol family                                                                                                                              
[    0.124020] DMA: preallocated 128 KiB GFP_KERNEL pool for atomic allocations                                                                                                                 
[    0.124524] audit: initializing netlink subsys (disabled)                                                                                                                                    
[    0.130776] thermal_sys: Registered thermal governor 'step_wise'                             
[    0.131885] audit: type=2000 audit(0.112:1): state=initialized audit_enabled=0 res=1         
[    0.132714] cpuidle: using governor menu                                                                                                                                                     
[    0.154646] cpu0: Ratio of byte access time to unaligned word access is 7.10, unaligned accesses are fast
[    0.177682] HugeTLB: registered 2.00 MiB page size, pre-allocated 0 pages                                                                                                                    
[    0.177721] HugeTLB: 0 KiB vmemmap can be freed for a 2.00 MiB page                          
[    0.183573] iommu: Default domain type: Translated                                           
[    0.183762] iommu: DMA domain TLB invalidation policy: strict mode                                                                                                                           
[    0.186010] SCSI subsystem initialized                                                                                                                                                       
[    0.188495] usbcore: registered new interface driver usbfs                                                                                                                                   
[    0.188857] usbcore: registered new interface driver hub                                                                                                                                     
[    0.189082] usbcore: registered new device driver usb                                                                                                                                        
[    0.207536] vgaarb: loaded                                                                                                                                                                   
[    0.212456] clocksource: Switched to clocksource riscv_clocksource                                                                                                                           
[    0.244933] NET: Registered PF_INET protocol family                                                                                                                                          
[    0.246487] IP idents hash table entries: 16384 (order: 5, 131072 bytes, linear)                                                                                                             
[    0.255226] tcp_listen_portaddr_hash hash table entries: 512 (order: 1, 10240 bytes, linear)                                                                                                 
[    0.255412] Table-perturb hash table entries: 65536 (order: 6, 262144 bytes, linear)                                                                                                         
[    0.255525] TCP established hash table entries: 8192 (order: 3, 32768 bytes, linear)                                                                                                         
[    0.255919] TCP bind hash table entries: 8192 (order: 6, 327680 bytes, linear)                                                                                                               
[    0.257890] TCP: Hash tables configured (established 8192 bind 8192)                         
[    0.259547] UDP hash table entries: 512 (order: 2, 24576 bytes, linear)                                                                                                                      
[    0.259996] UDP-Lite hash table entries: 512 (order: 2, 24576 bytes, linear)                                                                                                                 
[    0.261677] NET: Registered PF_UNIX/PF_LOCAL protocol family                                                                                                                                 
[    0.265748] RPC: Registered named UNIX socket transport module.                                                                                                                              
[    0.265816] RPC: Registered udp transport module.                                                                                                                                            
[    0.265833] RPC: Registered tcp transport module.                                                                                                                                            
[    0.265849] RPC: Registered tcp-with-tls transport module.                                                                                                                                   
[    0.265864] RPC: Registered tcp NFSv4.1 backchannel transport module.                                                                                                                        
[    0.266098] PCI: CLS 0 bytes, default 64                                                     
[    0.275944] workingset: timestamp_bits=30 max_order=18 bucket_order=0                                                                                                                        
[    0.281123] NFS: Registering the id_resolver key type                                                                                                                                        
[    0.282457] Key type id_resolver registered                                                                                                                                                  
[    0.282512] Key type id_legacy registered                                                                                                                                                    
[    0.283740] nfs4filelayout_init: NFSv4 File Layout Driver Registering...                     
[    0.283871] nfs4flexfilelayout_init: NFSv4 Flexfile Layout Driver Registering...             
[    0.284741] fuse: init (API version 7.39)                                                                                                                                                    
[    0.285414] 9p: Installing v9fs 9p2000 file system support                                                                                                                                   
[    0.287749] NET: Registered PF_ALG protocol family
[    0.285414] 9p: Installing v9fs 9p2000 file system support                                                                                                                 13:44:32 [366/542][    0.287749] NET: Registered PF_ALG protocol family                                                                                                                                           
[    0.288200] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 247)                                                                                                             
[    0.288412] io scheduler mq-deadline registered                                                                                                                                              
[    0.288524] io scheduler kyber registered                                                                                                                                                    
[    0.288679] io scheduler bfq registered                                                                                                                                                      
[    0.294915] pci-host-generic 30000000.pci: host bridge /soc/pci@30000000 ranges:                                                                                                             
[    0.296112] pci-host-generic 30000000.pci:       IO 0x0003000000..0x000300ffff -> 0x0000000000
[    0.296818] pci-host-generic 30000000.pci:      MEM 0x0040000000..0x007fffffff -> 0x0040000000
[    0.296966] pci-host-generic 30000000.pci:      MEM 0x0400000000..0x07ffffffff -> 0x0400000000                                                                                               
[    0.298041] pci-host-generic 30000000.pci: ECAM at [mem 0x30000000-0x3fffffff] for [bus 00-ff]           
[    0.300223] pci-host-generic 30000000.pci: PCI host bridge to bus 0000:00                                                                                                                    
[    0.300621] pci_bus 0000:00: root bus resource [bus 00-ff]                                   
[    0.300762] pci_bus 0000:00: root bus resource [io  0x0000-0xffff]                           
[    0.300828] pci_bus 0000:00: root bus resource [mem 0x40000000-0x7fffffff]                                                                                                                   
[    0.302589] pci 0000:00:00.0: [1b36:0008] type 00 class 0x060000                                                                                                                             
[    0.448736] Serial: 8250/16550 driver, 4 ports, IRQ sharing disabled                                                                                                                         
[    0.466420] printk: console [ttyS0] disabled                                                                                                                                                 
[    0.471390] 10000000.serial: ttyS0 at MMIO 0x10000000 (irq = 12, base_baud = 230400) is a 16550A                                                                                             
[    0.473479] printk: console [ttyS0] enabled                                                                                                                                                  
[    0.514572] SuperH (H)SCI(F) driver initialized                                                                                                                                              
[    0.537890] loop: module loaded                                                                                                                                                              
[    0.584074] virtio_blk virtio0: 1/0/0 default/read/poll queues                                                                                                                               
[    0.590144] virtio_blk virtio0: [vda] 20480 512-byte logical blocks (10.5 MB/10.0 MiB)                                                                                                       
[    0.619645] e1000e: Intel(R) PRO/1000 Network Driver                                                                                                                                         
[    0.619970] e1000e: Copyright(c) 1999 - 2015 Intel Corporation.                                                                                                                              
[    0.624101] usbcore: registered new interface driver uas                                                                                                                                     
[    0.624685] usbcore: registered new interface driver usb-storage                             
[    0.626268] mousedev: PS/2 mouse device common for all mice                                                                                                                                  
[    0.630938] goldfish_rtc 101000.rtc: registered as rtc0                                                                                                                                      
[    0.632073] goldfish_rtc 101000.rtc: setting system clock to 2024-07-31T05:44:31 UTC (1722404671)                                                                                            
[    0.636561] syscon-poweroff poweroff: pm_power_off already claimed for sbi_srst_power_off                                                                                                    
[    0.637606] syscon-poweroff: probe of poweroff failed with error -16                                                                                                                         
[    0.640382] sdhci: Secure Digital Host Controller Interface driver                                                                                                                           
[    0.640758] sdhci: Copyright(c) Pierre Ossman                                                                                                                                                
[    0.641548] sdhci-pltfm: SDHCI platform and OF driver helper                                                                                                                                 
[    0.642821] usbcore: registered new interface driver usbhid                                  
[    0.643223] usbhid: USB HID core driver                                                                                                                                                      
[    0.644079] riscv-pmu-sbi: SBI PMU extension is available                                                                                                                                    
[    0.645064] riscv-pmu-sbi: 16 firmware and 18 hardware counters                                                                                                                              
[    0.645415] riscv-pmu-sbi: Perf sampling/filtering is not supported as sscof extension is not available                                                                                      
[    0.649546] NET: Registered PF_INET6 protocol family                                         
[    0.658851] Segment Routing with IPv6                                                        
[    0.659475] In-situ OAM (IOAM) with IPv6                                                                                                                                                     
[    0.660255] sit: IPv6, IPv4 and MPLS over IPv4 tunneling driver                                                                                                                              
[    0.664677] NET: Registered PF_PACKET protocol family
[    0.660255] sit: IPv6, IPv4 and MPLS over IPv4 tunneling driver                                                                                                            13:44:41 [322/542][    0.664677] NET: Registered PF_PACKET protocol family                                                                                                                                        
[    0.666806] 9pnet: Installing 9P2000 support                                                                                                                                                 
[    0.667605] Key type dns_resolver registered                                                                                                                                                 
[    0.718248] debug_vm_pgtable: [debug_vm_pgtable         ]: Validating architecture page table helpers                                                                                        
[    0.727807] clk: Disabling unused clocks                                                                                                                                                     
[    0.749756] EXT4-fs (vda): mounting ext2 file system using the ext4 subsystem                                                                                                                
[    0.763771] EXT4-fs (vda): mounted filesystem 0658dec4-4d7b-4983-b4bf-5fe885737c74 ro without journal. Quota mode: disabled.
[    0.764684] VFS: Mounted root (ext2 filesystem) readonly on device 254:0.                                                                                                                    
[    0.768987] devtmpfs: mounted                                                                                                                                                                
[    0.812872] Freeing unused kernel image (initmem) memory: 352K                                                                                                                               
[    0.813324] Kernel memory protection not selected by kernel config.                                                                                                                          
[    0.813827] Run /sbin/init as init process                                                   
[    1.062641] EXT4-fs (vda): warning: mounting unchecked fs, running e2fsck is recommended     
[    1.136983] EXT4-fs (vda): re-mounted 0658dec4-4d7b-4983-b4bf-5fe885737c74 r/w. Quota mode: disabled.                                                                                        
Seeding 256 bits and crediting                                                                                                                                                                  
Saving 256 bits of creditable seed for next boot                                                                                                                                                
Starting syslogd: OK                                                                                                                                                                            
Starting klogd: OK                                                                                                                                                                              
Running sysctl: OK                                                                                                                                                                              
Starting network: OK                                                                                                                                                                            
                                                                                                                                                                                                
===============================================                                                                                                                                                 
=========== iperf3 TCP test ===================                                                                                                                                                 
===============================================                                                                                                                                                 
-----------------------------------------------------------                                                                                                                                     
Server listening on 5201 (test #1)                                                                                                                                                              
-----------------------------------------------------------                                     
Accepted connection from 127.0.0.1, port 59958                                                                                                                                                  
Connecting to host 127.0.0.1, port 5201                                                                                                                                                         
[  5] local 127.0.0.1 port 59960 connected to 127.0.0.1 port 5201                                                                                                                               
[  5] local 127.0.0.1 port 5201 connected to 127.0.0.1 port 59960                                                                                                                               
[ ID] Interval           Transfer     Bitrate                                                                                                                                                   
[ ID] Interval           Transfer     Bitrate         Retr  Cwnd                                                                                                                                
[  5]   0.00-1.00   sec   314 MBytes  2.64 Gbits/sec                                                                                                                                            
[  5]   0.00-1.00   sec   315 MBytes  2.64 Gbits/sec    0   3.12 MBytes                                                                                                                         
[  5]   1.00-2.00   sec   293 MBytes  2.45 Gbits/sec                                            
[  5]   1.00-2.00   sec   295 MBytes  2.48 Gbits/sec    0   3.25 MBytes                                                                                                                         
[  5]   2.00-3.00   sec   302 MBytes  2.55 Gbits/sec                                                                                                                                            
[  5]   2.00-3.00   sec   302 MBytes  2.53 Gbits/sec    0   3.25 MBytes                                                                                                                         
[  5]   3.00-4.00   sec   314 MBytes  2.64 Gbits/sec                                                                                                                                            
[  5]   3.00-4.00   sec   314 MBytes  2.63 Gbits/sec    0   3.43 MBytes                         
[  5]   4.00-5.00   sec   311 MBytes  2.60 Gbits/sec                                            
[  5]   4.00-5.01   sec   312 MBytes  2.62 Gbits/sec    0   3.62 MBytes                                                                                                                         
[  5]   5.00-6.00   sec   306 MBytes  2.58 Gbits/sec                                                                                                                                            
[  5]   5.01-6.00   sec   305 MBytes  2.57 Gbits/sec    0   3.62 MBytes
[  5]   5.00-6.00   sec   306 MBytes  2.58 Gbits/sec                                                                                                                          13:45:32 [278/542][  5]   5.01-6.00   sec   305 MBytes  2.57 Gbits/sec    0   3.62 MBytes                                                                                                                         
[  5]   6.00-7.00   sec   310 MBytes  2.60 Gbits/sec                                                                                                                                            
[  5]   6.00-7.00   sec   311 MBytes  2.61 Gbits/sec    0   3.62 MBytes                                                                                                                         
[  5]   7.00-8.00   sec   316 MBytes  2.65 Gbits/sec                                                                                                                                            
[  5]   7.00-8.00   sec   315 MBytes  2.64 Gbits/sec    0   3.62 MBytes                                                                                                                         
[  5]   8.00-9.00   sec   314 MBytes  2.63 Gbits/sec                                                                                                                                            
[  5]   8.00-9.00   sec   315 MBytes  2.64 Gbits/sec    0   3.62 MBytes                                                                                                                         
[  5]   9.00-10.00  sec   309 MBytes  2.60 Gbits/sec                                                                                                                                            
[  5]  10.00-10.01  sec  2.00 MBytes  2.14 Gbits/sec                                                                                                                                            
- - - - - - - - - - - - - - - - - - - - - - - - -                                                                                                                                               
[ ID] Interval           Transfer     Bitrate                                                                                                                                                   
[  5]   0.00-10.01  sec  3.02 GBytes  2.59 Gbits/sec                  receiver                  
[  5]   9.00-10.00  sec   309 MBytes  2.60 Gbits/sec    0   3.62 MBytes                         
- - - - - - - - - - - - - - - - - - - - - - - - -                                                                                                                                               
[ ID] Interval           Transfer     Bitrate         Retr                                                                                                                                      
[  5]   0.00-10.00  sec  3.02 GBytes  2.59 Gbits/sec    0             sender                                                                                                                    
[  5]   0.00-10.01  sec  3.02 GBytes  2.59 Gbits/sec                  receiver                                                                                                                  
                                                                                                                                                                                                
iperf Done.                                                                                                                                                                                     
-----------------------------------------------------------                                                                                                                                     
Server listening on 5201 (test #2)                                                                                                                                                              
-----------------------------------------------------------                                                                                                                                     
                                                                                                                                                                                                
Welcome to Buildroot
buildroot login:
```

##### 传统32位

使用 `./start-qemu-rv32ilp32.sh rootfs.ext2` 测试。平均速度为 2.32 Gbits/sec。   

见 [start-qemu-rv32ilp32.cast](https://github.com/ArielHeleneto/Work-PLCT/tree/master/newtoolchain/start-qemu-rv32ilp32.cast)

```
OpenSBI v1.4-15-g9c8b18e                                                                        
   ____                    _____ ____ _____                                                                                                                                                     
  / __ \                  / ____|  _ \_   _|                                                                                                                                                    
 | |  | |_ __   ___ _ __ | (___ | |_) || |                                                                                                                                                      
 | |  | | '_ \ / _ \ '_ \ \___ \|  _ < | |                                                                                                                                                      
 | |__| | |_) |  __/ | | |____) | |_) || |_                                                     
  \____/| .__/ \___|_| |_|_____/|____/_____|                                                    
        | |                                                                                                                                                                                     
        |_|                                                                                                                                                                                     
         
                 |_|                                                                                                                                                                   13:45:32 [234/542]                                                                                                                                                                                                
Platform Name             : riscv-virtio,qemu                                                                                                                                                   
Platform Features         : medeleg                                                                                                                                                             
Platform HART Count       : 1                                                                                                                                                                   
Platform IPI Device       : aclint-mswi                                                                                                                                                         
Platform Timer Device     : aclint-mtimer @ 10000000Hz                                                                                                                                          
Platform Console Device   : uart8250                                                                                                                                                            
Platform HSM Device       : ---                                                                                                                                                                 
Platform PMU Device       : ---                                                                                                                                                                 
Platform Reboot Device    : syscon-reboot                                                                                                                                                       
Platform Shutdown Device  : syscon-poweroff                                                                                                                                                     
Platform Suspend Device   : ---                                                                 
Platform CPPC Device      : ---                                                                 
Firmware Base             : 0x80000000                                                                                                                                                          
Firmware Size             : 319 KB                                                                                                                                                              
Firmware RW Offset        : 0x40000                                                                                                                                                             
Firmware RW Size          : 63 KB                                                                                                                                                               
Firmware Heap Offset      : 0x47000                                                                                                                                                             
Firmware Heap Size        : 35 KB (total), 2 KB (reserved), 9 KB (used), 23 KB (free)                                                                                                           
Firmware Scratch Size     : 4096 B (total), 188 B (used), 3908 B (free)                                                                                                                         
Runtime SBI Version       : 2.0                                                                                                                                                                 
                                                                                                                                                                                                
Domain0 Name              : root                                                                                                                                                                
Domain0 Boot HART         : 0                                                                                                                                                                   
Domain0 HARTs             : 0*                                                                                                                                                                  
Domain0 Region00          : 0x00100000-0x00100fff M: (I,R,W) S/U: (R,W)                                                                                                                         
Domain0 Region01          : 0x10000000-0x10000fff M: (I,R,W) S/U: (R,W)                         
Domain0 Region02          : 0x02000000-0x0200ffff M: (I,R,W) S/U: ()                                                                                                                            
Domain0 Region03          : 0x80040000-0x8004ffff M: (R,W) S/U: ()                                                                                                                              
Domain0 Region04          : 0x80000000-0x8003ffff M: (R,X) S/U: ()                                                                                                                              
Domain0 Region05          : 0x0c400000-0x0c5fffff M: (I,R,W) S/U: (R,W)                                                                                                                         
Domain0 Region06          : 0x0c000000-0x0c3fffff M: (I,R,W) S/U: (R,W)                                                                                                                         
Domain0 Region07          : 0x00000000-0xffffffff M: () S/U: (R,W,X)                                                                                                                            
Domain0 Next Address      : 0x80400000                                                                                                                                                          
Domain0 Next Arg1         : 0xbfe00000                                                                                                                                                          
Domain0 Next Mode         : S-mode                                                              
Domain0 SysReset          : yes                                                                                                                                                                 
Domain0 SysSuspend        : yes                                                                                                                                                                 
                                                                                                                                                                                                
Boot HART ID              : 0                                                                                                                                                                   
Boot HART Domain          : root                                                                
Boot HART Priv Version    : v1.12                                                               
Boot HART Base ISA        : rv32imafdch                                                                                                                                                         
Boot HART ISA Extensions  : sstc,zicntr,zihpm,zicboz,zicbom,sdtrig                                                                                                                              
Boot HART PMP Count       : 16
Boot HART ISA Extensions  : sstc,zicntr,zihpm,zicboz,zicbom,sdtrig                                                                                                            13:45:33 [190/542]Boot HART PMP Count       : 16                                                                                                                                                                  
Boot HART PMP Granularity : 2 bits                                                                                                                                                              
Boot HART PMP Address Bits: 32                                                                                                                                                                  
Boot HART MHPM Info       : 16 (0x0007fff8)                                                                                                                                                     
Boot HART Debug Triggers  : 2 triggers                                                                                                                                                          
Boot HART MIDELEG         : 0x00001666                                                                                                                                                          
Boot HART MEDELEG         : 0x00f0b509                                                                                                                                                          
[    0.000000] Linux version 6.6.0-gbf63582b08f4-dirty (guoren@guoren-linux-dev) (riscv32-unknown-linux-gnu-gcc () 13.2.0, GNU ld (GNU Binutils) 2.42) #1 SMP Wed Feb 21 16:02:51 CST 2024      
[    0.000000] random: crng init done                                                                                                                                                           
[    0.000000] OF: fdt: Ignoring memory range 0x80000000 - 0x80400000                                                                                                                           
[    0.000000] Machine model: riscv-virtio,qemu                                                                                                                                                 
[    0.000000] SBI specification v2.0 detected                                                  
[    0.000000] SBI implementation ID=0x1 Version=0x10004                                        
[    0.000000] SBI TIME extension detected                                                                                                                                                      
[    0.000000] SBI IPI extension detected                                                                                                                                                       
[    0.000000] SBI RFENCE extension detected                                                                                                                                                    
[    0.000000] SBI SRST extension detected                                                                                                                                                      
[    0.000000] efi: UEFI not found.                                                                                                                                                             
[    0.000000] OF: reserved mem: 0x80000000..0x8003ffff (256 KiB) nomap non-reusable mmode_resv1@80000000                                                                                       
[    0.000000] OF: reserved mem: 0x80040000..0x8004ffff (64 KiB) nomap non-reusable mmode_resv0@80040000                                                                                        
[    0.000000] Zone ranges:                                                                                                                                                                     
[    0.000000]   Normal   [mem 0x0000000080400000-0x00000000bfffffff]                                                                                                                           
[    0.000000] Movable zone start for each node                                                                                                                                                 
[    0.000000] Early memory node ranges                                                                                                                                                         
[    0.000000]   node   0: [mem 0x0000000080400000-0x00000000bfffffff]                                                                                                                          
[    0.000000] Initmem setup node 0 [mem 0x0000000080400000-0x00000000bfffffff]                                                                                                                 
[    0.000000] On node 0, zone Normal: 1024 pages in unavailable ranges                         
[    0.000000] SBI HSM extension detected                                                                                                                                                       
[    0.000000] Falling back to deprecated "riscv,isa"                                                                                                                                           
[    0.000000] riscv: base ISA extensions acdfhim                                                                                                                                               
[    0.000000] riscv: ELF capabilities acdfim                                                                                                                                                   
[    0.000000] percpu: Embedded 15 pages/cpu s32544 r8192 d20704 u61440                                                                                                                         
[    0.000000] Kernel command line: rootwait root=/dev/vda ro console=ttyS0 earlycon=sbi                                                                                                        
[    0.000000] Dentry cache hash table entries: 131072 (order: 7, 524288 bytes, linear)                                                                                                         
[    0.000000] Inode-cache hash table entries: 65536 (order: 6, 262144 bytes, linear)                                                                                                           
[    0.000000] Built 1 zonelists, mobility grouping on.  Total pages: 259080                    
[    0.000000] mem auto-init: stack:all(zero), heap alloc:off, heap free:off                                                                                                                    
[    0.000000] Virtual kernel memory layout:                                                                                                                                                    
[    0.000000]       fixmap : 0x9d800000 - 0x9e000000   (8192 kB)                                                                                                                               
[    0.000000]       pci io : 0x9e000000 - 0x9f000000   (  16 MB)                                                                                                                               
[    0.000000]      vmemmap : 0x9f000000 - 0xa0000000   (  16 MB)                               
[    0.000000]      vmalloc : 0xa0000000 - 0xc0000000   ( 512 MB)                               
[    0.000000]       lowmem : 0xc0000000 - 0xffc00000   (1020 MB)                                                                                                                               
[    0.000000] Memory: 1023364K/1044480K available (8456K kernel code, 748K rwdata, 2205K rodata, 329K init, 322K bss, 21116K reserved, 0K cma-reserved)                                        
[    0.000000] SLUB: HWalign=64, Order=0-3, MinObjects=0, CPUs=1, Nodes=1 
[    0.000000] Memory: 1023364K/1044480K available (8456K kernel code, 748K rwdata, 2205K rodata, 329K init, 322K bss, 21116K reserved, 0K cma-reserved)                      13:45:33 [146/542][    0.000000] SLUB: HWalign=64, Order=0-3, MinObjects=0, CPUs=1, Nodes=1                                                                                                                       
[    0.000000] rcu: Hierarchical RCU implementation.                                                                                                                                            
[    0.000000] rcu:     RCU restricting CPUs from NR_CPUS=64 to nr_cpu_ids=1.                                                                                                                   
[    0.000000] rcu:     RCU debug extended QS entry/exit.                                                                                                                                       
[    0.000000]  Tracing variant of Tasks RCU enabled.                                                                                                                                           
[    0.000000] rcu: RCU calculated value of scheduler-enlistment delay is 25 jiffies.                                                                                                           
[    0.000000] rcu: Adjusting geometry for rcu_fanout_leaf=16, nr_cpu_ids=1                                                                                                                     
[    0.000000] NR_IRQS: 64, nr_irqs: 64, preallocated irqs: 0                                                                                                                                   
[    0.000000] riscv-intc: 32 local interrupts mapped                                                                                                                                           
[    0.000000] plic: plic@c000000: mapped 95 interrupts with 1 handlers for 2 contexts.                                                                                                         
[    0.000000] riscv: providing IPIs using SBI IPI extension                                                                                                                                    
[    0.000000] rcu: srcu_init: Setting srcu_struct sizes based on contention.                   
[    0.000000] clocksource: riscv_clocksource: mask: 0xffffffffffffffff max_cycles: 0x24e6a1710, max_idle_ns: 440795202120 ns
[    0.000195] sched_clock: 64 bits at 10MHz, resolution 100ns, wraps every 4398046511100ns                                                                                                     
[    0.000392] riscv-timer: Timer interrupt in S-mode is available via sstc extension                                                                                                           
[    0.014400] Console: colour dummy device 80x25                                                                                                                                               
[    0.016629] Calibrating delay loop (skipped), value calculated using timer frequency.. 20.00 BogoMIPS (lpj=40000)                                                                            
[    0.016925] pid_max: default: 32768 minimum: 301                                                                                                                                             
[    0.018686] LSM: initializing lsm=capability,integrity                                                                                                                                       
[    0.021504] Mount-cache hash table entries: 2048 (order: 1, 8192 bytes, linear)                                                                                                              
[    0.021558] Mountpoint-cache hash table entries: 2048 (order: 1, 8192 bytes, linear)                                                                                                         
[    0.057086] RCU Tasks Trace: Setting shift to 0 and lim to 1 rcu_task_cb_adjust=1.                                                                                                           
[    0.057808] ASID allocator using 9 bits (512 entries)                                                                                                                                        
[    0.059208] rcu: Hierarchical SRCU implementation.                                                                                                                                           
[    0.059250] rcu:     Max phase no-delay instances is 1000.                                                                                                                                   
[    0.062347] EFI services will not be available.                                                                                                                                              
[    0.064094] smp: Bringing up secondary CPUs ...                                              
[    0.065574] smp: Brought up 1 node, 1 CPU                                                                                                                                                    
[    0.080584] devtmpfs: initialized                                                                                                                                                            
[    0.091492] clocksource: jiffies: mask: 0xffffffff max_cycles: 0xffffffff, max_idle_ns: 7645041785100000 ns                                                                                  
[    0.091685] futex hash table entries: 256 (order: 2, 16384 bytes, linear)                                                                                                                    
[    0.094677] pinctrl core: initialized pinctrl subsystem                                                                                                                                      
[    0.104328] NET: Registered PF_NETLINK/PF_ROUTE protocol family                                                                                                                              
[    0.116225] DMA: preallocated 128 KiB GFP_KERNEL pool for atomic allocations                                                                                                                 
[    0.116668] audit: initializing netlink subsys (disabled)                                                                                                                                    
[    0.122570] thermal_sys: Registered thermal governor 'step_wise'                             
[    0.123596] audit: type=2000 audit(0.108:1): state=initialized audit_enabled=0 res=1                                                                                                         
[    0.124386] cpuidle: using governor menu                                                                                                                                                     
[    0.150114] cpu0: Ratio of byte access time to unaligned word access is 3.59, unaligned accesses are fast                                                                                    
[    0.171431] HugeTLB: registered 4.00 MiB page size, pre-allocated 0 pages                                                                                                                    
[    0.171461] HugeTLB: 0 KiB vmemmap can be freed for a 4.00 MiB page                          
[    0.176200] iommu: Default domain type: Translated                                           
[    0.176247] iommu: DMA domain TLB invalidation policy: strict mode                                                                                                                           
[    0.179087] SCSI subsystem initialized                                                                                                                                                       
[    0.181426] usbcore: registered new interface driver usbfs
[    0.179087] SCSI subsystem initialized                                                                                                                                     13:45:33 [102/542][    0.181426] usbcore: registered new interface driver usbfs                                                                                                                                   
[    0.181805] usbcore: registered new interface driver hub                                                                                                                                     
[    0.182035] usbcore: registered new device driver usb                                                                                                                                        
[    0.199947] vgaarb: loaded                                                                                                                                                                   
[    0.204320] clocksource: Switched to clocksource riscv_clocksource                                                                                                                           
[    0.234921] NET: Registered PF_INET protocol family                                                                                                                                          
[    0.236384] IP idents hash table entries: 16384 (order: 5, 131072 bytes, linear)                                                                                                             
[    0.244620] tcp_listen_portaddr_hash hash table entries: 512 (order: 1, 10240 bytes, linear)                                                                                                 
[    0.244792] Table-perturb hash table entries: 65536 (order: 6, 262144 bytes, linear)                                                                                                         
[    0.244891] TCP established hash table entries: 8192 (order: 3, 32768 bytes, linear)                                                                                                         
[    0.245239] TCP bind hash table entries: 8192 (order: 6, 327680 bytes, linear)                                                                                                               
[    0.247196] TCP: Hash tables configured (established 8192 bind 8192)                         
[    0.248631] UDP hash table entries: 512 (order: 2, 24576 bytes, linear)                                                                                                                      
[    0.249057] UDP-Lite hash table entries: 512 (order: 2, 24576 bytes, linear)                                                                                                                 
[    0.250627] NET: Registered PF_UNIX/PF_LOCAL protocol family                                                                                                                                 
[    0.254434] RPC: Registered named UNIX socket transport module.                                                                                                                              
[    0.254501] RPC: Registered udp transport module.                                                                                                                                            
[    0.254517] RPC: Registered tcp transport module.                                                                                                                                            
[    0.254531] RPC: Registered tcp-with-tls transport module.                                                                                                                                   
[    0.254544] RPC: Registered tcp NFSv4.1 backchannel transport module.                                                                                                                        
[    0.254757] PCI: CLS 0 bytes, default 64                                                                                                                                                     
[    0.264859] workingset: timestamp_bits=30 max_order=18 bucket_order=0                                                                                                                        
[    0.269895] NFS: Registering the id_resolver key type                                                                                                                                        
[    0.271351] Key type id_resolver registered                                                                                                                                                  
[    0.271403] Key type id_legacy registered                                                                                                                                                    
[    0.271764] nfs4filelayout_init: NFSv4 File Layout Driver Registering...                                                                                                                     
[    0.271880] nfs4flexfilelayout_init: NFSv4 Flexfile Layout Driver Registering...             
[    0.272778] fuse: init (API version 7.39)                                                                                                                                                    
[    0.273419] 9p: Installing v9fs 9p2000 file system support                                                                                                                                   
[    0.275708] NET: Registered PF_ALG protocol family                                                                                                                                           
[    0.276141] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 247)                                                                                                             
[    0.276310] io scheduler mq-deadline registered                                                                                                                                              
[    0.276409] io scheduler kyber registered                                                                                                                                                    
[    0.276542] io scheduler bfq registered                                                                                                                                                      
[    0.282389] pci-host-generic 30000000.pci: host bridge /soc/pci@30000000 ranges:                                                                                                             
[    0.283522] pci-host-generic 30000000.pci:       IO 0x0003000000..0x000300ffff -> 0x0000000000
[    0.284180] pci-host-generic 30000000.pci:      MEM 0x0040000000..0x007fffffff -> 0x0040000000                                                                                               
[    0.284274] pci-host-generic 30000000.pci:      MEM 0x0300000000..0x03ffffffff -> 0x0300000000                                                                                               
[    0.285271] pci-host-generic 30000000.pci: ECAM at [mem 0x30000000-0x3fffffff] for [bus 00-ff]                                                                                               
[    0.287352] pci-host-generic 30000000.pci: PCI host bridge to bus 0000:00                                                                                                                    
[    0.287656] pci_bus 0000:00: root bus resource [bus 00-ff]                                   
[    0.287805] pci_bus 0000:00: root bus resource [io  0x0000-0xffff]                           
[    0.287871] pci_bus 0000:00: root bus resource [mem 0x40000000-0x7fffffff]                                                                                                                   
[    0.289501] pci 0000:00:00.0: [1b36:0008] type 00 class 0x060000                                                                                                                             
[    0.428584] Serial: 8250/16550 driver, 4 ports, IRQ sharing disabled
[    0.289501] pci 0000:00:00.0: [1b36:0008] type 00 class 0x060000                                                                                                            13:45:34 [58/542][    0.428584] Serial: 8250/16550 driver, 4 ports, IRQ sharing disabled                                                                                                                         
[    0.444324] printk: console [ttyS0] disabled                                                                                                                                                 
[    0.448862] 10000000.serial: ttyS0 at MMIO 0x10000000 (irq = 12, base_baud = 230400) is a 16550A                                                                                             
[    0.450984] printk: console [ttyS0] enabled                                                                                                                                                  
[    0.491318] SuperH (H)SCI(F) driver initialized                                                                                                                                              
[    0.513703] loop: module loaded                                                                                                                                                              
[    0.559460] virtio_blk virtio0: 1/0/0 default/read/poll queues                                                                                                                               
[    0.565074] virtio_blk virtio0: [vda] 20480 512-byte logical blocks (10.5 MB/10.0 MiB)                                                                                                       
[    0.592215] e1000e: Intel(R) PRO/1000 Network Driver                                                                                                                                         
[    0.592512] e1000e: Copyright(c) 1999 - 2015 Intel Corporation.                                                                                                                              
[    0.596417] usbcore: registered new interface driver uas                                                                                                                                     
[    0.596978] usbcore: registered new interface driver usb-storage                             
[    0.598588] mousedev: PS/2 mouse device common for all mice                                                                                                                                  
[    0.603133] goldfish_rtc 101000.rtc: registered as rtc0                                                                                                                                      
[    0.604310] goldfish_rtc 101000.rtc: setting system clock to 2024-07-31T05:45:32 UTC (1722404732)                                                                                            
[    0.608595] syscon-poweroff poweroff: pm_power_off already claimed for sbi_srst_power_off                                                                                                    
[    0.609641] syscon-poweroff: probe of poweroff failed with error -16                                                                                                                         
[    0.612269] sdhci: Secure Digital Host Controller Interface driver                                                                                                                           
[    0.612655] sdhci: Copyright(c) Pierre Ossman                                                                                                                                                
[    0.613470] sdhci-pltfm: SDHCI platform and OF driver helper                                                                                                                                 
[    0.614729] usbcore: registered new interface driver usbhid                                                                                                                                  
[    0.615164] usbhid: USB HID core driver                                                                                                                                                      
[    0.616033] riscv-pmu-sbi: SBI PMU extension is available                                                                                                                                    
[    0.617009] riscv-pmu-sbi: 16 firmware and 18 hardware counters                                                                                                                              
[    0.617435] riscv-pmu-sbi: Perf sampling/filtering is not supported as sscof extension is not available                                                                                      
[    0.621304] NET: Registered PF_INET6 protocol family                                                                                                                                         
[    0.630595] Segment Routing with IPv6                                                        
[    0.631227] In-situ OAM (IOAM) with IPv6                                                                                                                                                     
[    0.632063] sit: IPv6, IPv4 and MPLS over IPv4 tunneling driver                                                                                                                              
[    0.636507] NET: Registered PF_PACKET protocol family                                                                                                                                        
[    0.638504] 9pnet: Installing 9P2000 support                                                                                                                                                 
[    0.639237] Key type dns_resolver registered                                                                                                                                                 
[    0.688199] debug_vm_pgtable: [debug_vm_pgtable         ]: Validating architecture page table helpers                                                                                        
[    0.696667] clk: Disabling unused clocks                                                                                                                                                     
[    0.716949] EXT4-fs (vda): mounting ext2 file system using the ext4 subsystem                                                                                                                
[    0.731220] EXT4-fs (vda): mounted filesystem 0658dec4-4d7b-4983-b4bf-5fe885737c74 ro without journal. Quota mode: disabled.
[    0.732209] VFS: Mounted root (ext2 filesystem) readonly on device 254:0.                                                                                                                    
[    0.736543] devtmpfs: mounted                                                                                                                                                                
[    0.804626] Freeing unused kernel image (initmem) memory: 324K                                                                                                                               
[    0.805130] Kernel memory protection not selected by kernel config.                                                                                                                          
[    0.805720] Run /sbin/init as init process                                                   
[    1.032018] EXT4-fs (vda): warning: mounting unchecked fs, running e2fsck is recommended     
[    1.043662] EXT4-fs (vda): re-mounted 0658dec4-4d7b-4983-b4bf-5fe885737c74 r/w. Quota mode: disabled.                                                                                        
Seeding 256 bits and crediting                                                                                                                                                                  
Saving 256 bits of creditable seed for next boot
Seeding 256 bits and crediting                                                                                                                                                 13:45:46 [14/542]Saving 256 bits of creditable seed for next boot                                                                                                                                                
Starting syslogd: OK                                                                                                                                                                            
Starting klogd: OK                                                                                                                                                                              
Running sysctl: OK                                                                                                                                                                              
Starting network: OK                                                                                                                                                                            
                                                                                                                                                                                                
===============================================                                                                                                                                                 
=========== iperf3 TCP test ===================                                                                                                                                                 
===============================================                                                                                                                                                 
-----------------------------------------------------------                                                                                                                                     
Server listening on 5201 (test #1)                                                                                                                                                              
-----------------------------------------------------------                                     
Accepted connection from 127.0.0.1, port 54300                                                                                                                                                  
Connecting to host 127.0.0.1, port 5201                                                                                                                                                         
[  5] local 127.0.0.1 port 54316 connected to 127.0.0.1 port 5201                                                                                                                               
[  5] local 127.0.0.1 port 5201 connected to 127.0.0.1 port 54316                                                                                                                               
[ ID] Interval           Transfer     Bitrate         Retr  Cwnd                                                                                                                                
[ ID] Interval           Transfer     Bitrate                                                                                                                                                   
[  5]   0.00-1.00   sec   275 MBytes  2.31 Gbits/sec                                                                                                                                            
[  5]   0.00-1.00   sec   276 MBytes  2.31 Gbits/sec    0   2.81 MBytes                                                                                                                         
[  5]   1.00-2.00   sec   274 MBytes  2.30 Gbits/sec                                                                                                                                            
[  5]   1.00-2.00   sec   275 MBytes  2.31 Gbits/sec    0   3.00 MBytes                                                                                                                         
[  5]   2.00-3.00   sec   277 MBytes  2.32 Gbits/sec                                                                                                                                            
[  5]   2.00-3.00   sec   278 MBytes  2.33 Gbits/sec    0   3.12 MBytes                                                                                                                         
[  5]   3.00-4.00   sec   278 MBytes  2.33 Gbits/sec                                                                                                                                            
[  5]   3.00-4.00   sec   278 MBytes  2.33 Gbits/sec    0   3.12 MBytes                                                                                                                         
[  5]   4.00-5.00   sec   278 MBytes  2.34 Gbits/sec                                            
[  5]   4.00-5.00   sec   278 MBytes  2.33 Gbits/sec    0   3.12 MBytes                                                                                                                         
[  5]   5.00-6.00   sec   278 MBytes  2.33 Gbits/sec                                                                                                                                            
[  5]   5.00-6.00   sec   279 MBytes  2.33 Gbits/sec    0   3.12 MBytes                                                                                                                         
[  5]   6.00-7.00   sec   277 MBytes  2.32 Gbits/sec                                                                                                                                            
[  5]   6.00-7.00   sec   276 MBytes  2.32 Gbits/sec    0   3.12 MBytes                                                                                                                         
[  5]   7.00-8.00   sec   277 MBytes  2.32 Gbits/sec                                                                                                                                            
[  5]   7.00-8.00   sec   278 MBytes  2.32 Gbits/sec    0   3.12 MBytes                                                                                                                         
[  5]   8.00-9.00   sec   277 MBytes  2.32 Gbits/sec                                                                                                                                            
[  5]   8.00-9.00   sec   276 MBytes  2.32 Gbits/sec    0   4.75 MBytes                                                                                                                         
[  5]   9.00-10.00  sec   276 MBytes  2.32 Gbits/sec                                                                                                                                            
[  5]  10.00-10.01  sec  1.50 MBytes  1.69 Gbits/sec                                                                                                                                            
- - - - - - - - - - - - - - - - - - - - - - - - -                                                                                                                                               
[ ID] Interval           Transfer     Bitrate                                                                                                                                                   
[  5]   0.00-10.01  sec  2.70 GBytes  2.32 Gbits/sec                  receiver                  
[  5]   9.00-10.00  sec   276 MBytes  2.31 Gbits/sec    0   4.75 MBytes                         
- - - - - - - - - - - - - - - - - - - - - - - - -                                                                                                                                               
[ ID] Interval           Transfer     Bitrate         Retr                                                                                                                                      
[  5]   0.00-10.00  sec  2.70 GBytes  2.32 Gbits/sec    0             sender
Connecting to host 127.0.0.1, port 5201                                                                                                                                                  [0/542][  5] local 127.0.0.1 port 54316 connected to 127.0.0.1 port 5201                                                                                                                               
[  5] local 127.0.0.1 port 5201 connected to 127.0.0.1 port 54316                                                                                                                               
[ ID] Interval           Transfer     Bitrate         Retr  Cwnd                                                                                                                                
[ ID] Interval           Transfer     Bitrate                                                                                                                                                   
[  5]   0.00-1.00   sec   275 MBytes  2.31 Gbits/sec                                                                                                                                            
[  5]   0.00-1.00   sec   276 MBytes  2.31 Gbits/sec    0   2.81 MBytes                                                                                                                         
[  5]   1.00-2.00   sec   274 MBytes  2.30 Gbits/sec                                                                                                                                            
[  5]   1.00-2.00   sec   275 MBytes  2.31 Gbits/sec    0   3.00 MBytes                                                                                                                         
[  5]   2.00-3.00   sec   277 MBytes  2.32 Gbits/sec                                                                                                                                            
[  5]   2.00-3.00   sec   278 MBytes  2.33 Gbits/sec    0   3.12 MBytes                                                                                                                         
[  5]   3.00-4.00   sec   278 MBytes  2.33 Gbits/sec                                                                                                                                            
[  5]   3.00-4.00   sec   278 MBytes  2.33 Gbits/sec    0   3.12 MBytes                         
[  5]   4.00-5.00   sec   278 MBytes  2.34 Gbits/sec                                                                                                                                            
[  5]   4.00-5.00   sec   278 MBytes  2.33 Gbits/sec    0   3.12 MBytes                                                                                                                         
[  5]   5.00-6.00   sec   278 MBytes  2.33 Gbits/sec                                                                                                                                            
[  5]   5.00-6.00   sec   279 MBytes  2.33 Gbits/sec    0   3.12 MBytes                                                                                                                         
[  5]   6.00-7.00   sec   277 MBytes  2.32 Gbits/sec                                                                                                                                            
[  5]   6.00-7.00   sec   276 MBytes  2.32 Gbits/sec    0   3.12 MBytes                                                                                                                         
[  5]   7.00-8.00   sec   277 MBytes  2.32 Gbits/sec                                                                                                                                            
[  5]   7.00-8.00   sec   278 MBytes  2.32 Gbits/sec    0   3.12 MBytes                                                                                                                         
[  5]   8.00-9.00   sec   277 MBytes  2.32 Gbits/sec                                                                                                                                            
[  5]   8.00-9.00   sec   276 MBytes  2.32 Gbits/sec    0   4.75 MBytes                                                                                                                         
[  5]   9.00-10.00  sec   276 MBytes  2.32 Gbits/sec                                                                                                                                            
[  5]  10.00-10.01  sec  1.50 MBytes  1.69 Gbits/sec                                                                                                                                            
- - - - - - - - - - - - - - - - - - - - - - - - -                                                                                                                                               
[ ID] Interval           Transfer     Bitrate                                                                                                                                                   
[  5]   0.00-10.01  sec  2.70 GBytes  2.32 Gbits/sec                  receiver                  
[  5]   9.00-10.00  sec   276 MBytes  2.31 Gbits/sec    0   4.75 MBytes                                                                                                                         
- - - - - - - - - - - - - - - - - - - - - - - - -                                                                                                                                               
[ ID] Interval           Transfer     Bitrate         Retr                                                                                                                                      
[  5]   0.00-10.00  sec  2.70 GBytes  2.32 Gbits/sec    0             sender                                                                                                                    
[  5]   0.00-10.01  sec  2.70 GBytes  2.32 Gbits/sec                  receiver                                                                                                                  
                                                                                                                                                                                                
iperf Done.                                                                                                                                                                                     
-----------------------------------------------------------                                                                                                                                     
Server listening on 5201 (test #2)                                                                                                                                                              
-----------------------------------------------------------                                                                                                                                     
                                                                                                                                                                                                
Welcome to Buildroot                                                                                                                                                                            
buildroot login:
```