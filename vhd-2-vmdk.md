# Converting a Hyper-V VHD/VHDx to VMWare Workstation VMDK

If you ever find yourself in a situation where for example you want to test out things in a specific VM in VMWare Player or your dedicated Hyper-V hypervisor blew up, and all that was left after the carnage was VHD images, you might be considering switching to Xen,Proxmox or ESXi. There are many commercial and paid solutions for doing the conversion, but there's always the free way of doing it if you don't fear the command prompt or terminal.  

### Obtaining QEMU-img

QEMU is capable of converting VM image files by using qemu-img utility.  
To obtain it on Linux, install QEMU or just qemu-utils from your system repositories. Yes works with WSL as well.  
To get it for Windows, you'll have to [get it from here](https://www.qemu.org/download/). Make sure you leave the tools enabled in the setup. You will find qemu-img.exe in the root directory where you installed it.

### Conversion
I'd recommend looking at the help file by using `qemu-img -h`. My command does not include a progress bar, so you should look at it if you want to add one yourself.  

To start the conversion, call `qemu-img convert vm-from-hyperv.vhd -O vmdk vm-to-vmware.vmdk`.  

`convert` flag sets the utility to convert it and the `-O vmdk` flag sets an option to convert it to vmdk.  

Here's the command I had to run:

```
\qemu-img.exe convert M:\Zabbix.vhdx -O vmdk M:\zabbix-vmware.vmdk
```

Now go grab a cup of coffee, because it will take a while to convert depending on the size.


### Disclaimer 

This VMDK image is in VMWare Workstation 3/4 format. You should convert it once you import it. It will also do not work in ESXi unless you [convert it](/workstation-vmdk-2-esxi).