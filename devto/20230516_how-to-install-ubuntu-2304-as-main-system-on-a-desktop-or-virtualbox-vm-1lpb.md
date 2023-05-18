---
title: How to Install Ubuntu 23.04 as Main System on a Desktop or VirtualBox VM
description: In this guide, learn how to install Ubuntu 23.04 - you can install it as main operating system on a desktop computer, or as a VirtualBox VM.
published: true
tags: ubuntu, beginners, linux
cover_image: https://cdn.eheidi.dev/ubuntu2304/install-2304.png
canonical_url: https://onlinux.systems/guides/20230515_how-to-install-ubuntu-2304-desktop-as-main-system-or-vm/
---

## Introduction

The [latest Ubuntu release](https://releases.ubuntu.com/23.04/) came out in late April, and is called **Lunar Lobster**. Although the 23.04 release is not an LTS (long-term support) release, it brings several updates such as a brand new desktop installer and Gnome version 44. In this guide, learn step-by-step how to install Ubuntu 23.04 desktop on a local system or virtual machine.

In order to follow this guide, you'll need access to a computer or laptop where you can install Ubuntu 23.04 from scratch.

You can follow this guide in two ways:

- Install Ubuntu 23.04 **as the main operating system** on your computer (recommended)
    - This is the recommended option to have the best experience with Ubuntu, but it will erase all data in your hard disk. You can also try Ubuntu from the startup disk before installing it.
- Install Ubuntu 23.04 **on a virtual machine** with VirtualBox
    - This option will require more machine power, since you'll have to share RAM memory with the VM (about 4GB recommended). If you want to test Ubuntu or just learn how to install it, you can install it on a virtual machine. In this case, first [install VirtualBox](https://www.virtualbox.org/wiki/Downloads) on your main system.

In both cases, you should have an active connection to the internet so that you can download updates and 3rd party software such as graphics cards drivers.

### Requirements for installing Ubuntu as main operating system

If you're installing Ubuntu as your main operating system, before moving ahead you'll need to create an [Ubuntu 23.04](https://releases.ubuntu.com/23.04/) startup disk. You can also use the startup disk to try out Ubuntu before installing it.

You can follow [this step-by-step guide on how to create such a disk using another Ubuntu system](https://onlinux.systems/guides/20230515_how-to-create-a-ubuntu-2304-startup-disk-on-ubuntu-systems).

Somewhere in the beginning of the installation process, you will be prompted to connect to a wi-fi network. Follow the instructions to set that up and allow internet access from the installation program.

Before moving ahead you should **make sure** you have backed up any personal data to a removable disk, a remote / cloud driver, or another computer. Don't forget to save your `.ssh` directory containing your SSH keys, otherwise you will lose access to servers and services where your current key is registered.

### Requirements for installing Ubuntu on a virtual machine

If you're installing Ubuntu on a virtual machine, make sure you have an [up-to-date version of VirtualBox](ttps://www.virtualbox.org/wiki/Downloads) installed on your main operating system. You won't need a startup disk.  

Follow [this step-by-step guide on how to set up a VirtualBox Linux VM for running Ubuntu 23.04](https://onlinux.systems/guides/20230516_how-to-create-a-linux-virtual-machine-vm-for-running-ubuntu-2304-on-virtualbox).

Once you're on the Ubuntu 23.04 boot screen, you can skip to **Step 2**.

## Step 1: Boot from startup disk
Depending on your computer and the setup program it runs, you will have to press a special key to either choose the boot device directly or access the BIOS setup program and adjust your settings in order to boot from your new Ubuntu boot disk. In most computers, this key will be `F12`.

With my Lenovo Thinkpad laptop, I need to press `F12` as soon as the computer beeps after rebooting, and this will let me choose which device to boot. My old, generic USB stick is recognized as "USB HDD:VendorCo ProductCode".

![Choosing the boot disk on a Lenovo Thinkpad Carbon](https://onlinux.ams3.digitaloceanspaces.com/ubuntu2204_install/lenovo_boot.jpg)

When the computer successfully starts up the boot program from your Ubuntu disk, you'll then see the **Grub boot screen**.

![Starting the Ubuntu installation](https://onlinux.ams3.digitaloceanspaces.com/ubuntu2204_install/step1_grub.png)

Choose "Try or install Ubuntu" to proceed.

## Step 2: Start the installation program

You'll get a screen like the following, showcasing the new Ubuntu desktop installer:

![Step 2](https://cdn.eheidi.dev/ubuntu2304/installation-guide/step1.png)

You may want to try Ubuntu before installing it. In that case, use the option on the left. To install Ubuntu, click on "Install Ubuntu".

## Step 3: Choose the language and installation options
The first screen will be to choose your language, then your keyboard layout and language.
![Step 3](https://cdn.eheidi.dev/ubuntu2304/installation-guide/step2.png)

Once you confirm that, you'll be taken to a screen where you'll have a few options regarding updates. Leave "Normal installation" checked, and check both options in the "Other options" area to make sure your installation is able to download the latest updates and also third-party drivers whenever necessary.
![Step 4](https://cdn.eheidi.dev/ubuntu2304/installation-guide/step4.png)

Click on "Continue" to move on.

## Step 4: Customize disk options

For improved security, I strongly advise anyone installing Ubuntu these days to make use of their full disk encryption feature. When you get to the "Installation Type" screen, select `Advanced Features`.

![Step 5](https://cdn.eheidi.dev/ubuntu2304/installation-guide/step5.png)

On the popup that will appear, choose `Use LVM with the new Ubuntu installation`, then check the `Encrypt the new Ubuntu installation for security` option.

Once you confirm that, you'll be asked to choose a security key. You will be prompted to provide this security key every time you restart your computer, and if you lose this key you will be 100% locked out. Make sure to choose a good one!

![Step 6](https://cdn.eheidi.dev/ubuntu2304/installation-guide/step6.png)

### Confirming disk changes
You'll then be asked to confirm hard disk settings. Click "Install" to confirm the change to your disks.

![Step 7](https://cdn.eheidi.dev/ubuntu2304/installation-guide/step7.png)

## Step 4: User Info and Appearance
Next, set up your account on this system. Set your name, the hostname for the system, your username and password.

![Step 8](https://cdn.eheidi.dev/ubuntu2304/installation-guide/step8.png)

Choose your default theme from a choice of "light" or "dark".

![Step 9](https://cdn.eheidi.dev/ubuntu2304/installation-guide/step9.png)

## Step 5: Finish Installation
Now you can sit back and wait while Ubuntu is finished being installed on your machine. 

![Step 10](https://cdn.eheidi.dev/ubuntu2304/installation-guide/step10.png)

When the installation is finished, you'll get a screen like this, with a button to "Restart now".

![Step 11](https://cdn.eheidi.dev/ubuntu2304/installation-guide/step11.png)

Click on the "Restart now" button. When prompted, remove the boot device / usb stick and press `ENTER` to restart the system.

After the computer restarts, you should be greeted by the Grub boot screen again. Select the first option to enter your freshly installed Ubuntu 23.04 system.

## Step 6: Unlock the disk to access your fresh Ubuntu system
If you followed all steps in this guide and chose full disk encryption, you will be prompted to provide your secure key now. Type the key and hit `ENTER`.

![Step 11-2](https://cdn.eheidi.dev/ubuntu2304/installation-guide/step13.png)

You should see a message indicating that the disk was successfully unencrypted. After that, your Ubuntu system will be mounted, and you will be greeted with the login screen. Provide your login and password to access your new Ubuntu 23.04 system.

![Step 12](https://cdn.eheidi.dev/ubuntu2304/installation-guide/step13.png)

Your system should now be fully functional.

![Step 12](https://cdn.eheidi.dev/ubuntu2304/installation-guide/step14.png)

## Step 7: Customize your Ubuntu 23.04 Appearance

With the main system installed, you can now customize its appearance to make it look _just right_ for your taste.
In this section I will share the customization I made for my own system. Feel free to explore other options.

Go to `Settings -> Appearance` to customize the appearance of your new Ubuntu 23.04.

![Customizing Ubuntu 23.04 appearance](https://cdn.eheidi.dev/ubuntu2304/installation-guide/step15.png)

I particularly loved the default dark mode with purple accents, and the Lobster on a rock wallpaper. What do you think?

Have fun customizing your setup!

## Conclusion

The new Ubuntu 23.04 Lunar Lobster comes with a new desktop installer, which provides a smooth installation experience. With Gnome version 44 and lots of new and updated software, it is definitely worth updating your current Ubuntu setup to the latest edition; even though this won't be a LTS (long-term support) version, there are meaningful updates that should fix bugs from previous releases and some nice-to-have improvements to the UI in general. Go for it!
