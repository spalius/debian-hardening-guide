# Debian hardening guide
Seeing a general lack of guides which assist people in hardening Debian desktop installs, or even broadly Linux installs, I decided to write this guide. This is intended to still result in a practical, usable experience. Not every possible hardening measure is documented to due my own lack of knowledge. Work in progress.

# Physical security
## Encrypted boot
When choosing to install Debian, make sure you install using the encrypted install option. This will *not* encrypt the /boot partition. Generally speaking, an unencrypted boot is not a problem if your system supports Secure Boot. If it doesn't, [here are the instructions on how to set it up](https://cryptsetup-team.pages.debian.net/cryptsetup/encrypted-boot.html). You do end up with the requirement to input your password *three times*: first for the /boot partition, second for the /root partition and a third time to decrypt the /boot partition. [It is possible to avoid this with keyfiles](https://bbs.archlinux.org/viewtopic.php?pid=1669615#p1669615).
## UEFI settings
Enable Secure Boot and set a password for your UEFI/BIOS. If your UEFI supports requiring a password to use the boot menu, enable such an option. Secure Boot makes offline tampering with your install difficult, especially with the kernel, initramfs. Debian is supported by Secure Boot and components are signed with Microsoft's third party key. If possible, it may be useful to remove all other keys that your UEFI supports. However, since some firmware on your computer might be signed by Microsoft, or by your OEM, you might end up with an unbootable system. Only do this if you know what you're doing.

Secure Boot can be a problem if you use out of tree kernel drivers. To automatically sign kernel drivers, you need to enroll your own Machine Owner Key (MOK), import it, or import the DKMS MOK. [Detailed instructions are available on the Debian wiki](https://wiki.debian.org/SecureBoot). Most UEFIs should support importing your own MOK. For ease of use, [importing the DKMS MOK](https://wiki.debian.org/SecureBoot#Making_DKMS_modules_signing_by_DKMS_signing_key_usable_with_the_secure_boot) is recommended.

# Sandboxing and Mandatory Access Control
Sandboxing can work very well as a vulnerability mitigation error. Even if your browser is compromised, assuming your sandboxing software isn't, it won't be able to access all your files on your disk or do other damage. Running untrusted code is never safe, even if it is sandboxed. Mandatory Access Control is important as well.

## AppArmor
[SELinux](https://selinuxproject.org/page/Main_Page) and [AppArmor](https://wiki.apparmor.net/) are both slightly different MAC implementations. Due to the greater ease of use of AppArmor, as well as it being pre-installed on a normal Debian desktop install, this guide will only explain how to set up AppArmor.
To view the status of AppArmor, execute ```sudo aa-status```.

By default, [Debian has AppArmor preinstalled](https://wiki.debian.org/AppArmor/HowToUse), but doesn't have profiles applied to more software. To install more profiles, execute ``` sudo apt install apparmor-profiles apparmor-profiles-extra```.

## Firejail
[Firejail](https://firejail.wordpress.com/) is a sandboxing utility meant for desktop applications. It is one of many on Linux: Bubblewrap is a common alternative. Bubblewrap by itself is not very usable and Bubblejail is not shipped with Debian 12. Firejail has a large amount of profiles pre-installed and will generally work out of the box.

To install Firejail, execute ```sudo apt install firejail```.

[In order to avoid privilege escalation vulnerabilities](https://firejail.wordpress.com/documentation-2/basic-usage/#suid), edit ```/etc/firejail/firejail.config``` to include ```force-nonewprivs yes```. This may break Chromium browsers.

Execute ```sudo firecfg``` to integrate Firejail with your desktop environment. Launching applications through your desktop environment will launch them with Firejail. Right click menus in your file browser should also work with this, when launching applications.

If an application doesn't work with Firejail properly, you can disable firecfg from integrating it with Firejail. Comment out, using #, an entry in ```/etc/firejail/firecfg.config``` and run firecfg again.

In order to disable network access for a specific application, use --net=none. For example, ```firejail vlc --net=none```. This may be useful for applications which don't need network access to work.

To enable AppArmor for Firejail, do ```sudo apparmor_parser -r /etc/apparmor.d/firejail-default```.

# Wayland
[Keyloggers on Xorg are a big issue](https://theinvisiblethings.blogspot.com/2011/04/linux-security-circus-on-gui-isolation.html) Sandboxing can be rendered useless if any application can become a keylogger. Use Wayland with every single application that supports it. Firefox ESR, for some strange reason, doesn't run on Wayland by default on Debian 12, even if you are on a Wayland session. Launch Firefox with this environment variable: ```MOZ_ENABLE_WAYLAND=1```. To make this permanent, edit /usr/bin/firefox to include, in the exec line, ```env MOZ_ENABLE_WAYLAND=1``` before ```firefox-esr```. This may get overriden on updates. You can use a GUI method to add the environment variable for your desktop environment's application menu.

# Hardened memory allocation
[Hardened_malloc](https://github.com/GrapheneOS/hardened_malloc) was originally a project created for Android, which is implemented most heavily, to my knowledge, on GrapheneOS. Currently, Debian doesn't ship it, however, we can build it from source and use it with Firejail. It is recommended to read the documentation carefully.

Download the most current release and open the folder in your terminal. Use ```make``` to build it. In order to install hardened_malloc, copy libhardened_malloc.so in the /out folder to ```/usr/local/lib/```. You can use it with Firejail. This may break some applications and generally speaking, your performance will be decreased. For applications which require performance, don't use hardened_malloc.

# Sources
- [1] [Security - ArchWiki](https://wiki.archlinux.org/title/Security)
- 
