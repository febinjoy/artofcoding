---
title: "EndeavourOS - Gemini"
datePublished: Sat Jun 01 2024 10:14:01 GMT+0000 (Coordinated Universal Time)
cuid: clwvyh6sr000009le0ek34dzs
slug: endeavouros-gemini
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1717236523150/d51136ab-ae41-41d5-8712-06b96d26ba9d.png
tags: archlinux, gnome, linux-for-beginners, endeavouros, post-install, linux-installation, pacman, gnome-customise, gnome-customize, endeavouros-customisation, yay, linux-setup, arch-setup, endeavouros-setup, endeavouros-gemini

---

# Introduction

Last week, my trusty computer running Ubuntu 23.10 decided to throw a tantrum while I was trying to upgrade it to Ubuntu 24.04, the latest LTS release. It resulted in a crash, leaving my system in an unpleasant state. Instead of diving deeper into the troubleshooting steps to resolve the issue, I took it an opportunity to step outside my comfort zone and try something new.

My quest came to a halt on EndeavourOS, a distribution based on Arch Linux. Arch Linux, as everyone knows, is a highly customisable, minimalist Linux distribution. Here are some of the advantages of EndeavourOS that got me thinking in favour of it:

**Arch Accessibility:** As someone who’s always been curious about Arch Linux but hesitant to take the leap, EndeavourOS seems like the perfect bridge. It’s designed to make Arch Linux more approachable for users like me.

**GUI Installer:** Unlike the base distro - Arch, which demands manual configuration and a certain level of expertise, EndeavourOS provides a user-friendly GUI installer. It was easy to get the system set up. After installation, I found that, and I appreciate the EndeavourOS team for their wonderful effort in making the installation process a breeze.

**Rolling Release:** EndeavourOS follows the same release model as Arch. This means I don't have to wait through a release cycle to get the latest software. No more FOMO (fear of missing out) on updates!

**Total control:** Arch fans love the control it provides its users. EndeavourOS also inherits this from Arch. Being a software professional and a Linux enthusiast, I loved the OS to be the way I want it (Which I would be discussing with you in this article).

**No Bloatware:** Like Arch, EndeavourOS also keeps things to a minimum and allows the users to develop from the basics.

**Software availability:** There are 2 built-in package managers for EndeavourOS -**Pacman** and **Yay**. **Pacman** is the package manager for Arch, which EndeavourOS is based on. It is used to install, update, and remove packages from the official Arch (and EndeavourOS) repositories. (A repository is just a collection of packages, programs and libraries). Then there is Arch User Repository (AUR), which is a collection of packages not in the official repositories. These are maintained by users. It is highly recommended to install software from pacman if it is available there. If not, you can try checking yay.

# Installing EndeavourOS

Follow the steps below to install EndeavourOS

## Download the OS

