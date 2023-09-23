# Debian hardening guide
Seeing a general lack of guides which assist people in hardening Debian desktop installs, or even broadly Linux installs, I decided to write this guide. This is intended to still result in a practical, usable experience. Work in progress.

# Physical security
## Encrypted boot
When choosing to install Debian, make sure you install using the encrypted install option. This will *not* encrypt the /boot partition. Generally speaking, an unencrypted boot is not a problem if your system supports Secure Boot. If it doesn't, [here are the instructions on how to set it up](https://cryptsetup-team.pages.debian.net/cryptsetup/encrypted-boot.html). You do end up with the requirement to input your password *three times*: first for the /boot partition, second for the /root partition and a third time to decrypt the /boot partition. [It is possible to avoid this with keyfiles](https://bbs.archlinux.org/viewtopic.php?pid=1669615#p1669615).
## UEFI settings
Enable Secure Boot and set a password for your UEFI/BIOS. If your UEFI supports requiring a password to use the boot menu, enable such an option. Secure Boot makes offline tampering with your install difficult, especially with the kernel, initramfs. Debian is supported by Secure Boot and components are signed with Microsoft's third party key. If possible, it may be useful to remove all other keys that your UEFI supports. However, since some firmware on your computer might be signed by Microsoft, or by your OEM, you might end up with an unbootable system. Only do this if you know what you're doing.


# Sources
- [1] [Security - ArchWiki](https://wiki.archlinux.org/title/Security)
- 
