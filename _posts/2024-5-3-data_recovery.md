---
layout: post
title: Member data recovery?
---

My brother and I recently tried to transfer some data from MacOS to Windows using an external hard drive. Easy peasy, right? Simply copy the data MacOS -> HDD -> Windows.
First step was smooth. However, after inserting HDD into Windows a popup appeared. It said more or less something like "The data on your HDD looks corrupted. Do you want Windows to fix it?".
Withouth thinking much about it, we were like yes, sure. Putting the only backup copy of your data in the hands of Windows: sure, what can go wrong? 

Of course everything went wrong. After Windows finished its recovery, we were delighted to find out that the hard drive was unusable. 
The device wouldn't even be recognized by Windows anymore. Ok, let's try MacOS. At first, it seemed to work. 
I downloaded DiskDrill and was about to run it, but it was pretty late and in the end I decided to leave it for the next day. 
Little did I know that the next day the device wouldn't be recognized on Mac either.

Last resort, Ubuntu. Fortunately, here the device was still detected. Of course it wasn't possible to mount it but something was there. 
By running `sudo dmesg | grep -i usb` I could see something like this:

```
[  119.595927] usb 2-1: New USB device found, idVendor=059f, idProduct=10c4, bcdDevice= 0.03
[  119.595940] usb 2-1: New USB device strings: Mfr=2, Product=3, SerialNumber=1
[  119.595945] usb 2-1: Product: Mobile Drive
[  119.595949] usb 2-1: Manufacturer: LaCie
[  119.595953] usb 2-1: SerialNumber: 00000000NL6EGKG6
[  119.622064] usbcore: registered new interface driver usb-storage
[  119.629278] usbcore: registered new interface driver uas
[  257.887412] usb 2-1: device not accepting address 2, error -62
[  268.895478] usb 2-1: device not accepting address 2, error -62
[  279.907305] usb 2-1: device not accepting address 2, error -62
[  290.911390] usb 2-1: device not accepting address 2, error -62
```

After waiting some minutes and compulsively using `lsblk` finally I was able to see something 

```
NAME        MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
loop0         7:0    0     4K  1 loop /snap/bare/5
loop1         7:1    0  63,4M  1 loop /snap/core20/1974
loop2         7:2    0  63,9M  1 loop /snap/core20/2182
loop3         7:3    0  73,9M  1 loop /snap/core22/858
loop4         7:4    0 237,2M  1 loop /snap/firefox/2987
loop5         7:5    0 349,7M  1 loop /snap/gnome-3-38-2004/143
loop6         7:6    0 485,5M  1 loop /snap/gnome-42-2204/120
loop7         7:7    0   497M  1 loop /snap/gnome-42-2204/141
loop8         7:8    0  91,7M  1 loop /snap/gtk-common-themes/1535
loop9         7:9    0  12,3M  1 loop /snap/snap-store/959
loop10        7:10   0  53,3M  1 loop /snap/snapd/19457
loop11        7:11   0  40,4M  1 loop /snap/snapd/20671
loop12        7:12   0   452K  1 loop /snap/snapd-desktop-integration/83
loop13        7:13   0 266,6M  1 loop /snap/firefox/3836
sda           8:0    0   1,8T  0 disk 
nvme0n1     259:0    0 953,9G  0 disk 
├─nvme0n1p1 259:1    0   200M  0 part /boot/efi
├─nvme0n1p2 259:2    0   128M  0 part 
├─nvme0n1p3 259:3    0 558,7G  0 part 
├─nvme0n1p4 259:4    0   990M  0 part 
├─nvme0n1p5 259:5    0  19,6G  0 part 
├─nvme0n1p6 259:6    0   1,5G  0 part 
└─nvme0n1p7 259:7    0 372,8G  0 part /var/snap/firefox/common/host-hunspell
```

It was there, you can see the 1,8T disk! (It wasn't that fast actually, I tried to use all kind of commands, like `smartmontools` to check for hardware issues, 
`fsck`, `e2fsck` for file consistency, etc.)

Next step, try to recover something? Thanks to ChatGPT I got to know that I could use `testdisk` and `photorec`. They're great tools, but unfortunately I think that
the hard drive was so damaged that when I tried to run them, the process seemed to get stuck. Maybe it wasn't really stuck, but the absence of any debug info led me to think so.
So, I tried to use them both. No success with `testdisk`. Partial success with `photorec`: after one night running I could see some files restored (some pdfs and pptx files).
Unfortunately only 1 of the 30k photos stored. Curiously, every time I tried to do some operation using the hard drive, I had the feeling that the runtime was getting worse.
Actually, not just the runtime, but everything!

