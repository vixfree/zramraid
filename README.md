# Project ZRAMRAID

This project include set of shell scripts, which provides easy creation and manage the RAID 1 massive whith RAM and loop device on HDD.
The resulting speed of this device is equal to the speed of memory.

## History

This project is a derivative of all the experiments using virtualization XEN \ KVM, which we started in 2012. Then we did not like the speed of the disk subsystem in HVM mode for WINDOWS 2003-2012 servers.<br>
In the search for solutions remembered zram module and the first thing to make the virtual partition to swop. Of course definitely work in OS WINDOWS, "this" environment has become faster, more accurate became more "responsive", but not for server tasks. That's when we got the idea to make a so-called virtual RAID, which we call the derivative zram -> ZRAID. The idea itself is simple, create a block of the memory device using the module zram core size equal to the image of the disk for the virtual machine, and then combine the two media in a RAID array, and a ready-RAID (MD) give virtual machine XEN or KVM as a carrier for data.<br>
After that, in a virtual machine format, and use for other purposes.<br>
What result? First - the speed of communication with the carrier is almost equal to the speed of memory, and I'll tell you on the SSD will be steeper! Secondly, with regard to feelings of failure, especially in the virtual machine performed emergency shutdown of the system unit and the machine is adequately withstand such "failures". All the same software RAID in LINUX system worthy of respect! In all our experiments on extreme trips as part ZRAID, and emergency stops, the data remained intact, which helped us to solve many production problems in the operation of virtual machines. At the moment, we have a server who worked for 3years and despite occasional failures on nutrition but survived the test for reliability. Certainly our modest project can not qualify for the commercial operation and is rather unusual solution, but I think will help to solve many problems on the servers. In particular I have in this example «ZRAID» excellent health "feels" the mail database. In addition to the description and functional diagram «ZRAID», working code in BASH which is the system on OS LINUX for easier use of the solution.<br>

<br>
BASH language code consists of 3 scripts:<br>
1. install - installer script installer supports OS Debian<br>
2. zramraid-config - script for configuration zraid media management<br>
3. zramraid-maker - starting and stopping control script zraid media<br>

## How to use:

zramraid-install --install<br>
and..<br>
<hr>
1. Create an image of the required size, consider the size of allocated RAM to mirror your image!<br>
example:<br>
 cat /proc/meminfo |grep MemAvailable<br>
 MemAvailable:    6638832 kB<br>
or:<br>
 zramraid-config --list<br>
2. Create image:<br>
  fallocate -l 3G /home/kvm/disk0.img<br>
3. Create zramraid:<br>
  zramraid-config --add md0:/home/kvm/disk0.img<br>
4. Check the configuration:<br>
  zramraid-config --list<br>
5. Start or restart zramraid:<br>
 /etc/init.d/zramraid-manager restart<br>
or:<br>
  systemctl restart zramraid-manager<br>
6. Result:<br>
  cat /proc/mdstat<br>
  Personalities : [raid1]<br>
  md0 : active raid1 zram0[2] loop0[1]<br>
      3143680 blocks super 1.2 [2/2] [UU]<br>
 <br>     
  unused devices: <none><br>
To automatically start the system, you must correct the configuration file:<br>

 editor /etc/defaults/zramraid<br>
  ...<br>
  mode="auto";<br>
 <br>
 Now, when you start, zramraid will start automatically.<br>
 <hr>
Additional control parameters can be found from the parameter --help<br>
example:<br>
 zramraid-config --help<br>
 zramraid-maker --help<br>
 zramraid-install --help<br>
 
 ## Using with KVM

 If you plan to use zramraid together with KVM then we recommend making a change for the systemd:<br>
 editor /lib/systemd/system/libvirt-guests.service<br>
 ...<br>
 Requires=zramraid.service<br>
 ...<br>
 
* see example files from /lib/systemd/system
<hr>
version - 27.06.18

## License

This project is licensed under the GNU Public License GPLv3