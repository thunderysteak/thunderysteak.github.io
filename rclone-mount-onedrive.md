# Mounting Onedrive with Rclone mount on startup with Systemd

If you want to use OneDrive storage on your Linux system similar to using it as [WebDAV share on Windows](https://blogs.msdn.microsoft.com/robert_mcmurray/2014/09/30/using-the-webdav-redirector-with-onedrive-part-1-standard-security/), you'll quickly find out that trying to mount OneDrive as a WebDAV share will fail, since Onedrive will function this way only with Windows. Another option is to use the [rclone mount](https://rclone.org/commands/rclone_mount/) command to mount it as a regular drive.

### Limitations

If you want to utilize Onedrive as a backup storage because for example you pay for Office 365 and don't have any other use for it because you already use Google Drive or Dropbox, [please reconsider](https://www.red-gate.com/simple-talk/cloud/cloud-data/stop-relying-on-cloud-file-stores-as-a-backup-strategy/) and use something like [Azure Blob Storage](http://localhost:4000/linux-mysql-azure-bck) or AWS S3 for backup.  

Main reasons are the upload limitations. Microsoft will throttle you if you upload too much stuff in one go, and it will not let you upload anything above 15GB. Other cloud storage providers have similar upload limitations.


### Setting up Rclone

If you're doing this on a headless machine, personally I'd suggest to have another (preferably Windows) system handy to obtain the token. No, you won't be able to access the site on a headless machine with either opening up the required port or SSH tunneling.  

[Follow the OneDrive documentation on Rclone's site](https://rclone.org/onedrive/) and stop once you get to the "Use auto config?". Answer "no" and run `rclone authorize "onedrive"` on another machine. This will open up a new browser window where you have to authorize Rclone to access your account. After authorizing it, the Rclone window will print out the authorization token that you just paste into the Linux SSH session. You should end up with a working rclone target.

![](/img/WindowsTerminal_2020-01-05_12-38-44.png)


You can verify that your remote is working by using `rclone ls remote:`
![](/img/putty_2020-01-05_12-46-15.png)

### Testing out rclone mount

Create a new directory where you want to mount the storage. In this example I used `/mnt/onedrive`.  
Following the [rclone mount](https://rclone.org/commands/rclone_mount/) documentation, we know that the syntax for mounting is `rclone mount OneDrive:TestFolder /mnt/onedrive`, but we'll later find out that things like VEEAM Backup and Replication won't really like it due of it not being real local filesystem.  

![](/img/Screenshot_20200104-201601_Termux.jpg)  

We need to add [file caching](https://rclone.org/commands/rclone_mount/#file-caching) as well or things will fail when working directly with the mount point. In a nutshell, this will make it act more like local storage with a ton of latency. If you're going to be only writing into it, use `--vfs-cache-mode writes`, otherwise use `--vfs-cache-mode full`. Because we'll have to define the config location when executing it as systemd service, we also need to define the `---config`. Using `rclone config file` will display the config location.  

In the end, we'll end up with this command:
```
rclone mount --vfs-cache-mode writes OneDrive:VEEAM /mnt/onedrive --config /root/.config/rclone/rclone.conf
```

When you execute it, open a new session and do some file operations in the mount point to see if it works correctly, and when done, close it with CTRL+C.

### Fixing things
After terminating rclone, FUSE usually doesn't dismount correctly. We'll have to run `fusermount -u <mountpoint>` to unmount it correctly. If it says its busy, force it by `umount -l <mountpoint>` and then using the fusermount command again.


### Creating a systemd service
Go to `/etc/systemd/system/` and create a new file called `rclonemount.service` and paste this in:

```
[Unit]
Description=rclonemount
AssertPathIsDirectory=/mnt/onedrive
After=network-online.target

[Service]
Type=simple
ExecStart=/usr/bin/rclone mount \
        --config=/root/.config/rclone/rclone.conf \
        --vfs-cache-mode writes \
        OneDrive:TestMount /mnt/onedrive
ExecStop=/bin/fusermount -u /mnt/onedrive
Restart=always
RestartSec=10

[Install]
WantedBy=default.target
```

Save it and execute it by running `systemctl start rclonemount`. You can view if it failed or not by running `systemctl status rclonemount`. To make it execute on next boot, execute `systemctl enable rclonemount` and it should run on the next reboot. 

If you do any changes to `rclonemount.service`, make sure to execute `systemctl daemon-reload` to reload any changes you're done.



Also yes, VEEAM does run with that config, but does fail due of Onedrive's 15GB file limit:
![](/img/putty_2020-01-04_22-32-25.png)