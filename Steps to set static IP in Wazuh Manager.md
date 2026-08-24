# Steps to set static IP in Wazuh Manager
---
To set a static IP address on the Wazuh OVA, add the Address, gateway, and DNS servers to the /etc/systemd/network/10-eth0.network file. 
Here's the configuration I have set:

`[Match]
Type=ether
[Network]
Address=10.0.0.200/24
Gateway= 10.0.0.1
DNS=8.8.8.8
DNS=8.8.4.4
DHCP=no`
