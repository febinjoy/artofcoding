---
title: "Things to do after installing Fedora 40"
seoTitle: "Post-install tasks for Fedora 40"
seoDescription: "Discover essential steps and software to optimise your Fedora 40 experience, from system updates to installing necessary tools efficiently"
datePublished: Sat May 04 2024 14:50:55 GMT+0000 (Coordinated Universal Time)
cuid: clvs81fsg000e08l3hih4e8vz
slug: things-to-do-after-installing-fedora-40
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1714833181687/35bad1d3-4a43-4b92-99f5-4e0df9df1eb3.png
tags: gnome, fedora, linux-for-beginners, flatpak, post-install, dnf, linux-installation, fedoralinux, fedora40, flathub, gnome-tweaks, fedora-customise, fedora-customize, gnome-customise, gnome-customize

---

April 2024 saw the release of 2 very popular Linux distros — Fedora 40 and Ubuntu 24.04 LTS. As everyone knows, Fedora is a rolling release and Ubuntu 24.04 is a long-term support version. Both use the latest version of GNOME — GNOME 46. Be your own judge, and you are welcome to choose the distro that suits your purpose. You won't go wrong with either of them.

I completed reinstalling my laptop with Fedora 40 and just thought of sharing my workflow of setting up Fedora with you. Most of these work for Ubuntu as well.

# Step 1: Install the OS

Download the OS from the links below and create an installation disk. Follow the on-screen instructions and install the OS using the installation disk.

