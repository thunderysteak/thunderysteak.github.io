# Restrict SFTP user to a specific folder

If you need to limit a SFTP only user or users to a single folder for security, you can use OpenSSH's Chroot Jail mechanism to do it.  

#### By design, SFTP users can't write to `/` inside of a chroot, so you either have to force users into a writable subdirectory or create appropriate folders first

There are two ways to do it:  
* For multiple users, you can limit them to their home directory, then set the user's home directory to a custom folder
* For a single user, you can specify the folder in sshd_config

This guide will go over primarily how to set it up for multiple users.

### Create a new user and SFTP group only

First, create the SFTP group

```
sudo addgroup sftprestricted
```

Create a new user using adduser. In this example, we will be setting the home directory to a custom one later, so we will not be creating the user's home directory. 

```
sudo adduser --no-create-home USERNAME
```

If you created a user without a home directory, set the user’s home directory:

```
sudo usermod -d /location/to/your/folder USERNAME
```

#### Set the owner of the user's $HOME and the parent directory of $HOME as root or chroot will not work and permissions along the lines of 755 or 750.

```
sudo chown root. /location/to/your/folder
sudo chown root. /location/to/your
sudo chmod 750 /location/to/your/folder
sudo chmod 750 /location/to/your
```

Disable the user shell to disallow SSH shell access. For key authentication, do not use `ssh-copy-id` to copy your public key in this step before disabling the shell, as the chroot permissions will not permit the use of the `authorized_keys` file.

```
sudo usermod -s /sbin/nologin USERNAME
```

Then add the user to to the SFTP restricted group

```
sudo usermod -g sftprestricted USERNAME
```

### Setting up key based SFTP authentication

It is a best practice to use key based authentication for SSH and SFTP connections. 

With the usage of using home directories as chroot jails, the required permissions for a jail to work will not permit you to use the keys in the home directory. You have to place the user key somewhere safe and modify `/etc/ssh/sshd_config`, as by default, OpenSSH will check `/home/%u/.ssh/authorized_keys` for public keys.  

First, generate a new keypair. For Windows, `ssh-keygen` is available via command prompt if you have the OpenSSH Client optional feature enabled (Windows 10 settings -> Apps & Features -> Optional Features). Passphrase is optional, but recommended for security.

```
ssh-keygen -t rsa
```

Create the authorized_keys file with the username appended to it in a directory of your choosing. In this example I will use `/etc/ssh/user_keys/`. Please take into consideration security of the system and possible attack vectors when choosing the directory.

```
sudo nano /etc/ssh/user_keys/authorized_keys_USERNAME
```
On MacOS/Linux/WSL, you can use this trick as an alternative to `ssh-copy-id`
```
cat .ssh/id_rsa.pub | ssh otheruser@host.local 'cat >> /your/selected/location/authorized_keys_USERNAME'
```

Then give it appropriate permissions so that only the appropriate user can access the keys

```
sudo chown USERNAME:USERNAME authorized_keys_USERNAME
sudo chmod 640 authorized_keys_USERNAME
```

Then edit `/etc/ssh/sshd_config` and add this line at the end to specify custom location for the key

```
#SSH Key authentication for SFTP users using chroot
Match Group sftprestricted
    AuthorizedKeysFile  /etc/ssh/user_keys/authorized_keys_%u
```

For user specific keyfile location, replace `Match Group GROUPNAME` with `Match User USERNAME`

### (Optional) Change the sftp subsystem to internal 

Open `/etc/ssh/sshd_config` and locate this line. CTRL+W in nano to quickly find it.

```
# override default of no subsystems
Subsystem       sftp    /usr/libexec/openssh/sftp-server
```

Replace `/usr/libexec/openssh/sftp-server` with `internal-sftp`

```
# override default of no subsystems
#Subsystem       sftp    /usr/libexec/openssh/sftp-server
Subsystem       sftp    internal-sftp
```

This will make the sftp server run under SSH, instead of spawning a new sftp server process.

From a functional point of view, the sftp-server and internal-sftp are almost identical. They are built from the same source code.

### Setting up Chroot Jail

Open `/etc/ssh/sshd_config` and paste this at the end of the file.

```
#SFTP Chroot Jail
Match Group sftprestricted
    ChrootDirectory %h
    ForceCommand internal-sftp
    PasswordAuthentication no
    PubkeyAuthentication yes
    PermitTunnel no
    AllowAgentForwarding no
    AllowTcpForwarding no
    X11Forwarding no
```

`ChrootDirectory` will force the user into a chroot jail that is their home directory and `ForceCommand internal-sftp` will make sure that anything located in `.bashrc` will not execute. This will also prevent any kind of tunelling or forwarding, password based authentication and will make you use public keys. 

Once done, restart the sshd service, verify that its running, keep the terminal open and attempt to login in a second terminal session to verify you can still SSH in normally.

```
sudo systemctl restart sshd
systemctl status sshd
```

### Permit users to write to chroot's `/`

If you want to permit users to write into their root folder, you have to use a workaround of forcing them into a subdirectory once they login via sftp due of the design of chroot jails. Your `/etc/ssh/sshd_config` should look something like this.

```
#SFTP Chroot Jail
Match Group sftprestricted
    ChrootDirectory %h
    ForceCommand internal-sftp -d /subdirectory
    PasswordAuthentication no
    PubkeyAuthentication yes
    PermitTunnel no
    AllowAgentForwarding no
    AllowTcpForwarding no
    X11Forwarding no
```

The `subdirectory` should be owned by the user with correct permissions.

### Diagnosing any issues

First, doublecheck if everything has correct permissions, as permission issue is usually what causes issues.

You can verify from local any sftp issues using verbose flags
```
sftp -vvv username@host.local
```

You can also enable debug logging in `/etc/ssh/sshd_config`, but remember to comment it out or change it to default value once you're done debugging the issue. This will display even if you encounter permission issues.
```
# Logging
#SyslogFacility AUTH
SyslogFacility AUTHPRIV
LogLevel DEBUG
```

Then restart sshd and verify its running

```
sudo systemctl restart sshd
systemctl status sshd
```

You can then view the sshd service logs using journalctl (or in /var/log depending on your system)

```
journalctl -u sshd
```







