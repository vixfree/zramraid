# PROJECT ZRAM-RAID

Description<br>
This project is a derivative of all the experiments using virtualization XEN \ KVM, which we started in 2012. Then we did not like the speed of the disk subsystem in HVM mode for WINDOWS 2003-2012 servers.<br>
In the search for solutions remembered zram module and the first thing to make the virtual partition to swop. Of course definitely work in OS WINDOWS, "this" environment has become faster, more accurate became more "responsive", but not for server tasks. That's when we got the idea to make a so-called virtual RAID, which we call the derivative zram -> ZRAID. The idea itself is simple, create a block of the memory device using the module zram core size equal to the image of the disk for the virtual machine, and then combine the two media in a RAID array, and a ready-RAID (MD) give virtual machine XEN or KVM as a carrier for data.<br>
After that, in a virtual machine format, and use for other purposes.<br>
What result? First - the speed of communication with the carrier is almost equal to the speed of memory, and I'll tell you on the SSD will be steeper! Secondly, with regard to feelings of failure, especially in the virtual machine performed emergency shutdown of the system unit and the machine is adequately withstand such "failures". All the same software RAID in LINUX system worthy of respect! In all our experiments on extreme trips as part ZRAID, and emergency stops, the data remained intact, which helped us to solve many production problems in the operation of virtual machines. At the moment, we have a server who worked for 3years and despite occasional failures on nutrition but survived the test for reliability. Certainly our modest project can not qualify for the commercial operation and is rather unusual solution, but I think will help to solve many problems on the servers. In particular I have in this example «ZRAID» excellent health "feels" the mail database. In addition to the description and functional diagram «ZRAID», working code in BASH which is the system on OS LINUX for easier use of the solution.<br>
The code is distributed under the GNU Public License.<br>
<br>
BASH language code consists of 3 scripts:<br>
1. install - installer script installer supports OS Debian<br>
2. zraid-config - script for configuration zraid media management<br>
3. zraid-maker - starting and stopping control script zraid media<br>

# How to use:
1. Create an image of the required size, consider the size of allocated RAM to mirror your image!
examle:
 #cat /proc/meminfo |grep MemAvailable
 MemAvailable:    6638832 kB
or:
 #zraid-config --list
2. Create image:
 #fallocate -l 3G /home/kvm/disk0.img
3. Create zram-raid:
 #zraid-config --add md0:/home/kvm/disk0.img
4. Check the configuration:
 #zraid-config --list
5. Start or restart zram-raid:
 #/etc/init.d/zraid-manager restart
or
 #systemctl restart zraid-manager
6. Result:
 #cat /proc/mdstat
 Personalities : [raid1] 
 md0 : active raid1 zram0[2] loop0[1]
       3143680 blocks super 1.2 [2/2] [UU]
       
 unused devices: <none>
To automatically start the system, you must correct the configuration file:
 editor /etc/defaults/zraid
 ...
 ## service start
 # mode - startup parameter zraid system in manual or automatic mode, examples: mode="auto" or mode="manual"
 # if "manual" zraid does not start automatically at startup, dafult - "manual"
 #
 mode="auto";
 
 Now, when you start, ZRAM-RAID will start automatically.
