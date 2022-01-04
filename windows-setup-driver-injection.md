# Injecting drivers into Windows 10/2016/2019/2022 Installer

If you have an edge-case where the Windows Setup installer is bluescreening due of incompatible or missing drivers, you might have to create a custom installer with your drivers injected into it.

The OWC Accelsior S cards with their default firmware have an issue where trying to use it with Windows 10, you get WHEA_UNCORRECTABLE_ERROR BSOD even before you manage to get to setup or boot the system fully. You can also find people complaining about the same thing under the Amazon reviews for the cards.

This guide will show you how to inject drivers for such edge case.

# Injecting drivers into Windows Setup

First, create a folder somewhere with subfolders `drivers`, `ISO` and `mount`, then obtain a Windows ISO and either flash it onto USB or extract its contents into the ISO folder. This tutorial uses `E:\OWCINJECT\` as the folder that contains those subfolders.

Now open up Powershell or Command Prompt as admin and get the index of the version of Windows you want to inject drivers into.

```
Dism /Get-WimInfo /WimFile:E:\OWCINJECT\ISO\sources\install.wim
```
With Windows Server 2016, you should get something like this

![](/img/powershell_2021-10-24_02-20-18.png)

Do the same with `boot.wim` as we will have to modify that as well:
![](/img/4b91a73e60adf562300396ef784c711a787bf8d1.png)

Now mount the edition of Windows you want. Preferably you should do it with every index/version. This command mounts the ` Windows Server 2016 Datacenter Evaluation (Desktop Experience)` image.
```
Dism /Mount-Image /ImageFile:E:\OWCINJECT\ISO\sources\install.wim /Index:4 /MountDir:E:\OWCINJECT\MOUNT
```
Then inject the drivers into the image. This command will recursively install all files in the folder
```
Dism /Image:E:\OWCINJECT\MOUNT /Add-Driver /Driver:E:\OWCINJECT\Driver_Win10 /Recurse 
```
Once the driver installation is done, unmount the image and commit changes
```
Dism /Unmount-Image /MountDir:E:\OWCINJECT\MOUNT /Commit
```

![](/img/image (8).png)


Now do the same thing for BOTH INDEXES for `boot.wim` to inject the drivers into WinPE and WinSetup images!

If you modified the WIM files on the USB stick and did not extract the ISO to a folder, you should be good to go to USB boot with the added drivers. 

If you want an ISO for flashing onto multiple USB sticks or want to mount it as virtual media, continue with the next steps.

# Modify the Windows ISO

Get and install [imgBurn](https://www.imgburn.com/) and then mount the UNMODIFIED ISO file with Windows Explorer. Start imgBurn and click on `Create image file from files/folders`. 

Once the menu pops up, select the `Advanced` and then `Bootable Disc` tabs.

* Toggle `Make Image Bootable`
* Select the `Source` the folder containing the files from the ISO
* Destination is self explanatory
* Under `Extract Boot Image`, select the mounted ISO and save the `.ima` file somewhere. Then load the `.ima` file under `Boot Image`. `Load Segment` and `Sectors To Load` will autopopulate.

![](/img/d5507b6845e7398e3d838c11a0fc64544ba75fb0.png)

Once you get the `Confirm Volume Label` just click yes as this is inrelevant. imgBurn should auto-adjust the ISO filesystem to bootable UDF ISO image. Just click Yes:  

![](/img/86cabb5f522941ddc25b0b152426b7f08f411a4f.png)

Now you should have a bootable image that you can flash onto USB/Burn to DVD/Mount as virtual media:

![](/img/chrome_2021-10-22_00-45-27.png)



