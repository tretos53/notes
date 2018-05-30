## Linux

#### Run a Linux command at a certain time

```
at 1:00 PM Mon
at> sudo shutdown -r now
at> CTRL+D
```

#### Automatically remove old images from /boot partition
```sudo apt-get -y autoremove --purge```

#### Disk Operations

##### Add new disk to Linux. As root.
```grep mpt /sys/class/scsi_host/host?/proc_name```
###### Which will return something like 
```/sys/class/scsi_host/host0/proc_name:mptspi```
###### Then you follow it up with 
```echo "- - -" > /sys/class/scsi_host/host0/scan```

##### List disks
```
administrator@localhost:~$ ls /dev/sd*
/dev/sda /dev/sda1 /dev/sda2 /dev/sda5 /dev/sdb
```
```
sda = physical disk 1
sda1 = physical disk 1 partition 1
sdb = physical disk 2
```

##### Configure partition on disk sdb
```
fdisk /dev/sdb
p
n
w
q
```

####### Check new configured disk
```ls /dev/sd*```

####### Mount new partition as /appdata for example
```
/sbin/mkfs.ext3 -L /appdata /dev/sdb1
cd mnt/
mkdir /appdata
mount /dev/sdb1 /appdata
mount
```

###### check if worked
```df -h```

###### Add new mount to mount during boot
```
vi /etc/fstab
/dev/sdb1 /appdata ext3 defaults 0 0
```

###### Test by creating a new file
```
cd /appdata/
vi test
ls
```

###### DNS server on Solaris
###### Check the DNS service status
```svcs -a | grep dns```

###### Set the DNS server
```
svccfg -s network/dns/client
svc:/network/dns/client> setprop config/nameserver = net_address: (IP_OF_DNS_SERVER)
svc:/network/dns/client> quit
svcadm refresh dns/client
```

###### Find a file
###### 2>/dev/null means to send all the error messages to null so you won't see them
```find . -name "filename" 2>/dev/null```
```https://opensource.com/article/18/4/how-use-find-linux```

Locate is faster but not as powerfull and not always available.
```
locate filename
```