# linstaller
A minimalistic universal easy-to-implement linux installer

# Why?

Current Linux installers ended up absorbing out-of-scope functions, which made them complex and difficult to debug, they also eventually adopted the logic of formatting, extracting the image, configuring and only then installing the boot loader, meanwhile allows a reliable installation, this can result in an incomplete installation if for some reason some configuration such as generating locales, or cleaning the package manager fails, this results in an unbootable system, `linstaller` tries to maintain reliability however focusing on providing an uncivilized system instead of a pre-configured one, with the exception of the user and the hostname, all configurations are done by a post-installation script, ensuring that the system in minimal conditions can boot after installation

# Usage

You will need to setup a disk, `linstaller` don't do this but provide a utility to quick setup a full-disk installation, once time installed `linstaller` on `/usr/bin` you shold be able (as root) to:

```bash 
linstaller-fulldisk-setup --filesystem=ext4 /dev/sda
```

This will **erase all partitions and data** on `/dev/sda` create a 512M partition for EFI as FAT32 (even on MBR), create a 2nd partition with all remaining disk size and format as `ext4`

> **Tip:** You can format system target partition with any of `mkfs` supported format, you can list the supported with `ls /usr/sbin | grep "mkfs\." | cut -d\. -f2`

