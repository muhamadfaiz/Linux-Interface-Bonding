#Linux Interface Bonding

We bond two or more network interface together so that if one interface is down, we will have the other interface as backup.

We will set up this on Centos 6 Minimal installation.

1. Load required module

    ```
    modprobe --first-time bonding
    ```

2. Verify module loaded

    ```
    modinfo bonding
    ```
3. Create a bond interface script
    ```
    vi /etc/sysconfig/network-scripts/ifcfg-bond0
    ```
4. Put below code as its content
    ```
    DEVICE=bond0
    IPADDR=[IP address]
    NETMASK=255.255.255.0
    ONBOOT=yes
    BOOTPROTO=none
    USERCTL=no
    NM_CONTROLLED=no
    BONDING_OPTS="mode=1 miimon=100 fail_over_mac=1"
    ```
5. Edit eth1 and eth2 script

	eth1
	```
    vi /etc/sysconfig/network-scripts/ifcfg-eth1
    DEVICE=eth1
    HWADDR=ff:ff:ff:ff:ff:ff
    TYPE=Ethernet
    ONBOOT=yes
    NM_CONTROLLED=no
    BOOTPROTO=none
    MASTER=bond0
    SLAVE=yes
    USERCTL=no
    ```
	eth2
    ```
    vi /etc/sysconfig/network-scripts/ifcfg-eth2
    DEVICE=eth2
    HWADDR=ff:ff:ff:ff:ff:ff
    TYPE=Ethernet
    ONBOOT=yes
    NM_CONTROLLED=no
    BOOTPROTO=none
    MASTER=bond0
    SLAVE=yes
    USERCTL=no
    ```
1. Set bond0 as autostart. This configuration will survive reboot
    ```
    vi /etc/modprobe.d/bonding.conf
    alias bond0 bonding
    ``` 
1. Save both script and restart network

    ```service network restart```
    
1. Verify bond interface working

	```
    cat /proc/net/bonding/bond0
    Ethernet Channel Bonding Driver: v3.7.1 (April 27, 2011)

    Bonding Mode: load balancing (round-robin)
    MII Status: up
    MII Polling Interval (ms): 0
    Up Delay (ms): 0
    Down Delay (ms): 0

    Slave Interface: eth1
    MII Status: up
    Speed: 1000 Mbps
    Duplex: full
    Link Failure Count: 0
    Permanent HW addr: [MAC ADDRESS]
    Slave queue ID: 0

    Slave Interface: eth2
    MII Status: up
    Speed: 1000 Mbps
    Duplex: full
    Link Failure Count: 0
    Permanent HW addr: [MAC ADDRESS]
    Slave queue ID: 0
    ```

*Source*
*https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Deployment_Guide/s2-networkscripts-interfaces-chan.html*
*https://community.oracle.com/thread/2546040*