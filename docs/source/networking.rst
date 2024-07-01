Once you have a working cluster, you may need to configure the network to:

- Enable communication between containers and VMs on the same node
- Enable communication between containers and VMs on different nodes
- Provide internet access to containers and VMs
- Set up DHCP
- ...

The current network configuration is the following : 

Each server have only one network interface (NIC) and a bridge. The interface is configured in DHCP and the bridge is linked to the interface. 

.. image:: ./images/2_PX.png
    :alt: Proxmox Cluster
    :align: center

You can check your network configure in Datacenter > Servername > Network : 

.. image:: ./images/ntw_conf.png
    :alt: Network Conf On GUI
    :align: center

The main issue here is that we can not configure the VM with IP from the same network as the ip pool is restricted. This is why we need to create a local network. 
The easiest way to do this is to use Software-Defined Networking (SDN).  


Software-Defined Networking (SDN)
---------------------------------


Bridge Configuration
--------------------

Due to a limited number of available IP addresses from our network pool and the need for VMs and containers to have IP addresses, we created a network bridge on each node. A network bridge acts as a virtual switch that allows you to connect all your VMs and containers. Each bridge is assigned a local IP address. At this stage, VMs connected to the bridge can communicate with each other but cannot access external networks. To enable external communication, we need to configure **Network Address Translation (NAT)**.

.. _Bridge:

Network Address Translation (NAT)
---------------------------------

**Network Address Translation (NAT)** allows multiple devices on a local network to share a single public IP address for accessing external networks. It modifies the IP address information in the headers of IP packets while they are in transit, enabling the devices to communicate with external networks while preserving the limited pool of IP addresses.


.. image:: ./images/nat.png
   :alt: Network Address Translation
   :align: center

.. _NAT:
