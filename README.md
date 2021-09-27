# How to configure any external storage into NAS with OpenMediaVault on RaspberryPi
***
**Important Information**

This tutorial assumes that you have a Raspberry Pi setup with Rasberry Pi OS running on it. If you don't know what it is, [hop here](https://github.com/SwaroopGiri/Pi_hole_configuration) to know how to set it up.
***
**Additional Information**

**OpenMediaVault** is a networked attached storage (NAS) solution built on Debian that has support for the Raspberry Pi. Using this software, you can easily convert your Raspberry Pi into a powerful NAS that has built-in support for a variety of different services.

To run OpenMediaVault, you will need to be using a Raspberry Pi 2B or newer. It is recommended that you use the Raspberry Pi 4 or newer due to its improved IO capabilities.
*** 

### OpenMediaVault Installation

1. Before we install OpenMediaVault, let’s update the existing packages by running the following command.
```
sudo apt update
sudo apt upgrade
```

2. If you have existing Pi-Hole configuration on your Pi, it will nuke below OpenMediaVault Installation since web-interface of OpenMediaVault and Pi-hole, both are configured to listen to default http port (80). So, if you have pi-hole or any other service occupying port 80, OpenMediaVault will fail to install. You can skip this step if you don't have anything configured on your pi.
  - We'll need to stop Pi-hole service by following command:

  ```
  sudo service pihole-FTL stop
  ```
  - Temporarily change the port Pi-hole's web interface listens to by entering following command.
  
  `sudo nano /etc/lighttpd/lighttpd.conf`
    
  - Edit below entry in above file.
  ```
  server.port = 8000
  ```
  - To save press Ctrl+X --> Y --> Enter

3. Enter the following commands to do a base OpenMediaVault installation. We can run the following command to download the OpenMediaVault install script and pipe it directly through to bash.

This script will install and set up everything needed to run OpenMediaVault on the Raspberry Pi.

You can verify the contents of this script by going directly to the OpenMediaVault Plugin developers install script repository.

```
wget -O - https://raw.githubusercontent.com/OpenMediaVault-Plugin-Developers/installScript/master/install | sudo bash
```

4. Once the installation process has completed, it is recommended to restart your Raspberry Pi.

Restart your Raspberry Pi by running the following command.

```
sudo reboot
```

5. To be able to access the OpenMediaVault Web GUI, you will need to know your Raspberry Pi’s static IP address.

If you don’t know your Raspberry Pi’s IP address, you can run the following command.
```
hostname -I
```

6. With your Raspberry Pi’s local IP address, type in that IP address in your favorite web browser.

7. Upon loading the OpenMediaVault web interface, you will be asked to log in to be able to proceed.

The default username is admin, and the default password is openmediavault.

5. Once you have logged in, you will be greeted by the OpenMediaVault dashboard.

6. We'll need to change default port of OpenMediaVault so that Pi-hole can run on default http port and hence sustain updates. To do that, Click on Home icon and goto General Settings. Change the value of port to something like `8080`.
7. It is also recommended to change the default web administrator password. To do that, click on Web Administrator Password section in General Settings. Type in your new password, confirm password and click save.
8. Finally, we'll be reverting Pi-hole settings to default port.
  - Stop Pi-hole service by following command:

  ```
  sudo service pihole-FTL stop
  ```
  - Edit the conf file.
  
  `sudo nano /etc/lighttpd/lighttpd.conf`
    
  - Edit below entry in above file.
  ```
  server.port = 80
  ```
  - To save press Ctrl+X --> Y --> Enter

### Creating a NAS CIFS Share


