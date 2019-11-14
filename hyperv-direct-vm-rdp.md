# Connecting to Hyper-V VM console via RDP

Hyper-V uses the RDP protocol to connect to the virtual machine console. We can actually connect to a VM's console directly through RDP via a different machine.

### Get VM ID

We will require this to create our RDP file.

```
Get-VM -Name <VMName> | Select-Object id
```

![](/img/ApplicationFrameHost_2019-11-14_06-18-58.png)

### Create a new .rdp file

Paste this following into the RDP file with the correct server name or IP and the VM ID and save it.


```
full address:s:<Hyper-v server name or IP>
pcb:s:<Hyper-V VM ID>
server port:i:2179
negotiate security layer:i:0
```

Log in with the Hyper-V server credentials and you should have access to the console.

![](/img/mstsc_2019-11-14_06-22-19.png)