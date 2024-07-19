Creating an EVPN Zone in Proxmox
================================

This section covers how to create an EVPN (Ethernet Virtual Private Network) zone in Proxmox, which allows for efficient and scalable network segmentation.

For more detailed information, refer to the Proxmox official documentation:
`Proxmox SDN Documentation <https://your-proxmox-ip/pve-docs/chapter-pvesdn.html#pvesdn_config_zone>`_

Prerequisites
-------------

On each node, ensure the following packages are installed:

.. code-block:: shell

    apt update
    apt install libpve-network-perl

    apt update
    apt install dnsmasq
    # disable default instance
    systemctl disable --now dnsmasq

    apt update
    apt install frr-pythontools

Adding an EVPN Controller
-------------------------

1. **Navigate to** `Datacenter > SDN > Controllers` and click **Create**.
2. **Fill in the following fields**:

   - **ID**: Choose a unique identifier for the controller.
   - **ASN**: Set to `65000`.
   - **Peers**: List all Proxmox Node IP addresses that will be part of the EVPN zone.

   .. image:: ./images/evpn_controler.png
       :alt: Adding EVPN Controller
       :align: center

Adding an EVPN Zone
-------------------

1. **Navigate to** `Datacenter > SDN > Zones` and click **Create**.
2. **Fill in the following fields**:

   - **ID**: Choose a unique identifier for the zone.
   - **Controller**: Select the controller created earlier.
   - **Type**: Choose `VRF-VXLAN`.
   - **Exit Nodes**: Select the nodes that will serve as exit points.
   - **Primary Exit Node**: Choose a primary exit node.
   - **MTU**: Set to `1450` as specified in the documentation.

   .. image:: ./images/evpn_zone.png
       :alt: Adding EVPN Zone
       :align: center

Creating VNETs
--------------

1. **Navigate to** `Datacenter > SDN > VNETs` and click **Create**.
2. **Fill in the following fields**:

   - **Name**: Choose a name for the VNET.
   - **Zone**: Select the EVPN zone you just created.
   - **Tag**: Assign a VLAN tag if needed.

   .. image:: ./images/evpn_vnets.png
       :alt: Adding VNETs
       :align: center

Creating Subnets
----------------

1. **Navigate to** the VNET you just created.
2. **Click** `Create` under the Subnets section.
3. **Fill in the following fields**:

   - **Subnet**: Define the subnet range.
   - **Gateway**: Specify the gateway address.
   - **SNAT**: Enable SNAT to allow VMs/containers to communicate with other networks.

   .. image:: ./images/evpn_subnet.png
       :alt: Adding Subnets
       :align: center

Testing the Setup
-----------------

1. **Create a VM or LXC Container**.
2. **Manually set the IP and Gateway** since EVPN does not include a native DHCP server. Alternatively, you can set up a DHCP server, but this tutorial does not cover that setup.

   .. image:: ./images/evpn_LXC.png
       :alt: Testing EVPN Setup with LXC
       :align: center

Conclusion
----------

By following these steps, you have successfully created and configured an EVPN zone in Proxmox. This setup allows for scalable and efficient network segmentation within your Proxmox environment.

In the next section, we will see how to add an NFS storage to your Proxmox cluster.
