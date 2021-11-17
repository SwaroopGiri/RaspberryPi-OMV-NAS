# How to configure any external storage into NAS with OpenMediaVault 5 on RaspberryPi
***
**Important Information**

This tutorial assumes that you have a Raspberry Pi setup with Rasberry Pi OS running on it. If you're completely new to this and have a raspberry pi straight out of box, just google on how to setup a headless raspberry pi and follow instructions mentioned in first couple of links.
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
  
  ```
  sudo nano /etc/lighttpd/lighttpd.conf
  ```
    
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

The default username is admin and the default password is openmediavault.

8. Once you have logged in, you will be greeted by the OpenMediaVault dashboard.

9. yYou'll need to change default port of OpenMediaVault so that Pi-hole can run on default http port and hence sustain updates. To do that, Click on Home icon and goto General Settings. Change the value of port to something like `8080`. Click save
10. It is also recommended to change the default web administrator password. To do that, click on Web Administrator Password section in General Settings. Type in your new password, confirm password and click save.
11. For the changes to take effect, click Apply. To confirm the changes, click Yes.
12. Finally, we'll be reverting Pi-hole settings to default port. Login to raspberrypi via ssh.
  - Stop Pi-hole service by following command:

  ```
  sudo service pihole-FTL stop
  ```
  - Edit the conf file.
  
  ```
  sudo nano /etc/lighttpd/lighttpd.conf
  ```
    
  - Edit below entry in above file.
  ```
  server.port = 80
  ```
  - To save press Ctrl+X --> Y --> Enter

12. Restart your Raspberry Pi by running the following command.

```
sudo reboot
```

### Setting up drive

1. If you have followed above process correctly, OpenMediaVault web interface can be accessed by following URL.

http://raspberrypiIP:8080

2. Log into OpenMedisvault web interface.
3. It is recommended to format the drive prior mounting and configuring a NAS share. However, you can ignore these steps and hop to mounting part if you know what you're doing. 

4. Click on disks under Storage section to the left in OpenMediaVault Web Interface.
   - Here, 1st disk will be your SD Card on which your OS resides and below that(/dev/sdaX) would be your externally mounted drives.
   - Select the disk which you want to use as a NAS share. For me it is /dev/sda.
   - Click on wipe --> select yes --> select quick.
   - Once the process completes, the disk should be ready to me mounted.

5. Click on File Systems under Storage section to the left in OpenMediaVault Web Interface.
   - Click on + Create icon.
   - Select your formatted drive, type in your desired label (make sure to not to include any spaces), select file system as EXT4, click on Ok.
   - This will take a while. Once the process completes, click on Close.
   - Select the File System you just created (for me it was /dev/sda1) and click on Mount.

6. For the changes to take effect, click Apply. To confirm the changes, click Yes.

### Creating Shared Folder

1. Now we need to create a shared folder that exists on our newly added filesystem. Go to the Shared folders settings page by clicking the “Shared Folders” option in the sidebar.

2. Click on "+ Add" button.
3. Type in the Name of your shared folder, select the file system that you just created from the Device dropdown menu, and select the permissions(Everyone read/write for ease of access in your local network) for your shared folder using the Permissions dropdown menu. Once you have completed this step, click Save.
4. A shared folder should be created. For the changes to take effect, click Apply. To confirm the changes, click Yes.

### Enabling SAMBA/CIFS

1. To enable the share, go to Services > SMB/CIFS and then click on Enable in Settings --> General Settings.
2. Click save. For the changes to take effect, click Apply. To confirm the changes, click Yes.
3. Navigate to Services --> SMB/CIFS --> Shares tab and click "+ Add".
4. Click on enable. Select the Shared folder you just created from the dropdown menu. There are many options. You can configure your share however you want. To allow everyone to have full access to the share, we choose Guests allowed from the Public dropdown menu. I have these things enabled "Set Browsable", "Honor existing ACLs" and "Enable permissions inheritance". Click Save.
5. For the changes to take effect, click Apply. To confirm the changes, click Yes.
6. Voila! The share you just created is ready to be accessed by any system in your network. Open File Explorer on Windows 10, click on network and raspberrypi host should be visible or navigate to \\raspberrypiIP\ in file explorer. The SMB/CIFS share running on the Raspberry Pi 4 should be displayed.

### Troubleshooting

1. If your Pi-hole installation says Lost Connection to API. To fix it, type below pihole repair command.
   ```
   pihole -r
   ```
   
2. Select Yes when it prompts whether you want to keep existing configuration.
3. Wait for the process to complete.
4. Profit.
