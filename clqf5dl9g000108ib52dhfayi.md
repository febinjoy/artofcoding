---
title: "Linux hardening for beginners"
seoTitle: "Beginner's Guide to Linux Hardening"
seoDescription: "Beginner's Secure Linux: Master user management, password policies, SSH, firewall setup, and system updates for enhanced protection"
datePublished: Thu Dec 21 2023 11:56:51 GMT+0000 (Coordinated Universal Time)
cuid: clqf5dl9g000108ib52dhfayi
slug: linux-hardening-for-beginners
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1703069797746/149b94c3-a868-46e9-a1b3-5ca6fcd20c1c.png
tags: linux, linux-for-beginners, linux-basics, linux-security, securing-linux, linux-hardening

---

## Introduction

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1703077634896/6a911bd4-cb4d-45a0-bb78-75fefe9d654b.png align="center")

Many developers have utilised Linux for various tasks and projects during their careers, with many opting for it as their primary operating system. One of the key factors driving this choice is security. Linux is frequently regarded as more secure than Windows for several reasons:

**Open-Source:** Linux's security is boosted by its open-source nature, allowing a large community to identify and fix vulnerabilities quickly.

**Diversity:** Linux's numerous distributions, like Ubuntu, Debian, and Fedora, let users customise security and features for their needs.

**Privilege separation:** Linux's strict privilege model limits user and process permissions, preventing unauthorised access and reducing attack risks.

**Centralised package management**: Linux distributions offer secure package management systems for installing, updating, and verifying software from trusted sources, reducing malware risks.

**Strong user and permission management**: Linux provides a robust system for managing users and permissions, enabling administrators to set accurate access controls and limit privileges, enhancing security against unauthorised access and breaches.

System security relies on many things, like proper configuration, timely updates, user awareness, and adherence to security best practices, as no operating system is entirely immune to security risks. Different Linux distributions come with preinstalled applications and configurations, but their default settings might not be sufficiently hardened to minimise the potential attack surface.

In this article, we'll explore various configuration improvements to strengthen your Linux machine or server's security, safeguarding confidentiality, preserving integrity, and ensuring availability against potential attacks. Since this topic is too vast and deep, this article is designed only to act as a signboard which will lead you in the right direction towards security.

## What and How?

Let us categorise this as per MoSCoW prioritisation([MoSCoW method - Wikipedia](https://en.wikipedia.org/wiki/MoSCoW_method)) method into the following:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1703076829792/33db2c87-a12b-406b-b98a-4ce339ab088e.png align="center")

### Must have

#### Choose the right distro

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1703079757465/ac6c8857-ce0d-4b36-9cbc-04b5b5aa4398.png align="center")

Here are several examples of the many Linux distributions available for you to choose.

* Ubuntu
    
* OpenSUSE
    
* Debian
    
* Fedora
    
* Linux Mint
    
* CentOS
    
* Arch Linux
    

Each distribution has its own strengths, target audience, and purpose. It is crucial to investigate and select the one that best meets your needs in terms of usability, software availability, community support, and specific requirements.

**Factors to consider:**

* Usage. It should be the right version and flavour for your job. You are the best person to judge.
    
* Hardware requirements.
    
* Long-term support. (Find a right balance between the latest version and LTS)
    
* Ease of use.
    
* Stability and security.
    
* Community support.
    
* Previous experience (If you are familiar with a distro, pick the same unless if you want to try a new one).
    

#### Update the OS and Package Manager

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1703153560177/1d543059-1167-44fe-aaae-28244b0af1bd.png align="center")

The first thing you need to do after installing a Linux distro of your choice is to update it. There may be patches available which fix bugs and vulnerabilities. How you do this greatly depends on your choice of distro. Different distros use different package managers. For this article, I would be focusing more on the package manager: `apt` used by Debian-based Linux distros like Debian, Ubuntu, Linux Mint, and elementary OS. You can use the alternates for the distro of your choice.

Run the following after spinning up the machine. (The `-y` flag will say yes to all prompts for confirmation. If you want to review and proceed, please remove it.)

```bash
sudo apt update
sudo apt -y upgrade
```

> Quick Tip:
> 
> **Use** `apt` **instead of** `apt-get`
> 
> As mentioned earlier, `apt` which is an abbreviation of "Advanced Package Tool" and `apt-get` are package manager tools used in Debian-based Linux distros. The distinction between `apt` and `apt-get` goes beyond `apt` being a newer version of `apt-get`. The `apt` command was developed to provide a more user-friendly alternative to `apt-get`, consolidating the features of various package management tools for the ease of users.
> 
> **Use** `dnf` **instead of** `yum`
> 
> YUM, or "YellowDog Updater Modified", is a package manager designed for installing, updating, or removing software packages on Red Hat Enterprise Linux systems. On the other hand, DNF, which stands for Dandified YUM, is an enhanced version of the YUM package manager. The problems associated with YUM, such as sluggish performance, high memory consumption, and slow dependency resolution, have been addressed in DNF. This improved package manager offers increased efficiency and a more user-friendly experience for managing software packages on RedHat-based Linux systems.

#### Proper user management

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1703153886185/5bddd1c1-feb4-4ca4-8c89-0c4fccfad60d.png align="center")

User management is vital for system security, and mastering efficient account management techniques helps safeguard your server or machine from vulnerabilities.

##### Root User

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1703154737133/7ca4a4c1-a9f0-4a16-b71d-10ce28fbc554.png align="center")

On Linux, the `root user`, also known as the `root account` or `superuser account`, is the administrator with the highest level of system privileges who possesses unlimited access to all files, directories, and system resources on the operating system. You can perform critical system administration tasks, such as installing software, modifying configurations, managing users and permissions, and accessing sensitive files that regular users cannot modify or access as a root user.

Exercise caution when using the root user account, and use regular user accounts for daily activities. Keeping the root login enabled can pose a security threat, as hackers can potentially use these credentials to gain access to the server. To strengthen your Linux system and harden it, you should disable root login.

Most of the distros come with the root user disabled by default. Do not enable it unless you know what you are doing. Some older versions of the popular distros come with root user enabled. It is always recommended to use the latest version of the OS.

##### Sudo

The `sudo` command stands for either `substitute user do` or `super user do` and is typically used to execute commands as the root user, temporarily elevating a regular user's privileges for specific administrative tasks. This helps in enhancing security by limiting root account usage to only when required. You might have already seen me using `sudo` above when we tried to install updates.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1703133210565/aa8fe9c1-ed44-4973-9ca9-a5b612196393.png align="center")

##### Sudoers

If every user in the system can elevate themselves to have root privileges by using `sudo`, how does it make the system secure? Well, not all users can elevate themselves to have root privileges using `sudo`. They need to be part of `sudoers` group to do so.

Now what is `sudoers` group? The Linux `sudoers` group is a distinct group that provides selected users with the ability to execute commands with administrative privileges through the `sudo` command. This enables them to perform tasks that require higher permissions.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1703134531364/74ab1e31-2806-4f40-9c5f-056e92feb4b9.webp align="center")

##### How to review Sudoers and provide/revoke access

Using the following command would list all the `sudo` users in Ubuntu and Debian.

```bash
sudo grep -Po '^sudo.+:\K.*$' /etc/group
```

If you want to add a particular user account to the sudoers list, use the following:

```bash
sudo adduser <username> sudo
```

To remove a user from the sudoers list, use the following:

```bash
sudo deluser <username> sudo
```

##### Default user accounts

Some old versions of certain distros comes with pre-created user accounts. For example, older versions of Raspbian OS came with a user `pi` with password `raspberry`. This was dropped in OS version Bullseye. Please refer: [https://www.raspberrypi.com/news/raspberry-pi-bullseye-update-april-2022/](https://www.raspberrypi.com/news/raspberry-pi-bullseye-update-april-2022/)

Make sure your system does not have a pre-created user account.

**List all user accounts (Only usernames):**

```bash
sudo awk -F: '{ print $1}' /etc/passwd
```

**Power users can try this (Usernames and permissions):**

```bash
sudo less /etc/passwd
```

To remove a user, use the `userdel` command. Using `-h` option after the command will list out all options available for the command.

```bash
sudo userdel <username>
```

Reviewing user permissions is also very critical on Linux machines. Going deep into it will blow up the proportions of this article. Please refer to this wonderful article: [https://www.freecodecamp.org/news/how-to-manage-users-in-linux/](https://www.freecodecamp.org/news/how-to-manage-users-in-linux/) by [Destiny Erhabor](https://www.freecodecamp.org/news/author/caesarsage/) for an elaborative article on Linux User Management.

#### Passwords and login

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1703155460591/44f8da81-21f1-4781-af32-8068a0b95fba.png align="center")

Having strong user and sudo passwords is essential for maintaining the security and integrity of any system. It protects the machine from unauthorised access and safeguards sensitive data. Weak passwords are vulnerable to various password-based attacks, such as brute-force attacks or dictionary attacks.

Remember to choose a strong password that is unique, complex, and not easily guessable. It is advised to use a combination of uppercase and lowercase letters, numbers, and special characters in your password. Regularly updating passwords and refraining from password reuse can further enhance the security of your Linux system.

Make sure you do not write down or save your passwords anywhere. Notepads, OneNote, Notion and other similar tools are not places destined to save passwords. If passwords are complex and difficult to remember, use a reputed password manager like Bitwarden or LastPass, which will store your passwords encrypted.

#### SSH

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1703155646767/43c209b0-cdca-4c8d-a632-adce85023563.png align="center")

SSH, or Secure Shell, is a network protocol enabling secure remote access and communication between two computers over an unsecured network, allowing users to safely access and control a remote computer or server from their device, regardless of distance. This is specifically useful in the case of servers.

It uses public-key cryptography to authenticate and establish a secure connection between the client (local computer) and the server (remote computer), exchanging cryptographic keys to verify each other's identity before connecting. Once the SSH connection is established, users can remotely execute commands, manage files, transfer data, and perform various administrative tasks on the remote system.

Using SSH for connecting to remote Linux machines is highly encouraged. The following are a few pointers that could be helpful in configuring SSH.

* Ensure you are using V2 of the SSH protocol
    
* Ensure to have the following config set for SSH
    
    * PermitEmptyPasswords no
        
    * PermitRootLogin no
        
    * MaxAuthTries 4
        
    * Allow only specific users (choose who can SSH)
        
* Change the default port: 22 to something else and deny all traffic to port 22.
    
* Make sure that root cannot log in remotely through SSH
    
* Configure IPTables(A Linux firewall) to restrict SSH access from known IPs only.
    
* Key-based authentication should be used instead of password-based authentication.
    
* Client keys should be encrypted to prevent their use in case they are stolen.
    
* Use SSH tunnels if required.
    
    * SSH tunnelling, also known as SSH port forwarding, is a technique that enables a user to open a secure tunnel between a local host and a remote host. More on that later.
        

### Should have

#### Remove unwanted software and disable unused functionalities and services.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1703155805726/94ab2f1c-1b68-4075-9c21-b1086a700a15.png align="center")

Operating systems often come preloaded with software and services that run constantly in the background without notice. To enhance the security of the machine, list all packages and software installed using your package managers (e.g. apt, dnf).

```bash
sudo apt list –-installed
```

Make sure to remove unwanted packages

```bash
sudo apt remove <package name>
```

**Ensure to check and close open ports**

Open ports can expose information about your network architecture, making it susceptible to attacks. Malicious actors can exploit these vulnerabilities to gain access to your server/machine. To mitigate this risk, regularly scan for open ports and close any that are not in use. Tools like netstat can help you identify open ports and promptly secure your server by closing them.

**Disable unused services**  
Disable any unwanted services that you are not using. Make sure that the service is not in use or required before disabling.

```bash
sudo systemctl stop <service name>
sudo systemctl disable <service name>
```

Furthermore, make sure that you are not installing any unwanted software.

