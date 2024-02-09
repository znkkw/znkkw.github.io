# Arch Linux Full-Disk Encryption Installation Guide 
This guide provides instructions for an Arch Linux installation featuring full-disk encryption via LVM on LUKS and an encrypted boot partition (GRUB) for UEFI systems.

Following the main installation are further instructions to harden against Evil Maid attacks via UEFI Secure Boot custom key enrollment and self-signed kernel and bootloader.

## Preface
You will find most of this information pulled from the [Arch Wiki](https://wiki.archlinux.org/index.php/Dm-crypt/Encrypting_an_entire_system#Encrypted_boot_partition_(GRUB)) and other resources linked thereof.

*Note:* The system was installed on an NVMe SSD, substitute ```/dev/nvme0nX``` with ```/dev/sdX``` or your device as needed.

## Pre-installation
### Connect to the internet
Plug in your Ethernet and go, or for wireless consult the all-knowing [Arch Wiki](https://wiki.archlinux.org/index.php/installation_guide#Connect_to_the_internet).

### Update the system clock
```
timedatectl set-ntp true
```

### Preparing the disk
#### Create EFI System and Linux LUKS partitions
##### Create a 1MiB BIOS boot partition at start just in case it is ever needed in the future

Number | Start (sector) | End (sector) |    Size    | Code |        Name         |
-------|----------------|--------------|------------|------|---------------------|
   1   |   2048         |   4095       | 1024.0 KiB | EF02 | BIOS boot partition |
   2   |   4096         |   1130495    | 550.0 MiB  | EF00 | EFI System          |
   3   |   1130496      |   976773134  | 465.2 GiB  | 8309 | Linux LUKS          |

```gdisk /dev/nvme0n1```
```
o
n
[Enter]
0
+1M
ef02
n
[Enter]
[Enter]
+550M
ef00
n
[Enter]
[Enter]
[Enter]
8309
w
```

#### Create the LUKS1 encrypted container on the Linux LUKS partition (GRUB does not support LUKS2 as of May 2019)
```cryptsetup luksFormat --type luks1 --use-random -S 1 -s 512 -h sha512 -i 5000 /dev/nvme0n1p3```

#### Open the container (decrypt it and make available at /dev/mapper/cryptlvm)
```
cryptsetup open /dev/nvme0n1p3 cryptlvm
```

### Preparing the logical volumes
#### Create physical volume on top of the opened LUKS container
```
pvcreate /dev/mapper/cryptlvm
```

#### Create the volume group and add physical volume to it
```
vgcreate vg /dev/mapper/cryptlvm
```

#### Create logical volumes on the volume group for swap, root, and home
```
lvcreate -L 8G vg -n swap
lvcreate -L 32G vg -n root
lvcreate -l 100%FREE vg -n home
```

The size of the swap and root partitions are a matter of personal preference.

#### Format filesystems on each logical volume
```
mkfs.ext4 /dev/vg/root
mkfs.ext4 /dev/vg/home
mkswap /dev/vg/swap
```

#### Mount filesystems
```
mount /dev/vg/root /mnt
mkdir /mnt/home
mount /dev/vg/home /mnt/home
swapon /dev/vg/swap
```

### Preparing the EFI partition
#### Create FAT32 filesystem on the EFI system partition
```
mkfs.fat -F32 /dev/nvme0n1p2
```

#### Create mountpoint for EFI system partition at /efi for compatibility with grub-install and mount it
```
mkdir /mnt/efi
mount /dev/nvme0n1p2 /mnt/efi
```

## Installation
### Install necessary packages
```
pacstrap /mnt base linux linux-firmware mkinitcpio lvm2 vi dhcpcd wpa_supplicant
```

## Configure the system
### Generate an fstab file
```
genfstab -U /mnt >> /mnt/etc/fstab
```

#### (optional) Change `relatime` option to `noatime`
```/mnt/etc/fstab```
```
# /dev/mapper/vg-root
UUID=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx / ext4 rw,noatime 0 1

# /dev/mapper/vg-home
UUID=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx / ext4 rw,noatime 0 1
```

Reduces writes to disk when reading from a file, but may cause issues with programs that rely on file access time

### Enter new system chroot
```
arch-chroot /mnt
```

#### At this point you should have the following partitions and logical volumes:
```lsblk```

NAME           | MAJ:MIN | RM  |  SIZE  | RO  | TYPE  | MOUNTPOINT |
---------------|---------|-----|--------|-----|-------|------------|
nvme0n1        |  259:0  |  0  | 465.8G |  0  | disk  |            |
├─nvme0n1p1    |  259:4  |  0  |     1M |  0  | part  |            |
├─nvme0n1p2    |  259:5  |  0  |   550M |  0  | part  | /efi       |
├─nvme0n1p3    |  259:6  |  0  | 465.2G |  0  | part  |            |
..└─cryptlvm   |  254:0  |  0  | 465.2G |  0  | crypt |            |
....├─vg-swap  |  254:1  |  0  |     8G |  0  | lvm   | [SWAP]     |
....├─vg-root  |  254:2  |  0  |    32G |  0  | lvm   | /          |
....└─vg-home  |  254:3  |  0  | 425.2G |  0  | lvm   | /home      |

### Time zone
#### Set the time zone
Replace `America/Los_Angeles` with your respective timezone found in `/usr/share/zoneinfo`
```
ln -sf /usr/share/zoneinfo/America/Los_Angeles /etc/localtime
```

#### Run `hwclock` to generate ```/etc/adjtime```
Assumes hardware clock is set to UTC
```
hwclock --systohc
```

### Localization
#### Uncomment ```en_US.UTF-8 UTF-8``` in ```/etc/locale.gen``` and generate locale
```
locale-gen
```

#### Create ```locale.conf``` and set the ```LANG``` variable
```/etc/locale.conf```
```
LANG=en_US.UTF-8
```

### Network configuration
#### Create the hostname file
```/etc/hostname```
```
myhostname
```

This is a unique name for identifying your machine on a network.

#### Add matching entries to hosts
```/etc/hosts```
```
127.0.0.1 localhost
::1 localhost
127.0.1.1 myhostname.localdomain myhostname
```

### Initramfs
#### Add the ```keyboard```, ```encrypt```, and ```lvm2``` hooks to ```/etc/mkinitcpio.conf```
*Note:* ordering matters.
```
HOOKS=(base udev autodetect keyboard modconf block encrypt lvm2 filesystems fsck)
```

#### Recreate the initramfs image
```
mkinitcpio -p linux
```

### Root password
#### Set the root password
```
passwd
```

### Boot loader
#### Install GRUB
```
pacman -S grub
```

#### Configure GRUB to allow booting from /boot on a LUKS1 encrypted partition
```/etc/default/grub```
```
GRUB_ENABLE_CRYPTODISK=y
```

#### Set kernel parameter to unlock the LVM physical volume at boot using ```encrypt``` hook
##### UUID is the partition containing the LUKS container
```blkid```
```
/dev/nvme0n1p3: UUID="xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" TYPE="crypto_LUKS" PARTLABEL="Linux LUKS" PARTUUID="xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

```/etc/default/grub```
```
GRUB_CMDLINE_LINUX="... cryptdevice=UUID=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:cryptlvm root=/dev/vg/root ..."
```

#### Install GRUB to the mounted ESP for UEFI booting
```
pacman -S efibootmgr
grub-install --target=x86_64-efi --efi-directory=/efi
```

#### Enable microcode updates
##### grub-mkconfig will automatically detect microcode updates and configure appropriately
```
pacman -S intel-ucode
```

Use intel-ucode for Intel CPUs and amd-ucode for AMD CPUs.

#### Generate GRUB's configuration file
```
grub-mkconfig -o /boot/grub/grub.cfg
```

### (recommended) Embed a keyfile in initramfs

This is done to avoid having to enter the encryption passphrase twice (once for GRUB, once for initramfs.)

#### Create a keyfile and add it as LUKS key
```
mkdir /root/secrets && chmod 700 /root/secrets
head -c 64 /dev/urandom > /root/secrets/crypto_keyfile.bin && chmod 600 /root/secrets/crypto_keyfile.bin
cryptsetup -v luksAddKey -i 1 /dev/nvme0n1p3 /root/secrets/crypto_keyfile.bin
```

#### Add the keyfile to the initramfs image
```/etc/mkinitcpio.conf```
```
FILES=(/root/secrets/crypto_keyfile.bin)
```

#### Recreate the initramfs image
```
mkinitcpio -p linux
```

#### Set kernel parameters to unlock the LUKS partition with the keyfile using ```encrypt``` hook
```/etc/default/grub```
```
GRUB_CMDLINE_LINUX="... cryptkey=rootfs:/root/secrets/crypto_keyfile.bin"
```

#### Regenerate GRUB's configuration file
```
grub-mkconfig -o /boot/grub/grub.cfg
```

#### Restrict ```/boot``` permissions
```
chmod 700 /boot
```

The installation is now complete. Exit the chroot and reboot.
```
exit
reboot
```

## Post-installation
Your system should now be fully installed, bootable, and fully encrypted.

If you embedded the keyfile in the initramfs image, it should only require your encryption passphrase once to unlock to the system.

For the standard Arch Linux post-installation steps, [RTFM](https://wiki.archlinux.org/index.php/General_recommendations).

### (recommended) Hardening against Evil Maid attacks
With an encrypted boot partition, nobody can see or modify your kernel image or initramfs, but you would be still vulnerable to [Evil Maid](https://www.schneier.com/blog/archives/2009/10/evil_maid_attac.html) attacks.

One possible solution is to use UEFI Secure Boot. Get rid of preloaded Secure Boot keys (you really don't want to trust Microsoft and OEM), enroll [your own Secure Boot keys](https://wiki.archlinux.org/index.php/Secure_Boot#Using_your_own_keys) and sign the GRUB boot loader with your keys. Evil Maid would be unable to boot modified boot loader (not signed by your keys) and the attack is prevented.

#### Creating keys
The following steps should be performed as the `root` user, with accompanying files stored in the `/root` directory.

##### Install `efitools`
```
pacman -S efitools
```

##### Create a GUID for owner identification
```
uuidgen --random > GUID.txt
```

##### Platform key
CN is a Common Name, which can be written as anything.

```
openssl req -newkey rsa:4096 -nodes -keyout PK.key -new -x509 -sha256 -days 3650 -subj "/CN=my Platform Key/" -out PK.crt
openssl x509 -outform DER -in PK.crt -out PK.cer
cert-to-efi-sig-list -g "$(< GUID.txt)" PK.crt PK.esl
sign-efi-sig-list -g "$(< GUID.txt)" -k PK.key -c PK.crt PK PK.esl PK.auth
```

##### Sign an empty file to allow removing Platform Key when in "User Mode"
```
sign-efi-sig-list -g "$(< GUID.txt)" -c PK.crt -k PK.key PK /dev/null rm_PK.auth
```

##### Key Exchange Key
```
openssl req -newkey rsa:4096 -nodes -keyout KEK.key -new -x509 -sha256 -days 3650 -subj "/CN=my Key Exchange Key/" -out KEK.crt
openssl x509 -outform DER -in KEK.crt -out KEK.cer
cert-to-efi-sig-list -g "$(< GUID.txt)" KEK.crt KEK.esl
sign-efi-sig-list -g "$(< GUID.txt)" -k PK.key -c PK.crt KEK KEK.esl KEK.auth
```

##### Signature Database key
```
openssl req -newkey rsa:4096 -nodes -keyout db.key -new -x509 -sha256 -days 3650 -subj "/CN=my Signature Database key/" -out db.crt
openssl x509 -outform DER -in db.crt -out db.cer
cert-to-efi-sig-list -g "$(< GUID.txt)" db.crt db.esl
sign-efi-sig-list -g "$(< GUID.txt)" -k KEK.key -c KEK.crt db db.esl db.auth
```

#### Signing bootloader and kernel
When Secure Boot is active (i.e. in "User Mode") you will only be able to launch signed binaries, so you need to sign your kernel and boot loader.

Install `sbsigntools`
```
pacman -S sbsigntools
```
```
sbsign --key db.key --cert db.crt --output /boot/vmlinuz-linux /boot/vmlinuz-linux
sbsign --key db.key --cert db.crt --output /efi/EFI/arch/grubx64.efi /efi/EFI/arch/grubx64.efi
```

##### Automatically sign bootloader and kernel on install and updates
It is necessary to sign GRUB with your UEFI Secure Boot keys every time the system is updated via `pacman`. This can be accomplished with a [pacman hook](https://jlk.fjfi.cvut.cz/arch/manpages/man/alpm-hooks.5).

Create the hooks directory
```
mkdir -p /etc/pacman.d/hooks
```

Create hooks for both the `linux` and `grub` packages

```/etc/pacman.d/hooks/99-secureboot-linux.hook```
```
[Trigger]
Operation = Install
Operation = Upgrade
Type = Package
Target = linux

[Action]
Description = Signing Kernel for SecureBoot
When = PostTransaction
Exec = /usr/bin/find /boot/ -maxdepth 1 -name 'vmlinuz-*' -exec /usr/bin/sh -c 'if ! /usr/bin/sbverify --list {} 2>/dev/null | /usr/bin/grep -q "signature certificates"; then /usr/bin/sbsign --key /root/db.key --cert /root/db.crt --output {} {}; fi' \ ;
Depends = sbsigntools
Depends = findutils
Depends = grep
```

```/etc/pacman.d/hooks/98-secureboot-grub.hook```
```
[Trigger]
Operation = Install
Operation = Upgrade
Type = Package
Target = grub

[Action]
Description = Signing GRUB for SecureBoot
When = PostTransaction
Exec = /usr/bin/find /efi/ -name 'grubx64*' -exec /usr/bin/sh -c 'if ! /usr/bin/sbverify --list {} 2>/dev/null | /usr/bin/grep -q "signature certificates"; then /usr/bin/sbsign --key /root/db.key --cert /root/db.crt --output {} {}; fi' \ ;
Depends = sbsigntools
Depends = findutils
Depends = grep
```

#### Enroll keys in firmware
##### Copy all `*.cer`, `*.esl`, `*.auth` to the EFI system partition
```
cp /root/*.cer /root/*.esl /root/*.auth /efi/
```

##### Boot into UEFI firmware setup utility (frequently but incorrectly referred to as "BIOS")
```
systemctl reboot --firmware
```

Firmwares have various different interfaces, see [Replacing Keys Using Your Firmware's Setup Utility](http://www.rodsbooks.com/efi-bootloaders/controlling-sb.html#setuputil) if the following instructions are unclear or unsuccessful.

##### Set OS Type to `Windows UEFI mode`
Find the Secure Boot options and set OS Type to `Windows UEFI mode` (yes, even if we're not on Windows.) This may be necessary for Secure Boot to function.

##### Clear preloaded Secure Boot keys


Using Key Management, clear all preloaded Secure Boot keys (Microsoft and OEM).

By clearing all Secure Boot keys, you will enter into Setup Mode (so you can enroll your own Secure Boot keys).

##### Set or append the new keys
The keys must be set in the following order:

```db => KEK => PK```

This is due to some systems exiting setup mode as soon as a `PK` is entered.

Do not load the factory defaults, instead navigate the available filesystems in search of the files previously copied to the EFI System partition.

Choose any of the formats. The firmware should prompt you to enter the type (*Note:* type names may differ slightly.)
```
*.cer is a Public Key Certificate
*.esl is a UEFI Secure Variable
*.auth is an Authenticated Variable
```

Certain firmware (such as my own) require you use the *.auth files. Try various ones until they work.

##### Set UEFI supervisor (administrator) password
You must also set your UEFI firmware supervisor (administrator) password in the Security settings, so nobody can simply boot into UEFI setup utility and turn off Secure Boot.

You should never use the same UEFI firmware supervisor password as your encryption password, because on some old laptops, the supervisor password could be recovered as plaintext from the EEPROM chip.

##### Exit and save changes
Once you've loaded all three keys and set your supervisor password, hit F10 to exit and save your changes.

If everything was done properly, your boot loader should appear on reboot.

#### Check if Secure Boot was enabled
```
od -An -t u1 /sys/firmware/efi/efivars/SecureBoot-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
```
The characters denoted by XXXX differ from machine to machine. To help with this, you can use tab completion or list the EFI variables.

If Secure Boot is enabled, this command returns 1 as the final integer in a list of five, for example:

```6  0  0  0  1```

If Secure Boot was enabled and your UEFI supervisor password set, you may now consider yourself protected against Evil Maid attacks.
