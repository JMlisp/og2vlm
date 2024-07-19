
# OpenGenera Installation Procedure V4.0


Table of Contents:

- [Install VirtualBox and Debian onto your PC or Mac](#install-virtualbox-and-debian-onto-your-pc-or-mac)
- [Install Guest Additions onto VirtualBox Debian](#install-guest-additions-onto-virtualbox-debian)
- [Make macOS ready for OpenGenera](#make-macos-ready-for-opengenera)
- [Make Debian ready for OpenGenera](#make-debian-ready-for-opengenera)
- [Prepare Debian and OG2 to host the LMFS system](#prepare-debian-and-og2-to-host-the-lmfs-system)
- [Attach FEP disks and create a LMFS partition](#attach-fep-disks-and-create-a-lmfs-partition)
- [Activate the LMFS system at Boot Time and Create the Initial world](#activate-the-lmfs-system-at-boot-time-and-create-the-initial-world)
- [Prepare to create a Symbolics World](#prepare-to-create-a-symbolics-world)
- [Create a Symbolics world containing the most important systems](#create-a-symbolics-world-containing-the-most-important-systems)
- [Let OpenGenera talk to the Internet via the virtual TAP device](#let-opengenera-talk-to-the-internet-via-the-virtual-tap-device)
- [Emulate a Symbolics space-cadet keyboard using a Mac or PC keyboard](#emulate-a-symbolics-space-cadet-keyboard-using-a-mac-or-pc-keyboard)
- [Send the Lisp Listener screen to the X Server of a LAN host](#send-the-lisp-listener-screen-to-the-x-server-of-a-lan-host)
- [Install and set up a Telnet service onto a Debian-based host](#install-and-set-up-a-telnet-service-onto-a-debian-based-host)
- [Connect from OG2 to its Debian-based Genera-host via Telnet](#connect-from-og2-to-its-debian-based-genera-host-via-telnet)


Overview:

This procedure describes how to install OpenGenera (OG2) onto Debian Linux, which can be run either as a Virtual Machine (VM) in VirtualBox or on its own disk or partition, i.e it  details how to emulate a Symbolics Virtual Lisp Machine (VLM) and run in unprivileged mode a tap-controlled, purely LMFS-based, and NFSv3-capable OpenGenera (OG2) in Debian, either on macOS or on Windows hosts. 
 
If Debian runs on its own disk or partition, proceed as follows, ignoring any section and steps related to VirtualBox (VB) and VMs. Notice, however, that the description provided below is based on a sequential approach. That is, in order for a specific section to be successfully implemented, the steps of all the previous relevant sections must have been fully and correctly implemented. I.e, proceed step by step, as follows:
  
  
## Install VirtualBox and Debian onto your PC or Mac
  
On amd64, preferably install VirtualBox (VB) V5.2.30 and Debian V9.4, being both the latest versions validated with OG2, and all necessary drivers and packages you usually require for a development environment. We will assume that you have already created a user and, for the reminder of this document, the username used will be "jm"; and the default umask of "0022" is applied to "jm" and "root".  
  
For the VB installation, the following initial settings and options are recommended:  
General:        Name Jupiter, OS Debian (64-bit), Shared Clipboard Bidirectional.  
System:         Base Memory 4096 MB, but at least 2048 MB, else keep defaults.  
                      Give VirtualBox 2 Processors, available to Debian VM and OG2.  
Display:        Video Memory 128 MB, Enable 3D Acceleration, else keep defaults.  
Storage:       ≥ 20GB fixed Disk, Type VMDK (e.g / 12GB, /home 8GB, /swap rest GB).  
 better:         = 40GB fixed Disk, Type VMDK (e.g / 12GB, /home 28GB, /swap rest GB).  
Network:      1 Intel PRO/1000 MT Desktop Adapter, bridged either to eth1 on PC, to  
                      en0: Wi-Fi (AirPort) or to a USB/Ethernet Adapter on Mac, or to what ever  
                      is the first hardware network interface of your system.  
USB:             Leave unchanged to USB2.  
  
Into the Debian base installation, initially only include LXDE, the SSH server and Standard system utilities.  
  
Start and log into the Jupiter Debian Virtual Machine (VM), if not already running:  
$  Login:  **jm**  
password:  ********  
 
| Adjust Preferences: |	Modify settings as appropriate: | Parameter: | Value:  | Comment: |
|:--- |:--- |:--- |:--- |:--- |
| Desktop Preferences:| Text Font of label text: |   | Sans 8 |   |
| Screensaver Preferences: | Fiberlamp Blank After: |   | 30 minutes |   |
| Configure: | Internet > | Wicd Network Manager: |   |   |
| Properties: | Use Static IPs: | IP: | 182.168.0.85 |   |
|   |   | Netmask: | 255.255.255.0 |   |
|   |   | Gateway: | 192.168.0.1 | as of your router’s IP |
|   |   | DNS domain: | 192.168.0.1 | same as of Gateway |
| Preferences: | Wired interface: | enp0s3 or enp3s0 |   | See the NOTE below: |
 
**NOTE**:   On systems using predictable network interface names such as Debian 9, the first wired NIC usually is called enp0s3 and the second enp0s8.  

Also note that the following description has been prepared with the aim to run Debian 8 or 9 in VirtualBox as Guest OS on PC and Macs. I.e, the interfaces used in the following are eth1 and eth2 in Debian 8 and, enp0s3 and enp0s8 in Debian 9, respectively. Thus, if you haven't installed Debian 9 but Debian 10, and you do run it on its own disk or partition, the first wired NIC will be called enp3s0 and similarily the second. Therefore, be prepared to give the appropriate name to the interface you want to use via the Wicd Network Manager, and to replace occurrences of those interface names in the system configuration files /etc/hosts, /etc/network/interfaces, and the IPTABLES provided further below, using the appropriate interface names for the Debian OS in use.
		
**Else**:   Keep any other settings unchanged.  
 
When finished:	Click Disconnect and Connect. The wired network interface IP is now: 192.168.0.85. Notice, however, this IP will be persistent across reboots only after /etc/network/interfaces has been set up accordingly in section "Make Debian ready for OpenGenera".  
  
Start Debian’s xterm (System Tools > LXTerminal)  
  
Change to root:  
$  **su  -**  
password:  **root-password**  
  
Initially, keep /etc/apt/sources.list as provided by Debian.  
 root: **apt-get update**  
  
In Devices > Optical Drives:   Mount the Debian-x.y.z-amd64-DVD-1.iso  
  
Minimise window "Removable media is inserted".  
  
Install onto Debian the following packages:  
root:  **apt-get install xinetd nfs-kernel-server obmenu vnc4server xvnc4viewer uml-utilities bridge-utils**  
 
Also recommended to install are the KDE editor “Kate” and File Manager “Dolphin”:  
root:  **apt-get install kate dolphin**  
 
Upon installation completion, proceed as follows:  
  
Open the pop-up window if minimised, click OK to enter in the File Manager, eject the Debian ISO image by clicking left on Debian-x.y.z.  
  
| Start: | System Tools > Dolphin |   |
| --- | --- | --- |
| Configure Dolphin: | Modify settings: | As appropriate: |
| Select: | Details |   |
| Open: | Control > Configure Dolphin |
|   | Startup: | Editable location bar |
|   |   | Show full path inside location bar |
|   | General: | Use common properties for all folders |
| Folders: |   | Alphabetical sorting, case insensitive |
| When finished: |   | Click Apply and exit Dolphin |
  
Close LXTerminal and shut down the Debian VM.  
  
  
## Install Guest Additions onto VirtualBox Debian
  
Benefit from additional features by installing Guest Additions onto a Debian running in VirtualBox. Among these features there is a better mouse integration and a better video support, allowing you to resize windows to the size your applications require.  
  
Cold boot Debian host Jupiter  
$  Login:  **jm**  
password:  ********  
  
In LXTerminal (xterm) enter:  
$  **su  -**  
password:  **root-password**  
  
Mount the Debian-x.y.z-amd64-DVD-1.iso onto the VirtualBox’s Optical Drive and issue the following commands in a xterm as root, while, if requested to open the removable medium in the File Manager, don’t do so, but for now only minimise the pop-up window.  
  
If necessary:  
root:  **apt-get update**  
  
Retrieve the kernel version:  
root:  **uname  -r**  
4.9.0-4-amd64                              # kernel version at the time of writing
 
Install the following packages but with the linux headers for the specific kernel:  
root:  **apt-get install dkms build-essential linux-headers-$(uname -r)**  
  
        Should the above fail with following error, proceed as described below:  
        vboxadd.sh: failed: Look at /var/log/vboxadd-install.log to find out what  
        went wrong.  
        vboxadd.sh: failed: modprobe vboxguest failed.  
        .....  
        .....  
        Purge the packages previously installed, including all linux headers:  
        root:  apt-get purge dkms build-essential linux-headers-*  
  
        Unmount the Debian ISO by clicking left Debian x.y.z, close any  
        open windows, reboot Debian, mount the Debian ISO, and try again.  
  
Upon installation completion,  close LXTerminal and proceed as follows:  
  
Open the pop-up window if minimised, click OK to enter in the File Manager, eject the Debian ISO image by clicking left on Debian-x.y.z, and reboot the Debian VM.  
$  Login:  **jm**  
password:  ********  
 
Open the VirtualBox menu “Devices”, click left on “Insert Guest Additions CD image…”, and click OK to mount VBoxAdditions.  
  
Once done, in a LXTerminal (xterm) enter:  
$  **su  -**  
password:  **root-password**  
  
root:  **cd  /media/cdrom0/**  
  
Install VirtualBox Linux Guest Additions:  
root:  **sh  ./VBoxLinuxAdditions.run**       ; Should this fail, reboot Debian and try again.  
 
Following completion of “Building Guest Additions kernel modules” and “Starting the VirtualBox Guest Additions” it is recommended to restart Debian. I.e, in xterm enter:  
root:  **cd**  
  
Close xterm, eject VBoxAdditions from File Manager, close the File Manager, make sure that the Optical Drive in Oracle’s VM VirtualBox Manager storage area is empty, and reboot Jupiter.  
$  Login:  **jm**  
password:  ********  
  
Adjust the window size of the Jupiter Debian VM as you wish it to be, and proceed with the sections necessary for your system.  
  
  
## Make macOS ready for OpenGenera
  
From website www.xquartz.org download and install XQuartz-2.7.11.dmg.  
  
On a MBP with Touch Bar, adjust the settings required to let the function keys be displayed permanently in the Touch Bar while running Virtual Machines (VMs):  
  
In System Preferences, choose Keyboard,  
- Click Shortcuts,
- From the left sidebar, select Function Keys,
- Click the “+” symbol, then navigate to the VirtualBox app and select it. It will be added to the Function Keys list of exceptions.  
  
Then proceed as follows:  
- Open the Finder window Applications,
- Find and right-click the VirtualBox app,
- Select Show Package Contents,
- Expand the Contents folder,
- Drag the Resources folder into the Finder’s Favorites sidebar,
- Using the Resources folder you put in Favorites, add the apps VirtualBoxVM and vmstarter to the Function Keys list of exceptions, as done for the VirtualBox app.  
  
Finally, add the following two apps to the Function Keys list of exceptions:  
- XQuartz
- Terminal  
  
Close System Preferences and remove the Resources shortcut you added to Favorites.  
  
Set up Macs to use Genera fonts, and the XQuartz sever to allow X client connections:  
  
Unzip genera.zip and copy the Genera Fonts in macOS folder /usr/X11/share/fonts/genera.  
  
Your Genera-host has been called “jupiter”. Assuming the host to run OG2 will be called "kronos", in your Mac’s Terminal (xterm) Preferences>Profiles>Basic>Shell check “Run inside shell” and “Run command” and in the command field include the following:  
  
xhost +kronos +jupiter; xset +fp /usr/X11/share/fonts/genera     ; this will also start XQuartz once xterm gets started.  
  
Preferably in “Prompt/Ask before closing”, include telnet, ssh, rlogin and slogin. And, in Preferences>Profiles>Basic>Advanced>Terminfo, Declare terminal as: xterm  
  
Use Mac’s OS Terminal to create a Genera fonts.dir file for the XQuartz server:  
$ **sudo  mkfontdir  /usr/X11/share/fonts/genera**  
 
Finally, in XQuartz > X11 Preferences select the options:  
- Input:                 Enable key equivalents under X11
- Security:            Authenticate connections, and  
                           Allow connections from network clients
  
As next, let macOS use the keyboard shortcut Ctrl-Shift-Q as "Lock Screen" in place of Ctrl-Command-Q, which, when the Genera screen is sent to the XQuartz server, in Zmacs translates to c-m-Q, i.e, the keyboard accelerator "Indent Sexp", used to correct the indentation of following lines with respect to the current line. Do this as follows:  
  
In System Preferences, choose Keyboard,  
- Click Shortcuts,
- From the left sidebar, select App Shortcuts,
- Click the “+” symbol, and for "All Applications" use:
- Menu Title: Lock Screen
- Keyboard Shortcut:    Ctrl-Shift-Q  
That's all, go back and close System Preferences.   
  
As next, prepare macOS to export User filesystem folders via NFS. I.e for a macOS User "jm", a OG2 host Kronos and a Genera-host Jupiter, create or update the file /etc/exports using the Mac’s Terminal (xterm) to include an entry as shown below:  
  
/Users -alldirs host1 host2 host3 .... kronos jupiter -mapall=jm  
 
Notice, however, changes in the “Transparency, Consent, and Control” (TCC) framework in macOS 10.15 (Catalina) affected every local dev stack that utilised NFS for file sharing. In particular, TCC treats the folders Documents, Downloads and Desktop with special care, requiring the user’s explicit consent to allow access to apps. In our case, the app is "nfsd" (the NFS daemon), which does not have privileges to access user folders by default. So, to run OG2 in a Debian VM under macOS Catalina and allow access to user files and folders from within Debian and OG2 via NFS, do the following:  
  
- Open macOS Catalina's System Preferences
- Go to Security & Privacy > Privacy > Full Disk Access
- Click the lock to make changes
- Enter your password and press Unlock
- Click +
- Press Command-Shift-G
- In the field "Go to the folder" enter: /sbin/nfsd and click Go
- Click Open
- Finally, click the lock to prevent further changes. This will allow "nfsd" to access Files and Folders on your mac's disks.  
  
Reboot the Mac to activate NFS.  
  
  
## Make Debian ready for OpenGenera
 
Start and log into the Debian VM Jupiter, if not already running:  
$  Login:  **jm**  
password:  ********  
  
In LXTerminal (xterm) enter:  
$  **su  -**  
password:  **root-password**  
  
Edit /etc/xinetd.d/daytime and replace all occurrences of "disable = yes" with "disable = no"  
  
Edit /etc/xinetd.d/time and replace all occurrences of "disable = yes" with "disable = no"  
  
root: **service xinetd restart**  
  
Configure the /etc/exports, /etc/network/interfaces, and /etc/hosts files of the Debian host Jupiter so that it can act as the Kronos VLM's Genera-host and access other hosts on the local area network (LAN):  
  
**- Debian file /etc/exports:**  
In /etc/exports include the following. If /home resides on a separate filesystem, you will have to list it explicitly in /etc/exports, as shown below:  
  
/                kronos(rw,sync,no_root_squash,no_subtree_check)  
/home      *(rw,sync,no_root_squash,no_subtree_check)  
  
**- Debian file /etc/network/interfaces:**  
In Debian 8 configure the NICs eth0 and eth1 to suit your network topology. On systems using Predictable Network Interface Names, i.e Debian 9 or Debian 10, as explained in the previous NOTE, use instead the NIC names enp0s3, enp0s8, or enp3s0, as appropriate.  
  
**NOTE**:	You only need a single VM NIC for networking between Genera and Genera-host and enable Genera talk to other Internet hosts. As shown below, this NIC, can be either dynamic (DHCP) or static. If static, assign to it an IP address of your LAN address space. If dynamic, you must, however, set up your router as appropriate to assign to the NIC always the same IP address.  
  
Here is an example of how you could set up your network. I.e, in  /etc/network/interfaces include the following:  
  
**#** This file describes the network interfaces available on your system and how to activate them. For more information, see interfaces(5).  
  
source /etc/network/interfaces.d/*  
**#** The loopback network interface  
auto lo eth0 eth1                               # Debian 9 onwards, use “auto lo” only  
iface lo inet loopback  
**#** The primary network interface     # Debian 9 onwards, replace eth0 by enp0s3 or by enp3s0, as appropriate  
allow-hotplug eth0  
**#** iface eth0 inet dhcp                      # To use a dynamic NIC, uncomment this line,  
iface eth0 inet static                         # and comment the whole static configuration  
         address 192.168.0.85  
         netmask 255.255.255.0  
         gateway 192.168.0.1  
         up echo 1  
  
**- Debian file /etc/hosts:**  
Prepare to let the Debian host Jupiter become the genera-host of the OG2 VLM Kronos. I.e, in Jupiter's /etc/hosts include the following:  
  
**#** Entries for OG2 VLMs  
192.168.6.2     kronos                      # OG2 host's IP address and hostname  
  
**#** Entries for LAN hosts  
192.168.0.5     server                      # PC's or Mac's IP address and hostname  
192.168.0.85    jupiter                     # Debian host’s IP address and hostname  
  
As next edit /etc/sudoers and replace "%sudo ALL=(ALL:ALL) ALL" with "%sudo ALL=NOPASSWD: ALL"  
  
Also edit /etc/group and place "jm" in the sudo group: "sudo:x:27:jm"  
  
Change back to User mode:  
root: **su - jm**  
 
From now on, all the following actions will be run as "jm". Verify that "jm" is in the proper sudo group, otherwise logout and login again.  
 

## Prepare Debian and OG2 to host the LMFS system
 
Decompress the archive og2vlm-kit.zip into the folder $HOME/Downloads/. The archive og2vlm-kit.zip provides a ready to use OG2 distribution world named Genera-8-5e, which includes both NFSv3 Client and LMFS capability for the VLM, translations files, genera binaries for X86 and Xeon based platforms, as well as functions and commands necessary to create FEP disks, a LMFS partition, and an Initial world, without the need to disable Debian’s calendar clock prior to the installation and the subsequent launch of OpenGenera.  
   
I.e, on top of the original Symbolics distribution Genera-8-5, Genera-8-5e replaces the body of the function xlib:set-modifier-mapping by a no-op, extends the NFSv2 client, which almost is not more supported by the recent Linux operating systems, by a new NFSv3 client, and makes available to the user functions and commands to attach FEP disks, (up to 32 in total), initialise a LMFS partition and much more. See section “Attach FEP disks and create a LMFS partition” for details.  
  
Verify the archive’s og2vlm-kit.zip checksum:  
MD5 (og2vlm-kit.zip) = bd16f45374adfba7fd5035d3668fcd7e
  
Using Debian’s Xarchiver, extract the archive og2vlm-kit.zip. Its content will be made available in the  $HOME/Downloads/og2vlm/ folder.  
  
Move the items in og2vlm to $HOME/Downloads/:  
$  **mv $HOME/Downloads/og2vlm/*** **~/Downloads/**
  
Purge the empty og2vlm folder:  
$  **rmdir $HOME/Downloads/og2vlm**
  
Create a folder "vlm" in your $HOME directory and proceed as follows:  
$  **mkdir ~/vlm**  
  
$  **mv  $HOME/Downloads/Genera-8-5e.vlod  ~/vlm/**  
  
$  **mv  $HOME/Downloads/VLM_debugger  ~/vlm/**  
  
Depending on the platform's architecture, X86 or Xeon based, either do:  
$  **rm  $HOME/Downloads/genera-xeon**  
$  **mv  $HOME/Downloads/genera-x86  ~/vlm/genera**  
or:  
$  **rm  $HOME/Downloads/genera-x86**  
$  **mv  $HOME/Downloads/genera-xeon  ~/vlm/genera**  
  
$  **mv  $HOME/Downloads/dot.VLM  ~/vlm/**  
$  **mv  ~/vlm/dot.VLM ~/vlm/.VLM**  
  
Update .VLM to point to Genera-8-5e.vlod  
  
Make the genera binary executable:  
$  **chmod  755  ~/vlm/genera**  
  
Retrieve the OG2 tarball opengenera2.tar.bz2 from [INTERNET ARCHIVE](https://archive.org/details/SymblicsOpenGenera) repository.  
  
Verify the og2 tarball's checksum:  
MD5 (opengenera2.tar.bz2)  =  753411fbf964b15369b3a46997100ffa  
  
Unpack og2 tarball into its destination folder $HOME/og2:  
$  **tar  xvjf  $HOME/Downloads/opengenera2.tar.bz2**
  
Change og2 folder and file permissions:  
$  **sudo  chmod  -R  777  ~/og2**  
  
Verify Debian’s /etc/hosts includes KRONOS’s IP address:  
192.168.6.2     kronos  
  
Add Genera fonts and Meta-key to OpenGenera Linux VLM, i.e proceed as follows:  
 
Using Debian’s Xarchiver, unzip the archive genera.zip  
$  **cd  /usr/share/fonts/**  
$  **sudo  mkdir  genera**  
$  **sudo  cp  $HOME/Downloads/genera/**__*__.__*__  **./genera/**  
$  **cd**
  
Create a genera fonts.dir file for the X server:  
$  **sudo  mkfontdir  /usr/share/fonts/genera**
  
Activate Meta_L and genera fonts on startup:  
$  **cat  >>  ~/.bashrc  <<  EOF**
  
**#** Remap Alt_L to Meta_L for OpenGenera VLM:  
xmodmap -e "keysym Alt_L = Meta_L Alt_L"  
xmodmap -e "add mod1 = Meta_L"  
  
**#** Add genera fonts to the X server font-path:  
xset fp+ /usr/share/fonts/genera  
  
**#** extend default Debian PATH variable  
PATH=$PATH:/sbin:/usr/sbin  
export PATH  
**EOF**  
   
Copy from folder $HOME/Downloads/ the appropriate file for your keyboard into $HOME. I.e one of the following:  

| Xmodmap type: | Purpose: |
| --- | --- |
| xmodmap.smbx | to use a real Symbolics space-cadet keyboard |
| xmodmap.macx | for standard Mac or MacBook Pro US keyboards |
| xmodmap.pcukx | to use on PCs with a standard PC UK Keyboard |
|   | on PCs with US Keyboards, modify as required |
  
And move the following items to the destinations indicated below:  
$  **mv  ~/Downloads/foo.***  **./Documents/**  
$  **mv  ~/Downloads/og2vlm.sh  .**  
   
Make the shellscript "og2vlm.sh" executable:  
$  **chmod  +x  og2vlm.sh**  
  
Besides NFSv3 and higher versions, prepare the Genera-host Jupiter to support whenever required also NFSv2, which Debian 9 onwards, even if included, is disabled by default:
$  **sudo  cat  /proc/fs/nfsd/versions**  
-2 +3 +4 +4.1 +4.2                              <— NFSv2 is disabled in Debian 9 !!  
   
Enable NFSv2, as follows:  
$  **sudo  nano  /etc/default/nfs-kernel-server**  
   
Replace:  
RPCNFSDCOUNT=8  
by:  
RPCNFSDCOUNT="8 --nfs-version 2"  
  
Restart the nfs-kernel-server:  
$  **sudo  systemctl  restart  nfs-kernel-server**
  
Verify NFS2 availability:  
$  **sudo  cat  /proc/fs/nfsd/versions**  
+2 +3 +4 +4.1 +4.2                              <— NFS2 now enabled in Debian 9 !!  
  
Close the LXTerminal and shut down the Debian VM Jupiter.  
  
  
## Attach FEP disks and create a LMFS partition
  
Boot the Debian host Jupiter and log in as "jm".  
 
Start OG2 for the first time, i.e in LXTerminal enter:  
$  **xmodmap  ~/xmodmap.macx**  
$  **sudo  tunctl  -u  jm**  
$  **sudo  ifconfig  tap0  192.168.6.1  up**  
$  **cd  ~/vlm**  
$  **./genera  -network  "tap0:INTERNET|192.168.6.2;gateway=192.168.6.1"**  
  
command:  **login lisp-machine**  

Toggle more processing, i.e enter:  
**Function M**          # key in F3 followed by M  
  
Create and attach FEP disks:  
  
**NOTE**:  To create FEP disks bigger than 2 GB, use the command keyword :minimum-blocks, instead of :minimum-length. A FEP block equals to 2048 4xByte-Words = 8192 Bytes.  
  
Thus, using :minimum-length, the values below will create a FEP disk of 2 GB:  
command:  **(storage:attach-disk-channel (fs:parse-pathname “HOST:/home/jm/vlm/fep0.dsk”) :minimum-length** __(* 4 2048 250000))__  
0                                       ; returned FEP unit Number, i.e a next FEP disk will be 1.  
 
Otherwise, using :minimum-blocks, a value of 1250000, as shown below, will create a FEP disk of 10 GB:  
command:  **(storage:attach-disk-channel (fs:parse-pathname “HOST:/home/jm/vlm/fep0.dsk”) :minimum-blocks 1250000)**  
0                                       ; the returned FEP unit Number  
  
Then, proceed as follows:  
command:  **Create Initial FEP Filesystem (FEP unit Number) 0**     ; see returned values:  
size 250000, cal 31250, heads 1, s/t 8, bs 2048                               ; for a 2 GB FEP disk  
size 1250000, cyl 156250, heads 1, s/t 8, bs 2048                           ; for a 10 GB FEP disk
  
See the Initial FEP Filesystem files:  
command:  **Show Directory** **fep0:>**__*__.__*__**.newest**  
  
Enter the File System Editing operations (FSE):  
**Select  F**                        ; key in F1 followed by F  
  
Go to Level 3 and proceed until completion with the following steps to initialise the LMFS Partition, for example, as shown in [LMFS-Partition-Initialisation](https://github.com/JMlisp/og2vlm/blob/main/screenshots/LMFS-Partition-Initialisation.png), enter:  
  
Local LMFS Operations > LMFS Maintenance Operations > Initialize  
Initialize Partition Location [default FEP0:>lmfs.file.newest]: **return**  
Partition FEP0:>lmfs.file.newest does not now exist. It will be created.  
Number of FEPS blocks to allocate? 125000       ; 125000 equals to 1GB LMFS  
Created FEP0:>lmfs.file.1.                                      ; 625000 equals to 5GB LMFS  
  
Proceeding with initialisation of partition in FEP0:>lmfs.file.1  
Zeroing FEP0:>lmfs.file.1, 125000 blocks.            ; if 625000 blocks, i.e 5GB LMFS partition  
Partition initialisation complete.  
Command:  
  
Exit Levels 3 and 2, i.e go back to the initial level and proceed, as follows:  
Tree Edit Root                                    ; Click left to show the top-level LMFS directory  
DIS-LOCAL-HOST:>__*__.__*__.__*__                  ; Create Inferior Directory by right-clicking the  
**jm**                                                        ; current directory, key in the name, and Return.
  
**Select L**          ; key in F1 followed by L  
  
Copy from $HOME/Documents/ any two files into the folder jm as a test:  
command:  **copy  file  DIS-EMB-HOST:/home/jm/Documents/foo.***   **DIS-LOCAL-HOST:>jm>**  
The Files copied are shown.  
  
Warm-shutdown FEP disks:  
command:  **(lmfs:shut)**  
NIL  
  
Detach before OG2 shutdown:  
command:  **(storage:detach-disk-channel  0)**  
T  
  
Shut down in order to reboot OG2, i.e enter:  
command:  **halt  genera  :query  no**  
  
  
## Activate the LMFS system at Boot Time and Create the Initial world
  
From within the same LXTerminal folder ~/vlm, enter:  
$  **./genera  -network  "tap0:INTERNET|192.168.6.2;gateway=192.168.6.1"**  
  
command:  **login  lisp-machine**  
 
**Function M**          # key in F3 followed by M  
  
Attach the disk FEP0:  
command:  **(storage:attach-disk-channel  (fs:parse-pathname  “HOST:/home/jm/vlm/fep0.dsk”))**  
0  
  
Define your Site as "home". Note, If a different Site name is used, translations-files required later on must be updated as appropriate:  
command:  **Define  Site  (site name)  home**  
Defining site HOME with the local host as the Primary Namespace Server  
Namespace Server Name: **kronos**  
System File Directory: **local:>sys>site>**  
Namespace Descriptor File: **local:>sys>site>home-namespace.text**  
Unix Host Name: **jupiter**  
Default Login: **Lisp-Machine**  
Host for Bug Reports: **kronos**  
Local Timezone: **+0100**          ; +0200 if summertime  
Standalone Site: **Yes**  
<Abort> aborts, <END> uses these values  
  
After defining a site, revoke mapping of :MULTI-KEY-CHARACTER-PREFACE (Compose Character) key to #\Function caused in SYS:X11;CLX;KEYSYMS:  
command:  **load  file  jupiter:/home/jm/Downloads/undefine-keysym.lisp**  
Loading JUPITER:/home/jm/Downloads/undefine-keysym.lisp into package XLIB  
  
Verify/update host Jupiter, as follows:  
command:  **edit  namespace  object  host  jupiter**  
  
- IP address **INTERNET 192.168.6.1**          ; the gateway address defined in .VLM  
  
Optionally specify:  
- Short Name: **j**
- Pretty Name: **JUPITER**
- Server Machine: **Yes**
- File Control Lifetime: **1800 seconds**
  
Left click on: Save Object and enter:  
Save Object HOST JUPITER? **Y**
 
**Select  L**          ; back to the Lisp Listener  
  
Verify/update host KRONOS, as follows:  
command:  **edit  namespace  object  host  kronos**  
 
- IP address INTERNET 192.168.6.2 (Interface) tap0 
- Short Name: k
- Pretty Name: KRONOS
- Server Machine: Yes
- File Control Lifetime: 1800 seconds  
  
Left click on: **Save Object**  
Save Object HOST KRONOS? **Y**  
 
**Select  L**          ; back to the Lisp Listener  
  
Proceed as follows:  
command:  **(si:run-gc-cleanups)**  
command:  **(si:full-gc)**                     ; this step takes some time. Avoid interference!!  
  
Load the file required to Add OG2 Initialization Lists onto OG2:  
command:  **load  file  j:/home/jm/Downloads/loadfepdisks**  
  
Save the current world as:  
command:  **save  world (Default ....) Initial.vlod**  
**Y**  
**Y**  
command:  **halt  genera  :query  no**  
  
Update .VLM to point to Initial.vlod  
  
The [Initial](https://github.com/JMlisp/og2vlm/blob/main/screenshots/Initial.png) screenshot shows the initial screen of the Jupiter-Kronos Initial world.  
  
Close LXTerminal, shutdown Jupiter, and optionally backup the Initial world by creating a VM Initial.ova.  
  
  
## Prepare to create a Symbolics World
   
Boot the Debian host Jupiter and log in as "jm".  
  
Start OG2 in unprivileged mode, using the shellscript “og2vlm.sh” containing all the commands required to create and make persistent an interface tap0 and launch the genera binary from within the folder $HOME/vlm. I.e, in LXTerminal enter:  
$  **./og2vlm.sh**  
  
command:  **login  lisp-machine**  
**Function M**          # key in F3 followed by M  
  
Verify that following commands return system initialisation lists as last elements, regarding FEP disk-attachment and detachment:
  
command:  **Show Initialization List warm**  
command:  **Show Initialization List before-cold**  
command:  **Show Initialization List system-shutdown**  
command:  **Show Initialization List system**            ; includes user disk initialisation  
command:  **copy  file  j:/home/jm/Downloads/nfsv3.translations  sys:site;**  
command:  **load  file  sys:site;nfsv3.translations.newest**  
command:  **copy  file  j:/home/jm/Downloads/s.translations  sys:site;**  
command:  **copy  file  j:/home/jm/og2/sys.sct/site/*.translations  sys:site;**  
command:  **load  file  j:/home/jm/Downloads/og2-translations.lisp**  
  
Restore the OG2_SOURCE_DIST.TAPE using:  
command:  **select  activity  restore  distribution (...[default (..... (Genera on Kronos)])**  
 
Set:                        Read distribution from device:	**Disk**  
Spec for tape:       **J:/home/jm/og2/DISTRIBUTION/OG2_SOURCE_DIST.TAPE;1**  
Click left on:          **Initialize Restoration**  
 
Systems to Restore:     SYSTEMS to restore are shown
  
Click left on:                 **Perform Restoration**          ; This takes some time  
	
Upon completion enter:  
**Select  L**  
  
Revert the file sys.translations to its initial content:  
command:  **copy  file  sys:site;sys.translations.1  sys:site;sys.translations.3**  
  
Compile the script necessary to create the Symbolics world:  
command:  **compile  file  (File)  j:/home/jm/Downloads/w85.lisp**  
  
command:  **halt  genera  :query  no**  
  
  
## Create a Symbolics world containing the most important systems
  
Reboot the Debian host Jupiter and log in as "jm".  

Create a folder symbolics with the following attributes:  
$ **sudo mkdir /var/lib/symbolics**  
$ **sudo chown jm:jm /var/lib/symbolics**  
$ **chmod 777 /var/lib/symbolics**  
  
Start OG2 VLM, i.e in LXTerminal enter:  
$  **./og2vlm.sh**  
  
command:  **login   lisp-machine**  
**Function M**          # key in F3 followed by M  
  
command:  **load  file  sys:site;nfsv3.translations.newest**  
command:  **(setq  rpc::*unix-check-passwords-for-show*  nil)**  
command:  **Load  File  j:/home/jm/Downloads/w85**  
  
Execute w85 by calling:  
command:  **(wform)**
  
Move the mouse pointer over the left (PROGN ....) parenthesis, click left, and place it such that highlighting Lisp types is avoided while the form is being executed. This will reduce the size of the world and, therefore, the final GC execution time.  
  
On completion, save the current world as:  
command:  **save  world  Symbolics.vlod**  
  
Accept the proposed world title and Herald by typing "**Y**"  
  
Wait until the file Symbolics world has been written to disk and the Genera X screen is redisplayed. Then, halt genera:  
command:  **halt  genera  :query  no**  
  
Update .VLM to point to Symbolics.vlod  
  
The [Symbolics](https://github.com/JMlisp/og2vlm/blob/main/screenshots/Symbolics.png) screenshot shows the initial screen and [Kronos-lmfs-tree](https://github.com/JMlisp/og2vlm/blob/main/screenshots/Kronos-lmfs-tree.png) the first part of the "Tree Edit Root" directory listing of the Jupiter-Kronos Symbolics world.  
  
Shutdown Debian, optionally backup the Symbolics world, by creating a VM Symbolics.ova.  
  
  
## Let OpenGenera talk to the Internet via the virtual TAP device
  
Define Routing Tables in the Host OS and IPTABLES rules in the Guest OS:  
  
I.e, in a xterm on a MacOS SERVER enter the following commands to retrieve information necessary to set up Routing tables, and IPTABLES rules on JUPITER. That is, the first goal is to be able to unix-ping JUPITER and KRONOS from within the SERVER, and the other way around:  

On SERVER:  
$  **networksetup  -listallnetworkservices**  
An asterisk (*) denotes that a network service is disabled.  
Ethernet  
FireWire  
Wi-Fi  
Bluetooth PAN  
Thunderbolt Bridge  
  
On SERVER, for the networkservice Ethernet:  
$  **networksetup  -getinfo  Ethernet**  
Manual Configuration  
IP address: 192.168.0.5  
Subnet mask: 255.255.255.0  
Router: 192.168.0.1  
IPv6: Automatic  
IPv6 IP address: none  
IPv6 Router: none  
Ethernet Address: 10:dd:b1:cd:7b:8f  
  
**CASE:	MacOS HIGH SIERRA or higher:**  
Set up IPv4 additional routes configured for the networkservice, e.g for Ethernet, and persistent across reboots. Notice, each route is specified as a (destination address, subnet mask, gateway address) tuple. Specifying no tuples clears the list of routes:  
  
On SERVER, for the networkservice Ethernet:  
$  **sudo  networksetup  -setadditionalroutes  Ethernet 192.168.6.0 255.255.255.0 192.168.0.85**  
Password: ****
  
**CASE:	WINDOWS:**  
To add a route to KRONOS on JUPITER, specify as administrator in Windows Command Prompt on SERVER that all traffic bound for the destination subnet 192.168.6.0 shall be routed to the gateway 192.168.0.85, i.e to JUPITER, and make the new route persistent across reboots, using the option -p, as follows:  
**C:\Windows\system32>  route  -p  ADD  192.168.6.0  MASK  255.255.255.0  192.168.0.85**  
  
Boot Debian, log in, and depending on the Debian version you've installed, set up IPTABLES rules, using the interface name enp0s3 in Debian 9, and enp3s0 in Debian 10. I.e, open a LXTerminal and proceed as described below:  
  
Allow all loopback (lo0) traffic and drop all traffic to 127/8 that doesn't use lo0:  
$  **sudo  iptables  -A  INPUT  -i  lo  -j  ACCEPT**  
$  **sudo  iptables  -A  INPUT  !  -i  lo  -d  127.0.0.0/8  -j  REJECT**  
  
Accept all established inbound connections:  
$  **sudo  iptables  -A  INPUT -m  state  --state  ESTABLISHED,RELATED  -j  ACCEPT**  
  
Allow all outbound traffic (if required, modify to allow only certain traffic):  
$  **sudo  iptables  -A  OUTPUT  -j  ACCEPT**  
  
Don't touch packets if they're only from and to the internal network regardless of the protocol:  
$  **sudo  iptables  -t  nat  -A  POSTROUTING  -s  192.168/16  -d  192.168/16  -o  enp0s3  -j  ACCEPT**  
  
Else they're for the outside world (again regardless of the protocol):  
$  **sudo  iptables  -t  nat  -A  POSTROUTING  -o  enp0s3  -s  192.168/16  -j  SNAT  --to-source  192.168.0.85**  
  
Upon IPTABLES rules were set up, in the same xterm enter:  
$ **sudo  sysctl  -w  net.ipv4.ip_forward=1**  
  
and, make this configuration permanent, by uncommenting in /etc/sysctl.conf, or if not present, by including the line:  
**net.ipv4.ip_forward=1**            # which should have the value 1  
  
**In Debian 9:**  
Make the current live ruleset persistent, as follows. It will be activated asap, during Debian boot, what is the most secure way to do this, with respect to OS vulnerability.  
  
Mount the Debian-x.y.z-amd64-DVD-1.iso onto the VirtualBox’s Optical Drive.  
  
Install the package “iptables-persistent”, i.e in LXTerminal enter:  
$  **sudo  apt-get  install  iptables-persistent**  
  
Notice, installing iptables-persistent will offer to create files from the current live configuration while the package is installed. Accept to create the IPv4 ruleset in /etc/iptables/rules.v4. The file /etc/iptables/rules.v4 will contain the current live ruleset.  
  
Check its content using the following command:  
$  **cat  /etc/iptables/rules.v4**  
  
Remove the Debian ISO from Devices > Optical Drives > Remove disk from virtual drive.  
  
Now Reboot Debian and verify that above IPTABLES rules are activated, i.e enter:  
$  **sudo  iptables  -L**  
  
You should get following activated chains and policies:  
  
Chain INPUT (policy ACCEPT)  
target    prot opt source          destination  
ACCEPT  all  --   anywhere      anywhere  
REJECT   all  --  anywhere       loopback/8      reject-with icmp-port-unreachable  
ACCEPT  all  --  anywhere       anywhere        state RELATED,ESTABLISHED  
  
Chain FORWARD (policy ACCEPT)  
target    prot  opt source          destination  
  
Chain OUTPUT (policy ACCEPT)  
target     prot opt source         destination  
ACCEPT  all   --   anywhere    anywhere             
  
Verify the respective /etc/hosts on SERVER and Genera-host JUPITER include the following:  
 
192.168.6.2     kronos                  # OG2 host's IP address and hostname  
.......  
,,,,,,,  
192.168.0.5     server                   # PC's or Mac's IP address and hostname  
192.168.0.85    jupiter                 # Debian host’s IP address and hostname  
  
Start the VLM and verify it can access Internet hosts via NFS, i.e enter:  
$  **./og2vlm.sh**  
  
command:  **login  lisp-machine**  
**Function M**          # key in F3 followed by M  
  
Create a namespace object “server”, to enable the VLM KRONOS access the host SERVER (and the other way around), via the virtual interface tap0 and the genera-host JUPITER:  
command:  **create  namespace  object  host  server**  
  
In the Namespace Editor enter the following, substituting short-name, pretty-name and address internet, as appropriate for your LAN topology:  
  
FILE-CONTROL-LIFETIME 108000          ; to be entered as "1800 seconds"  
SERVER-MACHINE Yes  
SERVICE FILE TCP TCP-FTP  
SERVICE FILE UDP NFS  
SERVICE HARDCOPY TCP UNIX-LPD  
SERVICE LOGIN TCP TELNET  
SERVICE MAIL-TO-USER TCP SMTP  
SERVICE PRINTER-CONTROL TCP UNIX-LPD  
SERVICE PRINTER-QUEUE-CONTROL TCP UNIX-LPD  
SERVICE RPC UDP UDP-RPC  
SERVICE RPC TCP TCP-RPC  
SERVICE SEND TCP SMTP  
SERVICE TAPE TCP UNIX-REXEC  
SERVICE TIME UDP TIME-SIMPLE-MSB  
SERVICE UNIX-REXEC TCP UNIX-REXEC  
SERVICE X-WINDOW-SYSTEM TCP X-WINDOW-SYSTEM  
PRETTY-NAME SERVER  
ADDRESS INTERNET 192.168.0.5  
SYSTEM-TYPE UNIX42  
MACHINE-TYPE IBMPC  
SHORT-NAME S  
SITE HOME  
  
Click left on Save Object and confirm: Y  
**Select  L**  
  
Verify configuration and services of host SERVER, i.e enter:  
command:  **Show  Namespace  Object  (a namespace object ....)  Host  server**  
  
Ping SERVER from within KRONOS, i.e enter:  
command: **(tcp:send-icmp-echo :server)**      ; should return "T"  
  
On SERVER, to ping JUPITER and KRONOS the other way around, enter:  
$ **ping jupiter**  
$ **ping kronos**  
  
Enter other commands, for example:  
command:  **show  NFS  exports  server**  
command:  **show directory s:/Users/***  
....  
....  
  
Enter the File System Editor:  
**Select  F**  
  
Navigate around, i.e click "Tree Edit Any" and access the SERVER's exported Filesystem anonymously:  
Path to tree edit: (default ....) S:/Users/*  
....  
....  
**Select L**  
  
command:  **halt genera :query no**  
  
  
## Emulate a Symbolics space-cadet keyboard using a Mac or PC keyboard
  
For safety, save a copy of the actual Debian Xmodmap into your $HOME folder:  
$ **xmodmap -pke > xmodmap.deb**  

Re-enable shadowed OG2 accelerators by Debian LXDE shortcuts, among which the following:  
  
-  Avoid closing an active OG2 window via “Alt-F4”,  i.e OG2 “Meta-Suspend” used to enter the debugger while Lisp is waiting for user input.  
  
-  Enable Genera to use Meta-Complete by modifying the keybinding for Alt-F11.  
  
-  Enable c-m-D (Zmacs Forward Down Parentheses and Debugger command :Describe Last) by disabling LXDE shortcut Ctrl-Alt-D (minimise application window).  
  
-  Enable Genera accelerator m-Mouse-L (Edit Definition) by commenting LXDE Alt-Left mouse bindings.  
  
I.e, rename the original LXDE OpenBox config file:  
$  **mv  $HOME/.config/openbox/lxde-rc.xml  $HOME/.config/openbox/lxde-rc.xml-orig**  
  
and, move the modified LXDE OpenBox config file to this destination:  
$  **mv $HOME/Downloads/lxde-rc.xml  $HOME/.config/openbox/**  
  
Also notice, to be able to use the Genera keyboard accelerators c-m-A, c-m-P and c-m-F in Zmacs and the OG2 Debugger, do the following:  
  
Rename the original Debian CLIPIT config file:  
$  **mv  $HOME/.config/clipit/clipitrc  $HOME/.config/clipit/clipitrc-orig**  
  
Move the modified LXDE OpenBox config file to this destination:  
$  **mv  $HOME/Downloads/clipitrc  $HOME/.config/clipit/**  
  
Warm-boot OG2 VLM after the above changes, i.e in LXTerminal enter:  
$  **./og2vlm.sh**  
  
command:  **login   lisp-machine**  
**Function M**          # key in F3 followed by M  
  
If you are using a real Symbolics space-cadet keyboard on a Mac or PC enter:   
command:  **Show Keyboard Layout (a keyboard-layout) Symbolics**     ; to verify key strokes  
  
If you are using a standard Mac or PC keyboard, i.e with numeric keypad, Symbolics specific keys are located on the keys shown below:  
  
| Key: | Key Value: | Mod Key: | Mod Key Value: | Comment: |
|:--- |:--- |:--- |:--- |:--- |
| F1 | Select |   |   |   |
| F2 | Network |   |   |   |
| F3 | Function |   |   |   |
| F4 | Suspend |   |   |   |
| F5 | Resume |   |   |   |
| F6 | Abort |   |   |   |
| F7 | Super |   |   |   |
| F8 | Hyper |   |   |   |
| F9 | Scroll | Shift-F9 | Page up |   |   |
|   |   | Meta-F9 | Page up | If connected to OG2 over X |
| F10 | Clear Input | Shift-F10 | Refresh |   |
| F11 | Complete | Shift-F11 | End |   |
| F12 | Help | KP_5 | Symbol |   |
| Delete | Backspace | Shift-Delete | Delete | Mac and MBP US keyboards |
|   |   |   |   | If over X see Mac .Xmodmap |
  
**NOTE**:    Mac keyboards:  
Using the keys Complete (F11) and Help (F12) on Mac keyboards requires to remap the Mac OS Mission Control keys F11 and F12, for example to ⌥⌘F11 and ⌥⌘F12, respectively. Doing so, also Meta-Complete (Alt-F11), Control-Help (Ctrl-F12) and Symbol-Help (KP_5-F12) are available to the user. Accessing Genera from within a Mac OS Terminal via SSH or Telnet, requires to enable the keyboard shortcuts Control-Suspend (Ctrl-F4) and Control-Abort (Ctrl-F6) by unchecking the respective Mac OS flags ^F4 and ^F6 in System Preferences > Keyboard > Shortcuts > Keyboard>. Also notice that while accessing Genera via X11, the Meta key is bound to the Mac’s Command key (⌘) and not to the Alt key. Therefore, in order to use Meta-W (Command-W), i.e the shortcut for copying a Zmacs buffer region into the kill ring, remap the keyboard shortcut Command-W, used in Mac OS to close the front window, to Shift-Command-W in System Preferences > Keyboard > Shortcuts > App Shortcuts for the application XQuartz, Menu Title “Close”. Alternatively, as a workaround, you may of course mark a Zmacs buffer region using the mouse, press c-Mouse-R, select “Push masked text on kill ring”, and use Control-Y (Ctrl-Y) to yank it wherever required. Also remap the XQuartz shortcut Command-N, used in Mac OS to open a new X11 Terminal, to Shift-Command-N, thus to enable Genera’s Debugger command “Move down a frame, displaying detailed information about it” using the accelerator Meta-N (Command-N), when the Genera screen is sent to a Mac’s X11 server provided by XQuartz. Meta_R (Alt_R) has been remapped to Symbol, in favour of MacBook Pro keyboards, which don’t include a numeric keypad, i.e a KP_5 key acting as Symbol. When connected to OG2 from a Mac via X11, in order to remap Shift-Meta_R (Shift-Alt_R) to work as Shift-Symbol, Shift-Delete as Delete, and Command_L-F9 (Meta_L-F9) as Page up, create in your Mac OS home directory a .Xmodmap file with content as follows:  
  
**cat >> .Xmodmap << EOF**  
keycode  59 = BackSpace Delete BackSpace  
keycode  69 = KP_Begin KP_5 KP_Begin  
**EOF**  
  
Also notice that the current xmodmap versions don’t implement the key “Line”, equal to “Return+Tab” on emulated Symbolics space-cadet keyboards. Try out everything else, and compare the results to those displayed by the following commands:  
  
command:  **Show  Keyboard  Layout  (a keyboard-layout)  Apple Extended Keyboard II**  
and  
command:  **Show  Keyboard  Layout (a keyboard-layout)  NDS**        ; most closed to PC keyboards  
or enter “Control-Shift-?” after the aforementioned command, to select a different keyboard layout.  
  
Halt OG2 on Kronos:  
command:  **halt  genera  :query  no**  
  
Going back, you may wish to restore the initial Debian key mappings, using:  
$ **xmodmap ~/xmodmap.deb**     # restores the initial Debian key mappings.  
  
  
## Send the Lisp Listener screen to the X Server of a LAN host
  
Send the Dynamic Lisp Listener of the OG2 VLM Kronos to the X Server XQuartz of the macOS LAN host SERVER, as follows:  
  
Verify that /etc/hosts on SERVER includes the following entries:  
server:~ jm$ **cat /etc/hosts**  
........  
........  
192.168.6.2         kronos      # OG2 VLM's IP address and hostname  
192.168.0.85       jupiter      # Debian host’s IP address and hostname  
  
On SERVER, start xterm. Once started, it will open XQuartz, as well.  
  
Boot the Debian Genera-host Jupiter if not already running and log in as "jm".  
  
Verify that /etc/hosts on Jupiter includes the following entry:  
$ **cat /etc/hosts**  
........  
........  
192.168.0.5         server      # macOS host’s IP address and hostname  
  
Boot the OG2 VLM Kronos, i.e in an LXTerminal enter:  
$ **./og2vlm.sh**  
  
command: **login lisp-machine**  
**Function M**          # key in F3 followed by M  
  
Send the Kronos Dynamic Lisp Listener screen to the LAN host SERVER, specifying the appropriate screen geometry:  
command: **Start X screen (the name of a host) SERVER (keywords) :geometry (a string) 1280x960+640+120**  
A Kronos Dynamic Lisp Listener 2 is displayed on the macOS LAN host SERVER.  
  
Use the following Lisp Listener command and press Return to close the X screen and exit:  
command: **Halt X Screen (X screen or All [default X Screen SERVER:0.0 1 (Genera on KRONOS)])**  
  
Halt OG2 on Kronos:  
command:  **halt  genera  :query  no**  
  
  
## Install and set up a Telnet service onto a Debian-based host
  
Cold boot and log into Debian Genera-host Jupiter if not already running:  
$ Login: **jm**  
password: ********  
  
Mount the Debian-x.y.z-amd64-DVD-1.iso onto the VirtualBox’s Optical Drive.  
  
$ **sudo apt-get install telnetd**  
  
Create xinetd configuration file for Telnet:  
$ **sudo cat > /etc/xinetd.d/telnet << EOF**  
service telnet  
{  
disable = no  
flags = REUSE  
socket_type = stream  
wait = no  
user = root  
server = /usr/sbin/in.telnetd  
log_on_failure += USERID  
}  
**EOF**  
  
Initialise Terminal to VT100 as default:   # this is required Debian 10 onwards!  
$ **cat .bash_profile << EOF**  

**#** Set Terminal default to VT100:  
export TERM = vt100  
**EOF**  
  
And, also enable root access to host Jupiter via Telnet:  

$ **sudo cat > /etc/securettys << EOF**  

**#** Enable root access via Telnet  
ptys  
**EOF**  
  
Remove the Debian-x.y.z-amd64-DVD-1.iso from the VirtualBox’s Optical Drive. Close LXTerminal, shut down Jupiter, and re-export the Symbolics.ova appliance including the updated Debian LXDE and Telnet configuration files.  
  
  
## Connect from OG2 to its Debian-based Genera-host via Telnet
  
Cold boot Debian host Jupiter  
$ Login: **jm**  
password: ********  
  
Start OG2 Kronos VLM, i.e enter:
$ **./og2vlm.sh**  
  
command: **login lisp-machine**  
  
Toggle more processing, i.e enter:  
**Function M**          # key in F3 followed by M  
  
command: **select activity (activity name) terminal**   ; alternatively type in "Select T"  
  
Connect: (the name of a host [default ....]) **jupiter**  
jupiter Login: **jm**  
Password: ********  
........  
........  
**$** **ls /home/jm/**  
........    
........  
**$** **su -**                             # change to root  
Password: ********       # type in jupiter's root password  
**#** **ls /**  
........  
........  
**#** **exit**  
logout  
  
**$** **exit**  
logout  
  
**Select L**                       ; Back to Lisp  
  
Halt OG2 on Kronos:  
command:  **halt  genera  :query  no**  
  
Shutdown the Debian Genera-host Jupiter.  
