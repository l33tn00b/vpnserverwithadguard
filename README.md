# vpnserverwithadguard
Get an ipsec VPN server up and running with AdGuard on Ubuntu24.01  
Btw: f***k ufw and docker incompatibilities.

# Basic
- nano `/etc/apt/apt.conf.d/50unattended-upgrades` to make automatic reboot after updates
- install docker as per https://docs.docker.com/engine/install/ubuntu/

# VPN Container
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

# AdGuard
- install snap (if not already installed)
- `snap install adguard-home`

# Make it work together
- `ip link add dummy0 type dummy`
- `ip addr add 192.168.42.1 dev dummy0`
- `ip link set dummy0 up`
- make it permanent:
  - `nano /etc/netplan/99_config.yaml`
  - add
    ```
    network:
      version: 2
      renderer: networkd
      network:
        dummy-devices:
          dummy0:
            addresses:
              - 192.168.42.1/24
    ```
  - 
