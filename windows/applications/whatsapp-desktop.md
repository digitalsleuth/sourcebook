# WhatsApp Desktop

The databases for WhatsApp Desktop on Windows are encrypted when stored locally on the system, both when in use and when at rest. The encryption mechanisms used to encrypt the databases are quite extensive, which can make the decryption challenging. AES-256-GCM, AES-256-CBC-PKCS#7, and PBKDF2 are just some of the encryption methods used.

There are multiple values required from different locations, including data stored and available only on the system when it is live. These pieces of information are:

- DPAPI Blob
- Wrapped Key
- Nonce
- Cipher Text
- GCM Tag
- OfflineDeviceUniqueID
- WhatsApp DLL Passphrase (hard coded into DLL)

These pieces of information have to be extracted from both the nondb_settings16.dat and nondb_settings18.dat, as well as the live system (OfflineDeviceUniqueID). The WhatsApp DLL Passphrase is hard-coded in the WhatsApp DLL.

The values extracted from the nondb_settings16.dat file are used to extract the UserKey. The values extracted from the nondb_settings18.dat go through another round of decryption producting another encryption key, then the UserKey is used to decrypt the still-encrypted encryption key. Once this decryption process is complete, we get the Database Key, which can then be used to decrypt the files.

It's important to note, that the database files themselves are encrypted, not simply the content.

During my analysis, I've identified a number of file signatures with precede the values we need to extract from the two nondb_settings files. 

The following signatures were observed before each of the values during analysis of multiple
nondb_settings dat files

#### dpapi_blob signature: 02010430.
If the next byte is not 81 or 82, Then skip that and 2 more bytes and read the right nibble of the 4th byte to determine the number of bytes to read next for the size of the dpapi_blob.

If it is 81 or 82, the right nibble tells you how many bytes come after it, after those it's a 0x04, then the size byte for the num of bytes in the
dpapi_blob size. So if it's 81, then 1 byte follows (skip it), then 04 after that (skip it) then the third byte is the byte count for size.
If it's 82, then 2 bytes follow (skip them) then 04, then the fourth byte is the byte count for size.
Could use the 01 00 00 00 signature, however that could appear more frequently than expected.

#### wrapped_key signature: 04012D04
The byte after the dpapi_blob signature (rather, the right nibble) indicates how many bytes are in the size of the entire block from 0x04 until
the entry: 0x30 0B 06 09 60 86 48 01 65 03 04 01 2D 04
For now, we can use the last 4 bytes for the wrapped_key signature
The next byte is typically 28 (40) which is the size of the wrapped_key.

#### nonce signature: 2E301104
The next 30 bytes for the nondb_settings16.dat seem to be: 30 6D 06 09 2A 86 48 86 F7 0D 01 07 01 30 1E 06 09 60 86 48 01 65 03 04 01 2E 30 11 04 0C
And for the nondb_settings18.dat the next 32 seem to be:   30 82 01 EF 06 09 2A 86 48 86 F7 0D 01 07 01 30 1E 06 09 60 86 48 01 65 03 04 01 2E 30 11 04 0C
The 0C is the size for the nonce, but since both values have 2E 30 11 04 before the size, we can use that as the signature.

#### cipher_text: 02011080
Immediately after the nonce should be 02 01 10 80. If the following byte is 81 or 82, we read the right
nibble to determine how many bytes following this next byte to read for the size of the cipher_text and the gcm tag together.
Otherwise, the next byte is the size of the cipher_text and the gcm tag together.
This seems to go all the way to the end of the file, with the exception of the last 5 bytes.

#### gcm is last 16 bytes of cipher_text

The last 5 bytes of each file are different for each file, except that the last byte is always 01.

#### Hard Coded Value
The WhatsApp DLL Passphrase is a hard-coded value, which is `5303b14c0984e9b13fe75770cd25aaf7`

#### OfflineDeviceUniqueID (ODUID)
This value is a combination of the extraction of a RandomSeed from either the TPM (if present), UEFI, Registry, or License Service from the Microsoft Store. The value extracted from the TPM, UEFI, and Registry are all initially the RandomSeed, which then has to be applied to the Publisher ID. However, the License Service (using ClipSVC to retrieve the value for GetSystemIdForPublisher) is already "salted" with the Publisher ID.

There are ways to manually extract the data from UEFI, Reigstry and the License Service, however doing so from the TPM manually would be challenging. The extraction processes for each of these can be found in the research paper listed in the references below.

## Tools
Currently, there are two options for doing a WhatsApp acquisition and decryption. Live and Offline.
In doing an Offline decryption, you will not only need the database files, but you **must have the ODUID** value. You can use a tool like [ZAPiXDESK](https://github.com/digitalsleuth/ZAPiXDESK) originally written by Alberto Magno and updated with signatures by [digitalsleuth](https://github.com/digitalsleuth). While the version of the script provided here is the `digitalsleuth` version, it is only due to the addition of certain features to detect the proper offsets. A pull request has been submitted with these updates to the original author and, if accepted, this page will point to the original repo.

In the binaries folder on that page, you can run the "ODUID.exe" file on the live running system, or run the `ZAPiXDESK.ps1` PowerShell script referenced above with the `-GetID` flag.

If you want to do a live acquisition and decryption, you can use the same PowerShell script with the appropriate flags to choose the location of the WhatsApp Desktop directory and Output Path.

#### References
- https://doi.org/10.1016/j.fsidi.2024.301861
- https://github.com/digitalsleuth/ZAPiXDESK
- https://github.com/kraftdenker/ZAPiXDESK
