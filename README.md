HSMM-Pi
=======

HSMM-Pi is a set of tools designed to easily configure the Raspberry Pi to function as a High-Speed Multimedia (HSMM) wireless mesh node, compatible with Broadband Hamnet (BBHN) and AREDN.  Mesh networks offer amateur radio operators (hams) the ability to operate high-speed data networks in the frequencies shared with unlicenced users of 802.11 b/g/n networking equipment.  Hams can operate HSMM or BBHN at higher power with larger antennas than are available to unlicensed users.  The HSMM-Pi project makes it possible to run an HSMM mesh node on the Raspberry Pi.  The project has been tested to work on other embedded computing platforms, including the BeagleBone and BeagleBone Black.

The HSMM-Pi project can used by people not possessing an amateur radio license so long as they are in compliance with the transmission rules set by the FCC or the local regulating body.  This typically means sticking with the WiFi antenna provided with your WiFi adapter.

**Note:** the main install.sh script uses the sury.org repository for php5.6 in Raspberrpy Pi OS Buster, which will will only work on Raspberry Pi devices with the ARMv7 architecture and above, from the Raspberry Pi 2 and upward (tested on the Pi 3A+). For ARMv6 (tested on Raspberry Pi Zero W) devices there are special instructions and require an outdated version of Raspberry Pi OS (Jessie).

**Note(2):** this repository has been modified to work, and tested on the Raspberry Pi 3A+ and Raspberry Pi Zero W. I cannnot confirm the Beaglebone or Update functionality. I have tested meshing with 2 Pi Zero W's and a 3A+ and it appears to be working.

HSMM-Pi Blog:
http://hsmmpi.wordpress.com/

For a video tour, see the following YouTube video:
http://www.youtube.com/watch?v=ltUAw02vfqk

The project consists of a PHP web application that is used to configure and monitor the mesh node, and an installation shell script that installs dependencies and puts things in the right spots.

