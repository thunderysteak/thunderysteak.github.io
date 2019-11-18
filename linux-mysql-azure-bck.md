# Backing up MySQL database on Linux with rclone and Azure Blob Storage

MySQL and MariaDB contain an utility called `mysqldump` which can be used for creating backup of a database or systems of databases. This utility creates a logical backup, which allows for granual recovery scenarios. Full backups are recommended for full disaster recovery scenarios.

This guide uses [Azure storage emulator](https://docs.microsoft.com/en-us/azure/storage/common/storage-use-emulator#start-and-initialize-the-storage-emulator) for testing purposes and CentOS 7.

### How the database backup functions

As an example, here's a command for manually backing up all of the databases in the system.

```mysqldump --all-databases --single-transaction --lock-tables=false > full-backup-$(date +%F).sql -u root -p```

Breakdown of the options:
* `--all-databases` dumps all databases.
* `--single-transaction` option provides a way of making an online backup if InnoDB storage engine is used.
* `--lock-tables=false` by default, mysqldump will lock the table of your database during the dump process to make sure there will not have new data added during this time-frame. This may impact your apps during the db dump. For InnoDB, use `--single-transaction` instead.
* `$(date +%F)` specifies the timestamp formatting. `%F` displays the full date.

### Setting up config file to not expose MySQL database password

As stated in the [MySQL End-User Guidelines for Password Security manual](https://dev.mysql.com/doc/refman/5.6/en/password-security-user.html), our best option is to store the password in an option file. Putting the password in `crontab` in convenient but insecure, as every time the command runs, the password can be visible in `ps`.  

Create a new user where you'll be running the crontab and rclone. In the user's home directory, create a new `.your_filename_here.cnf` and restrict it by running the command `chmod 600 /home/user/.your_filename_here.cnf`.  

Example .cnf file:
```
[client]
user = root
password = mysql_root_pwd
```

We can then add `--defaults-extra-file=/home/bckusr/.my.cnf` when using the mysqldump command for logging in

### (Optional) Setting up the default editor for crontab
A bit controvensional, but if you don't like `vi` and prefer `nano` instead, you can change the default editor for a specific user by editing `.bash_profile`. To change the editor, paste this into your bash profile and relog (exit from the SSH session as well):
```
export VISUAL="nano"
export EDITOR="nano"
```

### Setting up RClone

Follow the [RClone Microsoft Azure Blob Storage](https://rclone.org/azureblob/) configuration documentation for setting up RClone. Due of me using the storage emulator, the configuration will greatly vary.  

### Setting up crontab
Execute `crontab -e` as the user you want to run backups from. You can use [Crontab Guru](https://crontab.guru/) for quickly setting up the cron schedule execution.  

Here's how the crontab for daily backup that runs at 1 AM looks like.

```
0 1 * * * /usr/bin/mysqldump --defaults-extra-file=/home/bckusr/.my.cnf -u root --single-transaction --lock-tables=false --all-databases > full-backup-$(date +\%F).sql && rclone copy /home/bckusr/full-backup-*.sql azure:backup && rm -rf /home/bckusr/full-backup-*.sql
```

This creates a new database backup, when it runs correctly, it copies the backup to blob storage and after successful copy, it cleans up after itself.  

I would heavily suggest using [Azure Blob Storage Lifecycle Management](https://docs.microsoft.com/en-us/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal).

### GZip compression

Since Azure Blob storage is priced per GB, if you have large databases, the price can quickly creep up. You can utilize `gzip` compression to save up some space.

```
0 1 * * * /usr/bin/mysqldump --defaults-extra-file=/home/bckusr/.my.cnf -u root --single-transaction --lock-tables=false --all-databases | gzip > full-backup-$(date +\%F).sql.gz && rclone copy /home/bckusr/full-backup-*.sql.gz azure:backup && rm -rf /home/bckusr/full-backup-*.sql.gz
```
This creates a new database backup and compresses it into a gunzip archive, when it runs correctly, it copies the backup to blob storage and after successful copy, it cleans up after itself.


### Setting up Azure storage emulator

If you want to test out things yourself without having to spend money on Azure blob storage, follow the official [Use the Azure storage emulator for development and testing](https://docs.microsoft.com/en-us/azure/storage/common/storage-use-emulator) guide on how to get the storage emulator running.  
You will require Microsoft SQL Server 2012 Express installed at the current date this document was written. Because RClone is assuming that the emulator runs locally, you'll have to fiddle with rclone or use [Azurite](https://github.com/azure/azurite) Azure server emulator for Linux instead.  

Depending on which version you installed, you might get away with supplying a SAS link with `127.0.0.1` changed to the address of the server.

![](/img/ApplicationFrameHost_2019-11-18_15-57-32.png)
