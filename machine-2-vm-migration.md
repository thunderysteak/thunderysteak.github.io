# Migrating a physical server or a VM from a different hypervisor into a new VM

If you have a physical server that you want to virtualize, or have a VM on XenServer, Hyper-V or ESXi that you want to move to a different hypervisor, you don't have that many options. For ESXi, there's [vCenter Converter](https://www.vmware.com/products/converter.html) that allows you to migrate the system to an ESXi hypervisor. Citrix had something similar called XenConvert, but that's not supported since the version of 6.2 of XenServer.

### Cloning to external storage and restoring image

This option is straightforward and you can utilize any cloning software that you prefer. You can even use `dd` in Linux.  

1. Create bootable media of cloning software or mount the cloning software
2. (Optional) Shrink the partition for space optimization
3. Clone the system onto external or network storage
4. Mount your cloning software in the VM and start it
5. Boot off it and restore the cloned image.  

You can do this in case you feel comfortable using specific cloning software like Acronis.

![](/img/ApplicationFrameHost_2019-11-12_20-39-57.png)

### Direct disk clone over the network with Clonezilla

If the machines are on the same network and can access each other, you can use Clonezilla's network restore feature.  
Boot up Clonezilla on both systems, selecting `remote-source` on the machine you want to clone and selecting `remote-dest` in the VM.   

![](/img/vmplayer_2019-11-12_21-28-03.png)

Proceed like you'd normally do with Clonezilla. Remote source system should show you the server IP that you need to enter in the remote-dest VM. After that, it should clone the drive remotely with ease.

![](/img/vmplayer_2019-11-12_21-31-20.png)


### Linux migration to VM

You will most likely encounter the VM ending up with a kernel panic on boot, but it running safely in rescue mode. This means that you have to rebuild the `initramfs` image due to hardware change. 