#### Firewall

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1703155125013/fe986411-1d98-458f-886d-03428c54e26b.png align="center")

Firewalls are essential for enhancing the security of Linux systems and networks. They control access, block harmful traffic, and defend against unauthorised access attempts and play a vital role in maintaining system integrity. Make sure to install a firewall and configure it properly.

Here is a list of popular Linux firewalls:

* iptables
    
* nftables
    
* UFW (Uncomplicated Firewall)
    
* Firewalld
    
* Shorewall
    

These are some examples of Linux firewalls. Each one has its own strengths, features, and ways to set it up. It's important to pick a firewall that fits your needs and works well with your network environment.

#### Backups

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1703156144912/3fc04bce-e847-4286-aba0-1ab870d4887e.png align="center")

Backups are essential to the security of any system. It comes handy in the event of a security incident or disaster recovery. There are plenty of options available for backing up your Linux systems like:

* rsync: A command-line utility for file synchronisation and backup.
    
* Bacula: An open-source network backup solution that offers a client-server architecture. It provides features like data compression, encryption, and backup scheduling. Bacula supports various backup methods, including full, differential, and incremental backups.
    
* Amanda (Advanced Maryland Automatic Network Disk Archiver): A backup software that allows for centralised management of backups across multiple systems. It offers features like deduplication, encryption, and backup scheduling.
    
* Duplicity: A command-line backup tool that focuses on secure and encrypted backups. It uses standard tools like GnuPG for encryption and supports various storage backends, including local file systems, FTP, SSH, and cloud storage providers. If you want to back up to cloud solutions like Amazon S3 or Microsoft Azure, Duplicity could be an option for you.
    
* Clonezilla: Clonezilla is a disk imaging and cloning tool that creates exact copies (images) of entire disks or partitions, allowing for easy restoration in case of system failures or data loss
    
* Timeshift: Timeshift is a system restore utility that creates snapshots of the operating system at different points in time.
    

These are just a few examples. Each one has its pros and cons. You can opt to choose one or a combination of more that aligns with your backup requirements and preferences.

#### Automate patches and updates

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1703156471711/b31f635c-aad8-4bcb-b72d-cb8bd02fb239.png align="center")

The Unattended Upgrades feature of Ubuntu (and other Debian-based distros) ensures that important security patches for installed packages are automatically downloaded and installed without needing any manual intervention from an end-user

```bash
sudo apt install unattended-upgrades && systemctl enable --now unattended-upgrades
```

#### Disable unused peripherals

Reduce the attack surface area by disabling the peripherals that are not in use.

e.g. A web server connected to a network using a LAN cable should not have Wi-Fi enabled.

#### Disk Encryption

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1703156656314/b33e0032-f975-463f-9ee0-63ec3d4e6c8c.png align="center")

The decision to encrypt the hard disk greatly depends on how sensitive is the data the machine handles. If the Linux server/machine handles sensitive data, disk encryption is highly recommended.

Please refer [https://ubuntu.com/core/docs/uc20/full-disk-encryption](https://ubuntu.com/core/docs/uc20/full-disk-encryption) for Ubuntu's disc encryption.

#### Transport layers and protocols

Please try to stick to the following as much as possible.

* Use TLS 1.3 and HTTPS as much as possible.
    
* Avoid Using FTP, Telnet, and Rlogin/Rsh Services
    
    * Under most network configurations, usernames, passwords, FTP / telnet / RSH commands and transferred files can be captured by anyone on the same network using a packet sniffer.
        
    * The common solution to this problem is to use either OpenSSH , SFTP, or FTPS (FTP over SSL), which adds SSL or TLS encryption to FTP.
        
* If your network applications are not using the IPv6 protocol, disable it to decrease the attack surface area.
    

### Could have

#### Logging and Auditing

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1703158878294/6e2a5b3c-48a5-4aa8-9f89-933a649ffb97.png align="center")

We need to configure logging and auditing to collect all hacking and cracking attempts. By default, syslog stores data in `` /var/log/` `` directory. This is useful to find out software misconfiguration, which may open your system to various attacks.

#### Mandatory Access Control

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1703158661306/f9e6d1f6-165f-4d90-bd8a-8f7e4f8a6208.png align="center")

Mandatory access control (MAC) systems give fine-grained control over what programs and applications can access. This means that your browser won't have access to your entire home directory or similarly.

There are two prominent options among the various others worth mentioning.

**SELinux:** SELinux aka Security-Enhanced Linux, is a fantastic security system for Linux. It provides different security policies for the Linux kernel. This overlay allows you to control processes access to files in the file system. Only a program with the appropriate role can access a specific resource, and even superuser privileges are not relevant. SELinux significantly enhances the security of the Linux system, as even the root user is considered a regular user here.

**AppArmor:** The AppArmor system is used on Ubuntu and operates similarly to SELinux. Profiles are created for each application, specifying which files the application can access, while denying access to everything else.

#### Sandboxing

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1703158443848/b2d078ef-0644-428d-aefc-a77f7d229d1f.png align="center")

A sandbox allows you to run a program in an isolated environment that has either limited access or none to the rest of your system. You can use these to secure applications or run untrusted programs.

Containerisation platforms like Docker and Bubblewrap are worth mentioning here.

### Won't have

We don't have much to mention here in this section, except for the following:

* Weaker policies and practices.
    
* Reluctance to update yourself and the server/machine.
    

### Conclusion

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1703159349623/11ce6629-d1aa-4bde-8378-02afdc9f9709.png align="center")

#### Security is a Journey, Not a destination

The Linux system hardening steps and configurations mentioned above will help boost the security of your Linux servers and machines. However, always remember that security is an ongoing process that requires regular checks, software updates, and data backups.

Once you have hardened the system as much as you can, please follow good privacy and security practices:

* Disable or remove things you don't need to minimise attack surface.
    
* Stay updated. Configure a cron job or init script to update the system daily.
    
* Don't leak any information about the system, no matter how minor it may seem.
    
* Follow general security and privacy advice.
    

#### Shout-outs and courtesy notes

* Image by [OpenClipart-Vectors](https://pixabay.com/users/openclipart-vectors-30363/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=149971) from [Pixabay](https://pixabay.com//?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=149971)
    
* Image by [StockSnap](https://pixabay.com/users/stocksnap-894430/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=926837) from [Pixabay](https://pixabay.com//?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=926837)
    
* Image by [Stefan Schweihofer](https://pixabay.com/users/stux-12364/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=1072366) from [Pixabay](https://pixabay.com//?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=1072366)
    
* Image by [Gerd Altmann](https://pixabay.com/users/geralt-9301/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=2679755) from [Pixabay](https://pixabay.com//?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=2679755)
    
* Image by Rahul from [Pixahive](https://pixahive.com/photo/drops-on-groot-character/)
    
* Image by [Jürgen Schmidtlein](https://pixabay.com/users/juergen_s-6938697/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=5420277) from [Pixabay](https://pixabay.com//?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=5420277)
    
* Image by [Alexa](https://pixabay.com/users/alexas_fotos-686414/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=1111448) from [Pixabay](https://pixabay.com//?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=1111448)
    
* Image by [Leopictures](https://pixabay.com/users/ulleo-1834854/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=2328289) from [Pixabay](https://pixabay.com//?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=2328289)
    
* Image by [422737](https://pixabay.com/users/422737-422737/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=445153) from [Pixabay](https://pixabay.com//?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=445153)
    
* Image by [Marjon Besteman](https://pixabay.com/users/marjonhorn-3698690/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=4393509) from [Pixabay](https://pixabay.com//?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=4393509)
    
* Image by [Pexels](https://pixabay.com/users/pexels-2286921/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=1846059) from [Pixabay](https://pixabay.com//?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=1846059)
    
* Image by [Joshua Woroniecki](https://pixabay.com/users/joshuaworoniecki-12734309/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=5089188) from [Pixabay](https://pixabay.com//?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=5089188)
    
* [https://xkcd.com/149/](https://xkcd.com/149/)