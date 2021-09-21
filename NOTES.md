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
