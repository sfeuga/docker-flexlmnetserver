# Installing The Foundry Software
[![license](https://img.shields.io/github/license/mashape/apistatus.svg)]()

## Installing The Foundry licence server
### Requirements
* A system running Docker 1.8 or above. (instruction are for Linux, but could be modified for Windows.)
* Ensure that ports 2080 and 27000-27009 are open to the LAN on this system and not being used by another service.

### Gather System and Network Information
You will need the name or IP of the host system, and the MAC address of your ethernet card. This can be determine with the following command. 

 Open a terminal and run 
 ```sh
 ifconfig
 ```
 
 This will produce a list of all network interfaces. Ignore interfaces name *lo* and *docker* You should find a interface starting with an 'e'. This is a sample of the network interface used on Dell 7910
 ```sh
 enp58s0f1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.3.5  netmask 255.255.255.0  broadcast 192.168.3.255
        ether d4:6d:6d:ad:27:c6  txqueuelen 1000  (Ethernet)
 ```
 
 The *inet X.X.X.X* is the IP address and the *ether xx:xx:xx:xx:xx:xx* is MAC address.
 ### Generate Licence File
 1. rename/create the file to be named 'the_foundry_server.lic'
 2. Save this file to your host in the /var/flexlm/the_foundry_server.lic
 
### Create Docker Container and start service. 
Type the following command. 
```sh
docker build -t flexlmnetserver .
docker run -d  --mac-address="$(ifconfig enp58s0f1 | grep -e [a-z0-9][a-z0-9]:[a-z0-9][a-z0-9]:[a-z0-9][a-z0-9]:[a-z0-9][a-z0-9]:[a-z0-9][a-z0-9]:[a-z0-9][a-z0-9] | awk -F \" \" '{print $2}')" -h $(ifconfig enp58s0f1 | grep "inet " | awk -F " " '{print $2}') -v /var/flexlm/the_foundry_server.lic:/usr/local/flexlm/licenses/license.dat:ro -p 2080:2080 -p 27000-27009:27000-27009 --restart=always --name flexlmnetserver
```
This will now launch the licence server 


### Logging

Docker's built-in logging functionality will collect the stdout/stderr generated by _lmgrd_. 

    docker logs [CONTAINER_ID]

Thus it's recommended you do NOT use the '-l' flag to log to a file, doing so will cause your Docker logs to be empty.  Additionally, avoid using the '-t' flag when using the 'run' command, enabling TTL support will cause extra line breaks in your Docker logs.

## Troubleshooting

If you are unsure if the server is running correctly, you can log into the container.

    docker exec -it CONTAINER_ID /bin/bash

Once in bash run: 

    lmutil lmstat -a -c [LICENSE_PATH]

## Resources
[Official Docs](https://www.foundry.com/licensing)

## Supports
Applies to all Foundry products
