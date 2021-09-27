# How to convert any external storage into NAS with OpenMediaVault on RaspberryPi
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
    ` /etc/lighttpd/lighttpd.conf `

3. Enter the following commands to do a base OpenMediaVault installation. We can run the following command to download the OpenMediaVault install script and pipe it directly through to bash.

This script will install and set up everything needed to run OpenMediaVault on the Raspberry Pi.

You can verify the contents of this script by going directly to the OpenMediaVault Plugin developers install script repository.

```
wget -O - https://raw.githubusercontent.com/OpenMediaVault-Plugin-Developers/installScript/master/install | sudo bash
```

Under Global settings add one or more servers. You can find list of available servers in dnscrypt [repo](https://github.com/dnscrypt/dnscrypt-resolvers). For example, you can search for DNS servers that block ads, support doh (DNS over HTTPS), view locations, etc. V3 is the most recently updated list, hence, we will be using V3 ODOH [server list](https://github.com/DNSCrypt/dnscrypt-resolvers/blob/master/v3/odoh-servers.md) and pairing servers with [relay list](https://github.com/DNSCrypt/dnscrypt-resolvers/blob/master/v3/odoh-relays.md) to anonymize the queries. You can also check out the public DNSCrypt server list and pick one or more that fits your requirements. 

```
server_names = ['odoh-cloudflare' , 'odohrelay-koki-ams']
```
