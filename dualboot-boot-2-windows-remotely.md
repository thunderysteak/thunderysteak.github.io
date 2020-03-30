# Booting to Windows remotely on dual-boot CentOS 7 system

This guide is useful for people that WFH and their office computer is a Linux and Windows dual-boot system.  
For example, you don't want to bother or can't reach someone in the office to manually press two arrow keys down to boot into Windows or your local IT is finding your requests annoying and don't know how to reboot to a different system remotely. Yes, you will still be able to boot into Linux after this. This will only temporaly change the default boot target and will switch back to its default entry on next boot.

### Prerequisites

There's only one thing you need to remember, and that's your boot menu entries. If you know that, great! [You can skip directly to the booting section](#selecting-os-for-booting-remotely).  

If you forgot and you didn't mess with the menu too much, [you can easily find out the menu entries](https://wiki.centos.org/HowTos/Grub2). Be aware, that this following information might differ from your setup and should be tested locally first (or have local IT nearby if you're WFH).  

### Listing your GRUB2 menu entries

Enter this following command to list the entires:

```
awk -F\' '$1=="menuentry " {print i++ " : " $2}' /etc/grub2.cfg
```
You should get something like this:

![](/img/chrome_2020-03-30_00-08-52.png)  


If you're using LVM, you need to check if you need to mount the boot partition first. Execute `df /boot` to see if you're in the proper boot partition, or just the boot folder on your LVM root volume. Boot partition is usually /dev/sda1. If you find out that your `/boot` is located on the LVM volume, mount the boot partition to `/boot` using `mount /dev/sdXX /boot`.

If you remember seeing Windows in your boot menu but doesn't appear when you run the awk command, your dualbooting is most likely setup with the 40_custom entry in GRUB2 and will usually be the last entry. If you had more than one Windows boot entry, continue reading the next chapter, if not, you can skip it.  

### Viewing 40_custom entries

Any entry you add to the `40_custom` file will depend on the formatting it was entered with or or the AWK command. The one that the CentOS documentation provides will most likely not show it, and you'll have to look manually.  

To view the `40_custom` entries, you can either view the `/etc/grub.d/40_custom` entries or view the `/boot/grub2/grub.cfg` config directly. I'll go with the latter. You can see the boot entry for Windows 10 here.  

![](/img/chrome_2020-03-30_00-23-08.png)  


But what if I have more than one entry? Its simple. They go in a chronological order of how they were entered. For example, here we have 3 entries and when we reboot, we see them in the same order as they were entered in `40_custom`. Keep in mind that the menu entries are not configured correctly and all 3 of them will boot Windows 10, as it is only for presentational purposes.

![](/img/chrome_2020-03-30_00-25-26.png)  

![](/img/chrome_2020-03-30_00-28-29.png)

### Selecting OS for booting remotely

If you didn't skip straight to this section, you might be asking "How the hell do I actually boot with the information I got?" Its very simple!  

```
grub2-reboot X
```

Replace the `X` with your prefered menu entry. It starts with 0, so keep that in mind.  

For example, if he have this menu and want to boot into Windows 10, we'd execute `grub2-reboot 2` to select Windows on next boot.  

![](/img/vmplayer_2020-03-29_22-51-41.png)

### Working example

<iframe width="760" height="515" src="https://www.youtube-nocookie.com/embed/arRHLUznx6s" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>