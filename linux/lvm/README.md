# LVM - Logical Volume Manager

Source: https://github.com/libyal/libvslvm/blob/main/documentation/Logical%20Volume%20Manager%20(LVM)%20format.asciidoc

Notes:
- The LVM consists of a physical volume, a volume group, and a logical volume.
- The physical volume contains information about the volume label, data and metadata areas, and can include date-time values
- Additional information about the layout can be found at the Source listed above.

## Physical Volume Label Header

32-bytes

|Offset   |Size             |Value                  |Description |
|-      |-                  |-                      |- |
|0      |8                  |LABELONE               |Signature |
|8		|8					|						|Sector Number of the Physical Volume Label Header |
|16		|4					|						|CRC-32 Checksum for offset 20 to end of the Physical Volume Label Sector|
|20		|4					|						|Data offset or header size in bytes relative from the start of the Physical Volume Label Sector|
|24		|8					|LVM2 001				|Volume Type |

Additional information about the remainder of the Physical Volume Header can be found at the Source listed above.  
  
## Mounting - Linux

The software necessary to mount the LVM image / device is already typically installed in a Debian OS, so no additional software will be required.

- The first step is to identify an available loop device on your system on which to mount the image:  
`sudo losetup -f  # This will find the first available loop device`  
- Now we need to mount the image / device to the first available loop:  
`sudo losetup -P /dev/loop# <path-to-disk/device>  # You could do losetup -f -P <disk/device> instead to merge this with the previous command`  
- List the block devices so you can see if the device is mounted at the proper loop device:  
`sudo lsblk -f`  
- Now we will create a device map from the loops partition table:  
`sudo kpartx -a /dev/loop#`  
- Then pass the LVM device information to the LVM:  
`sudo pvscan --cache`  
- And check for all available LVM's and identify their volume group names:  
`sudo vgs`  
`sudo lvs`  
  
With the device mounted and the LVM available, we will now need to activate the Volume Group we identified in the previous commands:  
`sudo vgchange -ay <volgroup name>`  
`sudo lvdisplay`  

If everything works out correctly, we should see the available `/dev/mapper/loop` partitions available, and we can then mount them:  
`sudo mount -o ro,noload /dev/mapper/loop0p<1-9> <mount-point>`  
`lvchange -an /dev/mapper/<vg_name>`  

## Unmounting  
  
Once you are finished with the mounted volume, you will need to remove the partition table mapping and remove the loop device:  
`kpartx -d /dev/loop#`  
`losetup -d /dev/loop#`  


## Troubleshooting

Should you need to troubleshoot the mounting of the volumes, you can use the `fdisk -l` command to list disks, or `parted -l` to list partitions available.  
Additionally, you can also use the built-in `mount` command to mount the desired device, using the `superblock` address to map specifically to the volume:  
`mount sb=<superblock> /dev/device /mountpoint`