Visit the official EndeavourOS website and download the ISO image for your architecture (32-bit or 64-bit). You can find the download link here: [https://endeavouros.com/#Download](https://endeavouros.com/#Download)

You have the option to download from Torrent, Magnet or directly download from a mirror closer to your location.

I used EndeavourOS Gemini in this article.

## Create a Bootable Disk and Install the OS

Once you have the ISO file, proceed to create a bootable USB drive using a tool similar to Rufus or Etcher.

Boot from that USB stick. If required, change your BIOS settings to boot from the USB. You will be greeted with the installer. It will provide you 2 options:

* **Online Install**: Allows you to choose your desktop environment during installation.
    
* **Offline Install**: Defaults to XFCE.
    

Select **Online Install** to choose your desktop environment.

Then the installer would ask you to choose the desktop environment. Please choose your favourite one. Being a fan of GNOME, my choice was obviously the same.

At this stage, you will need to connect to your Wi-Fi if your computer is not connected to the internet using a LAN cable.

Follow the on-screen instructions and perform tasks like partitioning the disk, choosing your timezone, creating your user account and password etc.

Once the installation is over, please remove the USB drive and restart your PC.

You will boot into the newly installed EndeavourOS!

# Make it yours!

Most of the steps in this article would apply to other Arch-based distros like Garuda Linux as well.

## Installing Updates and enabling essentials

You will be greeted with a welcome screen on your first login to EndeavourOS. Use it to update all the mirrors, and perform system updates. Once you are done, please restart the PC.

### Enable Bluetooth

By default, Bluetooth will be disabled in EndeavourOS. This makes it difficult for people using a USB keyboard, mouse or headphones. Use the following command to start it:

```bash
sudo systemctl start bluetooth
```

To enable it on every restart, run the following:

```bash
sudo systemctl enable bluetooth
```

Reboot the PC and verify that you can enable Bluetooth. Once the Bluetooth is enabled, connect your peripherals.

### Install Drivers

I use a USB hub which makes use of DisplayLink to connect to multiple monitors. Like most other Linux distros, EndeavourOS also did not detect it. As a result, I was connecting a monitor using HDMI for my setup. I installed the DisplayLink driver using the following:

```bash
yay -S displaylink
```

Once the driver was installed, I restarted the PS and the monitors were detected. It was never this easy with my good - old Ubuntu.

You are welcome to search and install any other drivers that you might need.

### Configure Backups

Backups are inevitable for any system. I use a tool - **Timeshift** for my backup needs. It allows capturing the snapshot of the system at a point of time. After installing it, take a backup. If there are 2 disks, choose one disk entirely for backups. It is recommended to schedule a daily backup and retain daily backups for at least a week to be on the safer side. Use the following command to install Timeshift.

```bash
sudo pacman -S timeshift
```

## Installing Software

### Preinstalled Software

EndeavourOS comes preinstalled with the following tools.

* Git
    
* Mozilla Firefox
    
* Meld (A tool used to find the differences between 2 files or folders. It is similar to WinMerge on Windows)
    
* Python
    

### Install software using Pacman

As mentioned earlier, pacman is the package manager for Arch, which EndeavourOS is based on. It is similar to **apt** in Debian and **dnf** in Fedora. To install a single package or list of packages, including dependencies, use the following command:

```bash
pacman -S package_name1 package_name2 ...
```

To remove a single package, leaving all of its dependencies installed, use the following:

```bash
pacman -R package_name
```

To remove a package and its dependencies which are not required by any other installed package, use the following:

```bash
pacman -Rs package_name
```

If you are not sure if a software is available in `pacman` or not, try the following:

```bash
pacman -Ss package_name
```

It will list out all the packages available. Please note the exact name of the package you need from the list provided, replace `package_name` with it and use `packman -S` to install it.

Prefix the commands with `sudo` as required.

#### Using shell script to install

I prefer to install software using a shell script so that it could be re-used the next time when it needs to be done. Please follow the following approach to do it:

* Search for all the packages you need to install and take a note of the exact package name you have to install.
    
* Once you know the name of all the packages to be installed, create a new folder — `Setup` in the home directory.
    
* Create a new file — `install_from_`[`file.sh`](http://file.sh) in it.
    
* Add the `pacman` install commands to it for all the packages you have to install.
    
* A sample file with all common tools is provided below for your convenience. Please refer to the inline comments.
    
* Add/Remove commands to this list as per your requirement.
    

```bash
#!/bin/bash

# Password Management
pacman -S bitwarden

# Programming
pacman -S filezilla
pacman -S dbeaver
pacman -S pycharm-community-edition
pacman -S remmina
pacman -S wireshark-qt
pacman -S docker
pacman -S nodejs
pacman -S npm
pacman -S dotnet-sdk
#It is recommended to run Postgres in Docker than locally
pacman -S postgresql
# If you want to run VMs
pacman -S gnome-boxes 

# Comms 
pacman -S discord

# Media
pacman -S vlc
pacman -S gimp
pacman -S reaper
pacman -S rawtherapee
pacman -S darktable
pacman -S shotwell
pacman -S rhythmbox
# For editing videos
pacman -S kdenlive 

# Text Utilities
pacman -S obsidian
pacman -S notepadqq

# Utilities
pacman -S bleachbit
pacman -S qbittorrent
# For creating bootable USB sticks
pacman -S isoimagewriter
# By default, calendar app is not installed
pacman -S gnome-calendar 

# Finance
pacman -S homebank

# Drawing 
pacman -S krita

# Extensions
pacman -S gnome-shell-extensions
```

Once you have the script ready, It is time to provide it executable permissions. Use the following from the terminal in the same folder as the script file.

```bash
sudo chmod +x install_from_file.sh
```

Execute the script to install the packages.

```bash
sudo ./install_from_file.sh
```

This will install all the packages that you chose. Occasionally, you may be prompted to provide confirmation whether you are sure if you need to install the package. To avoid this, you can use a `-y` flag in the shell script next to each pacman command. Here is an example:

```bash
pacman -S krita -y
```

All the packages from pacman will now be installed on your PC. It is recommended to restart your PC at this point.

#### Google Drive

In EndeavourOS, you would be able to add your Google accounts like any other GNOME distro. But your Google Drive may not show up in the Files application. It is because, you are lacking a package - `gvfs-google`. It is a virtual file system implementation for Google Drive on Linux. Other distros will have it preinstalled. Here, you will need to install it manually using the following:

```bash
sudo pacman -S gvfs-google
```

Once installed, restart your computer. Google Drive will show up in your Files application.

### Install software using Yay

Pacman installs packages only from the repos. There is a high chance of having some software that you need is not available. That is where Yay comes to your rescue. As mentioned earlier, there is a huge repository of software out there called AUR. Yay lets you install packages from both the repos and AUR.

You can follow the same format as pacman for searching, installing and removing packages with yay. The format is as follows:

```bash
yay -S package_name
```

However, I would not recommend using the shell script approach with yay.

> \[!Warning\] You should not be using sudo with `yay`.

#### Install Pamac

EndeavourOS does not come with a GUI-based package manager. I would recommend you to install Pamac which is a GUI-based package manager from Manjaro. Use the following to do it. If you are comfortable with a terminal package manager, this step is purely optional.

```bash
yay -Syu pamac-aur
```

#### Install LibreOffice

Please install LibreOffice from **Pamac GUI**.

#### Install other software

##### Bluemail

```bash
yay -S bluemail
```

##### pgadmin

```bash
yay -S phppgadmin
```

##### XMind

```bash
yay -S xmind
```

##### Opera Browser

```bash
yay -S opera
```

##### Artha (Dictionary)

Artha is a dictionary application. Something similar to **Wordweb** on Windows.

```bash
yay -S artha
```

##### Visual Studio Code

To install the official VS Code, install the following. This is not the open-source version and supports settings to sync with your GitHub account.

```bash
yay -S visual-studio-code-bin
```

##### Git Client (gitqlient)

```bash
yay -S gitqlient
```

##### Docker Desktop

```bash
yay -S docker-desktop
```

### Shell extensions

#### Blur-my-Shell Extension

Blur my shell is a popular extension that adds a blur look to different parts of the GNOME Shell.

```bash
yay -S gnome-shell-extension-blur-my-shell
```

#### Caffeine

This is a shell extension used to disable the screensaver and auto-suspend. It helps to keep your computer awake.

```bash
yay -S gnome-shell-extension-caffeine
```

#### Custom Accent Colours

This is a GNOME Shell Extension that provides 7 Custom Accent Colours. The selected Accent Colour can be applied to GTK4/GTK3 apps and the Gnome Shell.

```bash
yay -S gnome-shell-extension-custom-accent-colors-git
```

### Other customisation

#### Icons

If the default icon pack in EndeavourOS is not exciting enough for you, find some icon pack by searching in pacman.

```bash
pacman -Ss icon
```

Install the icon pack:

```bash
sudo pacman -S obsidian-icon-theme
```

#### Keyboard shortcuts

Following are some of the keyboard shortcuts I added.

##### Launchers

* Win (Super) + E : Open Home folder
    
* Win (Super) + F : Launch web browser
    
    * My browser is Firefox
        
* Ctrl + Shift + F : Search
    

##### Navigation

* Win (Super) + D : Hide all normal windows
    

##### Custom Shortcuts

* Win (Super) + T : Terminal
    
    * Use command : `gnome-terminal --geometry 132x43`
        
* Win (Super) + O : Obsidian
    
    * Use command : `obsidian`
        

Here is a preconfigured keyboard shortcut that might be helpful for you:

* Win (Super) + A : Opens app drawer
    

# Conclusion

So far, EndeavourOS seems to be one of the best Linux distros that suits my needs perfectly, and I am more than tempted to install it on my laptop as well. Kudos to the developers and contributors for their wonderful effort in making EndeavourOS what it is now.

Please share your experience with EndeavourOS with me, and don't hesitate to get in touch if you need better clarity on something I mentioned in this article.

Add a custom wallpaper of your choice, and enjoy using EndeavourOS.

> \[!Ouote\]: The Linux philosophy is - 'Laugh in the face of danger'. Oops. Wrong One. 'Do ot yourself'. Yes, that's it.
> 
> ~ Linus Torvalds