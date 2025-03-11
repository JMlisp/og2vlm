# OG2VLM
Open Genera 2 Virtual Lisp Machine emulator for Debian Linux

OG2VLM is the main JMlisp OG2 repository of a Workgroup of Symbolics machines and DEC Alpha VLM workstation users, whose goal it is to keep OG2 and associated OG2 systems alive and, based on Brad Parker's great work of adapting the Alpha version to x86_64, to provide bug fixes for still remaining problems, and maintain OG2 on Debian-based Linux platforms as well. 

The OG2VLM repository provides a ready to use OG2 distribution world that includes both NFSv3 Client and LMFS capability for the VLM, translation files, genera binaries for X86 and Xeon platforms, as well as functions and commands necessary to create FEP disks, a LMFS partition, and an Initial world, without the need to disable Debian’s calendar clock prior to the installation and the subsequent launch of OpenGenera.

It also includes a step-by-step procedure, in both Markdown and Text format, that describes how to emulate a Symbolics VLM and run in unprivileged mode a tap-controlled, purely LMFS-based, and NFSv3-capable OG2 in Debian, which can be run either as a Virtual Machine in VirtualBox or on its own disk or partition, on macOS and Windows hosts. 

The OG2VLM repository also includes screenshots, accessible via hyperlinks from within the procedure in Markdown format, showing what the OG2 screen looks like while implementing key steps and running saved worlds. However, it does not include the official Symbolics OG2 distribution, which you will need to download from a website referenced in the procedure and use it in your own responsibility regarding ownership rights.

**OG2vlm**:  Install OG2 on a Debian-based Genera-host on Macs and PCs:  
- Install VirtualBox and Debian onto your PC or Mac
- Install Guest Additions onto VirtualBox Debian
- Make macOS ready for OpenGenera
- Make Debian ready for OpenGenera
- Prepare Debian and OG2 to host the LMFS system
- Attach FEP disks and create a [LMFS partition](https://github.com/JMlisp/og2vlm/blob/main/screenshots/LMFS-Partition-Initialisation.png)
- Activate the LMFS system at Boot Time and Create the [Initial](https://github.com/JMlisp/og2vlm/blob/main/screenshots/Initial.png) world
- Prepare to create a Symbolics World
- Create a [Symbolics](https://github.com/JMlisp/og2vlm/blob/main/screenshots/Symbolics.png) world containing the most important systems
- Let OpenGenera talk to the Internet via the virtual TAP device
- Emulate a Symbolics space-cadet keyboard using a Mac or PC keyboard
- Send the Lisp Listener screen to the X Server of a LAN host
- Install and set up a Telnet service onto a Debian-based host
- Connect from OG2 to its Debian-based Genera-host via Telnet

Besides OG2VLM, the following JMlisp private repositories include OG2 system software and sources for debugging purposes, vendor-provided systems, initialisation files and procedures in both Markdown and Text format, patches and feature enhancements, and even the possibility, but for Workgroup members only, to create and upload new OG2 Distribution Worlds, as stated below:

**OG2WG**:  Enable VLMs to run OG2 worlds that include advanced features:  
- OG2VLM advanced setup
- Create a [Macsyma](https://github.com/JMlisp/og2vlm/blob/main/screenshots/Macsyma.png) world on top of the Symbolics world
- Create a [Standard](https://github.com/JMlisp/og2vlm/blob/main/screenshots/Standard.png) world including patches and harbison-steele-doc
- Configure an OG2 File server to act as a Lock-Simple server
- Set up a Network Printer and a Printer Host on the OG2 VLM
- Access an OG2 VLM from a LAN host via Telnet or via SSH
- Start OG2 by sending its display to the X server of a LAN host
- Enable Debian's X Server to accept OG2 Client requests
- Install and set up REXEC on a Debian-based Genera-host
- Access a VLM's and an iMach's LMFS and FEP FS via NFILE
- Increase the size of the [LMFS storage](https://github.com/JMlisp/og2vlm/blob/main/screenshots/KRONOS%20LMFS%20Free%20Records%20on%20FEP0%20and%20FEP1.png) capacity of a VLM

**OG2clients**:  Build an OG2 Client VLM on a Debian-based Genera-host:  
- Create [OG2 Client](https://github.com/JMlisp/og2vlm/blob/main/screenshots/TITAN%20Client%20on%20JUPITER.png) VLMs
- Enable Clients to show a Gateway's Hostname instead of its IP address

**OG2remote**:  Access and run an OG2 VLM remotely over the Internet:  
- Access an OG2 Genera-host remotely via an SSH Proxy Server of the LAN
- Prepare to forward OG's display over the Internet to a remote host using Xnest
- OG2 VLM Remote Operation over the Internet via [Xnest](https://github.com/JMlisp/og2vlm/blob/main/screenshots/Xnest%20KRONOS%20Lisp%20Listener%201%20to%20MBP.png)
- Security issues regarding Plain Remote Port Forwarding and Xnest
- OG2 VLM Operation over the Internet via Plain [Remote Port Forwarding](https://github.com/JMlisp/og2vlm/blob/main/screenshots/Port%20Forwarding%20KRONOS%20Lisp%20Listener%201%20to%20MBP.png)
- OG2 Client VLM Remote Operation over the Internet via Xnest
- OG2 Client VLM Operation over the Internet via Plain Remote Port Forwarding

**OG2glx**:  Implement and run OpenGL applications on a Debian-based OG2 VLM:  
- Prepare macOS to accept OpenGL IndirectGLX requests
- Prepare Debian to accept OpenGL IndirectGLX requests
- Install the OG2 OpenGL system on the OG2 VLM
- Prepare OG2 and the X server to run OpenGL apps
- Run [sample apps](https://github.com/JMlisp/og2vlm/blob/main/screenshots/Rotating%20Rectangle%20forwarded%20to%20macOS%20XQuartz.mp4) forwarding the screen to an X server

**OG2distro**:  Build an NFSv3-capable and LMFS-based OG2 Distribution world:  
- OG2VLM World Distribution
- Install Debian and make macOS and Debian ready for Open Genera
- Prepare Debian and OG2 to host the LMFS
- Start the Genera-8-5 Distribution world the first time
- Create an NFSv3-capable and purely LMFS-based [OG2 Distribution](https://github.com/JMlisp/og2vlm/blob/main/screenshots/Genera-8-5e.png) world