The HSMM-Pi project is designed to run on Rasbian Jessie for ARMv6 and Raspberry Pi OS Buster for ARMv7.  Rather than providing an OS image for HSMM-Pi, you will find an installation script that will transform a newly-imaged host into an HSMM-Pi node.  This has several benefits:

 * Greater transparency:  You can see exactly which changes are made to the base system by looking at the [install shell script](https://github.com/urlgrey/hsmm-pi/blob/master/install.sh).
 * Easier to port to more platforms: Any platform that runs the supported OS releases ought to be capable of running HSMM-Pi
 * Easier to host:  I only need to post the installation script and webapp files on Github and it's done.
 * Easier to seek support: Raspbian/Raspberry Pi OS is widely used and supported, no need to introduce another customization.

Hardware Requirements
=====================
HSMM-Pi from this repository has been tested to work with the Raspberry Pi running the Raspbian (Jessie) or Raspberry Pi OS (Buster), with the BeagleBone running Debian, and with the BeagleBone Black running Ubuntu 12.04 from the onboard eMMC flash memory.  The requirements for each are listed below.

Raspberry-Pi Node:

 *  Raspberry Pi in 32bit mode, ARMv6 or ARMv7
 *  USB WiFi adapter (tested with the N150 adapter using the Ralink 5370 chipset, and with the Alfa AWSU036NH), or built-in WiFi adapter (on Raspberry Pi 3 & Raspberry Pi Zero W)
 *  SD memory card (4GB minimum)

BeagleBone Node:

 *  BeagleBone or BeagleBone Black
 *  USB WiFi adapter (tested with the N150 adapter using the Ralink 5370 chipset)
 *  SD memory card (4GB minimum) (in the case of BeagleBone Black, used for initial imaging of the eMMC flash memory only)

Modes
=====
HSMM-Pi has two modes: Internal and Mesh Gateway.  A description of each is provided below.

Mesh Gateway Mode
-----------------
A node in Mesh Gateway mode routes traffic throughout the mesh, and provides the mesh with Internet access through the wired Ethernet port.  The gateway *obtains* a DHCP lease on the wired interface, and advertises its Internet link to mesh nodes using OLSR.


Internal Mode
-------------
A node in Internal mode routes traffic throughout the mesh and provides mesh access to any hosts connected to its wired Ethernet port.  The node in this mode runs a DHCP server that *issues* DHCP leases to any hosts on the wired connection.  It also runs a DNS server that can provide name resolution for both mesh nodes and Internet hosts.  The following diagram shows how the two types of nodes can be deployed:

![Diagram](http://i.imgur.com/sfR3Q12.png)

There could be any number of mesh nodes in the Ad-Hoc WiFi Network.  The route among the nodes is managed entirely with OLSR.

I've done all of my testing with N150 USB wifi adapters that use the Ralink 5370 wireless chipset.  These adapters are cheap (~$7 USD), compact, and easy to come by.  They also use drivers that are bundled with most Ubuntu distributions, making setup easy.  The N150 adapter tested included a threaded antenna connector that should make it easy to add a linear amplifier and aftermarket antenna (outside the scope of the HSMM-Pi project).

Raspberry Pi (ARMv7) Installation
=========================

1. Download the latest 32bit [Raspberry Pi OS](https://www.raspberrypi.org/software/operating-systems/) (Full or Lite; only Buster Lite was tested) disk image. The Lite image is suitable for headless installations as it omits the graphical interface, web browser, etc.
1. Write the image to an SD memory card.
1. If doing a headless install, follow the [RaspberryPi.org guide here](https://www.raspberrypi.org/documentation/computers/configuration.html)
1. If using USB gadget support, follow the [ADA Fruit guide here](https://learn.adafruit.com/turning-your-raspberry-pi-zero-into-a-usb-gadget/ethernet-gadget). It is possible to use the PI3A+ in USB gadget mode, but requires extra settings, [this video](https://www.youtube.com/watch?v=7nDJPidKSPA) shows the simplest way to do this.
1. Insert the card into a Raspberry Pi
1. Connect the wired Ethernet port (or USB port for Ethernet over USB gadget) on the Pi to a network with Internet access (Linux users made need to do extra steps, such as sharing internet with USB0 interface)
1. Apply power to the Pi
1. Login to the Pi through an SSH session, the console, or the terminal application. The username is "pi" and the password is "raspberry".
1. Do a complete update and upgrade before running config.

        sudo apt update; sudo apt upgrade -y
1. Run the Raspberry Pi Setup program:

        sudo raspi-config
1. Change the password for the "pi" account
1. If installing over an SSH connection to the Pi, then I recommend you install "screen" (`sudo apt-get install screen`) to ensure that the installation script is not stopped prematurely if you lose connectivity with the Pi.  This is optional, but I highly recommend using screen if installing over the network.  You can find more info on screen here: http://linux.die.net/man/1/screen
1. Run the following commands to download the HSMM-Pi project and install

        sudo apt install -y git
        git clone https://github.com/mrmabs/hsmm-pi.git
        cd hsmm-pi
        sh install.sh
1. Login to the web application on the Pi:
http://(wired Ethernet IP of the node):8080/ or http://raspberrypi.local:8080/ (over USB)
1. Access the Admin account using the username "admin" and password "changeme".
1. Change the password for HSMM-Pi
1. Configure as either an Internal or Gateway node

Raspberry Pi (ARMv6) Installation
=========================

1. Download a Raspbian Jessie (Image no longer appears to be available online) or [Raspbian Jessie Lite](https://downloads.raspberrypi.org/raspbian_lite/images/raspbian_lite-2017-07-05/) disk image. The Lite image is suitable for headless installations as it omits the graphical interface, web browser, etc. There images outdated and are from 2017 and should not be used for system connecting to the internet.
1. Write the image to an SD memory card.
1. If doing a headless install, follow the [RaspberryPi.org guide here](https://www.raspberrypi.org/documentation/computers/configuration.html)
1. If using USB gadget support, follow the [ADA Fruit guide here](https://learn.adafruit.com/turning-your-raspberry-pi-zero-into-a-usb-gadget/ethernet-gadget).
1. Insert the card into a Raspberry Pi
1. Connect the wired Ethernet port (or USB port for Ethernet over USB gadget) on the Pi to a network with Internet access (Linux users made need to do extra steps, such as sharing internet with USB0 interface)
1. Apply power to the Pi
1. Login to the Pi through an SSH session, the console, or the terminal application. The username is "pi" and the password is "raspberry".
1. Do a complete update and upgrade before running config.

        sudo apt update; sudo apt upgrade -y
1. Run the Raspberry Pi Setup program:

        sudo raspi-config
1. Change the password for the "pi" account
1. If installing over an SSH connection to the Pi, then I recommend you install "screen" (`sudo apt-get install screen`) to ensure that the installation script is not stopped prematurely if you lose connectivity with the Pi.  This is optional, but I highly recommend using screen if installing over the network.  You can find more info on screen here: http://linux.die.net/man/1/screen
1. Run the following commands to download the HSMM-Pi project and install (make sure you run the pi0 install script)

        sudo apt install -y git
        git clone https://github.com/mrmabs/hsmm-pi.git
        cd hsmm-pi
        sh **install-pi0.sh**
1. Login to the web application on the Pi:
http://(wired Ethernet IP of the node):8080/ or http://raspberrypi.local:8080/ (over USB)
1. Access the Admin account using the username "admin" and password "changeme".
1. Change the password for HSMM-Pi
1. Configure as either an Internal or Gateway node


BeagleBone Black Installation
=============================

1. Download the latest BeagleBone Black Ubuntu 12.04 image: http://www.armhf.com/index.php/boards/beaglebone-black/#precise
1. Write the image to an SD memory card using the steps on the page referenced in the previous step
1. Insert the SD card into a BeagleBone Black board
1. Apply power to the BeagleBone Black
1. Login to the BeagleBone Black through an SSH session or the console using the 'ubuntu' account
1. Transfer the image to the running BeagleBone Black using SCP
1. Write the image to the eMMC flash memory using the steps mentioned in the first step here.
1. Wait for all 4 LEDs to go solid (could take several minutes)
1. Shutdown the Beaglbone Black (sudo /sbin/init 0)
1. Remove the memory card from the BeagleBone Black
1. Apply power to the BeagleBone Black
1. Login to the BeagleBone Black through an SSH session or the console using the 'ubuntu' account
1. Change the password for the 'ubuntu' account
1. Install the development tools necessary to build OLSRD and retrieve the HSMM-Pi project:

        sudo apt-get upgrade
        sudo apt-get install make gcc git
1. If installing over an SSH connection, then I recommend you install 'screen' (sudo apt-get install screen) to ensure that the installation script is not stopped prematurely if you lose connectivity.  This is optional, but I highly recommend using screen if installing over the network.  You can find more info on screen here: http://linux.die.net/man/1/screen
1. Run the following commands to download the HSMM-Pi project and install

        git clone https://github.com/urlgrey/hsmm-pi.git
        cd hsmm-pi
        sh install.sh
1. Login to the web application:
http://(wired Ethernet IP of the node):8080/
1. Access the Admin account using the 'admin' username and 'changeme' password.
1. Change the password for HSMM-Pi
1. Configure as either an Internal or Gateway node



Upgrade Steps
=============
This is experimental, and you should fall back to a fresh installation if things aren't functioning as you'd expect.  This is supported only on the HEAD of the master branch at this time.

1. Login to the host using SSH or the console
1. Run the following commands to upgrade:

        cd ~/hsmm-pi
        git pull
        sh install.sh
1. Access the web UI and check the configuration.  Save the Network and Location settings, even if no changes are needed.
1. If the save operation fails, then you might need to replace the SQLite database file due to database schema changes.  Run the following command:

        cd ~/hsmm-pi/
        sudo cp src/var/data/hsmm-pi/hsmm-pi.sqlite /var/data/hsmm-pi/
1. Repeat the step of reviewing and saving the configuration through the web UI.

Internal Mesh Node Configuration
================================
This represents the minimum set of steps:

1. Select Admin->Network from the menubar
1. Configure the WiFi interface:
    1.  Specify an IP address that will be unique throughout the mesh network.  This will be different every mesh node.  A default will be provided based on the last 3 fragments of the WiFi adapter MAC address.
1. Configure the Wired interface:
    1. Set the Wired interface mode to LAN
1. Configure the Mesh settings
    1. Specify your node name, likely a composition of your callsign and a unique number in your mesh (i.e. KK6DCI-7)
1. Click 'Save'
1. If successful, click the 'Reboot' button in the alert and proceed.


Gateway Node Configuration
==========================
This represents the minimum set of steps:

1. Select Admin->Network from the menubar
1. Configure the WiFi interface:
    1. Specify an IP address that will be unique throughout the mesh network.  This will be different every mesh node.  A default will be provided based on the last 3 fragments of the WiFi adapter MAC address.
1. Configure the Wired interface:
    1. Set the Wired interface mode to WAN
1. Configure the Mesh settings
    1. Specify your node name, likely a composition of your callsign and a unique number in your mesh (i.e. KK6DCI-7)
1. Click 'Save'
1. If successful, click the 'Reboot' button in the alert and proceed.

FAQ
====

A list of frequently asked questions can be found on [our wiki](https://github.com/urlgrey/hsmm-pi/wiki), at the [FAQ page](https://github.com/urlgrey/hsmm-pi/wiki/FAQ).
