# cramfs - Compressed ROM/RAM file system

Source: https://www.kernel.org/doc/html/v5.7/filesystems/cramfs.html

Notes:
- designed to be small, simple
- uses zlib to compress a file one page at a time
- terse representation of metadata
- great for small embedded systems (DVR, Roku, etc)
- max file size is 16 MB
- max file system size is ~ 256 MB
- must be read / written with architectures of the same endianness
- endianness sometimes achieved at a 4-byte level
- timestamps are not stored in cramfs at the file-system level, but may be present in memory while the system is cached in memory

## Typical file system layout

|offset	|fmt/val			|purpose 				| 
|-		|-					|-						| 
| 0		|ulelong 0x28cd3d45	|Linux cramfs offset 0	| 
| 4		|ulelong x			|size %d				|
| 8		|ulelong x			|flags 0x%x				|
|12		|ulelong x			|future 0x%x			|
|16		|string  >\0		|signature "%.16s"		|
|32		|ulelong x			|fsid.crc 0x%x			|
|36		|ulelong x			|fsid.edition %d		|
|40		|ulelong x			|fsid.blocks %d			|
|44		|ulelong x			|fsid.files %d			|
|48		|string  >\0		|name "%.16s"			|
|512	|ulelong 0x28cd3d45	|Linux cramfs offset 512|
|516	|ulelong x			|size %d				|
|520	|ulelong x			|flags 0x%x				|
|524	|ulelong x			|future 0x%x			|
|528	|string  >\0		|signature "%.16s"		|
|544	|ulelong x			|fsid.crc 0x%x			|
|548	|ulelong x			|fsid.edition %d		|
|552	|ulelong x			|fsid.blocks %d			|
|556	|ulelong x			|fsid.files %d			|
|560	|string  >\0		|name "%.16s"			|

## Analysis

As stated in the Notes above, the file system must be read with an architecture of the same endianness, so if the image you acquire is from a BE (Big Endian) system, and you're using a LE (Little Endian) system for analysis, you will need to convert the image.

There are a couple of ways to do this:

- Use the tool `cramfsswap` which is available in most Ubuntu APT pkg repos, or you can download the .deb package and install it on another Debian-based system (https://launchpad.net/ubuntu/+source/cramfsswap)
- You can also use scripting to open the file read x # of bytes (where x is the endianness byte length, usually 4 bytes), convert the endianness of the bytes you've read and write them to another file

Once you've converted the endianness (if necessary), then you can use the tool `uncramfs` or `uncramfs-lzma` as necessary, from https://github.com/rampageX/firmware-mod-kit (under the src/ directory). 
Note, these two tools will have to be built using `make` or using your favorite compiler. Once compiled, the command will be:
`./uncramfs <output_directory> <input_file>` and your file system will be decompressed. It may also be possible to use the `mount -t cramfs` command to mount the image, but this would involve adding the driver subtype, which is not covered here.

## Tools

These tools may be useful during analysis, but not all of them have been tested. USE AT YOUR OWN RISK!

cramfs-tools - https://github.com/npitre/cramfs-tools  
firmware-mod-kit - https://github.com/rampageX/firmware-mod-kit (src/uncramfs and src/uncramfs-lzma)
cramfsswap - https://launchpad.net/ubuntu/+source/cramfsswap
lzma-uncramfs - https://github.com/digiampietro/lzma-uncramfs
binwalk - https://github.com/ReFirmLabs/binwalk


