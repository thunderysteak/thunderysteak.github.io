#Routing all traffic through OpenVPN client on Windows

By default, OpenVPN doesn't route all traffic through the VPN connection. Under normal circumstances, this is not a problem, but if you are trying to protect yourself from an unsecured network or a resource behind the VPN uses a TLD, it can cause issues.  

To fix this, enable OpenVPN to be the default gateway in OVPN config file. Your OpenVPN config files are usually located at `C:\Users\<User>\OpenVPN\config\`.  If you are connected to the VPN, I'd suggest disconnecting. Open the config with a text editor and add this line:

```
redirect-gateway def1
```

No need to close and open the OVPN client. All traffic should be routed through the VPN after you reconnect.
