# perlblast
a Perl-Tk implementation of galblast/atfblast that uses the parport device (even via USB!)
This is the README file for perlblast.

'perlblast' is a Perl-Tk implementation of 'galblast' (by Manfred Winterhoff) and 
'atfblast' (http://www.bhabbott.net.nz/atfblast.html). 

The main drive for 'perlblast' is that it does run on Linux. I tried 'galblast' under Windows XP in
a virtual machine but always the parallel port access was troublesome, Some other programs to access the parallel
port from a virtual machine worked very well....

I learned and tested also the use via an usb<->parallel dongle, that turned out to function very well too :-)

As the code is now it interfaces to the simplified hardware GAL/ATF Blaster v2.1 as found on 
http://www.bhabbott.net.nz/atfblast.html

I am working on another hardware project for GAL/ATF programming that can handle various VPP and VCC settings.
In that design I took some more measures to block any dangerous voltage from the GAL back to the parport. 
If anything goes wrong here, you may fry your motherboard in an instant. (I did so by doing something VERY stupid).

When ready, the design will come available on GitHub as I did for this software.

I believe I prefer the use of an USB<->LPT dongle and that is very well possible. On Linux you need to blacklist
the modules 'usblp' and load 'uss720'. Initially the uss720 module did not recognize my usb dongle. It turned out
that the table in uss720.c did not hold the usb-ID for my dongle. 

lsusb -v gave me: Bus 001 Device 019: ID 0711:0302 Magic Control Technology Corp. Parallel Port

After editing drivers/usb/misc/uss720.c in my Linxu source tree and adding the values to the table near to the end
of the uss720.c file.

- /* table of cables that work through this driver */
- static const struct usb_device_id uss720_table[] = {
-         { USB_DEVICE(0x047e, 0x1001) },
-         { USB_DEVICE(0x0557, 0x2001) },
-         { USB_DEVICE(0x0729, 0x1284) },
-         { USB_DEVICE(0x1293, 0x0002) },
-         { USB_DEVICE(0x050d, 0x0002) },
-         { USB_DEVICE(0x0711, 0x0302) },                 /* lucent chip */   <<<  I added this one 
-        { }                                             /* Terminating entry */
- };

Now after: make modules and make modules_install, rmmod uss720 and modprobe uss720, and re-inserting
te usb<->lpt dongle, it worked. I found that after use, the reference count stays at '1', so rmmod would not remove
the uss720 module. I don't know why that is at this moment.

From dmesg:
usb 1-12.3: new full-speed USB device number 19 using xhci_hcd
usb 1-12.3: New USB device found, idVendor=0711, idProduct=0302
usb 1-12.3: New USB device strings: Mfr=0, Product=0, SerialNumber=0
parport1: fix this legacy no-device port driver!
lp1: using parport1 (polling).

ls -l /dev/parport* shows

crw-rw-r-- 1 root lp 99, 0 Oct 23 11:54 /dev/parport0                           << LPT port on motherboard >>
crw-rw-r-- 1 root lp 99, 1 Oct 23 12:02 /dev/parport1                           << LPT port via USB dongle >>

Make sure that you are in the group 'lp'. If not, add yourself to it. You need to (re)login to make it having effect.

As for Perl, I had to add some modules with 'cpan'. 

cpan[1]> install Perl::Tk
cpan[1]> install Tk::ListBox
cpan[1]> install Tk::LabEntry
cpan[1]> install Tk::Optionmenu
cpan[1]> install Device::ParallelPort
cpan[1]> install Device::ParallelPort::drv::parport
cpan[1[> install Device::ParallelPort::drv::linux

The documentation on the Internet as on https://ubuntuforums.org/showthread.php?t=1246312
show how the most essential parport modules are added. 
For me the third module (Device::ParallelPort::drv::linux), I could not have it done the way described. 
After modifying the "linux.xs" file and "install Device::ParallelPort::drv::linux" it would fail as it 
loaded a fresh copy again with a higher number. I solved my struggle by adding temporarely a symbolic link 
between /usr/include/sys/io.h and /usr/include/asm/io.h. 
After a succesful "install Device::ParallelPort::drv::linux" in cpan, I removed the link again.

While the Perl-Tk implementation tries to mimmic the galblast version as best as I could, there are some differences.
I am not a 'fluent' Perl programmer, but I can get most things done as I want in Perl. If someone more capable
in Perl has hints and/or improvements I am happy to apply these.

As I don't have all the GAL types that the program would support I could not test every corner yet. Bugs may
hide in the code. But as it is now, it does it work for me. The same for the English grammar, 
it is not my native tongue, so words may look strange sometimes, help me.

Kees Schoenmakers,  keesgpl at gmail dot com
