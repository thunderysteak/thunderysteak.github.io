# Using Windows Deployment Services (WDS) with iPXE

This guide is using Windows Server 2016 with WDS set up as standalone server and Mikrotik RB751U-2HnD serving as the DHCP and TFTP server.


# Prerequisites
* A deployed and functional WDS server, preferably in standalone configuration
* A TFTP server that's not hosted on the WDS server itself
* A compiled iPXE binary with or without menu and a server serving the menu
* DHCP server


If you need to know how to compile your iPXE binary, create the menu and set up DHCP, [refer to the previous iPXE guide](/win-iis-ipxe-linux).  


# Setting up a TFTP server on Mikrotik RouterOS

Upload your `undionly.kpxe` file via FTP to your Mikrotik. To keep things clean, I will upload it into the `tftp/` directory. In Winbox, under the IP rollout, open TFTP and add new item. 

![](/img/winbox_2019-11-10_13-30-55.png)

For `IP Addresses`, select the network or a specific IP from which you want to allow TFTP to be accessed. Don't forget the netmask, as it is important.  

`Req. Filename` is requested filename in which return gives back `Real Filename`. This field supports regex. `Real Filename` is what the TFTP server will return to the client that requested it. Next, enable `Allow` and `Read Only` for security purposes, as someone could override our iPXE binary.  

In DHCP Network, set `Next Server` to the IP of the router and the `Boot File Name` to `undionly.kpxe`.

![](/img/winbox_2019-11-10_10-20-16.png)

# Configuring WDS

Since in this configuration our WDS server doesn't serve the DHCP server, we need to disable it listening it to DHCP ports.  
Go to the properties of Windows Deployment Services server, go to the DHCP tab and enable `Do not listen on DHCP ports`. If we don't enable this, the WDS server could load first instead of iPXE as it hijacks the DHCP request sent to the router.

![](/img/ApplicationFrameHost_2019-11-10_13-43-49.png)


# Configuring our menu.ipxe entry

Because WDS relies on the DHCP option 66 and 67 when PXE booting, we need to set them in iPXE's networking before chainloading the PXE file. If you use multiple NICs, make sure you set your `net0` interface to the interface you are going to use for booting.  

Due of the nature of Microsoft's TFTP implementation, TFTP booting will fail if we use forward slash. Another problem is that if we use backslash in iPXE, it will fail as there's no way how to escape characters. We can get around this problem by using the hexadecimal value of backslash, which is `%5C`

The iPXE code for menu containing just the WDS entry will look like this:

```
#!ipxe

menu iPXE menu

item win-wds Windows Deployment Services

:win-wds
set net0/next-server 10.1.69.5
set net0/filename boot\x86\wdsnbp.com
chain tftp://10.1.69.5/boot%5Cx86%5Cwdsnbp.com
```


If everything was set up correctly, WDS will successfully boot from iPXE.

![](/img/3b35dc.gif)