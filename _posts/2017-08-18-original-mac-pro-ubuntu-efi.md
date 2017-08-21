---
layout: post
published: true
title: "My Original Mac Pro Running Ubuntu 16.04 With EFI Booting"
author: Varun
image: /img/macpro.jpg
categories: Technology
galleries:
  my-macpro:
    pictures:
      - url: "my-macpro.jpg"
        caption: "11 years old and still a workhorse. I don't know how I'll ever replace it."
---
Yes, people think I'm weird for not letting my main computer go, but I'm just not ready to do that yet.

{% include gallery.html gallery_id="my-macpro" %}

I am very attached to my 2006 Mac Pro. It was the last thing I bought with my Apple employee discount, and with a few upgrades, it's a solid 11 year-old workhorse. Just a summary of what is in this thing now:

* Dual processor, quad-core Xeon X5355s @ 2.66GHz
* ATI Radeon 5770 with 2GB VRAM
* 13GB FB ECC RAM
* 256GB SSD, and several hard disks

With the power it has, it should have a long life ahead of it. **Apple doesn't agree with me, however.** The highest OS version officially supported is Mac OS X 10.7.5; 10.7 was released in 2011. Unofficially I can get Mavericks 10.9 working, which was released in 2013. Many applications, developer tools, browsers, and games don't work properly on 10.9. And since I'm looking to start working with FPGAs, Linux seemed like a good idea. Ubuntu 16.04 is specifically a supported OS for Xilinx design tools.

#### A Unique Challenge

The 2006 and 2008 Mac Pro are unusual. They have 64-bit processors and data buses, but the EFI (firmware) is 32-bit. That is probably why Apple stopped supporting them so soon after their release. It also means we will have to do some stuff to get Linux working. The goal is to install Linux onto its own hard drive in the Mac Pro.

#### The Strategy

1. Prepare 64-bit Ubuntu installer with 32-bit EFI and special GRUB bootloader params.
2. Install Ubuntu onto its own disk with an EFI partition.
3. Edit the installed GRUB config to include the necessary options.

#### The Steps

1. Set up a USB or CD with [Super GRUB 2](http://www.supergrubdisk.org/category/download/supergrub2diskdownload/super-grub2-disk-stable/). Just in case you need it later.
1. Download the 32-bit EFI GRUB tarball from [Christopher Smart's Blog](https://blog.christophersmart.com/2009/12/22/updated-efi-grub2-tarball-including-64bit/).
2. Set up the tarball on a USB stick according to the instructions.
3. *Delete the bootx64.efi from the USB stick.*
4. Download netboot `initrd.gz` and `linux` for [Ubuntu 16.04](http://archive.ubuntu.com/ubuntu/dists/xenial-updates/main/installer-amd64/current/images/hwe-netboot/ubuntu-installer/amd64/) or your preferred release.
5. Rename them to something like `initrd-xenial64.gz` and `linux-xenial64` for future reference.
6. Copy files to /efi/boot/ on your new USB install drive.
7. Edit “grub.cfg” and add the following lines:
    {% highlight bash %}
    menuentry "Install Xenial 64bit" {
      fakebios
      linux /efi/boot/linux-xenial64 priority=low vga=normal video=efifb noefi
      initrd /efi/boot/initrd-xenial64.gz
    } {% endhighlight %}
{:start="9"}
8. Boot installer USB on Mac, select Xenial from GRUB, and install Linux on your drive. Make sure it creates a UEFI boot partition to store the kernel and GRUB.
9. **Once installation is complete, your Mac will probably not start Linux.**
10. Restart your Mac, and select your Linux partition. It should show up as `EFI Boot`.
11. Tap the shift key repeatedly until GRUB comes up. Highlight the default Ubuntu option in the menu, and press `e`.
12. Scroll down to the default Ubuntu menu entry. Like we did in step 8, edit the entry to include `fakebios`. Also remove the `ro` and `splash` from the `linux` line, and add `noefi`.
13. Press the key to continue booting.
14. **Now Linux has booted, but we need to make our changes permanent.**
15. `sudoedit /boot/efi/grub/grub.cfg` to include the same exact changes we made in step 12.

#### Advantages

1. All of my drives are bootable from GRUB.
2. Power management seems to work properly during all phases of startup and operation.
3. No odd MBR setups, or *(shudder)* hybrid MBR/GPT partitioning schemes.

#### Disadvantages

1. For now we are telling the linux kernel `noefi`, which is not ideal. There is probably a way to address this, but I haven't figured it out yet.
2. Any time you install kernel updates through Ubuntu, you'll have to edit `/boot/efi/grub/grub.cfg` to redo your changes. If you forget to do this before restarting, just use the steps starting at step 11, or use a repair/live CD.
3. My graphics card does not produce output until the Ubuntu login screen. I'm pretty sure that's because it isn't properly EBC flashed for Macs, and it's something I'll fix eventually.

#### Acknowledgements

* [Christopher Smart's post](https://blog.christophersmart.com/2009/12/22/updated-efi-grub2-tarball-including-64bit/), where he figured a lot out and made many files available himself.
* [Serge M's post](http://blog.sergem.net/how-to-install-ubuntu-14-04-on-macpro-11-efi-boot-mode/) which outlined many of the basic steps. For my own setup I had to make some additional alterations to get a stable computing environment.

##### Who's Varun?

_I most recently was the founder of an HR tech startup, [Disqovery](http://disqovery.com). I have worn many hats, and I like making things. I also like talking business. You can reach me at [smartperson@gmail.com](mailto:smartperson@gmail.com), [@smartperson](https://twitter.com/smartperson), [Github](https://github.com/smartperson), and [LinkedIn](https://linkedin.com/in/varunkmehta)._
