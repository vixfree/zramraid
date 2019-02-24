# Project ZRAMRAID

This project include set of shell scripts, which provides easy creation and manage the RAID 1 massive whith RAM and loop device on HDD.
The resulting speed of this device is equal to the speed of memory.

## History

This project is a derivative of all the experiments using virtualization XEN \ KVM, which we started in 2012. Then we did not like the speed of the disk subsystem in HVM mode for WINDOWS 2003-2012 servers.

In the search for solutions remembered zram module and the first thing to make the virtual partition to swop. Of course definitely work in OS WINDOWS, "this" environment has become faster, more accurate became more "responsive", but not for server tasks. That's when we got the idea to make a so-called virtual RAID, which we call the derivative zram -> ZRAID. The idea itself is simple, create a block of the memory device using the module zram core size equal to the image of the disk for the virtual machine, and then combine the two media in a RAID array, and a ready-RAID (MD) give virtual machine XEN or KVM as a carrier for data.

After that, in a virtual machine format, and use for other purposes.

What result? First - the speed of communication with the carrier is almost equal to the speed of memory, and I'll tell you on the SSD will be steeper! Secondly, with regard to feelings of failure, especially in the virtual machine performed emergency shutdown of the system unit and the machine is adequately withstand such "failures". All the same software RAID in LINUX system worthy of respect! In all our experiments on extreme trips as part ZRAID, and emergency stops, the data remained intact, which helped us to solve many production problems in the operation of virtual machines. At the moment, we have a server who worked for 3years and despite occasional failures on nutrition but survived the test for reliability. Certainly our modest project can not qualify for the commercial operation and is rather unusual solution, but I think will help to solve many problems on the servers. In particular I have in this example «ZRAID» excellent health "feels" the mail database. In addition to the description and functional diagram «ZRAID», working code in BASH which is the system on OS LINUX for easier use of the solution.

BASH language code consists of 3 scripts:

1. install - installer script installer supports OS Debian
2. zramraid-config - script for configuration zraid media management
3. zramraid-maker - starting and stopping control script zraid media

## How to use:

zramraid-install --install

and...


1. Create an image of the required size, consider the size of allocated RAM to mirror your image!
   
   example:
   cat /proc/meminfo |grep MemAvailable
   MemAvailable:    6638832 kB

   or:

   zramraid-config --list

2. Create image:
   
   fallocate -l 3G /home/kvm/disk0.img

3. Create zramraid:
   
   zramraid-config --add md0:/home/kvm/disk0.img

4. Check the configuration:

   zramraid-config --list

5. Start or restart zramraid:
   
   /etc/init.d/zramraid-manager restart

   or:

   systemctl restart zramraid-manager
   
6. Result:

   ```
   cat /proc/mdstat
   Personalities : [raid1]
   md0 : active raid1 zram0[2] loop0[1]
      3143680 blocks super 1.2 [2/2] [UU]
   
   unused devices: <none>
   ```

To automatically start the system, you must correct the configuration file:

```shell
editor /etc/defaults/zramraid
...
mode="auto";
```

Now, when you start, zramraid will start automatically.

Additional control parameters can be found from the parameter --help:
```
zramraid-config --help
zramraid-maker --help
zramraid-install --help
```
 
## Using with KVM

If you plan to use zramraid together with KVM then we recommend making a change for the systemd:
```
editor /lib/systemd/system/libvirt-guests.service
...
Requires=zramraid.service
...
```
 
* see example files from /lib/systemd/system


version - 27.06.18

## License

This project is licensed under the GNU Public License GPLv3