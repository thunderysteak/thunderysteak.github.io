# Updating firmware on PCIe SATA expansion cards using ASMedia ASM106x series SATA controller

ASM106x is the popular chipset for SATA III PCIe expansion (add-on) cards, RAID and M.2, such as: SilverStone ECS03, OWC Accelsior S, Delock, I/O Crest, Syba, StarTech, Ableconn and probably many more. There are variations of these cards where some use the slower ASMedia ASM1061 chipset and some use the faster ASMedia ASM1062 chipset. One example is that [ICY-DOCK ToughArmor MB839SP-B](https://www.icydock.com/goods.php?id=306) uses slower ASM1061, while [OWC Accelsior S](https://www.owcdigital.com/products/accelsior-s) uses the faster ASM1062.

Some of these cards come with heavily out of date firmware, mainly the OWC Accelsior S cards and will cause kernel crashes or PCIe issues:  

![](/img/xnview_2022-01-04_21-40-03.png)

The reason is that these cards come with a firmware dating back to 2014 and the firmware is incompatible with newer hardware, and most vendors do not provide a way to update the firmware:
![](/img/rpviewer (20).png)

The firmware on the OWC cards are dated September 2014 and is incompatible with new systems. There's a new [Kernel patch](https://bugzilla.kernel.org/show_bug.cgi?id=212695) that should help fix the issue regarding incorrect PCIe payload size, but it does not appear to work for these cards.

## Obtaining the firmware

# WARNING: These links point to third-party sites with firmware and driver versions that might not work for your system or can lead to software or hardware bricking. Please refer to the card manufacturer or ASMedia first for obtaining the firmware update files before using the linked firmware files. 

You can obtain the firmware files from the [TechPowerUp Forums from this specific thread](https://www.techpowerup.com/forums/threads/latest-driver-and-firmware-for-asmedia-asm106x-series.264571/), or obtaining it from an OEM like Ableconn.

I have used the ASM1062 Firmware Version: 201105_00_76_00 firmware from Ableconn to flash two OWC cards:
[http://ableconn.com/support_1.php?gid=61](http://ableconn.com/support_1.php?gid=61)


## Create a bootable DOS USB stick

Obtain [FreeDOS](https://www.freedos.org/download/) and create a bootable USB stick using [Rufus](https://rufus.ie/en/). balenaEtcher won't function and will complain about the USB thumbdrive not being bootable.

![](/img/rufus-3.5_2022-01-04_21-33-45.png)

Once you finish flashing the image, you can just drag and drop the contents of the ZIP containing the firmware onto the USB thumbdrive.

![](/img/explorer_2022-01-04_22-00-33.png)

## Flashing the new firmware

**If your OS is set to UEFI boot, go to BIOS setup and set it to use Legacy boot and disable secure boot.**

Go through the wizard to use the FreeDOS in LiveCD mode until you get to a command prompt. Once there, just type `ahci.bat` and press enter. 

![](/img/rpviewer (21).png)

If the firmware worked and it shows `PASS : 1`, you can reboot the machine and unplug the thumbdrive. Once you reboot, you should see the new firmware number. This is only visible when in Legacy/BIOS mode.

![](/img/rpviewer (22).png)

Don't forget to set your BIOS settings if you changed them. *The ASMedia cards can only boot in UEFI mode.*   

