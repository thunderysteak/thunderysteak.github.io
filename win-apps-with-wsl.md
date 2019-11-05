# Using Windows applications with the Windows Subsystem for Linux (WSL) Linux filesystem

With the Windows 10 1903 update, Microsoft [integrated a P9 file server into WSL](https://devblogs.microsoft.com/commandline/whats-new-for-wsl-in-windows-10-version-1903/) which will allow you to access and modify your Linux files without [the risk of breaking it and having to reinstall your whole WSL distro](https://blogs.msdn.microsoft.com/commandline/2016/11/17/do-not-change-linux-files-using-windows-apps-and-tools/).


Previously you had to dig into your AppData folder to find the Linux filesystem of your installed WSL distro, and usually modifying any file in it lead to the distro getting destroyed. If you don't have Windows 10 version 1903, stop here and update to a newer version.


With the P9 server integration, its fairly easy to access the Linux files like it was a network share.  
The distro files are exposed through the `\\wsl$\` network path.

![](/img/explorer_2019-11-05_18-30-17.png)


You can also type `explorer.exe .` in the bash command line to open an explorer window directly in the folder you're in.

![](/img/de8981.gif)

Since it is a network share, you can mount it as a network drive in explorer for ease of access.  
I've been using it with VS Code and Gitkraken and it works with little to no problems. 