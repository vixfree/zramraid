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
zraid-install --install<br>
and..<br>
<hr>
1. Create an image of the required size, consider the size of allocated RAM to mirror your image!<br>
example:<br>
 cat /proc/meminfo |grep MemAvailable<br>
 MemAvailable:    6638832 kB<br>
or:<br>
 zraid-config --list<br>
2. Create image:<br>
  fallocate -l 3G /home/kvm/disk0.img<br>
3. Create zram-raid:<br>
  zraid-config --add md0:/home/kvm/disk0.img<br>
4. Check the configuration:<br>
  zraid-config --list<br>
5. Start or restart zram-raid:<br>
 /etc/init.d/zraid-manager restart<br>
or:<br>
  systemctl restart zraid-manager<br>
6. Result:<br>
  cat /proc/mdstat<br>
  Personalities : [raid1]<br>
  md0 : active raid1 zram0[2] loop0[1]<br>
      3143680 blocks super 1.2 [2/2] [UU]<br>
 <br>     
  unused devices: <none><br>
To automatically start the system, you must correct the configuration file:<br>

 editor /etc/defaults/zraid<br>
  ...<br>
  mode="auto";<br>
 <br>
 Now, when you start, ZRAM-RAID will start automatically.<br>
 <hr>
Additional control parameters can be found from the parameter --help<br>
examle:<br>
 zraid-config --help<br>
 zraid-maker --help<br>
 zraid-install --help<br>
