Setup Multiple Keys for LUKS
############################

Make sure you have all the needed packages installed: 
```
$ sudo apt install scdaemon opensc gnupg2
```

1. Generate an arbitrary number of gpg keys, e.g. like this:
```
$ gpg --edit-card
factory-reset
name
generate
```

2. Check for keys in gpg using `gpg -K` (the hardware keys should be referenced using the serial numbers)

3. Export the public keys using their IDs (found using `gpg -K`) like this:
```
$ gpg --armor --export MYKEYID > pubkey-1.asc
```

4. Find & verify LUKS device name, by comparing `/etc/crypttab`, `/etc/fstab` and contents in `/dev/disks/by-uuid/`

5. At this point we need:
* LUKS device name, e.g.: `nvme0n1p3_crypt` 
* all public keys to be used, e.g.: `pubkey-1.asc pubkey-2.asc`
* this script: https://github.com/daringer/smartcard-key-luks/raw/main/smartcard-key-luks

6. To enable apply the changes run (assuming all files are in the current directory):
```
$ chmod +x smartcard-key-luks
$ sudo ./smartcard-key-luks nvme0n1p3_crypt pubkey-1.asc pubkey-2.asc ...
```

You will be prompted for the luks passphrase in order to add the securtiy keys as luks keys. If not,
something went wrong and your security key(s) are not added as luks keys.

Now you can reboot your machine to test, if the keys do work.

From this point you might want to do the following things:

* change the keys' PINs: `gpg --change-pin`
* change the luks passphrase to something very secure: `sudo cryptsetup luksChangeKey /dev/nvme0n1p3`




