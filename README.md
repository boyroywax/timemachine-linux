# Serve TimeMachine Drives from linux
You get a really cool Xserve Drive Logo if you do this correctly.

![Really Cool Xserve Icon](./xserve.png)

## Run Down:
a. could not get ext4 format to work

b. could not get samba to work

c. easiest to format drive with hfs+ on mac osx 

d. began with missing partitions on hfs+ hdd (data still intact)

## Mount your hfs+ hdd
```shell
sudo apt-get install hfsplus hfsutils hfsprogs
sudo mount -t hfsplus -o force,rw /dev/sdXY /media/mntpoint
```

## Add the hfs+ hdd to '/etc/fstab'
* https://raspberrypi.stackexchange.com/questions/29087/adding-hfsplus-file-system-to-fstab
Enable mount persistent across reboots. 

Example:
```text
# /etc/fstab
UUID=fefc150b-2d57-3a85-9a41-d6b5d97de670 /media/hfs/2tb hfsplus force,rw 0 0
```

* Ubuntu AFP setup
https://fixpress.org/afp-ubuntu/
* AFP TimeMachine install
https://gregology.net/2018/09/raspberry-pi-time-machine/

## Install  netatalk  and  avahi
```shell
sudo apt-get install netatalk
sudo apt-get install avahi-daemon
sudo apt-get install libnss-mdns
```

## Ownership for success
```shell
sudo chmod 1777 -r /media/hdd/timemachine
```

## Configure netatalk -  WARNING NOT TESTED
Name your netatalk server
```shell
# /etc/default/netatalk
ATALK_NAME=vault
CNID_METAD_RUN=yes
AFPD_RUN=yes
```

## Edit the main AFP/netatalk config
```shell
# /etc/netatalk/afp.conf
[Global]
  mimic model = TimeCapsule6,106

[Time Machine]
  path = /media/tm
  time machine = yes
```

## Append the 'AppleVolumes.default' file
Example:
```text
# /etc/netatalk/AppleVolumes.default
# The line below sets some DEFAULT, starting with Netatalk 2.1.
:DEFAULT: options:upriv,usedots

# By default all users have access to their home directories.
#~/                     "Home Directory"
/media/hfs/2tb "TimeMachine" allow:j,odroid,root cnidscheme:dbd options:tm
# End of File
```

##  edit the /etc/nsswitch.conf
```shell
hosts:          files mdns4_minimal [NOTFOUND=return] dns mdns4 mdns
```

## Restart avahi-daemon and netatalk
```shell
sudo systemctl restart avahi-daemon
sudo systemctl restart netatalk
```

## Connect with MACOSX
Use the servers login credentials
```text
afp://<server-url>
```


