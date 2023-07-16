---
title: PCのスペックを確認する
date: 2023-07-15 10:00:00 +0900
categories: [Shell]
tags: [shell]
pin: false
---

し

## 概要

- PC のスペックを確認したい時があり、コマンドをまとめておく

## 参考

- [UbuntuのOSやCPU, GPUの情報を確認するコマンド](https://qiita.com/sabaku20XX/items/97db2c0bf7298e3a645c#gpu%E3%81%AE%E3%83%8F%E3%83%BC%E3%83%89%E6%83%85%E5%A0%B1)
- [Linuxでコマンドラインからマシンスペックを確認する方法](https://qiita.com/DaisukeMiyamoto/items/98ef077ddf44b5727c29)
- [Linuxで載ってるメモリ（DDR）の種類を調べるコマンド](https://qiita.com/aokomoriuta/items/74a807300f644d623a02)

## PC スペックの確認コマンド

### OS の情報

- os のバージョン

```bash
$ lsb_release -a
No LSB modules are available.
Distributor ID:	Ubuntu
Description:	Ubuntu 20.04.6 LTS
Release:	20.04
Codename:	focal

```

- カーネルバージョン

```bash
$ uname -a
Linux dev 5.15.0-76-generic #83~20.04.1-Ubuntu SMP Wed Jun 21 20:23:31 UTC 2023 x86_64 x86_64 x86_64 GNU/Linux
```

### cpu のスペック

```bash
$ sudo lshw -class processor
  *-cpu
       description: CPU
       product: 11th Gen Intel(R) Core(TM) i9-11900H @ 2.50GHz
       vendor: Intel Corp.
       physical id: 4d
       bus info: cpu@0
       version: 11th Gen Intel(R) Core(TM) i9-11900H @ 2.50GHz
       serial: To Be Filled By O.E.M.
       slot: U3E1
       size: 984MHz
       capacity: 4900MHz
       width: 64 bits
       clock: 100MHz
       capabilities: lm fpu fpu_exception wp vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush dts acpi mmx fxsr sse sse2 ss ht tm pbe syscall nx pdpe1gb rdtscp x86-64 constant_tsc art arch_perfmon pebs bts rep_good nopl xtopology nonstop_tsc cpuid aperfmperf tsc_known_freq pni pclmulqdq dtes64 monitor ds_cpl vmx est tm2 ssse3 sdbg fma cx16 xtpr pdcm pcid sse4_1 sse4_2 x2apic movbe popcnt tsc_deadline_timer aes xsave avx f16c rdrand lahf_lm abm 3dnowprefetch cpuid_fault epb cat_l2 invpcid_single cdp_l2 ssbd ibrs ibpb stibp ibrs_enhanced tpr_shadow vnmi flexpriority ept vpid ept_ad fsgsbase tsc_adjust bmi1 avx2 smep bmi2 erms invpcid rdt_a avx512f avx512dq rdseed adx smap avx512ifma clflushopt clwb intel_pt avx512cd sha_ni avx512bw avx512vl xsaveopt xsavec xgetbv1 xsaves split_lock_detect dtherm ida arat pln pts hwp hwp_notify hwp_act_window hwp_epp hwp_pkg_req avx512vbmi umip pku ospke avx512_vbmi2 gfni vaes vpclmulqdq avx512_vnni avx512_bitalg avx512_vpopcntdq rdpid movdiri movdir64b fsrm avx512_vp2intersect md_clear flush_l1d arch_capabilities cpufreq
       configuration: cores=8 enabledcores=8 threads=16
```

### RAM のスペック

```bash
$ sudo dmidecode --type memory
# dmidecode 3.2
Getting SMBIOS data from sysfs.
SMBIOS 3.3.0 present.
# SMBIOS implementations newer than version 3.2.0 are not
# fully supported by this version of dmidecode.

Handle 0x003C, DMI type 16, 23 bytes
Physical Memory Array
	Location: System Board Or Motherboard
	Use: System Memory
	Error Correction Type: None
	Maximum Capacity: 32 GB
	Error Information Handle: Not Provided
	Number Of Devices: 2

Handle 0x0040, DMI type 17, 92 bytes
Memory Device
	Array Handle: 0x003C
	Error Information Handle: Not Provided
	Total Width: 64 bits
	Data Width: 64 bits
	Size: 16384 MB
	Form Factor: SODIMM
	Set: None
	Locator: Controller0-ChannelA-DIMM0
	Bank Locator: BANK 0
	Type: DDR4
	Type Detail: Synchronous
	Speed: 3200 MT/s
	Manufacturer: Samsung
	Serial Number: 417EE8C0
	Asset Tag: 9876543210
	Part Number: M471A2K43EB1-CWE
	Rank: 2
	Configured Memory Speed: 3200 MT/s
	Minimum Voltage: 1.2 V
	Maximum Voltage: 1.2 V
	Configured Voltage: 1.2 V
	Memory Technology: DRAM
	Memory Operating Mode Capability: Volatile memory
	Firmware Version: Not Specified
	Module Manufacturer ID: Bank 1, Hex 0xCE
	Module Product ID: Unknown
	Memory Subsystem Controller Manufacturer ID: Unknown
	Memory Subsystem Controller Product ID: Unknown
	Non-Volatile Size: None
	Volatile Size: 16 GB
	Cache Size: None
	Logical Size: None

Handle 0x0041, DMI type 17, 92 bytes
Memory Device
	Array Handle: 0x003C
	Error Information Handle: Not Provided
	Total Width: 64 bits
	Data Width: 64 bits
	Size: 16384 MB
	Form Factor: SODIMM
	Set: None
	Locator: Controller1-ChannelA-DIMM0
	Bank Locator: BANK 0
	Type: DDR4
	Type Detail: Synchronous
	Speed: 3200 MT/s
	Manufacturer: Samsung
	Serial Number: 417EE45E
	Asset Tag: 9876543210
	Part Number: M471A2K43EB1-CWE
	Rank: 2
	Configured Memory Speed: 3200 MT/s
	Minimum Voltage: 1.2 V
	Maximum Voltage: 1.2 V
	Configured Voltage: 1.2 V
	Memory Technology: DRAM
	Memory Operating Mode Capability: Volatile memory
	Firmware Version: Not Specified
	Module Manufacturer ID: Bank 1, Hex 0xCE
	Module Product ID: Unknown
	Memory Subsystem Controller Manufacturer ID: Unknown
	Memory Subsystem Controller Product ID: Unknown
	Non-Volatile Size: None
	Volatile Size: 16 GB
	Cache Size: None
	Logical Size: None
```

### GPU のスペック

```bash
$ nvidia-smi
Sun Jul 16 11:54:38 2023
+---------------------------------------------------------------------------------------+
| NVIDIA-SMI 530.30.02              Driver Version: 530.30.02    CUDA Version: 12.1     |
|-----------------------------------------+----------------------+----------------------+
| GPU  Name                  Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf            Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|                                         |                      |               MIG M. |
|=========================================+======================+======================|
|   0  NVIDIA GeForce RTX 3080 L...    On | 00000000:01:00.0 Off |                  N/A |
| N/A   45C    P5               N/A /  35W|     10MiB / 16384MiB |      0%      Default |
|                                         |                      |                  N/A |
+-----------------------------------------+----------------------+----------------------+

+---------------------------------------------------------------------------------------+
| Processes:                                                                            |
|  GPU   GI   CI        PID   Type   Process name                            GPU Memory |
|        ID   ID                                                             Usage      |
|=======================================================================================|
|    0   N/A  N/A      1281      G   /usr/lib/xorg/Xorg                            4MiB |
|    0   N/A  N/A      2205      G   /usr/lib/xorg/Xorg                            4MiB |
+---------------------------------------------------------------------------------------+

```


## PCのスペックをlogファイルとして出力するスクリプト

```bash
#!/bin/sh

echo "==== os version ====" >> ~/pc_spec.log
lsb_release -a >> ~/pc_spec.log
echo "==== kernel version ====" >> ~/pc_spec.log
uname -a >> ~/pc_spec.log
echo "==== cpu info ====" >> ~/pc_spec.log
nvidia-smi >> ~/pc_spec.log
echo "==== gpu info ====" >> ~/pc_spec.log
sudo dmidecode --type memory >> ~/pc_spec.log

```