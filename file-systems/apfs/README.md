# APFS - Apple File System

Source: https://developer.apple.com/support/downloads/Apple-File-System-Reference.pdf

Notes:
- The storage device will have a GPT partition table and an EFI partition, followed by an APFS "container"
- The container has a superblock, checkpoint area, space manager area, then storage of objects and file data
- Objects are made up of several records, with each record stored as a key/value pair in a B-tree
- Objects can be ephemeral, physical, or virtual. Ephemeral are stored in memory for a container and persist across unmounts in a checkpoint. Physical are stored at a *known* block address on disk. Virtual are stored on disk at a block address and found using a *lookup* from an object map.

## File System Layout

The APFS File System layout is explained in a lengthy manner in the documentation, and will be added here later.

## Mounting an APFS volume  

### Linux

Required tools:
- APFS-fuse driver (can be installed from here if you don't already have it: https://github.com/sgan81/apfs-fuse)
- The Sleuth Kit (used to identify the partition offsets in the image: https://github.com/sleuthkit/sleuthkit/)  
- ewfmount (required only to mount an E01 image - not necessary if your image is a Raw/DD copy: https://github.com/libyal/libewf)  
  
Mounting:
- If your image is an E01, use `sudo ewfmount <image> <e01_mount_point>` to mount your image. If it is a Raw/DD image, proceed to the next step.
- To identify the important partitions in your image, you can run `sudo mmls <e01_mount_point>` or `sudo mmls <raw_image>`. The output will look similar to the following:
```bash
GUID Partition Table (EFI)
Offset Sector: 0
Units are in 512-byte sectors

      Slot      Start        End          Length       Description
000:  Meta      0000000000   0000000000   0000000001   Safety Table
001:  -------   0000000000   0000000039   0000000040   Unallocated
002:  Meta      0000000001   0000000001   0000000001   GPT Header
003:  Meta      0000000002   0000000033   0000000032   Partition Table
004:  000       0000000040   0000409639   0000409600   EFI System Partition
005:  001       0000409640   1954210079   1953800440   Unlabelled
006:  -------   1954210080   1954210119   0000000040   Unallocated
```
- From this, you want to identify the "longest" volume. Then you can determine additional information about the partition of interest. In this example, the partition started at `0000409640` named `Unlabelled` is the longest.
- To identify the APFS container layout, you can use the command `sudo pstat <raw_mounted_volume/image> -o <offset>`. In this case, the offset would be `409640`.
- Using the apfs-fuse driver, we will mount the raw image at another mount point. If your image started off as an E01, add `/ewf1` to the <image> variable: `sudo apfs-fuse <image> <apfs_mount_point>`
- Once mounted, you have access to the APFS volume at your `<apfs_mount_point>` path either through a terminal window, or through your file browsing application.

### Note:
- When running Linux command lines tools against the APFS image itself (the mounted E01 as ewf1 or the raw image), you may need the `pstat` output to identify the APSB Block Number to address the specific volume. For example:  
`sudo fls -m / -r -o 409640 <raw_mounted_volume/image> -B <APSB_Block_Number> > bodyfile`
- This command runs the Sleuthkit command `fls` recursively against the image at offset `409640` (the one we identified earlier) and points specifically at the APSB block number using the `-B` flag and redirects output to `> bodyfile`. This command generates a timeline from the file system contents of the volume at the identified offset.

## Tools  
- APFS-fuse driver - https://github.com/sgan81/apfs-fuse
- The Sleuth Kit - https://github.com/sleuthkit/sleuthkit/
- ewfmount - https://github.com/libyal/libewf