* Fedora Workstation — [https://fedoraproject.org/workstation/download](https://fedoraproject.org/workstation/download)
    
* Ubuntu 24.04 — [https://ubuntu.com/download/desktop](https://ubuntu.com/download/desktop)
    

# Step 2: Improvements

If any software update window pops up, please install the updates and restart the computer.

## Set Hostname

The default hostname of the system after installing the OS would be `fedora`. You can change it with the following. Replace `<hostname>` with the hostname of your choice. It is recommended to restart the PC after this step.

```bash
sudo hostnamectl set-hostname <hostname>
```

## Configure DNF

Fedora 40 uses **DNF** as its package manager. It provides secure package management and lets you query and fetch packages, install and uninstall them using automatic dependency resolution. Ubuntu users might be familiar with its alternative — **APT**.

DNF searches for packages and downloads them from various mirrors. It may not be using the fastest mirror and may take way more time to download packages. Its performance could be improved as follows:

Open the following file using the `nano⁣` command:

```bash
sudo nano /etc/dnf/dnf.conf 
```

Add the following to the end of the file:

```bash
# Added for speed:
fastestmirror=True
max_parallel_downloads=10
defaultyes=True
keepcache=True
```

These are DNF configuration changes which will help you speed up installing software. The full reference of the DNF configuration is available here — [https://dnf.readthedocs.io/en/latest/conf\_ref.html](https://dnf.readthedocs.io/en/latest/conf_ref.html). If required, include other config changes as well.

## Add Flathub

Like DNF, Flatpak is another package manager supported by both Fedora and Ubuntu. It is a utility for software deployment that offers a sandbox environment. This allows the users to install and run applications, keeping them isolated from the rest of the system. This makes it more secure and takes away the worries about dependencies/libraries required to run the software. Flatpak uses many online repositories, and FlatHub is one such repository that distributes the applications to Flatpak users.

Add the FlatHub repo to Flatpak using the following:

```bash
 sudo flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo
```

## Update your system

The first thing to do after installing the OS and setting up the repositories is to update your system. Use the following command to update the system. When prompted, enter your password. Use `apt` instead of `dnf` on Ubuntu.

```bash
 sudo dnf update
```

# Step 3: Essential configuration and software

The first software I would install on any Linux PC would be Timeshift. It is a backup tool that allows capturing the snapshot of the system. You can install it using the software application or use the following command to do it.

```bash
sudo dnf install timeshift -y
```

Once the tool is installed, take a backup before installing any other software. Make sure you choose all files when configuring backups.

## **Install both Free and Non-Free RPM Fusion**

RPM Fusion provides software that the Fedora Project or Red Hat doesn't want to ship. Download and install both the free and non-free versions of RPM fusion from here — [https://rpmfusion.org/Configuration](https://rpmfusion.org/Configuration).

## **Install media codecs**

Fedora does not come with all the required media codecs pre-installed. Install the media codecs using the following:

```bash
sudo dnf groupupdate multimedia --setop="install_weak_deps=False" --exclude=PackageKit-gstreamer-plugin
sudo dnf groupupdate sound-and-video
```

## **Install DnfDragora**

dnfdragora is a DNF frontend, based on rpmdragora from Mageia. Install it using the following:

```bash
sudo dnf install dnfdragora
```

## **Install git**

Usually `git` comes pre-installed in Fedora. Run the following to ensure it is installed.

```bash
sudo dnf install git
```

## Install Bitwarden

I, personally, use Bitwarden as my password manager and that would be one of the first software that I would install as it helps me with all the passwords to log in to other apps and services easily.

# Step 4: Install required software

## Install from Flatpak

As much as possible, I install all the software I need as Flatpak. In the home directory, create a new folder — `setup` and create a new file — `install_from_`[`file.sh`](http://file.sh). Open the `setup` folder in terminal and open the file using `nano`.

```bash
sudo nano install_from_file.sh
```

You can add all the Flatpak that you need to install in this shell script.

e.g. If you need to install `gimp` and `vlc`, the shell script will be as follows:

```bash
#!/bin/bash
flatpak install gimp -y
flatpak install vlc -y
```

The `-y` flag will automatically respond `yes` whenever the installer raises a user prompt. If you want more control, remove the flag and manually approve each prompt. You can search flatpak for packages using:

```bash
flatpak search <package name>
```

Once you have all the packages listed in the file, we must run the script to install them.

Make the script executable using the following:

```bash
sudo chmod +x install_from_file.sh
```

Execute the file:

```bash
sudo ./install_from_file.sh
```

This will install all the packages one by one. If flatpak identifies more than one package with the name we mentioned, it will prompt you to choose which one to install. Select the one you require when prompted.

Installing using this script saves a lot of time searching for the packages and installing them as I already have a backup of the `install_from_file.sh` readily available. Keep the contents of this file in your Google Drive or email it to yourself for later use. Whenever I reinstall the OS, I use the backup of the script to install all my tools.

### Software worth mentioning

Some notable software I, personally, use from flatpak are listed below:

* **PyCharm** as my Python editor
    
* Meld for comparing files and folders
    
* Tick Tick as a Todoist alternative
    
* Discord
    
* Bluemail as my email client
    
* VLC
    
* Gimp for editing images
    
* Reaper for audio editing
    
* Rawtherapee for editing RAW images
    
* MarkText as my Markdown editor
    
* Obsidian for organising my notes
    
* Joplin and Simplenote for all the text that I need to sync across other devices
    
* Artha as a dictionary tool
    
* XMind for mind mapping
    
* Bleachbit for system cleanup
    
* Missioncenter as a system resource monitoring tool.
    
* Krita for drawing
    

## Other Software

### Install VS Code

Download RPM from [https://code.visualstudio.com/download](https://code.visualstudio.com/download) and install VS Code. If you are already using VS Code on any other machine, sync the extensions by logging in using GitHub or Microsoft account.

### Install JRE

Run the following to install JRE

```bash
sudo dnf install java-openjdk
```

### Install Node.js

Install Node.js and NPM using the following commands:

```bash
sudo dnf install nodejs
sudo dnf install npm
```

Verify the installation using the following commands:

```bash
node -v
npm -v
```

### Install .Net SDK

Use the following to install .Net SDK

```bash
sudo dnf install dotnet-sdk-8.0 -y
```

### Install Microsoft Edge

Personally, I use Edge and Firefox for most of my browsing needs. Firefox comes pre-installed with Fedora. If you wish, you can download and install edge by following the steps below:

* Download RPM from Microsoft and install — [https://www.microsoft.com/en-us/edge/business/download?form=MA13FJ](https://www.microsoft.com/en-us/edge/business/download?form=MA13FJ)
    
* Login to browser profiles so that all the bookmarks and extensions are synchronised
    
* Login to each extension that requires a login.
    

## Extensions

Install **Gnome Tweaks** from the software centre and enable the minimise and maximise buttons.

Install **Extensions Manager** from Flatpak. It is also available here — [https://flathub.org/apps/com.mattjakeman.ExtensionManager](https://flathub.org/apps/com.mattjakeman.ExtensionManager)

Some notable extensions are listed below:

* Dash to Dock
    
* Blur my Shell
    
* Clipboard Indicator
    
* Coverflow Alt-Tab
    
* Caffeine
    
* Custom accent colors
    
* User Themes
    
    * Set theme to custom accent colors
        

# Other miscellaneous tweaks

## Connect your online accounts

If you want to access your Google Drive like a mounted disk on your system, add it from the settings. It also syncs your calendar with the built-in Calendar app.

If the browser window did not pop up to login to google account, it is because the control centre is broken. Run the following:

```bash
WEBKIT_DISABLE_COMPOSITING_MODE=1 gnome-control-center
```

## Setup Clocks

If you want to keep track of the time at a different location, add it to the built-in clock app.

## Install themes and Icons

Use DnfDragora to install themes and Icon packs, and use the tweaks app to choose them.

# Conclusion

Hardening a Linux system is already covered here — [https://hashnode.com/post/clqf5dl9g000108ib52dhfayi](https://hashnode.com/post/clqf5dl9g000108ib52dhfayi)

Add a custom wallpaper of your choice and enjoy using Fedora 40.

#### Shout-outs and courtesy notes

Photo by [Jan Huber](https://unsplash.com/@jan_huber?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) on [Unsplash](https://unsplash.com/photos/blue-and-white-bokeh-lights-3Btf2cTAGKs?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) for the cover picture background.