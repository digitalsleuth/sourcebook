# Signal Desktop

## Versions prior to July 2024

Signal Desktop for Windows prior to July of 2024 used to store the database key for the SQLCipher encrypted sqlite databases in the file `config.json` located in the Signal directory (%APPDATA%\Signal).
```
{
  "key": "4e85241594b49c3628593bb70b1695bdf630987837b0cca0c31cab39ff3476ee"
}
```
You could simply use a tool like DB Browser for SQLCipher, (which comes with the DB Browser for SQLite installation), change the drop-down on the right from `Passphrase` to `Raw`, type 0x in the text box then paste in the config.json `key` value. So your value would be:

`0x4e85241594b49c3628593bb70b1695bdf630987837b0cca0c31cab39ff3476ee`

The `v1.2.0` version of [signal-parser](https://github.com/digitalsleuth/signal-parser) will read the config.json key, use it to decrypt and parse all of the databases and data, and present them to you in a user-friendly Flask-based web interface.

However, this parser has not yet been updated to support the version of Signal from July 2024 onwards.

## Versions from July 2024 afterwards

Based on the [source code](https://github.com/signalapp/signal-desktop), the app is now using the Electron / Chromium "Safe Storage" mechanism, which utilizes both application encryption and OS encryption / security measures.

Under Windows, this means you will not only need access to the Signal directory, but access by some means to the Windows DPAPI layer, or at least the masterkeys.

On a live system, you will need to obtain the DPAPI masterkey in order to decrypt the "encryptedKey" from the `config.json` file.
You can attempt to run a tool like mimikatz or pypykatz to extract the master keys.
You will also need the contents of the "C:\\Users\\<user>\\AppData\\Roaming\\Signal\\Local State" file, specifically
the "encrypted_key" value.

#### On a dead-box system:
You will need a memory dump in order to proceed, as well as volatility3, pypykatz, 
the pypykatz-volatility3 plugin from https://github.com/skelsec/pypykatz-volatility3.

Using volatilty3, run:
    vol3 -f <memory_dump> -p <path_to_pypykatz-volatility3_folder> pypykatz
It will take a while to load, but once done, you should see a number of masterkeys. Make note of all of them.

#### If you have a bit-for-bit image (E01 or raw):
You may also be able to virtualize the system by [following these steps](https://github.com/digitalsleuth/forensics_tools/blob/master/Virtualize-an-E01.md)
Once you've virtualized the system, you should be able to copy/paste into the VM. 

**NOTE:** The following tools (at least mimikatz) will set off your Antivirus on your system. This is expected, so you should be aware of it and take the appropriate measures for your environment and office policies.

You'll need to disable AV (Defender, or whatever is installed), then copy the latest version of Mimikatz (https://github.com/gentilkiwi/mimikatz), and a copy of PsExec from SysInternals (https://learn.microsoft.com/en-us/sysinternals/downloads/sysinternals-suite?source=recommendations) into the VM and onto the Desktop.

Now, you will need to unzip Mimikatz and put the folder on the desktop as well. Then, from an admin command prompt, change directory (cd) to the desktop and run:

`PsExec.exe -i - s <full_path_to_mimikatz.exe>`

Replace what is between the <> with the actual FULL path to the mimikatz/x64/mimikatz.exe 
Once Mimikatz is open, run:

`sekurlsa::dpapi`

#### For both live and dead-box on Windows:
You will also need impacket, and the following steps will be the same for either system.
**NOTE:** the impacket suite may trigger an antivirus alert on your system. This is expected, so you should be aware of it and take the appropriate measures for your environment and office policies.

You may also want to get the following script: https://github.com/digitalsleuth/signal-parser/blob/develop/decrypt-signal-key.py. You can use it to decrypt the final keys to get the database key.

If you already have the "encrypted_key" value from the Signal Local State file, it needs to be base64 decoded and
placed / redirected to a file. If you don't already have it, you can get it using the following
command in Windows PowerShell:
 ```
    $hex = cat -Raw '<path_to_signal_directory>\\Local State' | ConvertFrom-Json | % os_crypt | % encrypted_key | % {[convert]::FromBase64String($_)}
    [System.IO.File]::WriteAllBytes(<full_file_path>, $hex)
 ```
Or Linux or Mac (you may need the jq program if you don't already have it):
```
    cat <path_to_Local State> | jq -r .os_crypt.encrypted_key | base64 -d | tail -c +6 > <full_file_path>
```

Once you have this encrypted file (which is the base64 decoded encrypted_key), you can use the 'dpapi.py' module from Impacket to unprotect it:
    `dpapi.py unprotect -file <encrypted_file> -k 0x<master_key>`
Make sure you place a 0x in front of the master key you retrieved. If you get an error, try the next master key.

Once you get "Successfully decrypted data", copy the bytes from the result and combine them together (no spaces).
The result of this script is the `decrypted_master_key`

Now run the `decrypt-signal-key.py` script with: -e <encrypted_key_from_config.json> -k <decrypted_master_key>

References:
    https://github.com/signalapp/signal-desktop
    https://source.chromium.org/chromium/chromium/src/+/main:components/os_crypt/sync/os_crypt_mac.mm
    https://www.hackthebox.com/blog/memory-dump-analysis-with-signal
    https://github.com/MatejKafka/PSSignalDecrypt
    https://www.coresecurity.com/core-labs/articles/reading-dpapi-encrypted-keys-mimikatz
    https://github.com/bepaald/signalbackup-tools