```
TestDisk 7.1, Data Recovery Utility, July 2019
Christophe GRENIER <grenier@cgsecurity.org>
https://www.cgsecurity.org

Disk /dev/sda - 2000 GB / 1863 GiB - CHS 243201 255 63
Current partition structure:
     Partition                  Start        End    Size in sectors

Trying alternate GPT
Bad GPT partition, invalid signature.
```

Long story short, I ended up in more or less the same situation [described here](https://superuser.com/questions/1558628/testdisk-how-to-recover-from-partition-read-error/1561179).
Now, if you look at the first answer you'll see this: 

```
STOP ANY ACTION ON THE DRIVE RIGHT NOW. Good. Now you are no longer compounding the problem, let's try to dig ourselves out of the hole.

Realize your disk is likely failing - anything you do to it can only make it worse.

The first step I would take is to get another equal or larger drive and use ddrescue to try copy off the raw data. Once this is done, you can then pick at it as you like.
```

AAARGH! I was only compounding the problem! The correct approach would have been to make a backup copy of the data! This is actually also what they say in the 
[testdisk manual](https://www.cgsecurity.org/testdisk.pdf) (see chapter 16). I tried to use `ddrescue`, but after leaving it running another night I recovered only around 70% 
of the original hard disk:

```
sudo ddrescue --verify-on-error --reopen-on-error --min-read-rate=1M -f /dev/sdb /dev/sda /home/nico/Documents/backup.logfile
GNU ddrescue 1.23
Press Ctrl-C to interrupt
     ipos:   25210 MB, non-trimmed:    1392 kB,  current rate:       0 B/s
     opos:   25210 MB, non-scraped:        0 B,  average rate:  48005 kB/s
non-tried:  616700 MB,  bad-sector:        0 B,    error rate:       0 B/s
  rescued:    1383 GB,   bad areas:        0,        run time:      8h 23s
pct rescued:   69.17%, read errors:       22,  remaining time:      8h 50m
 slow reads:     1900,        time since last successful read:         56s
Copying non-tried blocks... Pass 2 (backwards)
ddrescue: Input file no longer returns data: Input/output error
```

By the way, `ddrescue` has a lot of arguments that can be set. Just use `ddrescue -h` for an overview or `info ddrescue` to get to know more.
To my surprise, that 70% of the rescued hard drive couldn't be found anywhere. Again, [rookie mistake](https://askubuntu.com/questions/906544/ddrescue-successfully-rescued-but-no-rescued-file-on-output).
I formatted my new hard drive with `mkfs.ntfs -f /dev/sdb` and run `ddrescue` again (I mounted the new hard drive at /media/nico/7D0D6E9B63792429, don't ask me why):

```
sudo ddrescue --verify-on-error --reopen-on-error --min-read-rate=1M -d -r3 -f --log-events=/tmp/events.logfile --log-reads=/tmp/reads.logfile --log-rates=/tmp/rates.logfile /dev/sda /media/nico/7D0D6E9B63792429/copy.img /home/nico/Documents/backup_5.logfile
[sudo] password for nico: 
GNU ddrescue 1.23
Press Ctrl-C to interrupt
Initial status (read from mapfile)
rescued: 1250 GB, tried: 2228 kB, bad-sector: 0 B, bad areas: 0

Current status
     ipos:   31779 MB, non-trimmed:    2228 kB,  current rate:       0 B/s
     opos:   31779 MB, non-scraped:        0 B,  average rate:   55982 B/s
non-tried:  749595 MB,  bad-sector:        0 B,    error rate:       0 B/s
  rescued:    1250 GB,   bad areas:        0,        run time:  1h 16m 42s
pct rescued:   62.52%, read errors:        0,  remaining time:    431d 16h
 slow reads:        0,        time since last successful read:          0s
Copying non-tried blocks... Pass 3
```

But after getting to 62%, the reading process got extremely slow (just look at the remaining time). At this point, I decided to cancel the run and try again using photorec.
The nice thing about `ddrescue` is that if you interrupt the process and then you call it again with the same logfile, it won't start from scratch, but just resume from where it stopped.
Surprise surprise, this time photorec was muuuuch faster and it started recovering jpeg files right away! After roughly 6 hours, the process completed and I was 
actually able to recover ~30k photos! 

![image](/assets/photorec.png)
