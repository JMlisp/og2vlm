# OG2VLM
OpenGenera2 Virtual Lisp Machine emulator for Debian Linux

If you don't know what OG2VLM means, you probably don't need it.

This repository provides what is needed to install OpenGenera (OG2) on Debian Linux, which can be run either as a Virtual Machine in VirtualBox or on its own disk or partition.

It includes a step-by-step procedure that describes how to emulate a Symbolics Virtual Lisp Machine (VLM) and run in unprivileged mode a tap-controlled, purely LMFS-based, and NFSv3-capable OpenGenera (OG2) in Debian, either on macOS or on Windows hosts. In detail, the procedure comprises the following sections:

- Install VirtualBox and Debian onto your PC or Mac

- Install Guest Additions onto VirtualBox Debian

- Make macOS ready for OpenGenera

- Make Debian ready for OpenGenera

- Prepare Debian and OG2 to host the LMFS system

- Attach FEP disks and create a LMFS partition

- Activate the LMFS system at Boot Time and Create the Initial world

- Prepare to create a Symbolics World

- Create a Symbolics world containing the most important systems

- Let OpenGenera talk to the Internet via the virtual TAP device

- Emulate a Symbolics space-cadet keyboard using a Mac or PC keyboard

The repository includes screenshots to show what the OG2 screen looks like for implementing key steps and while running the saved worlds. It does, however, not include OpenGenera. It requires that you provide a bzip2 tar archive called opengenera2.tar.bz2 which you can download from a site referenced in the procedure.
