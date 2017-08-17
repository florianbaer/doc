**DISCLAIMER: These are my personal notes for experiments with my own laptop. These instructions could cause data loss, damage to your system, or even your system becoming unusable. Use at your own risk.**

These are my notes to get Ubuntu working on a Dell XPS 15 (9560, early 2017). 

I originally started with [Ubuntu 16.04 LTS](http://releases.ubuntu.com/16.04/). Then I upgraded to [Ubuntu 16.10](http://releases.ubuntu.com/16.10/), and recently to [Ubuntu 17.04](http://releases.ubuntu.com/17.04/).

At this point, I have 
* Ubuntu running
* encrypted home directory in Ubuntu
* encrypted Windows partition
* dual boot with Windows 10 Pro (it works fine)
* Suspend/resume work fine with the nVidia 381 driver
* I can also run the Windows 10 Pro (guest) from Ubuntu (host) as a virtual machine, at full resolution.
  * That is, I can choose whether I want to boot Windows natively, or as a virtual machine from Ubuntu. 
  * I also have a shared folder so that Windows (as virtual machine) can access my linux encrypted home directory. This way, all the data and documents can live on the Ubuntu partition and I don't need to allocate extra space for data in the Windows partition.

However, there are a couple of small [outstanding problems](#outstanding-problems):
* trying to run Office alternatively natively and on the virtual machine (you have to commit to either one)
* drag'n'drop from Windows to Ubuntu
* Sometimes, [bluetooth dies after suspend/resume](#bluetooth-stops-working-after-suspendresume) (possible fix below)
* Sometimes [the dual boot disappears and the laptop will boot Windows directly](#windows-update-removes-your-dual-boot) (but see below for easy fix).


# Table of Contents

   * [Table of Contents](#table-of-contents)
   * [Installation steps](#installation-steps)
      * [Prep work with Windows](#prep-work-with-windows)
      * [Installing Ubuntu](#installing-ubuntu)
      * [Upgrading things](#upgrading-things)
         * [Graphics card driver](#graphics-card-driver)
      * [Fixing problems](#fixing-problems)
         * [Touchpad](#touchpad)
         * [Windows update removes your dual boot](#windows-update-removes-your-dual-boot)
      * [Run your native Windows 10 Pro install as guest on VirtualBox with Ubuntu as host](#run-your-native-windows-10-pro-install-as-guest-on-virtualbox-with-ubuntu-as-host)
         * [Prep work](#prep-work)
         * [Letting VirtualBox boot the native Windows 10 Pro installation as a virtual machine](#letting-virtualbox-boot-the-native-windows-10-pro-installation-as-a-virtual-machine)
         * [Enabling full resolution for the guest Windows 10 Pro virtual machine](#enabling-full-resolution-for-the-guest-windows-10-pro-virtual-machine)
         * [Bidirectional interaction between Ubuntu host and Windows guest](#bidirectional-interaction-between-ubuntu-host-and-windows-guest)
         * [Shared folders](#shared-folders)
   * [Outstanding problems](#outstanding-problems)
      * [Office detects hardware change when booting natively/virtual machine](#office-detects-hardware-change-when-booting-nativelyvirtual-machine)
      * [Drag'n'Drop from Windows to Linux not working](#dragndrop-from-windows-to-linux-not-working)
      * [Bluetooth stops working after suspend/resume](#bluetooth-stops-working-after-suspendresume)
   * [References](#references)

Created by [gh-md-toc](https://github.com/ekalinin/github-markdown-toc)

# Laptop specs

* 15.6-inch (3840 x 2160) 4K 282ppi IPS InfinityEdge LCD glossy
* 2.8GHz Intel Core i7-7700HQ (3.8GHz boost) 4 cores, 8 threads
* Windows 10 Home 64-bit
* Nvidia GeForce GTX 1050 GPU Mobile with 4GB GDDR5 RAM
* 16GB 2400MHz DDR4 RAM (Up to 32GB)
* 512GB PCIe SSD Lite-On CX2-8B512-Q11 NV (Up to 1TB SSD)
* WiFi Qualcomm Atheros 802.11ac single-band 2x2 MIMO
* Bluetooth 4.1:
 * `lsusb` reports: Qualcomm Atheros
 * Invoice reports: Killer 1535
* 1x USB-C 3.1
* 2x USB 3.0
* HDMI
* Kensington security slot
* SDXC card slot
* Stereo speakers
* HD webcam
* Single mic
* 3.5mm headset jack
* UK tiled keyboard
* Two-button trackpad
* 97 WHr, 6-cell lithium-ion battery, removable (design capacity 8509 mAh)
* 357mm x 235mm x 17mm
* 2kg

# Installation steps

## Prep work with Windows

1. Plug the power cord into the laptop

1. Start laptop and go through the [Windows 10 set up process](http://www.dell.com/support/manuals/us/en/19/xps-15-9560-laptop/XPS-15-9560-SetupAndSpecifications/Set-up-your-computer?guid=GUID-F6379111-C2C3-4CBB-8CD6-ABE298AB0ED9&lang=en-us)

1. Create a [Windows recovery USB stick](http://www.dell.com/support/home/us/en/19/Drivers/OSISO/WT64A) so that we can reset the laptop to factory settings if things go wrong.

1. Go to the [XPS 15 9560 drivers](http://www.dell.com/support/home/uk/en/ukdhs1/product-support/product/xps-15-9560-laptop/drivers) webpage and update the BIOS (version 1.1.3 at the time of this writing)

1. Other driver updates
   * Graphics card: nVidia n17P-G0

1. Change SATA Operation so that Ubuntu can see the SSD

   1. Login into Windows 10, and set up Safe Mode: "Change advanced Startup Options" -> "Restart Now" -> "Troubleshoot" > "Advanced options" > "Startup Settings" > "Restart"

   1. Boot laptop and press F12 -> BIOS Setup

   1. System Configuration -> SATA Operation

   1. Change SATA Operation from "RAID On" to "AHCI"

   1. Apply and Exit. This will reboot the laptop, and provide several boot options

   1. Choose "4. Safe Mode"

   1. "Start" -> "Windows System" -> "Control Panel" -> "Device Manager". Check that the "IDE ATA/ATAPI controller" is "Intel(R) 100 Series/C230 Chipset Family SATA AHCI Controller"

   1. Reboot normally. This should boot into the regular Window 10

   1. If you reboot and go into the BIOS, SATA Operation should still be AHCI

## Installing Ubuntu

1. Create a bootable USB stick with Ubuntu Live (currently 16.04 LTS). You can do it from [Windows](https://www.ubuntu.com/download/desktop/create-a-usb-stick-on-windows) or [Linux](https://www.ubuntu.com/download/desktop/create-a-usb-stick-on-ubuntu)

1. Boot the laptop with the USB stick (you need to press F12 while booting, and then select the USB stick from the BIOS menu)

1. Launch Ubuntu Live with "Try Ubuntu without installing"

1. Click on "Install Ubuntu 16.04.1 LTS" to start installation

1. Select language (e.g. English)

1. Select whether you want to connect to Wifi (in my case I couldn't, because it doesn't give you the option to enter the Wifi username)

1. Select "Install third-party software for graphic and Wi-Fi hardware, Flash, MP3 and other media"

1. Check "Turn off Secure Boot" and enter a password that you'll need later

1. In "Installation Type", it should have detected the existing Windows installation, and offer to install Ubuntu alongside (dual boot).

1. Now you'll see the partitions in your drive. Select the main one, that contains Windows, and reduce it to make space for Ubuntu. For example, I reduced the Windows space to 50 GB, and left the rest for Ubuntu.

1. The [rest of the steps](https://www.ubuntu.com/download/desktop/install-ubuntu-desktop) (time zone, keyboard layout, login name and password) is straightforward, just complete them.

1. After the system installs, you will have dual boot. Boot into Ubuntu, login and now you can upgrade to 16.10 (you don't have to, if you want to have LTS support, but I did).

       sudo do-release-upgrade

## Upgrading things

### Graphics card driver

By default, Ubuntu uses the noveau driver. You can switch to the nVidia driver by launching "Additional drivers" from the dash, waiting for GUI to search for proprietary drivers, and then clicking on the nVidia driver, or using the command line. For example, to install v375 of the nVidia driver,

    sudo apt-get install nvidia-375

This will ask you for permission to deactivate Safe Booting, as it's a third-party driver. You'll have to provide a password, the system will reboot, boot up into a configuration mode that will ask whether you want to deactivate Safe Booting, and ask for said password.

To avoid corruption of the window manager after resuming from suspend, you need v381 of the driver. This is not in the regular repository of Ubuntu 17.04 at the time of this writing. To install the latest versions of the nVidia drivers, first you have to add the graphics team repositories

    sudo add-apt-repository ppa:graphics-drivers/ppa
    sudo apt-get update

Now launching "Additional drivers" from the dash will provide more options, in particular v381.09. Select and click "Apply changes".

The nVidia driver seems to work fine, and provides GPU acceleration. It also solves the window manager corruption after resuming from suspend caused by previous versions of the driver.

## Fixing problems

### Touchpad

Moving your hand near the touchpad can cause the mouse pointer jump to the bottom left corner of the desktop and open the Trash. Sometimes, it also makes your cursor jump back so that you type in the middle of the sentence. To fix this problem, [Juan Hernández suggests](https://bugs.launchpad.net/ubuntu/+source/xorg/+bug/1574667/comments/32) replacing `xserver-xorg-input-synaptics` with `xserver-xorg-input-libinput`

    sudo apt-get install xserver-xorg-input-libinput
    sudo apt-get remove --purge xserver-xorg-input-synaptics
    sudo reboot

### Windows update removes your dual boot

After updating Windows, or installing a program, you may lose the dual boot (i.e. Windows boots without giving you the option to choose between Ubuntu or Windows). This is very easy to fix.

1. Reboot the machine.

1. Press F12 when the Dell logo appears. Apart from the BIOS options, at the top you'll be given the option to boot in Windows or Ubuntu. Select Ubuntu.

1. Log in, and run in a terminal

    sudo grub-install

This will recover the dual boot.

## Run your native Windows 10 Pro install as guest on VirtualBox with Ubuntu as host

**DISCLAIMER: The instructions in this section are particularly dangerous, and can corrupt or destroy your system. If you use these instructions, it's really at your own risk.**

Usually, in VirtualBox you create a virtual disk where you install your guest operating system (e.g. Windows 10 Pro). Thus, Windows, all its programs and data are all inside a large file somewhere within your linux install. For example, `~/VirtualBox_VMs/win10pro.vdi`.

However, here we want to do something different. Instead of creating a virtual disk, we want to run the already installed *native* Windows 10 Pro from Ubuntu.

Basically, we are going to install VirtualBox in Ubuntu and follow GreenAsh's [Running a real Windows install in VirtualBox on Linux](http://greenash.net.au/thoughts/2016/02/running-a-real-windows-install-in-virtualbox-on-linux/) instructions, slightly modified and with a couple of extra steps at the end.

### Prep work

First, we want to install VirtualBox. Ubuntu provides a package `virtualbox` (5.1.6 at the time of this writing), but we want the latest version (5.1.18 at the time of this writing). For that, we are going to get the deb package `virtualbox-5.1` directly from the VirtualBox repository.

The reason is that the Guest Additions silently fail to work with the default Ubuntu 16.10 version, thus preventing us from resizing the Windows virtual machine to full resolution and full screen.

1. From the Ubuntu dash, "Software & Updates" -> "Other software" -> "Add". Following the [VirtualBox linux downloads instructions](https://www.virtualbox.org/wiki/Linux_Downloads), we add the deb source

       deb http://download.virtualbox.org/virtualbox/debian yakkety contrib

1. Download and register the keys for the repository

       wget -q https://www.virtualbox.org/download/oracle_vbox_2016.asc -O- | sudo apt-key add -
       wget -q https://www.virtualbox.org/download/oracle_vbox.asc -O- | sudo apt-key add -

1. Refresh the list of packages, and install the latest version of VirtualBox and the [Oracle VM VirtualBox Extension Pack](https://www.virtualbox.org/wiki/Downloads) so that we can mount USB 2.0 and 3.0 devices, and other extended functionality.

       sudo apt-get update
       sudo apt-get install virtualbox-5.1 virtualbox-ext-pack

1. **Troubleshooting:** If you had `virtualbox` installed, and have replaced it by `virtualbox-5.1`, when you try to start an existing virtual machine, you are going to get an error message saying that the VirtualBox kernel modules don't match the VirtualBox version. All you have to do is [remove the old modules and rebuild them](http://askubuntu.com/a/873471)

       sudo rmmod vboxnetadp vboxnetflt vboxpci vboxdrv
       sudo /sbin/rcvboxdrv setup

1. Because `virtualbox-ext-pack` is the extension pack of the older 5.1.6 VirtualBox, when you launch VirtualBox, you may be asked whether you want to upgrade the Extension pack to 5.1.18. If so, accept and follow the upgrade process.

1. Add yourself as a VirtualBox user and to the `disk` group by running on a terminal

       sudo adduser $USER vboxusers
       sudo adduser $USER disk

1. Log out of Ubuntu and log in again for the changes to take effect.

Now, some basic set up to prepare VirtualBox.

1. Reboot, and press F12 during the Dell logo to enter the BIOS configuration.

1. Check that you have Secure Boot -> Secure Boot Enable -> Disabled.

1. Save and exit, and boot into Ubuntu.

1. Launch VirtualBox (you shouldn't have any virtual machines here), and go to File -> Preferences. Rename the default machine folder from `/home/$USER/VirtualBox VMs` to `/home/$USER/VirtualBox_VMs`. Rename the `/home/$USER/VirtualBox VMs` folder on your filesystem too, if it already exists. The reason for this is that whitespaces cause problems if you forget quotes, etc.

### Letting VirtualBox boot the native Windows 10 Pro installation as a virtual machine

Now we are getting serious. This is the bit where we basically follow GreenAsh's [Running a real Windows install in VirtualBox on Linux](http://greenash.net.au/thoughts/2016/02/running-a-real-windows-install-in-virtualbox-on-linux/) instructions

1. Install gparted

       sudo apt-get install gparted

1. Run gparted and write down the partitions used by Windows. In my case, it was partitions `/dev/nvme0n1p1` (`/boot/efi`), `/dev/nvme0n1p2` (Microsoft reserved partition), `/dev/nvme0n1p3` (OS / "Windows C drive"), `/dev/nvme0n1p4` (WINRETOOLS), `/dev/nvme0n1p5` (Image) and `/dev/nvme0n1p6` (DELLSUPPORT).

1. Create the "raw disk", which is a file with a pointer to the actual Windows partitions. In the example above, Windows is using partitions 1-6.

       sudo VBoxManage internalcommands createrawvmdk \
       -filename "/home/rcasero/VirtualBox_VMs/win10pro.vmdk" \
       -rawdisk /dev/nvme0n1 \
       -partitions 1,2,3,4,5,6

1. Change the owner and group of the pointer files, so that you can use them as a user and not superuser (otherwise, VirtualBox won't be able to use these files)

       sudo chown $USER:$USER VirtualBox_VMs/win10pro*

After creating that pointer to the actual Windows partitions, we create a virtual machine for Windows 10 Pro in VirtualBox.

1. In VirtualBox, click on "New"

1. Set Name = "Windows_10_Pro", Type = "Microsoft Windows", Version = "Windows 10 (64-bit)".

1. Set Memory Size, e.g. 4096 B = 4 GB.

1. In "Hard drive", select "Use an existing virtual hard disk file", and select `/home/$USER/VirtualBox_VMs/win10pro.vmdk`.

1. Click "Create".

1. In the "Oracle VM VirtualBox Manager" you will see your new "Windows 10 Pro" virtual machine. This virtual machine is "Powered Off". 

If you try to boot now, you'll get a "FATAL! No bootable medium found" error. So we need a couple of final tweaks.

1. In VirtualBox, select the new Windows 10 Pro virtual machine, Settings -> Storage. Select `win10pro.vmdk` under `Controller: SATA` and check "Solid-state drive", as the Dell XPS 15 comes with an SSD.

1. Now select "System", and check "Enable EFI (special OSes only)".

### Enabling full resolution for the guest Windows 10 Pro virtual machine

We are going to enable graphics acceleration and provide enough video memory for full resolution.

1. In VirtualBox, select the Windows 10 Pro virtual machine, "Settings" -> "Display", and check "Enable 3D acceleration" and "Enable 2D video acceleration".

1. Increase video memory to 256 MB.

At this point, if you click "Start", the virtual machine will launch and boot your Windows 10 installation. But Windows 10 runs in a small size. To be able to increase the resolution, we need the Guest Additions.

1. Start the Windows 10 virtual machine.

1. On the virtual machine menu bar, "Devices" -> "Insert Guest Additions CD Image".

1. A dialogue will tell you that no CD is present and whether you wish to download from the internet. Click "Download", and then "Insert". This *doesn't* install the guest additions, only mounts the ISO as a CD.

1. Log into Windows. Open the File Explorer, "This PC" -> "CD Drive (D:)". Double-click on `VBoxWindowsAdditions.exe`. Follow the installation process, that at the end will ask to reboot Windows.

1. Shut down the virtual machine and close VirtualBox. From a terminal create a custom video mode (1920x1080 is the screen resolution, and 24 is the colour-depth in bits)

       VBoxManage setextradata "Windows_10_Pro" "CustomVideoMode1" "1920x1080x24"

Now we can use the laptop's screen at full specs for the Windows 10 virtual machine. This only needs to be done once, as afterwards every time we start the virtual machine, it'll remember the settings.

1. Launch VirtualBox and start the Windows 10 virtual machine.

1. In the menu, select "View" -> "Virtual Screen 1" -> "Resize to 1920x1080".

1. "View" -> "Full screen mode".

With this settings, the whole screen will display a Windows 10 Pro session, indistinguishable from running Windows natively, except for a thin gray line at the bottom that corresponds to a VirtualBox pull-up menu.

### Bidirectional interaction between Ubuntu host and Windows guest

1. Launch VirtualBox with the virtual machine shut down.

1. Go to "Settings" -> "General" -> "Advanced". 

1. Select "Bidirectional" for "Shared Clipboard". This enables copying something in Ubuntu and pasting to Windows, or vice versa.

1. Select "Bidirectional" for "Drag'n'Drop". This enables dragging a file from the Ubuntu file manager, and drop it in Windows, making a copy. For me, this works from Ubuntu to Windows, but not the other way around.

### Shared folders

1. With the virtual machine shut down, open "Settings" -> "Shared folders".

1. Click on "Add new shared folders", and point it to your linux home directory. "Folder path" = `/home/$USER`, "Folder name" = `linux_$USER`, where `$USER` is your linux username, , "Read-only" = No, "Auto-mount" = Yes.

1. When trying to start the virtual machine, I got the error

       Kernel driver not installed (rc=-1908)
       
       The VirtualBox Linux kernel driver (vboxdrv) is either not loaded or there is a permission problem with /dev/vboxdrv. Please reinstall the kernel module by executing
       
       '/sbin/vboxconfig'
       
       as root.
       
       where: suplibOsInit what: 3 VERR_VM_DRIVER_NOT_INSTALLED (-1908) - The support driver is not installed. On linux, open returned ENOENT.

1. Running the suggested command fixed the error

       sudo /sbin/vboxconfig

1. Start the Windows 10 Pro virtual machine. In File Explorer now you have a `linux_$USER` folder assigned to unit `E:\`. This way, Windows can access the Ubuntu partition, so that you can store your data and documents there.

## Encrypting Windows partition

1. Launch Windows from VirtualBox in Ubuntu.

1. Search for "BitLocker" in the Start menu, and click in "Turn on BitLocker". You'll get the error

       This device can’t use a Trusted Platform Module. Your administrator must set the ‘Allow BitLocker without a compatible TPM’ option in the ‘Require additional authentication at startup’ policy for OS volumes.

1. [Enable BitLocker encryption without a TPM chip](http://www.digitalcitizen.life/how-enable-bitlocker-without-tpm-chip-windows-7-windows-8?page=1)
   1. Go to the search box on the taskbar and type “group ” or “group policy ”. Then, open the “Edit group policy" search result.
   1. Go to Computer Configuration -> Administrative Templates -> Windows Components -> BitLocker Drive Encryption -> Operating System Drives.
   1. Open "Require additional authentication at startup".
   1. Change the value of the policy to "Enabled", and check "Allow BitLocker without a compatible TPM".
   1. Click OK, and close the policy editor.

1. Go back to BitLocker, and now you should be allowed to "Turn on BitLocker".

1. Click through several dialogues. 
   1. Check that partition "C:" is going to be encrypted. You don't want to encrypt something else by mistake.
   1. Create the private key and save it somewhere safe (whether you want to upload it to Microsoft is up to you). If you lose it, you won't be able to recover anything in "C:" if something goes wrong.
   1. Choose whether you want the fast or complete encryption.
   1. Click OK. Windows will need to restart to encrypt the partition.

# Outstanding problems

## Office detects hardware change when booting natively/virtual machine

Booting Windows alternatively as native or as virtual machine makes Office 2013 detect a hardware change and ask for re-activation (you only have a couple of online activations before you have to go through a slow phone process, so you probably want to stick to running Office either natively or on the virtual machine).

Possible source of information (I have only skimmed it): https://mazimi.wordpress.com/2007/07/11/getting-around-windows-activation-when-virtualizing/

## Drag'n'Drop from Windows to Linux not working

At this moment, when I try to drag a file from the Windows guest to the Ubuntu host, I get an error

    Drag and drop operation from guest to host failed.
    Drag and drop error from guest (VERR_FILE_NOT_FOUND).
    
    Result Code: 
    VBOX_E_IPRT_ERROR (0x80BB0005)
    Component: 
    GuestWrap
    Interface: 
    IGuest {13a11514-402e-022e-6180-c3944de3f9c8}

## Bluetooth stops working after suspend/resume

These problems happen with latest [linux-firmware](http://mirrors.kernel.org/ubuntu/pool/main/l/linux-firmware/) package (v1.164).

I'm currently using the [Microsoft Sculpt Comfort Mouse](https://www.microsoft.com/accessories/en-gb/products/mice/sculpt-comfort-mouse/h3s-00001) that connects via bluetooth to the laptop. 

However, suspending/resuming the laptop crashes the bluetooth, and it cannot be brought back to life without rebooting.

Manual bluetooth blocking/unblocking works, so user [rpress suggested blocking the bluetooth before suspend, and unblocking afterwards, and also killing and reloading module `btusb`](https://ubuntuforums.org/showthread.php?t=1387211&p=12464999#post12464999). Create the script with the right permissions and open it in an editor

    cd /etc/pm/sleep.d
    sudo touch 10_bluetooth
    sudo chmod 0755 10_bluetooth
    sudo emacs 10_bluetooth

Then add the code

    #!/bin/bash
    #Code from http://ubuntuforums.org/showthread.php?t=1387211
    
    . /usr/lib/pm-utils/functions
    
    case "$1" in
        hibernate|suspend)
            rfkill block bluetooth
            ;;
        thaw|resume)
            rfkill unblock bluetooth
            rmmod btusb
            modprobe btusb
	    ;;
        *)
	    ;;
    esac
    
    exit


This works for one suspend/resume, but with two in a row, bluetooth dies after some use of the mouse, and I haven't found a way to get it back without rebooting the laptop.

# References

1. [Ubuntu on Dell XPS 15 9560 (Early 2017)](https://bbs.archlinux.org/viewtopic.php?id=223056)
