# Fixing Ubuntu VM on Hyper-V stuck in read-only mode after hot backup or live migration

If you use Ubuntu in a Hyper-V hypervisor and run backups utilizing Hyper-V VSS Writer like Veeam Backup and Replication, you might find yourself in situation where the OS will report IO issues and the VM will go into read-only mode, and a hard reboot is not fixing it. First of all, don't panic! There's a big chance that you did not install the OS from Microsoft's image, you did P2V conversion or you migrated the VM from a different hypervisor. Check `dmesg` and look for this string:  

```
[210731.648573]hv_utils: VSS: failed to communicate to the daemon: -22
```

You will most likely also find these lines in `dmesg`  

```
[210731.648573] hv_utils: VSS: failed to communicate to the daemon: -22
[277124.230214] EXT4-fs (sda2): error count since last fsck: 1
[277124.230219] EXT4-fs (sda2): initial error at time 1611102763: __ext4_get_inode_loc:4703: inode 262816: block 1048649
[277124.230224] EXT4-fs (sda2): last error at time 1611102763: __ext4_get_inode_loc:4703: inode 262816: block 1048649
[369398.918203] EXT4-fs (sda2): error count since last fsck: 1
[369398.918208] EXT4-fs (sda2): initial error at time 1611102763: __ext4_get_inode_loc:4703: inode 262816: block 1048649
[369398.918213] EXT4-fs (sda2): last error at time 1611102763: __ext4_get_inode_loc:4703: inode 262816: block 1048649
[461673.606203] EXT4-fs (sda2): error count since last fsck: 1
[461673.606208] EXT4-fs (sda2): initial error at time 1611102763: __ext4_get_inode_loc:4703: inode 262816: block 1048649
[461673.606213] EXT4-fs (sda2): last error at time 1611102763: __ext4_get_inode_loc:4703: inode 262816: block 1048649
[517697.666181] sd 0:0:0:0: [storvsc] Sense Key : Unit Attention [current]
[517697.666188] sd 0:0:0:0: [storvsc] Add. Sense: Changed operating definition
[517697.666207] sd 0:0:0:0: Warning! Received an indication that the operating parameters on this target have changed. The Linux SCSI layer does not automa
```

No, there's no hardware issue with the host drive you use.

## Fixing the filesystem to boot into read-write state

You should invoke filesystem checks to fix any consistency issues. Once you're logged into the host in read-only mode, execute `sudo fsck -Af -M`. This will force a check of all filesystems present on the host and skip any mounted filesystems to prevent data corruption. If you run `fsck` but only output you get is the version header, you should use the filesystem specific `fsck`. In my case, I use EXT4 filesystem and have to run `sudo fsck.ext4 -f /dev/sda2` as `sda1` is the boot partition. This should fix the filesystem and allow you to automatically boot into read-write mode once you reboot the virtual machine. In order to remedy this issue, you need to use the Hyper-V Linux Integration Services.

```
~$ sudo fsck -Af -M
fsck from util-linux 2.34

~$ sudo fsck.ext4 -f /dev/sda2
e2fsck 1.45.5 (07-Jan-2020)
Pass 1: Checking inodes, blocks, and sizes
Inodes that were part of a corrupted orphan linked list found.  Fix<y>?
...
/dev/sda2: ***** FILE SYSTEM WAS MODIFIED *****
/dev/sda2: ***** REBOOT SYSTEM *****
/dev/sda2: 277049/7299072 files (0.3% non-contiguous), 4740399/29175296 blocks
```


## Installing Hyper-V Linux Integration Services for Ubuntu LTS

For non-Ubuntu Linux distributions, you'd just have to install the Linux Integration Services package that's usually named `hyperv-daemons`, but you will quickly find out that Ubuntu does not have such package. If you look at the Microsoft's [Supported Ubuntu virtual machines on Hyper-V](https://docs.microsoft.com/en-us/windows-server/virtualization/hyper-v/supported-ubuntu-virtual-machines-on-hyper-v) documentation, and scroll down to `Live virtual machine backup`, you'll see multiple notes. Pay attention to the 5th note, stating that you should be using the latest virtual Hardware Enablement (HWE) kernel for up to date Linux Integration Services. You have to install the Azure-tuned kernel:  

```
apt-get update
apt-get install linux-azure
```


This will automatically install the Azure-tuned Linux kernel, or will alternatively compile it for you and automatically modify GRUB so the VM automatically boots up with the correct kernel.

Before:  
```
Linux dub-lab-ubuntu01 5.4.0-62-generic #70-Ubuntu SMP Tue Jan 12 12:45:47 UTC 2021 x86_64 x86_64 x86_64 GNU/Linux
```

After:
```
Linux dub-lab-ubuntu01 5.4.0-1039-azure #41-Ubuntu SMP Mon Jan 18 13:22:11 UTC 2021 x86_64 x86_64 x86_64 GNU/Linux
```

You should also take a look at the [Best Practices for running Linux on Hyper-V](https://docs.microsoft.com/en-us/windows-server/virtualization/hyper-v/best-practices-for-running-linux-on-hyper-v) documentation for better VM performance on the Hyper-V hypervisor.