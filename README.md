# Linux Tips

Some things I consistently have to look up how to do. This is mainly a reference for myself to remember how to do certain things in Linux.

--------------------------------------------------------------------------------------------------------

## `rsync`
### Filter Files and Directories

Here is an example command to transfer files remote host to local by only including directories in the `host`'s `/path/to/files` directory that have the `directory_prefix` (and include all their contents) and exclude everything else:
```bash
rsync -avW host:/path/to/files/ ./my/local/dir --include 'directory_prefix*/***' --exclude '*'
```
  * [This post](https://unix.stackexchange.com/a/2503/114564) gives helpful advice on how to handle `--include`/`--exclude` with `rsync`.

### Use a Different Port Number
```bash
rsync -e 'ssh -p 5522' -av user@remote:/path/to/transfer ./local/path/to/save/to
```



## Mount a Hard Drive
### Manually Mount
1. Execute `lsblk` to see what your drive partitions are. Example output:
```bash
sda           8:0    0 931.5G  0 disk 
└─sda1        8:1    0 931.5G  0 part /media/data_haro
sdb           8:16   0   3.7T  0 disk 
├─sdb1        8:17   0   200M  0 part 
└─sdb2        8:18   0   3.7T  0 part /media/adam/LL4MA-D2
nvme0n1     259:0    0 465.8G  0 disk 
└─nvme0n1p1 259:1    0 465.8G  0 part /
```
2. Create new directory to mount drive to
```bash
sudo mkdir /media/data_haro
```
3. Mount the drive
```bash
sudo mount /dev/sda1 /media/data_haro
```
### Auto-mount at Startup
1. Install GParted
```bash
sudo apt install gparted
```
2. Open GParted and select the drive you want to auto-mount. See what it's filesystem type is.
3. Edit `/etc/fstab` with root permissions
```bash
sudo emacs /etc/fstab
```
3. Add a line at the bottom of the file that looks like
```
/dev/sda1    /media/data_haro    ext4    defaults    0    2
```
  * First column is drive partition to mount.
  * Second column is mount point.
  * Third column is filesystem type (from GParted).
  * Fourth column is options, leave as `defaults`.
  * Fifth column leave as `0`.
  * Sixth column is `fsck` which determines filesystem order. Only [options](https://help.ubuntu.com/community/Fstab#Pass_.28fsck_order.29) are `0 (no check)`, `1 (check first)`, and `2 (check second)`. Only boot should be `1`, additional drives should be `2`. 

  * [Ubuntu: Mount The Drive From Command Line](https://www.cyberciti.biz/faq/mount-drive-from-command-line-ubuntu-linux/) - Simple instructions for mounting a hard drive manually and setting up `fstab` to auto-mount on startup.
  
You may need to change the permissions on the drive:
```bash
sudo chown -R username:username /media/data_haro
```
where you should set `username` to be your username.

Note if you do the auto-mounting and then decide to remove the drive, you must comment out the entry in `/etc/fstab`. Otherwise you may find yourself unable to boot the OS and enter emergency mode like described [here](https://unix.stackexchange.com/questions/347808/ubuntu-gives-message-welcome-to-emergency-mode/347840).

--------------------------------------------------------------------------------------------------------
