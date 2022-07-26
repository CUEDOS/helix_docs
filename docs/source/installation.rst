Installing Ubuntu Desktop
=========================

Downloading Ubuntu and making live USB
--------------------------------------

1) Download the latest LTS (long-term support) version of Ubuntu from
   `https://ubuntu.com/download/desktop <https://ubuntu.com/download/desktop>`__.
   Save the ISO file on your computer (not on a flash memory).
2) Download Rufus to make a live USB from
   `https://rufus.ie/en/ <https://rufus.ie/en/>`__.
3) Now, open Rufus and attach your flash memory. In Rufus, click on
   Select and choose the downloaded iso file. Also, select GPT for
   “Partition scheme” and UEFI for “Target system”. Then, click on
   START. If you see a window asking you how to write the image, click
   on “Write in ISO image mode (Recommended)”. NOTE: All the data on the
   flash memory will be deleted through this process.
4) When you see “Ready” in Status section, you can click on “Close” and
   restart your computer to commence installing Ubuntu. (Section 1.1 is
   inferred from
   `https://itsfoss.com/create-live-usb-of-ubuntu-in-windows/ <https://itsfoss.com/create-live-usb-of-ubuntu-in-windows/>`__)

Installing Ubuntu on an external hard drive
-------------------------------------------

NOTE: When you install Ubuntu on an external hard drive, you can have a
portable Ubuntu and just. Your external hard drive and computer should
support USB3.

1) When the computer is starting, press Esc key (for ASUS laptops). Then
   from the “boot driver”, choose “UEFI: name of your flash memory”.
   UEFI is specification that defines a software interface between an
   operating system and platform firmware.
2) Follow the instructions on step-by-step (select your external hard
   drive instead of flash memory mentioned on this page):
   `https://www.fosslinux.com/10212/how-to-install-a-complete-ubuntu-on-a-usb-flash-drive.htm <https://www.fosslinux.com/10212/how-to-install-a-complete-ubuntu-on-a-usb-flash-drive.htm>`__
   Note: Don’t install Ubuntu on a flash memory because it will get so
   hot and causes some overcurrent issues. Note: I assigned 100
   Gigabytes of my external hard to FAT32 and 100 Gigabytes of it to
   EXT4 for Ubuntu at the partitioning step mentioned on the above
   website.
3) At the end, restart the computer. When the computer is starting,
   press Esc key (for ASUS laptops). Then from the “boot driver”, don’t
   choose any “UEFI” and just choose your hard drive name to start
   Ubuntu. Note 1: Ubuntu uses LibreOffice as Office in Windows.
   However, LibreOffice doesn’t have Times New Roman and some other
   fonts by default. So you should add these fonts to LibreOffice.
   Follow the instructions of this clip to do so:
   `https://www.youtube.com/watch?v=pi4lI5AqMww <https://www.youtube.com/watch?v=pi4lI5AqMww>`__
   Note 2: To number the headings of a whole document in LibreOffice, we
   should click on Tools then on Chapter Numbering. Then you can assign
   Level 1 to heading 1, Level 2 to heading to and so on. Then click on
   Insert then on Cross-reference to cite the headings.

Partitioning the rest of hard drive
-----------------------------------

Now we should partition the rest of the external hard drive to use it to
save your files.

1) On windows, install EaseUS Partition Master software.
2) Click on “unallocated” part of your external hard and then click on
   Create at the right side.
3) Assign this remaining space as NTFS to store your data by choosing
   File system as NTFS and click on OK. Afterward, click on “Execute
   Operations”. Note: Windows can just recognize FAT32 and NTFS file
   system to store data.

Installing Gazebo
=================

1) Go to `http://gazebosim.org/ <http://gazebosim.org/>`__.
2) Click on Download icon. Then on Debian icon.
3) Copy the download script and paste it in Terminal to download and
   install Gazebo. (You can find Terminal by searching it in your
   Applications)

Installing PX4 Firmware (PX4-Autopilot)
=======================================

Install the firmware step-by-step according to the official clip on:
`https://docs.px4.io/master/en/dev_setup/dev_env_linux_ubuntu.html <https://docs.px4.io/master/en/dev_setup/dev_env_linux_ubuntu.html>`__

Running Gazebo with PX4 Firmware (PX4-Autopilot)
================================================

Now, you have Gazebo and PX4 Firmware installed on your Ubuntu.

1) Open PX4 Firmware through writing cd Firmware in Terminal. (On PX4
   website, it is mentioned to use cd/path/to/PX4-Autopilot as the PX4
   repository, but be aware that cd/path/to/ is just a symbol of a path
   to Firmware of PX4-Autopilot which is cd Firmware)
2) Then write make px4_sitl gazebo in Terminal. (Section 4 is inferred
   from:
   `https://docs.px4.io/master/en/simulation/gazebo.html <https://docs.px4.io/master/en/simulation/gazebo.html>`__)
   It is assumed that Ubuntu 20.4 LTS is being used on the devlopment
   machine. This page outlines how to set up VS Code for development on
   drones on the local network through SSH.

[Contributors] Set up VS Code
=============================

VS Code can be installed by following the instructions on the `visual
studio website <https://code.visualstudio.com/docs/setup/linux>`__, it
should be as simple as downloading the .deb file and double-clicking to
install.

Once Installed there are a few extensions that will make development
easier and these should be installed from the extensions tab on the left
side of the interface. They are as follows.

-  Python
-  Pylance
-  Remote Development
-  Remote - SSH

Once these extensions are installed and the drone has been set up with a
static IP address navigate to the remote tab on the left of the
interface and click on the + next to 'ssh targets'. Now enter the
following where user is the username chosen when Ubuntu was installed on
the UP Core companion Computer and ip_address is the static IP address
chosen during network configuration.

.. code:: sh

   ssh user@ip_address
