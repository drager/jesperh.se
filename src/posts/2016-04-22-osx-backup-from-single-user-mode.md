---
title: OSX backup from Single User Mode
lang: en
---

I ran into some trouble when I closed my Macbook with a low percentage
of battery left, I had no problem with it whatsoever when I closed it.
The morning after it was turned off, probably because of the low battery.
I tried to start it up and the Apple logo appeared as usually as well as the
loading bar, but it came just till 50 % and no longer. I waited a while to see
if it could load more than 50 % but after a while the computer just turned off.

So I figured it was something wrong with my drive. I tested to boot into
recovery mode, `Command+R` which worked just fine. In recovery mode I thought
I should use *Disk Utility* to verify the disk, it told me the drive was
damaged somehow and that I should repair the disk via *Disk Utility*. I tried
to repair it multiple times but it always resulted in an error message that
said the following:
>"Error: Disk Utility can’t repair this disk. Back up as many of your
files as possible, reformat the disk, and restore your backed-up files."

After I had received that error message a bunch of times I was going to try
another approach in order to get my needed files. What I had in mind was
the `fsck` command which I needed to run in OSX's *Single User Mode*.

In order to boot into the *Single User Mode* we need to press
`Command+S` on startup, right after the startup sound. You should only see
a terminal which has outputted some text. And when booted into
*Single User Mode* we could just type `/sbin/fsck -fy`.
Once `fsck` completes, if you see a message that says:
*"File system was modified"*, then you should run `/sbin/fsck -fy`
again until you see a message that says:
*"The volume (name) appears to be OK"*. After that just type `reboot` to leave
*Single User Mode* and boot the Mac back into OSX.

This however did not work out for me either. I did get an error message from
`fsck` which stated: *"Disk full error"* even though I had *~8 GB* free on the disk.

It was a lot of fun that every repair tool that I used would fail in some way...
So I figured I could not repair it, but I hoped that I could anyhow back up
all the files that I wanted. In order to create a backup I first needed an
USB-drive to backup the data on. I plugged in the USB and started my Macbook and
once again entered *Single User Mode*.

In order to do an backup from the *Single User Mode* I first needed to set the
file system in read-write mode which is done with the following command:
```
/sbin/mount -uw /
```

Next in line was to create an folder where I could mount the USB:
```
mkdir /Volumes/usb
```

After the folder is created I could simply mount it using the `mount` command:
```
mount -t msdos /dev/disk2s1 /Volumes/usb
```

The flag `-t` lets you choose the file system type, in my case my USB was
FAT32 formatted and therefore did I pass `msdos` as the type. If the USB is
formatted in OSX you should probably use the type `hfs`.

The second part of the `mount` command `/dev/disk2s1` is which disk that should
be mounted. The name will depend of the disks and partitions you have
connected to your computer. It's possible to list all the disks with the
following command: `ls /dev/ | grep disk`. The last part of the `mount` command
`/Volumes/usb` is where we should mount our drive.
(The folder we just created using `mkdir`).

Okey, so the USB is mounted and the next step is to copy over the files to it.
I wanted to copy over my whole home folder.
It can be done either by using:
```
rsync -rv Users/jesper /Volumes/usb/
```
or by using:
```
cp -r Users/jesper /Volumes/usb/
```

The `-r` flag is for copying files recursively. The `v` flag is just for verbose
output.

It will take a lot of time to copy over all the files so I created an archive
using the `tar` command:
```
tar -zcvf backup.tar.gz /home/jesper
```

I also wanted to ignore folders like node_modules and Applications.
I did that by using the `tar` command with the `--exclude` flag:
```
tar --exclude="node_modules" --exclude="Applications" -zcvf backup.tar.gz /home/jesper
```

Then I could copy over the archive to the USB using `cp`:
```
cp backup.tar.gz /Volumes/usb/
```
and then unmount the USB using `umount`:
```
umount /Volumes/usb
```
and lastly rebooting by typing `reboot`.
