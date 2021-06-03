Setup Multiple Keys for LUKS
----------------------------

Make sure you have all the needed packages installed: 
```
$ sudo apt install scdaemon opensc gnupg2
```

1. Generate an arbitrary number of gpg keys, e.g. like this:
```
$ gpg --edit-card
admin
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
  **keep in mind that the ubuntu-disks-gui passphrase change will not work after setting up security keys, you have to use this method to change the passphrase**
* another option is to remove the passphrase entirely `sudo cryptsetup luksRemoveKey`, you will be prompted for the passphrase
* if you would like to set up a new set of keys you'll have to remove the last keyfile from the luks keys first: `sudo cryptsetup luksRemoveKey /etc/path/to/your.key`, afterwards delete the keyfile itself and restart the procedure above (the keyfile is to be taken from `/etc/crypttab`. The referenced script uses: `/etc/cryptsetup-initramfs/cryptkey.gpg`.



