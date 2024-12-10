# WSL - Windows Subsystem for Linux

Source: https://learn.microsoft.com/en-us/windows/wsl/


Notes:
- Runs variants of Linux in a Windows OS
- Two (current) versions, 1 and 2. WSL 2 is the current default
- Users can access files on one OS from within the other OS
- Windows drives are accessible in the `/mnt/` directory based on their drive letter: ie `/mnt/c` is the C: drive
- Linux drives and distributions are accessible from File Explorer using `\\wsl$` as the path
- Configuration of the interoperability of Linux with WSL can be accomplished with the `/etc/wsl.conf` file for each distro, or the `C:\Users\<UserName>\.wslconfig` file for all Linux distros
- Supports `systemd` to enable the execution of services and system management within the distro, making it self contained.


## General Info

The WSL distributions are saved as a vhdx file in the Windows OS under the `C:\Users\<UserName>\AppData\Local\Packages\<distro_folder>\LocalState` folder, where `<distro_folder>` is a folder named for the Distro.  
For example: `C:\Users\<UserName>\AppData\Local\Packages\CanonicalGroupLimited.Ubuntu20.04onWindows_79rhkp1fndgsc\LocalState\ext4.vhdx`. This vhdx file can be mounted using FTK Imager or Arsenal Image Mounter.


## Analysis


## Tools



## References

WSL documentation - https://learn.microsoft.com/en-us/windows/wsl/
wsl.conf / .wslconfig - https://learn.microsoft.com/en-us/windows/wsl/wsl-config#wslconf
