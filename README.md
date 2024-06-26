HSMM-Pi
=======

Original project created by <a href="https://github.com/urlgrey">urlgrey</a>. This version includes improvements from <a href="https://github.com/cbegg50/hsmm-pi">cbegg50</a> and is updated for PHP v7.

Updated to Raspberry Pi OS (Buster) by KD9YQK
-------

Updated Instructions
--------------

Updated to use Raspberry Pi OS Buster Lite since Jessie and Sketch are no longer supported; making it difficult to find dependancies. Migrated code to use a newer versions of dependancies. Fixed some errors in the original code as well.

KD9YQK

-------

HSMM-Pi is a set of tools designed to easily configure the Raspberry Pi to function as a High-Speed Multimedia (HSMM) or Broadband-Hamnet (BBHN) wireless node.  HSMM and BBHN offer radio amateurs (HAMs) the ability to operate high-speed data networks in the frequencies shared with unlicenced users of 802.11 b/g/n networking equipment.  HAMs can operate HSMM or BBHN at higher power with larger antennas than are available to unlicensed users.  The HSMM-Pi project makes it possible to run an HSMM or BBHN mesh node on the Raspberry Pi.  The project has been tested to work on other embedded computing platforms, including the BeagleBone and BeagleBone Black.

The HSMM-Pi project can used by people not possessing an amateur radio license so long as they are in compliance with the transmission rules set by the FCC or the local regulating body.  This typically means sticking with the WiFi antenna provided with your WiFi adapter.

HSMM-Pi Blog:
http://hsmmpi.wordpress.com/

For a video tour, see the following YouTube video:
http://www.youtube.com/watch?v=ltUAw02vfqk

The project consists of a PHP web application that is used to configure and monitor the mesh node, and an installation shell script that installs dependencies and puts things in the right spots.  Additionally, an update script allows for updates to the repository for existing installations.

The HSMM-Pi project is designed to run on Raspbian systems.  Rather than providing an OS image for HSMM-Pi, I've instead created an installation script that will transform a newly-imaged host into an HSMM-Pi node.  This has several benefits:

 * Greater transparency:  You can see exactly which changes are made to the base system by looking at the [install shell script](https://github.com/HansKillinger/hsmm-pi/blob/master/install.sh).
 * Easier to port to more platforms: Any platform that runs the supported Raspbian / Debian releases ought to be capable of running HSMM-Pi
 * Easier to host:  I only need to post the installation / update scripts and webapp files on Github and it's done.
 * Easier to seek support: Raspbian is widely used and supported, no need to introduce another customization.

Hardware Requirements
=====================
HSMM-Pi has been tested to work with the Raspberry Pi running the Raspbian OS, with the BeagleBone running Debian, and with the BeagleBone Black running Ubuntu 12.04 from the onboard eMMC flash memory.  The requirements for each are listed below.

Raspberry-Pi Node:

 *  Raspberry Pi (any model)
 *  USB WiFi adapter (tested with the N150 adapter using the Ralink 5370 chipset, and with the Alfa AWSU036NH), or built-in WiFi adapter (on Raspberry Pi 3)
 *  SD memory card (4GB minimum)

BeagleBone Node:

 *  BeagleBone or BeagleBone Black
 *  USB WiFi adapter (tested with the N150 adapter using the Ralink 5370 chipset)
 *  SD memory card (4GB minimum) (in the case of BeagleBone Black, used for initial imaging of the eMMC flash memory only)

Modes
=====
The HSMM-Pi can function as an internal mesh node or as a gateway mesh node.  A description of each is provided below.

Mesh Gateway Node
=================
The Mesh Gateway is capable of routing traffic throughout the mesh, and provides an Internet link to the mesh through the wired Ethernet port.  The gateway obtains a DHCP lease on the wired interface, and advertises its Internet link to mesh nodes using OLSR.


Internal Mesh Node
==================
This node is capable of routing traffic throughout the mesh and providing mesh access to any hosts connected to its wired Ethernet port, operating with NAT or in Direct 13-port mode.  The node can run a DHCP server that issues DHCP leases to any hosts on the wired connection, allowing DHCP reservation through a new View.  It also runs a DNS server that can provide name resolution for mesh nodes and Internet hosts.  The following sequence shows how the two types of nodes can be deployed:

```
(Client1) --> (Switch) --> (Internal Mesh Node) -->
(Ad-Hoc WiFi Network) --> (Mesh Gateway) --> (Internet)
```

There could be any number of mesh nodes in the Ad-Hoc WiFi Network.  The route among the nodes is managed entirely with OLSR.

I've done all of my testing with N150 USB wifi adapters that use the Ralink 5370 wireless chipset.  These adapters are cheap (~$7 USD), compact, and easy to come by.  They also use drivers that are bundled with most Ubuntu distributions, making setup easy.  The N150 adapter tested included a threaded antenna connector that should make it easy to add a linear amplifier and aftermarket antenna (outside the scope of the HSMM-Pi project).

Raspberry Pi Installation
=========================

1. Download the [Raspberry Pi OS Buster](https://www.raspberrypi.com/software/operating-systems/#raspberry-pi-os-legacy) disk image. The Lite image is suitable for headless installations as it omits the graphical interface, web browser, etc.
1. Write the image to an SD memory card.  This involves formatting the SD card; I recommend the steps described at http://elinux.org/RPi_Easy_SD_Card_Setup
1. Insert the card into a Raspberry Pi
1. Connect the wired Ethernet port on the Pi to a network with Internet access
1. Apply power to the Pi
1. Login to the Pi through an SSH session, the console, or the terminal application. The username is "pi" and the password is "raspberry".
1. Run the Raspberry Pi Setup program:

        sudo raspi-config
1. Expand the filesystem to fill the SD memory card
1. Change the password for the "pi" account
1. If installing over an SSH connection to the Pi, then I recommend you install "screen" (`sudo apt-get install screen`) to ensure that the installation script is not stopped prematurely if you lose connectivity with the Pi.  This is optional, but I highly recommend using screen if installing over the network.  You can find more info on screen here: http://linux.die.net/man/1/screen
1. Run the following commands to download the HSMM-Pi project and install

        sudo apt-get update
        sudo apt-get install -y git
        git clone https://github.com/KansKillinger/hsmm-pi.git
        cd hsmm-pi
        sh install.sh
1. Login to the web application on the Pi:
http://(wired Ethernet IP of the node):8080/
1. Access the Admin account using the username "admin" and password "changeme".
1. Change the password for HSMM-Pi
1. Configure as either an Internal or Gateway node


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
================================
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
