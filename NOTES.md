Notes
=====

CoreFS
------  
CoreFS is the base system that contains the running kernel.  All updates are called from
inside this context.  It houses the main grub config.

RootFS
------  
RootFS is the running OS.  This system actually runs the payload.  To update to new 
versions of rootfs, the base subvolid (5) must be mounted.  An nspawn container is
launched and downloads the new image to the image dir (/srv/images).  If there are 
more than two images, then the oldest is deleted until there are only two images.

# Mount the default subvol  
```
mount -o subvolid=5 /dev/device  
```

# Download new image  
TBD

# Convert the sendstream  
```
cat image.zstd | zstd -dv | btrfs rec /srv/images  
btrfs property set image.btrfs ro false
```

# Update grub  
TBD

inst
----  
This is a simple installer.


Misc
----  
A simple chroot mount will suffice to run the kernel-install program from inside the rootfs context.

example:
mount -o subvolid=5 /dev/nvme0n1p3 /mnt
mount -o bind /boot /mnt/boot
chroot /mnt
kernel-install kernel-install -v add 5.13.19-100.fc33.x86_64 ./usr/lib/modules/5.13.19-100.fc33.x86_64/vmlinuz

At this point the /boot/loader/entries files will need to be updated with the following:

sed -i 's@options rhgb quiet selinux=0 audit=0 rw@options root=UUID=5984febe-fa19-4eef-9c6c-c7dca21432fc ro rhgb quiet selinux=0 audit=0 rw rootflags=subvol=/srv/images/rootfs_2102.img@' bfdf222d5f8143d6a7f9fa6901f5c26d-5.13.19-100.fc33.x86_64.conf

