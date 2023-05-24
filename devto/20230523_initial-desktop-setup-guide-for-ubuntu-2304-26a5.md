---
title: Initial Desktop Setup Guide for Ubuntu 23.04
published: true
description: In this step-by-step guide with screenshots, follow my initial desktop setup for Ubuntu 23.04: restoring backup and SSH keys, customizing appearance, and installing basic software.
series: Ubuntu 23.04 Desktop Setup Guide
tags: ubuntu, linux, beginners, tutorials
cover_image: https://cdn.eheidi.dev/ubuntu2304/setup/setup2304.png
# Use a ratio of 100:42 for best results.
# published_at: 2023-05-23 16:29 +0000
---

The new Ubuntu 23.04 "Lunar Lobster" was released in late April with some nice updates, most notably the new graphic installer and Gnome 44. If you are migrating from another system or just wants to update to a fresh Ubuntu install, now is a good time to go for it! Even though this release is not an LTS (long-term support) release, it brings many bugfixes and updated software, so it's definitely worth updating if you can.

In this guide, I'll share in detail how I set up a freshly installed Ubuntu 23.04 system to get it ready for work and fun.

## Before Starting
If you haven't yet, now is the time to back up your home folder and install Ubuntu 23.04. For a detailed step-by-step guide on how to install Ubuntu 23.04, check my previous post:

{% embed https://dev.to/erikaheidi/how-to-install-ubuntu-2304-as-main-system-on-a-desktop-or-virtualbox-vm-1lpb %}

Once you have your base Ubuntu 23.04 system up and running, you can proceed with the rest of this guide.

## 1. Installing Updates
As soon as you log in, you'll be asked to install some updates to the system. 

![Installing Updates](https://cdn.eheidi.dev/ubuntu2304/setup/updates.png)

You should do that promptly to make sure you get all the latest patches. Proceed when the updates are finished - you may need to restart the computer depending on the type of update.

## 2. Customizing Appearance
After installing updates, the very first thing I like to do in a fresh new Ubuntu install is customizing the desktop appearance. I don't like the default menu bar on the left, so that's one of the first things I change.

Hit the "window" key to open the desktop search, then type "appearance" to open the settings app where you can select the desktop colors. I personally prefer the light theme, with purple accents. In that screen you can also change your desktop background.

![Desktop appearance settings Gnome 44](https://cdn.eheidi.dev/ubuntu2304/setup/03.png)

After selecting the theme, color accent, and background, go to the "Ubuntu Desktop" menu that is right below the "Appearance" item on the left menu. In this screen you can change the dock and desktop icons. Here are the settings I use:

![Ubuntu desktop settings 23.04](https://cdn.eheidi.dev/ubuntu2304/setup/01.png)

- Desktop Icons
    - Size: large
    - Position of new icons: bottom right
    - Show personal folder enabled
- Dock
    - Auto-hide the dock enabled
    - Panel mode disabled
    - Icon size set to max size
    - Position on screen: bottom

On the "Configure dock behavior" menu, I prefer to **uncheck** the option "Show Volumes and Devices", so my Dock doesn't get too crowded.

![Ubuntu desktop settings 23.04](https://cdn.eheidi.dev/ubuntu2304/setup/02.png)

Now you should have a nice dock at the bottom of your screen, where you can pin your most used applications:

![Ubuntu 23.04 with Gnome 44 desktop and bottom dock](https://cdn.eheidi.dev/ubuntu2304/setup/04_.png)

## 3. Restoring Backup
Now is a good time to restore backup files, including SSH keys. You may copy the whole home folder back, but if you're like me and likes to start fresh with the minimal amount of things possible, you may prefer to copy folders separately while keeping the full backup in your external drive, in case you need the files later.

### Restoring SSH Keys
First thing is to copy your SSH keys. Let's say you copied your home folder to a `backups` folder in an external device mounted at `/media/user/external-disk`. The following command will copy the `.ssh` folder from the backup folder in the external drive and into your home folder. Make sure to replace the drive location with the correct path for your setup.

```shell
cp -R /media/user/external-disk/backups/user/.ssh ~/.ssh
```

You'll most likely need to fix the file permissions for your SSH keys. Here is a summary of how the permissions should look like for your .ssh folder:

- Private keys (typically `id_rsa`, but can have a different name): 0600
- Public keys (`.pub`): 0644
- `known_hosts` and `authorized_keys`: 0644

To set the permissions:

```shell
cd ~/.ssh
chmod 0600 id_rsa
chmod 0644 *.pub
chmod 0644 known_hosts authoeized_keys
```

### Testing Connection to GitHub
After changing the permissions, check that you're able to connect to GitHub using your backed up keys by running the following command:

```shell
ssh -T git@github.com
```

You should get a message like this, informing your username and letting you know that you were able to authenticate successfully:

```
Hi erikaheidi! You've successfully authenticated, but GitHub does not provide shell access.
```

Your SSH keys are restored and good to go. While you're at it, configure your git name and email:

```shell
git config --global user.email "you@example.com"
git config --global user.name "Your Name"
```

### Restoring other Files

You can follow the same procedure to copy over other folders in your backup, or you can use Nautilus (the file explorer) to copy files using a graphic interface.

## 4. Installing Essentials

Next, let's install some essential command-line software. The good thing about installing a new distro as soon as it comes out is that the software from official repositories is typically up-to-date with the most recent releases.

Update the apt cache just to be sure:

```shell
sudo apt update
```

The following command will install `git`, `curl`, `vim`, `ffmpeg`, and `docker` on your new Ubuntu system:

```shell
sudo apt install git curl vim ffmpeg docker
```

Check also the "Ubuntu Software" Store for more software that you can install directly with a few clicks. This includes software from Snap repositories and also traditional Ubuntu deb repositories.

![Ubuntu software store](https://cdn.eheidi.dev/ubuntu2304/setup/14.png)

Some of my personal recommendations:

- [Inkscape](https://inkscape.org/) - Vector art, digital illustration
- [Gimp](https://www.gimp.org/) - Image editing
- [MyPaint](https://mypaint.app/) - Digital Drawing
- [Steam](https://store.steampowered.com/about/) - Gaming on Linux
- [OpenShot](https://www.openshot.org/) - Video editing
- [FreeCAD](https://www.freecad.org/) - 3D Design (CAD style)
- [OBS Studio](https://obsproject.com/) - Livestreaming and video recording software
- [Audacity](https://www.audacityteam.org/) - Audio recording and editing

In the next post of this series about Ubuntu 23.04, we'll install and set up Terminator with Oh-My-ZSH for a pretty and extra customizable terminal prompt.