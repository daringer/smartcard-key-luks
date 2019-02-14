# smartcard-key-luks

OpenPGP smartcard setup with Linux boot.

## Description

This is a bash script that takes advantage of cryptsetup's new OpenPGP
smartcard support (https://tracker.pureos.net/T462).

You run the script with the GPG public key it should use, and it
automatically sets up a new LUKS secret, encrypts it against that
public key, and sets up crypttab, LUKS, initramfs, and GRUB so that
when you boot, you are prompted to insert your Librem Key and enter
your normal GPG PIN to decrypt your disk. It also modifys the
"recovery" boot option in grub to bypass the GPG key and request your
passphrase instead.
