# vpnserverwithadguard
Get an ipsec VPN server up and running with AdGuard on Ubuntu24.01    
Idea was: Use docker VPN container, Adguard will run as snap package.  
Btw: f***k ufw and docker incompatibilities.  
At first we tried a nice containerized install of https://github.com/hwdsl2/docker-ipsec-vpn-server which gave us routing issues between this container and the snap package for AdGuard Home. No way out. So a non-containerized version von the vpn server did the trick: https://github.com/hwdsl2/setup-ipsec-vpn/

# Basic
- nano `/etc/apt/apt.conf.d/50unattended-upgrades` to make automatic reboot after updates
- install docker as per https://docs.docker.com/engine/install/ubuntu/

# Networking
- create dummy interface with ip:
  - `nano /etc/netplan/99_config.yaml`, add:  
    ```
    network:
      dummy-devices:
        dummy0:
          addresses:
            - 10.8.2.1/24
     ```
  - `netplan apply`

# AdGuard
- install snap (if not already installed)
- `snap install adguard-home`
- edit `/var/snap/adguard-home/current/AdGuardHome.yaml`
  - configure admin interface to bind to 10.8.2.1:8080
  - configure dns server to bind to 10.8.2.1:53
  
# Set up 



# stuff that didn't work out:
## VPN Container
- `nano vpn.env`
  - add
    ```
    VPN_IPSEC_PSK=your_ipsec_pre_shared_key
    VPN_USER=your_vpn_username
    VPN_PASSWORD=your_vpn_password
    ```
  - save and exit
- run container:
  ```
  docker run \
    --name ipsec-vpn-server \
    --env-file ./vpn.env \
    --restart=always \
    -v ikev2-vpn-data:/etc/ipsec.d \
    -v /lib/modules:/lib/modules:ro \
    -p 500:500/udp \
    -p 4500:4500/udp \
    -d --privileged \
    hwdsl2/ipsec-vpn-server
  ```
## Make it work together
- `ip link add dummy0 type dummy`
- `ip addr add 192.168.42.1 dev dummy0`
- `ip link set dummy0 up`
- make it permanent:
  - `nano /etc/netplan/99_config.yaml`, add:  
    ```
    network:
      dummy-devices:
        dummy0:
          addresses:
            - 192.168.42.1/24
     ```
  - `netplan apply`
  - 
 
  -  
    
  - 
 
