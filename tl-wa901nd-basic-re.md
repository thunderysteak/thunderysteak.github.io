# Reverse Engineering TP-Link TL-WA901ND firmware and obtaining the filesystem

My friend gave me a TP-Link TL-WA901ND v2 AP to play around with, as I needed an alternative for my wifi while I played around with WPA2 EAP. Since the first thing I did when I got my hands on it was to flash OpenWRT, I got kinda curious what the firmware contains and how old the Linux kernel is if it runs Linux. I'll be using Kali Linux for this. This is pretty much a beginners guide to FW RE.


### Obtaining the firmware

This one is pretty easy. [We can download the firmware directly from TP-Link](https://www.tp-link.com/eg/support/download/tl-wa901nd/v2/#Firmware). After extracting the archive, we will end up with a bin file that we will work with.

### Orienting ourselves for correct approach

A good place to start would be by trying to run the `file` command against the file, as sometimes manufacturers hide simple archives behind the .bin extension. If you get something like `file wa901nv2_en_3_12_16_up(130131).bin: data`, proceed to finding strings in the bin file.

```strings -5 wa901nv2_en_3_12_16_up\(130131\).bin|head```

![](/img/vmplayer_2019-11-11_18-59-15.png)

The `strings` command will look for printable characters in the file. The `-5` flag makes strings search for 5 or more printable characters in a row. we use `|head` at the end to limit the output of the command to first 10 lines found. We already see that the kernel is packed in a vmlinux.bin image.

### Binwalk

Binwalk is a tool for searching a given binary image for embedded files and executable code. Its pretty much a firmware analysis and reverse engineering tool. Run the `binwalk wa901nv2_en_3_12_16_up\(130131\).bin` command to check what the firmware contains.

![](/img/vmplayer_2019-11-11_19-27-45.png)

We see that we have the kernel in a gzip format that starts at the decimal offset of 512 and squashfs filesystem at decimal offset 1048576. 

### Extracting the filesystem

We will be using `dd` to extract the squashfs filesystem.
```dd if=wa901nv2_en_3_12_16_up\(130131\).bin skip=1048576 bs=1 of=filesystem.squashfs
unsquashfs filesystem.squashfs```

![](/img/vmplayer_2019-11-11_19-36-42.png)

We should end up with a new folder called `squashfs-root`. This contains the filesystem of the AP.

![](/img/vmplayer_2019-11-11_19-38-32.png)

But wait! There's more!

### Getting the contents of vmlinux.bin

Extract `vmlinux.bin` from the binary. In this case, its not really needed as we already have the filesystem, but its good to extract the contents of it for practice. We already know from binwalk that its a gzip archive. Extract it and run binwalk against it once again.
```dd if=wa901nv2_en_3_12_16_up\(130131\).bin skip=512 bs=1 of=vmlinux.bin.gz
gunzip vmlinux.bin.gz
binwalk vmlinux.bin```

![](/img/vmplayer_2019-11-11_19-50-15.png)

From here we can see that the AP uses the 2.6.15 Linux kernel. If we wanted to exploit the device externally, we could just look up to what attacks the kernel is vulnerable to. We also notice another gzip archive. Time to crack it open with dd, extract it and find out what kind of file it is.

```dd if=vmlinux.bin skip=1855488 bs=1 of=secret.gz
gunzip secret.gz
file secret```

![](/img/vmplayer_2019-11-11_19-53-37.png)

We finally stumble upon a diffent archive that isn't gzip. To keep things clean, create a new directory, enter it and execute this command.

```cpio -idm --no-absolute-filenames < ../secret```

`-i` is for extract, `-d` is to create leading directories where needed, `-m` is to retain previous file modification times during extraction and `--no-absolute-filenames` creates all files relative to the current directory.

![](/img/vmplayer_2019-11-11_20-04-51.png)

Sadly we will just find only /dev/console here.

From [Kernel.org](https://www.kernel.org/doc/html/latest/admin-guide/devices.html):  
"The console device, /dev/console, is the device to which system messages should be sent, and on which logins should be permitted in single-user mode. Starting with Linux 2.1.71, /dev/console is managed by the kernel; for previous versions it should be a symbolic link to either /dev/tty0, a specific virtual console such as /dev/tty1, or to a serial port primary (tty*, not cu*) device, depending on the configuration of the system."