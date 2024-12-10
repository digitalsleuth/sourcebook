## Windows CMD Cheat Sheet

#### Table of Contents

- [Navigation](#navigation)
- [File Management](#file-management)
- [Disk Management](#disk-management)
- [System Information and Networking](#system-information-and-networking)
- [Process Management](#process-management)
- [Batch Scripting](#batch-scripting)
- [Flow Control](#flow-control)
- [Keyboard Shortcuts](#keyboard-shortcuts)


### <a href="#navigation">Navigation</a>

| Command | Explanation |
|---------|-------------|
|c:|Change the current drive to the C:\ drive|
|d:|Change the current drive to the D:\ drive|
|CD| c:\path\to\my_folder|Change directory to c:\path\to\my_folder|
|CD ..|Navigate to the parent directory of the current working directory|
|CD .\new_folder|Navigate to the folder new_folder located in the current working directory|
|CD /D d:\videos\ |Change the current drive to D:\ and access the folder videos on it.|
|DIR|Display files and folders in the current directory|
|DIR /A c:\apps\ |Display files and folders in the directory c:\apps\|
|DIR /A:D|Display only folders (D: directories)|
|DIR /A:-D|Display only files (D: directories; -: not)|
|DIR /A:H|Display hidden files and folders|
|DIR /O|Display files and folders sorted alphabetically|
|DIR /O:S|Display files and folders sorted by file size from smallest to largest|
|DIR /O:-S|Display files and folders sorted by file size from largest to smallest|
|DIR /B|Display only the names of files and folders in the current working directory|
|SORT|Take input from a source file/pipeline, sort its contents alphabetically (default: A to Z; in reverse: Z to A), and display the output|
|SORT "C:\music\playlist.m3u"|Sort the contents of C:\music\playlist.m3u line by line|
|DIR /B \| SORT /R /O ZtoA.txt|List all file and folder names in the current working directory, sort them in reverse alphabetical order, and save the sorted output to a file ZtoA.txt|
|MOVE|Move a file or files|
|MOVE c:\f1\text.txt c:\f2|Move a file text.txt from one folder c:\f1 to another folder c:\f2|
|MD new_folder<br>MAKEDIR new_folder|Create a new folder called new_folder in the current directory|
|RD new_folder<br>RMDIR new_folder|Delete the folder called new_folder in the current directory|
|TREE|Show the directory structure of a disk/folder|
|TREE "C:\Program Files"|Show the directory structure of the folder “Program Files” on the disk C:\|
|TREE C:\ /F|Display the names of the files in each folder in the directory structure of the C:\ drive|
|ATTRIB|Display/set the attributes of the files in the current directory|
|ATTRIB +H +S +R myItem|Hide a file/folder myItem|
|ATTRIB -H -S -R myItem|Unhide a file/folder myItem|

### <a href="#file-management">File Management</a>

| Command | Explanation |
|---------|-------------|
|COPY text.txt C:\schoolwork|Copy the file text.txt to a folder with the path C:\schoolwork|
DEL text.txt<br>ERASE text.txt|Delete the file text.txt
REN text.txt script.bat<br>RENAME text.txt script.bat|Rename a file text.txt to script.bat
REPLACE .\src\hey.txt .\dest|Overwrite; replace a file named hey.txt in a local folder src with another hey.txt in a local folder dest, both files sharing the same name.<br>Warning: Don’t specify .\dest\hey.txt anywhere here.
XCOPY|Copy files and directory trees to another folder.<br>XCOPY is similar to COPY but with additional switches to specify the source and destination paths in detail.
XCOPY /S folder1 folder2|Copy folders and subfolders of folder1 to folder2
ROBOCOPY|Robust copying of files and directories: by default, such copying only occurs if the source and destination differ in time stamps or file sizes.
EXPAND gameData.cab|Decompresses the compressed .CAB cabinet file gameData.cab
FC file1.ext file2.ext|Compare the contents of two files (file1.ext, file2.ext) and display non-matching lines
COMP file1.ext file2.ext|Compare the contents of two files (file1.ext, file2.ext) and display non-matching items
FIND "python" in run.bat|Output every line that contains a text string (which you must enclose in quotation marks) "python" in the file run.bat
FIND /C "python" in run.bat|Count every line that contains a text string (which you must enclose in quotation marks) "python" in the file run.bat
PRINT resume.txt|Print contents of a file resume.txt
OPENFILES /QUERY|Query/display open files
OPENFILES /DISCONNECT|Disconnect files opened by network users.
TYPE test.txt|Displays the contents of the file test.txt
TYPE playlist.m3u | SORT /unique /o C:\work\unique_play.m3u|Sort a file playlist.m3u and output only the unique values to a file C:\work\unique_play.m3u
MORE|Display contents of one or more files, one screen at a time.
ASSOC|Display or change the association between a file extension and a file type
NOTEPAD|Open the Notepad application from cmd
NOTEPAD filename.ext|Open a file filename.ext in Notepad

### <a href="#disk-management">Disk Management</a>

| Command | Explanation |
|---------|-------------|
CHKDSK|Check and repair disk problems (local disks only)
CHKDSK /F A:|Fix errors on A: drive
CHKDSK /R A:|Recover data on A: drive
CHKDSK /X A:|Dismount drive A:
CIPHER /E classified|Encrypt the folder classified
CIPHER /D secret_recipe.txt|Decrypt the file secret_recipe.txt
DEFRAG|Disk Defragmentation
CHKNTFS|Display/modify disk-checking on startup
COMPACT|Display/change the compression of files in NTFS partitions
CONVERT|Convert FAT disk volume to NTFS
DISKPART|Display and adjust disk partition properties
FORMAT|Format the disk
FSUTIL|File system management
LABEL d:x|Rename disk D:\ to X:\
SUBST p: c:\taxes|Assign drive P:\ to the local folder c:\taxes
SUBST p: /D|Remove the path represented by P:\
RECOVER d:\data.dat|Recover a file data.dat from a bad or defective disk D:\
VOL|Display current disk volume label and serial number
POWERCFG|Control power settings and configure Hibernate/Standby modes
SFC /SCANNOW|Scan and update protected system files


### <a href="#system-information-and-networking">System Information and Networking</a>


| Command | Explanation |
|---------|-------------|
VER|Display the current operating system version
SYSTEMINFO|List system configuration
HOSTNAME|Show the computer’s hostname on the network
DRIVERQUERY|Show all installed device drivers
DATE|Display/set system date
TIME|Display/set system time
GPRESULT|Display Resultant Set of Policy (RSoP) information for a remote user and computer.
GPUPDATE|Update group policies
IPCONFIG|Display Windows IP network configurations
IPCONFIG /release|Release your current local IP address
IPCONFIG /renew|Request a new local IP address
IPCONFIG /flushdns|Reset the contents of the DNS client resolver cache
PING google.com|Send ICMP requests to the target google.com and check host availability
PATHPING|Trace route and provide network latency and packet loss for each router and link in the path
NET|Provide various network services
NET use M: \\gameServ /user:"ReadyPlayerOne" player1|Assign as disk M:\ the path \\gameServ, logging in as “ReadyPlayerOne” and password “player1”
TRACERT|Find the IP address of any remote host
NSLOOKUP|Find IP addresses on a nameserver
ROUTE|Manipulate network routing tables
ROUTE PRINT|Displays network route details
ARP -A|List IP addresses and corresponding physical addresses (Address Resolution Protocol)
NETSH|Configure network interfaces, Windows firewall, routing, and remote access
NETSTAT|Display current TCP/IP network connections and protocol statistics
GETMAC|Shows all MAC addresses of the network adapters

### <a href="#process-management">Process Management</a>


| Command | Explanation |
|---------|-------------|
SCHTASKS|Create/edit a job on Task Scheduler. Use this to create scheduled tasks in Disk Management.
SET|List environment variables
PATH|Display/change the list of folders stored in the %PATH% environment variable
SHUTDOWN /R|Restart the computer
SHUTDOWN /S /T 60|Shut down the computer 60 seconds from now
TASKLIST|List running tasks
TASKLIST /SVC|Show services related to each task
TASKLIST /V|Display detailed task information
TASKLIST \| FIND "1234"|Get the name of the executable associated with the process ID (PID) of 1234
TASKKILL|End one or more tasks
TASKKILL /IM "msedge.exe"|Terminate all Microsoft Edge instances
TASKKILL /PID 10736|Terminate process with PID of 10736
REGEDIT|Registry Editor
RUNAS /USER:user2 program1|Execute a program program1 as another user user2
POWERSHELL|Open a Powershell instance

### <a href="#batch-scripting">Batch Scripting</a>

Note that you call variables in arithmetic or logical expressions by enclosing each with two “%” signs (e.g., “%a%”).

| Command | Explanation |
|---------|-------------|
REM comment. . .<br>:comment. . . | Prefix for the single-line comment “comment. . .”
GOTO end<br><comment_block><br>:end|Format of multi-line comments represented by <comment_block> enclosed by delimiters end and :end
SET /A c = %a% + %b%|Assign the arithmetic expression a+b to the variable c
^|Escape character
some_command > output.txt|Redirect output of some_command to a file output.txt
?|Wildcard representing one character
*|Wildcard representing multiple characters
&|Introduce a new command on the same line
TIMEOUT 3600|Tell the command prompt to sleep for 3600 seconds (= 1 hour)
PAUSE|Prompt the user to continue
CHOICE|Prompt the user to pick an on-screen option
CHOICE /T 15 /C ync /CS /D y /M "Press y=Yes, n=No, c=cancel:"|You have 15 seconds to press Y, N, or C keys without capitalization, defaulting to “y” if time runs out without a decision
CLS|Clear screen
CMD|Restarts Windows command prompt window:
COLOR|Set text and background color of cmd:
ECHO ON|Display each command executed
ECHO OFF|Only display command output
ECHO a string of characters|Display a string of characters
HELP|Display help
PROMPT topSecret^>$$|Changes the command line prompt to topSecret>$ for the current session
PROMPT|Reset the command line prompt to default
START X|Start/open a program/document X in a new window
TITLE top Secret|Set the title of the current session of Windows command prompt to top Secret
/?|Add this to the end of any command word (shown in ALL CAPS in this cheat sheet) to get help on the command, e.g., CD/? = manual for CD (change directory) command
| CLIP|Append this to the end of a command to copy the command output to the clipboard
EXIT|Exits the command line

### <a href="#flow-control">Flow Control</a>

Note the condition is a Boolean expression e.g., %a%==5.

| Conditional | Syntax |
|-------------|--------|
If|IF (condition) do_something
If-else|If (condition) (do_something)<br>ELSE (do_something_else)
Nested if|IF (condition1) IF (condition2) do_something
Infinite loop|:marker<br>do_something<br>GOTO marker
While loop|:marker<br>IF (condition) (<br>&nbsp;&nbsp;&nbsp;&nbsp;do_something<br>&nbsp;&nbsp;&nbsp;&nbsp;GOTO :marker<br>)

### <a href="#keyboard-shortcuts">Keyboard Shortcuts</a>

| Key | Effect |
|-----|--------|
Tab|Autocomplete
Ctrl+F|Find text in console (opens dialog box)
F1, F3, F5, F8|Retype command
F2|Copy the current command leftward of the cursor
F4|Delete the current command rightward of the cursor
F6|Insert end-of-file character
F7|List previous commands from which you choose
F9|Retype a command by typing its line number in the command history

Source: https://www.stationx.net/windows-command-line-cheat-sheet/