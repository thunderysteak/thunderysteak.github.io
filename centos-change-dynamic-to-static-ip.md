# Changing dynamic or static IP to dynamic or static IP on RHEL based Linux

If you need to reassign the static IP of your server, or want to set a server's dynamic IP into a static one, as you're not assigning it staticly via DHCP, you might find this useful.

### Discovering our interface

Run the `ip addr` command to see the active interfaces on the host and finding out which one we want to change.  

![](/img/mstsc_2019-11-18_11-36-48.png)

We see that `enp0s10f0` is our interface with the IP of `10.1.69.9`.

### Modifying our network interface config

We need to modify the `ifcfg-xxxxxx` file in the `/etc/sysconfig/network-scripts/` folder. In our case, since our interface is called `enp0s10f0`, we'll open `/etc/sysconfig/network-scripts/ifcfg-enp0s10f0` in our editor of choice.  

If it doesn't exist, create one and make sure [it follows the networkscripts interfaces template config](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/deployment_guide/s1-networkscripts-interfaces). Make sure when assigning static IP, to set DNS and gateway correctly. Use the `HWADDR` directive in hosts with multiple NICs. If you changed the NIC or moved the host to a different hardware, you also need to get rid of the old configuration file for it, or the default network manager would fail to start.

### Disable Network-Manager
If you're setting a static IP, it would be a good idea to stop NetworkManager.

```systemctl disable NetworkManager && systemctl stop NetworkManager && service network restart && chkconfig network on```

### Making Apache aware of the IP change

If the host is using Apache and depending on your configuration, you might notice that after changing the static IP the httpd service will either no longer start or it will fail with Service Unavailable errors when trying to serve a page.  
If we know the previous IP address the host had, use the `grep -ir 192.168.x.x /etc/httpd` command to locate where we need to make changes. After we're done, restart the service with `systemctl restart httpd.service`.
